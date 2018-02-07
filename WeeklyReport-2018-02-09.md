### 观察者（Observer）模式与订阅发布（Publish/Subscribe）模式
##### 发布订阅模式属于广义上的观察者模式
###### 发布订阅模式是最常用的一种观察者模式的实现，并且从解耦和重用角度来看，更优于典型的观察者模式
##### 发布订阅模式多了个事件通道
###### 在观察者模式中，观察者需要直接订阅目标事件；在目标发出内容改变的事件后，直接接收事件并作出响应

```
 ╭─────────────╮  Fire Event  ╭──────────────╮
 │             │─────────────>│              │
 │   Subject   │              │   Observer   │
 │             │<─────────────│              │
 ╰─────────────╯  Subscribe   ╰──────────────╯
```

###### 在发布订阅模式中，发布者和订阅者之间多了一个发布通道；一方面从发布者接收事件，另一方面向订阅者发布事件；订阅者需要从事件通道订阅事件
###### 以此避免发布者和订阅者之间产生依赖关系

```
 ╭─────────────╮                 ╭───────────────╮   Fire Event   ╭──────────────╮
 │             │  Publish Event  │               │───────────────>│              │
 │  Publisher  │────────────────>│ Event Channel │                │  Subscriber  │
 │             │                 │               │<───────────────│              │
 ╰─────────────╯                 ╰───────────────╯    Subscribe   ╰──────────────╯
```
##### 从代码看区别
> 需求：数据打包下载功能
###### 用观察者模式实现
定义一个 DownloadTask 类作为观察者

```
function DownloadTask(id) {
  this.id = id;
  this.loaded = false;
  this.url = null;
}

DownloadTask.prototype.finish = function(url) {
  this.loaded = true;
  this.url = url;
  console.log('Task ' + this.id + ' load data from ' + url);
}
```
再定义一个 DownloadTaskList 类放便管理多个下载任务

```
function DownloadTaskList() {
  this.downloadTaskList = [];
}

DownloadTaskList.prototype.getCount = function() {
  return this.downloadTaskList.length;
};

DownloadTaskList.prototype.get = function(index) {
  return this.downloadTaskList[index];
};

DownloadTaskList.prototype.add = function(obj) {
  return this.downloadTaskList.push(obj);
};

DownloadTaskList.prototype.remove = function(obj) {
  const downloadTaskCount = this.downloadTasks.getCount();
  while (i < downloadTaskCount) {
    if (this.downloadTaskList[i] === obj) {
      this.downloadTaskList.splice(i, 1);
      break;
    }
    i++;
  }
};
```
定义一个 DataHub 作为被观察目标

```
function DataHub() {
  this.downloadTasks = new DownloadTaskList();
}

DataHub.prototype.addDownloadTask = function(downloadTask) {
  this.downloadTasks.add(downloadTask);
};

DataHub.prototype.removeDownloadTask = function(downloadTask) {
  this.downloadTasks.remove(downloadTask);
};

DataHub.prototype.notify = function(url) {
  const downloadTaskCount = this.downloadTasks.getCount();
  for (var i = 0; i < downloadTaskCount; i++) {
    this.downloadTasks.get(i).finish(url);
  }
};
```
创建一个数据中心

```
var dataHub = new DataHub();
```

现在用户来取数据了，创建两个任务

```
var downloadTask1 = new DownloadTask(1);
var downloadTask2 = new DownloadTask(2);

dataHub.addDownloadTask(downloadTask1);
dataHub.addDownloadTask(downloadTask2);
```
数据打包完成了

```
dataHub.notify('http://somedomain.someaddress');
```
###### 用发布订阅模式实现
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
