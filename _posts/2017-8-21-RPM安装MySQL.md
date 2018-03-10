---

layout: post
title: RPM安装MySQL
categories: DB
tags: MySQL

---

### 1) 检查MySQL及相关RPM包 ###

rpm -qa | grep -i mysql

yum -y remove mysql-libs*

### 2)下载相关包 ###

[http://mirror.neu.edu.cn/mysql/Downloads/MySQL-5.6/](http://mirror.neu.edu.cn/mysql/Downloads/MySQL-5.6/)

分为：devel、server、client（这三个是要装的）、embedded、shared、test等；

### 3）安装MySQL ###

rpm -ivh MySQL-server-5.6.15-1.el6.x86_64.rpm

rpm -ivh MySQL-devel-5.6.15-1.el6.x86_64.rpm

rpm -ivh MySQL-client-5.6.15-1.el6.x86_64.rpm


######修改配置文件
cp /usr/share/mysql/my-default.cnf /etc/my.cnf

### 4）初始化MySQL及设置密码 ###

/usr/bin/mysql_install_db

service mysql start

cat /root/.mysql_secret

mysql -uroot –pqKTaFZnl（随机密码，之后修改）

mysql> SET PASSWORD = PASSWORD('123456'); 

mysql>exit

mysql -uroot -p123456

### 5)允许远程登录 ###

mysql> use mysql;

mysql> select host,user,password from user;

mysql> update user set password=password('123456') where user='root';

mysql> update user set host='%' where user='root' and host='localhost';

mysql> flush privileges;

mysql> exit


### 6)设置开机自启动 ###

chkconfig mysql on

chkconfig --list | grep mysql

### 7)MySQL的默认安装位置 ###

/var/lib/mysql/               #数据库目录

/usr/share/mysql              #配置文件目录

/usr/bin                     #相关命令目录

/etc/init.d/mysql              #启动脚本

### 8)修改字符集和数据存储路径 ###
配置/etc/my.cnf文件,修改数据存放路径、mysql.sock路径以及默认编码utf-8. 

查看字符集
show variables like '%collation%';

show variables like '%char%';