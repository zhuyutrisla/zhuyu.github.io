---
title: FRAGMENT MP4相关
date: 2019-10-19 14:30:00
tags:
---
之前做过一个H5项目，项目中的一个需求进入到页面后，需要下载完宣传视频，再引导用户点击，来播放宣传视频。我最终使用了MediaSource方案来实现这个需求


## 一、首先从MediaSource讲起


> MediaSource是Media Source Extensions API 表示媒体资源HTMLMediaElement对象的接口。MediaSource 对象可以附着在HTMLMediaElement在客户端进行播放。----MDN

#### sourceBuffer

> SourceBuffer是通过MediaSource对象传递到HTMLMediaElement并播放的媒体。 它可以由一个或几个媒体段组成。

#### MediaSource方法

##### 1.MediaSource.addSourceBuffer()
```
创建一个带有给定MIME类型的新的 SourceBuffer 并添加到 MediaSource 的 SourceBuffers 列表。


例如：

let mime = 'video/mp4; codecs="avc1.64001f, mp4a.40.2"';
// codecs 为编码器信息，可通过Bento4的mp4info进行查询  https://github.com/shihuade/MP4Info

sourceBuffer = mediaSource.addSourceBuffer(mime);
```
##### 2.MediaSource.removeSourceBuffer()
```
删除指定的SourceBuffer 从这个MediaSource对象中的 SourceBuffers列表。
```
##### 3.MediaSource.endOfStream()
```
表示流的结束。

通常使用在sourceBuffer的updateend的响应事件中

如：

sourceBuffer.addEventListener('updateend', function(_) {
  mediaSource.endOfStream();
});
```

## 二、FRAGMENT MP4
MediaSource想顺畅播放得使用FRAGMENT MP4

MP4 中最基本的单元就是Box，它内部是各个 Box 拼接而成的
1. ftyp Box  
主要是 MP4 的说明，告诉解码器它的基本解码版本，兼容格式。一般都是 mp4 的第一个 box。
2. moov Box  
主要包含了视频的一些信息: 视频创建时间、视频修改时间、播放速率、时长、音量大小

3. mdat Box  包含视频媒体数据。mdat中的帧依次存放，每个帧的位置、时间、长度都由moov中的信息指定。

fmp4 不需要一个 moov Box 来进行初始化，fmp4 的 moov Box 只包含了一些 track 信息。
fmp4 的 视频/音频 metadata 信息与数据都存在一个个 moof、mdat 中，它是一个流式的封装格式。


## 三、把MP4转化成FRAGMENT MP4

[可以使用Bento4的mp4fragment](https://github.com/axiomatic-systems/Bento4)
```
mp4fragment  raw.mp4  new.mp4
```
所生成的new.mp4就是FRAGMENT MP4



## 四、下面为下载核心代码
```
let supportMediaSource = 'MediaSource' in window;

if (supportMediaSource) {

  mediaSource = new MediaSource();
  try {
    video.srcObject = mediaSource;
  } catch (error) {
    video.src = URL.createObjectURL(mediaSource);
  }
  mediaSource.addEventListener('sourceopen', sourceOpen);
} else {
  downloadVideo();
}

function sourceOpen () {
  let mime = 'video/mp4; codecs="avc1.64001f, mp4a.40.2"';
  sourceBuffer = mediaSource.addSourceBuffer(mime);
  downloadVideo();
  sourceBuffer.addEventListener('updateend', function(_) {
    mediaSource.endOfStream();
  });
}

function downloadVideo () {
  musicSrPlay = new Audio(musicSource);
  musicSrPlay.loop = true;
  videoContain.style.display = 'none';
  video.style.width = '1px';
  video.style.height = '1px';
  videoPercentWordContain.innerHTML = videoPercent + '%';
  videoPercentWordContain.style.display = 'block';
  let xhr = new XMLHttpRequest();
  xhr.open('GET',
    'http://nickdesaulniers.github.io/netfix/demo/frag_bunny.mp4',
    true);
  xhr.responseType = 'arraybuffer';
  xhr.setRequestHeader('Cache-Control', 'public, max-age=31536000');
  xhr.send();
  xhr.onload = function() {
    if (xhr.status !== 200) {
      return;
    }
    if (supportMediaSource) {
      sourceBuffer.appendBuffer(xhr.response);
    } else {
      // 转换成文件格式
      let binaryData = new Uint8Array(xhr.response);
      // 生成一个本地的url
      let rUrl = window.URL.createObjectURL(new Blob([binaryData], {
        type: 'video/mp4'
      }));
      video.src = rUrl;
    }

  };
  xhr.onerror = function() {
    console.log('video请求失败了');
  };
  xhr.onprogress = function(event) {
    //  下载进度
    if (event.lengthComputable) {
      videoPercent = Math.round((event.loaded * 100) / event.total);
      videoPercentWordContain.innerHTML = videoPercent + '%';
      if (videoPercent === 100) {
        //  下载视频完成
  };
}

```