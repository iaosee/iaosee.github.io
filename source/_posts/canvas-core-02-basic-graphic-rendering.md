---
title: Canvas 基础图形绘制 — 矩形、圆形、曲线、多边形
name: canvas-core-02-basic-graphic-rendering
keywords: 'Canvas, 2D 图形, Canvas 绘制基本图形'

image: https://z3.ax1x.com/2020/12/23/r6DKk8.png
date: 2020-12-10 12:58:32
categories:
  - Canvas
tags:
  - 2D
  - Canvas
  - 图形开发
---

## 路径绘制


在 Canvas 中绘制形状大致分为两个步骤，先是 **创建路径**，然后为创建的路径 **填充颜色**。填充颜色可以选择**描边**或者**填充**，或者两者同时应用。

`CanvasRenderingContext2D` 中路径相关的 API：只通过这些基础的路径 API 就可以创建任何想要的形状了。


```ts
interface CanvasPath {
  arc(x: number, y: number, radius: number, startAngle: number, endAngle: number, counterclockwise?: boolean): void;
  arcTo(x1: number, y1: number, x2: number, y2: number, radius: number): void;
  bezierCurveTo(cp1x: number, cp1y: number, cp2x: number, cp2y: number, x: number, y: number): void;
  closePath(): void;
  ellipse(x: number, y: number, radiusX: number, radiusY: number, rotation: number, startAngle: number, endAngle: number, counterclockwise?: boolean): void;
  lineTo(x: number, y: number): void;
  moveTo(x: number, y: number): void;
  quadraticCurveTo(cpx: number, cpy: number, x: number, y: number): void;
  rect(x: number, y: number, w: number, h: number): void;
}
```


创建路径之后，通过 `fillStyle` / `strokeStyle` 设置填充样式或者轮廓样式，使用 `fill()` 或者 `stroke()` 进行填充颜色或者只上轮廓色。

- `fillStyle` 对应 `fill()` 填充的样式
- `strokeStyle` 对应 `stroke()` 轮廓的样式


`fillStyle` / `strokeStyle` 接收的值可以是 *CSS 颜色字符串* 或者一个 `CanvasGradient` / `CanvasPattern` 。


对于一些复杂的图像，都是基于路径绘制的。基于路径绘图，首先需要调用 `beginPath()` 方法来开启一段新路径，调用各个路径方法用于创建不同的路径。然后调用 `stroke()` / `fill()` 进行描边和填充。想要关闭某段路径，则需要调用 `closePath()` 方法，描边与填充的效果取决于当前绘图属性。


填充路径时使用 [`非零环绕规则`](https://zhuanlan.zhihu.com/p/113411760)，用来判断哪块区域是里面，哪块区域是外面，从而在填充不规则复杂图形时表示那块区域要填充或不填充。—— 对于路径中的任意给定区域，从该区域内部画一条足够长的线段，使此线段的终点完全落在路径范围之外。然后将计数器初始化为零，如果与路径的顺时针方向相交，计数器加 1， 与路径的逆时针方向，计数器减 1，若最后计数器的结果不为零，那么此区域就在路径里面，在调用 `fill()` 方法时，就会进行填充。



## 画线与文本

若在像素边界处绘制 1 像素宽的线，那么该线会占据 2 个像素宽度，线相关的 API：

- `moveTo(x, y)` 向当前路径中增加一条子路径
- `lineTo(x, y)` 如果当前路径中没有子路径，它的行为就和 `moveTo()` 方法行为一样，若当前存在子路径，会将制定的点添加到子路径中。
- `setLineDash([10, 20])`  绘制虚线，设置虚线间断


文本绘制相关 API ：

- `font` 指定字体样式/大小，一个 CSS3 属性字符串，顺序为 `font-style font-variant font-weight font-size font-height font-family`
- `textAlign` 指定对齐方式，取值 `start|center|end|left|right`
- `textBaseline` 指定文本基线对齐方式，取值 `top|bottom|middle|alphabetic|ideographic|hanging` 

- `strokeText(text, x, y)`  文本描边
- `fillText(text, x, y)`  文本填充
- `measureText(text)`  测量文本 在当前上下文状态中的信息 返回 `TextMetrics` 类型的值


<iframe src="https://codesandbox.io/embed/canvas-test-demo-koccm?fontsize=14&hidenavigation=1&initialpath=%2F%23%2FDemo.01&module=%2Fsrc%2Fdemo%2FDemo.01.ts&theme=dark&view=preview"
  style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
  title="canvas-test-demo"
  allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
  sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
></iframe>

## 矩形填充与描边

绘制矩形的 API:

``` ts
interface CanvasRect {
    clearRect(x: number, y: number, w: number, h: number): void;
    fillRect(x: number, y: number, w: number, h: number): void;
    strokeRect(x: number, y: number, w: number, h: number): void;
}
```

简单矩形绘制，矩形描边：

<iframe src="https://codesandbox.io/embed/canvas-test-demo-koccm?fontsize=14&hidenavigation=1&initialpath=%2F%23%2FDemo.02&module=%2Fsrc%2Fdemo%2FDemo.02.ts&theme=dark&view=preview"
  style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
  title="canvas-test-demo"
  allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
  sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
></iframe>

## 圆形与椭圆

<iframe src="https://codesandbox.io/embed/canvas-test-demo-koccm?fontsize=14&hidenavigation=1&initialpath=%2F%23%2FDemo.03&module=%2Fsrc%2Fdemo%2FDemo.03.ts&theme=dark&view=preview"
  style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
  title="canvas-test-demo"
  allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
  sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
></iframe>


## 内容填充

可以给绘制的图形填充一些东西，包括颜色，渐变，图案，通过设置 [`fillStyle`](https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D/fillStyle) 属性来指定填充样式，`fillStyle` 属性接收的值有三种类型，分别是：`string | CanvasGradient | CanvasPattern`。填充图形通常的步骤是：**设置填充样式 -> 创建路径 -> 调用 `fill()` 方法填充**。

- `string` 一个 CSS 颜色字符串
- [`CanvasGradient`](https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasGradient) 通过 `createLinearGradient()` 或 `createRadialGradient()` 方法创建的渐变
- [`CanvasPattern`](https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasPattern) 通过 `createPattern()` 方法创建的模式


Canvas 中渐变填充支持 `线性渐变(linear)` 和 `径向渐变(radial)` 。


### 线性渐变填充


线性渐变需要两个点的坐标，Canvas 会根据两点之间的连线来建立渐变效果。

使用 `createLinearGradient(x0, y0, x1, y1)` 方法创建渐变，返回一个 `CanvasGradient` 实例，通过它的 `addColorStop(percentage, color)` 方法添加 `颜色停止点`。


``` ts
const gradient = context.createLinearGradient(0, 0, this.centerX, 0);
gradient.addColorStop(0.00, 'blue');
gradient.addColorStop(0.50, 'red');
gradient.addColorStop(1.00, 'yellow');
context.fillStyle = gradient;
context.fillRect(0, 0, canvas.width / 2,  canvas.height / 2);
```


<iframe src="https://codesandbox.io/embed/canvas-test-demo-koccm?fontsize=14&hidenavigation=1&initialpath=%2F%23%2FDemo.04&module=%2Fsrc%2Fdemo%2FDemo.04.ts&theme=dark&view=preview"
  style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
  title="canvas-test-demo"
  allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
  sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
></iframe>

### 径向渐变填充

径向渐变需要两个圆形，canvas 会根据两个圆之间的范围来建立渐变效果。
使用 `createRadialGradient(x0, y0, r0, x1, y1, r1)` 方法创建渐变实例，返回一个 `CanvasGradient` 实例

``` ts
const gradient = context.createRadialGradient(
  this.centerX / 2, canvas.height / 2 * 1.5, 20,
  this.centerX / 2, canvas.height / 2 * 1.5, 200
);
gradient.addColorStop(0.0, 'blue');
gradient.addColorStop(0.5, 'yellow');
gradient.addColorStop(1.0, 'white');
context.fillStyle = gradient;
context.fillRect(0, this.centerY, canvas.width / 2, canvas.height / 2);
```

<iframe src="https://codesandbox.io/embed/canvas-test-demo-koccm?fontsize=14&hidenavigation=1&initialpath=%2F%23%2FDemo.05&module=%2Fsrc%2Fdemo%2FDemo.05.ts&theme=dark&view=preview"
  style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
  title="canvas-test-demo"
  allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
  sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
></iframe>


### 图案填充

使用 `createPattern(image: CanvasImageSource, repetition: string)` 方法创建图案，返回一个 `CanvasPattern` 实例。 Canvas 允许使用图案来对图形和文本进行填充和描边，图案又可分为这些类型：

- `Image` 图像元素 可以是 `HTMLImageElement | SVGImageElement ｜ ImageBitmap`
- `Canvas` Canvas 画布元素
- `Video` 视频元素

参数介绍：

- `image` - `HTMLOrSVGImageElement | HTMLVideoElement | HTMLCanvasElement | ImageBitmap`
- `repetition` - `repeat | repeat-x | repeat-y | no-repeat`



<iframe src="https://codesandbox.io/embed/canvas-test-demo-koccm?fontsize=14&hidenavigation=1&initialpath=%2F%23%2FDemo.06&module=%2Fsrc%2Fdemo%2FDemo.06.ts&theme=dark&view=preview"
  style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
  title="canvas-test-demo"
  allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
  sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
></iframe>


## 阴影效果

可以通过下列 4 个属性值来指定 图形、图像、文本 阴影效果，设置阴影的属性：

``` ts
interface CanvasShadowStyles {
  shadowBlur: number;
  shadowColor: string;
  shadowOffsetX: number;
  shadowOffsetY: number;
}
```

- `shadowColor` CSS3 格式支持的颜色值
- `shadowOffsetX` 阴影的水平像素偏移，像素为单位，默认 0
- `shadowOffsetY` 阴影的垂直像素偏移，像素为单位，默认 0
- `shadowBlur` 阴影模糊值，默认 0

- 将 `shadowColor` 设置为 `underfined` 可以禁用阴影效果。
- 将 `shadowOffsetX` 和 `shadowOffsetY` 可以为负值，以显示内阴影效果。


阴影效果的绘制可能会比较耗时，特别是动画时候运用阴影效果，需要考虑性能问题。


<iframe src="https://codesandbox.io/embed/canvas-test-demo-koccm?fontsize=14&hidenavigation=1&initialpath=%2F%23%2FDemo.07&module=%2Fsrc%2Fdemo%2FDemo.07.ts&theme=dark&view=preview"
  style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
  title="canvas-test-demo"
  allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
  sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
></iframe>


## 贝塞尔曲线

- `quadraticCurveTo(cpx: number, cpy: number, x: number, y: number)` 创建一条表示二次贝塞尔曲线的路径，前两个参数控制点坐标，后两个是锚点坐标。
- `bezierCurveTo(cp1x: number, cp1y: number, cp2x: number, cp2y: number, x: number, y: number)` 创建一条表示三次贝塞尔曲线的路径，参数为三个点坐标，前两个点为曲线控制点，最后一个为锚点。

<iframe src="https://codesandbox.io/embed/canvas-test-demo-koccm?fontsize=14&hidenavigation=1&initialpath=%2F%23%2FDemo.08&module=%2Fsrc%2Fdemo%2FDemo.08.ts&theme=dark&view=preview"
  style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
  title="canvas-test-demo"
  allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
  sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
></iframe>


## 任意多边形

Canvas 接口本身未提供直接绘制多边形的接口，需要自己实现。

```ts
function getPolygonPoints(center: Point, radius: number, sides: number, startAngle: number = 0) {
  const points: Point[] = [];
  for (let i = 0; i < sides; i++) {
    points.push({
      x: center.x + Math.sin(startAngle) * radius,
      y: center.y - Math.cos(startAngle) * radius
    });
    startAngle += (2 * Math.PI) / sides;
  }
  return points;
}

function drawPolygonPath(center: Point, radius: number, sides: number, startAngle: number = 0) {
  const points = getPolygonPoints(center, radius, sides, startAngle);
  context.beginPath();
  context.moveTo(points[0].x, points[0].y);
  for (let i = 1; i < sides; ++i) {
    context.lineTo(points[i].x, points[i].y);
  }
  context.closePath();
}
```

<iframe src="https://codesandbox.io/embed/canvas-test-demo-koccm?fontsize=14&hidenavigation=1&initialpath=%2F%23%2FDemo.09&module=%2Fsrc%2Fdemo%2FDemo.09.ts&theme=dark&view=preview"
  style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
  title="canvas-test-demo"
  allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
  sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
></iframe>

