### Vue Router 源码学习笔记6 - 试用Object.defineProperty

#### Object.defineProperty 的定义
MDN：
> Object.defineProperty(obj, prop, descriptor)  
> 
> 描述：  
> 该方法允许精确添加或修改对象的属性
> 
> 参数：  
> obj  要在其上定义属性的对象  
> prop  要定义或修改的属性的名称  
> descriptor  将被定义或修改的属性描述符  
> 
> > 在 descriptor 中可以设置：configurable, enumerable, value, writable, get, set
> 
> 返回值：  
> 被传递给函数的对象

#### 用代码体验
1. 添加或修改对象的属性
```
let obj = {
  existingProp: '123'
}

// 既可以用在已有的属性上
Object.defineProperty(obj, 'existingProp', {})
// 又可以定义一个新属性，加以设置
Object.defineProperty(obj, 'newProp', {})

// 参数 descriptor 为必传参数，不传报错，但可以传空对象，呵呵哒

console.log(obj)
// output:
// {existingProp: "123", newProp: undefined}
```
2. configurable 为 true 时，属性才可删或再次修改特性（configurable, enumerable，writable）；默认为 false
```
let obj = {}

Object.defineProperty(obj, 'annoyingProp', {
  configurable: false,
  value: 'annoying'
})
delete obj.annoyingProp 
// 不会生效，看着不爽，却干不掉它

console.log(obj)
// output:
// {annoyingProp: "annoying"}
```
3. enumerable 为 true 时，属性才能被遍历出来；默认为 false
```
let obj = {
  oneProp: 'one'
}

Object.defineProperty(obj, 'sevenProp', {
  enumerable: false,
  value: 'seven'
})
// sevenProp 不会被遍历出来，七娃是隐身的，不会被看见

for(key in obj) {
  console.log(key)
}
// output:
// oneProp
// 能看见大娃
```
4. writable 为 true 时，属性值才能被修改；默认为 false

```
let obj = {}

Object.defineProperty(obj, 'uglyProp', {
  writable: false,
  value: 'ugly'
})
obj.uglyProp = 'beautiful' 
// 不会生效，丑就是丑，不可能变美

console.log(obj)
// output:
// {uglyProp: "ugly"} 
```
5. 定义一个数据描述符类型（Data Descriptors）的属性
```
let obj = {}

Object.defineProperty(obj, 'dataProp', {
  enumerable: true,
  writable: true,
  value: 'data'
})
obj.dataProp = 'changedData'

console.log(obj)
// output:
// {dataProp: "changedData"}
```
6. 定义一个存取描述符类型（Accessor Descriptor）的属性
```
let obj = {}
let innerValue = 'innerValue'

Object.defineProperty(obj, 'accessorProp', {
  enumerable: true,
  get() {
    return innerValue
  },
  set(value) {
    innerValue = value
    console.log('值改变了，该干嘛记得干嘛')
    // vue 的响应式原理，就是在 set 方法中添加属性值改变需要做的处理
  }
})
obj.accessorProp = 'changedInnerValue'

console.log(obj)
// output:
// "值改变了，该干嘛记得干嘛"
// {accessorProp: "changedInnerValue"}
```
7. 数据描述符专用的 value 和 writable，不能与存取描述符专用的 get 和 set 混用
```
let obj = {}

Object.defineProperty(obj, 'thought', {
  value: '朝秦',
  get() {
    return '暮楚'
  }
})

console.log(obj)
// output:
// Uncaught TypeError: Invalid property descriptor. Cannot both specify accessors and a value or writable attribute, #<Object>
// 不可以朝秦暮楚
```

#### 下一步
实践发布订阅模式