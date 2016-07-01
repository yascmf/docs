# 内网服务器说明

## WEB服务器环境

>    IP：10.101.1.9  
>    OS：CENTOS 6.5  
>    PHP 5.5.25  安装路径 `/usr/local/php/`  
>    Tengine 1.5.2  安装路径  `/usr/local/nginx`


管理脚本：

```
service nginx start|stop|restart|reload|status|help
service php-fpm start|stop|force-quit|restart|reload|status
```

网站目录：

```
/usr/local/nginx/html/
/home/wwwroot/lc.yas.so/
```

nginx（含虚拟主机vhost）配置文件：

```
/usr/local/nginx/conf/nginx.conf
/usr/local/nginx/conf/vhost/lc.yas.so.conf
```

php配置文件：

```
/usr/local/php/etc/php.ini
```

SSH帐号：

```
10.101.1.9:22
root
root123
```

内部测试网站：

```
lc.yas.so  #博客
qc.yas.so  #文档
```

## Database服务器环境

>    IP：10.101.1.10  
>    OS：CENTOS 6.5  
>    MySQL 5.5.44  安装路径 `/usr/local/mysql/`  数据路径 `/home/mysql/data/`

管理脚本：

```
service mysqld start|stop|restart|reload|force-reload|status
```

配置文件：

```
/ect/my.cnf
```

SSH帐号：

```
ip:10.101.1.10:22
root
root123
```

数据库帐号：

```
ip:10.101.1.10:3306
root
qc123456
```