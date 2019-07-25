## 正则扩展
```
{
     let regex=new RegExp('xyz','i');
     let regex2=new RegExp('/xyz/i');
     console.log(regex.test('xyz123'),regex2.test('xyz123'));

// es6允许后面的修饰符覆盖前面的，新增获取修饰符的方法flags
     let regex3=new RegExp(/xyz/ig,'i'); 
     console.log(regex3.flags);  //i   后面的修饰符覆盖了前面的修饰符
 }
```
知识点2：y修饰符(紧接着查找后面)
```
// y修饰符
 {
    let s='bbb_bb_b';
    let a1=/b+/g;
    let a2=/b+/y;

    console.log('one',a1.exec(s),a2.exec(s));  
    //one ["bbb", index: 0, input: "bbb_bb_b", groups: undefined]
    // ["bbb", index: 0, input: "bbb_bb_b", groups: undefined]
    console.log('two',a1.exec(s),a2.exec(s));
// two ["bb", index: 4, input: "bbb_bb_b", groups: undefined] null
    // y跟g都是全局匹配，g不强调上次的匹配位置，只要后面任何位置匹配到都可以，而y修饰符是从紧接着在第一次匹配的位置后面
    // sticky检查是否有y修饰符
    console.log(a1.sticky,a2.sticky);
 }
```
**知识点3：u修饰符**
```
{
     // 加u时\uD83D\uDC2A是同一个字符，而加时是不同的两个字符
     console.log('u-1',/^\uD83D/.test('\uD83D\uDC2A'));  //u-1 true
     console.log('u-2',/^\uD83D/u.test('\uD83D\uDC2A')); //u-2 false
 }

```
大于2个字节的匹配符时需要使用u

**知识点4：s修饰符**
## 新增查找字符串方法
在ES5中只有indexof一个方法来查找字符串，在ES6中新增了includes(), startsWith(), endsWith() 

- includes()：返回布尔值，表示是否找到了参数字符串。
- startsWith()：返回布尔值，表示参数字符串是否在原字符串的头部。
- endsWith()：返回布尔值，表示参数字符串是否在原字符串的尾部。

```
let s = 'Hello world!';

s.startsWith('world', 6) // true
s.endsWith('Hello', 5) // true
s.includes('Hello', 6) // false
```
#### repeat() 方法返回一个新字符串，表示将原字符串重复n次。
```
'x'.repeat(3) // "xxx"
'hello'.repeat(2) // "hellohello"
'na'.repeat(0) // ""
```
#### 补全字符串功能
ES2017 引入了字符串补全长度的功能。如果某个字符串不够指定长度，会在头部或尾部补全。padStart()用于头部补全，padEnd()用于尾部补全。
```
'x'.padStart(5, 'ab') // 'ababx'
'x'.padStart(4, 'ab') // 'abax'

'x'.padEnd(5, 'ab') // 'xabab'
'x'.padEnd(4, 'ab') // 'xaba'
```
## ES6模板字符串之标签模板
首先，**模板字符串**和**标签模板**是两个东西。

标签模板不是模板，而是函数调用的一种特殊形式。“标签”指的就是函数，紧跟在后面的模板字符串就是它的参数。
但是，如果模板字符串中有变量，就不再是简单的调用了，而是要将模板字符串先处理成多个参数，再调用函数。

```
var a = 5;
var b = 10;
 
tag`Hello ${ a + b } world ${ a * b }`;
//等同于
tag(['Hello ', ' world ', ''], 15, 50);
```
这里我产生了一个疑问，为什么数组第三个参数是空字符串。

看下一段代码 ：
```
var total = 30;
var msg = passthru`The total is ${total} (${total * 1.05} with tax)`;
...
//由此可以得出 上面的方法等同于
var msg = passthru(['The total is ', ' (', ' with tax'], 30, 31.5)
```
这段代码中数组参数并没有产生空字符串。百度后找到原因。

模板字符串由**变量**和**非变量**组成，什么是变量，```${}```就是变量。模板标签函数调用的第一个参数是数组，是由模板字符串中那些非变量部分组成，包括空格。

那么不难理解，假设模板字符串中的变量为```A```，非变量为```B```，那么模板字符串的组成可能就是:
```
tpl1 = ABABA; -> ['', B, B, '']
tpl2 = BAB;   -> [B, B]
tpl3 = ABAB   -> ['', B, B]
...
```
可以解读到：模板字符串中变量必须徐是由非变量包含着的，如果变量在开始位置或者结束位置且没有非变量包含，那么该位置就是空字符串。
## 数值的扩展
### 1.二进制和八进制表示
ES6 提供了二进制和八进制数值的新的写法，分别用前缀```0b```（或```0B```）和```0o```（或```0O```）表示。
```
0b111110111 === 503 // true
0o767 === 503 // true
```
如果要将0b和0o前缀的字符串数值转为十进制，要使用Number方法。
```
Number('0b111')  // 7
Number('0o10')  // 8
```
### 2.Number.isFinite(),Number.isNaN()
```Number.isFinite()```用来检查一个数值是否为有限的（finite），即不是```Infinity```。
```
Number.isFinite(15); // true
Number.isFinite(0.8); // true
Number.isFinite(NaN); // false
Number.isFinite(Infinity); // false
Number.isFinite(-Infinity); // false
Number.isFinite('foo'); // false
Number.isFinite('15'); // false
Number.isFinite(true); // false
```
```Number.isNaN()```用来检查一个值是否为```NaN```。
```
Number.isNaN(NaN) // true
Number.isNaN(15) // false
Number.isNaN('15') // false
Number.isNaN(true) // false
Number.isNaN(9/NaN) // true
Number.isNaN('true' / 0) // true
Number.isNaN('true' / 'true') // true
```
### 3.Number.isInteger()
```Number.isInteger()```用来判断一个数值是否为整数。
```
Number.isInteger(25) // true
Number.isInteger(25.1) // false
Number.isInteger(25) // true
Number.isInteger(25.0) // true
Number.isInteger() // false
Number.isInteger(null) // false
Number.isInteger('15') // false
Number.isInteger(true) // false
```
#### 4.安全整数和Number.isSafeInteger()
JavaScript 能够准确表示的整数范围在-2^53到2^53之间（不含两个端点），超过这个范围，无法精确表示这个值。

ES6 引入了```Number.MAX_SAFE_INTEGER```和```Number.MIN_SAFE_INTEGER```这两个常量，用来表示这个范围的上下限。

```Number.isSafeInteger()```则是用来判断一个整数是否落在这个范围之内。

```
Number.isSafeInteger('a') // false
Number.isSafeInteger(null) // false
Number.isSafeInteger(NaN) // false
Number.isSafeInteger(Infinity) // false
Number.isSafeInteger(-Infinity) // false

Number.isSafeInteger(3) // true
Number.isSafeInteger(1.2) // false
Number.isSafeInteger(9007199254740990) // true
Number.isSafeInteger(9007199254740992) // false

Number.isSafeInteger(Number.MIN_SAFE_INTEGER - 1) // false
Number.isSafeInteger(Number.MIN_SAFE_INTEGER) // true
Number.isSafeInteger(Number.MAX_SAFE_INTEGER) // true
Number.isSafeInteger(Number.MAX_SAFE_INTEGER + 1) // false
```
### 5.Math 对象的扩展
#### Math.trunc()
```Math.trunc()```方法用于去除去一个数的小数部分，返回整数部分。
```
Math.trunc(4.1) // 4
Math.trunc(4.9) // 4
Math.trunc(-4.1) // -4
Math.trunc(-4.9) // -4
Math.trunc(-0.1234) // -0
```
对于非数值，```Math.trunc```内部使用```Number````方法将其先转为数值。
```
Math.trunc('123.456') // 123
Math.trunc(true) //1
Math.trunc(false) // 0
Math.trunc(null) // 0
```
对于空值和无法截取整数的值，返回```NaN```。
```
Math.trunc(NaN);      // NaN
Math.trunc('foo');    // NaN
Math.trunc();         // NaN
Math.trunc(undefined) // NaN
```
#### Math.sign()
```Math.sign```方法用来判断一个数到底是正数、负数、还是零。对于非数值，会先将其转换为数值。

它会返回五种值。

-    参数为正数，返回+1；
- 参数为负数，返回-1；
- 参数为 0，返回0；
- 参数为-0，返回-0;
- 其他值，返回NaN。

```
Math.sign(-5) // -1
Math.sign(5) // +1
Math.sign(0) // +0
Math.sign(-0) // -0
Math.sign(NaN) // NaN
```
如果参数是非数值，会自动转为数值。对于那些无法转为数值的值，会返回```NaN```。
```
Math.sign('')  // 0
Math.sign(true)  // +1
Math.sign(false)  // 0
Math.sign(null)  // 0
Math.sign('9')  // +1
Math.sign('foo')  // NaN
Math.sign()  // NaN
Math.sign(undefined)  // NaN
```

#### Math.cbrt()
```Math.cbrt```方法用于计算一个数的立方根。
### 数组的扩展
#### 1. 扩展运算符
#### 含义

扩展运算符（spread）是三个点（```...```）。它好比 rest 参数的逆运算，将一个数组转为用逗号分隔的参数序列。

该运算符主要用于函数调用。
```
function push(array, ...items) {
  array.push(...items);
}

function add(x, y) {
  return x + y;
}

const numbers = [4, 38];
add(...numbers) // 42
```
#### 替代函数的apply方法
由于扩展运算符可以展开数组，所以不再需要```apply```方法，将数组转为函数的参数了。
```
// ES5 的写法
function f(x, y, z) {
  // ...
}
var args = [0, 1, 2];
f.apply(null, args);

// ES6的写法
function f(x, y, z) {
  // ...
}
let args = [0, 1, 2];
f(...args);
```
下面是扩展运算符取代```apply```方法的一个实际的例子，应用```Math.max```方法，简化求出一个数组最大元素的写法。
```
// ES5 的写法
Math.max.apply(null, [14, 3, 77])

// ES6 的写法
Math.max(...[14, 3, 77])

// 等同于
Math.max(14, 3, 77);
```
通过```push```函数，将一个数组添加到另一个数组的尾部。
```
// ES5的 写法
var arr1 = [0, 1, 2];
var arr2 = [3, 4, 5];
Array.prototype.push.apply(arr1, arr2);

// ES6 的写法
let arr1 = [0, 1, 2];
let arr2 = [3, 4, 5];
arr1.push(...arr2);
```
####  扩展运算符的应用
##### （1）复制数组
数组是复合的数据类型，直接复制的话，只是复制了指向底层数据结构的指针，而不是克隆一个全新的数组。
```
const a1 = [1, 2];
const a2 = a1;

a2[0] = 2;
a1 // [2, 2]
```
上面代码中，```a2```并不是```a1```的克隆，而是指向同一份数据的另一个指针。修改```a2```，会直接导致```a1```的变化。

ES5 只能用变通方法来复制数组。
##### （2）合并数组
扩展运算符提供了数组合并的新写法。
```
const arr1 = ['a', 'b'];
const arr2 = ['c'];
const arr3 = ['d', 'e'];

// ES5 的合并数组
arr1.concat(arr2, arr3);
// [ 'a', 'b', 'c', 'd', 'e' ]

// ES6 的合并数组
[...arr1, ...arr2, ...arr3]
// [ 'a', 'b', 'c', 'd', 'e' ]
```
不过，这两种方法都是浅拷贝，使用的时候需要注意。
##### （3）与解构赋值结合
扩展运算符可以与解构赋值结合起来，用于生成数组。
```
const [first, ...rest] = [1, 2, 3, 4, 5];
first // 1
rest  // [2, 3, 4, 5]

const [first, ...rest] = [];
first // undefined
rest  // []

const [first, ...rest] = ["foo"];
first  // "foo"
rest   // []
```
如果将扩展运算符用于数组赋值，只能放在参数的最后一位，否则会报错。
##### （4）字符串
扩展运算符还可以将字符串转为真正的数组。
```
[...'hello']
// [ "h", "e", "l", "l", "o" ]
```
上面的写法，有一个重要的好处，那就是能够正确识别四个字节的 Unicode 字符。
```
'x\uD83D\uDE80y'.length // 4
[...'x\uD83D\uDE80y'].length // 3
```
上面代码的第一种写法，JavaScript 会将四个字节的 Unicode 字符，识别为 2 个字符，采用扩展运算符就没有这个问题。因此，正确返回字符串长度的函数，可以像下面这样写。
```
function length(str) {
  return [...str].length;
}

length('x\uD83D\uDE80y') // 3
```
##### （5）实现了 Iterator 接口的对象
##### （6）Map 和 Set 结构，Generator 函数
### 2. Array.from()
```Array.from```方法用于将两类对象转为真正的数组：类似数组的对象（array-like object）和可遍历（iterable）的对象（包括 ES6 新增的数据结构 Set 和 Map）。

下面是一个类似数组的对象，```Array.from```将它转为真正的数组。
```
let arrayLike = {
    '0': 'a',
    '1': 'b',
    '2': 'c',
    length: 3
};

// ES5的写法
var arr1 = [].slice.call(arrayLike); // ['a', 'b', 'c']

// ES6的写法
let arr2 = Array.from(arrayLike); // ['a', 'b', 'c']
```
**只要是部署了 Iterator 接口的数据结构，Array.from都能将其转为数组。**

### 3. Array.of()
```Array.of```方法用于将一组值，转换为数组。
```
Array.of(3, 11, 8) // [3,11,8]
Array.of(3) // [3]
Array.of(3).length // 1

Array() // []
Array(3) // [, , ,]
Array(3, 11, 8) // [3, 11, 8]

Array.of() // []
Array.of(undefined) // [undefined]
Array.of(1) // [1]
Array.of(1, 2) // [1, 2]
```
### 4. 数组实例的 copyWithin() 
数组实例的```copyWithin()```方法，在当前数组内部，将指定位置的成员复制到其他位置（会覆盖原有成员），然后返回当前数组。也就是说，使用这个方法，会修改当前数组。
```
Array.prototype.copyWithin(target, start = 0, end = this.length)
```
它接受三个参数。
- target（必需）：从该位置开始替换数据。如果为负值，表示倒数。
- start（可选）：从该位置开始读取数据，默认为 0。如果为负值，表示从末尾开始计算。
- end（可选）：到该位置前停止读取数据，默认等于数组长度。如果为负值，表示从末尾开始计算。

这三个参数都应该是数值，如果不是，会自动转为数值。
```
[1, 2, 3, 4, 5].copyWithin(0, 3)
// [4, 5, 3, 4, 5]
/ 将3号位复制到0号位
[1, 2, 3, 4, 5].copyWithin(0, 3, 4)
// [4, 2, 3, 4, 5]

// -2相当于3号位，-1相当于4号位
[1, 2, 3, 4, 5].copyWithin(0, -2, -1)
// [4, 2, 3, 4, 5]

// 将3号位复制到0号位
[].copyWithin.call({length: 5, 3: 1}, 0, 3)
// {0: 1, 3: 1, length: 5}

// 将2号位到数组结束，复制到0号位
let i32a = new Int32Array([1, 2, 3, 4, 5]);
i32a.copyWithin(0, 2);
// Int32Array [3, 4, 5, 4, 5]

// 对于没有部署 TypedArray 的 copyWithin 方法的平台
// 需要采用下面的写法
[].copyWithin.call(new Int32Array([1, 2, 3, 4, 5]), 0, 3, 4);
// Int32Array [4, 2, 3, 4, 5]
```
### 5. 数组实例的 find() 和 findIndex()
数组实例的```find```方法，用于找出第一个符合条件的数组成员。它的参数是一个回调函数，所有数组成员依次执行该回调函数，直到找出第一个返回值为```true```的成员，然后返回该成员。如果没有符合条件的成员，则返回```undefined```。
```
[1, 4, -5, 10].find((n) => n < 0)
// -5
```

```
[1, 5, 10, 15].find(function(value, index, arr) {
  return value > 9;
}) // 10
```
上面代码中，```find```方法的回调函数可以接受三个参数，依次为当前的值、当前的位置和原数组。

数组实例的```findIndex```方法的用法与```find```方法非常类似，返回第一个符合条件的数组成员的位置，如果所有成员都不符合条件，则返回-1。
```
[1, 5, 10, 15].findIndex(function(value, index, arr) {
  return value > 9;
}) // 2
```
这两个方法都可以接受第二个参数，用来绑定回调函数的```this```对象。
```function f(v){
  return v > this.age;
}
let person = {name: 'John', age: 20};
[10, 12, 26, 15].find(f, person);    // 26

```
这两个方法都可以发现```NaN````，弥补了数组的```indexOf```方法的不足。
```
[NaN].indexOf(NaN)
// -1

[NaN].findIndex(y => Object.is(NaN, y))
// 0
```
### 6. 数组实例的 fill()
```fill```方法使用给定值，填充一个数组。
```
['a', 'b', 'c'].fill(7)
// [7, 7, 7]

new Array(3).fill(7)
// [7, 7, 7]
```
```fill```方法还可以接受第二个和第三个参数，用于指定填充的起始位置和结束位置。
```
['a', 'b', 'c'].fill(7, 1, 2)
// ['a', 7, 'c']
```
注意，如果填充的类型为对象，那么被赋值的是同一个内存地址的对象，而不是深拷贝对象。
### 7. 数组实例的 entries()，keys() 和 values() 
ES6 提供三个新的方法——```entries()```，```keys()```和```values()```——用于遍历数组。它们都返回一个遍历器对象（详见《Iterator》一章），可以用```for...of```循环进行遍历，唯一的区别是```keys()```是对键名的遍历、```values()```是对键值的遍历，```entries()```是对键值对的遍历。
```
for (let index of ['a', 'b'].keys()) {
  console.log(index);
}
// 0
// 1

for (let elem of ['a', 'b'].values()) {
  console.log(elem);
}
// 'a'
// 'b'

for (let [index, elem] of ['a', 'b'].entries()) {
  console.log(index, elem);
}
// 0 "a"
// 1 "b"
```
### 8. 数组实例的 includes()
```Array.prototype.includes```方法返回一个布尔值，表示某个数组是否包含给定的值，与字符串的includes方法类似。
```
[1, 2, 3].includes(2)     // true
[1, 2, 3].includes(4)     // false
[1, 2, NaN].includes(NaN) // true
```
该方法的第二个参数表示搜索的起始位置，默认为```0```。如果第二个参数为负数，则表示倒数的位置，如果这时它大于数组长度（比如第二个参数为```-4```，但数组长度为```3```），则会重置为从```0```开始。
```
[1, 2, 3].includes(3, 3);  // false
[1, 2, 3].includes(3, -1); // true
```
```indexOf```方法有两个缺点，一是不够语义化，它的含义是找到参数值的第一个出现位置，所以要去比较是否不等于```-1```，表达起来不够直观。二是，它内部使用严格相等运算符（```===```）进行判断，这会导致对```NaN```的误判。

```includes```使用的是不一样的判断算法，就没有这个问题。
## 函数扩展
### 1. 函数参数的默认值
ES6 允许为函数的参数设置默认值，即直接写在参数定义的后面。
```
function log(x, y = 'World') {
  console.log(x, y);
}

log('Hello') // Hello World
log('Hello', 'China') // Hello China
log('Hello', '') // Hello
```
参数变量是默认声明的，所以不能用```let```或```const```再次声明。

使用参数默认值时，函数不能有同名参数。
#### 函数的 length 属性 
指定了默认值以后，函数的```length```属性，将返回没有指定默认值的参数个数。也就是说，指定了默认值后，```length```属性将失真。
#### 作用域
一旦设置了参数的默认值，函数进行声明初始化时，参数会形成一个单独的作用域（context）。等到初始化结束，这个作用域就会消失。这种语法行为，在不设置参数默认值时，是不会出现的。
```
var x = 1;

function f(x, y = x) {
  console.log(y);
}

f(2) // 2

let x = 1;

function f(y = x) {
  let x = 2;
  console.log(y);
}

f() // 1

function f(y = x) {
  let x = 2;
  console.log(y);
}

f() // ReferenceError: x is not defined
```
下面是一个更复杂的例子。
```
var x = 1;
function foo(x, y = function() { x = 2; }) {
  var x = 3;
  y();
  console.log(x);
}

foo() // 3
x // 1
```
上面代码中，函数foo的参数形成一个单独作用域。这个作用域里面，首先声明了变量x，然后声明了变量y，y的默认值是一个匿名函数。这个匿名函数内部的变量x，指向同一个作用域的第一个参数x。函数foo内部又声明了一个内部变量x，该变量与第一个参数x由于不是同一个作用域，所以不是同一个变量，因此执行y后，内部变量x和外部全局变量x的值都没变。
### 2. rest参数
ES6 引入 ```rest``` 参数（形式为```...变量名```），用于获取函数的多余参数，这样就不需要使用```arguments```对象了。```rest``` 参数搭配的变量是一个数组，该变量将多余的参数放入数组中。
```
function add(...values) {
  let sum = 0;

  for (var val of values) {
    sum += val;
  }

  return sum;
}

add(2, 5, 3) // 10
```
上面代码的```add```函数是一个求和函数，利用 ```rest```参数，可以向该函数传入任意数目的参数。
注意，```rest``` 参数之后不能再有其他参数（即只能是最后一个参数），否则会报错。
```
// 报错
function f(a, ...b, c) {
  // ...
}
```
### 3. 箭头函数
ES6 允许使用“箭头”（=>）定义函数。
```
var f = v => v;

// 等同于
var f = function (v) {
  return v;
};
```
如果箭头函数不需要参数或需要多个参数，就使用一个圆括号代表参数部分。
```
var f = () => 5;
// 等同于
var f = function () { return 5 };

var sum = (num1, num2) => num1 + num2;
// 等同于
var sum = function(num1, num2) {
  return num1 + num2;
```
如果箭头函数的代码块部分多于一条语句，就要使用大括号将它们括起来，并且使用```return```语句返回。
```
var sum = (num1, num2) => { return num1 + num2; }
```
由于大括号被解释为代码块，所以如果箭头函数直接返回一个对象，必须在对象外面加上括号，否则会报错。
```
// 报错
let getTempItem = id => { id: id, name: "Temp" };

// 不报错
let getTempItem = id => ({ id: id, name: "Temp" });
```
下面是 rest 参数与箭头函数结合的例子。
```
const numbers = (...nums) => nums;

numbers(1, 2, 3, 4, 5)
// [1,2,3,4,5]

const headAndTail = (head, ...tail) => [head, tail];

headAndTail(1, 2, 3, 4, 5)
// [1,[2,3,4,5]]
```
### 使用注意点
箭头函数有几个使用注意点。

（1）函数体内的```this```对象，就是定义时所在的对象，而不是使用时所在的对象。

（2）不可以当作构造函数，也就是说，不可以使用```new```命令，否则会抛出一个错误。

（3）不可以使用```arguments```对象，该对象在函数体内不存在。如果要用，可以用 ```rest``` 参数代替。

（4）不可以使用```yield```命令，因此箭头函数不能用作``` Generator``` 函数。

```
function foo() {
  return () => {
    return () => {
      return () => {
        console.log('id:', this.id);
      };
    };
  };
}

var f = foo.call({id: 1});

var t1 = f.call({id: 2})()(); // id: 1
var t2 = f().call({id: 3})(); // id: 1
var t3 = f()().call({id: 4}); // id: 1
```
上面代码之中，只有一个```this```，就是函数```foo```的```this```，所以```t1```、```t2```、```t3```都输出同样的结果。因为所有的内层函数都是箭头函数，都没有自己的```this```，它们的```this```其实都是最外层foo函数的```this```。

### 4. 尾调用优化
尾调用（Tail Call）是函数式编程的一个重要概念，本身非常简单，一句话就能说清楚，就是指某个函数的最后一步是调用另一个函数。
非尾递归的 Fibonacci 数列实现如下。
```
function Fibonacci (n) {
  if ( n <= 1 ) {return 1};

  return Fibonacci(n - 1) + Fibonacci(n - 2);
}

Fibonacci(10) // 89
Fibonacci(100) // 超时
Fibonacci(500) // 超时
```
尾递归优化过的 Fibonacci 数列实现如下。
```
function Fibonacci2 (n , ac1 = 1 , ac2 = 1) {
  if( n <= 1 ) {return ac2};

  return Fibonacci2 (n - 1, ac2, ac1 + ac2);
}

Fibonacci2(100) // 573147844013817200000
Fibonacci2(1000) // 7.0330367711422765e+208
Fibonacci2(10000) // Infinity
```

```
function factorial(n, total = 1) {
  if (n === 1) return total;
  return factorial(n - 1, n * total);
}

factorial(5) // 120
```
总结一下，递归本质上是一种循环操作。纯粹的函数式编程语言没有循环操作命令，所有的循环都用递归实现，这就是为什么尾递归对这些语言极其重要。对于其他支持“尾调用优化”的语言（比如 Lua，ES6），只需要知道循环可以用递归代替，而一旦使用递归，就最好使用尾递归。
## 对象扩展
### 1. 属性的简介表示法
ES6 允许直接写入变量和函数，作为对象的属性和方法。这样的书写更加简洁。

ES6 允许在对象之中，直接写变量。
```
function f(x, y) {
  return {x, y};
}

// 等同于

function f(x, y) {
  return {x: x, y: y};
}

f(1, 2) // Object {x: 1, y: 2}
```
除了属性简写，方法也可以简写。
```
let birth = '2000/01/01';

const Person = {

  name: '张三',

  //等同于birth: birth
  birth,

  // 等同于hello: function ()...
  hello() { console.log('我的名字是', this.name); }

};
```
这种写法用于函数的返回值，将会非常方便。
```
function getPoint() {
  const x = 1;
  const y = 10;
  return {x, y};
}

getPoint()
// {x:1, y:10}
```
### 2. 属性名表达式
ES6 允许字面量定义对象时，用方法二（表达式）作为对象的属性名，即把表达式放在方括号内。
```
let lastWord = 'last word';

const a = {
  'first word': 'hello',
  [lastWord]: 'world'
};

a['first word'] // "hello"
a[lastWord] // "world"
a['last word'] // "world"
```
表达式还可以用于定义方法名。
```
let obj = {
  ['h' + 'ello']() {
    return 'hi';
  }
};

obj.hello() // hi
```
注意，属性名表达式与简洁表示法，不能同时使用，会报错。
```
// 报错
const foo = 'bar';
const bar = 'abc';
const baz = { [foo] };

// 正确
const foo = 'bar';
const baz = { [foo]: 'abc'};
```
注意，属性名表达式如果是一个对象，默认情况下会自动将对象转为字符串```[object Object]```，这一点要特别小心。
```
const keyA = {a: 1};
const keyB = {b: 2};

const myObject = {
  [keyA]: 'valueA',
  [keyB]: 'valueB'
};

myObject // Object {[object Object]: "valueB"}
```
### 3. 方法的name属性
函数的```name```属性，返回函数名。对象方法也是函数，因此也有```name```属性。

如果对象的方法使用了取值函数（```getter```）和存值函数（```setter```），则```name```属性不是在该方法上面，而是该方法的属性的描述对象的```get```和```set```属性上面，返回值是方法名前加上```get``和```set```。
```
const obj = {
  get foo() {},
  set foo(x) {}
};

obj.foo.name
// TypeError: Cannot read property 'name' of undefined

const descriptor = Object.getOwnPropertyDescriptor(obj, 'foo');

descriptor.get.name // "get foo"
descriptor.set.name // "set foo"
```
如果对象的方法是一个 Symbol 值，那么```name```属性返回的是这个Symbol 值
的描述。
```
const key1 = Symbol('description');
const key2 = Symbol();
let obj = {
  [key1]() {},
  [key2]() {},
};
obj[key1].name // "[description]"
obj[key2].name // ""
```
### 4. super关键字
我们知道，```this```关键字总是指向函数所在的当前对象，ES6 又新增了另一个类似的关键字```super```，指向当前对象的原型对象。
```
const proto = {
  foo: 'hello'
};

const obj = {
  foo: 'world',
  find() {
    return super.foo;
  }
};

Object.setPrototypeOf(obj, proto);
obj.find() // "hello"
```
#### 属性的遍历 
ES6 一共有 5 种方法可以遍历对象的属性。

#####（1）for...in

```for...in```循环遍历对象自身的和继承的可枚举属性（不含 Symbol 属性）。

#####（2）Object.keys(obj)

```Object.keys```返回一个数组，包括对象自身的（不含继承的）所有可枚举属性（不含 Symbol 属性）的键名。

#####（3）Object.getOwnPropertyNames(obj)

```Object.getOwnPropertyNames```返回一个数组，包含对象自身的所有属性（不含 Symbol 属性，但是包括不可枚举属性）的键名。

#####（4）Object.getOwnPropertySymbols(obj)

```Object.getOwnPropertySymbols```返回一个数组，包含对象自身的所有 Symbol 属性的键名。

#####（5）Reflect.ownKeys(obj)

```Reflect.ownKeys```返回一个数组，包含对象自身的所有键名，不管键名是 Symbol 或字符串，也不管是否可枚举。

以上的 5 种方法遍历对象的键名，都遵守同样的属性遍历的次序规则。

- 首先遍历所有数值键，按照数值升序排列。
- 其次遍历所有字符串键，按照加入时间升序排列。
- 最后遍历所有 Symbol 键，按照加入时间升序排列。

#### 扩展运算符
对象的扩展运算符（```...```）用于取出参数对象的所有可遍历属性，拷贝到当前对象之中。
### 5. 对象的新增方法
#### Object.is() 
```Object.is```就是来比较两个值是否严格相等，与严格比较运算符（===）的行为基本一致。
```
Object.is('foo', 'foo')
// true
Object.is({}, {})
// false

+0 === -0 //true
NaN === NaN // false

Object.is(+0, -0) // false
Object.is(NaN, NaN) // true
```
#### Object.assign()
```Object.assign```方法用于对象的合并，将源对象（source）的所有可枚举属性，复制到目标对象（target）。
```
const target = { a: 1 };

const source1 = { b: 2 };
const source2 = { c: 3 };

Object.assign(target, source1, source2);
target // {a:1, b:2, c:3}
```
```Object.assign```方法的第一个参数是目标对象，后面的参数都是源对象。

注意，如果目标对象与源对象有同名属性，或多个源对象有同名属性，则后面的属性会覆盖前面的属性。
```
const target = { a: 1, b: 1 };

const source1 = { b: 2, c: 2 };
const source2 = { c: 3 };

Object.assign(target, source1, source2);
target // {a:1, b:2, c:3}
```
如果只有一个参数，Object.assign会直接返回该参数。
```
const obj = {a: 1};
Object.assign(obj) === obj // true
```
注意点
（1）浅拷贝

```Object.assign```方法实行的是浅拷贝，而不是深拷贝。也就是说，如果源对象某个属性的值是对象，那么目标对象拷贝得到的是这个对象的引用。
（2）同名属性的替换
对于这种嵌套的对象，一旦遇到同名属性，```Object.assign```的处理方法是替换，而不是添加。
```
const target = { a: { b: 'c', d: 'e' } }
const source = { a: { b: 'hello' } }
Object.assign(target, source)
// { a: { b: 'hello' } }
```
#### Object.getOwnPropertyDescriptors()
ES5 的```Object.getOwnPropertyDescriptor()```方法会返回某个对象属性的描述对象（descriptor）。ES2017 引入了```Object.getOwnPropertyDescriptors()```方法，返回指定对象所有自身属性（非继承属性）的描述对象。
```
const obj = {
  foo: 123,
  get bar() { return 'abc' }
};

Object.getOwnPropertyDescriptors(obj)
// { foo:
//    { value: 123,
//      writable: true,
//      enumerable: true,
//      configurable: true },
//   bar:
//    { get: [Function: get bar],
//      set: undefined,
//      enumerable: true,
//      configurable: true } }
```
#### Object.keys()，Object.values()，Object.entries()
ES2017 引入了跟```Object.keys```配套的```Object.values```和```Object.entries```，作为遍历一个对象的补充手段，供```for...of```循环使用。
```
let {keys, values, entries} = Object;
let obj = { a: 1, b: 2, c: 3 };

for (let key of keys(obj)) {
  console.log(key); // 'a', 'b', 'c'
}

for (let value of values(obj)) {
  console.log(value); // 1, 2, 3
}

for (let [key, value] of entries(obj)) {
  console.log([key, value]); // ['a', 1], ['b', 2], ['c', 3]
}
```
## Symbol的用法
### Symbol数据类型
概念：这种数据类型提供一个独一无二的值，也就是说Symbol声明的变量a和变量b永远不可能相等。

声明
```
let a = Symbol();
let b = Symbol();
console.log(a === b); //false 
```
它自己都不会等于它自己
#### Symbol.for
```
            let c = Symbol.for("aaa");
            let d = Symbol.for("aaa");
            console.log(c === d); //true
```
Symbol.for和Symbol的唯一区别是 Symbol.for创建的两个实例可能相等， 根据Symbol的参数生成实例， 如果参数一样， 那么会返回同一个实例。
看到这里，还完全不知道要怎么使用，不急不急，接下来。。。

使用：
```
           let a = Symbol.for('abc');
           let obj = {
               [a]:'111',
               'abc':222,
               'b':333
           };
           console.log('obj',obj);//{abc: 222, b: 333, Symbol(abc): "111"}
           console.log(Symbol.keyFor(a)); // abc 检索字符串键
```
这种使用就可以避免对象中键值相同替换的问题
但是这样使用会有一个问题，对象中使用Symbol做键值，通过for in 和 let of是拿不到那个属性。
```
for(let [key,value] of Object.entries(obj)){
console.log('entries',key,value);//abc 222;b 333
}
```
解决方法： Object.getOwnPropertySymbol()返回值是数组, 但是这个只能打印出Symbol做键值的值，这就尴尬了，下面使用reflect.ownKeys来解决这个问题
```
            Object.getOwnPropertySymbols(obj).forEach(function (item) {
                console.log('getOwnPropertySymbols', obj[item]);//111
            });
```
reflect.ownKeys()返回的也是数组
```
           Reflect.ownKeys(obj).forEach(function (item){
                console.log('ownKeys', item, obj[item]);
                /*ownKeys abc 222
                 * ownKeys b 333
                 * ownKeys Symbol(abc) 111
                 * */
            });
```
## Set 和 Map 数据结构
### Set
ES6 提供了新的数据结构 Set。它类似于数组，但是成员的值都是唯一的，没有重复的值。

```Set```本身是一个构造函数，用来生成 Set 数据结构。
```
const s = new Set();

[2, 3, 5, 4, 5, 2, 2].forEach(x => s.add(x));

for (let i of s) {
  console.log(i);
}
// 2 3 5 4
```
Set 结构不会添加重复的值。
```
let set = new Set();
let a = NaN;
let b = NaN;
set.add(a);
set.add(b);
set // Set {NaN}
```
### Set 实例的属性和方法
Set 结构的实例有以下属性。

- ```Set.prototype.constructor```：构造函数，默认就是Set函数。
- ```Set.prototype.size```：返回Set实例的成员总数。

Set 实例的方法分为两大类：操作方法（用于操作数据）和遍历方法（用于遍历成员）。下面先介绍四个操作方法。

- ```Set.prototype.add(value)```：添加某个值，返回 Set 结构本身。
- ```Set.prototype.delete(value)```：删除某个值，返回一个布尔值，表示删除是否成功。
- ```Set.prototype.has(value)```：返回一个布尔值，表示该值是否为Set的成员。
- ```Set.prototype.clear()```：清除所有成员，没有返回值。

上面这些属性和方法的实例如下。
```
s.add(1).add(2).add(2);
// 注意2被加入了两次

s.size // 2

s.has(1) // true
s.has(2) // true
s.has(3) // false

s.delete(2);
s.has(2) // false
```
下面是一个对比，看看在判断是否包括一个键上面，```Object```结构和```Set```结构的写法不同。
```
// 对象的写法
const properties = {
  'width': 1,
  'height': 1
};

if (properties[someName]) {
  // do something
}

// Set的写法
const properties = new Set();

properties.add('width');
properties.add('height');

if (properties.has(someName)) {
  // do something
}
```
```Array.from```方法可以将 Set 结构转为数组。
```
const items = new Set([1, 2, 3, 4, 5]);
const array = Array.from(items);
```
去除数组重复成员的另一种方法。
```
function dedupe(array) {
  return Array.from(new Set(array));
}

dedupe([1, 1, 2, 3]) // [1, 2, 3]
```

#### 遍历操作
Set 结构的实例有四个遍历方法，可以用于遍历成员。

- Set.prototype.keys()：返回键名的遍历器
- Set.prototype.values()：返回键值的遍历器
- Set.prototype.entries()：返回键值对的遍历器
- Set.prototype.forEach()：使用回调函数遍历每个成员

需要特别指出的是，Set的遍历顺序就是插入顺序。这个特性有时非常有用，比如使用 Set 保存一个回调函数列表，调用时就能保证按照添加顺序调用。

```
let set = new Set(['red', 'green', 'blue']);

for (let item of set.keys()) {
  console.log(item);
}
// red
// green
// blue

for (let item of set.values()) {
  console.log(item);
}
// red
// green
// blue

for (let item of set.entries()) {
  console.log(item);
}
// ["red", "red"]
// ["green", "green"]
// ["blue", "blue"]
```
forEach()

Set 结构的实例与数组一样，也拥有forEach方法，用于对每个成员执行某种操作，没有返回值。
```
let set = new Set([1, 4, 9]);
set.forEach((value, key) => console.log(key + ' : ' + value))
// 1 : 1
// 4 : 4
// 9 : 9
```
### WeakSet
**WeakSet** 结构与 **Set** 类似，也是不重复的值的集合。但是，它与 **Set** 有两个区别。

首先，**WeakSet** 的成员只能是对象，而不能是其他类型的值。
```
const ws = new WeakSet();
ws.add(1)
// TypeError: Invalid value used in weak set
ws.add(Symbol())
// TypeError: invalid value used in weak set
```
上面代码试图向 **WeakSet** 添加一个数值和```Symbol```值，结果报错，因为 **WeakSet** 只能放置对象。

其次，**WeakSet**中的对象都是弱引用，即垃圾回收机制不考虑 **WeakSet**对该对象的引用，也就是说，如果其他对象都不再引用该对象，那么垃圾回收机制会自动回收该对象所占用的内存，不考虑该对象还存在于 **WeakSet** 之中。
**WeakSet** 是一个构造函数，可以使用```new````命令，创建 **WeakSet** 数据结构。
```
const ws = new WeakSet();
```
作为构造函数，**WeakSet** 可以接受一个数组或类似数组的对象作为参数。（实际上，任何具有 **Iterable** 接口的对象，都可以作为 **WeakSet&** 的参数。）该数组的所有成员，都会自动成为 **WeakSet** 实例对象的成员。
```
const a = [[1, 2], [3, 4]];
const ws = new WeakSet(a);
// WeakSet {[1, 2], [3, 4]}
```
WeakSet 结构有以下三个方法。

- **WeakSet.prototype.add(value)**：向 WeakSet 实例添加一个新成员。
- **WeakSet.prototype.delete(value)**：清除 WeakSet 实例的指定成员。
- **WeakSet.prototype.has(value)**：返回一个布尔值，表示某个值是否在 WeakSet 实例之中。

###  Map
ES6 提供了 Map 数据结构。它类似于对象，也是键值对的集合，但是“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键。也就是说，Object 结构提供了“字符串—值”的对应，Map 结构提供了“值—值”的对应，是一种更完善的 Hash 结构实现。如果你需要“键值对”的数据结构，Map 比 Object 更合适。
```
const m = new Map();
const o = {p: 'Hello World'};

m.set(o, 'content')
m.get(o) // "content"

m.has(o) // true
m.delete(o) // true
m.has(o) // false
```
```
const map = new Map([
  ['name', '张三'],
  ['title', 'Author']
]);

map.size // 2
map.has('name') // true
map.get('name') // "张三"
map.has('title') // true
map.get('title') // "Author"
```
如果对同一个键多次赋值，后面的值将覆盖前面的值。
```
const map = new Map();

map
.set(1, 'aaa')
.set(1, 'bbb');

map.get(1) // "bbb"
```
如果读取一个未知的键，则返回```undefined```。

同样的值的两个实例，在 Map 结构中被视为两个键。
```
const map = new Map();

const k1 = ['a'];
const k2 = ['a'];

map
.set(k1, 111)
.set(k2, 222);

map.get(k1) // 111
map.get(k2) // 222
```
### 实例的属性和操作方法
**（1）size 属性**

```size```属性返回 Map 结构的成员总数。
```
const map = new Map();
map.set('foo', true);
map.set('bar', false);

map.size // 2
```
**（2）Map.prototype.set(key, value)**

```set```方法设置键名```key```对应的键值为```value```，然后返回整个``` Map``` 结构。如果```key```已经有值，则键值会被更新，否则就新生成该键。
```
let map = new Map()
  .set(1, 'a')
  .set(2, 'b')
  .set(3, 'c');
```
**（3）Map.prototype.get(key)**

```get```方法读取```key```对应的键值，如果找不到```key```，返回```undefined```。
```
const m = new Map();

const hello = function() {console.log('hello');};
m.set(hello, 'Hello ES6!') // 键是函数

m.get(hello)  // Hello ES6!
```
**（4）Map.prototype.has(key)**

```has```方法返回一个布尔值，表示某个键是否在当前 Map 对象之中。
```
const m = new Map();

m.set('edition', 6);
m.set(262, 'standard');
m.set(undefined, 'nah');

m.has('edition')     // true
m.has('years')       // false
m.has(262)           // true
m.has(undefined)     // true
```
**（5）Map.prototype.delete(key)**

```delete```方法删除某个键，返回```true```。如果删除失败，返回```false```。
```
const m = new Map();
m.set(undefined, 'nah');
m.has(undefined)     // true

m.delete(undefined)
m.has(undefined)       // false
```
**（6）Map.prototype.clear()**

```clear```方法清除所有成员，没有返回值。
```
let map = new Map();
map.set('foo', true);
map.set('bar', false);

map.size // 2
map.clear()
map.size // 0
```
### WeakMap
```WeakMap```结构与```Map```结构类似，也是用于生成键值对的集合。
```
// WeakMap 可以使用 set 方法添加成员
const wm1 = new WeakMap();
const key = {foo: 1};
wm1.set(key, 2);
wm1.get(key) // 2

// WeakMap 也可以接受一个数组，
// 作为构造函数的参数
const k1 = [1, 2, 3];
const k2 = [4, 5, 6];
const wm2 = new WeakMap([[k1, 'foo'], [k2, 'bar']]);
wm2.get(k2) // "bar"
```
```WeakMap```与```Map```的区别有两点。

首先，```WeakMap```只接受对象作为键名（```null```除外），不接受其他类型的值作为键名。
```
const map = new WeakMap();
map.set(1, 2)
// TypeError: 1 is not an object!
map.set(Symbol(), 2)
// TypeError: Invalid value used as weak map key
map.set(null, 2)
// TypeError: Invalid value used as weak map key
```
上面代码中，如果将数值```1```和```Symbol```值作为 WeakMap 的键名，都会报错。

其次，```WeakMap```的键名所指向的对象，不计入垃圾回收机制。

基本上，如果你要往对象上添加数据，又不想干扰垃圾回收机制，就可以使用**WeakMap**。一个典型应用场景是，在网页的**DOM**元素上添加数据，就可以使用```WeakMap```结构。当该**DOM**元素被清除，其所对应```的WeakMap```记录就会自动被移除。
```
const wm = new WeakMap();

const element = document.getElementById('example');

wm.set(element, 'some information');
wm.get(element) // "some information"
```
### WeakMap 的语法
**WeakMap** 与 **Map** 在 **API** 上的区别主要是两个，一是没有遍历操作（即没有```keys()```、```values()```和```entries()```方法），也没有```size```属性。因为没有办法列出所有键名，某个键名是否存在完全不可预测，跟垃圾回收机制是否运行相关。这一刻可以取到键名，下一刻垃圾回收机制突然运行了，这个键名就没了，为了防止出现不确定性，就统一规定不能取到键名。二是无法清空，即不支持```clear```方法。因此，WeakMap只有四个方法可用：```get()```、````set()```、```has()```、```delete()```。
```
const wm = new WeakMap();

// size、forEach、clear 方法都不存在
wm.size // undefined
wm.forEach // undefined
wm.clear // undefined
```
WeakMap 的另一个用处是部署私有属性。
```
const _counter = new WeakMap();
const _action = new WeakMap();

class Countdown {
  constructor(counter, action) {
    _counter.set(this, counter);
    _action.set(this, action);
  }
  dec() {
    let counter = _counter.get(this);
    if (counter < 1) return;
    counter--;
    _counter.set(this, counter);
    if (counter === 0) {
      _action.get(this)();
    }
  }
}

const c = new Countdown(2, () => console.log('DONE'));

c.dec()
c.dec()
// DONE
```
上面代码中，```Countdown```类的两个内部属性```_counter```和```_action```，是实例的弱引用，所以如果删除实例，它们也就随之消失，不会造成内存泄漏。
### Proxy 和Reflect
ES6 原生提供 Proxy 构造函数，用来生成 Proxy 实例。
```
var proxy = new Proxy(target, handler);
```
Proxy 对象的所有用法，都是上面这种形式，不同的只是```handler```参数的写法。其中，```new Proxy()```表示生成一个```Proxy```实例，```target```参数表示所要拦截的目标对象，handler参数也是一个对象，用来定制拦截行为。

下面是另一个拦截读取属性行为的例子。
```
var proxy = new Proxy({}, {
  get: function(target, property) {
    return 35;
  }
});

proxy.time // 35
proxy.name // 35
proxy.title // 35
```
如果```handler```没有设置任何拦截，那就等同于直接通向原对象。
```
var target = {};
var handler = {};
var proxy = new Proxy(target, handler);
proxy.a = 'b';
target.a // "b"
```
上面代码中，```handler```是一个空对象，没有任何拦截效果，访问```proxy```就等同于访问```target```。

同一个拦截器函数，可以设置拦截多个操作。
```
var handler = {
  get: function(target, name) {
    if (name === 'prototype') {
      return Object.prototype;
    }
    return 'Hello, ' + name;
  },

  apply: function(target, thisBinding, args) {
    return args[0];
  },

  construct: function(target, args) {
    return {value: args[1]};
  }
};

var fproxy = new Proxy(function(x, y) {
  return x + y;
}, handler);

fproxy(1, 2) // 1
new fproxy(1, 2) // {value: 2}
fproxy.prototype === Object.prototype // true
fproxy.foo === "Hello, foo" // true
```
下面是 Proxy 支持的拦截操作一览，一共 13 种。
#### Proxy 实例的方法
#### get() 
```get```方法用于拦截某个属性的读取操作，可以接受三个参数，依次为目标对象、属性名和 proxy 实例本身（严格地说，是操作行为所针对的对象），其中最后一个参数可选。
```
ar person = {
  name: "张三"
};

var proxy = new Proxy(person, {
  get: function(target, property) {
    if (property in target) {
      return target[property];
    } else {
      throw new ReferenceError("Property \"" + property + "\" does not exist.");
    }
  }
});

proxy.name // "张三"
proxy.age // 抛出一个错误
```
```get```方法可以继承。
```
function createArray(...elements) {
  let handler = {
    get(target, propKey, receiver) {
      let index = Number(propKey);
      if (index < 0) {
        propKey = String(target.length + index);
      }
      return Reflect.get(target, propKey, receiver);
    }
  };

  let target = [];
  target.push(...elements);
  return new Proxy(target, handler);
}

let arr = createArray('a', 'b', 'c');
arr[-1] // c
```
#### set() 
```set```方法用来拦截某个属性的赋值操作，可以接受四个参数，依次为目标对象、属性名、属性值和 Proxy 实例本身，其中最后一个参数可选。

假定```Person```对象有一个```age```属性，该属性应该是一个不大于 200 的整数，那么可以使用```Proxy```保证```age```的属性值符合要求。
```
let validator = {
  set: function(obj, prop, value) {
    if (prop === 'age') {
      if (!Number.isInteger(value)) {
        throw new TypeError('The age is not an integer');
      }
      if (value > 200) {
        throw new RangeError('The age seems invalid');
      }
    }

    // 对于满足条件的 age 属性以及其他属性，直接保存
    obj[prop] = value;
  }
};

let person = new Proxy({}, validator);

person.age = 100;

person.age // 100
person.age = 'young' // 报错
person.age = 300 // 报错
```
上面代码中，由于设置了存值函数```set```，任何不符合要求的```age```属性赋值，都会抛出一个错误，这是数据验证的一种实现方法。利用```set```方法，还可以数据绑定，即每当对象发生变化时，会自动更新 DOM。
```
const handler = {
  get (target, key) {
    invariant(key, 'get');
    return target[key];
  },
  set (target, key, value) {
    invariant(key, 'set');
    target[key] = value;
    return true;
  }
};
function invariant (key, action) {
  if (key[0] === '_') {
    throw new Error(`Invalid attempt to ${action} private "${key}" property`);
  }
}
const target = {};
const proxy = new Proxy(target, handler);
proxy._prop
// Error: Invalid attempt to get private "_prop" property
proxy._prop = 'c'
// Error: Invalid attempt to set private "_prop" property
```
上面代码中，只要读写的属性名的第一个字符是下划线，一律抛错，从而达到禁止读写内部属性的目的。
### Reflect
```Reflect```对象的设计目的有这样几个。

（1） 将```Object``对象的一些明显属于语言内部的方法（比如```Object.defineProperty```），放到```Reflect```对象上。现阶段，某些方法同时在```Object```和```Reflect```对象上部署，未来的新方法将只部署在```Reflect```对象上。也就是说，从```Reflect```对象上可以拿到语言内部的方法。

（2） 修改某些```Object```方法的返回结果，让其变得更合理。比如，```Object.defineProperty(obj, name, desc)```在无法定义属性时，会抛出一个错误，而```Reflect.defineProperty(obj, name, desc)```则会返回```false```。
```
// 老写法
try {
  Object.defineProperty(target, property, attributes);
  // success
} catch (e) {
  // failure
}

// 新写法
if (Reflect.defineProperty(target, property, attributes)) {
  // success
} else {
  // failure
}
```
（3） 让```Object```操作都变成函数行为。某些```Object```操作是命令式，比如```name in obj```和```delete obj[name]```，而```Reflect.has(obj, name)```和```Reflect.deleteProperty(obj, name)```让它们变成了函数行为。
```
// 老写法
'assign' in Object // true

// 新写法
Reflect.has(Object, 'assign') // true
```
（4）```Reflect```对象的方法与```Proxy```对象的方法一一对应，只要是```Proxy```对象的方法，就能在```Reflect```对象上找到对应的方法。这就让```Proxy```对象可以方便地调用对应的```Reflect```方法，完成默认行为，作为修改行为的基础。也就是说，不管```Proxy```怎么修改默认行为，你总可以在```Reflect```上获取默认行为。
```
Proxy(target, {
  set: function(target, name, value, receiver) {
    var success = Reflect.set(target, name, value, receiver);
    if (success) {
      console.log('property ' + name + ' on ' + target + ' set to ' + value);
    }
    return success;
  }
});
```
有了Reflect对象以后，很多操作会更易读。
```
// 老写法
Function.prototype.apply.call(Math.floor, undefined, [1.75]) // 1

// 新写法
Reflect.apply(Math.floor, undefined, [1.75]) // 1
```
### 静态方法
```Reflect```对象一共有 13 个静态方法。

- Reflect.apply(target, thisArg, args)
- Reflect.construct(target, args)
- Reflect.get(target, name, receiver)
- Reflect.set(target, name, value, receiver)
- Reflect.defineProperty(target, name, desc)
- Reflect.deleteProperty(target, name)
- Reflect.has(target, name)
- Reflect.ownKeys(target)
- Reflect.isExtensible(target)
- Reflect.preventExtensions(target)
- Reflect.getOwnPropertyDescriptor(target, name)
- Reflect.getPrototypeOf(target)
- Reflect.setPrototypeOf(target, prototype)

参考链接：http://es6.ruanyifeng.com/#docs/reflect
## 类和对象
```
{
    // 构造函数和实例
    class Parent{
        constructor(name='mukewan'){
            this.name=name;
        }
    }
    let v_parent=new Parent('v');
    console.log('构造函数和实例',v_parent);   //构造函数和实例 Parent {name: "v"}
}

{
    // 继承
    class Parent{
        constructor(name='mukewan'){
            this.name=name;
        }
    }

    class Child extends Parent{

    }
    console.log('继承',new Child());  //继承 Child {name: "mukewan"}
}
{
    // 继承传递参数
    class Parent{
        constructor(name='mukewan'){
            this.name=name;
        }
    }

    // 子类Child继承父类Parant
    class Child extends Parent{
        constructor(name='child'){
            super(name);  //修改父类属性,一定要放在构造函数的第一行
            this.type='child';
        }
    }

    console.log('继承传递参数',new Child('hello'));  //继承传递参数 _Child {name: "hello", type: "child"}
}
```
#### getter（读取）、setter（设置）属性
```
{
    // getter,setter
    class Parent {
        constructor(name='mukewan'){
            this.name=name;
        }

        get longName(){  //读取
            return 'mk'+this.name;
        }

        set longName(value){  //设置
            this.name=value;
        }        
    }

    let v=new Parent();
    console.log('getter',v.longName);  // getter mkmukewan
    v.longName='hello';
    console.log('setter',v.longName);  //setter mkhello
}
```
#### static静态方法跟静态属性
```
{
    // 静态方法
    class Parent {
        constructor(name='mukewan'){
            this.name=name;
        }

        static tell(){  //static+函数名，
             console.log('tell');   
        }        
    }

    Parent.tell();  //tell  静态方法是通过类去调用，而不是实例去调用!

}

{
    // 静态属性
    class Parent {
        constructor(name='mukewan'){
            this.name=name;
        }

        static tell(){  //static+函数名，
             console.log('tell');   
        }        
    }

    Parent.type='test'  //这边使用的都是类，而不是实例

    console.log('静态属性',Parent.type);  //静态属性 test

}
```
## promise异步
ES6 规定，```Promise```对象是一个构造函数，用来生成```Promise```实例。

下面代码创造了一个```Promise```实例。
```
const promise = new Promise(function(resolve, reject) {
  // ... some code

  if (/* 异步操作成功 */){
    resolve(value);
  } else {
    reject(error);
  }
});
```
```Promise```实例生成以后，可以用```then```方法分别指定```resolved```状态和```rejected```状态的回调函数。
```
function timeout(ms) {
  return new Promise((resolve, reject) => {
    setTimeout(resolve, ms, 'done');
  });
}

timeout(100).then((value) => {
  console.log(value);
});
```
Promise 新建后就会立即执行。
```
let promise = new Promise(function(resolve, reject) {
  console.log('Promise');
  resolve();
});

promise.then(function() {
  console.log('resolved.');
});

console.log('Hi!');

// Promise
// Hi!
// resolved
```
下面是异步加载图片的例子。
```
function loadImageAsync(url) {
  return new Promise(function(resolve, reject) {
    const image = new Image();

    image.onload = function() {
      resolve(image);
    };

    image.onerror = function() {
      reject(new Error('Could not load image at ' + url));
    };

    image.src = url;
  });
}
```
下面是一个用```Promise```对象实现的 Ajax 操作的例子。
```
const getJSON = function(url) {
  const promise = new Promise(function(resolve, reject){
    const handler = function() {
      if (this.readyState !== 4) {
        return;
      }
      if (this.status === 200) {
        resolve(this.response);
      } else {
        reject(new Error(this.statusText));
      }
    };
    const client = new XMLHttpRequest();
    client.open("GET", url);
    client.onreadystatechange = handler;
    client.responseType = "json";
    client.setRequestHeader("Accept", "application/json");
    client.send();

  });

  return promise;
};

getJSON("/posts.json").then(function(json) {
  console.log('Contents: ' + json);
}, function(error) {
  console.error('出错了', error);
});
```
上面代码中，```getJSON```是对 XMLHttpRequest 对象的封装，用于发出一个针对 JSON 数据的 HTTP 请求，并且返回一个```Promise```对象。需要注意的是，在```getJSON```内部，```resolve```函数和```reject```函数调用时，都带有参数。

## Iterator 和 for...of 循环
### Iterator（遍历器）的概念
JavaScript 原有的表示“集合”的数据结构，主要是数组（```Array```）和对象（```Object```），ES6 又添加了```Map```和```Set```。这样就有了四种数据集合，用户还可以组合使用它们，定义自己的数据结构，比如数组的成员是```Map```，```Map```的成员是对象。这样就需要一种统一的接口机制，来处理所有不同的数据结构。

遍历器（Iterator）就是这样一种机制。它是一种接口，为各种不同的数据结构提供统一的访问机制。任何数据结构只要部署 Iterator 接口，就可以完成遍历操作（即依次处理该数据结构的所有成员）。

Iterator 的作用有三个：一是为各种数据结构，提供一个统一的、简便的访问接口；二是使得数据结构的成员能够按某种次序排列；三是 ES6 创造了一种新的遍历命令```for...of```循环，```Iterator``` 接口主要供```for...of```消费。

Iterator 的遍历过程是这样的。

（1）创建一个指针对象，指向当前数据结构的起始位置。也就是说，遍历器对象本质上，就是一个指针对象。

（2）第一次调用指针对象的```next```方法，可以将指针指向数据结构的第一个成员。

（3）第二次调用指针对象的```next```方法，指针就指向数据结构的第二个成员。

（4）不断调用指针对象的```next```方法，直到它指向数据结构的结束位置。

每一次调用```next```方法，都会返回数据结构的当前成员的信息。具体来说，就是返回一个包含```value```和```done```两个属性的对象。其中，```value```属性是当前成员的值，```done```属性是一个布尔值，表示遍历是否结束。
```
var it = makeIterator(['a', 'b']);

it.next() // { value: "a", done: false }
it.next() // { value: "b", done: false }
it.next() // { value: undefined, done: true }

function makeIterator(array) {
  var nextIndex = 0;
  return {
    next: function() {
      return nextIndex < array.length ?
        {value: array[nextIndex++], done: false} :
        {value: undefined, done: true};
    }
  };
}
```
```
{
    let obj={
        start:[1,3,2],
        end:[7,8,9],
        [Symbol.iterator](){
            let self=this;
            let index=0;
            let arr=self.start.concat(self.end);
            let len=arr.length;
            return {
                next(){
                    if(index<len){
                        return {
                            value:arr[index++],
                            done:false  //fasle会继续执行
                        }
                    }else {
                        return {
                            value:arr[index++],
                            done:true //truee会结束
                        }
                    }
                }
            }
        }
    }
    for(let key of obj){
        console.log(key);
    }
}
```
## Generator异步处理
```
{
    // genertaor基本定义,next()一步步执行
    let tell=function* (){
        yield 'a';
        yield 'b';
        return 'c'
    };
    let k=tell();

    console.log(k.next()); //{value: "a", done: false}
    console.log(k.next()); //{value: "b", done: false}
    console.log(k.next()); //{value: "c", done: true}
    console.log(k.next());//{value: undefined, done: true}

}
```
**应用场景**
```
// 应用场景，变量变化的实时存储
{
    let draw=function(count){
        // 具体抽奖逻辑
        console.info(`剩余${count}次`)
    }

    let residue=function *(count){
        while(count>0){
            count--;
            yield draw(count);
        }
    }

    let star=residue(5);
    let btn=document.createElement("button");
    btn.id='star';
    btn.textContent='抽奖';
    document.body.appendChild(btn);
    document.getElementById('star').addEventListener("click",function(){
        star.next();
    },false)
}
```
**长轮询(服务端某个状态在变化，我们也需要实时去访问变化)**
```
{
    // 长轮询(服务端某个状态在变化，我们也需要实时去访问变化)
    let ajax=function* (){
        yield new Promise(function(resolve,reject){
            setTimeout(function(){
                resolve({code:1})
            },200);
        })
    }

    let pull=function(){
        let genertaor=ajax();
        let step=genertaor.next();
        step.value.then(function(d){
            if(d.code!=0){
                setTimeout(function(){
                    console.info('wait');
                    pull()
                },1000);
            }else {
                console.log(d);
            }
        })
    }

    pull();
}
```
## Decorator函数修饰符，通过修饰器修改类的行为