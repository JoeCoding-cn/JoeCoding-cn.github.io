toc: true

tags: 
  - hexo
  - icarus
  - live2d
  - 看板娘
---

### 版本
icarus < 3.0

## HEXO+icarus主题 给博客添加看板娘(Live2D)

> 最近在很多博客左下角或者右下角有看到小人(看板娘)，折腾了一番搜到的都没有达到预期的效果，并且大多数教程都是基于Next来做的，本人使用的主题是[Icarus](https://github.com/ppoffice/hexo-theme-icarus)，跟Next有细微的区别。后面找到了大神的作品，本次教程基于Hexo+Icarus添加的看板娘(Live2D)  参考博客  [张书樵](https://zhangshuqiao.org/2018-07/%E5%9C%A8%E7%BD%91%E9%A1%B5%E4%B8%AD%E6%B7%BB%E5%8A%A0Live2D%E7%9C%8B%E6%9D%BF%E5%A8%98/)      [梆子井欢喜坨](https://blog.csdn.net/qq_39610915/article/details/90679768)  [潘高](https://www.jianshu.com/p/89440678ee3c)


> Icarus3请移步  [传送门]([https://blog.joecoding.cn/2020-08-03/Hexo+Icarus3+live2d%E7%BB%99%E5%8D%9A%E5%AE%A2%E6%B7%BB%E5%8A%A0%E7%9C%8B%E6%9D%BF%E5%A8%98/](http://localhost:4000/2020-08-03/Hexo+Icarus3+live2d给博客添加看板娘/))

<!--more-->

### 效果展示

[![GIF.gif](https://joe-1253912574.cos.ap-shenzhen-fsi.myqcloud.com/images/GIF%20(1).gif)](https://joe-1253912574.cos.ap-shenzhen-fsi.myqcloud.com/images/GIF%20(1).gif)

### 教程

	1、把大神已修改好的代码托管在Github [源码](https://github.com/stevenjoezhang/live2d-widget)  下载到hexo目录下的`/themes/icarus/source`下下载好的文件名`live2d-widget` 
	
	2、进入`live2d-widget` 修改`autoload.js` 

``` js
// 把这个替换掉
const live2d_path = "https://cdn.jsdelivr.net/gh/stevenjoezhang/live2d-widget/";
// 替换为
const live2d_path = "/live2d-widget/";
```

这个`live2d_path`是指刚才下载的源码存放的目录，在`icarus`中就会以`hexo/theme/icarus/source` 为根目录 ，刚才下载的源码放在这个位置所以这里的`live2d_path` 就是源码存放的位置

	3、**重点**  不加会没有效果

> 在`/themes/icarus/layout` 中编辑 `layout.ejs`  加入以下依赖到`<head></head>`内 再次申明博主锁使用的是`icarus` 主题 如果是`Next` 主题则对应的文件是 `/themes/next/layout/_layout.swig`

``` html
 <script src="https://cdn.jsdelivr.net/npm/jquery/dist/jquery.min.js"></script>
 <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/font-awesome/css/font-awesome.min.css"/>
 <script src="/live2d-widget/autoload.js"></script>
```

	4、编辑主题的配置文件`_config.yml` 

``` yaml
live2d:
  enable: true
```

	保存之后执行 `hexo clean && hexo g && hexo s` 查看效果

### 大功告成 

想修改看板娘大小、位置、格式、文本内容等，可查看并修改`live2d-widget` 下的 > `waifu-tips.js` 、 `waifu-tips.json`  、 `waifu.css`

live2D模型地址：
https://github.com/summerscar/live2dDemo

live2D部分模型预览:
https://huaji8.top/post/live2d-plugin-2.0/