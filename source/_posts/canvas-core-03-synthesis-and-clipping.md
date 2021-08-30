---
title: Canvas 图像合成与剪辑区域
name: canvas-core-03-synthesis-and-clipping
keywords: 'Canvas, 2D 图形, 图像合成, 剪辑区域'

image: https://s3.ax1x.com/2020/12/23/r6GktI.png
date: 2020-12-23 15:46:59
categories:
  - Canvas
tags:
  - 2D
  - Canvas
  - 图形开发
---


## 图像合成

图像合成就是在画布上将某一个物体（源物体）绘制到另一个物体（目标物体）之上，绘制图形的叠加显示，默认情况下，浏览器会简单的将源物体叠放在目标物体上面。但是可以通过设置绘图环境对象的 [`globalCompositeOperation`](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/globalCompositeOperation) 属性来改变图像合成的行为，该属性可以取得如下列出的任意一个值，这些值叫做 **Portor-Duff** 操作符，它的详细描述在一篇由 LucasFilm Ltd —— 卢卡斯影视有限公司（著名星球大战系列影片）的 Thomas Porter 和 Tom Duff 所发表的文章中 —— [https://keithp.com/~keithp/porterduff/p253-porter.pdf](https://keithp.com/~keithp/porterduff/p253-porter.pdf)。

- `source-over` 默认，在现有画布上下文之上绘制新图形。
- `source-in` 新图形只在新图形和目标画布重叠的地方绘制，其他的都是透明的。
- `source-out`  在不与现有画布内容重叠的地方绘制新图形。
- `source-atop` 新图形只在与现有画布内容重叠的地方绘制。
- `destination-over`  在现有的画布内容后面绘制新的图形。
- `destination-in`  现有的画布内容保持在新图形和现有画布内容重叠的位置，其他的都是透明的。
- `destination-out` 只有源图像外的目标图像部分会被显示，现有内容保持在新图形不重叠的地方。
- `destination-atop`  现有的画布只保留与新图形重叠的部分，新的图形是在画布内容后面绘制的。
- `lighter` 显示源图像 + 目标图像，两个重叠图形的颜色是通过颜色值相加来确定的。。
- `copy`  显示源图像，忽略目标图像，只显示新图形。
- `xor` 那些重叠和正常绘制之外的其他地方是透明的。
- `multiply` 将顶层像素与底层相应像素相乘，结果是一幅更黑暗的图片。
- `screen` 像素被倒转，相乘，再倒转，结果是一幅更明亮的图片。
- `overlay` multiply 和 screen 的结合，原本暗的地方更暗，原本亮的地方更亮。
- `darken` 保留两个图层中最暗的像素。
- `lighten` 保留两个图层中最亮的像素。
- `color-dodge` 将底层除以顶层的反置。
- `color-burn` 将反置的底层除以顶层，然后将结果反过来。
- `hard-light` 屏幕相乘（A combination of multiply and screen）类似于叠加，但上下图层互换了。
- `soft-light` 用顶层减去底层或者相反来得到一个正值。
- `difference` 一个柔和版本的强光（hard-light）。纯黑或纯白不会导致纯黑或纯白。
- `exclusion` 和 difference 相似，但对比度较低。
- `hue` 保留了底层的亮度（luma）和色度（chroma），同时采用了顶层的色调（hue）。
- `saturation` 保留了底层的亮度（luma）和色调（hue），同时采用顶层的色度（chroma）。
- `color` 保留了底层的亮度（luma），同时采用了顶层的色调(hue)和色度(chroma)。
- `luminosity` 保持底层的色调（hue）和色度（chroma），同时采用顶层的亮度（luma）。


### MDN 官方文档示例

<iframe class="live-sample-frame sample-code-frame" frameborder="0" height="600" width="100%" loading="lazy"  id="frame_合成示例" allow="fullscreen" src="https://yari-demos.prod.mdn.mozit.cloud/en-US/docs/Web/API/Canvas_API/Tutorial/Compositing/Example/_sample_.Compositing_example.html"></iframe>

[示例来自 MDN](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/globalCompositeOperation)


### 图像合成示例 Demo


<iframe 
  class="live-sample-frame sample-code-frame" 
  frameborder="0" 
  height="600" 
  width="100%" 
  loading="lazy"      
  style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
  title="canvas-test-demo"
  allow="fullscreen; accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking" 
  src="https://html5-canvas-core.vercel.app/#/Demo.28">
</iframe>

[查看完整 Demo 效果](https://html5-canvas-core.vercel.app/#/Demo.28)



## 剪辑区域

剪辑区域：剪辑区域，是一个非常有用的功能，也可以叫做裁剪区域，是指绘制路径所定义的一块区域，定义了该区域，后续的所有绘图操作都被限制在该区域中执行。在默认情况下，裁剪区域与 Canvas 画布大小一致，可以自行定义裁剪区域，自定义裁剪区域就是创建创一段自定义路径（路径组成的区域），并调用绘图环境上的 `clip()` 方法，一旦设置了剪辑区域后，那么在 Canvas 上绘制的所有内容都会被限制在该区域内，在剪辑区域外部绘制没有效果。

[`clip()`](https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D/clip) 将当前创建的路径设置为当前剪切路径，`clip()`具有多种形式，方法的重载形式如下。

- `clip(fillRule?: CanvasFillRule): void`
- `clip(path: Path2D, fillRule?: CanvasFillRule): void`

`fillRule` 取值为 `evenodd` | `nonzero`，`nonzero` 为非零环绕原则，默认原则，`evenodd`: 奇偶环绕原则，`path` 为需要剪切的 Path2D 路径。

### 简单裁剪示例

如下 Demo 所示：在调用 `clearRect()` 清除整个画布之前调用了 `clip()` 方法，会将之前调用 `arc()` 创建的路径作为裁剪路径，那后续调用 `clearRect()` 只会作用在该裁剪路径里面，所以只清除了中心圆形那块区域。

<iframe src="https://codesandbox.io/embed/canvas-test-demo-koccm?fontsize=14&hidenavigation=1&initialpath=%2F%23%2FDemo.10&module=%2Fsrc%2Fdemo%2FDemo.10.ts&theme=dark&view=preview"
  style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
  title="canvas-test-demo"
  allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
  sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
></iframe>


### 利用裁剪实现橡皮檫

比如利用裁剪区域可以实现橡皮檫效果，橡皮檫实现原理：在拖动橡皮檫时，将剪辑区域设置为橡皮擦图形的区域，然后调用 `clearRect(0, 0, canvas.width, canvas.height)` 将整个画布擦除，因为在调用 `clearRect()` 方法之前，设置了剪辑区域为橡皮擦区域，调用 `clearRect()` 方法只会在剪辑区域生效，所以只擦除了剪辑区域（橡皮檫区域）的图形。

<iframe 
  class="live-sample-frame sample-code-frame" 
  frameborder="0" 
  height="600" 
  width="100%" 
  loading="lazy"      
  style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
  title="canvas-test-demo"
  allow="fullscreen; accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking" 
  src="https://html5-canvas-core.vercel.app/#/Demo.29">
</iframe>

[查看完整 Demo 效果](https://html5-canvas-core.vercel.app/#/Demo.29)

