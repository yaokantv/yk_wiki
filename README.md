# Yaokantv Docs
> [示例](https://fervent-goldberg-a4201e.netlify.com/ "示例")

## 文档编写
### 添加顶部菜单
> 比如添加一个iOS-SDK菜单

* 在`/source/_data/menu.yml`文件中添加对应的菜单名称和菜单对应的目录 `docs_ios: /docs_ios/`
* 在`/themes/navy/languages/` 目录下所有语言文件的 menu 下面添加对应说明比如在zh-cn.yml里menu下面添加  `docs_ios: iOS-SDK`
* 在/`scripts/helpers.js`文件的第十行,`localizedPath`数组加上刚刚添加的 `docs_ios`
* 在`/source/`目录下对应的语言文件夹,如: `zh-cn, en-us` 目录下添加菜单对应的文件夹,如:`/source/zh-cn/docs_ios/`

### 菜单下添加一篇文档
> 比如在iOS-SDK菜单下添加更新日志文档

* 在`/source/zh-cn/docs_ios/`和`/source/en-us/docs_ios/`目录下各添加一个 **update_log.md**文件,并写入内容(如某些语言的文档还未完成,可以使用空的md文件)
* 在 `/source/_data/sidebar.yml`下添加对应的键值对: `update_log: update_log.html`
* 在`/themes/navy/languages/` 目录下所有语言文件的 sidebar 下面添加对应说明比如在zh-cn.yml里sidebar 下面添加  `update_log: 更新日志`

## 编译
``` bash
$ git clone https://github.com/yaokantv/yk_wiki.git
$ cd yk_wiki
$ npm install hexo-cli -g
$ npm install
$ hexo g
$ hexo s
```
此时可通过 localhost:4000 在本地测试运行.生成的所有文件在`/public` 目录下.
