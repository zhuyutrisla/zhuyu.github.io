---
title: 使用HEXO和Git Pages 搭建blog
date: 2017-12-26 10:11:24
tags: hexo
---
### 环境需求 
1. 需要安装Node.js 
2. 命令行（Windows可用Git）
3. 安装Hexo 

``` 
npm install hexo-cli -g
```
### 创建Hexo


```
cd e:/xxx
hexo init blog
cd blog
npm install
```


### Hexo 常用命令
```
hexo clean       //清除缓存文件 (db.json) 和已生成的静态文件 (public)。

hexo g           //生成静态文件，会在当前目录下生成一个新的叫做public的文件夹

hexo s           //启动本地web服务，用于博客的预览  
hexo s -p 8989   //指定 8989端口

hexo d           //部署到GitHub



hexo new "postName"      //新建文章
hexo new page "pageName" //新建页面


常用组合

hexo d -g       //生成部署
hexo s -g       //生成预览


```

### 修改Hexo主题
下载主题文件放至theme文件夹

修改Hexo目录下的_config.yml配置文件中的theme属性

### Git Pages 设置
1. 新建一个repo，可先添加一个README.md, 通过setting开启

![image](/uploads/hexo1.png)图片一

![image](/uploads/hexo2.png)图片二

通过图片2得到GitHub Pages 的链接

### 部署hexo 到GitHub
先安装扩展
```
npm install hexo-deployer-git --save

```

在配置文件_config.xml中作如下修改：
```
deploy:
  type: git
  repo: https://github.com/zhuyutrisla/zhuyu.io.git     //修改成刚才创建GitHub仓库的地址
  branch: master

```
在命令行输入
```
hexo d

```
部署完之后可点击图片二生成的地址进行访问，如果没有CSS样式
可以在配置文件_config.xml中作如下修改：
```
url: http://yoursite.com
root: /
permalink: :year/:month/:day/:title/
permalink_defaults:

```

### 绑定独立域名
先在blog文件的source目录下新增CNAME文件，在CNAME文件里添加自己的域名

如```zhuyu.group```  ,保存

域名设置
域名解析里新增两条记录类型为A，记录值分别为192.30.252.153、192.30.252.154,
还有www的记录，记录值为 yourname.github.io.        
yourname为github的账号

再重新进行Git Pages 设置，图片二的地址会改变