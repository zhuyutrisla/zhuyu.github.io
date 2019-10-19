---
title: Ubuntu更新nodejs-和-npm
date: 2017-12-22 17:22:40
tags: node npm
---


更新nodejs
```
sudo npm cache clean -f
sudo npm install -g n    // 安装n
sudo n stable    //安装当前稳定版    
sudo ln -sf /usr/local/n/versions/node/<VERSION>/bin/node /usr/bin/nodejs  //<VERSION>替换成版本号，具体内容到相应目录查询

```
第三步可以替换成下面命令
```
 sudo n latest //这是安装最新版nodejs
```

更新npm
```
sudo npm install npm@latest -g
```
