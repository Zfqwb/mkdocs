# mybatis_plus 补充知识

## 1. 枚举

```
我们经常出现如下场景,数据库存储的性别为数字:1,2, 而我们前端页面要的数据是 '男','女',于是我们程序每次需要处理这类数据转换时每次都需要if else 判断进行出具处理后再进行返回
```

 为了更好的存储和获取此类数据 mybatis_plus 提供了枚举特性的支持

1) 建表语句

```sql
CREATE TABLE `tb_user1` (
  `id` BIGINT(20) NOT NULL AUTO_INCREMENT COMMENT '主键ID',
  `user_name` VARCHAR(20)  COMMENT '用户名',
  `password` VARCHAR(20) COMMENT '密码',
  `name` VARCHAR(30) DEFAULT NULL COMMENT '姓名',
  `age` INT(11) DEFAULT NULL COMMENT '年龄',
  `sex` INT(1) DEFAULT NULL COMMENT '性别',
  `email` VARCHAR(50) DEFAULT NULL COMMENT '邮箱',
  created DATETIME,
  updated DATETIME,
  PRIMARY KEY (`id`)
) ENGINE=INNODB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8
```

注意,枚举支持的字段不能是tinyint 类型,

2) 定义枚举

```java
package com.itheima.mp.enums;

import com.baomidou.mybatisplus.core.enums.IEnum;

public enum SexEnum implements IEnum<Integer> {

    MAN(1,"男"),
    WOMAN(2,"女"),
    UNKNOWN(3,"未知");

    private int value;
    private String desc;
    SexEnum(int value, String desc) {
        this.value = value;
        this.desc = desc;
    }
    @Override
    public Integer getValue() {
        return this.value;
    }
    @Override
    public String toString() {
        return this.desc;
    }
}
```

3) 创建实体类对象

```java
//@TableName("tb_user")
@Data
public class User1 {
    @TableId(type=IdType.AUTO)
    private Long id;
    private String userName;
    private String password;
    private String name;
    private Integer age;
    private String email;
    private SexEnum sex; //性别,这个字段使用枚举
}
```

3) mapper

```java
/**
 * 使用mp定义Mapper，需要让Mapper接口继承 BaseMapper接口。
 */
@Repository
public interface User1Mapper extends BaseMapper<User1> {
}
```

4) 定义service

```java
@Service
public class User1ServiceImpl extends ServiceImpl<User1Mapper, User1> {

}
```

5) 增加枚举包扫描配置

```yaml

mybatis-plus:
  # 指定枚举所在的包
  type-enums-package: com.itheima.mp.enums
  global-config:
    db-config:
      # 表名前缀
      table-prefix: tb_
      # id生成策略 数据库自增
      id-type: auto
  configuration:
    # 日志
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
```

6) 测试

```java
@SpringBootTest
@RunWith(SpringRunner.class)
public class User1ServiceTest {


    @Autowired
    private User1ServiceImpl userService;

    @Test
    public void testSave(){
        User1 user1 = new User1();
        user1.setName("zhangsn");
        user1.setSex(SexEnum.MAN);
        userService.save(user1);// java 赋值为枚举,插入数据库为 数字类型
    }
    
    @Test
    public void test(){
        QueryWrapper<User1> wrapper = new QueryWrapper<>();
        wrapper.eq("id","13");
        User1 user = userService.getOne(wrapper);
        System.out.println(user);// 查询后,tostring 方法得到的是 '男',未来转json 也是'男'
    }
    
}
```

## 2.  字段自动赋值

​		我们每个表都需要一个 createdDate  ,和updateDate字段,来记录数据创建时间和数据更新时间, 但是每次我们如果都这么赋值,代码重复性很多,而且一旦忘记,讲导致数据不准确问题产生

​		mybatis_plus 提供了这类的功能支持,方便我们编程

1) 实体类上增加字段 created ,和updated

```java

    
@Data
public class User1 {
    
    @TableId(type=IdType.AUTO)
    private Long id;
    private String userName;
    private String password;
    private String name;
    private Integer age;
    private String email;
    private SexEnum sex; //性别
    //注解,fill = FieldFill.INSERT 表示执行插入时 给该字段赋值,但是更新时不会重新赋值
    @TableField(fill = FieldFill.INSERT)
    private Date created;
    //注解,fill = FieldFill.INSERT_UPDATE 表示执行插入,更新时都会(重新)赋值
    @TableField(fill = FieldFill.INSERT_UPDATE)
    private Date updated;
}
    
```

2) 编写Handler 

```java
package com.itheima.mp.config;

import com.baomidou.mybatisplus.core.handlers.MetaObjectHandler;
import org.apache.ibatis.reflection.MetaObject;
import org.springframework.stereotype.Component;

import java.util.Date;

@Component
public class MyMetaObjectHandler implements MetaObjectHandler {

    @Override
    public void insertFill(MetaObject metaObject) {
        Object created = getFieldValByName("created", metaObject);
        if (null == created) {
            //字段为空，可以进行填充
            setFieldValByName("created", new Date(), metaObject);
        }

        Object updated = getFieldValByName("updated", metaObject);
        if (null == updated) {
            //字段为空，可以进行填充
            setFieldValByName("updated", new Date(), metaObject);
        }
    }

    @Override
    public void updateFill(MetaObject metaObject) {
        //更新数据时，直接更新字段
        setFieldValByName("updated", new Date(), metaObject);
    }
}

```

3) 测试,查看数据库会自动赋值

```java
  @Test
    public void testSave(){
        User1 user1 = new User1();
        user1.setName("zhangsn");
        user1.setSex(SexEnum.MAN);
        userService.save(user1);
    }
```



