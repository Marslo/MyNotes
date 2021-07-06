---
search:
  keywords:
    - gitbook
---

# gitbook

**Table of Contents** _generated with_ [_DocToc_](https://github.com/thlorenz/doctoc)

* [`gitbook-cli`](gitbook.md#gitbook-cli)
  * [installation](gitbook.md#installation)
  * [initial `book.json`](gitbook.md#initial-bookjson)
* [gitbook format](gitbook.md#gitbook-format)
  * [hints](gitbook.md#hints)
  * [tab](gitbook.md#tab)
  * [emoji](gitbook.md#emoji)
* [Q&A](gitbook.md#qa)
  * [`if (cb) cb.apply(this, arguments)`](gitbook.md#if-cb-cbapplythis-arguments)
  * [`TypeError [ERR_INVALID_ARG_TYPE]` in `git init`](gitbook.md#typeerror-err_invalid_arg_type-in-git-init)
  * [`unexpected token: .`](gitbook.md#unexpected-token-)

{% hint style="info" %}
reference:

* [gitbook 简明教程](http://www.chengweiyang.cn/gitbook/)
* [gitbook 入门教程](https://yuzeshan.gitbooks.io/gitbook-studying/content/)
* [book.json](http://www.chengweiyang.cn/gitbook/customize/book.json.html)
* [emoji](https://gist.github.com/rxaviers/7360908)
* [gitbook howto](https://coding-notes.readthedocs.io/en/latest/rst/dt/gitbook.html)
* [gitbook 安装配置](http://gitbook.wiliam.me/)
* [GitBook插件整理 - book.json](https://blog.csdn.net/qq_43514847/article/details/86598399)
* [Gitbook常用插件简介](https://blog.csdn.net/qq_37149933/article/details/64170653)
* [gitbook使用及book.json详细配置](https://blog.csdn.net/gongch0604/article/details/107494736)
* [GitBookによるドキュメント作成](https://qiita.com/mebiusbox2/items/938af4b0d0bf7a4d3e33)

useful plugins

* [gitbook-plugin-fbqx](https://ymcatar.gitbooks.io/gitbook-test/content/testing_fbqx.html)
{% endhint %}

## `gitbook-cli`

### installation

* install

  ```bash
  $ brew install node
  $ npm i -g gitbook-cli [--save-dev]
  ```

* verify

  ```bash
  $ gitbook --version
  CLI version: 2.3.2
  GitBook version: 3.2.3
  ```

* check packages

  ```bash
  $ npm root -g
  /usr/local/lib/node_modules
  ```

### initial `book.json`

* gitbook install

  ```bash
  $ gitbook install
  info: installing 29 plugins using npm@3.9.2
  info:
  info: installing plugin "search-plus"
  info: install plugin "search-plus" (^0.0.11) from NPM with version 0.0.11
  /Users/marslo/mywork/tools/git/marslo/mbook
  └─┬ gitbook-plugin-search-plus@0.0.11
    └── html-entities@1.2.0
  ....
  ```

  * gitbook install in fresh environment"

    ```bash
    $ gitbook install --log=debug --debug
    Installing GitBook 3.2.3
      SOLINK_MODULE(target) Release/.node
      CXX(target) Release/obj.target/fse/fsevents.o
      SOLINK_MODULE(target) Release/fse.node
      SOLINK_MODULE(target) Release/.node
      CXX(target) Release/obj.target/fse/fsevents.o
      SOLINK_MODULE(target) Release/fse.node
    gitbook@3.2.3 ../../var/folders/s3/mg_f3cv54nn7y758j_t46zt40000gn/T/tmp-10600Rn1q3aFhRWiI/node_modules/gitbook
    ├── escape-html@1.0.3
    ├── escape-string-regexp@1.0.5
    ├── destroy@1.0.4
    ├── ignore@3.1.2
    ├── bash-color@0.0.4
    ├── gitbook-plugin-livereload@0.0.1
    ├── cp@0.2.0
    ...
    ```

  * alternatively

    ```bash
    $ gitbook serve
    Live reload server started on port: 35729
    Press CTRL+C to quit ...

    info: 7 plugins are installed
    info: 31 explicitly listed

    Error: Couldn't locate plugins "search-plus, simple-page-toc, github, github-buttons, prism, prism-themes, advanced-emoji, anchors, include-codeblock, ace, emphasize, katex, splitter, mermaid-gb3, tbfed-pagefooter, sectionx, local-video, anchor-navigation-ex, favicon, todo, alerts, include-csv, puml, sharing-plus, image-captions, donate, toggle-chapters, navigator, downloadpdf", Run 'gitbook install' to install plugins from registry.
    ```

## gitbook format

### [hints](https://github.com/GitbookIO/plugin-hints)

> [Simran-B/gitbook-plugin-callouts](https://github.com/Simran-B/gitbook-plugin-callouts)

| styles | code |
| :--- | :--- |
| info | `{% hint style='info' %}`  `{% endhint %}` |
| tip | `{% hint style='tip' %}`  `{% endhint %}` |
| danger | `{% hint style='danger' %}`  `{% endhint %}` |
| warning | `{% hint style='warning' %}`  `{% endhint %}` |
| success | `{% hint style='success' %}`  `{% endhint %}` |

#### success

{% hint style="success" %}
```javascript
{% hint style='success' %}
success
{% endhint %}
```
{% endhint %}

#### tip

{% hint style="info" %}
```javascript
{% hint style='tip' %}
info
{% endhint %}
```
{% endhint %}

#### danger

{% hint style="danger" %}
```javascript
{% hint style='danger' %}
danger
{% endhint %}
```
{% endhint %}

#### warning

{% hint style="warning" %}
```javascript
{% hint style='warning' %}
warning
{% endhint %}
```
{% endhint %}

#### quote

{% hint style="info" %}
**Important info**: this _note_ needs to be highlighted

```javascript
{% hint style='info' %}
**Important info**: this *note* needs to be highlighted
{% endhint %}
```
{% endhint %}

### tab

> \[!TIP\] this function only for native [gitbook.io](https://www.gitbook.com/) example:
>
> * [here](https://imarslo.gitbook.io/handbook/kubernetes/certificates#check-info)
>
>   reference:
>
>   -[Templating](https://gitbookio.gitbooks.io/documentation/content/format/templating.html)

#### code

* gitbook.io
* [gitbook-plugin-codetabs](https://www.npmjs.com/package/gitbook-plugin-codetabs)

### emoji

> **reference**:
>
> * [gist](https://gist.github.com/rxaviers/7360908)
> * [Emoji](https://github.com/StylishThemes/GitHub-Dark/wiki/Emoji)
> * [broken & new emojis](https://github.com/WebpageFX/emoji-cheat-sheet.com/issues/429)
> * [Emojis list from https://api.github.com/emojis](https://awes0mem4n.github.io/emojis-github.html)
> * [emoji-cheat-sheet.com](https://github.com/WebpageFX/emoji-cheat-sheet.com)

| **emoji** | **code** |
| :---: | :---: |
| :joy: | `:joy:` |
| :u7981: | `:u7981:` |
| :u7121: | `:u7121:` |
| :white\_check\_mark: | `:white_check_mark:` |
| :four\_leaf\_clover: | `:four_leaf_clover:` |

## Q&A

### `if (cb) cb.apply(this, arguments)`

> **reference**:
>
> * [How I fixed a "cb.apply is not a function" error while using Gitbook](https://flaviocopes.com/cb-apply-not-a-function/)

* issue

  ```bash
  $ gitbook serve
  Live reload server started on port: 35729
  Press CTRL+C to quit ...

  /usr/local/lib/node_modules/gitbook-cli/node_modules/npm/node_modules/graceful-fs/polyfills.js:287
        if (cb) cb.apply(this, arguments)
                   ^

  TypeError: cb.apply is not a function
      at /usr/local/lib/node_modules/gitbook-cli/node_modules/npm/node_modules/graceful-fs/polyfills.js:287:18
  ```

* solution:
  * raw `polyfills.js`

    ```javascript
    62   fs.stat = statFix(fs.stat)
    63   fs.fstat = statFix(fs.fstat)
    64   fs.lstat = statFix(fs.lstat)
    ```

  * fix `polyfills.js`

    ```javascript
    62   // fs.stat = statFix(fs.stat)
    63   // fs.fstat = statFix(fs.fstat)
    64   // fs.lstat = statFix(fs.lstat)
    ```

### `TypeError [ERR_INVALID_ARG_TYPE]` in `git init`

* issue

  ```bash
  $ gitbook init
  warn: no summary file in this book
  info: create README.md
  info: create SUMMARY.md

  TypeError [ERR_INVALID_ARG_TYPE]: The "data" argument must be of type string or an instance of Buffer, TypedArray, or DataView. Received an instance of Promise
  ```

* solution: downgrade the nodejs to `12.x.x`
  * [purge nodejs](https://github.com/marslo/ibook/tree/ce2528bdd9edeb68f3c12d879c2d0042b5f18bec/docs/tools/node.md#purge)

    ```bash
    $ brew uninstall node
    $ brew doctor
    $ brew cleanup --prune-prefix
    ```

  * re-install `node@12`

    ```bash
    $ brew install node@12
    $ brew link node@12
    $ cat >> ~/.bash_profile << EOF
    NODE_HOME='/usr/local/opt/node@12'
    LDFLAGS="-L${NODE_HOME}/lib ${LDFLAGS}"
    CPPFLAGS="-I${NODE_HOME}/include ${CPPFLAGS}"
    PATH=${NODE_HOME}/bin:$PATH
    export NODE_HOME LDFLAGS CPPFLAGS PATH
    EOF
    ```

### `unexpected token: .`

* issue

  ```bash
  $ git serve
  ...
  unexpected token: .
  ```

* [solution](https://blog.csdn.net/HammerTien/article/details/86613392)
  * raw code
  * fixed code
