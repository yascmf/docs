# WEB服务器编译安装与配置

>    如遇到YUM无法下载某些包，可参考网络文章更改YUM源地址或者直接下载其源码包自行编译。
>    
>    当前服务器安装环境为：  
>        CENTOS 6.5  
>        PHP 5.5.25  安装路径 `/usr/local/php/`  
>        Tengine 1.5.2  安装路径  `/usr/local/nginx`

## VMWare安装CentOS系统

这里不做过多说明，请参考查阅此文 <http://www.jb51.net/os/128751.html> 安装。

## 设置IP地址、网关、DNS

>    `CentOS 6.5` 默认安装好之后是没有自动开启网络连接的！

输入账号root，再输入安装过程中设置的密码，登录到系统，参考下面配置：

>    注意：IP地址与DNS请结合实际修改。

```bash
vi  /etc/sysconfig/network-scripts/ifcfg-eth0   #编辑配置文件,添加修改以下内容
DEVICE=eth1
TYPE=Ethernet
BOOTPROTO=static   #启用静态IP地址
ONBOOT=yes  #开启自动启用网络连接
IPADDR=192.168.21.129  #设置IP地址
NETMASK=255.255.255.0  #设置子网掩码
GATEWAY=192.168.21.1   #设置网关
DNS1=8.8.8.8  #设置主DNS
DNS2=8.8.4.4  #设置备DNS
IPV6INIT=no  #禁止IPV6
:wq!  #保存退出

service ip6tables stop   #停止IPV6服务
chkconfig ip6tables off  #禁止IPV6开机启动
service yum-updatesd stop   #关闭系统自动更新
chkconfig yum-updatesd off  #禁止开启启动
service network restart  #重启网络连接
ifconfig  #查看IP地址
```

如出现 `Device eth0 does not seem to be present` 问题请参考：<http://www.cnblogs.com/fbwfbi/archive/2013/04/29/3050907.html> ，或者将DEVICE设备名由 `eth0` 改为 `eth1` 。

## 启动SSH

>    最小化安装的CentOS，一般不会装上SSH，你可以参考下面安装SSH。

```bash
rpm -qa |grep ssh  #检查是否装了SSH相关包
yum install openssh-server  #没有的话，则安装
chkconfig --list sshd  #检查SSHD是否在本运行级别下设置为开机启动
chkconfig --level 2345 sshd on  #如果没设置启动就设置下.
service sshd restart  #重新启动
netstat -antp |grep sshd  #确认下是否启动了22端口
iptables -nL  #看看是否放行了22口，如果没放行就设置放行
```

## 配置防火墙

配置防火墙，开启22端口、80端口、3306端口：

```bash
vi /etc/sysconfig/iptables
-A INPUT -m state --state NEW -m tcp -p tcp --dport 22 -j ACCEPT #允许22端口（SSH服务默认端口）通过防火墙
-A INPUT -m state --state NEW -m tcp -p tcp --dport 80 -j ACCEPT #允许80端口（Web服务默认端口）通过防火墙
-A INPUT -m state --state NEW -m tcp -p tcp --dport 3306 -j ACCEPT #允许3306端口（MySQL数据库服务默认端口）通过防火墙
:wq
service iptables restart 
```
## 关闭SELINUX

```bash
vi /etc/selinux/config
#SELINUX=enforcing #注释掉
#SELINUXTYPE=targeted #注释掉
SELINUX=disabled #增加
:wq 保存，关闭
shutdown -r now #重启系统
```

## 系统约定

软件源代码包存放位置：`/usr/local/src`  
源码包编译安装位置：`/usr/local/{soft_name}`

## 更新系统

新安装的系统建议更新：

```bash
yum upgrade
```

## 添加www用户及其组

>    这里我们添加www用户及其组，作为 `Nginx` 与 `PHP` 默认所属。

```bash
groupadd www
useradd -g www -s /sbin/nologin -M www
```

## 安装 `Tengine` 或 `Nginx`

>    如遇到无法使用 `wget` 、 `gcc` 或 `make` 编译器等， 请先执行：

```bash
yum -y install wget
yum -y install gcc gcc-c++ autoconf automake libtool make
```

### 安装 `Tengine` 步骤：

**① 下载Tengine稳定版源码**

```bash
wget http://tengine.taobao.org/download/tengine-1.5.2.tar.gz
tar xf tengine-1.5.2.tar.gz
```
**②安装依赖**

```bash
yum -y install zlib zlib-devel openssl openssl-devel pcre-devel jemalloc-devel
```
最后一项 `jemalloc-devel` 可能因为`yum` 源问题无法安装，后面给出源码安装方法。

**③编译安装**

```bash
cd tengine-1.5.2 
./configure --prefix=/usr/local/nginx --user=www --group=www --with-http_stub_status_module --without-http-cache --with-http_ssl_module --with-http_gzip_static_module --with-http_concat_module
make
sudo make install
ln -s /usr/local/nginx/sbin/nginx /usr/sbin/nginx  #软连接

nginx -v #测试是否安装上，有回显版本号说明安装上
```
**④添加 `Tengine` 特有选项**

>    `Tengine` 支持一些特有[选项](http://tengine.taobao.org/document_cn/install_cn.html)，如：
* --with-jemalloc  
让 `Tengine` 链接 `jemalloc` 库，运行时用 `jemalloc`来分配和释放内存。
* --with-jemalloc={path}  
设置 `jemalloc` 库的源代码路径，`Tengine` 可以静态编译和链接该库。

下载 `jemalloc` 源码：

```bash
wget https://github.com/jemalloc/jemalloc/releases/download/3.6.0/jemalloc-3.6.0.tar.bz2
tar -xjf jemalloc-3.6.0.tar.bz2
```
新加上 `jemalloc` 之后的编译参数如下：

这里是完整**编译参数**：

```bash
./configure \
--prefix=/usr/local/nginx \
--user=www \
--group=www \
--with-http_ssl_module \
--with-http_flv_module \
--with-http_gzip_static_module \
--with-http_stub_status_module \
--without-http-cache \
--with-http_ssl_module \
--with-http_concat_module \
--with-jemalloc=/usr/local/src/jemalloc-3.6.0
```
### 安装 `Nginx` 步骤

>    安装`Nginx` 与上面安装 `Tengine` 类似，这里我们就不做过多说明了。

### 服务脚本

关于 `Nginx` 的服务脚本可参考 [Nginx Init Scripts](http://wiki.nginx.org/InitScripts)。

```bash
chmod a+x /etc/init.d/nginx
service nginx start|stop|restart|reload|status|help
```
下面语句是将nginx服务开机启动的命令：

```
service nginx start 
chkconfig --level 35 nginx on
```

## 安装 `PHP`

**①获取最新源码**

```bash
wget http://cn.php.net/distributions/php-5.5.25.tar.gz
tar xf php-5.5.25.tar.gz
```

**②安装前准备**

安装前可能你需要以下执行 `yum install` 命令安装某些依赖包，请依据编译条件与参数自行增删。

```bash
yum -y install gcc automake autoconf libtool make
yum -y install gcc gcc-c++ glibc
yum -y install libmcrypt-devel mhash-devel libxslt-devel \
libjpeg libjpeg-devel libpng libpng-devel freetype freetype-devel libxml2 libxml2-devel \
zlib zlib-devel glibc glibc-devel glib2 glib2-devel bzip2 bzip2-devel \
ncurses ncurses-devel curl curl-devel openssl openssl-devel
```

>    特别说明：参考 `Laravel` 文档，我们可以知道，该框架安装的环境需求，新发布的 `Laravel 5.1 LTS` 环境需求更高：
>
* PHP 版本 >= 5.4
* Mcrypt PHP 扩展
* OpenSSL PHP 扩展
* Mbstring PHP 扩展
* Tokenizer PHP 扩展
* 在 PHP 5.5 之后， 有些操作系统需要手动安装 PHP JSON 扩展包。如果你是使用 Ubuntu，可以通过 `apt-get install php5-json` 来进行安装，CentOS可以通过`yum install php-pecl-json`来安装。

>    上面所说的环境需求只是框架自身的最低需求，某些第三方包扩展与组件可能需要额外的环境需求。

**③手动安装某些组件包**

上面某些组件包在YUM下无法安装，这里简单说明下，源码编译安装方法：

* 安装 `libmcrypt` 组件  

>    下载地址：http://mcrypt.hellug.gr/lib/  
http://sourceforge.net/projects/mcrypt/files/Libmcrypt/  

```bash
tar -zxvf libmcrypt-2.5.8.tar.gz
cd libmcrypt-2.5.8
./configure
make
make install  
```

* 安装 `mhash` 组件

>    下载地址：http://sourceforge.net/projects/mhash/files/mhash/

```bash
tar -zxvf mhash-0.9.9.9.tar.gz
cd mhash-0.9.9.9
./configure
make
make install
```

* 安装 `mcrypt` 组件 

>    下载地址：http://sourceforge.net/projects/mcrypt/files/MCrypt/

```bash
tar -zxvf mcrypt-2.6.8.tar.gz
cd mcrypt-2.6.8
./configure
make
make install
```

**④安装PHP**

参考后面编译参数安装PHP，编译没出错，就执行下面安装：

```bash
make
make install
```

**⑤配置PHP**

```bash
cp php.ini-development /usr/local/php/etc/php.ini
cp /usr/local/php/etc/php-fpm.conf.default /usr/local/php/etc/php-fpm.conf
cp sapi/fpm/init.d.php-fpm /etc/init.d/php-fpm
chmod +x /etc/init.d/php-fpm
service php-fpm start
```
加入开机启动：

```bash
chkconfig --level 35 php-fpm on
```

添加 PHP 命令到环境变量
修改 `/etc/profile` 文件使其永久性生效，并对所有系统用户生效，在文件末尾加上如下两行代码

```bash
PATH=$PATH:/usr/local/php/bin
export PATH
```

编辑 `~/.bash_profile` ，将 `PATH=$PATH:$HOME/bin` 改为 `PATH=$PATH:$HOME/bin:/usr/local/php/bin` 。

使 PHP 环境变量生效：
```bash
source /etc/profile
php -v
```
再执行 `php -v` 应该能看到 `php` 版本号信息。


**⑥软连接**

如果执行 `service php-fpm start` 出现某个动态链接库（如 `libmcrypt` ）出错，很有可能是没有做软连接，而且这种情况很可能出现在64位系统上，它软连接对应的目录应该是`usr/lib64`，而非 `usr/lib` ：

注意，如果存在上面问题，就做下面软链接操作，没有，则不需要做任何操作。

32位系统软连接：

```bash
ln -s /usr/local/lib/libmcrypt.la /usr/lib/libmcrypt.la
ln -s /usr/local/lib/libmcrypt.so /usr/lib/libmcrypt.so
ln -s /usr/local/lib/libmcrypt.so.4 /usr/lib/libmcrypt.so.4
ln -s /usr/local/lib/libmcrypt.so.4.4.8 /usr/lib/libmcrypt.so.4.4.8
ln -s /usr/local/lib/libmhash.a /usr/lib/libmhash.a
ln -s /usr/local/lib/libmhash.la /usr/lib/libmhash.la
ln -s /usr/local/lib/libmhash.so /usr/lib/libmhash.so
ln -s /usr/local/lib/libmhash.so.2 /usr/lib/libmhash.so.2
ln -s /usr/local/lib/libmhash.so.2.0.1 /usr/lib/libmhash.so.2.0.1
ln -s /usr/local/bin/libmcrypt-config /usr/bin/libmcrypt-config
```

64位系统软连接：
```bash
ln -s /usr/local/lib/libmcrypt.la /usr/lib64/libmcrypt.la
ln -s /usr/local/lib/libmcrypt.so /usr/lib64/libmcrypt.so
ln -s /usr/local/lib/libmcrypt.so.4 /usr/lib64/libmcrypt.so.4
ln -s /usr/local/lib/libmcrypt.so.4.4.8 /usr/lib64/libmcrypt.so.4.4.8
ln -s /usr/local/lib/libmhash.a /usr/lib64/libmhash.a
ln -s /usr/local/lib/libmhash.la /usr/lib64/libmhash.la
ln -s /usr/local/lib/libmhash.so /usr/lib64/libmhash.so
ln -s /usr/local/lib/libmhash.so.2 /usr/lib64/libmhash.so.2
ln -s /usr/local/lib/libmhash.so.2.0.1 /usr/lib64/libmhash.so.2.0.1
```

**⑦配置Nginx**

这里就不做过多说明了，文档包里面有示例文件。


### 编译参数

```bash
./configure \
--prefix=/usr/local/php \
--with-config-file-path=/usr/local/php/etc \
--enable-fpm \
--with-fpm-user=www \
--with-fpm-group=www \
--with-libdir=lib64 \
--with-mysql=mysqlnd \
--with-mysqli=mysqlnd \
--with-pdo-mysql=mysqlnd \
--with-iconv-dir \
--with-freetype-dir \
--with-zlib-dir=/usr/lib \
--with-png-dir=/usr/lib \
--with-jpeg-dir=/usr/lib \
--with-curl \
--with-libxml-dir=/usr \
--with-mcrypt \
--with-mhash \
--with-gd \
--with-zlib \
--enable-gd-native-ttf \
--with-openssl \
--with-gd \
--with-openssl \
--with-mhash \
--enable-bcmath \
--with-gettext \
--enable-json \
--enable-zip \
--enable-sockets \
--enable-inline-optimization \
--disable-ipv6 \
--disable-debug \
--disable-rpath \
--enable-mbstring \
--enable-exif \
--enable-mbregex \
--enable-sysvsem \
--enable-pdo \
--enable-session \
--enable-tokenizer \
--enable-ctype \
--enable-hash  \
```

### PHP部分编译参数说明

```
--prefix=/usr/local/php \   #指定安装路径
--with-config-file-path=/usr/local/php/etc \  #指定配置路径
--enable-fpm \  #开启fpm
--with-fpm-user=www \  #用户名
--with-fpm-group=www \  #用户组
--disable-all \  #禁用其他组件，有点狠，但可大幅加快编译速度
--with-libdir=lib64 \  #64位系统指定/usr/lib64为默认库路径
--with-mysql=mysqlnd \  #mysql相关
--with-mysqli=mysqlnd \  #mysql相关
--with-pdo-mysql=mysqlnd \  #mysql pdo相关
--with-iconv-dir \  #iconv支持
--with-freetype-dir \  #打开对freetype字体库的支持
--with-jpeg-dir \  #打开对jpeg图片的支持 
--with-png-dir \  #打开对png图片的支持 
--with-zlib-dir \  #打开zlib库的支持，页面压缩相关
--with-curl \  #cURL支持
--with-libxml-dir \  #打开libxml2库的支持 
--with-mcrypt \  #加密相关
--with-mhash \  #mhash库支持
--enable-bcmath \ #任意精度数学扩展
--with-gd \  #gd图像处理相关
--enable-gd-native-ttf \  #支持TrueType字符串函数库 
--with-openssl \  #SSL相关
--with-xmlrpc  \  #xmlrpc协议支持，某些博客程序如Workpress需要
--with-gettext \ #打开gnu的gettext支持，编码库用到 
--enable-zip \  #zip压缩支持
--enable-sockets \  #sockets协议支持，某些在线实时通信程序需要依赖
--enable-ftp \  #ftp支持
--enable-opcache \  #opcache支持，能减少php运行时耗
--enable-soap \  #soap协议支持
--enable-inline-optimization \  #优化线程
--disable-ipv6 \  #禁用ipv6，ipv4站点可以禁用掉
--disable-debug \  #关闭调试模式
--disable-rpath \  #关闭额外的运行库文件 
--enable-mbstring \  #宽字符支持
--enable-xml \  #xml支持
--enable-exif \  #图片exif支持，图像处理要要用到
--enable-mbregex \  #宽字节正则相关
--enable-pdo \  #启用pdo支持
```



