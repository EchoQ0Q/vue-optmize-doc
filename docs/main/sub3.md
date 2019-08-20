# 三.用户体验
## 1).app-skeleton
为了减少白屏时间，在页面完全渲染完成之前提升用户感知体验，可以通过插件[vue-skeleton-webpack-plugin](https://github.com/lavas-project/vue-skeleton-webpack-plugin)为单页/多页应用生成骨架屏`skeleton`来优化用户体验。  

>   VUE骨架屏的原理是: 在项目编译之前，将骨架屏插件编译成`html`输出到项目`index.html`内的`#app`节点内，在当页面完全渲染完成之后，VUE即会实时替换`#app`内的内容。
我们可以为项目MPA(多页面应用)配置骨架屏，也可为SPA(单页面应用)的多路由配置骨架屏，具体方法可参考官方文档。

以在`vue-cli3`中使用骨架屏为例，具体做法如下：

-  1. 在vue.config.js中引入插件，并初始化配置
```javascript
const path = require('path');
const SkeletonWebpackPlugin = require('vue-skeleton-webpack-plugin');

module.exports = {
  configureWebpack: {
    plugins: [
      new SkeletonWebpackPlugin({
        webpackConfig: {
          entry: {
            app: path.join(__dirname, './src/skeleton.js'),
          },
        },
        minimize: true,
        quiet: true,
      }),
    ],
  },
}
```

-  2. 创建skeleton组件
```javascript
<template>
  <div class="skeleton-wrapper">
    <section class="skeleton-block">
        骨架屏
    </section>
  </div>
</template>

<script>
export default {
  name: 'Skeleton',
};
</script>
```

-  3. 创建skeleton入口文件
```javascript
import Vue from 'vue';
import Skeleton from './Skeleton.vue';

export default new Vue({
  components: {
    Skeleton,
  },
  render: h => h(Skeleton),
});
```

## 2).app-shell
## 3).pwa manifest serviceWorker
