toc: true
title: Hexo+Icarus3+live2d给博客添加看板娘
tags: 
  - hexo
  - icarus
  - live2d
  - 看板娘
---

#### 补坑

之前写过一篇icarus添加看板娘的教程但是版本是<Icarus3的  Icarus3改版很大，完全使用了jsx来代替了ejs，不过添加看板娘不管是jsx还是ejs差别都不大
icarus3之前的教程博客 [传送门](https://blog.joecoding.cn/2020-06-29/(%E4%B8%89)%20%E7%BB%99%E4%BD%A0%E7%9A%84%E5%8D%9A%E5%AE%A2%E6%B7%BB%E5%8A%A0%E7%9C%8B%E6%9D%BF%E5%A8%98(Live2D)HEXO+icarus/)

上一篇博客那时候拉的live2D还需要导入jQuery 2020年1月1日起，项目不再依赖于 jQuery。



这次我把live2d直接放到了主题文件夹下的source下面 跟js/css/img同级

#### 下载live2D

进入博客根目录

`cd theme/icarus/source && git clone https://github.com/stevenjoezhang/live2d-widget.git`

<!--more-->

#### 修改配置

##### 1. 导入css依赖

找到`theme/icarus/layout/common/head.jsx` 插入css依赖

大概是在一百四十多行的样子吧  或者可以在head.jsx内搜索`<link>`标签 然后插入这行

```jsx
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/font-awesome/css/font-awesome.min.css"/>
```

修改后完整的head.jsx

```jsx
const { Component } = require('inferno');
const MetaTags = require('hexo-component-inferno/lib/view/misc/meta');
const OpenGraph = require('hexo-component-inferno/lib/view/misc/open_graph');
const StructuredData = require('hexo-component-inferno/lib/view/misc/structured_data');
const Plugins = require('./plugins');

function getPageTitle(page, siteTitle, helper) {
    let title = page.title;

    if (helper.is_archive()) {
        title = helper._p('common.archive', Infinity);
        if (helper.is_month()) {
            title += ': ' + page.year + '/' + page.month;
        } else if (helper.is_year()) {
            title += ': ' + page.year;
        }
    } else if (helper.is_category()) {
        title = helper._p('common.category', 1) + ': ' + page.category;
    } else if (helper.is_tag()) {
        title = helper._p('common.tag', 1) + ': ' + page.tag;
    } else if (helper.is_categories()) {
        title = helper._p('common.category', Infinity);
    } else if (helper.is_tags()) {
        title = helper._p('common.tag', Infinity);
    }

    return [title, siteTitle].filter(str => typeof str !== 'undefined' && str.trim() !== '').join(' - ');
}

module.exports = class extends Component {
    render() {
        const { env, site, config, helper, page } = this.props;
        const { url_for, cdn, fontcdn, iconcdn, is_post } = helper;
        const {
            url,
            meta_generator = true,
            head = {},
            article,
            highlight,
            variant = 'default'
        } = config;
        const {
            meta = [],
            open_graph = {},
            structured_data = {},
            canonical_url = page.permalink,
            rss,
            favicon
        } = head;

        const language = page.lang || page.language || config.language;
        const fontCssUrl = {
            default: fontcdn('Ubuntu:wght@400;600&family=Source+Code+Pro', 'css2'),
            cyberpunk: fontcdn('Oxanium:wght@300;400;600&family=Roboto+Mono', 'css2')
        };

        let hlTheme, images;
        if (highlight && highlight.enable === false) {
            hlTheme = null;
        } else if (article && article.highlight && article.highlight.theme) {
            hlTheme = article.highlight.theme;
        } else {
            hlTheme = 'atom-one-light';
        }

        if (typeof page.og_image === 'string') {
            images = [page.og_image];
        } else if (helper.has_thumbnail(page)) {
            images = [helper.get_thumbnail(page)];
        } else if (article && typeof article.og_image === 'string') {
            images = [article.og_image];
        } else if (page.content && page.content.includes('<img')) {
            let img;
            images = [];
            const imgPattern = /<img [^>]*src=['"]([^'"]+)([^>]*>)/gi;
            while ((img = imgPattern.exec(page.content)) !== null) {
                images.push(img[1]);
            }
        } else {
            images = [url_for('/img/og_image.png')];
        }

        let adsenseClientId = null;
        if (Array.isArray(config.widgets)) {
            const widget = config.widgets.find(widget => widget.type === 'adsense');
            if (widget) {
                adsenseClientId = widget.client_id;
            }
        }

        let openGraphImages = images;
        if ((typeof open_graph === 'object' && open_graph !== null)
            && ((Array.isArray(open_graph.image) && open_graph.image.length > 0) || typeof open_graph.image === 'string')) {
            openGraphImages = open_graph.image;
        } else if ((Array.isArray(page.photos) && page.photos.length > 0) || typeof page.photos === 'string') {
            openGraphImages = page.photos;
        }

        let structuredImages = images;
        if ((typeof structured_data === 'object' && structured_data !== null)
            && ((Array.isArray(structured_data.image) && structured_data.image.length > 0) || typeof structured_data.image === 'string')) {
            structuredImages = structured_data.image;
        } else if ((Array.isArray(page.photos) && page.photos.length > 0) || typeof page.photos === 'string') {
            structuredImages = page.photos;
        }

        return <head>
            <meta charset="utf-8" />
            {meta_generator ? <meta name="generator" content={`Hexo ${env.version}`} /> : null}
            <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1" />
            {meta && meta.length ? <MetaTags meta={meta} /> : null}
            <meta name="baidu-site-verification" content="mhWv4NNMfG" />
            <meta name="google-site-verification" content="aNBCEhnauRjyRi2s55JA4LemtzHTmgcHT43vigw9Qek" />
            <title>{getPageTitle(page, config.title, helper)}</title>

            {typeof open_graph === 'object' && open_graph !== null ? <OpenGraph
                type={open_graph.type || (is_post(page) ? 'article' : 'website')}
                title={open_graph.title || page.title || config.title}
                date={page.date}
                updated={page.updated}
                author={open_graph.author || config.author}
                description={open_graph.description || page.description || page.excerpt || page.content || config.description}
                keywords={page.keywords || (page.tags && page.tags.length ? page.tags : undefined) || config.keywords}
                url={open_graph.url || page.permalink || url}
                images={openGraphImages}
                siteName={open_graph.site_name || config.title}
                language={language}
                twitterId={open_graph.twitter_id}
                twitterCard={open_graph.twitter_card}
                twitterSite={open_graph.twitter_site}
                googlePlus={open_graph.google_plus}
                facebookAdmins={open_graph.fb_admins}
                facebookAppId={open_graph.fb_app_id} /> : null}

            {typeof structured_data === 'object' && structured_data !== null ? <StructuredData
                title={structured_data.title || config.title}
                description={structured_data.description || page.description || page.excerpt || page.content || config.description}
                url={structured_data.url || page.permalink || url}
                author={structured_data.author || config.author}
                date={page.date}
                updated={page.updated}
                images={structuredImages} /> : null}

            {canonical_url ? <link rel="canonical" href={canonical_url} /> : null}
            {rss ? <link rel="alternative" href={url_for(rss)} title={config.title} type="application/atom+xml" /> : null}
            {favicon ? <link rel="icon" href={url_for(favicon)} /> : null}
            <link rel="stylesheet" href={iconcdn()} />
            {hlTheme ? <link rel="stylesheet" href={cdn('highlight.js', '9.12.0', 'styles/' + hlTheme + '.css')} /> : null}
            <link rel="stylesheet" href={fontCssUrl[variant]} />
            <link rel="stylesheet" href={url_for('/css/' + variant + '.css')} />
          	{/* 这行是live2d需要的css依赖 */}
            <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/font-awesome/css/font-awesome.min.css"/>
            <Plugins site={site} config={config} helper={helper} page={page} head={true} />

            {adsenseClientId ? <script data-ad-client={adsenseClientId}
                src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js" async={true}></script> : null}
        </head>;
    }
};

```



##### 2. 修改刚下载的live2d-widget 下的`autoload.js`

注释掉第二行 `//const live2d_path = "https://cdn.jsdelivr.net/gh/stevenjoezhang/live2d-widget@latest/";`

放开第三行 `const live2d_path = "/live2d-widget/";`

修改后的autoload.js

```js
// 注意：live2d_path 参数应使用绝对路径
//const live2d_path = "https://cdn.jsdelivr.net/gh/stevenjoezhang/live2d-widget@latest/";
const live2d_path = "/live2d-widget/";

// 封装异步加载资源的方法
function loadExternalResource(url, type) {
	return new Promise((resolve, reject) => {
		let tag;

		if (type === "css") {
			tag = document.createElement("link");
			tag.rel = "stylesheet";
			tag.href = url;
		}
		else if (type === "js") {
			tag = document.createElement("script");
			tag.src = url;
		}
		if (tag) {
			tag.onload = () => resolve(url);
			tag.onerror = () => reject(url);
			document.head.appendChild(tag);
		}
	});
}

// 加载 waifu.css live2d.min.js waifu-tips.js
if (screen.width >= 768) {
	Promise.all([
		loadExternalResource(live2d_path + "waifu.css", "css"),
		loadExternalResource(live2d_path + "live2d.min.js", "js"),
		loadExternalResource(live2d_path + "waifu-tips.js", "js")
	]).then(() => {
		initWidget({
			waifuPath: live2d_path + "waifu-tips.json",
			//apiPath: "https://live2d.fghrsh.net/api/",
			cdnPath: "https://cdn.jsdelivr.net/gh/fghrsh/live2d_api/"
			//cdnPath: "https://live2d.fghrsh.net/api/"
		});
	});
}
// initWidget 第一个参数为 waifu-tips.json 的路径，第二个参数为 API 地址
// API 后端可自行搭建，参考 https://github.com/fghrsh/live2d_api
// 初始化看板娘会自动加载指定目录下的 waifu-tips.json
```



##### 3. 在主题内导入`autoload.js`

> 前提是 live2d-widget 的位置在`theme/icarus/source`下

找到`theme/icarus/layout/common/scripts.jsx`  在末尾处 `<Fragment>`标签内添加

```jsx
<script src={url_for('/live2d-widget/autoload.js')}></script>
```

添加后完整的scripts.jsx

```jsx
const {Component, Fragment} = require('inferno');
const Plugins = require('./plugins');

module.exports = class extends Component {
    render() {
        const {site, config, helper, page} = this.props;
        const {url_for, cdn} = helper;
        const {external_link, article} = config;
        const language = page.lang || page.language || config.language || 'en';

        let externalLink;
        if (typeof external_link === 'boolean') {
            externalLink = {enable: external_link, exclude: []};
        } else {
            externalLink = {
                enable: typeof external_link.enable === 'boolean' ? external_link.enable : true,
                exclude: external_link.exclude || []
            };
        }

        let fold = 'unfolded';
        let clipboard = true;
        if (article && article.highlight) {
            if (typeof article.highlight.clipboard !== 'undefined') {
                clipboard = !!article.highlight.clipboard;
            }
            if (typeof article.highlight.fold === 'string') {
                fold = article.highlight.fold;
            }
        }

        const embeddedConfig = `var IcarusThemeSettings = {
            site: {
                url: '${config.url}',
                external_link: ${JSON.stringify(externalLink)}
            },
            article: {
                highlight: {
                    clipboard: ${clipboard},
                    fold: '${fold}'
                }
            }
        };`;

        return <Fragment>
            <script src={cdn('jquery', '3.3.1', 'dist/jquery.min.js')}></script>
            <script src={cdn('moment', '2.22.2', 'min/moment-with-locales.min.js')}></script>
            <script dangerouslySetInnerHTML={{__html: `moment.locale("${language}");`}}></script>
            <script dangerouslySetInnerHTML={{__html: embeddedConfig}}></script>
            {clipboard ? <script src={cdn('clipboard', '2.0.4', 'dist/clipboard.min.js')} defer={true}></script> : null}
            <Plugins site={site} config={config} page={page} helper={helper} head={false}/>
            <script src={url_for('/js/main.js')} defer={true}></script>
            <script src={url_for('/live2d-widget/autoload.js')}></script>
        </Fragment>;
    }
};

```

##### 4. 开启live2d

编辑主题配置文件` _config.yml`  添加

```yaml
live2d:
  enable: true
```

大功告成！

#### 备注

看板娘到这儿应该就可以出来了 但是会发现在icarus的样式下面 这时候需要把看板娘给置顶

找到live2d-widget 下的`waifu.css ` 修改33行 id为`#waifu`的样式 把z-index:1 修改为z-index:1000；

修改后的waifu.css

```css
#waifu-toggle {
	background-color: #fa0;
	border-radius: 5px;
	bottom: 66px;
	color: #fff;
	cursor: pointer;
	font-size: 12px;
	left: 0;
	margin-left: -100px;
	padding: 5px 2px 5px 5px;
	position: fixed;
	transition: margin-left 1s;
	width: 60px;
	writing-mode: vertical-rl;
}

#waifu-toggle.waifu-toggle-active {
	margin-left: -50px;
}

#waifu-toggle.waifu-toggle-active:hover {
	margin-left: -30px;
}

#waifu {
	bottom: -1000px;
	left: 0;
	line-height: 0;
	margin-bottom: -10px;
	position: fixed;
	transform: translateY(3px);
	transition: transform .3s ease-in-out, bottom 3s ease-in-out;
	z-index: 1000;
}

#waifu:hover {
	transform: translateY(0);
}

#waifu-tips {
	animation: shake 50s ease-in-out 5s infinite;
	background-color: rgba(236, 217, 188, .5);
	border: 1px solid rgba(224, 186, 140, .62);
	border-radius: 12px;
	box-shadow: 0 3px 15px 2px rgba(191, 158, 118, .2);
	font-size: 14px;
	line-height: 24px;
	margin: -30px 20px;
	min-height: 70px;
	opacity: 0;
	overflow: hidden;
	padding: 5px 10px;
	position: absolute;
	text-overflow: ellipsis;
	transition: opacity 1s;
	width: 250px;
	word-break: break-all;
}

#waifu-tips.waifu-tips-active {
	opacity: 1;
	transition: opacity .2s;
}

#waifu-tips span {
	color: #0099cc;
}

#waifu #live2d {
	cursor: grab;
	height: 280px;
	position: relative;
	width: 280px;
}

#waifu #live2d:active {
	cursor: grabbing;
}

#waifu-tool {
	color: #aaa;
	opacity: 0;
	position: absolute;
	right: -10px;
	top: 70px;
	transition: opacity 1s;
}

#waifu:hover #waifu-tool {
	opacity: 1;
}

#waifu-tool span {
	color: #7b8c9d;
	cursor: pointer;
	display: block;
	line-height: 30px;
	text-align: center;
	transition: color .3s;
}

#waifu-tool span:hover {
	color: #0684bd; /* #34495e */
}

@keyframes shake {
	2% {
		transform: translate(.5px, -1.5px) rotate(-.5deg);
	}

	4% {
		transform: translate(.5px, 1.5px) rotate(1.5deg);
	}

	6% {
		transform: translate(1.5px, 1.5px) rotate(1.5deg);
	}

	8% {
		transform: translate(2.5px, 1.5px) rotate(.5deg);
	}

	10% {
		transform: translate(.5px, 2.5px) rotate(.5deg);
	}

	12% {
		transform: translate(1.5px, 1.5px) rotate(.5deg);
	}

	14% {
		transform: translate(.5px, .5px) rotate(.5deg);
	}

	16% {
		transform: translate(-1.5px, -.5px) rotate(1.5deg);
	}

	18% {
		transform: translate(.5px, .5px) rotate(1.5deg);
	}

	20% {
		transform: translate(2.5px, 2.5px) rotate(1.5deg);
	}

	22% {
		transform: translate(.5px, -1.5px) rotate(1.5deg);
	}

	24% {
		transform: translate(-1.5px, 1.5px) rotate(-.5deg);
	}

	26% {
		transform: translate(1.5px, .5px) rotate(1.5deg);
	}

	28% {
		transform: translate(-.5px, -.5px) rotate(-.5deg);
	}

	30% {
		transform: translate(1.5px, -.5px) rotate(-.5deg);
	}

	32% {
		transform: translate(2.5px, -1.5px) rotate(1.5deg);
	}

	34% {
		transform: translate(2.5px, 2.5px) rotate(-.5deg);
	}

	36% {
		transform: translate(.5px, -1.5px) rotate(.5deg);
	}

	38% {
		transform: translate(2.5px, -.5px) rotate(-.5deg);
	}

	40% {
		transform: translate(-.5px, 2.5px) rotate(.5deg);
	}

	42% {
		transform: translate(-1.5px, 2.5px) rotate(.5deg);
	}

	44% {
		transform: translate(-1.5px, 1.5px) rotate(.5deg);
	}

	46% {
		transform: translate(1.5px, -.5px) rotate(-.5deg);
	}

	48% {
		transform: translate(2.5px, -.5px) rotate(.5deg);
	}

	50% {
		transform: translate(-1.5px, 1.5px) rotate(.5deg);
	}

	52% {
		transform: translate(-.5px, 1.5px) rotate(.5deg);
	}

	54% {
		transform: translate(-1.5px, 1.5px) rotate(.5deg);
	}

	56% {
		transform: translate(.5px, 2.5px) rotate(1.5deg);
	}

	58% {
		transform: translate(2.5px, 2.5px) rotate(.5deg);
	}

	60% {
		transform: translate(2.5px, -1.5px) rotate(1.5deg);
	}

	62% {
		transform: translate(-1.5px, .5px) rotate(1.5deg);
	}

	64% {
		transform: translate(-1.5px, 1.5px) rotate(1.5deg);
	}

	66% {
		transform: translate(.5px, 2.5px) rotate(1.5deg);
	}

	68% {
		transform: translate(2.5px, -1.5px) rotate(1.5deg);
	}

	70% {
		transform: translate(2.5px, 2.5px) rotate(.5deg);
	}

	72% {
		transform: translate(-.5px, -1.5px) rotate(1.5deg);
	}

	74% {
		transform: translate(-1.5px, 2.5px) rotate(1.5deg);
	}

	76% {
		transform: translate(-1.5px, 2.5px) rotate(1.5deg);
	}

	78% {
		transform: translate(-1.5px, 2.5px) rotate(.5deg);
	}

	80% {
		transform: translate(-1.5px, .5px) rotate(-.5deg);
	}

	82% {
		transform: translate(-1.5px, .5px) rotate(-.5deg);
	}

	84% {
		transform: translate(-.5px, .5px) rotate(1.5deg);
	}

	86% {
		transform: translate(2.5px, 1.5px) rotate(.5deg);
	}

	88% {
		transform: translate(-1.5px, .5px) rotate(1.5deg);
	}

	90% {
		transform: translate(-1.5px, -.5px) rotate(-.5deg);
	}

	92% {
		transform: translate(-1.5px, -1.5px) rotate(1.5deg);
	}

	94% {
		transform: translate(.5px, .5px) rotate(-.5deg);
	}

	96% {
		transform: translate(2.5px, -.5px) rotate(-.5deg);
	}

	98% {
		transform: translate(-1.5px, -1.5px) rotate(-.5deg);
	}

	0%, 100% {
		transform: translate(0, 0) rotate(0);
	}
}

```



