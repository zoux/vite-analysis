# vite-analysis

## vite 运行原理

### es module

vite 直接利用了 `ES module`，因此可以直接执行模块；在运行时中，借助浏览器解析模块引用关系来实现按需加载。

相比 webpack 的优势：
1. 去掉打包步骤：
webpack 需要将应用所有模块一起打包形成 bundle 后才能执行。
2. 更高效的按需加载：
webpack 在运行时中更新模块时，不管模块当前是否需要执行，都需要重新构建进 bundle。而 vite 只在需要某个模块的时候动态(借助 `import()`)的引入它，而不需要提前打包。


### vite 如何处理 ESM

在浏览器里使用 ES module 就是使用 http 请求拿到模块，vite 提供 web server 代理这些模块。
通过 AST 解析 import 的资源，若为绝对路径的视为 npm 模块(如 `import { createApp } from 'vue'`)，并改写资源路径(如 `import { createApp } from '/@modules/vue'`)，
最后将获取到的模块内容返回给浏览器。


### vite 如何处理 SFC

通过将 `template/style` 写入当前请求的 vue 文件的模块引用来触发 http 请求，来实现将一个 SFC 变成多个请求。

对于 `style` vite 使用 `updateStyle` 处理。

> updateStyle：通过创建 style 元素，设置了 innerHtml 为 css 内容。


### vite 热更新的实现
    
vite 和通用的热更新实现方式一样：
1. server 通过 watcher 监听文件改动，来触发编译资源。
2. server 通过 WebSocket 通知 client 文件改动。
3. client 请求新的模块内容，执行 rerender/reload。


## 参考资料

[vite —— 一种新的、更快地 web 开发工具](https://mp.weixin.qq.com/s/xdg1NIZYdNQgbunVUGh38w)
