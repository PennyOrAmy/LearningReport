### 编码小 Tips

#### 1. 如何跳出多重循环
##### 写若干个 break ，就像这样？
```
for (let i = 0; i < length; i++) {
  for (let j = i + 1; j < length; j++) {
    if ( ... ) {
      break;
      break;
    }
  }
}
```
错了！因为第一个 break 执行之后就跳出当前循环了，后面的 break 等于白写。
##### 正确方式是：
```
let isBreak = false;
for (let i = 0; i < length; i++) {
  for (let j = i + 1; j < length; j++) {
    if ( ... ) {
      isBreak = true;
      break;
    }
  }
  if(isBreak) {
    break;
  }
}
```
##### 如果多重循环在一个方法内，且这个方法对返回值没有特殊要求，可以用 return 跳出：
```
for (let i = 0; i < length; i++) {
  for (let j = i + 1; j < length; j++) {
    if ( ... ) {
      return;
    }
  }
}
```
&nbsp;
#### 2. boolean 参与四则运算（+ - * /）
##### true 当1用，false 当0用
&nbsp;
#### 3. 按位取反操作符（~）
##### 按位取反运算本质
```
操作数的负值减1
```
##### 按位取反的妙用
###### 字符串和数组的 indexOf 函数查找失败会返回 -1，这时可以用

```
if(~str.indexOf('str')) // 表示找到了
```
##### 两次按位取反的妙用
###### a. 将其它类型的值转换为数字类型的值，比 parseInt() 更高效，且不会返回 NaN
```
~~('')   ~~('b')   ~~('some string') 
0

~~(null)   ~~({})   ~~({a:1}) 
0

~~(undefined)   ~~(NaN)   ~~(Infinity) 
0

~~(false) 
0

~~(true) 
1

~~('3') 
3
```
###### b. 用来给数字取整，比 parseInt()、ToInt32()、Math.floor() 更高效

```
~~(1.2)
1
~~(-1.2)
-1
```

&nbsp;
#### 4. 按位与操作符（&）
##### 按位与运算过程
```
将两个数值（二进制下）的每一位对齐，按“相同位都是1得1，其它都得0”的规则，得出当前位的值
```
##### 按位与的妙用：用一个数值存储若干个 boolean 值
```
// 需要被记录的boolean型值
let isA = false, isB = true, isC = false, isD = false;
// 用来存储这些boolean型值的数值
let misc = (isA ? 1 : 0) + (isB ? 2 : 0) + (isC ? 4 : 0) + (isD ? 8 : 0);

// 从数值中还原boolean型值
isA = (misc & 1) === 1;
isB = (misc & 2) === 2;
isC = (misc & 4) === 4;
isD = (misc & 8) === 8;
```
&nbsp;
#### 5. 按位异或操作符（^）
##### 按位异或运算过程
```
将两个数值（二进制下）的每一位对齐，按“相同位的值不同得1，相同得0”的规则，得出当前位的值
```
##### 按位异或的妙用：找出数值型数组中不成对的单个值
> https://leetcode.com/explore/interview/card/top-interview-questions-easy/92/array/549/   
> Given an array of integers, every element appears twice except for one. Find that single one.

```
/**
 * @param {number[]} nums
 * @return {number}
 */
var singleNumber = function(nums) {
  let length = nums.length;
  result = nums[0];
  for (let i = 1; i < length; i++) {
    result = result ^ nums[i];
  }
  return result;
};
```