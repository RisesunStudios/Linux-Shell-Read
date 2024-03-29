# 了解Chrome 进程模型 

- [ ] 笔记时间: 2021.7.18
- [ ] 为了理解electron的chromium原理而学习

## 1 简介

### 1.1 设计思路

- 目的是 简单、稳定、高效、安全、开源
- 当时浏览器是单线程,一旦js代码卡死就完蛋了,所以设计了异步API,假如使用多线程的话,一旦某些线程挂掉,进程也会被拖垮.于是采用多进程
- ![image-20210718115327480](images/image-20210718115327480.png)

- 当一个窗口(进程)被关闭,内存就会被释放,插件也会占用进程

### 1.2 webkit 和 V8

- WebKit 是一个渲染引擎
- V8是一个虚拟机,可以引入隐藏类转换等,可以共享隐藏类,生成内部解释再运行,直接编译成机器码.使用了精确垃圾回收机制
- chrome 调用的是 V8 的API,内核没有集成进去,所以别的项目可以使用

### 1.3 沙盒模式

- 每个窗口都运行在沙盒模式,类似Windows Vista的授权机制,不能访问硬盘,鼠标等
- 但是插件是例外,没有再沙盒里,可以访问不同标签