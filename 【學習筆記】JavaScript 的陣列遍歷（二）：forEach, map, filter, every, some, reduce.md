###### tags: `JavaScript` `Front-End` `array`
# 【學習筆記】JavaScript 的陣列遍歷（二）：forEach/map/filter/every/some/reduce

[TOC]

接續上篇[【學習筆記】JavaScript 的陣列遍歷（ㄧ）：for/for...of/for...in/forEach](https://hackmd.io/@Heidi-Liu/javascript-for-loop)，這篇筆記整理幾種 JavaScript 遍歷陣列的方法，探討其使用時機與彼此的區別：

+ `arr.forEach((value, index, array) => { // todo... })`
+ `arr.map(value => { // todo... })`
+ `arr.filter(value => { // todo... })`
+ `arr.every(value => { // todo... })`
+ `arr.some(value => { // todo... })`
+ `arr.reduce((acc, value)=> acc + value)`

## [forEach](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach)：遍歷陣列，無返回值，會改變原來陣列

+ 用法與 for loop 類似
+ 會改變原始陣列

```javascript=
array.forEach(function callback(currentValue, index?, array?){
    // TODO
}, thisArg?)
```

+ 使用範例：

```javascript=
let arr = [1, 3, 5, 11, -2, 8];
arr.forEach((item, index, array) => {
    array[index] = item * 2;
});

console.log('arr: ', arr);
// arr: [2, 6, 10, 22, -4, 16]
```

## [map](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/map)：遍歷陣列，返回處理後的新陣列

+ 不會改變原始陣列
+ 會返回新陣列

```javascript=
let newArray = arr.map(function callback( currentValue, index?, array?) {
    // return element for newArray
}, thisArg?)
```

+ 使用範例：
  + 常用於修改陣列中的資料格式，得到返回值後再執行後續動作

```javascript=
let arr = [1, 3, 5, 11, -2, 8];
let newArr = arr.map(item => item * 2);

console.log('arr: ', arr);
console.log('newArr ', newArr);
// arr: [1, 3, 5, 11, -2, 8]
// NewArr: [2, 6, 10, 22, -4, 16]
```

## [filter](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)：篩選陣列中滿足條件的元素，並返回一個新陣列

+ 不會改變原始陣列
+ 會返回新陣列

```javascript=
let newArray = arr.filter(callback(element, index, array), thisArg?)
```

+ 使用範例：
  + 常用於篩選陣列中符合搜尋條件的元素

```javascript=
let arr = [1, 3, 5, 11, -2, 8];
let newArr = arr.filter(item => item < 5);

console.log('arr: ', arr);
console.log('newArr: ', newArr);
// arr: [1, 3, 5, 11, -2, 8]
// newArr: [1, 3, -2]
```

## [every](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/every)：判斷陣列中所有元素是否都滿足條件，返回一個布林值

```javascript=
arr.every(callback, thisArg?)
```

+ 使用範例：

```javascript=
let arr = [1, 3, 5, 11, -2, 8];
let allSmallerThan10 = arr.every(x => x < 10);
let allSmallerThan15 = arr.every(x => x < 15);

console.log('allSmallerThan10: ', allSmallerThan10);
console.log('allSmallerThan15: ', allSmallerThan15);
// allSmallerThan10: false
// allSmallerThan15: true
```

## [some](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/some)：判斷陣列中是否至少存在一個滿足條件的元素，返回一個布林值

```javascript=
arr.some(callback, thisArg?)
```

+ 使用範例：

```javascript=
let arr = [1, 3, 5, 11, -2, 8];
let isBiggerThan10 = arr.some(x => x > 10);
let isBiggerThan15 = arr.some(x => x > 15);

console.log('isBiggerThan10: ', isBiggerThan10);
console.log('isBiggerThan15: ', isBiggerThan15);
// isBiggerThan10: true
// isBiggerThan15: false
```

## [reduce](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)：將陣列中所有元素加總計算，返回一個總和

+ callback 傳入參數：
  + accumulator：目前累加值
  + currentValue：目前執行到的元素值
  + currentIndex：目前執行到的元素索引值
  + array：陣列本身
+ initialValue：初始累加值 => 若無指定則預設為 0

```javascript=
arr.reduce(
  callback[accumulator, currentValue, currentIndex?, array?],
  initialValue?
)
```

+ 使用範例：

```javascript=
let total = [1, 2, 3].reduce(
  (accumulator, currentValue) => {
    console.log('accumulator: ', accumulator);
    console.log('currentValue: ', currentValue);
    return accumulator + currentValue;
  }
);
console.log('total: ', total);
// accumulator: 1
// currentValue: 2
// accumulator: 3
// currentValue: 3
// total: 6
```


## 參考資料

+ [陣列遍歷的幾種用法---forEach()、map()、filter()、every()、some()](https://www.itread01.com/content/1546826064.html)
+ [Day09_Array的迭代方法](https://ithelp.ithome.com.tw/articles/10194412)
