# Laravel 项目代码规范与约束

>   本文假定您有一定的基础储备（不仅限于PHP），如果您缺少某些基础，请自行查阅下面链接进行补漏。

关键词：

`PHP` `Laravel` `MVC` `英文语义化` `Composer` `命名空间` `自动加载` `Session` `Cookies` `反射` `类与对象` `邮件SMTP` `正则表达式` `Git` `CVS` `设计模式` `反转控制IoC` `Facade` `PSR规范` `图像处理GD` `模板引擎` `缓存` `数据库PDO` `Bootstrap` `Markdown` ...

参考链接：

* [PHP NET](http://php.net) 
* [自动加载](http://php.net/manual/zh/language.oop5.autoload.php)
* [PHP-FIG PSR中文版](https://github.com/PizzaLiu/PHP-FIG)
* [PHP-FIG官网](http://www.php-fig.org/)
* [fig-standards英文版](https://github.com/php-fig/fig-standards/tree/master/accepted)
* [使用 PHP-CS-Fixer 自动规范化你的 PHP 代码](https://phphub.org/topics/547)
* [PHP之道](http://laravel-china.github.io/php-the-right-way/)
* [Awesome PHP](https://github.com/ziadoz/awesome-php)
* [PHP 资源大全](http://www.cnblogs.com/taletao/p/4212916.html)
* [设计模式详解及PHP实现](https://phphub.org/topics/560)
* [Laravel 学习笔记 —— 神奇的服务容器](https://phphub.org/topics/789)
* [php中的数种依赖注入](http://www.4wei.cn/archives/1002316)
* [Laravel官网](http://laravel.com/)
* [Laravel中文文档](http://laravel-china.org/docs/5.0)
* [Composer](https://getcomposer.org/)
* [Composer中文网](http://www.phpcomposer.com/)
* [Composer私有包](http://docs.phpcomposer.com/05-repositories.html#VCS)
* [SegmentFault](http://segmentfault.com/)
* [Git](http://git-scm.com/)
* [TortoiseGit](http://download.tortoisegit.org/tgit/1.8.14.0/)
* [Markdown 语法说明](http://wowubuntu.com/markdown/)
* [Markdown 编辑器语法指南](http://segmentfault.com/markdown)
* [Github](https://github.com)
* [Coding](http://coding.net)
* [Laravel 架构中的 Container/ServiceProvider/Facade](https://phphub.org/topics/769)
* ...

## 基本规范约束

关于PHP代码规范，已经由专业组织制定出了，这就是 **PHP-FIG** 做出 `PSR` 规范，目前接受可用的规范到 **4** 了。可参阅以下链接阅读：

* [PHP-FIG PSR中文版](https://github.com/PizzaLiu/PHP-FIG)
* [PHP-FIG官网](http://www.php-fig.org/)
* [fig-standards英文版](https://github.com/php-fig/fig-standards/tree/master/accepted)
* [使用 PHP-CS-Fixer 自动规范化你的 PHP 代码](https://phphub.org/topics/547)

具体到项目中，可能会有其它代码性约束，项目负责人可做出明确的规范。

### 代码风格

遵循 [`PSR-2` 代码风格指南](https://github.com/hfcorriez/fig-standards/blob/zh_CN/%E6%8E%A5%E5%8F%97/PSR-2-coding-style-guide.md)，核心要点是：

* 使用 `4 Space空格符` 代替 `1 Tab制表符` ；
* 命名空间 `namespace` 申明在下一行；
* use 与 namespace 申明隔开一行；
* 更多...

### 自动加载

Laravel 使用 Composer 进行源码管理，自动加载推荐遵循 PSR-4 规范，可以到此查阅相关文档：

* [PSR-4中译文](https://github.com/hfcorriez/fig-standards/blob/zh_CN/%E6%8E%A5%E5%8F%97/PSR-4-autoloader.md)
* [Composer Autoload不完全中译文](https://github.com/5-say/composer-doc-cn/blob/master/cn-introduction/04-schema.md#autoload)

### 注释

注意，不管你是大牛还是菜鸟，请务必给类与方法带上注释，签上你的 `ID` 名也是可以，详细描述作用、传入与返回的参数。

```php
<?php
namespace Douyasi\Cache;
use Douyasi\Models\Setting as Setting;
use Douyasi\Models\SettingType as SettingType;
use Cache;
use Config;
/**
 * Class SettingCache
 *
 * 系统动态设置缓存
 * 操作模型：Setting, SettingType
 * 操作数据表：settings, setting_type
 *
 * @package Douyasi\Cache
 * @author raoyc <raoyc2009@gmail.com>
 */
class SettingCache
{
    
/**
     * 缓存特定动态设置分组下的设置数据
     *
     * @param string $type_name 动态设置分组名
     * @param string $format 'object'|'array' 缓存数据的格式
     * @return boolean true|false 缓存成功，则返回true, 否则返回false
     */
    public static function cacheSetting($type_name, $format ='object')
    {
       ......
       return true;
    }
    ......
}
```

### 数据库命名规范

* 参照 Laravel 模型相关内容，我们可以看出，Laravel 倾向于使用复数名词作为表名，Laravel 框架自带了一个 User 模型操作 users 用户表。

* 推荐使用三个小写字母以上的字符串作为数据库表前缀，如芽丝内容管理框架就使用 `yascmf_` 作为表前缀。

* 数据库表默认使用 `utf8_unicode_ci` 作为排序规则。

* 数据表名与表字段推荐使用全小写英文字母，单词之间采用下划线 (_) 作为分隔符；数据库字段应避免使用 MySQL 关键字（如 `desc` 、 `null` 、 `count` 与 `order` 等 ）；数据库表及字段在设计时应添加与保留注释（即 `COMMENT` ）内容。

* 使用Laravel ORM时，可能还需要添加额外的字段，如自动时间戳记录的字段 `created_at` 、 `updated_at` 与 `deleted_at` 等。

* 在 SQL 语句的编写中，凡是 SQL 语句的关键字一律大写，如：`SELECT`、`ORDER BY`、 `GROUP BY`、 `FROM`、 `WHERE`、 `UPDATE`、 `INSERT INTO`、 `SET`、 `BEGIN`、 `END` 等。

* 遵守第三范式 3NF 标准的规定：

>    A.表内的每一个值都只能被表达一次。  
>    B.表内的每一行都应该被唯一的标识(有唯一键)。  
>    C.表内不应该存储依赖于其他键的非键信息。  

* 主键（包括联合主键）与索引名应表义清晰明确，唯一索引建议带上 `_unique` 缀名，以与其它普通索引区别。

这里列出一份数据表示例：

```SQL
DROP TABLE IF EXISTS `yascmf_users`;
CREATE TABLE `yascmf_users` (
  `id` int(12) unsigned NOT NULL AUTO_INCREMENT COMMENT 'id',
  `username` varchar(20) COLLATE utf8_unicode_ci NOT NULL COMMENT '用户登录名',
  `password` varchar(60) COLLATE utf8_unicode_ci NOT NULL COMMENT '用户密码',
  `nickname` varchar(20) COLLATE utf8_unicode_ci NOT NULL COMMENT '用户屏显昵称，可以不同用户登录名',
  `email` varchar(120) COLLATE utf8_unicode_ci DEFAULT NULL COMMENT '用户邮箱',
  ......
  `created_at` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '创建时间',
  `updated_at` datetime NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '修改更新时间',
  `is_locked` tinyint(3) NOT NULL DEFAULT '0' COMMENT '是否锁定限制用户登录，1锁定,0正常',
  `confirmation_code` varchar(255) COLLATE utf8_unicode_ci NOT NULL COMMENT '确认码',
  `confirmed` tinyint(1) DEFAULT '0' COMMENT '是否已通过验证 0：未通过 1：通过',
  `remember_token` varchar(60) COLLATE utf8_unicode_ci DEFAULT NULL COMMENT 'Laravel 追加的记住令牌',
  PRIMARY KEY (`id`),
  KEY `nickname_index` (`nickname`),
  UNIQUE KEY `user_username_unique` (`username`),
  UNIQUE KEY `user_email_unique` (`email`),
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci COMMENT='用户表';
```

### 控制器文件方法命名

① `RESTful` 资源控制器

对于 `RESTful` 资源控制器 ，相应的方法采用其默认命名规则，如：

```
index() //index方法对应资源列表页面
create() //create方法对应创建资源页面
show($id) //show方法对应特定id资源页面
......
```

如在资源控制器上有其它扩展方法，比如批量删除，请遵循下面普通控制器方法命名规范。
控制器类方法命名规范。

② 普通控制器

对于其它普通控制器方法名，推荐使用 HTTP动作 ( 如 `get|post|delete` ) + 资源|行为 的驼峰命名规范。如：

```php
......
    public function getLogin(){
        return View::make('login');
    }
    public function getUpload(){
        return View::make('upload');
    }
    public function postUpload(){
        //处理upload过来的数据
    }
......
```

### 视图文件命名

默认视图文件放置在 `resource/views` 目录下，视图文件或文件夹采用全小写英文命名，单词之间采用下划线 ( `_` ) 作为分隔符。`Blade` 视图模版请带上 `blade` 点缀作为文件名。

对于 `RESTful` 资源控制器相关视图文件，遵循官方默认命名，如：

```
resource/
    views/
        article/
            create.blade.php
            index.blade.php
            show.blade.php
        setting/
            create.blade.php
            ......    
```

在控制器中调用视图遵循点分调用规则，如：

```php
......
    public function getCustomerList(){
        return view('customer.index'); 
        //返回位于resource/views/customer/index.blade.php的视图
    }
    public function index(){
        return view('setting.index'); 
        //返回位于resource/views/setting/index.blade.php的视图
    }
......
```

视图里面 `Blade` 模版标签的使用请遵循官方文档，注意 `{{ }}` 与 `{!! !!}` 区别。

### 常规模型（Model）文件命名

这里所说的常规模型是指业务不复杂的数据表模型，针对业务复杂的模型需要自行扩展中间层模型。

Laravel 默认的 `Eloquent ORM` 模型放置在 `app` 目录下，`Laravel`
模型默认会操作对应名称为「类名称的小写复数形态」的数据库表，如名为 `User.php` 的模型默认会操作名为 `users` 的数据表。

常规模型命名遵循 `Laravel` 默认规范,如操作 `setting_type` 数据表的模型，我们可以这样定义：

```php
class SettingType extends Eloquent {

    protected $table = 'setting_type';

    public $timestamps = false;  //关闭自动更新时间戳

    public function setting()
    {
        return $this->hasMany('Setting');
    }
}
```

### 复杂业务逻辑模型的文件命名

根据业务需求，某些复杂的模型需要自行扩展中间层模型，以便更好地项目管理。

这里举一个例子，接到要一个理财项目开发需求，我们可以把处理投资客户的账户资金相关业务逻辑放置在 `Account` 目录下。这种扩展开发方式可以参考 Laravel 某些著名的项目，如 `laravel.io` 、 `LaraBook` 等。

它的目录结构可以如下：

```php
app/
        Account/  /*处理投资客户的账户资金相关业务逻辑*/
            Account.php
            AccountLog.php
            ......
        Customer/  /*处理投资客户开户登记，个人信息变更等业务逻辑*/
            Customer.php 
            CustomerCreator.php
```

>    某些 `CMS` 会在模型层上面封装一层 仓库（`repository`），仓库负责数据接口（从模型中读写数据），这种好处是避免在控制器中堆积过多代码以免撑爆，难以阅读，也方便以后数据库模型扩展。

### 自定义函数（helper）

实际开展中我们需要引入与扩展一些帮助函数，可以将它放置于 `app/helper.php` 文件里，然后在 `bootstrap/autoload.php` 中手动引入加载的，也可以通过 `Composer` 配置 `composer.json` 来实现自动加载。

```php
......
require __DIR__.'/../vendor/autoload.php';
require __DIR__.'/../app/helper.php'; // 引入自定义函数库
......
```

```json
......
    "autoload": {
        "classmap": [
            "database"
        ],
        "psr-4": {
            "App\\": "app/",
            "Douyasi\\": "douyasi/"
        },
        "files": [
            "app/helper.php",
            "douyasi/helper.php"
        ]
    },
......
```

下面列出一个示例 `helper` 函数，该函数参考 `view` 或 `request` 实例化缓存类。

```php
if (!function_exists('cache')) {

    /**
     * cache helper
     *
     * @param  string $key
     * @param  mixed $default
     * @return mixed
     */
    function cache($key = null, $default = null)
    {
        if (is_null($key)) {
            return app('cache');
        }

        return app('cache')->get($key, $default);
    }
}
```
