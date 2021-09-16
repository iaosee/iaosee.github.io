---
title: Canvas 基础概念 — 基础、绘制、坐标
name: canvas-core-01-concept-of-summary
keywords: 'Canvas, 2D 图形, Canvas 绘制, Canvas 概念'

image: https://z3.ax1x.com/2020/12/23/r63cPx.png
date: 2020-11-22 20:46:40
categories:
  - Canvas
tags:
  - 2D
  - Canvas
  - 图形开发
mathjax: true
---


## 基本概念介绍

Canvas 是一个 HTML5 新增的东西，[MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Canvas_API) 的介绍如下：

> Canvas API 提供了一个通过 `JavaScript` 和 HTML 的 `<canvas>` 元素来绘制图形的方式。它可以用于动画、游戏画面、数据可视化、图片编辑以及实时视频处理等方面。

就是可以使用 `<canvas>` 元素提供的画布来制作各种炫酷的东西。

[MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Canvas_API) 还有一句介绍如下：

> Canvas API 主要聚焦于 2D 图形。而同样使用 `<canvas>` 元素的 WebGL API 则用于绘制硬件加速的 2D 和 3D 图形。

`<canvas>` 元素提供的画布可以绘制 2D，还可以绘制 3D，也就是说还可以用来做各种更炫酷的东西。

### Canvas 元素

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

`<canvas>` 元素就是一个普通的 HTML 元素，和其他 HTML 标签一样，但是不一样的是 `<canvas>` 元素提供了绘制 2D/3D 图形的接口，也就是提供了绘制 2D/3D 图形的绘图上下文。`<canvas>` 元素默认初始化宽度为 300 像素，高度为 150 像素。元素可以使用 CSS 来定义大小，但在绘制时图像会伸缩以适应它的框架尺寸：如果 CSS 的尺寸与初始画布尺寸的比例不一致，画出的图形就会出现扭曲。

虽然支持 `<canvas>` 元素的浏览器普遍都允许在设置 `<canvas>` 元素的 `width` 和 `height` 属性时使用 `px` 作为后缀，但是并不是 `<canvas>` 规范所接受的。

`<canvas>` 元素实际上有两套尺寸，一个是元素本身的大小，一个是元素绘图表面的大小。当设置元素的 `width`、`height` 属性时，实际上同时修改了该元素本身的大小与绘图表面大小；如果使用 CSS 来设置元素大小，那么只会改变元素本身大小，不会改变绘图表面大小。若这两个尺寸大小不一样，即使比例一致，绘制出来的图像也可能会模糊。还有在高清屏下不做处理绘制出来的图形也会模糊。

- `<canvas> 元素` 默认的大小为`300x150` 个屏幕像素。
- 通常使用 `width` 和 `height` 属性为 `<canvas>` 元素明确规定宽高
- 设置`<canvas>` 元素的 `width` 和 `height` 属性时，不要使用 `px` 作为后缀，
- 使用 CSS 设置 `<canvas>` 元素的大小与通过 `<canvas>` 元素属性的 `width` / `height` 设置大小并不一样
- 若使用 CSS 指定了宽高，要让 `width`、`height` 属性 与 CSS指定的 `width`、`height` 比例一致

### 渲染上下文

`<canvas> 元素` 本身并不能画图，只是创建并提供了一个画布，并公开了多个**渲染上下文**，需要拿到对应的绘图上下文，染回使用绘图上下文提供的 API 进行绘制，可根据自己的需要获取对应的上下文。`<canvas>` 元素提供了 [`getContext()`](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLCanvasElement/getContext) 方法来获取绘图上下文，该方法接收两个参数，一个**上下文类型**和一个可选的**上下文属性**。

接收的**上下文类型**与获得的**渲染上下文对象**：

- `2d` ->`CanvasRenderingContext2D`  获取 2D 绘制上下文，主要用于 2D 图形绘制
- `webgl` ->`WebGLRenderingContext`  获取 WebGL 版本1 (OpenGL ES 2.0) 绘制上下文，主要用于 3D 图形绘制
- `webgl2` ->`WebGL2RenderingContext`  获取 WebGL 版本2 (OpenGL ES 3.0) 绘制上下文，同上
- `bitmaprenderer` ->`ImageBitmapRenderingContext ` 提供将`canvas` 内容替换为指定`ImageBitmap` 功能

```js
const canvas = document.getElementById('canvasid');
const context = canvas.getContext('2d');
```

这里通过 `getContext('2d')` 方法获取到的是一个 2D 绘图环境，是一个 `CanvasRenderingContext2D` 的对象实例，主要用于绘制 2D 图形。

并不是 `CanvasRenderingContext2D` 上下文对象只能绘制 2D 图形，`WebGLRenderingContext` 上下文对象只能绘制 3D，当然也可以使用 `CanvasRenderingContext2D` 上下文去绘制 3D 图形，也可使用 `WebGLRenderingContext` 上下文去绘制 2D 图形，这主要取决于绘制的算法和性能的取舍，只是不同的绘制上下文做自己适合的事情，不管是 2D/3D 最终都是绘制到二维平面上的，将三维坐标投影映射到二维平面，所以 3D 只是一种视觉效果而已。使用 `CanvasRenderingContext2D` 上下文绘制 3D图形，没有 `GLSL` 着色器语言，需要自己实现大量三维图形算法，同时 2D 上下文绘制 3D 图形性能也会不足；使用 `WebGLRenderingContext` 上下文绘制 2D 图形，就得使用 3D 那套算法去绘制，需要编写 `GLSL` 着色器语言，各种矩阵、投影等，虽性能好，但若只想画一个平面的圆，WebGL 上下文是没有直接画圆的 API 的，使用 `WebGLRenderingContext` 上下文就太繁琐，也大材小用，使用 `2D` 上下文一个 `arc()` 就可以搞定了。

这里主要记录 2D 绘制，关于 WebGL 3D 的概念就不再展开。

## 绘制要点

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


**适配高清屏主要代码：**

```js
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

### Canvas 绘制模型

要更好的使用 Canvas 进行绘制，需要对 Canvas 绘制模型有一个很好的理解，包括 Canvas 究竟是如何绘制 图形、图像与文本的。 理解这些就需要理解 **阴影**、**alpha 通道**、**裁剪区域** 以及 **图像合成**等内容。

在 Canvas 上绘制图形或图像时，浏览器会按照以下步骤进行操作：

1. 将图形或图像绘制到一个无限大的透明位图中，绘制使用当前绘制环境样式，包括填充、描边、以及线条样式
2. 将图形或图像的阴影绘制到另一幅位图中，绘制时使用当前绘制环境的阴影样式
3. 将阴影中每个像素的`alpha` 分量乘以绘图环境对象的`globalAlpha` 属性值
4. 将绘有阴影的位图与经过剪辑区域剪切过的`canvas` 进行图像合成，使用当前合成模式参数
5. 将图形或图像每个颜色像素分量，乘以绘图环境对象的`globalAlpha` 属性值
6. 将绘有图形或图形的位图，合成到当前经过剪辑区域裁剪过的`canvas` 位图上，使用当前合成参数

只有在启用阴影效果时才会执行 2 ~ 4 的步骤。

### CanvasRenderingContext2D 绘图 API

通过 `getContext('2d')` 获取到的 [`CanvasRenderingContext2D`](https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D) 对象，就可以调用该对象上的各种 API 方法来绘制各类图形，Canvas API 中支出绘制的基本图形: 线段、矩形、圆弧、贝塞尔曲线等，可以根据这些基本图形组合成任意需要的图形。

这里更多 API 详细的调用细节就不详细记录了，API 的使用参考文档： https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D

### 坐标系统

Canvas 的坐标系统默认在画布左上角为原点， X 轴坐标向右延伸， Y 轴坐标向下延伸。但是 Canvas 的坐标系统不是固定的，可以根据需要进行改变坐标系统：包括 **平移** **旋转** **缩放** **自定义变换**。

简单的说，对坐标进行变换，就是移动 Canvas 坐标的原点，有时候改变坐标的原点，可以极大的简化图形的绘制与操作时所需的坐标计算。

比如可以要在 Canvas 中心画一个矩形：

```js
const rect_width = 100;
const rect_height = 100;
context.strokeRect(
  canvas.width / 2 - rect_width / 2, 
  canvas.height / 2 - rect_height / 2, 
  rect_width, 
  rect_height
);
```

这段代码在绘制时候计算出矩形的坐标，如果将坐标原点移动到这个位置，就可以简化 `strokeRect()` 方法的调用，

```js
const rect_width = 100;
const rect_height = 100;
context.translate(
  canvas.width / 2 - rect_width / 2,
  canvas.height / 2 - rect_height / 2
);
context.strokeRect(
  0,
  0,
  rect_width, 
  rect_height
);
```

从代码中可以看出来，很显然，这并没有简化代码，还是需要计算原点的坐标，后面段代码唯一的差别就是将坐标传给了 `translate()` 方法，不过在当要绘制很多复杂图形的时候，那么移动原点坐标就可以很大的简化接下来在绘制其他图形的计算了。

#### 坐标系统变换

Canvas 2D API 提供了如下方法来变换坐标系统：

- `rotate(radians)` 按给定的角度选择坐标系，这里是弧度 （`π` 弧度 = 180 角度）
- `scale(x, y)` 在 `X` 和`Y` 轴上分别按照给定的数值来缩放坐标系
- `translate(x, y)` 将坐标系原点平移到给定的坐标处

**坐标变换好处：** 有时在编码过程中对绘坐标变换很有用，对坐标系统的变换可以让我们更方便的控制绘制，比如当你绘制一个较小的图形，可以在绘制之前调用 `scale(2, 2)` 将当前绘制放大 2 倍，这样更容易观察，完成编码时将这句去掉。同样有时也可能调用 `translate()` 方法来移动坐标控制视窗显示的图形。

**镜像绘制：** 坐标系的变换可以实现很多不同的效果，比如在绘制了某个图形之后，可以通过调用 `scale(-1, 1)` 制作该图形的水平镜像，调用 `scale(1, -1)` 制作该图形的垂直镜像，这样就可以省略掉镜像图形的坐标计算，很方便的绘制出对称图形。

#### 自定义坐标变换

上面的 `rotate()` `scale()` `translate()` 这三个方法只是提供了一种简便的方式去操作绘制环境的变换矩阵，然后在绘制的物体上运用变换矩阵，默认情况变换矩阵为 `单位矩阵`，单位矩阵并不会影响绘制，当调用了这三个方法之一后变换矩阵就被修改了，从而影响后面的绘制。

通常情况下，只用这三个方法就已经足够了，不过有时也可能需要自己直接操作变换矩阵。

Canvas 2D API 提供了两个可以直接操作变换矩阵的方法：

- `transform()` 在当前变换矩阵之上叠加运用到另一个变换效果
- `setTransform()` 重置当前变换矩阵（置位单位矩阵），然后再单位矩阵上运用指定的变换


这两个方法不同的是：多次调用 `transform()` 方法造成的变换效果的叠加累计的，而每次调用 `setTransform()` 方法会将上一次的变换效果彻底清除。

直接使用 `transform()` `setTransform()` 方法操作变换矩阵好处：

- 可以做出更复杂的变换，比如 “错切”，无法通过三个基础方法达到的效果
- 只需要调用一次`transform()` 或`setTransform()` 就可以同时应用 `缩放` `旋转` `平移` 等多种效果

`transform()`/ `setTransform()` 均接收 6 个参数。

**计算平移后的新坐标：** 新旧坐标横向距离差记为 `dx`， 新旧坐标纵向距离差记为 `dy`

$$
\begin{align}
x' = x + dx \\
y' = y + dy
\end{align}
$$

**计算缩放后的新坐标：** 横向缩放倍数记为 `sx`， 纵向缩放倍数记为 `sy`

$$
\begin{align}
x' = x * sx \\
y' = y * sy
\end{align}
$$

**计算旋转后的新坐标：**

$$
\begin{align}
x' = x * cos(angle) - y * sin(angle) \\
y' = y * cos(angle) + x * sin(angle)
\end{align}
$$

`transform/setTransform` 两个方法的参数是相同的，分别来自下面坐标的计算公式，从上面的几个基本计算公式可得到如下坐标变换通用计算公式，`a ~ f` 分别表示方法中的 6 个参数：

- `transform(a, b, c, d, e, f, g)` 
- `setTransform(a, b, c, d, e, f, g)` 

**变换矩阵：**

$$
\left[
\begin{array}{ccc}
a & c & e \\ 
b & d & f \\ 
0 & 0 & 1 \\
\end{array} 
\right]
$$


**坐标变换通用计算公式：**

$$
\begin{align}
x' = ax + cy + e \\
y' = bx + dy + f
\end{align}
$$

- `a` x 轴水平缩放
- `b` y 轴垂直倾斜
- `c` x 轴水平倾斜
- `d` y 轴垂直缩放
- `e` x 轴水平偏移
- `f` y 轴垂直偏移


这样一个公式就可以同时设置平移、缩放、旋转了，当然也可以通过这个公式设置其中一种变换。

甚至可以利用 `b` `c` 参数设置不同方向上的偏移，达到错切效果，这种效果是三个基础方法不能实现的。

**根据通用公式平移坐标**，可以设置： `a = 1` `b = 0` `c = 0` `d = 1` `e` 为 x 轴偏移，`f` 为 y 轴偏移，带入公式得到：

$$
\begin{align}
x' &= 1x + 0y + e \\
   &= x + e \\
y' &= 0x + 1y + f \\
   &= y + f \\
\end{align}
$$

**根据通用公式缩放坐标**，`a` 为 x 轴缩放倍数，`d` 为 y 轴缩放倍数，其他参数置位 0 带入公式得：

$$
\begin{align}
x' &= ax + 0y + 0 \\
   &= ax \\
y' &= 0x + dy + 0 \\
   &= dy \\
\end{align}
$$

**根据通用公式旋转坐标**，讲坐标系绕原点旋转旋转一定弧度，`a = cos(angle)` `b = sin(angle)` `c = -sin(angle)` `d = cos(angle)` `e = 0` `f = 0` 带入公式得：

$$
\begin{align}
x' &= \cos(angle)x - \sin(angle)y + 0 \\
y' &= \sin(angle)x + \cos(angle)y + 0 \\
\end{align}
$$

如下代码使用 `transform()`方法实现将坐标系旋转 45 度，`45° = π/4 弧度`，这样画来的矩形就顺时针旋转了 45 度。

``` js 
// 将坐标系旋转 45 度
// ctx.rotate(Math.PI / 4); 等同于如下
ctx.transform(
  Math.cos(Math.PI / 4), 
  Math.sin(Math.PI / 4), 
  -Math.sin(Math.PI / 4), 
  Math.cos(Math.PI / 4),
  0,
  0
);
ctx.fillRect(0,0,200,100);
```



----


<!-- 
公式测试：

$$
\begin{align}

(\vec{a} + \vec{b}) \cdot \vec{c} 
&= |\vec{c}|Prj_{\vec{c}}(\vec{a} + \vec{b}) \\
&= |\vec{c}|(Prj_{\vec{c}}\vec{a} 
+ Prj_{\vec{c}}\vec{b}) \\

&= |\vec{c}|Prj_{\vec{c}}\vec{a} 
 + |\vec{c}|Prj_{\vec{c}}\vec{b} \\
&= \vec{a} \cdot \vec{c} + \vec{b} \cdot \vec{c} 

\end{align}
$$

$$
\begin{align}
\vec{a} = \{a_1, b_1, c_1\} 
\\
\vec{b} = \{b_1, b_2, b_3\}
\end{align}
$$


$$
\begin{split} 
\vec{a} = a_1\vec{i} + a_2\vec{j} + a_3\vec{j} 
\\
\vec{b} = b_1\vec{i} + b_2\vec{j} + b_3\vec{k} 
\end{split}
$$


$$
\cos\varphi = 
\frac{\vec{a} \cdot \vec{b}}{|\vec{a}||\vec{b}|}
= \frac{a_1b_1 + a_2b_2 + a_3b_3}
       {\sqrt{a_1^2+a_2^2+a_3^2}
        \sqrt{b_1^2+b_2^2+b_3^2}}
$$ -->
