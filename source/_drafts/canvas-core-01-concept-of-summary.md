---
title:  Canvas 核心：重点概念汇总 — 基础、绘制、坐标
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

就是可以使用 `<canvas>` 元素提供的画布来制作各种炫酷的东西。

[MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Canvas_API) 还有一句介绍如下：

> Canvas API 主要聚焦于 2D 图形。而同样使用 `<canvas>` 元素的 WebGL API 则用于绘制硬件加速的 2D 和 3D 图形。

`<canvas>` 元素提供的画布可以绘制 2D，还可以绘制 3D，也就是说还可以用来做各种更炫酷的东西。


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

**注意的地方：**

`<canvas>` 元素就是一个普通的 HTML 元素，和其他 HTML 标签一样，但是不一样的是 `<canvas>` 元素提供了绘制 2D/3D 图形的接口，也就是提供的绘制 2D/3D 图形的绘图上下文。`<canvas>` 元素默认初始化宽度为 300 像素，高度为 150 像素。元素可以使用 CSS 来定义大小，但在绘制时图像会伸缩以适应它的框架尺寸：如果 CSS 的尺寸与初始画布尺寸的比例不一致，画出的图形就会出现扭曲。

虽然支持 `<canvas>` 元素的浏览器普遍都允许在设置 `<canvas>` 元素的 `width` 和 `height` 属性时使用 `px` 作为后缀，但是并不是 `<canvas>` 规范所接受的。

`<canvas>` 元素实际上有两套尺寸，一个是元素本身的大小，一个是元素绘图表面的大小。当设置元素的 `width`、`height` 属性时，实际上同时修改了该元素本身的大小与绘图表面大小；如果使用 CSS 来设置元素大小，那么只会改变元素本身大小，不会改变绘图表面大小。若这两个尺寸大小不一样，即使比例一致，绘制出来的图像也可能会模糊。还有在高清屏下不做处理绘制出来的图形也会模糊。

- `<canvas> 元素` 默认的大小为 `300x150` 个屏幕像素。
- 通常使用 `width`和 `height` 属性为 `<canvas>` 元素明确规定宽高
- 设置`<canvas>` 元素的 `width`和 `height` 属性时，不要使用 `px` 作为后缀，
- 使用 CSS 设置 `<canvas>` 元素的大小与通过 `<canvas>` 元素属性的 `width`/`height` 设置大小并不一样
- 若使用 CSS 指定了宽高，要让 `width`、`height` 属性 与 CSS指定的 `width`、`height` 比例一致


### 渲染上下文

`<canvas> 元素` 本身并不能画图，只是创建并提供了一个画布，并公开了多个**渲染上下文**，需要拿到对应的绘图上下文，染回使用绘图上下文提供的 API 进行绘制，可根据自己的需要获取对应的上下文。`<canvas>` 元素提供了 [`getContext()`](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLCanvasElement/getContext) 方法来获取绘图上下文，该方法接收两个参数，一个**上下文类型**和一个可选的**上下文属性**。

接收的**上下文类型**与获得的**渲染上下文对象**：

- `2d` -> `CanvasRenderingContext2D`  获取 2D 绘制上下文，主要用于 2D 图形绘制
- `webgl` -> `WebGLRenderingContext`  获取 WebGL 版本1 (OpenGL ES 2.0) 绘制上下文，主要用于 3D 图形绘制
- `webgl2` -> `WebGL2RenderingContext`  获取 WebGL 版本2 (OpenGL ES 3.0) 绘制上下文，同上
- `bitmaprenderer` -> `ImageBitmapRenderingContext ` 提供将 `canvas` 内容替换为指定 `ImageBitmap` 功能

```js
const canvas = document.getElementById('canvasid');
const context = canvas.getContext('2d');
```

这里通过 `getContext('2d')` 方法获取到的是一个 2D 绘图环境，是一个 `CanvasRenderingContext2D` 的对象实例，主要用于绘制 2D 图形。

并不是 `CanvasRenderingContext2D` 上下文对象只能绘制 2D 图形，`WebGLRenderingContext` 上下文对象只能绘制 3D，当然也可以使用 `CanvasRenderingContext2D` 上下文去绘制 3D 图形，也可使用 `WebGLRenderingContext` 上下文去绘制 2D 图形，这主要取决于绘制的算法和性能的取舍，只是不同的绘制上下文做自己适合的事情，不管是 2D/3D 最终都是绘制到二维平面上的，将三维坐标投影映射到二维平面，所以 3D 只是一种视觉效果而已。使用 `CanvasRenderingContext2D` 上下文绘制 3D图形，没有 `GLSL` 着色器语言，需要自己实现大量三维图形算法，同时 2D 上下文绘制 3D 图形性能也会不足；使用 `WebGLRenderingContext` 上下文绘制 2D 图形，就得使用 3D 那套算法去绘制，需要编写 `GLSL` 着色器语言，各种矩阵、投影等，虽性能好，但若只想画一个平面的圆，WebGL 上下文是没有直接画圆的 API 的，使用 `WebGLRenderingContext` 上下文就太繁琐，也大材小用，使用 `2D` 上下文一个`arc()` 就可以搞定了。

这里主要记录 2D 绘制，关于 WebGL 3D 的概念不再展开。

## 绘制


###  2D 绘图 API

通过 `getContext('2d')` 获取到的 [`CanvasRenderingContext2D`](https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D) 对象，就可以调用该对象上的各种 API 方法来绘制各类图形，Canvas API 中支出绘制的基本图形: 线段、矩形、圆弧、贝塞尔曲线等，可以根据这些基本图形组合成任意需要的图形。。

这里每个 API 的调用细节就不详细记录了，API 的使用参考文档： https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D

### 高清屏绘制模糊问题

在高清屏下，画出的图形就会出现模糊。这是因为逻辑像素与物理像素不一致导致的。

**逻辑像素** 也叫设备独立像素，是一种虚拟像素或者抽象像素，一般我们在程序中使用的 `10px`、`20px` 这就是逻辑像素，是一种相对单位，我们平常使用 CSS 写的也属于逻辑像素。可以通过 `screen.width` 和 `screen.height` 获取逻辑像素宽高。

**物理像素** 也就是设备像素，是设备显示器上屏幕实际拥有的像素点，在 CSS 中写的逻辑像素最终会被转换为物理像素上显示。同样尺寸的非高清与高清屏，在高清屏中物理像素会越多，像素密度越密集。

 **设备像素比**  —— `Device Pixel Ratio (DPR)`  是物理像素与逻辑像素之间的比例，逻辑像素到物理像素如何转换，就是根据**设备像素比** 进行转换，在非高清屏下这个 `dpr` 的值通常是 1，而在高清品下这个值通常是大于 1 的。比如非高清屏下 `dpr` 为 1，那么 CSS 写 `1px` 那么实际设备实际显示也是 1 个物理像素。而在高清屏下，由于高清屏通常屏幕尺寸一致，但是物理像素密度增加，若高清屏 `1px` 按照 `dpr` 为 1 计算，那么在高清屏下 `1px` 就会看起来很小看不清，所以在高清屏下一般 `dpr` 都会增大， 如果 `dpr` 的值为 2，那么 CSS `1px` 就会被换算为 `(dpr)^2 * 1dp` 也就是等于 4 个设备物理像素，这样 CSS 中的 `1px` 在高分辨率中显示的大小就和低分辨率差不多，看起来就不会太小。

在浏览器中可以通过 `window.devicePixelRatio` 获取到设备像素比 `dpr`, 使用逻辑像素 `screen.width * dpr`、`screen.height * dpr` 就能获取到设备的物理像素。

我们平常设置宽度是根据逻辑像素来设置，而最终在呈现显示的时候会根据 `dpr` 放大数倍，因为在高清屏下，设备像素比比较大，屏幕上显示的像素点由 1 个变为多个，若下直接使用 `<canvas>` 元素的 `width`、`height` 属性指定大小，那么 `<canvas>` 元素本身的大小和元素绘图表面的大小一样了，Canvas 画布并非矢量图形，而是位图模式，而绘图区域大小被渲染呈现到 `dpr` 倍的高清屏就会被放大，图像被放大，因此绘制出来的图形被放大了会变模糊。

要使 `Canvas` 适配高清屏，需要将 Canvas 绘图表面放大到设备像素比后再绘制，在绘制的时候同样坐标也需要放大到 `dpr` 倍，若不想每次计算坐标，则需要将 Canvas 绘图区域缩放 `dpr` 倍，可以调用 `context.scale(dpr, dpr)` 缩放画布。

**解决方案：**

- [High DPI Canvas](https://www.html5rocks.com/en/tutorials/canvas/hidpi/)
- [hidpi-canvas-polyfill](https://github.com/jondavidjohn/hidpi-canvas-polyfill)
- [高清屏中绘制模糊](https://www.html.cn/demo/canvas_retina/index.html)

**主要代码：**

``` js
// 适配高清屏 主要代码
const dpr = window.devicePixelRatio || 1;
const boundingRect = canvas.getBoundingClientRect();
const width = boundingRect.width;
const height = boundingRect.height;

if ( dpr > 1 )  {
  canvas.style.width = width + 'px';
  canvas.style.height = height + 'px';
  canvas.width = width * dpr;
  canvas.height = height * dpr;
  context.scale(dpr, dpr);
}
```


### 状态的保存和恢复

**立即模式** 和 **保留模式** 绘图系统

`Canvas` 是采用 **立即模式** 的绘图形式, 意思就是它会立即将指定的内容绘制到 canvas 上, 然后就会忘记刚才绘制的内容，意味着 Canvas 中不会包含将要绘制的图像列表。

`SVG` 则是基于 DOM 的 **保留模式** 绘图系统， `SVG` 中会维护一份所绘制图形对象的列表。

**立即模式** 绘图系统不维护所绘制的图形队形列表, **保留模式** 绘图系统会维护所绘制的图形对象列表. **立即模式** 相对 **保留模式** 来说，是一种更加底层的绘图模式，立即模式更为灵活。

立即模式 适合制作 "绘画应用程序", 保留模式 适合制作 "画图应用程序".

在进行绘图操作的时候，通常需要频繁的设置 `context` 的属性值，但是有时候只想临时改变这些属性，用完恢复之前的状态，设置了 `context` 属性又设置回去，那代码就会很多重复也不美观。幸运的是 `context` 提供了两个 `save()` 和 `restore()` 的 API。可以认为它是绘制状态的存储管理器，以栈的形式维持着绘制状态。

场景：比如 `context` 已经设置了很多状态属性了，需要临时改变部分属性绘制另一个图形，绘制完后需要恢复刚才的状态在绘制。

这就要在开始做临时属性改变之前调用 `save()` 完成临时绘制之后调用 `restore()` 就可以恢复到上一次调用 `save()` 之前的状态了。`save()` 与 `restore()` 方法可以嵌套使用，每次调用 `save()` 方法会将当前的绘图环境压入栈顶，调用 `restore()` 方法则会从栈顶弹出上次绘图环境，恢复到上一次调用 `save()` 方法之前的状态。


```js 
// 初始绘制状态
context.lineCap = 'round';
context.lineWidth = 0.5;
context.fillStyle = 'red';
context.strokeStyle = 'red';
context.shadowOffsetX = 2;
context.shadowOffsetY = 2;
context.shadowBlur = 4;
// ... 绘制图形

// 临时改变属性  改变三个属性其他不变
context.save();
context.lineWidth = 2;
context.fillStyle = 'blue';
context.strokeStyle = 'blue';
// 绘制临时图形

// 恢复到上一次调用 save() 之前的状态
context.restore();
```

### 坐标系统

Canvas 的坐标系统默认左上角为原点， X 轴坐标向右延伸， Y 轴坐标向下延伸。但是 Canvas 的坐标系统不是固定的，可以根据需要进行改变坐标系统：包括 **平移** **旋转** **缩放** **自定义变换**。


### Canvas 绘制模型

要更好的使用 Canvas 进行绘制，需要对 Canvas 绘制模型有一个很好的理解，包括 Canvas 究竟是如何绘制 图形、图像与文本的。 理解这些就需要理解 **阴影**、**alpha 通道**、**裁剪区域** 以及 **图像合成**等内容。

在 Canvas 上绘制图形或图像时，浏览器会按照以下步骤进行操作：

1. 将图形或图像绘制到一个无限大的透明位图中，绘制使用当前绘制环境样式，包括填充、描边、以及线条样式
2. 将图形或图像的阴影绘制到另一幅位图中，绘制时使用当前绘制环境的阴影样式
3. 将阴影中每个像素的 `alpha` 分量乘以绘图环境对象的 `globalAlpha` 属性值
4. 将绘有阴影的位图与经过剪辑区域剪切过的 `canvas` 进行图像合成，使用当前合成模式参数
5. 将图形或图像每个颜色像素分量，乘以绘图环境对象的 `globalAlpha` 属性值
6. 将绘有图形或图形的位图，合成到当前经过剪辑区域裁剪过的 `canvas` 位图上，使用当前合成参数

只有在启用阴影效果时才会执行 2 ~ 4 的步骤。







<iframe src="https://codesandbox.io/embed/intelligent-lake-kqvw8?fontsize=14&hidenavigation=1&module=%2Fsrc%2FDemo.01.ts&theme=dark"
  style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
  title="intelligent-lake-kqvw8"
  allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
  sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
></iframe>

