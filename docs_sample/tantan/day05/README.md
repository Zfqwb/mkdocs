## 课程说明

- 圈子实现点赞、喜欢功能
- 圈子实现评论
- 圈子实现评论的点赞
- 小视频功能介绍
- FastDFS入门学习
- 实现发布小视频功能
- 实现查询小视频列表功能

## 1、圈子点赞实现分析

在圈子功能中，对于圈子的点赞、喜欢、评论等均可理解为用户对动态的评论（Comment），在quanzi_comment表中使用commentType进行区分。

在具体的实现中，需要将点赞数、某用户是否点赞等数据保存到Reds中，以减轻MongoDB的压力。

具体存储结构如下：

![image-20201218153502042](assets/image-20201218153502042.png)

> 说明：在Redis的存储结构中，采用的是Hash存储，这样的好处就在于一条动态的点赞、喜欢等数据都会集中的存储到一起，从而减少了Redis中数据条数。

## 2、点赞

### 2.1、定义枚举

为了规范使用CommentType，所以将其定义为枚举类型。

~~~java
package com.tanhua.dubbo.server.enums;

/**
 * 评论类型：1-点赞，2-评论，3-喜欢
 */
public enum CommentType {

    LIKE(1), COMMENT(2), LOVE(3);

    int type;

    CommentType(int type) {
        this.type = type;
    }

    public int getType() {
        return type;
    }
}
~~~

### 2.2、dubbo服务

#### 2.2.1、定义接口

~~~java
package com.tanhua.dubbo.server.api;

import com.tanhua.dubbo.server.pojo.Publish;
import com.tanhua.dubbo.server.vo.PageInfo;

public interface QuanZiApi {

    //........此处忽略其他代码..........

    /**
     * 根据id查询动态
     *
     * @param id 动态id
     * @return
     */
    Publish queryPublishById(String id);

    /**
     * 点赞
     *
     * @param userId
     * @param publishId
     * @return
     */
    Boolean likeComment(Long userId, String publishId);

    /**
     * 取消点赞
     *
     * @param userId
     * @param publishId
     * @return
     */
    Boolean disLikeComment(Long userId, String publishId);

    /**
     * 查询点赞数
     *
     * @param publishId
     * @return
     */
    Long queryLikeCount(String publishId);

    /**
     * 查询用户是否点赞该动态
     *
     * @param userId
     * @param publishId
     * @return
     */
    Boolean queryUserIsLike(Long userId, String publishId);

}

~~~

#### 2.2.2、编写实现

~~~java
package com.tanhua.dubbo.server.api;

@Service(version = "1.0.0")
@Slf4j
public class QuanZiApiImpl implements QuanZiApi {

    //评论数据存储在Redis中key的前缀
    private static final String COMMENT_REDIS_KEY_PREFIX = "QUANZI_COMMENT_";

    //用户是否点赞的前缀
    private static final String COMMENT_USER_LIEK_REDIS_KEY_PREFIX = "USER_LIKE_";

    //用户是否喜欢的前缀
    private static final String COMMENT_USER_LOVE_REDIS_KEY_PREFIX = "USER_LOVE_";

    @Autowired
    private MongoTemplate mongoTemplate;

    @Autowired
    private RedisTemplate<String, String> redisTemplate;

   	//........此处忽略其他代码..........

        @Override
    public Publish queryPublishById(String id) {
        return this.mongoTemplate.findById(new ObjectId(id), Publish.class);
    }

    @Override
    public Boolean likeComment(Long userId, String publishId) {
        //判断该用户是否已经点赞，如果已经点赞就直接返回
        if (this.queryUserIsLike(userId, publishId)) {
            return false;
        }

        //保存Comment数据
        Boolean result = this.saveComment(userId, publishId, CommentType.LIKE, null);
        if (!result) {
            return false;
        }

        //修改redis中的点赞数以及是否点赞

        //修改点赞数
        String redisKey = this.getCommentRedisKeyPrefix(publishId);
        String hashKey = CommentType.LIKE.toString();
        this.redisTemplate.opsForHash().increment(redisKey, hashKey, 1);

        //用户是否点赞
        String userHashKey = this.getCommentUserLikeRedisKeyPrefix(userId);
        this.redisTemplate.opsForHash().put(redisKey, userHashKey, "1");

        return true;
    }

    private String getCommentRedisKeyPrefix(String publishId) {
        return COMMENT_REDIS_KEY_PREFIX + publishId;
    }

    private String getCommentUserLikeRedisKeyPrefix(Long userId) {
        return COMMENT_USER_LIKE_REDIS_KEY_PREFIX + userId;
    }

    @Override
    public Boolean disLikeComment(Long userId, String publishId) {
        //判断用户是否已经点赞，如果没有点赞就返回
        if (!this.queryUserIsLike(userId, publishId)) {
            return false;
        }

        //删除评论数据
        Boolean result = this.removeComment(userId, publishId, CommentType.LIKE);
        if (!result) {
            return false;
        }

        //修改Redis中的数据

        //修改点赞数
        String redisKey = this.getCommentRedisKeyPrefix(publishId);
        String hashKey = CommentType.LIKE.toString();
        this.redisTemplate.opsForHash().increment(redisKey, hashKey, -1);

        //用户是否点赞
        String userHashKey = this.getCommentUserLikeRedisKeyPrefix(userId);
        this.redisTemplate.opsForHash().delete(redisKey, userHashKey);

        return true;
    }

    @Override
    public Long queryLikeCount(String publishId) {
        //从Redis中命中查询，如果命中直接返回即可
        String redisKey = this.getCommentRedisKeyPrefix(publishId);
        String hashKey = CommentType.LIKE.toString();
        Object data = this.redisTemplate.opsForHash().get(redisKey, hashKey);
        if (ObjectUtil.isNotEmpty(data)) {
            return Convert.toLong(data);
        }

        //查询Mongodb
        Long count = this.queryCommentCount(publishId, CommentType.LIKE);
        //写入Redis中
        this.redisTemplate.opsForHash().put(redisKey, hashKey, String.valueOf(count));

        return count;
    }

    @Override
    public Boolean queryUserIsLike(Long userId, String publishId) {
        //从redis中查询数据
        String redisKey = this.getCommentRedisKeyPrefix(publishId);
        String userHashKey = this.getCommentUserLikeRedisKeyPrefix(userId);
        Object data = this.redisTemplate.opsForHash().get(redisKey, userHashKey);
        if (ObjectUtil.isNotEmpty(data)) {
            return StrUtil.equals(Convert.toStr(data), "1");
        }

        //查询Mongodb，确定是否已经点赞
        Query query = Query.query(Criteria.where("publishId").is(new ObjectId(publishId))
                .and("userId").is(userId)
                .and("commentType").is(CommentType.LIKE)
        );
        long count = this.mongoTemplate.count(query, Comment.class);
        if(count == 0){
            return false;
        }

        //写入到redis中
        this.redisTemplate.opsForHash().put(redisKey, userHashKey, "1");

        return true;
    }

    /**
     * 保存Comment
     *
     * @return
     */
    private Boolean saveComment(Long userId, String publishId,
                                CommentType commentType, String content) {
        try {
            Comment comment = new Comment();
            comment.setId(ObjectId.get());
            comment.setUserId(userId);
            comment.setPublishId(new ObjectId(publishId));
            // 评论类型
            comment.setCommentType(commentType.getType());
            // 内容
            comment.setContent(content);
            comment.setCreated(System.currentTimeMillis());

            Publish publish = this.queryPublishById(publishId);
            //TODO 其他评论对象，暂不处理
            comment.setPublishUserId(publish.getUserId());

            this.mongoTemplate.save(comment);

            return true;
        } catch (Exception e) {
            log.error("保存Comment出错~ userId = " + userId + ", publishId = " + publishId + ", commentType = " + commentType, e);
        }

        return false;
    }

    /**
     * 删除评论数据
     *
     * @param userId
     * @param publishId
     * @param commentType
     * @return
     */
    private Boolean removeComment(Long userId, String publishId, CommentType commentType) {
        Query query = Query.query(Criteria.where("userId").is(userId)
                .and("publishId").is(new ObjectId(publishId))
                .and("commentType").is(commentType.getType())
        );
        return this.mongoTemplate.remove(query, Comment.class).getDeletedCount() > 0;
    }

    /**
     * 查询数量
     *
     * @param publishId
     * @param commentType
     * @return
     */
    private Long queryCommentCount(String publishId, CommentType commentType) {
        Query query = Query.query(Criteria.where("publishId").is(new ObjectId(publishId))
                .and("commentType").is(commentType.getType())
        );
        return this.mongoTemplate.count(query, Comment.class);
    }
}

~~~

#### 2.2.3、编写测试用例

```java
package com.tanhua.dubbo.server.api;

import com.tanhua.dubbo.server.pojo.Publish;
import com.tanhua.dubbo.server.vo.PageInfo;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

@RunWith(SpringRunner.class)
@SpringBootTest
public class TestQuanZiApi {

    @Autowired
    private QuanZiApi quanZiApi;

    //........此处忽略其他代码..........
    
    @Test
    public void testLike(){
        Long userId = 1L;
        String publishId = "5fae53947e52992e78a3afb1";
        Boolean data = this.quanZiApi.queryUserIsLike(userId, publishId);
        System.out.println(data);

        System.out.println(this.quanZiApi.likeComment(userId, publishId));

        System.out.println(this.quanZiApi.queryLikeCount(publishId));

        System.out.println(this.quanZiApi.disLikeComment(userId, publishId));

        System.out.println(this.quanZiApi.queryLikeCount(publishId));
    }

}
```

### 2.3、APP接口服务

点赞接口地址：https://mock-java.itheima.net/project/35/interface/api/707

![image-20201218161505609](assets/image-20201218161505609.png)

![image-20201218161521081](assets/image-20201218161521081.png)

从接口文档来看，点赞完成后需要返回点赞数。

#### 2.3.1、编写接口服务

~~~java
//com.tanhua.server.controller.QuanZiController
	/**
     * 点赞
     *
     * @param publishId
     * @return
     */
    @GetMapping("/{id}/like")
    public ResponseEntity<Long> likeComment(@PathVariable("id") String publishId) {
        try {
            Long likeCount = this.quanZiService.likeComment(publishId);
            if (likeCount != null) {
                return ResponseEntity.ok(likeCount);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).build();
    }

    /**
     * 取消点赞
     *
     * @param publishId
     * @return
     */
    @GetMapping("/{id}/dislike")
    public ResponseEntity<Long> disLikeComment(@PathVariable("id") String publishId) {
        try {
            Long likeCount = this.quanZiService.disLikeComment(publishId);
            if (null != likeCount) {
                return ResponseEntity.ok(likeCount);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).build();
    }

~~~

#### 2.3.2、编写服务实现

~~~java
//com.tanhua.server.service.QuanZiService

	public Long likeComment(String publishId) {
        User user = UserThreadLocal.get();
        //点赞
        Boolean result = this.quanZiApi.likeComment(user.getId(), publishId);
        if(result){
            //查询点赞数
            return this.quanZiApi.queryLikeCount(publishId);
        }
        return null;
    }

    public Long disLikeComment(String publishId) {
        User user = UserThreadLocal.get();
        //取消点赞
        Boolean result = this.quanZiApi.disLikeComment(user.getId(), publishId);
        if(result){
            //查询点赞数
            return this.quanZiApi.queryLikeCount(publishId);
        }
        return null;
    }
~~~

#### 2.3.3、修改查询动态点赞数

查询点赞数、是否点赞，需要通过dubbo服务进行查询。

~~~java
//com.tanhua.server.service.QuanZiService
    /**
     * 填充用户信息
     *
     * @param userInfo
     * @param quanZiVo
     */
    private void fillUserInfoToQuanZiVo(UserInfo userInfo, QuanZiVo quanZiVo) {
        BeanUtil.copyProperties(userInfo, quanZiVo, "id");
        quanZiVo.setGender(userInfo.getSex().name().toLowerCase());
        quanZiVo.setTags(StringUtils.split(userInfo.getTags(), ','));

        //当前用户
        User user = UserThreadLocal.get();

        quanZiVo.setCommentCount(0); //TODO 评论数
        quanZiVo.setDistance("1.2公里"); //TODO 距离
        quanZiVo.setHasLiked(this.quanZiApi.queryUserIsLike(user.getId(), quanZiVo.getId()) ? 1 : 0); //是否点赞（1是，0否）
        quanZiVo.setLikeCount(Convert.toInt(this.quanZiApi.queryLikeCount(quanZiVo.getId()))); //点赞数
        quanZiVo.setHasLoved(0); //TODO 是否喜欢（1是，0否）
        quanZiVo.setLoveCount(0); //TODO 喜欢数
    }
~~~

#### 2.3.4、测试

![image-20201221105505538](assets/image-20201221105505538.png)

从测试结果中可以看出，在响应结果中返回了点赞数以及是否点赞的数据。

## 3、喜欢

喜欢的实现与点赞类似，只是其类型不同。需要注意的是，在推荐动态中才有喜欢功能，好友动态中是没有此功能的。

### 3.1、dubbo服务

#### 3.1.1、定义接口

~~~java
//com.tanhua.dubbo.server.api.QuanZiApi

	/**
     * 喜欢
     *
     * @param userId
     * @param publishId
     * @return
     */
    Boolean loveComment(Long userId, String publishId);

    /**
     * 取消喜欢
     *
     * @param userId
     * @param publishId
     * @return
     */
    Boolean disLoveComment(Long userId, String publishId);

    /**
     * 查询喜欢数
     *
     * @param publishId
     * @return
     */
    Long queryLoveCount(String publishId);

    /**
     * 查询用户是否喜欢该动态
     *
     * @param userId
     * @param publishId
     * @return
     */
    Boolean queryUserIsLove(Long userId, String publishId);
~~~

#### 3.1.2、编写实现

```java
//com.tanhua.dubbo.server.api.QuanZiApiImpl

    @Override
    public Boolean loveComment(Long userId, String publishId) {
        //查询该用户是否已经喜欢
        if (this.queryUserIsLove(userId, publishId)) {
            return false;
        }

        //喜欢
        boolean result = this.saveComment(userId, publishId, CommentType.LOVE, null);
        if (!result) {
            return false;
        }

        //喜欢成功后，修改Redis中的总的喜欢数
        String redisKey = this.getCommentRedisKeyPrefix(publishId);
        String hashKey = CommentType.LOVE.toString();
        this.redisTemplate.opsForHash().increment(redisKey, hashKey, 1);

        //标记用户已经喜欢
        hashKey = this.getCommentUserLoveRedisKey(userId);
        this.redisTemplate.opsForHash().put(redisKey, hashKey, "1");

        return true;
    }

    private String getCommentUserLoveRedisKey(Long userId) {
        return COMMENT_USER_LOVE_REDIS_KEY_PREFIX + userId;
    }

    @Override
    public Boolean disLoveComment(Long userId, String publishId) {
        if (!this.queryUserIsLove(userId, publishId)) {
            //如果用户没有喜欢，就直接返回
            return false;
        }

        boolean result = this.removeComment(userId, publishId, CommentType.LOVE);
        if (!result) {
            //删除失败
            return false;
        }

        //删除redis中的记录
        String redisKey = this.getCommentRedisKeyPrefix(publishId);
        String hashKey = this.getCommentUserLoveRedisKey(userId);
        this.redisTemplate.opsForHash().delete(redisKey, hashKey);
        this.redisTemplate.opsForHash().increment(redisKey, CommentType.LOVE.toString(), -1);

        return true;
    }

    @Override
    public Long queryLoveCount(String publishId) {
        //首先从redis中命中，如果命中的话就返回，没有命中就查询Mongodb
        String redisKey = this.getCommentRedisKeyPrefix(publishId);
        String hashKey = CommentType.LOVE.toString();
        Object value = this.redisTemplate.opsForHash().get(redisKey, hashKey);
        if (ObjectUtil.isNotEmpty(value)) {
            return Convert.toLong(value);
        }

        //查询count
        Long count = this.queryCommentCount(publishId, CommentType.LOVE);

        //存储到redis中
        this.redisTemplate.opsForHash().put(redisKey, hashKey, String.valueOf(count));

        return count;
    }

    @Override
    public Boolean queryUserIsLove(Long userId, String publishId) {
        String redisKey = this.getCommentRedisKeyPrefix(publishId);
        String hashKey = this.getCommentUserLoveRedisKey(userId);
        Object value = this.redisTemplate.opsForHash().get(redisKey, hashKey);
        if (ObjectUtil.isNotEmpty(value)) {
            return StrUtil.equals(Convert.toStr(value), "1");
        }

        //查询mongodb
        Query query = Query.query(Criteria.where("publishId")
                .is(new ObjectId(publishId))
                .and("userId").is(userId)
                .and("commentType").is(CommentType.LOVE.getType()));
        long count = this.mongoTemplate.count(query, Comment.class);
        if (count == 0) {
            return false;
        }

        //标记用户已经喜欢
        this.redisTemplate.opsForHash().put(redisKey, hashKey, "1");

        return true;
    }
```

### 3.2、APP接口服务

#### 3.2.1、编写接口服务

~~~java
//com.tanhua.server.controller.QuanZiController

    /**
     * 喜欢
     *
     * @param publishId
     * @return
     */
    @GetMapping("/{id}/love")
    public ResponseEntity<Long> loveComment(@PathVariable("id") String publishId) {
        try {
            Long loveCount = this.quanZiService.loveComment(publishId);
            if (null != loveCount) {
                return ResponseEntity.ok(loveCount);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).build();
    }

    /**
     * 取消喜欢
     *
     * @param publishId
     * @return
     */
    @GetMapping("/{id}/unlove")
    public ResponseEntity<Long> disLoveComment(@PathVariable("id") String publishId) {
        try {
            Long loveCount = this.quanZiService.disLoveComment(publishId);
            if (null != loveCount) {
                return ResponseEntity.ok(loveCount);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).build();
    }
~~~

#### 3.2.2、编写服务实现

~~~java
//com.tanhua.server.service.QuanZiService

    public Long loveComment(String publishId) {
        User user = UserThreadLocal.get();
        //喜欢
        Boolean result = this.quanZiApi.loveComment(user.getId(), publishId);
        if(result){
            //查询喜欢数
            return this.quanZiApi.queryLoveCount(publishId);
        }
        return null;
    }

    public Long disLoveComment(String publishId) {
        User user = UserThreadLocal.get();
        //取消喜欢
        Boolean result = this.quanZiApi.disLoveComment(user.getId(), publishId);
        if(result){
            //查询喜欢数
            return this.quanZiApi.queryLoveCount(publishId);
        }
        return null;
    }

    /**
     * 填充用户信息
     *
     * @param userInfo
     * @param quanZiVo
     */
    private void fillUserInfoToQuanZiVo(UserInfo userInfo, QuanZiVo quanZiVo) {
        BeanUtil.copyProperties(userInfo, quanZiVo, "id");
        quanZiVo.setGender(userInfo.getSex().name().toLowerCase());
        quanZiVo.setTags(StringUtils.split(userInfo.getTags(), ','));

        //当前用户
        User user = UserThreadLocal.get();

        quanZiVo.setCommentCount(0); //TODO 评论数
        quanZiVo.setDistance("1.2公里"); //TODO 距离
        quanZiVo.setHasLiked(this.quanZiApi.queryUserIsLike(user.getId(), quanZiVo.getId()) ? 1 : 0); //是否点赞（1是，0否）
        quanZiVo.setLikeCount(Convert.toInt(this.quanZiApi.queryLikeCount(quanZiVo.getId()))); //点赞数
        quanZiVo.setHasLoved(this.quanZiApi.queryUserIsLove(user.getId(), quanZiVo.getId()) ? 1 : 0); //是否喜欢（1是，0否）
        quanZiVo.setLoveCount(Convert.toInt(this.quanZiApi.queryLoveCount(quanZiVo.getId()))); //喜欢数
    }
~~~

#### 1.2.3、测试

 ![image-20201221114335332](assets/image-20201221114335332.png)

## 4、查询单条动态

用户点击评论时需要查询单条动态详情，需要有接口支持。

服务接口地址：https://mock-java.itheima.net/project/35/interface/api/695

![image-20201221114927609](assets/image-20201221114927609.png)

响应的数据接口与查询好友动态一致，只是单条返回而不是集合。

要注意的是，dubbo服务接口在前面已经开发完成，现在只要想实现APP端的接口服务即可。

### 4.1、定义服务接口

~~~java
//com.tanhua.server.controller.QuanZiController

    /**
     * 查询单条动态信息
     *
     * @param publishId
     * @return
     */
    @GetMapping("/{id}")
    public ResponseEntity<QuanZiVo> queryById(@PathVariable("id") String publishId) {
        try {
            QuanZiVo movements = this.quanZiService.queryById(publishId);
            if(null != movements){
                return ResponseEntity.ok(movements);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).build();
    }
~~~

### 4.2、服务实现

~~~java
//com.tanhua.server.service.QuanZiService

	public QuanZiVo queryById(String publishId) {
        Publish publish = this.quanZiApi.queryPublishById(publishId);
        if (publish == null) {
            return null;
        }
        return this.fillQuanZiVo(Arrays.asList(publish)).get(0);
    }
~~~

### 4.3、测试

![image-20201221214045637](assets/image-20201221214045637.png)

可以看到，返回了单条数据。

### 4.4、异常的解决

在完成查询单条动态接口后，会发现，刷新首页时会出现如下异常：

~~~shell
java.lang.IllegalArgumentException: invalid hexadecimal representation of an ObjectId: [visitors]
	at org.bson.types.ObjectId.parseHexString(ObjectId.java:550)
	at org.bson.types.ObjectId.<init>(ObjectId.java:239)
	at com.tanhua.dubbo.server.api.QuanZiApiImpl.queryPublishById(QuanZiApiImpl.java:411)
	at com.alibaba.dubbo.common.bytecode.Wrapper1.invokeMethod(Wrapper1.java)
	at com.alibaba.dubbo.rpc.proxy.javassist.JavassistProxyFactory$1.doInvoke(JavassistProxyFactory.java:47)
	at com.alibaba.dubbo.rpc.proxy.AbstractProxyInvoker.invoke(AbstractProxyInvoker.java:76)
	at com.alibaba.dubbo.config.invoker.DelegateProviderMetaDataInvoker.invoke(DelegateProviderMetaDataInvoker.java:52)
	at com.alibaba.dubbo.rpc.protocol.InvokerWrapper.invoke(InvokerWrapper.java:56)
~~~

原因是：谁看过我的接口还没实现，导致了映射到了查询单条动态的接口，导致的异常，接口地址：https://mock-java.itheima.net/project/35/interface/api/743

解决方法：编写一个空的方法《谁看过我》的接口实现。

```java
//com.tanhua.server.controller.QuanZiController
	/**
     * TODO：谁看过我
     *
     * @return
     */
    @GetMapping("visitors")
    public ResponseEntity<Object> queryVisitors() {
        return ResponseEntity.ok(Collections.EMPTY_LIST);
    }
```

## 5、评论

在单条动态打开后，可以看到有评论列表，功能包括：查询评论列表，评论点赞、取消点赞。

需要注意的是，评论的点赞操作与圈子动态的点赞使用同一套逻辑。

### 5.1、dubbo服务

#### 5.1.1、定义服务接口

~~~java
//com.tanhua.dubbo.server.api.QuanZiApi

	/**
     * 查询评论
     *
     * @return
     */
    PageInfo<Comment> queryCommentList(String publishId, Integer page, Integer pageSize);

    /**
     * 发表评论
     *
     * @param userId
     * @param publishId
     * @param content
     * @return
     */
    Boolean saveComment(Long userId, String publishId, String content);
~~~

#### 5.1.2、编写实现

~~~java
    /**
     * 查询评论列表
     * 
     * @param publishId
     * @param page
     * @param pageSize
     * @return
     */
    @Override
    public PageInfo<Comment> queryCommentList(String publishId, Integer page, Integer pageSize) {
        PageRequest pageRequest = PageRequest.of(page - 1, pageSize, Sort.by(Sort.Order.asc("created")));
        
        Query query = new Query(Criteria
                .where("publishId").is(new ObjectId(publishId))
                .and("commentType").is(CommentType.COMMENT.getType())).with(pageRequest);

        //查询评论列表
        List<Comment> commentList = this.mongoTemplate.find(query, Comment.class);

        PageInfo<Comment> pageInfo = new PageInfo<>();
        pageInfo.setPageNum(page);
        pageInfo.setPageSize(pageSize);
        pageInfo.setRecords(commentList);
        return pageInfo;
    }

    /**
     * 发表评论
     *
     * @param userId
     * @param publishId
     * @param content
     * @return
     */
    @Override
    public Boolean saveComment(Long userId, String publishId, String content) {
        return this.saveComment(userId, publishId, CommentType.COMMENT, content);
    }
~~~

### 5.2、APP接口服务

#### 5.2.1、CommentVo

根据响应结果的数据结构定义对象：

~~~java
package com.tanhua.server.vo;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

/**
 * 评论
 */
@Data
@NoArgsConstructor
@AllArgsConstructor
public class CommentVo {

    private String id; //评论id
    private String avatar; //头像
    private String nickname; //昵称
    private String content; //评论
    private String createDate; //评论时间: 08:27
    private Integer likeCount; //点赞数
    private Integer hasLiked; //是否点赞（1是，0否）

}
~~~
#### 5.2.2、编写Controller

在APP接口服务中，需要开发4个接口，分别是查询评论列表、发表评论、点赞、取消点赞。

由于其接口的url地址与QuanZiConroller地址不同，所以需要创建不同的Controller类。

~~~java
package com.tanhua.server.controller;

import com.tanhua.server.service.QuanZiService;
import com.tanhua.server.vo.PageResult;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.Map;

/**
 * 圈子功能中的评论
 */
@RestController
@RequestMapping("comments")
public class QuanZiCommentController {

    @Autowired
    private QuanZiService quanZiService;

    /**
     * 查询评论列表
     *
     * @return
     */
    @GetMapping
    public ResponseEntity<PageResult> queryCommentsList(@RequestParam("movementId") String publishId,
                                                        @RequestParam(value = "page", defaultValue = "1") Integer page,
                                                        @RequestParam(value = "pagesize", defaultValue = "10") Integer pageSize) {
        try {
            PageResult pageResult = this.quanZiService.queryCommentList(publishId, page, pageSize);
            return ResponseEntity.ok(pageResult);
        } catch (Exception e) {
            e.printStackTrace();
        }
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).build();

    }

    /**
     * 保存评论
     */
    @PostMapping
    public ResponseEntity<Void> saveComments(@RequestBody Map<String, String> param) {
        try {
            String publishId = param.get("movementId");
            String content = param.get("comment");
            Boolean result = this.quanZiService.saveComments(publishId, content);
            if (result) {
                return ResponseEntity.ok(null);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).build();
    }

    /**
     * 点赞
     *
     * @param publishId
     * @return
     */
    @GetMapping("{id}/like")
    public ResponseEntity<Long> likeComment(@PathVariable("id") String publishId) {
        try {
            Long likeCount = this.quanZiService.likeComment(publishId);
            if (likeCount != null) {
                return ResponseEntity.ok(likeCount);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).build();
    }

    /**
     * 取消点赞
     *
     * @param publishId
     * @return
     */
    @GetMapping("{id}/dislike")
    public ResponseEntity<Long> disLikeComment(@PathVariable("id") String publishId) {
        try {
            Long likeCount = this.quanZiService.disLikeComment(publishId);
            if (null != likeCount) {
                return ResponseEntity.ok(likeCount);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).build();
    }

}

~~~

#### 5.2.3、编写Service实现

Service的具体实现依然是放到QuanZiSerivce中完成。

~~~java
//com.tanhua.server.service.QuanZiService

   /**
     * 查询评论列表
     *
     * @param publishId
     * @param page
     * @param pageSize
     * @return
     */
    public PageResult queryCommentList(String publishId, Integer page, Integer pageSize) {

        PageResult pageResult = new PageResult();
        pageResult.setPage(page);
        pageResult.setPagesize(pageSize);

        User user = UserThreadLocal.get();

        //查询评论列表数据
        PageInfo<Comment> pageInfo = this.quanZiApi.queryCommentList(publishId, page, pageSize);
        List<Comment> records = pageInfo.getRecords();
        if(CollUtil.isEmpty(records)){
            return pageResult;
        }

        //查询用户信息
        List<Object> userIdList = CollUtil.getFieldValues(records, "userId");
        List<UserInfo> userInfoList = this.userInfoService.queryUserInfoByUserIdList(userIdList);

        List<CommentVo> result = new ArrayList<>();
        for (Comment record : records) {
            CommentVo commentVo = new CommentVo();
            commentVo.setContent(record.getContent());
            commentVo.setId(record.getId().toHexString());
            commentVo.setCreateDate(DateUtil.format(new Date(record.getCreated()), "HH:mm"));
            //是否点赞
            commentVo.setHasLiked(this.quanZiApi.queryUserIsLike(user.getId(), commentVo.getId()) ? 1 : 0);
            //点赞数
            commentVo.setLikeCount(Convert.toInt(this.quanZiApi.queryLikeCount(commentVo.getId())));


            for (UserInfo userInfo : userInfoList) {
                if(ObjectUtil.equals(record.getUserId(), userInfo.getUserId())){

                    commentVo.setAvatar(userInfo.getLogo());
                    commentVo.setNickname(userInfo.getNickName());

                    break;
                }
            }

            result.add(commentVo);
        }

        pageResult.setItems(result);

        return pageResult;
    }

    /**
     * 发表评论
     * @param publishId
     * @param content
     * @return
     */
    public Boolean saveComments(String publishId, String content) {
        User user = UserThreadLocal.get();
        return this.quanZiApi.saveComment(user.getId(), publishId, content);
    }
~~~

#### 5.2.4、测试

 ![image-20201222115831760](assets/image-20201222115831760.png)

测试点赞时会发现dubbo服务中会出现null指针异常，如下：

~~~shell
java.lang.NullPointerException
	at com.tanhua.dubbo.server.api.QuanZiApiImpl.saveComment(QuanZiApiImpl.java:386)
	at com.tanhua.dubbo.server.api.QuanZiApiImpl.likeComment(QuanZiApiImpl.java:180)
	at com.alibaba.dubbo.common.bytecode.Wrapper1.invokeMethod(Wrapper1.java)
	at com.alibaba.dubbo.rpc.proxy.javassist.JavassistProxyFactory$1.doInvoke(JavassistProxyFactory.java:47)
	at com.alibaba.dubbo.rpc.proxy.AbstractProxyInvoker.invoke(AbstractProxyInvoker.java:76)
	at com.alibaba.dubbo.config.invoker.DelegateProviderMetaDataInvoker.invoke(DelegateProviderMetaDataInvoker.java:52)
	at com.alibaba.dubbo.rpc.protocol.InvokerWrapper.invoke(InvokerWrapper.java:56)
~~~

原因是：原有的点赞实现中，需要查询Publish对象，但是现在实现的是针对评论的点赞，是查询不到Publish对象的，所以抛出了空指针异常。

解决如下：

~~~java
//com.tanhua.dubbo.server.api.QuanZiApiImpl

    /**
     * 保存Comment
     *
     * @return
     */
    private Boolean saveComment(Long userId, String publishId,
                                CommentType commentType, String content) {
        try {
            Comment comment = new Comment();
            comment.setId(ObjectId.get());
            comment.setUserId(userId);
            comment.setPublishId(new ObjectId(publishId));
            // 评论类型
            comment.setCommentType(commentType.getType());
            // 内容
            comment.setContent(content);
            comment.setCreated(System.currentTimeMillis());

            Publish publish = this.queryPublishById(publishId);
            if (ObjectUtil.isNotEmpty(publish)) {
                comment.setPublishUserId(publish.getUserId());
            } else {
                //查询评论
                Comment myComment = this.queryCommentById(publishId);
                if(ObjectUtil.isNotEmpty(myComment)){
                    comment.setPublishUserId(myComment.getUserId());
                }else{
                    //TODO 其他情况，比如小视频等
                }
            }

            this.mongoTemplate.save(comment);

            return true;
        } catch (Exception e) {
            log.error("保存Comment出错~ userId = " + userId + ", publishId = " + publishId + ", commentType = " + commentType, e);
        }

        return false;
    }

    /**
     * 根据id查询Comment对象
     *
     * @param id
     * @return
     */
    private Comment queryCommentById(String id) {
        return this.mongoTemplate.findById(new ObjectId(id), Comment.class);
    }
~~~

 ![image-20201222120513728](assets/image-20201222120513728.png)

这样，点赞功能正常了。

## 6、小视频

### 6.1、功能说明

小视频功能类似于抖音、快手小视频的应用，用户可以上传小视频进行分享，也可以浏览查看别人分享的视频，并且可以对视频评论和点赞操作。

效果：

 ![1568878011614](assets/1568878011614.png)

查看详情：

 ![1568878327076](assets/1568878327076.png)

评论：

 ![1568878353854](assets/1568878353854.png)

点赞：

 ![1568878397710](assets/1568878397710.png)

### 6.2、技术方案

对于小视频的功能的开发，核心点就是：存储 + 推荐 + 加载速度 。

- 对于存储而言，小视频的存储量以及容量都是非常巨大的。
  - 所以我们选择自己搭建分布式存储系统 FastDFS进行存储。
- 对于推荐算法，我们将采用多种权重的计算方式进行计算。
- 对于加载速度，除了提升服务器带宽外可以通过CDN的方式进行加速，当然了这需要额外购买CDN服务。

## 7、FastDFS

### 7.1、FastDFS是什么？

FastDFS是分布式文件系统。使用 FastDFS很容易搭建一套高性能的文件服务器集群提供文件上传、下载等服务。

### 7.2、工作原理

FastDFS 架构包括 Tracker server 和 Storage server。客户端请求 Tracker server 进行文件上传、下载，通过 Tracker server 调度最终由 Storage server 完成文件上传和下载。

Tracker server 作用是负载均衡和调度，通过 Tracker server 在文件上传时可以根据一些策略找到 Storage server 提供文件上传服务。可以将 tracker 称为追踪服务器或调度服务器。

Storage server 作用是文件存储，客户端上传的文件最终存储在 Storage 服务器上，Storage server 没有实现自己的文件系统而是利用操作系统的文件系统来管理文件。可以将storage称为存储服务器。

 ![6bd41a6af1fef5cc89dda7c94208c6a64f3.png](assets/6bd41a6af1fef5cc89dda7c94208c6a64f3.png)

每个 tracker 节点地位平等，收集 Storage 集群的状态。

Storage 分为多个组，每个组之间保存的文件是不同的。每个组内部可以有多个成员，组成员内部保存的内容是一样的，组成员的地位是一致的，没有主从的概念。

### 7.3、文件的上传

 ![1746338-20190925190927228-1815093216.png](assets/1746338-20190925190927228-1815093216.png)

客户端上传文件后存储服务器将文件 ID 返回给客户端，此文件 ID 用于以后访问该文件的索引信息。文件索引信息包括：组名，虚拟磁盘路径，数据两级目录，文件名。

### 7.4、文件的下载

 ![img](assets/b3d48e8fd6255834f2445e0b956390455a4.png)

客户端下载请求到Tracker服务，Tracker返回给客户端storage的信息，客户端根据这些信息进行请求storage获取到文件。

### 7.5、开始使用

在我们提供的虚拟机中已经通过docker搭建了FastDFS环境，下面我们来学习下如何通过Java程序来使用FastDFS。

#### 7.5.1、引入依赖

关于使用FastDFS上传小视频的逻辑我们在server工程中完成，所以需要在server工程中引入依赖。

~~~xml
<dependency>
    <groupId>com.github.tobato</groupId>
    <artifactId>fastdfs-client</artifactId>
    <version>1.26.7</version>
    <exclusions>
        <exclusion>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-classic</artifactId>
        </exclusion>
    </exclusions>
</dependency>
~~~

#### 7.5.2、编写配置文件

在application.properties配置文件中加入如下内容：

~~~properties
# ===================================================================
# 分布式文件系统FDFS配置
# ===================================================================
fdfs.so-timeout = 1501
fdfs.connect-timeout = 601
#缩略图生成参数
fdfs.thumb-image.width= 150
fdfs.thumb-image.height= 150
#TrackerList参数,支持多个
fdfs.tracker-list=192.168.31.81:22122
#访问路径
fdfs.web-server-url=http://192.168.31.81:8888/
~~~

#### 7.5.3、测试代码

~~~java
package com.tanhua.server;

import com.github.tobato.fastdfs.domain.conn.FdfsWebServer;
import com.github.tobato.fastdfs.domain.fdfs.StorePath;
import com.github.tobato.fastdfs.service.FastFileStorageClient;
import org.apache.commons.io.FileUtils;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

import java.io.File;
import java.io.IOException;

@RunWith(SpringRunner.class)
@SpringBootTest
public class TestFastDFS {

    @Autowired
    protected FastFileStorageClient storageClient;

    @Autowired
    private FdfsWebServer fdfsWebServer;

    @Test
    public void testUpload(){
        String path = "F:\\1.jpg";
        File file = new File(path);

        try {
            StorePath storePath = this.storageClient.uploadFile(FileUtils.openInputStream(file), file.length(), "jpg", null);

            System.out.println(storePath); //StorePath [group=group1, path=M00/00/00/wKgfUV2GJSuAOUd_AAHnjh7KpOc1.1.jpg]
            System.out.println(fdfsWebServer.getWebServerUrl() + storePath.getFullPath());//group1/M00/00/00/wKgfUV2GJSuAOUd_AAHnjh7KpOc1.1.jpg
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

~~~

通过浏览器访问图片：

 ![image-20201222171613466](assets/image-20201222171613466.png)

## 8、发布小视频

发布小视频的流程如下：

![image-20201224231912916](assets/image-20201224231912916.png)

说明：

- 用户发通过客户端APP上传视频到server服务
- server服务上传视频到FastDFS文件系统，上传成功后返回视频的url地址
- server通过rpc的调用dubbo服务进行保存小视频数据

### 8.1、dubbo服务

#### 8.1.1、编写pojo

在dubbo接口工程中编写pojo：

~~~java
package com.tanhua.dubbo.server.pojo;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.bson.types.ObjectId;
import org.springframework.data.mongodb.core.mapping.Document;

import java.util.List;

@Data
@NoArgsConstructor
@AllArgsConstructor
@Document(collection = "video")
public class Video implements java.io.Serializable {

    private static final long serialVersionUID = -3136732836884933873L;

    private ObjectId id; //主键id
    private Long vid; //自增长id
    private Long userId;
    private String text; //文字
    private String picUrl; //视频封面文件
    private String videoUrl; //视频文件
    private Long created; //创建时间
    private Integer seeType; // 谁可以看，1-公开，2-私密，3-部分可见，4-不给谁看
    private List<Long> seeList; //部分可见的列表
    private List<Long> notSeeList; //不给谁看的列表
    private String longitude; //经度
    private String latitude; //纬度
    private String locationName; //位置名称
}

~~~

#### 8.1.2、定义接口

~~~java
package com.tanhua.dubbo.server.api;

import com.tanhua.dubbo.server.pojo.Video;

public interface VideoApi {

    /**
     * 保存小视频
     *
     * @param video
     * @return 保存成功后，返回视频id
     */
    String saveVideo(Video video);

}

~~~

#### 8.1.3、编写实现

~~~java
package com.tanhua.dubbo.server.api;

import cn.hutool.core.util.ObjectUtil;
import com.alibaba.dubbo.config.annotation.Service;
import com.mongodb.Mongo;
import com.tanhua.dubbo.server.enums.IdType;
import com.tanhua.dubbo.server.pojo.Video;
import com.tanhua.dubbo.server.service.IdService;
import lombok.extern.slf4j.Slf4j;
import org.bson.types.ObjectId;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.mongodb.core.MongoTemplate;

@Service(version = "1.0.0")
@Slf4j
public class VideoApiImpl implements VideoApi {

    @Autowired
    private IdService idService;

    @Autowired
    private MongoTemplate mongoTemplate;

    /**
     * 发布小视频
     *
     * @param video
     * @return
     */
    @Override
    public String saveVideo(Video video) {
        try {
            //校验
            if(!ObjectUtil.isAllNotEmpty(video.getUserId(), video.getPicUrl(), video.getVideoUrl())){
                return null;
            }

            //设置id
            video.setId(ObjectId.get());
            video.setVid(this.idService.createId(IdType.VIDEO));

            //发布时间
            video.setCreated(System.currentTimeMillis());

            //保存到Mongodb中
            this.mongoTemplate.save(video);

            return video.getId().toHexString();
        } catch (Exception e) {
            log.error("小视频发布失败~ video = " + video, e);
        }
        return null;
    }
}

~~~

### 8.2、APP接口服务

接口地址：https://mock-java.itheima.net/project/35/interface/api/821

 ![image-20201222212229360](assets/image-20201222212229360.png)

#### 8.2.1、VideoController

~~~java
package com.tanhua.server.controller;

import com.tanhua.server.service.VideoService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.multipart.MultipartFile;

@RestController
@RequestMapping("smallVideos")
public class VideoController {

    @Autowired
    private VideoService videoService;

    /**
     * 发布小视频
     *
     * @param picFile
     * @param videoFile
     * @return
     */
    @PostMapping
    public ResponseEntity<Void> saveVideo(@RequestParam("videoThumbnail") MultipartFile picFile,
                                          @RequestParam("videoFile") MultipartFile videoFile) {
        try {
            Boolean bool = this.videoService.saveVideo(picFile, videoFile);
            if (bool) {
                return ResponseEntity.ok(null);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).build();
    }
}

~~~

#### 8.2.2、VideoService

~~~java
package com.tanhua.server.service;

import cn.hutool.core.util.StrUtil;
import com.alibaba.dubbo.config.annotation.Reference;
import com.github.tobato.fastdfs.domain.conn.FdfsWebServer;
import com.github.tobato.fastdfs.domain.fdfs.StorePath;
import com.github.tobato.fastdfs.service.FastFileStorageClient;
import com.tanhua.common.pojo.User;
import com.tanhua.common.service.PicUploadService;
import com.tanhua.common.utils.UserThreadLocal;
import com.tanhua.common.vo.PicUploadResult;
import com.tanhua.dubbo.server.api.VideoApi;
import com.tanhua.dubbo.server.pojo.Video;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.web.multipart.MultipartFile;

@Service
@Slf4j
public class VideoService {

    @Autowired
    private PicUploadService picUploadService;

    @Autowired
    protected FastFileStorageClient storageClient;

    @Autowired
    private FdfsWebServer fdfsWebServer;

    @Reference(version = "1.0.0")
    private VideoApi videoApi;

    /**
     * 发布小视频
     *
     * @param picFile
     * @param videoFile
     * @return
     */
    public Boolean saveVideo(MultipartFile picFile, MultipartFile videoFile) {
        User user = UserThreadLocal.get();

        Video video = new Video();
        video.setUserId(user.getId());
        video.setSeeType(1); //默认公开

        try {
            //上传封面图片
            PicUploadResult picUploadResult = this.picUploadService.upload(picFile);
            video.setPicUrl(picUploadResult.getName()); //图片路径

            //上传视频
            StorePath storePath = storageClient.uploadFile(videoFile.getInputStream(),
                    videoFile.getSize(),
                    StrUtil.subAfter(videoFile.getOriginalFilename(), '.', true),
                    null);

            //设置视频url
            video.setVideoUrl(fdfsWebServer.getWebServerUrl() + storePath.getFullPath());

            String videoId = this.videoApi.saveVideo(video);
            return StrUtil.isNotEmpty(videoId);
        } catch (Exception e) {
            log.error("发布小视频失败！file = " + picFile.getOriginalFilename() , e);
        }

        return false;
    }
}

~~~

#### 5.4.3、测试

如果上传视频，会导致异常，是因为请求太大的缘故：

![1569075180126](assets/1569075180126.png)

解决：application.properties

~~~properties
spring.servlet.multipart.max-file-size=30MB
spring.servlet.multipart.max-request-size=30MB
~~~

测试：

![image-20201222214127912](assets/image-20201222214127912.png)

![image-20201222214313987](assets/image-20201222214313987.png)

可以看到数据已经写入到了MongoDB中。

## 9、小视频列表

小视频的列表查询的实现需要注意的是，如果有推荐视频，优先返回推荐视频，如果不够或没有，按照时间倒序查询视频表。

推荐数据：

![image-20201222224514603](assets/image-20201222224514603.png)

### 9.1、dubbo服务

#### 9.1.1、定义dubbo服务

~~~java
package com.tanhua.dubbo.server.api;

import com.tanhua.dubbo.server.pojo.Video;
import com.tanhua.dubbo.server.vo.PageInfo;

public interface VideoApi {

    /**
     * 保存小视频
     *
     * @param video
     * @return
     */
    Boolean saveVideo(Video video);

    /**
     * 分页查询小视频列表，按照时间倒序排序
     *
     * @param userId
     * @param page
     * @param pageSize
     * @return
     */
    PageInfo<Video> queryVideoList(Long userId, Integer page, Integer pageSize);

}

~~~

#### 9.1.2、实现dubbo服务

~~~java
//com.tanhua.dubbo.server.api.VideoApiImpl

    /**
     * 查询小视频列表，优先展现推荐的视频，如果没有推荐的视频或已经查询完成，就需要查询系统视频数据
     *
     * @param userId
     * @param page
     * @param pageSize
     * @return
     */
    @Override
    public PageInfo<Video> queryVideoList(Long userId, Integer page, Integer pageSize) {
        PageInfo<Video> pageInfo = new PageInfo<>();
        pageInfo.setPageNum(page);
        pageInfo.setPageSize(pageSize);

        //从redis中获取推荐视频的数据
        String redisKey = "QUANZI_VIDEO_RECOMMEND_" + userId;
        String redisData = this.redisTemplate.opsForValue().get(redisKey);
        List<Long> vids = new ArrayList<>();
        int recommendCount = 0;
        if (StrUtil.isNotEmpty(redisData)) {
            //手动分页查询数据
            List<String> vidList = StrUtil.split(redisData, ',');
            //计算分页
            //[0, 10]
            int[] startEnd = PageUtil.transToStartEnd(page - 1, pageSize);
            int startIndex = startEnd[0]; //开始
            int endIndex = Math.min(startEnd[1], vidList.size()); //结束

            for (int i = startIndex; i < endIndex; i++) {
                vids.add(Convert.toLong(vidList.get(i)));
            }
            recommendCount = vidList.size();
        }

        if (CollUtil.isEmpty(vids)) {
            //没有推荐或前面推荐已经查询完毕，查询系统的视频数据

            //计算前面的推荐视频页数
            int totalPage = PageUtil.totalPage(recommendCount, pageSize);

            PageRequest pageRequest = PageRequest.of(page - totalPage - 1, pageSize, Sort.by(Sort.Order.desc("created")));
            Query query = new Query().with(pageRequest);
            List<Video> videoList = this.mongoTemplate.find(query, Video.class);
            pageInfo.setRecords(videoList);
            return pageInfo;
        }

        //根据vid查询对应的视频数据了
        Query query = Query.query(Criteria.where("vid").in(vids));
        List<Video> videoList = this.mongoTemplate.find(query, Video.class);
        pageInfo.setRecords(videoList);

        return pageInfo;
    }
~~~

#### 9.1.3、测试用例

~~~java
package com.tanhua.dubbo.server.api;

import com.tanhua.dubbo.server.pojo.Video;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

@RunWith(SpringRunner.class)
@SpringBootTest
public class TestVideoApi {

    @Autowired
    private VideoApi videoApi;

    @Test
    public void testQueryVideoList() {
        //返回的推荐结果数据
        System.out.println(this.videoApi.queryVideoList(1L, 1, 8));
        //返回少于pageSize数据，因为推荐数据不够了
        System.out.println(this.videoApi.queryVideoList(1L, 3, 8));

        //返回系统数据
        System.out.println(this.videoApi.queryVideoList(1L, 4, 8));

    }

}

~~~

### 9.2、APP接口服务

服务地址：https://mock-java.itheima.net/project/35/interface/api/815

#### 9.2.1、定义VideoVo

~~~java
package com.tanhua.server.vo;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
@AllArgsConstructor
public class VideoVo {

    private String id;
    private Long userId;
    private String avatar; //头像
    private String nickname; //昵称
    private String cover; //封面
    private String videoUrl; //视频URL
    private String signature; //签名
    private Integer likeCount; //点赞数量
    private Integer hasLiked; //是否已赞（1是，0否）
    private Integer hasFocus; //是是否关注 （1是，0否）
    private Integer commentCount; //评论数量
}

~~~

#### 9.2.2、VideoController

~~~java
@RestController
@RequestMapping("smallVideos")
public class VideoController {    
/**
     * 查询小视频列表
     *
     * @param page
     * @param pageSize
     * @return
     */
    @GetMapping
    public ResponseEntity<PageResult> queryVideoList(@RequestParam(value = "page", defaultValue = "1") Integer page,
                                                     @RequestParam(value = "pagesize", defaultValue = "10") Integer pageSize) {
        try {
            if (page <= 0) {
                page = 1;
            }
            PageResult pageResult = this.videoService.queryVideoList(page, pageSize);
            if (null != pageResult) {
                return ResponseEntity.ok(pageResult);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).build();
    }
}
~~~

#### 9.2.3、VideoService

~~~java
//com.tanhua.server.service.VideoService

	public PageResult queryVideoList(Integer page, Integer pageSize) {
        User user = UserThreadLocal.get();

        PageResult pageResult = new PageResult();
        pageResult.setPage(page);
        pageResult.setPagesize(pageSize);

        PageInfo<Video> pageInfo = this.videoApi.queryVideoList(user.getId(), page, pageSize);
        List<Video> records = pageInfo.getRecords();

        if(CollUtil.isEmpty(records)){
            return pageResult;
        }

        //查询用户信息
        List<Object> userIds = CollUtil.getFieldValues(records, "userId");
        List<UserInfo> userInfoList = this.userInfoService.queryUserInfoByUserIdList(userIds);

        List<VideoVo> videoVoList = new ArrayList<>();
        for (Video record : records) {
            VideoVo videoVo = new VideoVo();

            videoVo.setUserId(record.getUserId());
            videoVo.setCover(record.getPicUrl());
            videoVo.setVideoUrl(record.getVideoUrl());
            videoVo.setId(record.getId().toHexString());
            videoVo.setSignature("我就是我~"); //TODO 签名

            videoVo.setCommentCount(0); //TODO 评论数
            videoVo.setHasFocus(0); //TODO 是否关注
            videoVo.setHasLiked(0); //TODO 是否点赞（1是，0否）
            videoVo.setLikeCount(0);//TODO 点赞数

            //填充用户信息
            for (UserInfo userInfo : userInfoList) {
                if (ObjectUtil.equals(videoVo.getUserId(), userInfo.getUserId())) {
                    videoVo.setNickname(userInfo.getNickName());
                    videoVo.setAvatar(userInfo.getLogo());
                    break;
                }
            }

            videoVoList.add(videoVo);
        }

        pageResult.setItems(videoVoList);
        return pageResult;
    }
~~~

#### 9.2.5、测试

 ![image-20201222234918988](assets/image-20201222234918988.png)

![image-20201222234936528](assets/image-20201222234936528.png)

可以看到已经查询到数据。下面使用手机进行测试：

 ![image-20201222235041124](assets/image-20201222235041124.png)
