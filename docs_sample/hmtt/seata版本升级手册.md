由于seata服务端使用了1.3.0的版本，但是客户端使用的是0.7.1（虽然直接导入了0.9但是最终还是使用的0.7.1）。会出现内存泄漏等问题，所以需要将版本升级为1.3.0，升级方式如下:

1.在父工程中添加版本锁定

```xml
            <dependency>
                <groupId>io.seata</groupId>
                <artifactId>seata-all</artifactId>
                <version>1.3.0</version>
            </dependency>
```

虽然在seata工程中使用依赖引入的方式指定了版本号，但是由于引入了spring-cloud-starter-alibaba-seata，在2.1.0release版本中已经锁定了seata为0.7.1，所以需要手动锁定。



2.修改所有相关项目的file.conf，修改如下内容

```properties
#原来是vgroup_mapping，新版本中修改为vgroupMapping
vgroupMapping.leadnews-admin_tx_group = "default"
```

其中leadnews-admin要修改成对应的服务名。