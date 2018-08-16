# 安装

>   诸如 `composer` 包镜像站点不稳定等问题，请自行解决，墙都不会翻，还敢自称码农。


## 安装说明

① 下载源码包：

你可以通过多种方式下载源码（如HTTP下载，Git克隆），下载之后进入源码目录，使用 `composer` 安装PHP依赖，生成 `.env` 配置文件。

`Linux` 下 或 `Window Git Bash` 下可执行下面命令：

```bash
git clone https://github.com/yascmf/base.git
cd base
```

② 修改 `.env` 配置文件，并安装依赖：

```bash
cp .env.example .env
vim .env
composer install -vvv
```

请根据数据库与服务器实际情况修改 `.env` 配置文件，这里给出一个示例，你也可参考 `.env.example` 文件。

```ini
APP_ENV=local
APP_DEBUG=true
APP_KEY=4fXSYZITSteq6jq0Zr5u2x5Tt3J4O9kF
APP_URL=http://localhost

DB_HOST=127.0.0.1
DB_DATABASE=yascmf_base
DB_USERNAME=homestead
DB_PASSWORD=secret
DB_PREFIX=yascmf_

......

DESKTOP_SITE=//base.yascmf.test
API_SITE=//base.yascmf.test
ADMIN_SITE=//base.yascmf.test
DOC_SITE=//base.yascmf.test
```

生成 `APP_KEY` 随机串可执行下面 `artisan` 命令：

```bash
php artisan key:generate
```

③ 域名绑定：

服务器绑定域名，并将文档根目录设置为源码包 `public` 目录下，给 `storage` 目录可写权限，如果后台需要上传图片请给 `public\uploads` 可写权限。

*特别说明：请配置站点参数为你绑定的域名。*

如果你是单域名站点，绑定的域名假设为 `base.yascmf.test` ，那请配置所有站点域名参数为此域名：

```ini
DESKTOP_SITE=//base.yascmf.test
API_SITE=//base.yascmf.test
ADMIN_SITE=//base.yascmf.test
DOC_SITE=//base.yascmf.test
```

如果你不是通过常规80端口来访问，请配置域名时带上端口号，类似如下面：

```ini
DESKTOP_SITE=//localhost:8080
```

现在 `YASCMF` 支持使用内置的 `php artisan serve` 启动，只需要参考上面的将 `DESKTOP_SITE` 设置为默认的 `//localhost:8000` 值。

④ 迁移数据：

目前已支持 `migrantion` ，不需要再手动导入数据库了。

```bash
php artisan migrate
php artisan db:seed
```

④ 访问前台：

访问服务器绑定的域名，出现 `YASCMF` 或 `芽丝轻博客` 字眼界面，说明您已经安装成功。

⑤ 登录后台：

输入 `http://base.yascmf.test/admin/` 或 `http://localhost:8080/admin` 网址，后台使用的超管帐号与密码均为 `admin` ，登入之后，您可以体验一番。

## 公共静态资源配置

有关配置公共静态资源的说明，请参考：https://github.com/yascmf/asset 。

## editor.md 编辑器配置

如果后台 `markdown` 编辑器异常，请参考： https://github.com/douyasi/laravel-editor-md 说明，删除 `public/vendor/editor.md` 目录，重新发布资源。

