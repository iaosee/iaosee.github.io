---
title:  Canvas 核心：概念汇总 — 基础、绘制、坐标
subTitle: canvas-core-01-concept-of-summary

tags: 
  - Canvas
  - 2D
  - 图形开发
categories:
  - Canvas
---


## 基本介绍

Canvas 是一个 HTML5 新增的东西，[MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Canvas_API) 的介绍如下：

> Canvas API 提供了一个通过 `JavaScript` 和 HTML 的 `<canvas>` 元素来绘制图形的方式。它可以用于动画、游戏画面、数据可视化、图片编辑以及实时视频处理等方面。

就是可以使用 `<canvas>` 元素来制作各种炫酷的东西。

[MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Canvas_API) 还有一句介绍如下：

> Canvas API 主要聚焦于 2D 图形。而同样使用 `<canvas>` 元素的 WebGL API 则用于绘制硬件加速的 2D 和 3D 图形。

`<canvas>` 元素可以绘制 2D，还可以绘制 3D，也就是说还可以用来做各种更炫酷的东西。


### **`<canvas>` 元素**

`<canvas>` 元素的使用，就像使用其他元素一样，直接写在 HTML 文档中，当然也可以使用 `JavaScript` 操作 DOM 一样动态创建。

```html
<canvas id="canvas" width="500" height="400">
  Sorry, canvas tag is not supported.
</canvas>
```

```js
const canvas = document.createElement('canvas');
canvas.width = window.innerWidth;
canvas.height = window.innerHeight;
document.body.insertBefore(canvas, document.body.firstChild);
```

`<canvas>` 元素就是一个普通的 HTML 元素，和其他 HTML 标签一样，但是不一样的是 `<canvas>` 元素提供了绘制 2D/3D 图形的接口，也就是提供的绘制 2D/3D 图形的绘图上下文。`<canvas>` 元素默认初始化宽度为 300 像素，高度为 150 像素。元素可以使用 CSS 来定义大小，但在绘制时图像会伸缩以适应它的框架尺寸：如果 CSS 的尺寸与初始画布的比例不一致，就会出现扭曲。

虽然支持 `<canvas>` 元素的浏览器普遍都允许在设置 `<canvas>` 元素的 `width` 和 `height` 属性时使用 `px` 作为后缀，但是并不是 `<canvas>` 规范所接受的。

`<canvas>` 元素实际上有两套尺寸，一个是元素本身的大小，一个是元素绘图表面的大小。当设置元素的 `width`、`height` 属性时，实际上同时修改了该元素本身的大小与绘图表面大小；如果使用 CSS 来设置元素大小，那么只会改变元素本身大小，不会改变绘图表面大小。


**注意的地方：**

- `<canvas> 元素` 默认的大小为 `300x150` 个屏幕像素。
- 通常使用 `width`和 `height` 属性为 `<canvas>` 元素明确规定宽高
- 设置`<canvas>` 元素属性的宽度和高度时，不要使用 `px` 作为后缀，也不要使用使用 CSS 指定宽高
- 使用 CSS 设置 `<canvas>` 元素的大小与通过 `<canvas>` 元素属性的 `width`/`height` 设置大小并不一样


### 绘图上下文

`<canvas> 元素` 本身并不能画图，只是创建并提供了一个画布，并公开了多个**渲染上下文**，需要拿到对应的绘图上下文，染回使用绘图上下文提供的 API 进行绘制，可根据自己的需要获取对应的上下文。`<canvas>` 元素提供了 [`getContext()`](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLCanvasElement/getContext) 方法来获取绘图上下文，该方法接收两个参数，一个**上下文类型**和一个可选的**上下文属性**。

接收的上下文类型与获得的渲染上下文对象：

- `2d` -> `CanvasRenderingContext2D`  获取 2D 绘制上下文，主要用于 2D 图形绘制
- `webgl` -> `WebGLRenderingContext`  获取 WebGL 版本1 (OpenGL ES 2.0) 绘制上下文，主要用于 3D 图形绘制
- `webgl2` -> `WebGL2RenderingContext`  获取 WebGL 版本2 (OpenGL ES 3.0) 绘制上下文，同上
- `bitmaprenderer` -> `ImageBitmapRenderingContext ` 提供将 `canvas` 内容替换为指定 `ImageBitmap` 功能

```js
const canvas = document.getElementById('canvasid');
const context = canvas.getContext('2d');
```

这里通过 `getContext('2d')` 方法获取到的是一个 2D 绘图环境，是一个 `CanvasRenderingContext2D` 的对象实例，主要用于绘制 2D 图形。

当然也可以使用 `CanvasRenderingContext2D` 2D 上下文去绘制 3D 图形，也可使用 `WebGLRenderingContext` 3D 上下文去绘制 2D 图形，这主要取决于绘制的算法和性能的取舍。使用 `CanvasRenderingContext2D` 上下文绘制 3D图形，没有 `GLSL` 着色器语言，需要自己实现大量三维图形算法，同时 2D 上下文绘制 3D 图形性能也会不足；使用 `WebGLRenderingContext` 上下文绘制 2D 图形，就得使用 3D 那套算法去绘制，需要编写 `GLSL` 着色器语言，各种矩阵、投影等，虽性能好，但若只想画一个平面的圆，WebGL 上下文是没有直接画圆的 API 的，使用 `WebGLRenderingContext` 上下文就太繁琐，也大材小用，使用 `2D` 上下文一个`arc()` 就可以搞定了。

这里主要记录 2D 绘制，关于 WebGL 3D 的概念不再展开。

### 绘制


####  2D 绘图 API

通过 `getContext('2d')` 获取到的 [`CanvasRenderingContext2D`](https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D) 对象，就可以调用该对象上的各种 API 方法来绘制各类图形。

每个 API 的使用参考文档： https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D


<iframe src="https://codesandbox.io/embed/intelligent-lake-kqvw8?fontsize=14&hidenavigation=1&module=%2Fsrc%2Fdemo%2FDemo.01.ts&theme=dark"
  style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
  title="intelligent-lake-kqvw8"
  allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
  sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
></iframe>


#### 




