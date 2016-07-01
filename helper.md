# 助手方法

>   `YASCMF` 新增了不少助手方法，以提高开发效率。请仔细阅读下面助手方法。

## 站点URL生成

由于本项目采用多域名站点方式部署，故生成的 `URL` 跟站点配置有很大的关系。

为此，系统提供相关助手方法来生成URL。

### 非静态资源URL生成

>   `site_url($path, $site = 'desktop', $scheme_less = true)`

`site_url()` 接受3个参数，第1个参数为去掉根路由前缀的路径，第2个参数为站点别名，第3个参数为是否移除 `https:` 或者 `http:` 。

站点别名配置在 `config/site.php` 里面，目前配置为：

```php
    //路由相关配置
    'route' => [

        #路由站点分组(对应block中间件参数)
        'group' => [
            'desktop',
            'api',
            'admin',
            'doc',
        ],

        #路由域名绑定
        'domain' => [
            'desktop' => env('DESKTOP_SITE', 'http://yascmf.dev/'),
            'api'     => env('API_SITE', 'http://yascmf.dev/'),
            'admin'   => env('ADMIN_SITE', 'http://yascmf.dev/'),
            'doc'     => env('DOC_SITE', 'http://yascmf.dev/'),
        ],

        #路由前缀绑定
        'prefix' => [
            'desktop' => '',
            'api'     => 'api',
            'admin'   => 'admin',
            'doc'     => 'docs',
        ],

    ],
```

其中 `desktop` 表示桌面站点，`mobile` 表示移动站点，`admin` 表示管理后台站点，`doc` 表示文档站点。它们对应的路由前缀配置在 `prefix` 里，部署到线上时可以变更路由前缀。

示例：

```php

// site_url('dashboard', 'admin') === '//example.com/admin/dashboard'
<li><a href="{{ site_url('dashboard', 'admin') }}"><i class="fa fa-dashboard"></i> 主页</a></li>

// site_url('role/create', 'admin') === '//example.com/admin/role/create'
<a href="{{ site_url('role/create', 'admin') }}" class="btn btn-primary margin-bottom">新增角色</a>

```

### 静态资源URL生成

>   `_asset($path, $sub_dir = '', $scheme_less = true)`

`_asset()` 其实等价于 `cdn()` ，可以看作是对框架 `asset()` 的重写，第1个参数为相对于 `$sub_dir` 路径，第2个参数为 静态资源相对于 `public` 的子目录，第3个参数为是否移除 `https:` 或者 `http:` 。

示例：

```html
<!--
_asset('lib/Lato_100.css') === '//example.com/lib/Lato_100.css'
<link href="{{ _asset('lib/Lato_100.css') }}" rel="stylesheet" type="text/css" />
-->

<!--
 _asset('back/dist/js/yascmf.js') === '//example.com/back/dist/js/yascmf.js'
 _asset('dist/js/yascmf.js', 'back') === '//example.com/back/dist/js/yascmf.js'
-->
<script src="{{ _asset('back/dist/js/yascmf.js') }}" type="text/javascript"></script>
```

### 站点路由URL生成

>   `_route($name, $parameters = [])`

`_route()` 主要用于生成不同站点的 `RESTFUL URL` ，可以看作是对框架 `route()` 的重写（但请注意，这里并不需要在路由里面定义 `as` 路由别名）。它接受2个参数：第1个参数为路由名，为了区分站点与路径，路由名由 `:` 与 `.` 切分，`:` 之前的为站点别名，之后的为点分式的路由别名，支持 `{}` 参数绑定，第2个参数为数组或者数字或者字符串,一般来说借助于第2个参数能够生成你想要的复杂URL。下面给出具体的示例：

```php
_route('admin:res.index');  // 等价于 '//example.com/admin/res'
_route('admin:res.create');  // 等价于 '//example.com/admin/res/create'
_route('admin:res.create', [ 'lang' =>'chs' ]);  // 等价于 '//example.com/admin/res/create?lang=chs'
_route('admin:res.store');  // 等价于 '//example.com/admin/res'
_route('admin:res.show');  // 等价于 '//example.com/admin/res/{id}'
_route('admin:res.edit');  // 等价于 '//example.com/admin/res/{id}/edit'
_route('admin:res.edit', 5);  // 等价于 '//example.com/admin/res/5/edit'
_route('admin:res.edit', [2, 'lang' => 'chs']);  // 等价于 '//example.com/admin/res/2/edit?lang=chs'
_route('admin:res.update');  // 等价于 '//example.com/admin/res/{id}'
_route('admin:res.destroy');  // 等价于 '//example.com/admin/res/{id}'
_route('admin:user.photo');  // 等价于 '//example.com/admin/user/photo'
_route('admin:user.{id}.block', 5);  // 等价于 '//example.com/admin/user/5/block'
_route('admin:user.{uid}.photo.{pid}', [4, 5, 'lang' => 'chs']);  //等价于 '//example.com/admin/user/4/photo/5?lang=chs'
```

### 站点相对路径生成

>   `site_path($path, $site = 'desktop')`

`site_path()` 主要用于生成站点的相关路径，以便框架重定向跳转。它接受2个参数：第1个参数为路径，第2个参数为站点别名。

示例：

```php
//......
$data = $request->all();
$role = $this->role->store($data);
if ($role->id) {  //添加成功
    // site_path('role', 'admin') === 'admin/role'
    return redirect()->to(site_path('role', 'admin'))->with('message', '成功新增角色！');
} else {  //添加失败
    return redirect()->back()->withInput($request->input())->with('fail', '数据库操作返回异常！');
}
//......
```

### 资源快捷路径

>   `ref($key = null, $default = null)`

`ref()` 用于映射较长的资源路径，也为了方便以后资源类库升级。键名对应的映射关系配置在 `site.php` 文件 `asset.alias` 数组中。

```php
    'asset' => [
    //......
        'alias' => [
            'bootstrap.js'        => 'lib/bootstrap/3.3.4/js/bootstrap.min.js',
            'bootstrap.css'       => 'lib/bootstrap/3.3.4/css/bootstrap.min.css',
            'font-awesome.css'    => 'lib/font-awesome/4.3.0/css/font-awesome.min.css',
            'icheck.js'           => 'lib/iCheck/1.0.2/icheck.min.js',
            'icheck_all.css'      => 'lib/iCheck/1.0.2/all.css',
            'icheck_blue.css'     => 'lib/iCheck/1.0.2/square/blue.css',
            'ionicons.css'        => 'lib/ionicons/2.0.1/css/ionicons.min.css',
            'html5shiv.js'        => 'lib/html5shiv/3.7.3/html5shiv.min.js',
            'respond.js'          => 'lib/respond.js/1.4.2/respond.min.js',
            'jquery.js'           => 'lib/jQuery/jQuery-2.1.3.min.js',
            'lato.css'            => 'lib/Lato_100.css',
            'open-sans.css'       => 'lib/Open+Sans400italic,600italic,400,600.css',
            'source-sans-pro.css' => 'lib/Source+Sans+Pro_300,400,600,700,300italic,400italic,600italic.css',
            'layer.js'            => 'lib/layer/2.2/layer.js',
            'chosen.js'           => 'lib/chosen/1.3.0/chosen.jquery.min.js',
            'chosen.css'          => 'lib/chosen/1.3.0/chosen.css',
            'ckeditor.js'         => 'lib/ckeditor/ckeditor.js',
            'my97datepicker.js'   => 'lib/My97DatePicker/WdatePicker.js',
            'form.js'             => 'lib/form/jquery.form.js',
        ],
    ],
```

你可以在模版里通过 `ref()` 来引用这些类库文件：

```php
@section('head_css')
    <link href="{{ _asset(ref('bootstrap.css')) }}" rel="stylesheet" type="text/css" />
    <!-- Font Awesome Icons -->
    <link href="{{ _asset(ref('font-awesome.css')) }}" rel="stylesheet" type="text/css" />
    <!-- Ionicons -->
    <link href="{{ _asset(ref('ionicons.css')) }}" rel="stylesheet" type="text/css" />
    <!-- Theme style -->
    ......
@stop
```


## 其它辅助方法

### 字典

>   `dict($dot_key = null, $default = null)`

字典方法，将机器可读的数字字符串转换为系统人类可读的文字。第1个参数为点分式键名，第2个参数为不存在该键名时给出的默认值。所有字典数据定义于 `Douyasi\Dict` 类中，采用 `Laravel` 点分式获取特定数组键。

```php
dict('log_type.upload');  //'上传'
dict('bool.0');  //'无'
dict('unknown.0');  //不存在该键名,返回null,模版中不可见此字符
```

### cache

>   `cache($key = null, $default = null)`

类似于 `session()` 与 `config()` 之类的框架方法，`cache()` 返回 `Cache` 实例。

### deny

>   `deny($site = 'admin')`

根据不同站点返回不同模版的 `403` 状态页面。

## 更多

`YASCMF` 更多的助手方法请自行查阅 `app/helper.php` 与 `douyasi/helper.php` 文件，注释写的比较明白。
