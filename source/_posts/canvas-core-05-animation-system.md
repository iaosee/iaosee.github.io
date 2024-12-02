---
title: Canvas 动画系统 - 动画帧速率、基于时间运动、最佳实践
name: canvas-core-04-animation-system
date: 2020-12-28 19:52:18

image: images/article/animation-play.gif
categories:
  - Canvas
tags:
  - 2D
  - Canvas
  - 图形开发
  - 动画
---


## 动画原理

动画原理 —— 持续更新并绘制，这种持续持续更新与重绘就叫做**动画循环**，是所有动画的核心逻辑。像是 GIF 动画图片，视频播放这类我们都可以理解为是一种动画播放的过程。

在 Web 端实现动画的方式有很多中，更为熟悉的方法之一是使用 CSS3 动画，CSS3 支持各种动画，甚至是 3D 动画；还有就是使用 JavaScript 自己实现动画，通过 JavaScript 可以实现更加复杂的动画，这篇文章主要也是记录这一种形式。

### 动画循环 

动画循环 是一种持续不断的循环，连续不断地重复的动画帧序列，如果不手动停止，通常来说是无限循环的。动画循环最常见的用途是在游戏开发中。在游戏中，每个角色都会有一组动画循环，可以混合创建一个可控的角色。例如，步行循环可能会在特定帧与跳跃循环混合。

更新数据 -> 重新渲染，通常来说代码就像这样：

```ts
void animate() {
  // 动画逻辑 —— 持续更新并绘制
  update();
  draw();
}

while(true) {
  animate();
}
```

在其他编程语言中，确确实实就是这么实现的。但是，你不能在 JavaScript 中这么做，这样会导致浏览器失去响应，因为 JavaScript 是单线程的，浏览器在主线程中执行代码，这样的死循环会导致浏览器无法响应用户操作，浏览器无法获得喘息的机会。要实现动画效果，必须让浏览器每隔一段时间有一个喘息的机会。

所以，在 JavaScript 中提供了另一种方式来不断执行动作。在浏览器的执行动画，可以使用 `setInterval()` / `setTimeout()` 来执行循环。`setInterval` 方法每隔一段时间就会调用传给它的函数，而 `setTimeout` 方法只会在到达指定的时间点调用一次传给它的函数，所以要使用 `setTimeout` 持续调用，就必须将下次执行动画循环的时间点计算出来，而 `setInterval` 只需调用一次。

`setInterval` / `setTimeout` 的特点：

- 这两者都是通用的方法，并不是专门为制作动画而用
- 即使向其传递以毫秒为单位的参数值，也依然达不到毫秒级精确性
- 没有对调用动画循环的做优化
- 不会考虑绘制动画的最佳时机，一味的以某个大致时间间隔来调用动画

setInterval 动画循环：

```ts
function animate() {
  // Update and draw ...
}

setInterval(animate, 1000 / 60);
```

setTimeout 实现动画循环：

```ts
function animate() {
  const start = new Date();
  // Update and draw ...
  const finish = new Date();
  setTimeout(animate, (1000 / 60) - (finish - start));
}

setTimeout(animate, 1000 / 60);
```

然而需要注意的是，`setInterval` 和 `setTimeout` 都不是专门用来实现动画的，都不是最优的选择，最好不要使用他们。动画实现的首选方式，是 W3C 标准中 `requestAnimationFrame()` 方法，这个方法属于 HTML5 规范新添加的 API，到目前大多数主流浏览器都支持它了，这个方法的好处是浏览器自行决定帧速率，不需指定帧率。`cancelAnimationFrame()` 方法将 `requestAnimationFrame()` 方法的返回值作为参数可以取消函数的执行，结束动画。requestAnimationFrame [MDN API 文档参考](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/requestAnimationFrame)

使用 `requestAnimationFrame` 优点：

- 对回调函数的调用频率通常与显示器的刷新率相匹配。
- 并根据当前系统性能情况浏览器自行决定帧速率
- 当前网页未处于可见（激活状态）会自动暂停执行

```ts
function animate(timeStamp) {
  // Update and draw ...
  requestAnimationFrame(animate);
}

requestAnimationFrame(animate);
```

使用 `requestAnimationFrame()` 指定的动画循环回调函数，调用时回调函数会被传入 DOMHighResTimeStamp 参数，DOMHighResTimeStamp 指示当前被 requestAnimationFrame() 排序的回调函数被触发的时间。在同一个帧中的多个回调函数，它们每一个都会接受到一个相同的时间戳，即使在计算上一个回调函数的工作负载期间已经消耗了一些时间。该时间戳是一个十进制数，单位毫秒，最小精度为1ms(1000μs)。


## 帧速率

动画是由一系列叫做 “帧” (frame) 的图像组成，这些图像显示的频率就叫做 —— 帧速率 (FPS)。在上方的设置的 `setTimeout(animate, 1000 / 60)` 中，意思是在理想状态下，我们希望动画的执行速率是 60 帧每秒，即 60 fps。因为 setTimeout 并不是精确的这个时间，所以是理想情况下。通过动画的帧率，我们可以得知该动画是否播放得足够流畅。

帧率的计算： 根据当前帧距离上一帧的时间，计算出动画每秒中播放的帧数。

```ts
let lastTime = 0;

const animate = (timestamp: number) => {
  const deltaTime = timestamp - lastTime;
  const fps = 1000 / deltaTime;
  // Update and draw ...
  lastTime = timestamp;
  requestAnimationFrame(animate);
};

requestAnimationFrame(animate);
```


## 恢复动画背景

绘制动画时具有挑战性的环节在于如何处理背景，通常有三种方式。

- 将所有内容都擦除，并重新绘制
- 仅绘制内容发生变化的部分区域
- 冲离屏缓冲区中将内容发生变化的那部分背景复制到屏幕 (也叫图块复制)


将所有内容都擦除并重绘，最直接。 仅绘制变化部分也需擦除背景重绘，但擦除的范围仅限与发生变化的那块区域。

有时候将每一帧内容擦除并重绘，反倒可以获得最佳性能，比如背景很简单，绘制的物体也很简单时。如果背景复杂，重绘所有就会耗时，那就要考虑利用裁剪区域将重绘限制在特定区域。

利用裁剪区域恢复上一帧背景步骤：

- 调用 context.save() 保存状态
- 通过调用 beginPath() 开始一段新路径
- 调用 arc() / rect() 等方法设置路径
- 调用 context.clip() 将当前路径设置为裁剪区域
- 擦除屏幕图像，实际上是擦出了裁剪区域所在图像
- 将背景绘制到屏幕画布，实际上只作用于裁剪区域
- context.restore() 恢复状态

如果动画中物体多，这种方法会耗时。一般倩况，图块复制要比使用剪辑区域快，但需要离屏 canvas。


## 基于时间的运动

如果动画不做限制，在不同设备上，如果两台设备都以相同的帧率运行，物体速度相同保持不变，那么不同设备上的物体都会以相同的速度同时到达某处。但是，如果有一台设备配置要高，有 120hz 刷新率，另一台设备只有 60hz 刷新率，那么会导致配置高的设备播放动画的速度要快得多。常见的比如在多人射击游戏中有两个玩家都同时各自某条道路前行，如果保持速度不变，那么他们都会同时到达指定地方，但是由于某个玩家的电脑配置比另一个好得多，导致其电脑播放游戏动画的速度更快，那么配置高的玩家总是能提前到达，那就没有玩家和比自己电脑配置高的人玩游戏了。

当然我们可以限制帧率，让物体在不同的设备上都以相同的帧率运行，比如让动画每秒播放 30 帧，但是这样物体的运动就赶不上显示器的刷新频率了，因此动画的某些帧会被省略掉，这种现象又叫做 **掉帧**或者**丢帧**，我们就会看到物体的运动从一个地方突然跳到了另一个地方，整个动画的执行不够连贯。当以每秒 30帧的速度来运行动画，不管是否使用 基于时间的运动 ，都会发生掉帧显现，动画看上去总是断断续续的。

所以最好是使用利用本身的刷新率，再加上使用**基于时间的运动**使动画在所有情况下都能以相同的速度播放。这种情况下，我们就能让物体在不同刷新率的设备上都以相同时间到达，需要使用**基于时间的运动**来控制动画，让所有物体都以相同的速度来移动。

基于时间的运动，就是不管帧率如何，动画都应该稳定播放。要让动画以稳定的速度运动，不受帧速率的影响，就需要根据物体的速度计算出它在两帧之间要移动的像素。

计算公式：

`像素/帧 = 像素/秒 * 帧/秒`

也可以写为：

`像素/帧 = 像素/秒 * 秒/帧`


```ts
let lastTime = 0;

const animate = (timestamp: number) => {
  const deltaTime = timestamp - lastTime;
  const fps = 1000 / deltaTime;

  // Update and draw ...
  const preFrame = circle.speed / fps;
  // const preFrame = circle.speed * deltaTime / 1000;
  circle.x += preFrame;

  lastTime = timestamp;
  requestAnimationFrame(animate);
};

requestAnimationFrame(animate);
```


## 动画制作最佳实践

- 使用 requestAnimationFrame() 或这样的 polyfill 形式保证浏览器兼容性
- 将业务的跟新与动画绘制分开
- 使用 "基于时间的运动" 来协调动画的播放速度
- 使用裁剪区域或图块复制技术将复杂的背景恢复到画布上
- 必要时可使用一个或多个离屏缓冲区以提升背景的绘制速度
- 不要手动实现传统的双缓冲算法，浏览器会自动实现它
- 避免使用 CSS 指定阴影及圆角效果
- 避免在 Canvas 中绘制阴影效果
- 避免在播放动画时分配内存
- 使用性能调试及时间轴工具来监控并改善动画绘制效率

## 动画示例

<iframe 
  class="live-sample-frame sample-code-frame" 
  frameborder="0" 
  height="600" 
  width="100%" 
  loading="lazy"      
  style="width:100%; height:600px; border:0; border-radius: 4px; overflow:hidden;"
  title="canvas-test-demo"
  allow="fullscreen; accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking" 
  src="https://iaosee.com/html5-canvas-core/#/Demo.46">
</iframe>

[查看完整 Demo 效果](https://iaosee.com/html5-canvas-core/#/Demo.46)
