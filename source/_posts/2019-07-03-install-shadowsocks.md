---
title: install shadowsocks
date: 2019-07-03 19:54:04
tags:
- Shadowsocks
- Linux
categories:
- 工具
---

> 每次安装ss的时候都要网上搜索教程，所以自己记录一下，以后要用的时候，不用再费力查找。还有自己经常忘记启动命令，这里也做个备忘录。

<!--more-->

## 安装方法
首先安装python pip(v2.6 or v2.7),对于RedHat系列和Debian系列，安装方法如下

针对**Debain**系列
```
apt-get install python-pip
```
针对**RedHat**系列
```
yum install python-setuptools && easy_install pip
```

运行`pip`命令安装shadowsocks
```
pip install shadowsocks
```
运行`ssserver`命令检查是否安装成功

## 运行服务
开启ss服务有两种方法，一种是直接在命令行下输入参数启动：
```
ssserver -p 8388 -k password -m aes-256-cfb
```
另一种是通过事先写好的配置文件启动，省去写端口、密码、加密方式的麻烦。

新建配置文件：
```
vi /etc/shadowsocks.json
```
文件内容：
```json
{
	"server":"my_server_ip",
	"server_port":443,
	"local_port":1080,
	"password":"mypassword",
	"timeout":300,
	"method":"aes-256-cfb",
}
```
各个配置项的含义如下：

|配置项 |含义|
|:-:|:-:|
|server|服务器ip地址(0.0.0.0)|
|server_port|服务监听端口|
|local_port|本地转发端口|
|password|密码|
|timeout|连接超时时间|
|method|加密方式|

用如下命令启动和停止：
```
ssserver -c /etc/shadowsocks.json
```

## 后台运行和停止
```
ssserver -c /etc/shadowsocks.json -d start
ssserver -c /etc/shadowsocks.json -d stop
```

## 安装BBR加速
> BBR 是 Google 提出的一种新型拥塞控制算法，可以使 Linux 服务器显著地提高吞吐量和减少 TCP 连接的延迟。

1.首先确认linux内核版本在4.9及以上，否则需要升级内核
```
uname -r
```
2.开启BBR:
```
echo "net.core.default_qdisc=fq" >> /etc/sysctl.conf
echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.conf
sysctl -p
```
3.验证

*查看可用的拥塞控制算法*
```
sysctl net.ipv4.tcp_available_congestion_control
```
*得到*
> net.ipv4.tcp_available_congestion_control = reno cubic bbr

*检查BBR是否正常运行*
```
lsmod | grep tcp_bbr
```
> tcp_bbr 20480 1

4.速度测试

*安装speedtest-cli*
```
pip install speedtest-cli
```

*运行*
```
speedtest-cli
```
![speed测试结果](/images/speed.png)

## Refer

[shadowsocks官网](https://shadowsocks.org/en/index.html): https://shadowsocks.org/en/index.html

[BBR项目地址](https://github.com/google/bbr): https://github.com/google/bbr

[Speed的github地址](https://github.com/sivel/speedtest-cli): https://github.com/sivel/speedtest-cli