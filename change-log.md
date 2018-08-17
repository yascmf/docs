# 更新日志

>   这里记录历次更新日志。


### 2016年度

* 2016-07-01 在 `Github` 发布适配 `Laravel 5.2` 新版： [`YASCMF/BASE`](https://github.com/yascmf/base) ，启用新的仓库地址，`5.1` 旧版将进入存档期。更新的内容，可参考下面版本比较。

* 2016-08-11 剥离 `public\lib` 目录下公共静态资源到 `cdn` 或第三方域，以加快速度。如需要自行部署公共静态资源，请访问 [`YASCMF/ASSET`](https://github.com/yascmf/asset) 。

* 2016-12-28 完成后台路线图中文章、分类模块，借助于 `editor.md` 可以很方便地使用 `markdown` 撰写博客；官方提供一套异常简洁的轻博客模板。

![](http://www.yascmf.com/uploads/content/20161228/586383321ad29_38o.png)

### 2017年度

* 2017年 由于工作原因，暂停更新，但会对个人或商业用户项目提供技术支持。


### 2018年度

* 2018-07 开始适配 `Laravel 5.5 LTS` 版本的开发，同时开始前后端分离版本（[YASCMF/API](https://github.com/yascmf/api) + [YASCMF/ADMIN](https://github.com/yascmf/admin)）的开发。

* 2018-08 使用 `migration` 来迁移数据库，并发布 `LTS 5.5 release` 版本。


### 项目及其版本比较

| 特征\项目 | `douyasi/yascmf` | `YASCMF/BASE` | `YASCMF/API` | `YASCMF/ADMIN` |
| ---------- | -------- | ---------------- | ----- | ------- |
| 底层框架版本 | `Laravel 5.1.x` | `Laravel 5.2.x` / `Laravel LTS 5.5.x`       | `Laravel/Lumen LTS 5.5.x`  | `Vue 2.5.x` + `Element-UI 2.3.x`   |
| 最低依赖 | `php >= 5.5.9`     | `php >= 5.5.9` 或 `php >= 7.0.0`  | `php > 7.0.0` | `node >= 4.0.0` + `npm >= 3.0.0`  |