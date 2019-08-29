# 六.缓存，压缩

## 浏览器缓存

浏览器缓存(Browser Cache)，是浏览器为了节省网络带宽、加快网站访问速度而推出的一项功能。浏览器缓存的运行机制是这样的：

- 用户使用浏览器第一次访问某网站页面，该页面上引入了各种各样的静态资源（js/css/图片/字体等），浏览器会把这些静态资源，甚至是页面本身(html文件)，都一一储存到本地。
- 用户在后续的访问中，如果需要再次请求同样的静态资源（根据 url 进行匹配），且静态资源没有过期（服务器端有一系列判别资源是否过期的策略，比如`Cache-Control`、`Pragma`、`ETag`、`Expires`、`Last-Modified`），则直接使用前面本地储存的资源，而不需要重复请求。

而我们项目都使用`webpack`来构建网站静态资源，因此需要利用`webpack`来操控静态资源的文件名，来优化浏览器缓存。

在`webpack`中，我们主要用到`[hash]`和`[chunkhash]`这两个变量：
- 用`[hash]`的话，每次使用 `webpack` 构建代码， `hash` 字符串都会更新，因此相当于强制刷新浏览器缓存。
- 用`[chunkhash]`的话，当`chunk` 的内容发生改变，该 `chunk`所对应生成出来的文件的文件名也会发生改变，因此浏览器缓存便能得以继续利用。

## 服务端gzip压缩

gzip（GNUzip）的缩写，最早用于 UNIX 系统的文件压缩。HTTP 协议上的 gzip 编码是一种用来改进 web 应用程序性能的技术，web 服务器和客户端（浏览器）必须共同支持 gzip。目前主流的浏览器Chrome，Firefox，IE等都支持该协议。常见的服务器如 Apache，Nginx，IIS 同样支持，gzip 压缩效率非常高，通常可以达到 70% 的压缩率，也就是说，如果你的网页有 30K，压缩之后就变成了 9K 左右。

目前,我们项目服务大都都开启了gzip，node服务端以express为例，开启步骤如下:

- 安装相关依赖
```javascript
npm install compression --save
```

- 项目内使用
```javascript
var compression = require('compression');
var app = express();
app.use(compression())
```