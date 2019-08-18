# 五.webpack打包优化
- 使用cdn的方式加载第三方模块,设置externals 
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
- 多线程打包 happypack
- splitChunks 抽离公共文件
- sourceMap的配置 

> webpack-bundle-analyzer 分析打包的插件
