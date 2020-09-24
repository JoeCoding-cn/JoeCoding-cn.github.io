categories:
  - [vue]
tags: 
  - Windows
  - npm
  - vue
  - sass
---

#### 背景

刚换过主机装好环境 git clone下vue项目 照常执行npm install  然后就看到报错信息如下

``` 
npm ERR! code ELIFECYCLE
npm ERR! errno 1
npm ERR! node-sass@4.13.1 postinstall: `node scripts/build.js`
npm ERR! Exit status 1
npm ERR!
npm ERR! Failed at the node-sass@4.13.1 postinstall script.
npm ERR! This is probably not a problem with npm. There is likely additional logging output above.

npm ERR! A complete log of this run can be found in:
npm ERR!     C:\Users\Joe\AppData\Roaming\npm-cache\_logs\2020-09-18T06_32_51_442Z-debug.log
```

起初以为是安装sass没开代理的原因. 设置了淘宝镜像之后还是报错 ，然后往上翻  看到了这一段日志
<!--more-->
```
gyp verb check python checking for Python executable "python2" in the PATH
gyp verb `which` failed Error: not found: python2
gyp verb `which` failed     at getNotFoundError (D:\workSpace\e-c\grb\grb4vp\node_modules\which\which.js:13:12)
gyp verb `which` failed     at F (D:\workSpace\e-c\grb\grb4vp\node_modules\which\which.js:68:19)
gyp verb `which` failed     at E (D:\workSpace\e-c\grb\grb4vp\node_modules\which\which.js:80:29)
gyp verb `which` failed     at D:\workSpace\e-c\grb\grb4vp\node_modules\which\which.js:89:16
gyp verb `which` failed     at D:\workSpace\e-c\grb\grb4vp\node_modules\isexe\index.js:42:5
gyp verb `which` failed     at D:\workSpace\e-c\grb\grb4vp\node_modules\isexe\windows.js:36:5
gyp verb `which` failed     at FSReqCallback.oncomplete (fs.js:176:21)
gyp verb `which` failed  python2 Error: not found: python2
gyp verb `which` failed     at getNotFoundError (D:\workSpace\e-c\grb\grb4vp\node_modules\which\which.js:13:12)
gyp verb `which` failed     at F (D:\workSpace\e-c\grb\grb4vp\node_modules\which\which.js:68:19)
gyp verb `which` failed     at E (D:\workSpace\e-c\grb\grb4vp\node_modules\which\which.js:80:29)
gyp verb `which` failed     at D:\workSpace\e-c\grb\grb4vp\node_modules\which\which.js:89:16
gyp verb `which` failed     at D:\workSpace\e-c\grb\grb4vp\node_modules\isexe\index.js:42:5
gyp verb `which` failed     at D:\workSpace\e-c\grb\grb4vp\node_modules\isexe\windows.js:36:5
gyp verb `which` failed     at FSReqCallback.oncomplete (fs.js:176:21) {
gyp verb `which` failed   code: 'ENOENT'
gyp verb `which` failed }
gyp verb check python checking for Python executable "python" in the PATH
```

一查才知道是没有python支持 



#### 解决方案

打开`PowerShell` 以管理员身份运行 执行以下操作

```shell
 npm install --global --production windows-build-tools --registry=https://registry.npm.taobao.org
```

切记使用管理员身份运行`PowerShell`  不然会报错 Please restart this script from an administrative PowerShell!

