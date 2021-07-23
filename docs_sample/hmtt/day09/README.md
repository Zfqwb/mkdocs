# 01-今日学习目标 00:49

- 能够完成app端文章列表展示功能开发
- 能够完成app端文章详情的功能开发
- 能够掌握解决long类型丢失精度的问题
- 能够完成app端登录的功能
- 能够掌握关注作者功能



# 02-文章列表-需求分析

![Snipaste_2020-10-13_07-34-34](asserts\Snipaste_2020-10-13_07-34-34.png)



1,在默认频道展示10条文章信息						

	>分页查询

2,可以切换频道查看不同种类文章

>根据频道类别进行查询

3,当用户下拉可以加载最新的文章信息（按照发布时间）

>见上图，根据发布时间查询

 本页文章列表中发布时间最大的时间为依据

4,当用户上拉可以加载更多的文章（分页）

 本页文章列表中发布时间为最小的时间为依据



![Snipaste_2020-10-13_07-35-23](asserts\Snipaste_2020-10-13_07-35-23.png)

# 03-接口定义 04:11

# article-home-controller


## load

**接口地址**:`/api/v1/article/load`

**请求方式**:`POST`

**请求数据类型**:`application/json`

**响应数据类型**:`*/*`

**接口描述**:


**请求示例**:


```javascript
{
	"maxBehotTime": "",
	"minBehotTime": "",
	"size": 10,
	"tag": ""
}
```

**请求参数**:


| 参数名称                 | 参数说明             | in   | 是否必须 | 数据类型          | schema         |
| ------------------------ | -------------------- | ---- | -------- | ----------------- | -------------- |
| dto                      | dto                  | body | true     | ArticleHomeDto    | ArticleHomeDto |
| &emsp;&emsp;maxBehotTime | 最大时间             |      | false    | string(date-time) |                |
| &emsp;&emsp;minBehotTime | 最小时间             |      | false    | string(date-time) |                |
| &emsp;&emsp;size         | 当前页展示数据数据量 |      | false    | integer(int32)    |                |
| &emsp;&emsp;tag          | 频道ID               |      | false    | string            |                |


**响应状态**:


| 状态码 | 说明         | schema         |
| ------ | ------------ | -------------- |
| 200    | OK           | ResponseResult |
| 201    | Created      |                |
| 401    | Unauthorized |                |
| 403    | Forbidden    |                |
| 404    | Not Found    |                |


**响应参数**:


| 参数名称     | 参数说明 | 类型           | schema         |
| ------------ | -------- | -------------- | -------------- |
| code         |          | integer(int32) | integer(int32) |
| data         |          | object         |                |
| errorMessage |          | string         |                |
| host         |          | string         |                |


**响应示例**:

```javascript
{
	"code": 0,
	"data": {},
	"errorMessage": "",
	"host": ""
}
```


## loadMore

**接口地址**:`/api/v1/article/loadmore`


**请求方式**:`POST`

**请求数据类型**:`application/json`


**响应数据类型**:`*/*`


**接口描述**:

**请求示例**:


```javascript
{
	"maxBehotTime": "",
	"minBehotTime": "",
	"size": 0,
	"tag": ""
}
```


**请求参数**:


| 参数名称                 | 参数说明 | in   | 是否必须 | 数据类型          | schema         |
| ------------------------ | -------- | ---- | -------- | ----------------- | -------------- |
| dto                      | dto      | body | true     | ArticleHomeDto    | ArticleHomeDto |
| &emsp;&emsp;maxBehotTime |          |      | false    | string(date-time) |                |
| &emsp;&emsp;minBehotTime |          |      | false    | string(date-time) |                |
| &emsp;&emsp;size         |          |      | false    | integer(int32)    |                |
| &emsp;&emsp;tag          |          |      | false    | string            |                |


**响应状态**:


| 状态码 | 说明         | schema         |
| ------ | ------------ | -------------- |
| 200    | OK           | ResponseResult |
| 201    | Created      |                |
| 401    | Unauthorized |                |
| 403    | Forbidden    |                |
| 404    | Not Found    |                |


**响应参数**:


| 参数名称     | 参数说明 | 类型           | schema         |
| ------------ | -------- | -------------- | -------------- |
| code         |          | integer(int32) | integer(int32) |
| data         |          | object         |                |
| errorMessage |          | string         |                |
| host         |          | string         |                |


**响应示例**:

```javascript
{
	"code": 0,
	"data": {},
	"errorMessage": "",
	"host": ""
}
```


## loadNew


**接口地址**:`/api/v1/article/loadnew`


**请求方式**:`POST`


**请求数据类型**:`application/json`


**响应数据类型**:`*/*`


**接口描述**:


**请求示例**:


```javascript
{
	"maxBehotTime": "",
	"minBehotTime": "",
	"size": 0,
	"tag": ""
}
```


**请求参数**:


| 参数名称                 | 参数说明 | in   | 是否必须 | 数据类型          | schema         |
| ------------------------ | -------- | ---- | -------- | ----------------- | -------------- |
| dto                      | dto      | body | true     | ArticleHomeDto    | ArticleHomeDto |
| &emsp;&emsp;maxBehotTime |          |      | false    | string(date-time) |                |
| &emsp;&emsp;minBehotTime |          |      | false    | string(date-time) |                |
| &emsp;&emsp;size         |          |      | false    | integer(int32)    |                |
| &emsp;&emsp;tag          |          |      | false    | string            |                |


**响应状态**:


| 状态码 | 说明         | schema         |
| ------ | ------------ | -------------- |
| 200    | OK           | ResponseResult |
| 201    | Created      |                |
| 401    | Unauthorized |                |
| 403    | Forbidden    |                |
| 404    | Not Found    |                |


**响应参数**:


| 参数名称     | 参数说明 | 类型           | schema         |
| ------------ | -------- | -------------- | -------------- |
| code         |          | integer(int32) | integer(int32) |
| data         |          | object         |                |
| errorMessage |          | string         |                |
| host         |          | string         |                |


**响应示例**:

```javascript
{
	"code": 0,
	"data": {},
	"errorMessage": "",
	"host": ""
}
```



在apis模块中新增接口

```java
package com.heima.apis.article;

import com.heima.model.article.dtos.ArticleHomeDto;
import com.heima.model.common.dtos.ResponseResult;

public interface ArticleHomeControllerApi {


    /**
     * 加载首页文章
     * @return
     */
    public ResponseResult load(ArticleHomeDto dto);

    /**
     * 加载更多
     * @return
     */
    public ResponseResult loadMore(ArticleHomeDto dto);

    /**
     * 加载最新
     * @return
     */
    public ResponseResult loadNew(ArticleHomeDto dto);
}
```

ArticleHomeDto

```java
package com.heima.model.article.dtos;

import lombok.Data;

import java.util.Date;

@Data
public class ArticleHomeDto {

    // 最大时间
    Date maxBehotTime;
    // 最小时间
    Date minBehotTime;
    // 分页size
    Integer size;
    // 数据范围，比如频道ID
    String tag;
}
```



# 04-SQL编写 10:05

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.heima.article.mapper.ApArticleMapper">

    <resultMap id="resultMap" type="com.heima.model.article.pojos.ApArticle">
        <id column="id" property="id"/>
        <result column="title" property="title"/>
        <result column="author_id" property="authorId"/>
        <result column="author_name" property="authorName"/>
        <result column="channel_id" property="channelId"/>
        <result column="channel_name" property="channelName"/>
        <result column="layout" property="layout"/>
        <result column="flag" property="flag"/>
        <result column="images" property="images"/>
        <result column="labels" property="labels"/>
        <result column="likes" property="likes"/>
        <result column="collection" property="collection"/>
        <result column="comment" property="comment"/>
        <result column="views" property="views"/>
        <result column="province_id" property="provinceId"/>
        <result column="city_id" property="cityId"/>
        <result column="county_id" property="countyId"/>
        <result column="created_time" property="createdTime"/>
        <result column="publish_time" property="publishTime"/>
        <result column="sync_status" property="syncStatus"/>
    </resultMap>
    <select id="loadArticleList" resultMap="resultMap">
        SELECT
        aa.*
        FROM
        `ap_article` aa
        LEFT JOIN ap_article_config aac ON aa.id = aac.article_id
        <where>
            and aac.is_delete != 1
            and aac.is_down != 1
            <!-- loadmore -->
            <if test="type != null and type == 1">
                and aa.publish_time &lt; #{dto.minBehotTime}
            </if>
            <if test="type != null and type == 2">
               <![CDATA[ and aa.publish_time > #{dto.maxBehotTime} ]]>
            </if>
            <if test="dto.tag != '__all__'">
                and aa.channel_id = #{dto.tag}
            </if>
        </where>
        order by aa.publish_time desc
        limit #{dto.size}
    </select>

</mapper>
```



# 05-业务层代码编写 14:02

```java
package com.heima.article.mapper;

import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.heima.model.article.dtos.ArticleHomeDto;
import com.heima.model.article.pojos.ApArticle;
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Param;

import java.util.List;

@Mapper
public interface ApArticleMapper extends BaseMapper<ApArticle> {

    public List<ApArticle> loadArticleList(@Param("dto") ArticleHomeDto dto,@Param("type") Short type);

}
```

```java
    @Override
    public ResponseResult load(ArticleHomeDto dto, Short loadType) {
        //1.校验参数
        Integer size = dto.getSize();
        if(size == null || size <= 0){
            size = 10;
        }
        //最多只能请求50条，如果>=50就取50
        size = Math.min(size,MAX_PAGE_SIZE); 
        dto.setSize(size);

        //类型参数检验 判断如果不等于1和2，给出默认值1
        if(!loadType.equals(ArticleConstans.LOADTYPE_LOAD_MORE)&&!loadType.equals(ArticleConstans.LOADTYPE_LOAD_NEW)){
            loadType = ArticleConstans.LOADTYPE_LOAD_MORE;
        }
        //文章频道校验 如果没有传递tag参数，就给出默认的值_all_,查询所有数据
        if(StringUtils.isEmpty(dto.getTag())){
            dto.setTag(ArticleConstans.DEFAULT_TAG);
        }

        //时间校验 如果为空，设置为最新的时间
        if(dto.getMaxBehotTime() == null) dto.setMaxBehotTime(new Date());
        if(dto.getMinBehotTime() == null) dto.setMinBehotTime(new Date());
        //2.查询数据
        List<ApArticle> apArticles = apArticleMapper.loadArticleList(dto, loadType);

        //3.结果封装 host地址 + 图片ID 拼成图片URL
        ResponseResult responseResult = ResponseResult.okResult(apArticles);
        responseResult.setHost(fileServerUrl);
        return responseResult;
    }
```



# 06-测试和网关搭建 08:50

# 07-前端搭建和联调测试 10:44

#### *注意：按TAB键才可以在前端选中输入框*

1.使用vs code打开项目之后，使用npm install先更新项目（可不做）

2.根据讲义中的内容修改IP地址（已修改为127.0.0.1，本地调试就不用改，如果是WIFI调试，改成同一网段下的内网IP）

3.使用Terminal终端，npm run start命令启动即可



>前端需要登录才可以测试文章内容展示，先完成登录功能再测试

# 08-需求说明和思路分析 03:21

文章详情所展示的主要是文章的内容，这个时候无须再次加重ap_article表中的数据，只需要通过前端传递过来的文章id去查询文章的内容即可，同时需要判断当前文章是否已上架和是否已经删除。

由分析得出，主要是通过文章id查询ap_article_content和ap_article_config表的数据即可。

ap_article_content app文章内容表

![1586157495840](data:image/jpg;base64,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)

ap_article_config app文章配置表

![1586157523334](data:image/jpg;base64,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)



## loadArticleInfo

**接口地址**:`/api/v1/article/load_article_info`

**请求方式**:`POST`


**请求数据类型**:`application/json`


**响应数据类型**:`*/*`

**接口描述**:


**请求示例**:


```javascript
{
	"articleId": 0
}
```

**请求参数**:


**请求参数**:


| 参数名称              | 参数说明 | in   | 是否必须 | 数据类型       | schema         |
| --------------------- | -------- | ---- | -------- | -------------- | -------------- |
| dto                   | dto      | body | true     | ArticleInfoDto | ArticleInfoDto |
| &emsp;&emsp;articleId |          |      | false    | integer(int64) |                |


**响应状态**:


| 状态码 | 说明         | schema         |
| ------ | ------------ | -------------- |
| 200    | OK           | ResponseResult |
| 201    | Created      |                |
| 401    | Unauthorized |                |
| 403    | Forbidden    |                |
| 404    | Not Found    |                |


**响应参数**:


| 参数名称     | 参数说明 | 类型           | schema         |
| ------------ | -------- | -------------- | -------------- |
| code         |          | integer(int32) | integer(int32) |
| data         |          | object         |                |
| errorMessage |          | string         |                |
| host         |          | string         |                |


**响应示例**:
```javascript
{
	"code": 0,
	"data": {
        "content" : 文章对象ApArticleContent,
        "config" : 文章配置对象ApArticleConfig     
    },
	"errorMessage": "",
	"host": ""
}
```



# 09-功能开发 10:43

```java
package com.heima.article.service.impl;

import com.baomidou.mybatisplus.core.toolkit.Wrappers;
import com.heima.article.mapper.ApArticleConfigMapper;
import com.heima.article.mapper.ApArticleContentMapper;
import com.heima.article.service.ArticleInfoService;
import com.heima.model.article.dtos.ArticleInfoDto;
import com.heima.model.article.pojos.ApArticleConfig;
import com.heima.model.article.pojos.ApArticleContent;
import com.heima.model.common.dtos.ResponseResult;
import com.heima.model.common.enums.AppHttpCodeEnum;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.HashMap;
import java.util.Map;

@Service
public class ArticleInfoServiceImpl implements ArticleInfoService {

    @Autowired
    private ApArticleConfigMapper apArticleConfigMapper;

    @Autowired
    private ApArticleContentMapper apArticleContentMapper;

    @Override
    public ResponseResult loadArticleInfo(ArticleInfoDto dto) {

        Map<String, Object> resultMap = new HashMap<>();

        //1.检查参数
        if(dto == null || dto.getArticleId() == null){
            return ResponseResult.errorResult(AppHttpCodeEnum.PARAM_INVALID);
        }

        //2.查询文章的配置
        ApArticleConfig apArticleConfig = apArticleConfigMapper.selectOne(Wrappers.<ApArticleConfig>lambdaQuery().eq(ApArticleConfig::getArticleId, dto.getArticleId()));
        if(apArticleConfig == null){
            return ResponseResult.errorResult(AppHttpCodeEnum.PARAM_INVALID);
        }

        //3.查询文章的内容
        if(!apArticleConfig.getIsDelete()&& !apArticleConfig.getIsDown()){
            ApArticleContent apArticleContent = apArticleContentMapper.selectOne(Wrappers.<ApArticleContent>lambdaQuery().eq(ApArticleContent::getArticleId, dto.getArticleId()));
            resultMap.put("content",apArticleContent);
        }else{
            //当前文章已经被下架或者删除，无法进行阅读
            return ResponseResult.errorResult(AppHttpCodeEnum.PARAM_INVALID);
        }
        resultMap.put("config",apArticleConfig);
        //4.结果返回
        return ResponseResult.okResult(resultMap);
    }
}
```



# 10-APP文章详情-测试  04:52

1.启动article微服务

2.启动app网关

3.启动前端项目 npm run start



# 11-Long类型转换 12:25

产生原因：

js 的 number 类型支持的最大值是9007199254740992 （2 的 53次方 -1）16位，溢出之后的精度会丢失，导致前后端的值不一致。
java 的 long 类型最大值为 9223372036854775807  19位，远高于 js number类型的最大值，所以这个坑就出现了。



解决思路：

方案一：将文章的id的由long类型手动改为String类型，可以解决此问题。（需要修改表结构）

方案二：可以使用jackson进行序列化和反序列化解决（本项目采用这种方案）



这部分不需要同学们自己写，把相关代码放进去即可。



jackson的配置文件

```java
package com.heima.common.jackson;

import com.fasterxml.jackson.core.Version;
import com.fasterxml.jackson.databind.DeserializationFeature;
import com.fasterxml.jackson.databind.Module;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.PropertyNamingStrategy;

public class ConfusionModule extends Module {

    public final static String MODULE_NAME = "jackson-confusion-encryption";
    public final static Version VERSION = new Version(1,0,0,null,"heima",MODULE_NAME);

    @Override
    public String getModuleName() {
        return MODULE_NAME;
    }

    @Override
    public Version version() {
        return VERSION;
    }

    @Override
    public void setupModule(SetupContext context) {
        context.addBeanSerializerModifier(new ConfusionSerializerModifier());
        context.addBeanDeserializerModifier(new ConfusionDeserializerModifier());
    }

    /**
     * 注册当前模块
     * @return
     */
    public static ObjectMapper registerModule(ObjectMapper objectMapper){
        //CamelCase策略，Java对象属性：personId，序列化后属性：persionId
        //PascalCase策略，Java对象属性：personId，序列化后属性：PersonId
        //SnakeCase策略，Java对象属性：personId，序列化后属性：person_id
        //KebabCase策略，Java对象属性：personId，序列化后属性：person-id
        // 忽略多余字段，抛错
        objectMapper.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false);
//        objectMapper.setPropertyNamingStrategy(PropertyNamingStrategy.SNAKE_CASE);
        return objectMapper.registerModule(new ConfusionModule());
    }

}
```



jackson的配置类：

```java
package com.heima.common.jackson;

import com.fasterxml.jackson.databind.ObjectMapper;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class InitJacksonConfig {

    @Bean
    public ObjectMapper objectMapper() {
        ObjectMapper objectMapper = new ObjectMapper();
        objectMapper = ConfusionModule.registerModule(objectMapper);
        return objectMapper;
    }

}
```



在article中启动配置类：

```java
package com.heima.article.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan("com.heima.common.jackson")
public class InitConfig {
}
```



# 12-登录 需求及思路 04:21

点击登录，校验手机号密码，获取用户ID生成jwtToken

不登录先看看，直接使用0作为用户ID生成jwtToken



# 13-功能实现 12:13

```java
package com.heima.user.service.impl;

import com.baomidou.mybatisplus.core.toolkit.Wrappers;
import com.heima.model.common.dtos.ResponseResult;
import com.heima.model.common.enums.AppHttpCodeEnum;
import com.heima.model.user.dtos.LoginDto;
import com.heima.model.user.pojos.ApUser;
import com.heima.user.mapper.ApUserMapper;
import com.heima.user.service.ApUserLoginService;
import com.heima.utils.common.AppJwtUtil;
import org.apache.commons.lang3.StringUtils;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.util.DigestUtils;

import java.util.HashMap;
import java.util.Map;

@Service
public class ApUserLoginServiceImpl implements ApUserLoginService {

    @Autowired
    ApUserMapper apUserMapper;

    @Override
    public ResponseResult login(LoginDto dto) {
        //1.校验参数
        //2.这儿写错了，应该是手机号 密码 设备ID同时为空 返回错误
        if(dto.getEquipmentId() == null &&(StringUtils.isEmpty(dto.getPhone())&&StringUtils.isEmpty(dto.getPassword()))){
            return ResponseResult.errorResult(AppHttpCodeEnum.PARAM_INVALID);
        }

        //2.手机号+密码登录
        if(!StringUtils.isEmpty(dto.getPhone())&&!StringUtils.isEmpty(dto.getPassword())){
            //用户登录
            ApUser dbUser = apUserMapper.selectOne(Wrappers.<ApUser>lambdaQuery().eq(ApUser::getPhone, dto.getPhone()));
            if (dbUser!=null) {
                String pswd = DigestUtils.md5DigestAsHex((dto.getPassword()+dbUser.getSalt()).getBytes());
                if (dbUser.getPassword().equals(pswd)) {
                    Map<String, Object> map = new HashMap<>();
                    dbUser.setPassword("");
                    dbUser.setSalt("");
                    map.put("token", AppJwtUtil.getToken(dbUser.getId().longValue()));
                    map.put("user", dbUser);
                    return ResponseResult.okResult(map);
                } else {
                    return ResponseResult.errorResult(AppHttpCodeEnum.LOGIN_PASSWORD_ERROR);
                }
            } else {
                return ResponseResult.errorResult(AppHttpCodeEnum.DATA_NOT_EXIST, "用户不存在");
            }
        }else {
            //3.设备登录
            if(dto.getEquipmentId() == null){
                return ResponseResult.errorResult(AppHttpCodeEnum.PARAM_INVALID);
            }
            //根据设备ID，在用户表中查询一下，如果用户存在，返回用户ID，如果用户不存在，创建用户并且绑定设备ID
            //设备登录直接给用户设置id为0
            Map<String,Object> map = new HashMap<>();
            map.put("token",AppJwtUtil.getToken(0l));
            return ResponseResult.okResult(map);

        }

    }
}
```



# 14-网关搭建及测试 12:13

# 15-关注和取消关注-需求和思路分析 06:48

点击按钮可以关注用户，再次点击取消关注

一个用户可以关注多个作者，一个作者也可以有多个粉丝

>1.前端传递作者ID
>
>2.用户保存关注的作者，作者保存当前粉丝，这是两张表
>
>3.取消关注，要删除上述两张表的内容

两张表：

app关注信息表 ap_user_follow

```sql
CREATE TABLE `ap_user_follow` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT COMMENT '主键',
  `user_id` int(11) unsigned DEFAULT NULL COMMENT '用户ID',
  `follow_id` int(11) unsigned DEFAULT NULL COMMENT '关注作者ID',
  `follow_name` varchar(20) COLLATE utf8mb4_unicode_ci DEFAULT NULL COMMENT '粉丝昵称',
  `level` tinyint(1) unsigned DEFAULT NULL COMMENT '关注度\r\n            0 偶尔感兴趣\r\n            1 一般\r\n            2 经常\r\n            3 高度',
  `is_notice` tinyint(1) unsigned DEFAULT NULL COMMENT '是否动态通知',
  `created_time` datetime DEFAULT NULL COMMENT '创建时间',
  PRIMARY KEY (`id`) USING BTREE
) ENGINE=InnoDB AUTO_INCREMENT=14 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci ROW_FORMAT=DYNAMIC COMMENT='APP用户关注信息表'

```





app用户粉丝表 ap_user_fan

```sql
CREATE TABLE `ap_user_fan` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT COMMENT '主键',
  `user_id` int(11) unsigned DEFAULT NULL COMMENT '作者ID',
  `fans_id` int(11) unsigned DEFAULT NULL COMMENT '粉丝ID',
  `fans_name` varchar(20) COLLATE utf8mb4_unicode_ci DEFAULT NULL COMMENT '粉丝昵称',
  `level` tinyint(1) unsigned DEFAULT NULL COMMENT '粉丝忠实度\r\n            0 正常\r\n            1 潜力股\r\n            2 勇士\r\n            3 铁杆\r\n            4 老铁',
  `created_time` datetime DEFAULT NULL COMMENT '创建时间',
  `is_display` tinyint(1) unsigned DEFAULT NULL COMMENT '是否可见我动态',
  `is_shield_letter` tinyint(1) unsigned DEFAULT NULL COMMENT '是否屏蔽私信',
  `is_shield_comment` tinyint(1) unsigned DEFAULT NULL COMMENT '是否屏蔽评论',
  PRIMARY KEY (`id`) USING BTREE
) ENGINE=InnoDB AUTO_INCREMENT=13 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci ROW_FORMAT=DYNAMIC COMMENT='APP用户粉丝信息表'

```





# 16-接口定义 07:56

## follow

**接口地址**:`/api/v1/user/user_follow`

**请求方式**:`POST`


**请求数据类型**:`application/json`


**响应数据类型**:`*/*`


**接口描述**:

**请求示例**:


```javascript
{
	"articleId": 0,
	"authorId": 0,
	"operation": 0
}
```

**请求参数**:

**请求参数**:


| 参数名称              | 参数说明    | in   | 是否必须 | 数据类型        | schema          |
| --------------------- | ----------- | ---- | -------- | --------------- | --------------- |
| dto                   | dto         | body | true     | UserRelationDto | UserRelationDto |
| &emsp;&emsp;articleId | 文章ID      |      | false    | integer(int64)  |                 |
| &emsp;&emsp;authorId  | 作者ID      |      | false    | integer(int32)  |                 |
| &emsp;&emsp;operation | 0关注 1取消 |      | false    | integer(int32)  |                 |


**响应状态**:


| 状态码 | 说明         | schema         |
| ------ | ------------ | -------------- |
| 200    | OK           | ResponseResult |
| 201    | Created      |                |
| 401    | Unauthorized |                |
| 403    | Forbidden    |                |
| 404    | Not Found    |                |


**响应参数**:


| 参数名称     | 参数说明 | 类型           | schema         |
| ------------ | -------- | -------------- | -------------- |
| code         |          | integer(int32) | integer(int32) |
| data         |          | object         |                |
| errorMessage |          | string         |                |
| host         |          | string         |                |


**响应示例**:
```javascript
{
	"code": 0,
	"data": {},
	"errorMessage": "",
	"host": ""
}
```



# 17-获取作者和获取登录人 09:59

>前端传递的author_id是ap_author表中的id,所以需要查询出对应的user_id

 

```java
package com.heima.user.feign;

import com.heima.model.article.pojos.ApAuthor;
import com.heima.model.common.dtos.ResponseResult;
import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;

@FeignClient("leadnews-article")
public interface ArticleFeign {

    @GetMapping("/api/v1/author/findByUserId/{id}")
    public ApAuthor findByUserId(@PathVariable("id") Integer id);

    @PostMapping("/api/v1/author/save")
    public ResponseResult save(@RequestBody ApAuthor apAuthor);

    @GetMapping("/api/v1/author/one/{id}")
    public ApAuthor findById(@PathVariable("id") Integer id);
}

```



AuthorController：

```java
    @GetMapping("/one/{id}")
    @Override
    public ApAuthor findById(@PathVariable("id") Integer id) {
        return authorService.getById(id);
    }
```



user模块中添加过滤器：

```java
package com.heima.user.filter;

import com.heima.model.user.pojos.ApUser;
import com.heima.model.wemedia.pojos.WmUser;
import com.heima.utils.threadlocal.AppThreadLocalUtils;
import com.heima.utils.threadlocal.WmThreadLocalUtils;
import org.springframework.core.annotation.Order;
import org.springframework.web.filter.GenericFilterBean;

import javax.servlet.FilterChain;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.annotation.WebFilter;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@Order(1)
@WebFilter(filterName = "appTokenFilter", urlPatterns = "/*")
public class AppTokenFilter extends GenericFilterBean {
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        HttpServletRequest request = (HttpServletRequest) servletRequest;
        HttpServletResponse response = (HttpServletResponse) servletResponse;
        //得到header中的信息
        String userId = request.getHeader("userId");
        if (userId != null && Integer.valueOf(userId).intValue() != 0) {
            ApUser apUser = new ApUser();
            apUser.setId(Integer.valueOf(userId));
            AppThreadLocalUtils.setUser(apUser);
        }
        filterChain.doFilter(request, response);
    }
}
```



在utils中添加工具类

```java
package com.heima.utils.threadlocal;

import com.heima.model.user.pojos.ApUser;
import com.heima.model.wemedia.pojos.WmUser;

public class AppThreadLocalUtils {

    private final static ThreadLocal<ApUser> USER_THREAD_LOCAL = new ThreadLocal<>();

    /**
     * 设置当前线程中的用户
     * @param wmUser
     */
    public static void setUser(ApUser wmUser){
        USER_THREAD_LOCAL.set(wmUser);
    }

    /**
     * 获取当前线程中的用户
     * @return
     */
    public static ApUser getUser(){
        return USER_THREAD_LOCAL.get();
    }
}
```



# 18-业务层 12:32

```java
public ResponseResult follow(UserRelationDto dto) {
    //1.参数检查
    if(dto.getOperation() == null || dto.getOperation()<0 || dto.getOperation() > 1){
        return ResponseResult.errorResult(AppHttpCodeEnum.PARAM_INVALID);
    }
    if(dto.getAuthorId() == null){
        return ResponseResult.errorResult(AppHttpCodeEnum.PARAM_REQUIRE,"作者信息不能为空");
    }

    //2.获取作者
    Integer followId = null;
    ApAuthor apAuthor = articleFeign.findById(dto.getAuthorId());
    if(apAuthor != null){
        followId = apAuthor.getUserId();
    }
    if(followId == null){
        return ResponseResult.errorResult(AppHttpCodeEnum.DATA_NOT_EXIST,"关注人不存在");
    }else{

        ApUser apUser = AppThreadLocalUtils.getUser();
        if(apUser!= null){

            if(dto.getOperation() == 0){
                //3.如果当前操作是0 创建数据（app用户关注信息和app的用户粉丝信息）
                return followByUserId(apUser,followId,dto.getArticleId());
            }else{
                //4.如果当前操作是1 删除数据（app用户关注信息和app的用户粉丝信息）
                return followCancelByUserId(apUser,followId);
            }


        }else{
            return ResponseResult.errorResult(AppHttpCodeEnum.NEED_LOGIN);
        }
    }

}
```



# 19-处理关注逻辑 15:41

```java
/**
     * 处理关注的操作
     * @param apUser
     * @param followId
     * @param articleId
     * @return
     */
    private ResponseResult followByUserId(ApUser apUser, Integer followId, Long articleId) {
        //判断当前关注人是否存在
        ApUser followUser = apUserMapper.selectById(followId);
        ApUser fansUser = apUserMapper.selectById(apUser.getId());
        if(followUser == null || fansUser==null){
            return ResponseResult.errorResult(AppHttpCodeEnum.DATA_NOT_EXIST,"关注用户或者登陆用户不存在");
        }

        //判断用户是否重复关注
        ApUserFollow apUserFollow = apUserFollowMapper.selectOne(Wrappers.<ApUserFollow>lambdaQuery().eq(ApUserFollow::getUserId, apUser.getId()).eq(ApUserFollow::getFollowId, followId));
        //没有找到关注信息，所以是未关注的状态
        if(apUserFollow == null){
            //保存数据  ap_user_follow   ap_user_fan

            //查询是否有粉丝关注信息，如果有的话，也不需要保存
            ApUserFan apUserFan = apUserFanMapper.selectOne(Wrappers.<ApUserFan>lambdaQuery().eq(ApUserFan::getUserId, followId).eq(ApUserFan::getFansId, apUser.getId()));
            //保存app用户粉丝信息
            if(apUserFan == null){
                apUserFan = new ApUserFan();
                //粉丝表中的userId,就是关注的作者ID
                apUserFan.setUserId(followId);
                //粉丝表中的fansId,就是登录人
                apUserFan.setFansId(apUser.getId().longValue());
                //登录人名字(修改为粉丝名字)
                apUserFan.setFansName(fansUser.getName());
                apUserFan.setLevel((short)0);
                apUserFan.setIsDisplay(true);
                apUserFan.setIsShieldLetter(false);
                apUserFan.setIsShieldComment(false);
                apUserFan.setCreatedTime(new Date());
                apUserFanMapper.insert(apUserFan);
            }else{
                //如果粉丝存在，返回错误
                            return 				ResponseResult.errorResult(AppHttpCodeEnum.DATA_EXIST,"已关注");
            }
            //保存app用户关注信息
            apUserFollow = new ApUserFollow();
            apUserFollow.setUserId(apUser.getId());
            apUserFollow.setFollowId(followId);
            //设置关注的作者名字
            apUserFollow.setFollowName(apUserFollow.getName());
            apUserFollow.setCreatedTime(new Date());
            apUserFollow.setIsNotice(true);
            apUserFollow.setLevel((short)1);
            apUserFollowMapper.insert(apUserFollow);
            //TODO 记录关注文章的行为
            return ResponseResult.okResult(AppHttpCodeEnum.SUCCESS);
        }else{
            //已关注
            return ResponseResult.errorResult(AppHttpCodeEnum.DATA_EXIST,"已关注");
        }
    }
```



# 20-取消关注逻辑 11:17

```java
/**
 * 取消关注的处理逻辑
 * @param apUser
 * @param followId
 * @return
 */
private ResponseResult followCancelByUserId(ApUser apUser, Integer followId) {
    //先查再删
    ApUserFollow apUserFollow = apUserFollowMapper.selectOne(Wrappers.<ApUserFollow>lambdaQuery().eq(ApUserFollow::getUserId, apUser.getId()).eq(ApUserFollow::getFollowId, followId));
    if(apUserFollow == null){
        return ResponseResult.errorResult(AppHttpCodeEnum.DATA_NOT_EXIST,"未关注");
    }else{
        ApUserFan apUserFan = apUserFanMapper.selectOne(Wrappers.<ApUserFan>lambdaQuery().eq(ApUserFan::getUserId, followId).eq(ApUserFan::getFansId, apUser.getId()));
        //删除用户粉丝信息 修改apUser为apUserFan
        if(apUserFan != null){
            apUserFanMapper.delete(Wrappers.<ApUserFan>lambdaQuery().eq(ApUserFan::getUserId, followId).eq(ApUserFan::getFansId, apUser.getId()));
        }
        //删除用户关注信息
        apUserFollowMapper.delete(Wrappers.<ApUserFollow>lambdaQuery().eq(ApUserFollow::getUserId, apUser.getId()).eq(ApUserFollow::getFollowId, followId));
        return ResponseResult.okResult(AppHttpCodeEnum.SUCCESS);
    }
}
```