# 四.SEO优化方案
### 1).vue的预渲染插件
```bash
npm install prerender-spa-plugin 
```

缺陷数据不够动态，可以使用ssr服务端渲染
```javascript
const PrerenderSPAPlugin = require('prerender-spa-plugin');
const path = require('path');
module.exports = {
    configureWebpack: {
        plugins: [
            new PrerenderSPAPlugin({
                staticDir: path.join(__dirname, 'dist'),
                routes: [ '/', '/about',],
            })
        ]
    }
  }
```
### 2).什么是服务端渲染?
概念：放在浏览器进行就是浏览器渲染,放在服务器进行就是服务器渲染。
- 客户端渲染不利于 SEO 搜索引擎优化
- 服务端渲染是可以被爬虫抓取到的，客户端异步渲染是很难被爬虫抓取到的
- SSR直接将HTML字符串传递给浏览器。大大加快了首屏加载时间。
- SSR占用更多的CPU和内存资源
- 一些常用的浏览器API可能无法正常使用
- 在vue中只支持beforeCreate和created两个生命周期

![](https://www.fullstackjavascript.cn/ssr.png)

> 服务端渲染 SSR => NuxtJS



