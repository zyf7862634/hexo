---
title: ibmmq mq-golang使用总结
date: 2019-10-28 19:11:43
tags:
- 消息队列
categories: 
- 总结
---

​	ibmmq  mq-golang使用总结

/opt/mqm/bin/dspmqver   //看版本号  

相关错误信息位置  ~/IBM/MQ/data/errors/AMQxxx
mq server端 ，需要要mqm 用户执行
调用mq-golang 对于mq server来说是 app 用户， mqserver 配置 app 属于 mqmclient 组

集成mq-golang 动态库需要：
lib64/
libmqm_r.so  libmqe_r.so

samp/
ccsid.tbl  sccsid_part2.tbl

inc/
cmqc.h
cmqcfc.h
cmqstrc.h
cmqxc.h


//指定动态库为相对路径 运行时路径为 相对路径 或 /opt/mdm/lib64/
\#cgo !windows,!darwin LDFLAGS: -L${SRCDIR}/../../../../../mqm/lib64 -lmqm_r -Wl,-rpath,${SRCDIR}/../../../../../mqm/lib64 -Wl,-rpath,/opt/mqm/lib64


//手动添加app用户
docker exec -it -u root mqm bash
useradd app
usermdo -aG mqm app
usermdo -aG mqmclient app


查看权限配置
/opt/mqm/bin/dmpmqaut

\#链接
/opt/mqm/samp/bin/amqsconn QM1 "DEV.APP.SVRCONN" "127.0.0.1(1414)"
 
\#发布
/opt/mqm/samp/bin/amqspub 'topicstr'

订阅
/opt/mqm/samp/bin/amqssub 'topicstr'


具体mqm 队列和topic 接受权限可以用命令：

 
/*************docker 模式 测试*************************************/
//9.1.0 版本官方没有，需要自己本地编译容器
切换到mq-contianer  9.1.0分支

make build-devserver

生成9.1.0.0镜像

可用通过启动镜像，看到里面执行的mqm命令

docker run  -it --env LICENSE=accept  --env MQ_QMGR_NAME=QM1 --publish 1414:1414  --publish 7054:9443 --name mqm1.0 --detach  peersafes/ibmmq-9.1.0.0 

docker run  -it --env LICENSE=accept  --env MQ_QMGR_NAME=QM1  --publish 1415:1414  --publish 5984:9443 --name mqm1.1 --detach  ibmcom/mq:9.1.1.0 

docker run  -it --env LICENSE=accept  --env MQ_QMGR_NAME=QM1  --publish 1416:1414  --publish 8080:9443 --name mqm1.3 --detach  ibmcom/mq:9.1.3.0 


//访问mqserver web  ip:9443
https://127.0.0.1:8080/ibmmq/console/login.html

username: admin
password: passw0rd

如果是topic 模式， 要选中 字符串 然后点击预定消息才可以收到