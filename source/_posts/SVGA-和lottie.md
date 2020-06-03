---
title: SVGA初体验
date: 2020-05-15 22:11:55
tags: SVGA
---


web端可以采用[SVGA](https://github.com/svga/SVGAPlayer-Web)来实现酷炫的动画。


![image](/zhuyu.github.io/images/shoes.gif)使用SVGA的鞋子动画

动画的实现需要先通过After Effects 或是 Animate(Flash) 软件进行设计，导出成相应的SVGA文件后才可使用


### 使用SVGA

```
<div id="demoCanvas" style="styles..."></div>


var player = new SVGA.Player('#demoCanvas');
var parser = new SVGA.Parser('#demoCanvas'); // Must Provide same selector eg:#demoCanvas IF support IE6+
parser.load('rose_2.0.0.svga', function(videoItem) {
    player.setVideoItem(videoItem);
    player.startAnimation();
})

```
tips:  
1. 过大SVGA的文件会影响页面加载速度，建议添加Loading层，等SVGA文件加载完后进行后续展示
2. 如需本地进行加载SVGA文件，会遇到跨域问题从而无法加载文件，可将SVGA转化为base64文件，再通过script标签引入, 当然base64文件变大33%左右



### svga 动画倒放修改
SVGA源代码中并没有提供从指定帧数倒退播放的API, 我们可以通过修改补充代码来实现
```
=====src/Canvas/player.js  

// reverse 为true 代表倒退 speed 指定速度
stepToFrame(frame, andPlay, reverse=false, speed = 1) {
    if (frame >= this._videoItem.frames || frame < 0) {
        return;
    }
    this.pauseAnimation();
    this._currentFrame = frame;
    this._update();
    if (andPlay) {
        this._doStart(undefined, reverse, this._currentFrame, speed)
    }
}


_doStart(range, reverse, fromFrame, speed=1) {
    this._animator = new ValueAnimator()
    if (range !== undefined) {
        this._animator.startValue = Math.max(0, range.location)
        this._animator.endValue = Math.min(this._videoItem.frames - 1, range.location + range.length)
        this._animator.duration = (this._animator.endValue - this._animator.startValue + 1) * (1.0 / this._videoItem.FPS) * 1000
    }
    else {
        this._animator.startValue = 0
        this._animator.endValue = this._videoItem.frames - 1
        this._animator.duration = this._videoItem.frames * (1.0 / this._videoItem.FPS) * 1000 * speed
    }
    this._animator.loops = this.loops <= 0 ? Infinity : this.loops
    this._animator.fillRule = this.fillMode === "Backward" ? 1 : 0
    this._animator.onUpdate = (value) => {
        if (this._currentFrame === Math.floor(value)) {
            return;
        }
        if (this._forwardAnimating && this._currentFrame > Math.floor(value)) {
            this._renderer.clearAudios()
        }
        this._currentFrame = Math.floor(value)
        this._update()
        if (typeof this._onFrame === "function") {
            this._onFrame(this._currentFrame);
        }
        if (typeof this._onPercentage === "function") {
            this._onPercentage(parseFloat(this._currentFrame + 1) / parseFloat(this._videoItem.frames));
        }
    }
    this._animator.onEnd = () => {
        this._forwardAnimating = false
        if (this.clearsAfterStop === true) {
            this.clear()
        }
        if (typeof this._onFinished === "function") {
            this._onFinished();
        }
    }
    if (reverse === true) {
        this._animator.reverse(fromFrame)
        this._forwardAnimating = false
    }
    else {
        this._animator.start(fromFrame)
        this._forwardAnimating = true
    }
    this._currentFrame = this._animator.startValue
    this._update()
}


```