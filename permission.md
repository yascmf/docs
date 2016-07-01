# 权限

>   `YASCMF` 后台权限控制模块参考了 `Entrust` 并结合框架自身的`ACL` 授权功能而完成。请注意，`YASCMF` 仅支持单用户单角色，如果需要单用户多角色支持，需要自行扩展与修改相关代码。

## 权限控制

`Laravel 5.2` 其实已经内置 `ACL` 授权控制，考虑到本系统业务多样性以及第三方包（如 `Entrust` 适配进度过慢等）因素，故 `YASCMF` 自行完成权限系统的构建。

有关权限认证ACL参考文档:

https://mattstauffer.co/blog/acl-access-control-list-authorization-in-laravel-5-1

http://9iphp.com/web/laravel/laravel-5-acl-define.html

## 新增权限

你应该看到后台权限页面的提示文字：

>   权限影响系统安全与稳定，错误或不合理的修改可能会影响系统业务与逻辑，故在此屏蔽掉权限 增、删、改 功能。
后续如果增加新的权限或者删改老的权限，可以使用SQL脚本来实现。

权限对应的数据库表名为 `yascmf_permissions` ，为了实现权限分组，带有 `@` 前缀符的权限表示该权限组的父级（可用于导航菜单显隐控制，也可用于在控制器构造方法中），尾缀 `-{action}` 表示其下的子权限。

你可以通过 `SQL` 脚本文件或者可视化的 `SQL` 管理软件来新增权限，比如我们可定义以下权限字符串来完成文章的权限控制：

权限名 | 权限含义
----- | -----
@article | 文章
article-show | 文章查看（只读型权限）
article-write | 文章写入（包括存储与更新，可写型的权限）
article-search | 文章搜索

当然，如果继续细分文章模块里面的权限，你只需要新增权限字符串，比如文章删除 （`article-delete`）与文章批量删除（`article-multi-delete`）。


## 模版中权限判断

`Laravel 5.2` 内置了 `@can` 与 `@endcan` 标签用于权限判断。`@can('article-write')` 用于判断是否拥有 `文章写入` 的权限。

```php
......
@foreach ($articles as $art)
  <tr>
    <td>
        @can('article-write')
        <a href="{{ _route('admin:article.edit', $art->id) }}"><i class="fa fa-fw fa-pencil" title="修改"></i></a>
        @endcan
        <a href="javascript:void(0);"><i class="fa fa-fw fa-link" title="预览"></i></a>
    </td>
    ......
  </tr>
@endforeach
```

## 控制器中权限判断

控制器中使用 `Gate` 门面，结合路由中间件等实现返回未授权的 `403` 状态页面。`Gate::denies('article-write')` 用于判断 `GATE` 网关是否拒绝 `文章写入` （即是否没有 `文章写入` 权限）。

```php
<?php

namespace App\Http\Controllers\Admin;

use Illuminate\Http\Request;
use Gate;

class ArticleController extends BackController
{

    public function __construct()
    {
        parent::__construct();

        if (Gate::denies('@article')) {
            $this->middleware('deny403');
        }
    }

    //......

    public function store(Request $request)
    {
        if (Gate::denies('article-write')) {
            return abort(403);
            //return deny();
        }
        //......
    }

    //......

}
```
