使用老师提供的虚拟机，本日课程中内容都已经配置完毕，可以直接使用（注意是leadnews14-jenkinsall）。



![Snipaste_2020-10-15_10-32-15](D:/教学/课程资料/2020/黑马头条/黑马头条笔记/asserts/Snipaste_2020-10-15_10-32-15.png)

设置模式为nat模式，点击编辑-虚拟网络编辑器

![Snipaste_2020-10-15_10-33-09](D:/教学/课程资料/2020/黑马头条/黑马头条笔记/asserts/Snipaste_2020-10-15_10-33-09.png)



![Snipaste_2020-10-15_10-33-36](D:/教学/课程资料/2020/黑马头条/黑马头条笔记/asserts/Snipaste_2020-10-15_10-33-36.png)





开机之后按顺序执行以下命令：

```shell
docker start zookeeper
#启动成功之后
docker start kafka nacos mysql
#启动服务
docker start heima-leadnews-user heima-leadnews-admin heima-leadnews-admin-gateway heima-leadnews-wemedia 
```

启动nginx:

```shell
/usr/local/nginx/sbin/nginx
```

访问http://192.168.226.135/



查询docker日志：

```
docker logs 容器ID或者容器名
```
