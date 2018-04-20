### Promise 异步编程

#### Promise 解决的问题

##### 同步与异步

同步需要等待结果，结果未出 block 整个流程。
异步不阻断后面的流程，结果什么时候返回再执行操作。

##### 轮询与回调

获取异步操作结果有轮询和回调两种方式。  

假设有一个大数据量的耗时查询，查询等待时间甚至超过用户登陆的 session  过期的时间。查询请求发往后端后，由于后端无法在查询完成后主动推送查询结果，因而需要前端每隔一会就去后端询问一下查询情况。这就是轮询。

回调，顾名思义就是回头再调用，那么就需要在异步操作完成时，还能访问到处理异步操作结果的方法。

回调本身没有问题，但如果流程复杂，一环套一环，层层嵌套，会令代码变得难以阅读，难以维护。这就是 [callback hell](http://callbackhell.com/)：

callback hell 正是 Promise 要解决的问题。

#### 使用 Promise

Promise 最早由社区提出和实现，ES6 已将其纳入标准，提供原生支持。Promise 让开发者可以通过链式调用的方式处理业务逻辑，解决回调嵌套的问题。  

假设这样一个需求，我们需要渲染一个数据表，表头和表数据分两个接口。  
这里我们用 Promise 作为异步获取接口数据的方案：

```
// 获取表头
function getTableHeader() {
  return new Promise((resolve, reject) => {
    console.log('Try to get table header');
    setTimeout(() => {
      if (Math.random() > 0.5) {
        resolve('table header');
        console.log('Got table header');
      } else {
        reject('Cannot get table header');
      }
    }, 1000);
  });
}

// 获取表数据
function getTableData() {
  return new Promise((resolve, reject) => {
    console.log('Try to get table data');
    setTimeout(() => {
      if (Math.random() > 0.5) {
        resolve('table data');
        console.log('Got table data');
      } else {
        reject('Cannot get table data');
      }
    }, 1000);
  });
}
```
其它辅助方法：
```
// 渲染表格
function renderTable(tableHeader, tableData) {
  console.log('We have got ' + tableHeader + ' and ' + tableData + ', now we can render table');
}

// 处理异常
function handleError(e) {
  console.log(e);
}

// 结束流程
function endProcess() {
  console.log('End process');
}
```
现在我们需要一个 buildTable 方法，把流程贯穿起来：
```
function buildTable() {
  let tableHeader;
  getTableHeader().then((th) => {
    tableHeader = th;
    return getTableData();
  }).then((tableData) => {
    renderTable(tableHeader, tableData);
  }).catch((e) => {
    handleError(e);
  }).then(() => {
    endProcess();
  });
}

buildTable();
```
如果获取表头和表数据都成功，输出是这样的：
```
Try to get table header
Got table header
Try to get table data
Got table data
We have got table header and table data, now we can render table
End process
```
和我们想象的一样，获取表头和获取表数据是依次执行的。假如获取表头失败，就不会再去获取表数据，直接结束流程，节省了资源。但依次执行花的时间更多。

#### 使用 Promise.all
buildTable 方法还可以用 Promise.all 来改写，如下：

```
function buildTable() {
  Promise.all([getTableHeader(), getTableData()]).then(function(data) {
    renderTable(data[0], data[1]);
  }, function(e) {
    handleError(e);
  }).then(() => {
    endProcess();
  });
}
```
在获取表头和表数据都成功的时候，输出是这样的：

```
Try to get table header
Try to get table data
Got table header
Got table data
We have got table header and table data, now we can render table
End process
```
也就是说，获取表头和获取表数据是同时开始的，就像 jQuery 里的 $.when。同时请求节约了时间，但如果获取表头是失败的，获取表数据就是多余的，浪费了资源。  
是否用 Promise.all 需要看业务场景。

#### Promise 与 Generator  结合
使用 function* 语法可以定义出 GeneratorFunction，这是一种可以分步执行的特殊函数，给需要分步执行的语句加上 yield 关键词（有点像打断点），而触发函数一步一步的执行，是通过调用函数返回的 Generator 对象的 next 方法做到的。每调用一次 next，函数就向下执行异步，直到完成。  

下面将 Generator 与 Promise 结合，再次改写 buildTable：
```
function* buildTable() {
  try {
    let tableHeader = yield getTableHeader();
    let tableData = yield getTableData();
    if (tableHeader && tableData) {
      yield renderTable(tableHeader, tableData);
    }
  } catch (e) {
    handleError(e);
  }
  yield endProcess();
}
```
可以看到，在 Generator 版本的 buildTable 中，只有同步执行般的、逻辑清晰的业务流程，异步过程被隐藏起来。但 Generator 需要一个辅助方法，也就是执行器，帮它迭代执行：
```
function run(generator) {
  const g = generator();

  function go(result) {
    if (result.done) return result.value;

    if ((typeof result.value === 'object') && (result.value instanceof Promise)) {
      return result.value.then(function(value) {
        return go(g.next(value));
      }, function(error) {
        return go(g.throw(error));
      });
    } else {
      go(g.next());
    }
  }

  go(g.next());
}

run(buildTable);
```
如果把 run 方法作为公用库中的方法（就像 co 模块那样），不当作额外成本，Generator 方式是更好的选择，更易写、易维护。


#### Await / Async
await 是等待执行标识符，async 是申明函数是异步的。继续套路，改写 buildTable：

```
async function buildTable() {
  try {
    let tableHeader = await getTableHeader();
    let tableData = await getTableData();
    if (tableHeader && tableData) {
      renderTable(tableHeader, tableData);
    }
  } catch (e) {
    handleError(e);
  }
  endProcess();
}
```
看起来和 Generator 版本的很像，因为 async 函数其实就是 Generator 函数的语法糖。 但它自带执行器，也不需要用 .next() 去触发下一步执行，一次调用即可。

#### 小心得
一路改写 buildTable，还是 await / async 方案最好用。不过它是 ES2017 的语法，需要 babel 这样的工具进行预编译。

#### 小贴士
主动触发失败事件：

```
return Promise.reject(new Error())
```

#### 在线演示
[TryAsyncAwait](https://codepen.io/PennyOrAmy/full/qYdNEM)  

#### 参考资料
[JavaScript Promise：简介](https://developers.google.com/web/fundamentals/primers/promises)   
[Generator 函数的含义与用法](http://www.ruanyifeng.com/blog/2015/04/generator.html)    
[Javascript 异步编程的4种方法](http://www.ruanyifeng.com/blog/2012/12/asynchronous%EF%BC%BFjavascript.html)  
[Callback、Promise、Generator、async/await对比](https://www.lazycoffee.com/articles/view?id=58ab09eea072b332753d9774)
