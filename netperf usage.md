## install

install

```
sudo apt install netperf
```

for centos：

```shell
wget http://repo.iotti.biz/CentOS/7/x86_64/netperf-2.7.0-1.el7.lux.x86_64.rpm
sudo yum install netperf-2.7.0-1.el7.lux.x86_64.rpm

```



## start on server

```
netserver
```

may occur "unable", because service is already running. 

```
xilinx@TRX40:~/pktgen/pktgen-20.03.0$ netserver
Unable to start netserver with  'IN(6)ADDR_ANY' port '12865' and family AF_UNSPEC

```

```
netstat -ant|grep 12865
tcp6       0      0 :::12865                :::*                    LISTEN
```



## start on client

```
 netperf  -H 172.16.75.54 -l 10
```





#### 命令参数介绍

命令行参数包括如下选项：

```
-H host ：指定远端运行netserver的server IP地址。
-l testlen：指定测试的时间长度（秒）
-t testname：指定进行的测试类型，包括TCP_STREAM，UDP_STREAM，TCP_RR，TCP_CRR，UDP_RR
-s size	设置本地系统的socket发送与接收缓冲大小
-S size	设置远端系统的socket发送与接收缓冲大小
-m size	设置本地系统发送测试分组的大小
-M size	设置远端系统接收测试分组的大小
-D 对本地与远端系统的socket设置TCP_NODELAY选项
```



