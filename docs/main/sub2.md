# 二.vue加载性能优化

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

##  图片懒加载  

对于图片过多的页面，为了加速页面加载速度，所以很多时候我们需要将页面内未出现在可视区域内的图片先不做加载， 等到滚动到可视区域后再去加载。这样对于页面加载性能上会有很大的提升，也提高了用户体验。可使用[vue-lazyload](https://github.com/hilongjw/vue-lazyload) 插件，具体做法如下：

(1) 首先安装`vue-lazyload`:

```javascripit
npm i vue-lazyload -S
```

(2) 然后在main.js中引入，并初始化配置

```javascript
import VueLazyload from 'vue-lazyload'

Vue.use(VueLazyload)

// or with options
Vue.use(VueLazyload, {
  preLoad: 1.3,
  error: 'dist/error.png',
  loading: 'dist/loading.gif',
  attempt: 1
})
```

(3) 最后在模板中使用插件

```javascript
<ul>
  <li v-for="img in list">
    <img v-lazy="img.src" >
  </li>
</ul>
```

##  优化无限列表性能

如果应用存在非常长或者无限滚动的列表，那么需要采用 `窗口化` 的技术来优化性能，只需要渲染少部分区域的内容，减少重新渲染组件和创建 dom 节点的时间。 可以参考开源项目[vue-virtual-scroll-list](https://github.com/tangbc/vue-virtual-scroll-list)  来优化这种无限列表的场景。
具体做法如下：

(1) 首先安装`vue-virtual-scroll-list`:

```javascripit
npm i vue-virtual-scroll-list -S
```

(2) 然后在main.js中引入

```javascript
import virtualList from 'vue-virtual-scroll-list'
Vue.component('virtual-list', virtualList)
```

(3) 最后在模板中使用插件

```javascript
<template>
    <div>
        <virtual-list :size="18" :remain="5">
            <div v-for="item of items" :key="item.id">{{ item.id }}</div>
        </virtual-list>
    </div>
</template>
<script>
export default {
    data() {
        return {
            items: [
                { id: 1 },
                { id: 2 },
                { id: 3 },
                ...
            ]
        }
    }
}
</script>
```








