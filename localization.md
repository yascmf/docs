# 本地化

>   本项目已提供了基础的多语言外观切换功能，如果你需要完整的本地化功能，请自己开发。

## 多语言化仓库

本地化可参考下面 `Laravel` 的语言仓库：

https://github.com/caouecs/Laravel-lang

`YASCMF` 使用到此仓库的 `zh-CN` 本地化语言文件，`zh-CN` 表示中国大陆简体，`zh-TW` 表示中国台湾正(繁)体。

## 本地化开发

### 首选语言与回落语言

你可以在 `config/app.php` 中配置首选语言与回落语言

```php
    'locale' => 'zh-CN',
    'fallback_locale' => 'en',
```

`YASCMF` 首选语言为 `zh-CN` （简体中文），回落语言为 `en` （英文）。

### 语言文件

`YASCMF` 本地化外观语言配置在 `resources/lang/` 目录下，其中：`yascmf.php` 为此内容框架前台宣传页本地化语言文件。您可以参考此自行开发其它站点本地化（多语言）版本。

`Laravel` 官网本地化文档链接：

https://laravel.com/docs/5.2/localization

请特别注意：`YASCMF` 前台（宣传页）提供了基础的多语言外观（导航）切换，但管理后台依然是简体中文（后台使用者一般为单一语言，多语言本地化更多地会在前台网站上使用到），如果有海外项目开发需求，可自行考虑对后台进行多语言本地化开发。
