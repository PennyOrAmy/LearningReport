### VueRouter 源码学习笔记7 - 实践发布订阅模式
#### 概念与特点
如下图所示，发布者和订阅者之间有一个发布通道；一方面从发布者接收事件，另一方面向订阅者发布事件；订阅者需要从事件通道订阅事件  
以此可避免发布者和订阅者之间产生依赖关系，更利于解耦和重用

```
 ╭─────────────╮                 ╭───────────────╮   Fire Event   ╭──────────────╮
 │             │  Publish Event  │               │───────────────>│              │
 │  Publisher  │────────────────>│ Event Channel │                │  Subscriber  │
 │             │                 │               │<───────────────│              │
 ╰─────────────╯                 ╰───────────────╯    Subscribe   ╰──────────────╯
```
#### 用代码实践
> 需求：数据打包下载功能

定义 DataHub 类作为发布者

```
function DataHub() {}

DataHub.prototype.notify = function(url, callback) {
  callback(url);
}
```
定义 DownloadManager 类作为事件通道

```
function DownloadManager() {
  this.events = {};
  this.uId = -1;
}

DownloadManager.prototype.publish = function(eventType, url) {
  if (!this.events[eventType]) {
    return false;
  }
  var subscribers = this.events[eventType],
    count = subscribers ? subscribers.length : 0;
  while (count--) {
    var subscriber = subscribers[count];
    subscriber.handler(eventType, subscriber.taskId, url);
  }
}

DownloadManager.prototype.subscribe = function(eventType, handler) {
  if (!this.events[eventType]) {
    this.events[eventType] = [];
  }
  var taskId = (++this.uId).toString();
  this.events[eventType].push({
    taskId: taskId,
    handler: handler
  });

  return taskId;
}
```
创建一个数据中心

```
var dataHub = new DataHub();
```
创建一个下载事件管理器

```
var downloadManager = new DownloadManager();
```
创建一个下载器

```
var dataLoader = function(eventType, taskId, url) {
  console.log('Task ' + taskId + ' load data from ' + url);
}
```
用户来请求数据了

```
var downloadTask1 = downloadManager.subscribe('dataReady', dataLoader);
```
数据打包完成了

```
dataHub.notify('http://somedomain.someaddress', function(url){
  downloadManager.publish('dataReady', url);
});
```
