toc: true
categories:
  - Hexo
  - Icarus
tags: 
  - hexo
  - icarus
  - 博客
  - 国际化
---
#### 作者资料卡

你可以启用作者资料卡挂件来展示文章作者/网站站长的信息。 资料卡的配置如下所示：

编辑icarus下`_config.yml`

<!--more-->
```shell
widgets:
    # Profile widget configurations
    -
        # Where should the widget be placed, left sidebar or right sidebar
        position: left
        type: profile
        # 作者名称
        author: Joe
        # 作者头衔
        # author_title: java-coding
        ## 作者所在地
        location: 中国·深圳
        # 头像图片地址
        avatar: https://joe-1253912574.cos.ap-shenzhen-fsi.myqcloud.com/images/IMG_1292(20200629-005627).jpg
        # 是否显示圆形头像
        avatar_rounded: true
        # Gravatar邮箱(如不设置`avatar`项)
        gravatar: joe-code@foxmail.com
        # 关注按钮链接地址
        follow_link: 'https://gitee.com/joejay'
        # 社交媒体链接

        social_links:
            Github:
                icon: fab fa-github
                url: 'https://github.com/ppoffice'
```

这个头像我设置之后也没生效... 一气之下直接写死了。在目录`themes/icarus/layout/widget`下有个profile.jsx  找到38行编辑图片src地址

```
<img class={'avatar' + (avatarRounded ? ' is-rounded' : '')} src='你的头像地址' alt={author} />
```



#### 文章目录

编辑icarus下`_config.yml` 默认开启

```
widgets:
    -
        type: toc
        position: left #展示位置 左或右
```

在需要显示目录的文章的.md开头插入

```
toc: true
---

文章内容...
```



#### 友链

编辑icarus下`_config.yml` 默认开启

```
widgets:
    -
        position: left
        type: links
        # 友站名称与链接
        links:
            LinkedBear: 'https://juejin.im/user/5d9c4a7b518825427b27645f'
            BugStack: 'https://bugstack.cn'
```



#### 最新文章

编辑icarus下`_config.yml` 默认开启

```
widgets:
    -
        position: right
        type: recent_posts
```



#### 文章归档

编辑icarus下`_config.yml` 默认开启

```
widgets:
    -
        position: right
        type: archives
```



#### 文章分类

编辑icarus下`_config.yml` 默认开启

```
widgets:
    -
        position: right
        type: categories
```



#### 文章标签

编辑icarus下`_config.yml` 默认开启

```
widgets:
    -
        position: right
        type: tags
```

在需要显示目录的文章的.md开头插入

```
toc: true
tags: 
  - hexo
  - icarus
  - 博客
---
博客内容
```



#### 国际化

hexo支持国际化, icarus主题自带了多个语言

进入到blog目录下 `/themes/icarus/languages`

![](https://joe-1253912574.cos.ap-shenzhen-fsi.myqcloud.com/images/Snipaste_2020-06-29_11-39-28.png)

可以看到这里已经内置了中文简体

接下来修改配置文件`_config.yml`  需要注意的是 `_config.yml` 有两个 一个是hexo的  一个是icarus主题的 icarus的配置文件在 `/themes/icarus/_config.yml`  

我们需要修改的是hexo的_config.yml 千万不能搞错了 修改languages为 zh-CN 繁体就是zh-TW咯上图文件夹下有的都可以配置 或者你也可以自定义国际化,随便打开一个yml照着改就行了

![](https://joe-1253912574.cos.ap-shenzhen-fsi.myqcloud.com/images/Snipaste_2020-06-29_11-49-28.png)