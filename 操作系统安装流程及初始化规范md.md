# 操作系统安装流程及初始化规范 #
>  v1.0 
##操作系统安装 ##

1.	服务器采购
2.	服务器验收并设置raid
3.	服务商提供验收单，运维验收负责人签字
4.	服务器上架
5.	资产录入。
6.	开始自动化安装。
 - （1）	将新服务器划入装机vlan
 - （2）	根据资产清单上的mac地址，自定义安装。
  > a.	机房 b.机房区域 c机柜 d.服务器位置 e 服务器网线接入端口 f 该端口mac地址
Profile 操作系统 分区 预分配的ip地址 主机名 子网 网关 dns 角色
 - （3）	自动化装机平台安装 IP	10.0.1.63
 <pre>
主机名:Linux-node2.oldboyedu.com
掩码:255.255.255.0
网关:10.0.1.2
Dns:10.0.1.2
Mac:00:0C:29:78:1A:47
</pre>
 <pre>
[root@cobbler ks_mirror]# cobbler system add --name=linux-node2.oldboyedu.com  --mac=00:0C:29:78:1A:47 --profile=Gentos-7-x87-64-x86_64  --ip-address=10.0.1.63 –subnet=255.255.255.0 --gateway=10.0.1.2 --interface=eth0  --static=1 --hostname=Linux-node2  --name-servers=10.0.1.2  --kickstart=/var/lib/cobbler/kickstarts/CentOS-7.2-cobbler.cfg
</pre>
* 内核参数优化
* yum仓库
* 主机名解析
##操作系统安装规范
  1. 当前我公司使用操作系统为centos 6和centos 7，均使用x86_64位系统，需使用cobbler进行自动化安装，禁止自定义设置。
  2. 版本选择，数据库统一使用cobbler上centos-7-DB这个专用的profile，其他web应用统一使用cobbler上centos-7-web。
##系统初始化规范 ##
###初始化操作###
* 设置DNS 192.168.56.111 192.168.56.112
* 安装zabbix agent: zabbix server 192.168.56.11
* 安装saltstack minion:saltstack master:192.168.56.11
* histroy记录时间
  <pre>
	export HISTIMEFORMAT="%F %T `whoami`"
 </pre>
###目录规范###
* 脚本放置目录： /opt/shell
* 脚本日志目录： /opt/shell/log
* 脚本锁文件目录： /opt/shell/lock
###服务安装规范###
1. 源码安装路径 /usr/local/appname.version
2. 创建软链接 ln -s /usr/local/appname.version /usr/local/appname
###主机名命名规范 
**机房名称-项目-角色-集群-节点.域名**

例子：
<pre>
	idc01-xxshop-nginx-bj-node1.shop.com
</pre>
###服务启动用户规范
所有服务，统一使用www用户，uid为666，除负载均衡需要监听80端口使用root启动外，所有服务必须使用www用户启动，使用大约1024端口。
