# MongoDB 事务

MongoDB 4.0 引入的事务功能，支持多文档ACID特性，

1)  想要支持事务,mongoDB 必须使用集群环境

2) 在容器中注册一个事务管理器

```
@Configuration
public class MongoTransactionConfig {
    @Bean
    public MongoTransactionManager transactionManager(MongoDbFactory dbFactory) {
        return new MongoTransactionManager(dbFactory);
    }
}
```

3) 在需要事务控制的方法上使用 @Transactional 注解即可