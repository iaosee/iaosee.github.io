---
title: Canvas 基础图形绘制 — 矩形、圆形、曲线、多边形、背景填充
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


fillStyle / strokeStyle 接收的值可以是 *CSS 颜色字符串* 或者一个 `CanvasGradient` / `CanvasPattern`

- `fillStyle: string | CanvasGradient | CanvasPattern;`
- `strokeStyle: string | CanvasGradient | CanvasPattern;`


## 画线与文本

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

### 线性渐变填充

<iframe src="https://codesandbox.io/embed/canvas-test-demo-koccm?fontsize=14&hidenavigation=1&initialpath=%2F%23%2FDemo.04&module=%2Fsrc%2Fdemo%2FDemo.04.ts&theme=dark&view=preview"
  style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
  title="canvas-test-demo"
  allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
  sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
></iframe>

### 径向渐变填充

<iframe src="https://codesandbox.io/embed/canvas-test-demo-koccm?fontsize=14&hidenavigation=1&initialpath=%2F%23%2FDemo.05&module=%2Fsrc%2Fdemo%2FDemo.05.ts&theme=dark&view=preview"
  style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
  title="canvas-test-demo"
  allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
  sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
></iframe>


### 图案填充

<iframe src="https://codesandbox.io/embed/canvas-test-demo-koccm?fontsize=14&hidenavigation=1&initialpath=%2F%23%2FDemo.06&module=%2Fsrc%2Fdemo%2FDemo.06.ts&theme=dark&view=preview"
  style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
  title="canvas-test-demo"
  allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
  sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
></iframe>


## 阴影效果

设置阴影的属性：

``` ts
interface CanvasShadowStyles {
  shadowBlur: number;
  shadowColor: string;
  shadowOffsetX: number;
  shadowOffsetY: number;
}
```

<iframe src="https://codesandbox.io/embed/canvas-test-demo-koccm?fontsize=14&hidenavigation=1&initialpath=%2F%23%2FDemo.07&module=%2Fsrc%2Fdemo%2FDemo.07.ts&theme=dark&view=preview"
  style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
  title="canvas-test-demo"
  allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
  sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
></iframe>


## 贝塞尔曲线


<iframe src="https://codesandbox.io/embed/canvas-test-demo-koccm?fontsize=14&hidenavigation=1&initialpath=%2F%23%2FDemo.08&module=%2Fsrc%2Fdemo%2FDemo.08.ts&theme=dark&view=preview"
  style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
  title="canvas-test-demo"
  allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
  sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
></iframe>


## 任意多边形


<iframe src="https://codesandbox.io/embed/canvas-test-demo-koccm?fontsize=14&hidenavigation=1&initialpath=%2F%23%2FDemo.09&module=%2Fsrc%2Fdemo%2FDemo.09.ts&theme=dark&view=preview"
  style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
  title="canvas-test-demo"
  allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
  sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
></iframe>

