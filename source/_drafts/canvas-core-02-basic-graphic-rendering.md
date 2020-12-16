---
title: Canvas 核心：基础图形 — 矩形、圆形、贝塞尔曲线
subTitle: canvas-core-02-basic-graphic-rendering

tags: 
  - Canvas
  - 2D
  - 图形开发
categories:
  - Canvas
---




## 基础图形绘制

### 矩形填充与描边

绘制矩形的 API:

``` ts
interface CanvasRect {
    clearRect(x: number, y: number, w: number, h: number): void;
    fillRect(x: number, y: number, w: number, h: number): void;
    strokeRect(x: number, y: number, w: number, h: number): void;
}
```

简单矩形绘制，矩形描边：

<iframe src="https://codesandbox.io/embed/canvas-test-demo-koccm?fontsize=14&hidenavigation=1&initialpath=%2F%23%2FDemo.01&module=%2Fsrc%2Fdemo%2FDemo.01.ts&theme=dark&view=preview"
  style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
  title="canvas-test-demo"
  allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
  sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
></iframe>

### 渐变与图案填充

**线性渐变填充：**

<iframe src="https://codesandbox.io/embed/canvas-test-demo-koccm?fontsize=14&hidenavigation=1&initialpath=%2F%23%2FDemo.02&module=%2Fsrc%2Fdemo%2FDemo.02.ts&theme=dark&view=preview"
  style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
  title="canvas-test-demo"
  allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
  sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
></iframe>

**径向渐变填充：**

<iframe src="https://codesandbox.io/embed/canvas-test-demo-koccm?fontsize=14&hidenavigation=1&initialpath=%2F%23%2FDemo.03&module=%2Fsrc%2Fdemo%2FDemo.03.ts&theme=dark&view=preview"
  style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
  title="canvas-test-demo"
  allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
  sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
></iframe>


**图案填充：**

<iframe src="https://codesandbox.io/embed/canvas-test-demo-koccm?fontsize=14&hidenavigation=1&initialpath=%2F%23%2FDemo.04&module=%2Fsrc%2Fdemo%2FDemo.04.ts&theme=dark&view=preview"
  style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
  title="canvas-test-demo"
  allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
  sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
></iframe>
