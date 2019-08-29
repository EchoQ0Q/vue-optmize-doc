# 四.SEO优化方案

## 什么是服务端渲染?
服务端渲染是指 Vue 在客户端将标签渲染成的整个 html 片段的工作在服务端完成，服务端形成的 html 片段直接返回给客户端这个过程就叫做服务端渲染。
- 优点
    - 客户端渲染不利于 SEO 搜索引擎优化
    - 服务端渲染是可以被爬虫抓取到的，客户端异步渲染是很难被爬虫抓取到的
    - SSR直接将HTML字符串传递给浏览器。大大加快了首屏加载时间。
- 缺点
    - 更多的服务器负载：SSR占用更多的CPU和内存资源
    - 更多的开发条件限制：需要处于Node.js server运行环境，一些常用的浏览器API可能无法正常使用，在vue中只支持beforeCreate和created两个生命周期钩子函数。

因此如果我们的应用以SEO和首屏渲染为关键指标，那么项目就需要服务端渲染来帮助实现最佳效果，我们可以采用`Vue SSR`方案，目前有成熟的解决方案[Nuxt.js](https://nuxtjs.org/)。但是如果我们只需要改善项目中的营销页面（例如 `/`, `/about`, `/contact` 等）的SEO，那我们只需要采用预渲染方案。

## VUE预渲染插件

我们可以使用[prerender-spa-plugin](https://github.com/chrisvfritz/prerender-spa-plugin)来轻松的添加预渲染，他已经被Vue应用程序广泛测试使用。`prerender-spa-plugin`的原理是使用了`puppeteer`无头浏览器，来模拟真实的浏览器使用场景，它可以抓取单页应用`SPA`执行并渲染出`html`静态资源。

具体使用方法如下：
 - 1. 在vue.config.js中引入插件，并初始化配置

 ```javascript
const PrerenderSPAPlugin = require('prerender-spa-plugin');
const Renderer = PrerenderSPAPlugin.PuppeteerRenderer;
 ```

 这里简单说明最常用的配置参数

 ```javascript
module.exports = {
    configureWebpack: {
        plugins: [
            new PrerenderSPAPlugin({
                // 必传 - webpack编译应用程序的输出路径
                staticDir: path.join(__dirname, 'dist'),
                // 必传 - 需预渲染的路由
                routes: [ '/status' ],
                // 可选 - 编译后的预渲染页面输出路径(默认为staticDir路径)
                outputDir: path.join(__dirname, 'prerendered'),
                //可选 - 页面渲染配置
                renderer: new Renderer({
                    // 注入全局对象的属性名
                    injectProperty: '__PRERENDER_INJECTED',
                    // 全局对象属性
                    inject: {
                      foo: 'bar'
                    },
                    // 设定预渲染等待时间
                    renderAfterTime: 5000,
                    // 等待渲染，直到在document触发指定的事件，
                    // 我们可以这样触发事件: document.dispatchEvent(new Event('custom-render-trigger'))
                    renderAfterDocumentEvent: 'render-event',
                }),
                // 可选 - 压缩预渲染页面
                minify: {
                    collapseBooleanAttributes: true,
                    collapseWhitespace: true,
                    decodeEntities: true,
                    keepClosingSlash: true,
                    sortAttributes: true
                }
            })
        ]
    }
}
 ```

配置完成之后，每次build打包之后，都会把配置中的路由页面编译为html。

::: warning Vue.js Notes
如果在使用Vue.js进行异步预渲染时遇到问题，可尝试将`data-server-rendered =“true”`属性添加到根应用元素中。 这将导致Vue将当前的页面视为已经呈现的应用程序去更新它而不是完全重新渲染整个树, 也可以在使用`renderAfterDocumentEvent`预渲染之前使用JavaScript操作DOM。
:::






