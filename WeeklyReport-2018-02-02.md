### 编码小Tips

#### 1. 如何跳出多重循环
##### 写若干个break，就像这样？
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
错了！因为第一个break执行之后就跳出当前循环了，后面的break等于白写。
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
##### 如果多重循环在一个方法内，且这个方法对返回值没有特殊要求，可以用return 跳出：
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
#### 2. boolean参与四则运算（+ - * /）
##### true当1用，false当0用
&nbsp;
#### 3. 按位取反操作符（~）
##### 按位取反运算本质
```
操作数的负值减1
```
##### 两次按位取反的妙用：将其它类型的值转换为数字类型的值
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
&nbsp;
#### 4. 按位与操作符（&）
##### 按位与运算过程
```
将两个数值（二进制下）的每一位对齐，按“相同位都是1得1，其它都得0”的规则，得出当前位的值
```
##### 按位与的妙用：用一个数值存储若干个boolean值
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