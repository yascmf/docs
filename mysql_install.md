# MySQL服务器安装与配置

>    参考链接：  
http://blog.csdn.net/zqtsx/article/details/9378703  
http://www.centoscn.com/mysql/2014/0924/3833.html

>    当前服务器安装环境为：  
>        MySQL 5.5.44  安装路径 `/usr/local/mysql/`  数据路径 `/home/mysql/data/`


## 安装编译源码所需要的工具和库

```bash
yum install gcc gcc-c++ ncurses-devel perl  
```
安装 `cmake` ，从 http://www.cmake.org 下载源码并编译安装

```bash
wget http://www.cmake.org/files/v3.2/cmake-3.2.2.tar.gz 
tar -xzvf cmake-3.2.2.tar.gz 
cd cmake-3.2.2  
./bootstrap
make
make install   
```

## 设置MySQL用户和组

新增 `mysql` 用户及组。
```bash 
groupadd mysql   
useradd -r -g mysql mysql
```

## 新建MySQL所需要的目录

```bash
mkdir -p /usr/local/mysql  #这是安装目录
mkdir -p /home/mysql/data  #这是数据目录
```

## 下载MySQL源码包并解压

```bash
wget http://dev.mysql.com/downloads/mysql/mysql-5.5.44.tar.gz  
tar -zxv -f mysql-5.5.44.tar.gz  
cd mysql-5.5.44
```

## 编译安装MySQL

编译参数如下：

```bash
cmake \
-DCMAKE_INSTALL_PREFIX=/usr/local/mysql \
-DMYSQL_DATADIR=/home/mysql/data \
-DSYSCONFDIR=/etc \
-DWITH_INNOBASE_STORAGE_ENGINE=1 \
-DWITH_PARTITION_STORAGE_ENGINE=1 \
-DWITH_FEDERATED_STORAGE_ENGINE=1 \
-DWITH_BLACKHOLE_STORAGE_ENGINE=1 \
-DWITH_MYISAM_STORAGE_ENGINE=1 \
-DWITH_ARCHIVE_STORAGE_ENGINE=1 \
-DWITH_READLINE=1 \
-DENABLED_LOCAL_INFILE=1 \
-DENABLE_DTRACE=0 \
-DDEFAULT_CHARSET=utf8 \
-DDEFAULT_COLLATION=utf8_general_ci \
-DWITH_EMBEDDED_SERVER=1 \
```

部分编译参数注释说明：

``` 
cmake \
-DCMAKE_INSTALL_PREFIX=/usr/local/mysql \  #安装路径
-DMYSQL_DATADIR=/home/mysql/data \  #数据文件存放位置
-DSYSCONFDIR=/etc \  #my.cnf路径
-DWITH_MYISAM_STORAGE_ENGINE=1 \  #支持MyIASM引擎
-DWITH_INNOBASE_STORAGE_ENGINE=1 \  #支持InnoDB引擎
-DWITH_MEMORY_STORAGE_ENGINE=1 \  #支持Memory引擎
-DWITH_READLINE=1 \  #快捷键功能(没用过)
-DMYSQL_UNIX_ADDR=/tmp/mysqld.sock \   #连接数据库socket路径
-DMYSQL_TCP_PORT=3306 \  #端口
-DENABLED_LOCAL_INFILE=1 \  #允许从本地导入数据
-DWITH_PARTITION_STORAGE_ENGINE=1 \  #安装支持数据库分区
-DEXTRA_CHARSETS=all \  #安装所有的字符集
-DDEFAULT_CHARSET=utf8 \  #默认字符
-DDEFAULT_COLLATION=utf8_general_ci  #默认排序
```
>    注：重新运行配置，需要删除CMakeCache.txt文件
```bash
rm CMakeCache.txt  
```

随后执行编译并安装：
```bash
make
make install
```

## 修改MySQL目录所有者和组

```bash
cd /usr/local/mysql   
chown -R mysql:mysql .
cd /home/mysql/data
chown -R mysql:mysql .
```

## 初始化MySQL数据库

```bash
cd /usr/local/mysql   
scripts/mysql_install_db --user=mysql --datadir=/home/mysql/data
```

## 复制MySQL服务启动配置文件

```bash
cp /usr/local/mysql/support-files/my-medium.cnf /etc/my.cnf
```
>    注：如果 `/etc/my.cnf` 文件存在，则覆盖。

## 复制MySQL服务启动脚本及加入PATH路径

```bash
cp support-files/mysql.server /etc/init.d/mysqld
vi /etc/profile

PATH=/usr/local/mysql/bin:/usr/local/mysql/lib:$PATH
export PATH

:wq
source /etc/profile 
```

## 启动MySQL服务并加入开机自启动

```
service mysqld start 
chkconfig --level 35 mysqld on
```

## 检查MySQL服务是否启动

```bash
netstat -tulnp | grep 3306   
mysql -u root -p
```

## 修改MySQL用户root的密码

```bash
mysqladmin -u root password '123456'
```

配置MySQL root可远程链接：
```bash
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '123456' WITH GRANT OPTION;
```
其中 `123456` 为密码。

>    注：也可运行安全设置脚本，修改MySQL用户root的密码，同时可禁止root远程连接，移除test数据库和匿名用户。
```bash
/usr/local/mysql/bin/mysql_secure_installation  
```

## 配置防火墙

防火墙的3306端口默认没有开启，若要远程访问，需要开启这个端口

打开 `/etc/sysconfig/iptables`

在 `-A INPUT –m state --state NEW –m tcp –p tcp --dport 22 –j ACCEPT` 下添加：

```bash
-A INPUT -m state --state NEW -m tcp -p tcp --dport 3306 -j ACCEPT
```
然后保存，并关闭该文件，在终端内运行下面的命令，刷新防火墙配置：
```bash
service iptables restart
```


## 可能会出现的错误

问题：  
`Starting MySQL..The server quit without updating PID file ([FAILED]/mysql/Server03.mylinux.com.pid).`

解决：   
修改 `/etc/my.cnf` 中 `datadir` ，指向正确的 `MySQL` 数据库文件目录。

问题：  
`ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/tmp/mysql.sock' (2) ` 
 
解决：   
新建一个链接或在 `MySQL` 中加入-S参数，直接指出 `mysql.sock` 位置。  
``` 
ln -s /usr/local/mysql/data/mysql.sock /tmp/mysql.sock   
/usr/local/mysql/bin/mysql -u root -S /usr/local/mysql/data/mysql.sock
```

问题：  
5.7版本的MySQL密码过期问题

解决：  
① 用mysql命令行登录mysql的root用户：

```bash
mysql -u root -p
```

② 重新修改 `root` 密码：

```bash
SET PASSWORD FOR 'root'@'localhost' = PASSWORD('123456');
```
 
③ `MySQL5.7` 增加了两个字段 `password_last_changed`、`password_lifetime`来完善安全策略。上面的方法仅仅治标不治本。可以设置参数 `default_password_lifetime`来延长使用期限或者使用以下命令：

```bash
ALTER USER 'root'@localhost' PASSWORD EXPIRE INTERVAL 90 DAYS;  #90天有效期
ALTER USER 'root'@'localhost' PASSWORD EXPIRE NEVER;  #不验证有效期
ALTER USER 'root'@'localhost' PASSWORD EXPIRE DEFAULT;  #默认
```

>    备注：本数据库虚拟机安装完毕之后，数据库默认root默认密码：qc123456 。

