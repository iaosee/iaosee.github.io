---
title: Canvas 精灵 - 精灵系统实现 🧚
name: canvas-core-06-sprite-system
date: 2021-01-02 19:54:46

image: images/article/sprite-system.jpg

categories:
  - Canvas
tags:
  - 2D
  - Canvas
  - 图形开发
  - 动画
---


## 精灵介绍

**精灵** 是一种可以集成到动画之中图像对象，可以称为**精灵图**。[精灵图](https://en.wikipedia.org/wiki/Sprite_(computer_graphics))（英语：Sprite），又被称为拼合图，是一种图片拼合技术，它就是把多张小图合成一张大图，这张大图就叫做精灵图。在计算机图形学中，当一张二维图像集成进场景中，成为整个显示图像的一部分时，这张图就称为精灵图。因为常见碳酸饮料雪碧的英文名称也是 "Sprite"，也有人会使用雪碧图的非正式译名。


在前端中最常见的是通过 CSS 使用精灵图，通过 CSS 中的 `background-position` 属性，由在定义的 X 和 Y 坐标下将小图像组合成一个大图像组成，显示精灵图中某一个小图标。


这里需要在 Canvas 中通过 JS 使用精灵图，Canvas 提供了实现精灵所需的全部处理能力。

## 精灵实现


要制作一个有用的精灵对象，需要自己实现将精灵图绘制出来，并将其放置于动画中指定的位置，并能实现从一个地方移动到另一个地方。这些精灵对象还能够接受调用者的命令，来执行某些特定的动作，比如下落、弹起、飞行，以及与其他精灵的碰撞等等。


### Sprite

精灵对象有两个方法：`paint()` 和 `update()`。`update()` 方法用于执行每个精灵的行为，一个精灵可以有多个行为，执行的顺序就是这些行为被加入的顺序，`paint()` 方法则是将绘制操作代理给绘制器对象来执行绘制操作。

精灵无需自己完成绘制操作，它的绘制操作将代理给另一个对象来做。从本质上来讲， Painter 对象就是一个可以相互交换着使用的绘制算法。


| 属性      | 说明 |
| ----------- | :----------- |
| top      | 精灵左上角 Y 坐标       |
| left   | 精灵左上角 X 坐标        |
| width   | 精灵宽度        |
| height   | 精灵高度        |
| velocityX   | 水平移动速度        |
| velocityY   | 垂直移动速度        |
| painter   | 用于绘制精灵对象的绘制器        |
| visible   | 精灵是否可见 boolean 值        |
| animating   | 精灵是否正在执行动画效果 boolean 值        |
| behaviors   | 包含精灵行为对象的数组，执行更新逻辑        |


定义公共接口:


```ts
// 定义公共接口
export interface IPainter {
  paint(sprite: Sprite, context: CanvasRenderingContext2D): void;
}

export interface IBehavior {
  execute(sprite: Sprite, context: CanvasRenderingContext2D, time: number): void;
}
```

Sprite 对象类的实现：

```ts
// Sprite 实现
export class Sprite<P extends IPainter = IPainter> {
  public name: string;
  public x = 0;
  public y = 0;
  public left = 0;
  public top = 0;
  public width = 10;
  public height = 10;
  public velocityX = 0;
  public velocityY = 0;
  public visible = true;
  public animating = false;
  public painter: P = null;
  public behaviors: IBehavior[] = [];

  public constructor(name: string, painter: P = null, behaviors: IBehavior[] = []) {
    this.name = name;
    this.painter = painter;
    this.behaviors = behaviors;
  }

  public setX(x: number) {
    this.x = x;
  }

  public setY(y: number) {
    this.y = y;
  }

  public addBehavior(behavior: IBehavior) {
    if (this.behaviors.includes(behavior)) {
      return this;
    }
    this.behaviors.push(behavior);
    return this;
  }

  public setPainter(painter: P) {
    this.painter = painter;
    return this;
  }

  public paint(context: CanvasRenderingContext2D) {
    if (this.painter && this.visible) {
      this.painter.paint(this, context);
    }
  }

  public update(context: CanvasRenderingContext2D, time: number) {
    for (let i = this.behaviors.length; i > 0; --i) {
      this.behaviors[i - 1].execute(this, context, time);
    }
  }
}
```

现在一个简单的精灵对象就实现完成了。


## Painter - 绘制器

由于精灵自身不绘制，精灵展示显示的样式，取决于绘制器如何绘制。Sprite 对象和它的绘制器之间是应该是解耦的，这样就可以在程序运行时对精灵对象动态的设置或更换绘制器，这样极大的提高了精灵对象的灵活性。

Painter 对象主要需要实现 `paint(sprite, context)` 方法即可，所有的 Painter 对象都可以被归纳为三类：

- 描边及填充绘制器
- 图像绘制器
- 精灵表绘制器


描边及填充绘制器，根据自己想要的形状调用 Canvas 的图形 API 绘制就行了。


### 图像绘制器

图像绘制器包含一个指向图像对象的引用，它会将此图像绘制到由 `paint()` 方法所传入的绘图环境对象之上。只有当图像加载完成之后，图像绘制器才会将其图像绘制出来。

```ts
export class ImagePainter implements IPainter {
  public image = new Image();

  public constructor(imageUrl: string) {
    this.image.src = imageUrl;
  }

  public paint(sprite: Sprite, context: CanvasRenderingContext2D) {
    if (!this.image) {
      return;
    }

    const x = sprite.x || sprite.left;
    const y = sprite.y || sprite.top;

    if (!this.image.complete) {
      this.image.onload = (e) => {
        sprite.width = this.image.width;
        sprite.height = this.image.height;

        context.drawImage(this.image, x, y, sprite.width, sprite.height);
      };
    } else {
      context.drawImage(this.image, x, y, sprite.width, sprite.height);
    }
  }
}
```

### 精灵表绘制器

这里就是最开始讲的精灵图的绘制，精灵表绘制器会把精灵表中表示当前动画帧的那个单元格绘制出来。绘制器对象有一个数组索引，用于表示精灵表每个单元格的信息。

```ts
export interface SheetCell {
  x: number;
  y: number;
  width: number;
  height: number;
}

export class SpriteSheetPainter implements IPainter {
  public cells: SheetCell[] = [];
  public cellIndex: number = 0;
  public spriteSheet = new Image();

  public constructor(spriteSheetImageUrl: string, cells: SheetCell[] = []) {
    this.cells = cells;
    this.spriteSheet.src = spriteSheetImageUrl;
  }

  public advance() {
    if (this.cellIndex === this.cells.length - 1) {
      this.cellIndex = 0;
    } else {
      this.cellIndex++;
    }
  }

  public paint(sprite: Sprite, context: CanvasRenderingContext2D) {
    if (!this.spriteSheet) {
      return;
    }

    const cell = this.cells[this.cellIndex];
    sprite.width = cell.width;
    sprite.height = cell.height;

    if (!this.spriteSheet.complete) {
      this.spriteSheet.onload = e => {
        context.drawImage(
          this.spriteSheet,
          cell.x,
          cell.y,
          cell.width,
          cell.height,
          sprite.x,
          sprite.y,
          cell.width,
          cell.height
        );
      };
    } else {
      context.drawImage(
        this.spriteSheet,
        cell.x,
        cell.y,
        cell.width,
        cell.height,
        sprite.x,
        sprite.y,
        cell.width,
        cell.height
      );
    }
  }
}
```

## Behavior - 行为

实现了各个精灵绘制器之后，继续研究如何为精灵对象添加行为，使其能够执行不同的动作。之前已经定义了 `IBehavior` 接口，只要实现了该接口定义了 `execute(sprite: Sprite, context: CanvasRenderingContext2D, time: number)` 方法的对象，都可以叫做行为，该方法用来修改精灵的属性，比如移动其位置，修改其外观。行为可以是一个简单的移动动作，也可以是一个比较复杂的操作。

精灵包含一个行为对象数组，它的 `update()` 方法会遍历行为数组，使得精灵的每个行为都能够执行。


实现一些精灵的行为：

```ts
/**
 * @description 跑动行为
 */
export class RunInPlaceBehavior implements IBehavior {
  public lastAdvance: number = 0;
  public PAGEFLIP_INTERVAL: number = 100;

  public constructor(interval?: number) {
    this.PAGEFLIP_INTERVAL = interval || this.PAGEFLIP_INTERVAL;
  }

  public execute(sprite: Sprite<SpriteSheetPainter>, context: CanvasRenderingContext2D, time: number) {
    if (time - this.lastAdvance > this.PAGEFLIP_INTERVAL) {
      sprite.painter.advance();
      this.lastAdvance = time;
    }
  }
}

/**
 * @description 向右跑行为
 */
export class MoveLeftToRightBehavior implements IBehavior {
  public lastMove: number = 0;

  public constructor(private startX?: number, private endX?: number) {}

  public execute(sprite: Sprite<SpriteSheetPainter>, context: CanvasRenderingContext2D, time: number) {
    if (this.lastMove !== 0) {
      sprite.x -= sprite.velocityX * ((time - this.lastMove) / 1000);
      const startX = this.startX || 0;
      const endX = this.endX || context.canvas.width;
      sprite.x = sprite.x < startX ? endX : sprite.x;
    }
    this.lastMove = time;
  }
}
```

## 使用精灵类

使用精灵并指定精灵的行为：

```ts

const runnerCells: SheetCell[] = [
  { x: 0, y: 0, width: 47, height: 64 },
  { x: 55, y: 0, width: 44, height: 64 },
  { x: 107, y: 0, width: 39, height: 64 },
  { x: 150, y: 0, width: 46, height: 64 },
  { x: 208, y: 0, width: 49, height: 64 },
  { x: 265, y: 0, width: 46, height: 64 },
  { x: 320, y: 0, width: 42, height: 64 },
  { x: 380, y: 0, width: 35, height: 64 },
  { x: 425, y: 0, width: 35, height: 64 },
];

const sprite = new Sprite(
  'Sprite', 
  new SpriteSheetPainter(running_sprite_sheet, runnerCells), 
  [
    new RunInPlaceBehavior(),
    new MoveLeftToRightBehavior(),
  ]
);

const animate = (timestamp: number) => {

  // Update and draw ...
  sprite.update(context, timestamp);
  sprite.paint(context);

  requestAnimationFrame(animate);
};

requestAnimationFrame(animate);

```


## 总结

精灵是制作绚丽动画的关键要素之一，文章上面介绍了如何实现封装精灵对象，精灵行为对象，以及如何讲精灵动画效果提取封装为可以复用的精灵动画制作器。并且上述对象的代码实现能够在更高的抽象层面上编程，使得精灵的各部分解耦，增强了精灵的灵活性和代码的可复用性。

### 精灵示例

<iframe 
  class="live-sample-frame sample-code-frame" 
  frameborder="0" 
  height="600" 
  width="100%" 
  loading="lazy"      
  style="width:100%; height:600px; border:0; border-radius: 4px; overflow:hidden;"
  title="canvas-test-demo"
  allow="fullscreen; accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking" 
  src="https://iaosee.com/html5-canvas-core/#/Demo.52">
</iframe>

[查看完整 Demo 效果](https://iaosee.com/html5-canvas-core/#/Demo.52)
