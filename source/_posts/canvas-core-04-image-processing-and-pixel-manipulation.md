---
title: Canvas 图像处理 - 像素操作和滤镜实现
name: canvas-core-04-image-processing-and-pixel-manipulation
date: 2020-12-25 20:32:16

image: images/article/2020-11-02/2020-12-25-201402.jpg
categories:
  - Canvas
tags:
  - 2D
  - Canvas
  - 图形开发
---

## 图像操作 API

图像操作相关接口：

- `drawImage()` 绘制图像到 Canvas 画布，该方法有三个重载方式。 [文档](https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D/drawImage)
- `getImageData()` 返回一个 ImageData 对象，用于描述 canvas 指定区域的隐含像素数据。 [文档](https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D/getImageData)
- `putImageData()` 将数据从已有的 ImageData 对象绘制到画布上。 [文档](https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D/putImageData)
- `createImageData()` 用于创建一个新的、空白的、指定大小的 ImageData 对象。所有的像素在新对象中都是透明的黑色。 [文档](https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D/createImageData)


Canvas 2D API 的 `drawImage()` 有三个重载方法提供了多种在画布（Canvas）上绘制图像的方式。

```ts
interface CanvasDrawImage {
    /** [MDN Reference](https://developer.mozilla.org/docs/Web/API/CanvasRenderingContext2D/drawImage) */
    drawImage(image: CanvasImageSource, dx: number, dy: number): void;
    drawImage(image: CanvasImageSource, dx: number, dy: number, dw: number, dh: number): void;
    drawImage(image: CanvasImageSource, sx: number, sy: number, sw: number, sh: number, dx: number, dy: number, dw: number, dh: number): void;
}
```

## 操作图形像素

### ImageData 介绍

ImageData 接口描述 `<canvas>` 元素的一个隐含像素数据的区域。使用 ImageData() 构造函数创建，或者使用 CanvasRenderingContext2D 对象上的 创建方法 `context.createImageData()` 和 获取方法 `context.getImageData()`。也可以使用 `context.putImageData()` 设置 canvas 的一部分区域。

`getImageData()` / `putImageData()` 两个方法分别用于获取像素和填充像素，同时也可以修改像素中的值以达到预期效果。getImageData() 方法返回一个 ImageData 类型对象，包含三个属性:

- `width` 以设备像素为单位的图像数据宽度
- `height` 以设备像素为单位的图像数据高度
- `data` 包含各个设备像素值得数组，每个值中包含的颜色分量，都是含有 8 位二进制的整数, 使用 TypedArray 形式 ArrayBuffer 来存储数据

```ts
const imgdata = context.getImageData(0, 0, canvas.width, canvas.height)
const data = imgdata.data;
const length = imgdata.data.length;
const width = imgdata.width;

imgdata.data[0]       // red
imgdata.data[0]       // green
imgdata.data[0]       // blue
imgdata.data[0]       // alpha

imgdata.data[n - 4]   // red
imgdata.data[n - 3]   // green
imgdata.data[n - 2]   // blue
imgdata.data[n - 1]   // alpha
```

### 常见图像像素遍历方式

遍历每个像素

```ts
for (let i = 0; i < length; i++) {
  const pixel = data[i]
  // do something
}
```

反向遍历每个像素

```ts
let i = length - 1;
while (i >= 0) {
  data[i--]
}
```

只处理 alpha 值，不改变 r,g,b

```ts
for (let i = 3; i < length - 4; i += 4) {
  data[i]
}
```

只处理  r,g,b 值，不改变 alpha

```ts
for (let i = 0; i < length - 4; i += 4) {
  data[i]               // red
  data[i + 1]           // green
  data[i + 2]           // blue
}
```


## 图像滤镜实现

了解到如何获取和操作像素之后，接下来就可以给图片实现各种滤镜了，滤镜的原理其实就是对图像像素的各种操作，给图像像素做一些改动，主要在于各种滤镜算法实现。

### 常见图像滤镜

负片滤镜

```ts
for (let i = 0; i < length - 4; i += 4) {
  data[i] = 255 - data[i];
  data[i + 1] = 255 - data[i + 1];
  data[i + 2] = 255 - data[i + 2];
}
```

黑白滤镜

```ts
for (let i = 0; i < length - 4; i += 4) {
  const average = (data[i] + data[i + 1] + data[i + 2]) / 3;
  data[i] = average;
  data[i + 1] = average;
  data[i + 2] = average;
}
```

浮雕滤镜

```ts
for (let i = 0; i < length; i++) {
  // 防止超出边界
  if (i <= length - width * 4) {
    // 不是 alpha
    if ((i + 1) % 4 !== 0) {
      // 如果是一行的最后一个像素，右边不会有像素，拷贝前一个像素值
      if ((i + 4) % (width * 4) === 0) {
        data[i] = data[i - 4];
        data[i + 1] = data[i - 3];
        data[i + 2] = data[i - 2];
        data[i + 3] = data[i - 1];
        i += 4;
      } else {
        // 不是一行的最后一个像素
        data[i] =
          255 / 2 + // Average value
          2 * data[i] - // current pixel
          data[i + 4] - // next pixel
          data[i + width * 4]; // pixel underneath
      }
    }
  } else { 
    // 最后一行，下方没有像素，拷贝上方像素
    if ( (i + 1) % 4 !== 0) {
      data[i] = data[i - width * 4];
    }
  }
}
```

墨镜滤镜

```ts
for (let i = 0; i < length - 4; i++) {
  if ((i + 1) % 4 !== 0) {
    if ( (i + 4) % (width * 4) === 0) {
      data[i] = data[i - 4];
      data[i + 1] = data[i - 3];
      data[i + 2] = data[i - 2];
      data[i + 3] = data[i - 1];
      i += 4;
    } else {
      data[i] = 2 * data[i] - data[i + 4] - 0.5 * data[i + 4];
    }
  }
}
```

### 各种滤镜实现效果

<iframe 
  class="live-sample-frame sample-code-frame" 
  frameborder="0" 
  height="600" 
  width="100%" 
  loading="lazy"      
  style="width:100%; height:600px; border:0; border-radius: 4px; overflow:hidden;"
  title="canvas-test-demo"
  allow="fullscreen; accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking" 
  src="https://iaosee.com/html5-canvas-core/#/Demo.40">
</iframe>

[查看完整 Demo 效果](https://iaosee.com/html5-canvas-core/#/Demo.40)



## 像素处理性能优化 

在处理大图像数据时，很容易遇到性能瓶颈，可以考虑将图像处理的任务交给工作线程 —— [Web Worker](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Workers_API) 来处理，这样将耗时的数据处理代码放在主线程之外执行。


### 离屏 Canvas

在大量的像素操作的情况下，对于 Canvas 来说是很消耗性能的，这时候可以使用离屏 Canvas 来优化性能。离屏 Canvas 对性能优化非常有用，可以提高绘制效率。

在较新的浏览器中，新增了脱离屏幕渲染的 Canvas 对象。 `OffscreenCanvas` 对象，可以使用 `new OffscreenCanvas(width, height)` 创建一个离屏 Canvas  `OffscreenCanvas` 对象, 使用 `getContext('2d')` 获得一个 `OffscreenCanvasRenderingContext2D` 上下文，使用方式与 `CanvasRenderingContext2D` 方式基本一致，离屏 Cavnas 优先使用 `OffscreenCanvas`。

另一种使用 OffscreenCanvas API 的方式，是在一个 `<canvas>` 元素上调用 `transferControlToOffscreen()` 方法会返回一个 `OffscreenCanvas`

要使用离屏 Canvas 遵循一下四个步骤：

- 创建用作离屏绘制的 Canvas 元素
- 设置离屏 Canvas 的宽高
- 在离屏 Canvas 中进行绘制
- 将离屏 Canvas 中的全部或部分内容复制到要显示的 Canvas 中


```ts
const canvas = document.getElementById('canvas') as HTMLCanvasElement;
const context = canvas.getContext('2d');
// const offScreenCanvas = document.createElement('canvas');
const offScreenCanvas = new OffscreenCanvas(canvas.width, canvas.height);
const offScreenContext = offScreenCanvas.getContext('2d');

offScreenCanvas.width = canvas.width;
offScreenCanvas.height = canvas.height;

offScreenContext.drawImage(image, 0, 0);
context.drawImage(offScreenCanvas, 0, 0);
```
