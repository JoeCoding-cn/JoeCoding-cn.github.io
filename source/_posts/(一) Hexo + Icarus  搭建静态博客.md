toc: true
categories:
  - Hexo
  - Icarus
tags: 
  - Hexo
  - Icarus
  - 博客
---

#### 为什么要自建博客？(都是废话，可以跳过看下面的)

在之前我记录一些笔记的时候用过CSDN，后面用过一段时间简书、印象笔记、有道云啥的都有用过，CSDN的排版个人感觉总是很别扭, 后面就想着自己给搭建一个博客。 现在网上非常多的开源博客 WordPress、Ghost、Halo、Hexo之类的，主要分为动态、静态两类。自己看了看案例对比最后选择了Hexo这个静态博客 ，以上博客有些啥区别可以自行百度了解 。 

#### 为什么选择Hexo？

正如Hexo官网描述所说 `快速、简洁且高效的博客框架`  Hexo支持`Markdown` 平时自己写好md文件之后编译之后就成了html格式了 支持部署到GithubPages Hexo只需要一条指令就能发布到GitPage 我选择Hexo还有一个原因就是穷~ 买不起云服务器 咱们就利用Gitee或者是Github的Pages空间配合Hexo来搭建一个自己的博客!
<!--more-->

#### (一) 搭建Hexo所需环境

确保电脑有安装 `Node Npm` Hexo是基于Node构建的，没有的自行安装 推荐最新稳定版. 

安装之后最好修改一下Npm的国内镜像仓库。npm默认简直是龟速，有梯子的小伙伴当我没说 我这边使用的是淘宝仓库

本文所使用的版本 node:v12.18.1  npm:6.14.5  

#### (二) 安装Hexo

1.安装

打开终端 执行 `npm install hexo-cli -g` 全局安装Hexo.

2.创建博客 

`hexo init blog`    blog就是博客根目录 

`cd blog` 进入博客目录

`npm install` 安装依赖

`hexo server` 或者 `hexo s` 启动服务

不出意外控制台会输出

``` shell
> hexo server
INFO  Start processing
INFO  Hexo is running at http://localhost:4000 . Press Ctrl+C to stop.
```

浏览器打开 [http://localhost:4000](http://localhost:4000) 

#### (三) 选择主题

在blog初始化的时候hexo就默认下载了landscape主题，对比了很多款 我现在正在用的这款是[icarus](https://github.com/ppoffice/hexo-theme-icarus) 也可以到hexo的主题列表里面自己选择https://hexo.io/themes/ 

下载 

进入博客根目录

`cd blog`

`git clone https://github.com/ppoffice/hexo-theme-icarus.git themes/icarus`

下载完成之后需要修改配置在blog文件夹下 `_config.yml`文件 所有配置基本都在这里 找到`theme: landscape` 修改为`theme: icarus` 重启服务即可.



##### 常见问题

修改了主题之后执行 hexo server可能会有如下报错 这是因为icarus主题所需的依赖没有安装.

```shell
❯ hexo server
INFO  =======================================
 ██╗ ██████╗ █████╗ ██████╗ ██╗   ██╗███████╗
 ██║██╔════╝██╔══██╗██╔══██╗██║   ██║██╔════╝
 ██║██║     ███████║██████╔╝██║   ██║███████╗
 ██║██║     ██╔══██║██╔══██╗██║   ██║╚════██║
 ██║╚██████╗██║  ██║██║  ██║╚██████╔╝███████║
 ╚═╝ ╚═════╝╚═╝  ╚═╝╚═╝  ╚═╝ ╚═════╝ ╚══════╝
=============================================
INFO  === Checking package dependencies ===
ERROR Package bulma-stylus is not installed.
ERROR Package hexo-component-inferno is not installed.
ERROR Package hexo-renderer-inferno is not installed.
ERROR Package inferno is not installed.
ERROR Package inferno-create-element is not installed.
ERROR Please install the missing dependencies your Hexo site root directory:
ERROR npm install --save bulma-stylus@0.8.0 hexo-component-inferno@^0.4.0 hexo-renderer-inferno@^0.1.3 inferno@^7.3.3 inferno-create-element@^7.3.3
ERROR or:
ERROR yarn add bulma-stylus@0.8.0 hexo-component-inferno@^0.4.0 hexo-renderer-inferno@^0.1.3 inferno@^7.3.3 inferno-create-element@^7.3.3
```

按照提示安装依赖

```shell
npm install --save bulma-stylus@0.8.0 hexo-component-inferno@^0.4.0 hexo-renderer-inferno@^0.1.3 inferno@^7.3.3 inferno-create-element@^7.3.3
```

再次启动服务就ok了



#### (四) 开始写作

hexo默认是使用的markdown，执行`hexo new 文章名`  默认会在./source/_posts/ 文件夹下创建 文章名.md文件 同理 也可以把自己已经写好的.md文件放到这个目录下  然后执行 `hexo g   && hexo s`  打开浏览器就能看到编译好的html了

```
❯ hexo new test
Inferno is in development mode.
INFO  =======================================
 ██╗ ██████╗ █████╗ ██████╗ ██╗   ██╗███████╗
 ██║██╔════╝██╔══██╗██╔══██╗██║   ██║██╔════╝
 ██║██║     ███████║██████╔╝██║   ██║███████╗
 ██║██║     ██╔══██║██╔══██╗██║   ██║╚════██║
 ██║╚██████╗██║  ██║██║  ██║╚██████╔╝███████║
 ╚═╝ ╚═════╝╚═╝  ╚═╝╚═╝  ╚═╝ ╚═════╝ ╚══════╝
=============================================
INFO  === Checking package dependencies ===
INFO  === Checking the configuration file ===
INFO  === Registering Hexo extensions ===
INFO  Created: ~/JoeSpace/Blogs/joejay/source/_posts/test.md
```



#### (五) hexo常用命令

```shell
❯ hexo help
Commands:
  clean     Remove generated files and cache.
  config    Get or set configurations.
  deploy    Deploy your website.
  generate  Generate static files.
  help      Get help on a command.
  init      Create a new Hexo folder.
  list      List the information of the site
  migrate   Migrate your site from other system to Hexo.
  new       Create a new post.
  publish   Moves a draft post from _drafts to _posts folder.
  render    Render files with renderer plugins.
  server    Start the server.
  version   Display version information.
```

经常使用到的就是 clean new server deploy 具体用法可以参考官方文档http://hexo.io/docs/ 官网也有中文



