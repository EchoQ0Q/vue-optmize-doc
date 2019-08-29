# 五.webpack打包优化

##  第三方插件按需导入

我们在项目中经常需要引入第三方插件，如果我们直接引入整个插件，会导致体积太大，因此可借助[babel-plugin-import](https://github.com/ant-design/babel-plugin-import)只引入需要的组件。
以下为项目引入iview为例,具体做法如下:

(1) 首先安装`babel-plugin-import`:

```javascripit
npm install babel-plugin-import -D
```

(2) 然后在.babelrc文件添加

```javascript
{
  plugins: [["import", {
    "libraryName": "iview",
    "libraryDirectory": "src/components"
  }]]
}
```

(3) 最后在main.js中可以按需引入组件

```javascript
import { Button, Table } from 'iview';
Vue.component('Button', Button);
Vue.component('Table', Table);
```

## 引用CDN第三方模块
使用cdn的方式加载第三方模块,设置externals
```javascript
externals: {
  'vue': 'Vue',
  'vue-router': 'VueRouter',
  'vuex': 'Vuex',
}
<script src="https://cdn.bootcss.com/vue-router/3.1.2/vue-router.min.js"></script>
<script src="https://cdn.bootcss.com/vue/2.6.10/vue.min.js"></script>
<script src="https://cdn.bootcss.com/vuex/3.1.1/vuex.min.js"></script>
```
## 多线程打包 happypack

由于运行在 `Node.js` 之上的 `Webpack` 是单线程模型的，所以`Webpack` 需要处理的事情需要一件一件的做，不能多件事一起做。
我们需要`Webpack` 能同一时间处理多个任务，发挥多核 `CPU` 的威力，`HappyPack` 就能让 `Webpack` 做到这点，它把任务分解给多个子进程去并发的执行，子进程处理完后再把结果发送给主进程。

具体做法如下：

- 1. 安装插件
```javascript
npm install happypack --save-dev
```

- 2. 修改vue.config.js
```javascript
const HappyPack = require('happypack')
const os = require('os')
const happyThreadPool = HappyPack.ThreadPool({ size: os.cpus().length })
module.exports = {
  configureWebpack: {
    plugins: [
      new HappyPack({
        id: 'happy-babel-js',
        loaders: ['babel-loader?cacheDirectory=true'],
        threadPool: happyThreadPool,
      })
    ]
  }
}
```

- 3. 替换vue.config.js中处理js文件的loader

```javascript
module.exports = {
  chainWebpack: config => {
    const jsRule = config.module.rule('js')

    // 清除已有的所有 loader。
    // 如果你不这样做，接下来的 loader 会附加在该规则现有的 loader 之后。
    jsRule.uses.clear()

    // 添加要替换的 loader
    jsRule
      .use('happypack/loader?id=happy-babel-js')
        .loader('happypack/loader?id=happy-babel-js')
  }
}
```

## splitChunks 抽离公共文件

`webpack4`废弃了原来的`CommonsChunkPlugin`插件，采用了0配置的方式使用`splitChunks`插件进行代码块分割，`splitChunks`是一个可选的用于建立一个独立文件(又称作 chunk)的功能，这个文件包括多个入口 chunk 的公共模块。简单来说`splitChunks`主要是用来提取第三方库和公共模块，避免首屏加载的bundle文件或者按需加载的bundle文件体积过大，从而导致加载时间过长。

```javascript

module.exports = {
  chainWebpack: {
    optimization: {
      splitChunks: {
          chunks: 'all',
          cacheGroups: {
              vendors: {
                  test: /react/,
                  name: 'vendors'
              }
          }
      }
    }
  }
}
```

默认基础配置如下：

  - chunks: 表示哪些代码需要优化，有三个可选值：initial(初始块)、async(按需加载块)、all(全部块)，默认为async
  - minSize: 表示在压缩前的最小模块大小，默认为30000
  - minChunks: 表示被引用次数，默认为1
  - maxAsyncRequests: 按需加载时候最大的并行请求数，默认为5
  - maxInitialRequests: 一个入口最大的并行请求数，默认为3
  - automaticNameDelimiter: 命名连接符
  - name: 拆分出来块的名字，默认由块名和hash值自动生成
  - cacheGroups: 缓存组。缓存组的属性除上面所有属性外，还有test, priority, reuseExistingChunk
    - test: 用于控制哪些模块被这个缓存组匹配到
    - priority: 缓存组打包的先后优先级
    - reuseExistingChunk: 如果当前代码块包含的模块已经有了，就不在产生一个新的代码块
