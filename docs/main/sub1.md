# 一.编码优化
### 1).data属性  
不要将所有的数据都放在data中，data中的数据都会增加getter和setter，会收集对应的watcher
```javascript
export function defineReactive (
  obj: Object,
  key: string,
  val: any,
  customSetter?: ?Function,
  shallow?: boolean
) {
  const dep = new Dep()
  let childOb = !shallow && observe(val)
  Object.defineProperty(obj, key, {
    get: function reactiveGetter () {
      const value = getter ? getter.call(obj) : val
      if (Dep.target) {
        dep.depend() // 收集依赖
        if (childOb) {
          childOb.dep.depend()
          if (Array.isArray(value)) {
            dependArray(value)
          }
        }
      }
      return value
    },
    set: function reactiveSetter (newVal) {
      const value = getter ? getter.call(obj) : val
      if (setter) {
        setter.call(obj, newVal)
      } else {
        val = newVal
      }
      childOb = !shallow && observe(newVal)
      dep.notify() // 执行watcher的update
    }
  })
}
```

### 2).SPA页面采用keep-alive缓存组件
keep-alive可以实现组件的缓存功能，缓存当前组件的实例
```javascript
render () {
    const slot = this.$slots.default // 获取默认插槽
    const vnode: VNode = getFirstComponentChild(slot)
    const componentOptions: ?VNodeComponentOptions = vnode && vnode.componentOptions
    if (componentOptions) {
      // check pattern
      const name: ?string = getComponentName(componentOptions)
      const { include, exclude } = this
      if ( // 匹配 include / exclude
        // not included
        (include && (!name || !matches(include, name))) ||
        // excluded
        (exclude && name && matches(exclude, name))
      ) {
        return vnode
      }

      const { cache, keys } = this
      const key: ?string = vnode.key == null
        // same constructor may get registered as different local components
        // so cid alone is not enough (#3269)
        ? componentOptions.Ctor.cid + (componentOptions.tag ? `::${componentOptions.tag}` : '')
        : vnode.key
      if (cache[key]) { // 如果有缓存 直接将缓存返回
        vnode.componentInstance = cache[key].componentInstance
        // make current key freshest
        remove(keys, key)
        keys.push(key)
      } else {
        cache[key] = vnode // 缓存下来下次用
        keys.push(key)
        // 超过缓存限制 就删除
        if (this.max && keys.length > parseInt(this.max)) {
          pruneCacheEntry(cache, keys[0], keys, this._vnode)
        }
      }

      vnode.data.keepAlive = true
    }
    return vnode || (slot && slot[0])
  }
```
  
### 3).拆分组件 
  - 提高复用性、增加代码的可维护性
  - 减少不必要的渲染 (尽可能细化拆分组件)

### 4).v-if 
当值为false时内部指令不会执行,具有阻断功能，很多情况下使用v-if替代v-show

### 5).key保证唯一性 
- 默认vue会采用就地复用策略
- 如果数据项的顺序被改变，Vue不会移动DOM元素来匹配数据项的顺序
- 应该用数据的id作为key属性

### 6).Object.freeze
vue会实现数据劫持，给没个属性增加getter和setter，可以使用freeze冻结数据
```javascript
Object.freeze([{ value: 1 },{ value: 2 }]);
```
在数据劫持时属性不能被配置，不会重新定义
```javascript
const property = Object.getOwnPropertyDescriptor(obj, key)
if (property && property.configurable === false) {
return
}
```

### 7).路由懒加载、异步组件
动态加载组件，依赖webpack-codespliting功能
```javascript
const router = new VueRouter({
  routes: [
    { path: '/foo', component: () => import(/* webpackChunkName: "group-foo" */ './Foo.vue') }
    { path: '/bar', component: () => import(/* webpackChunkName: "group-foo" */ './Bar.vue') }
  ]
})
```
动态导入组件
```javascript
import Dialog from "./Dialog";
export default {
  components: {
    Dialog: () => import("./Dialog")
  }
};
```
### 8).runtime运行时
在开发时尽量采用单文件的方式.vue 在webpack打包时会进行模板的转化

### 9).数据持久化的问题 
- vuex-persist 合理使用 (防抖、节流)