# 《JavaScript中的for循环》

> 作者：阿拉拉布
> 参与编辑：火火同学

> **目录**
> - part 1: 关于for-in循环
> - part 2: 关于for-of循环

## part 1: for-in循环
> **I 关于for-in循环**

1.for-in 语句用于对数组或者对象的属性进行循环操作。

``` javascript
// 遍历数组时,index获取数组的下标(实际上该下标属于该数组对象的属性)
var arr = [1, 2, 3, 4, 5, 6];
for(var index in arr) {
      // 得到的index是arr数组中各个值的下标,
      // arr[index]则是对应的值.
      console.log(index);
}
      
```
``` javascript
var orc = {
      name: '雷克萨',
      age: 18,
      job: 'teacher'
};
// 结果: "name","age","job"
for(var field in orc) {
      // 此时得到的value为orc中定义的属性,将value作为orc的
      // 下标进行遍历则可以得到value所对应的属性值.
      console.log(field);
      console.log(field + "=>", orc[field]);
}
```
2.for-in 循环中的代码每执行一次，就会对数组的元素或者对象的属性进行一次操作。

3.for-in循环应该用在非数组对象的遍历上，除非你希望每次也拿到index作为下标。使用for-in进行循环也被称为“枚举”遍历。

4.for-in是为了对象设计的，可以遍历字符串型的键。因此以下遍历会依次输出"小,兔,子,吃,草"。
``` javascript
let str = '小兔子吃草';
// 遍历字符串
// 结果:
// 0 => 小
// 1 => 兔
// 2 => 子
// 3 => 吃
// 4 => 草
for(let index in str) {
	console.log(index + "=>", str[index]);
}
```

> **II for-in循环的弊端**

1. for-in 循环只遍历可枚举属性。像 Array 和 Object ， Number等 js中基本包装类型的原型属性是不可枚举的。

``` javascript
var arr = new Array();
for（var i in arr）{
	// 对象.hasOwnProperty(属性)
	// 可判断该属性是否属于该对象自身,而非其原型链中继承的属性
	if(!arr.hasOwnProperty(i)) continue;//跳过继承的属性
	// do something...
}
```
该方法会忽略掉那些从原型链上继承到的属性.

2. 代码中的index不是数字，是字符串“0”、“1”、“2”，可能无意间进行字符串的计算“2”+1=“21”等。
``` javascript
var arr = ['a','b','c'];
for(var i in arr){
	// i 是字符串类型的值
	console.log(i);		// 0,1,2依次输出
	console.log(typeof i);	// string
	console.log(i + 10);	// 010,110,210依次输出
	console.log(i - 10);	// -10,-9,-8依次输出
}
```
3. for-in可能按照仿佛是随机的顺序遍历对象枚举（在该对象仿佛是一个数组对象时，优先遍历数组下标，再遍历其他对象属性）。
``` javascript
var obj = {
	'first':'first',
	'zoo':'zoo',
	'2':'2',
	'34':'34',
	'1':'1',
	'second':'second'
};
// 输出：1, 2, 34, first, zoo, second
for (var i in obj) { console.log(i); };

```

4. 可以手动禁止某些属性特性为"不可枚举"，届时该属性便无法通过for-in进行遍历。
``` javascript
 /**
  * 通过Object对象的defineProperty方法
  * 可以对某对象的某属性进行特殊设置，如更改它为“不可枚举”
  * 那么该属性就无法通过for-in循环遍历到
  */
 Object.defineProperty(obj, '1', {
   enumerable: false
 });
 Object.defineProperty(obj, '2', {
   enumerable: false
 });
 Object.defineProperty(obj, '34', {
   enumerable: false
 });
 // 输出：first, zoo, second
 for (var i in obj) { console.log(i); };
```
5. for-in循环能够遍历来自于继承的枚举，有的时候使用 for-in 遍历数组会发生诡异的现象。
``` javascript

// List继承于 Array 对象以保证自己是一个数组
// 并为自己添加 arrName 属性
class List extends Array{
   constructor(){
     super();
     this.arrName = 'I am List!';
   }
}
// ArrayList 继承于 List
class ArrayList extends List{}
// 通过 new 关键字创建 ArrayList 的对象 myList
// 并为其添加数组元素
var myList = new ArrayList();
myList[0] = 'a';
myList[1] = 'b';
myList[2] = 'c';
// 数组长度为: 3
console.log( myList.length );
// 遍历结果为: 0, 1, 2, arrName
// 遍历 4 次...
for (var field in myList) {
  console.log( field );
}
```
6. for-of 却可以完美解决该问题，仅对数组元素进行遍历。因此尽量使用 for-in 去遍历非数组对象是一个好的选择。
``` javascript
// 结果: a, b, c
for(let value of myList){
	console.log(value);
}
```

## part 2: for-of循环
> **I. for-of  循环的应用**

1. 数组 for-of 直接获取数组的值:
``` javascript
var arr = ['a','b','c','d'];
for(var val of arr) {
	console.log("val的值为:",val); // 依次输出: a,b,c,d
}
```
2. 当你想遍历一个对象的枚举时，可以通过 Object.keys() 获取对象的枚举部分的数组，并通过for-of进行遍历。
``` javascript
var orc = {
	name: 'Thrall',
	age: 18,
	work: function(){
		return this.name + ' say, work make me happy!'
	}
};
// 如果你想用 for-of 遍历一个对象的枚举（属性和函数），
// 你可以使用 Object.keys() 方法.
// Object.keys() 可以将对象的枚举置入一个数组
var keyList = Object.keys(orc); // 获取key集合
// 结果: name, age, work
for(var key of keyList) {
	console.log( key );
}

```

3. 字符串 for-of 遍历
``` javascript
var s = '小兔子吃草';
for(var c of s) {
	console.log("值 =>", c); // 依次输出: 小,兔,子,吃,草
}
```
4. Set对象(ES6)是一种特殊的数组，值部分必须保持其在当前数组中的唯一性（不可出现重复值）
``` javascript
var name = ['花花','豆豆','明明','豆豆'];
// 基于单词数组创建一个Set对象
var uniqueName = new Set(name);
// 生成Set对象后，你可以轻松遍历它所包含的内容：
// set对象会自动过滤相同的字符
// 以确保数组内容的唯一性
for (var n of uniqueName) {
	console.log("他的名字是>>",n); // 结果: 花花,豆豆,明明
}

```
5.Map对象(ES6)是一个键值对数组，键部分是一个Set以确保其键不重复。
``` javascript
var m = new Map();
m.set('兔子','1');
m.set('猫咪','2');
m.set('二哈','3');
m.set('猫咪','4');
// {"兔子" => "1", "猫咪" => "4", "二哈" => "3"}
console.log(m);
// 结果: 1
console.log(m.get('兔子'));
// 获取map中表示key的集合
// key是一个不可重复的Set集合
var mapKeys = m.keys();
for (var key of mapKeys) {
  console.log('key :', key);// 结果: 兔子,猫咪,二哈
}
// 获取map中表示value的集合
var mapValues = m.values();
for (var value of mapValues) {
  console.log('value :', value);// 结果: 1, 4, 3
}
```
第二次为key'猫咪'赋值时，并没有创建新的数组元素，而是对原有的key'猫咪'赋值，因此值部分的结果是： [1, 4, 3]

> **II 遍历Map对象的其他方法:**

**方法1:** el 将获取到每一个元素的键值数组（[0]位置存放 key，[1]位置存放 value）
``` javascript
// for-of 直接遍历map对象,获取每个值都是一个数组表示的键值对
for(let el of m){
	// 结果依次为: ["兔子", "1"],["猫咪", "4"]["二哈", "3"]
	console.log(el);
	// 结果依次为: 兔子,猫咪,二哈
	console.log(el[0]);
	// 结果依次为: 1, 4, 3
	console.log(el[1]);
}
```
**方法2:** 利用解构赋值（ES6）可以直接获取键值并进行遍历
``` javascript
// for-of 解构赋值方式
// 结果依次为:
// 兔子 的岁数是: 1
// 猫咪 的岁数是: 4
// 二哈 的岁数是: 3
for (var [key, value] of m ){
	console.log(key + " 的岁数是: " + value);
}

```
**方法3:** 获取Map的迭代器，依次获取值
``` javascript
/**
 * 通过 entries 可获取Map的迭代器对象
 * 每次调用 next() 方法将获取该迭代器中的第一个元素的封装对象
 * 并且将该元素移出迭代器
 * 其中还包含一个 done 属性, 表示是否迭代完毕
 */
let iterator = m.entries();
// 结果: {done: false, value: ['兔子','1']}
console.log( iterator.next() );
// 结果: {done: false, value: ['猫咪','4']}
console.log( iterator.next() );
// 结果: {done: false, value: ['二哈','3']}
console.log( iterator.next() );
// 结果: {done: true, value: undefined}
console.log( iterator.next() );
```
**方法4：** 循环遍历该迭代器(方法2是最优选择，但该示例可以让你理解迭代器)
``` javascript
let iterator = m.entries();
// 是否迭代完成
let isDone = false;
// 获取每一个迭代器中元素的包装对象
let el = null;
// 将迭代器中的下一个元素赋值给el,
// 并获取该元素的"结束状态"进行判断是否结束遍历
while( (el = iterator.next()) && !(isDone = el.done) ){
  let [key, value] = el.value;
  console.log( key + ' > ' + value );
}
```
实际entries()获取了Map对象中的特殊属性"iterator"，直接获取该"iterator"对象还可以使用Symbol(ES6)符号对象:
``` javascript
// 等同于上例中的: 
// let iterator = m.entries();
// 该 iterator 实际上是一个函数，作用是返回一个迭代器对象
let iterator = m[Symbol.iterator]();
```
看完上一个例子，我们来试着使用 for-of 遍历一个普通对象（非数组对象）
``` javascript
var orc = {
	name: 'Thrall',
	age: 18
};
/**
 * 当我们使用 for-of 遍历非数组对象时，
 * 编译器会出现错误提示：
 * orc[Symbol.iterator] is not a function
 */
for (let value of orc) {
  console.log( value );
}
```
编译器直接提示错误: “orc[Symbol.iterator] is not a function”
该对象不存在可遍历的迭代器！
因此可以这么说： for-of 遍历的原理就是通过调用对象的迭代器对象并获取每一个元素！
我们可以为该对象添加一个迭代器对象，并告诉 for-of 你该如何遍历这个对象
``` javascript
Object.defineProperty(orc, Symbol.iterator, {
  enumerable: false,
  value: function(){
    let that = this;
    let idx = 0;
    let keys = Object.keys(that);
    return {
      next: function(){
        return {
          value: that[ keys[idx++] ],
          done: (idx > keys.length)
        };
      }
    };
  }
});
// 借此我们实现了普通对象(非数组对象)的 for-of 遍历
// 结果: Thrall, 18
for (let value of orc) {
  console.log( value );
}
```
当然，为了简化阅读，以下是注释版本 ^-^
``` javascript
// 通过 Object.defineProperty() 为 orc 设置迭代器属性
Object.defineProperty(orc, Symbol.iterator, {
  // 不可枚举
  enumerable: false,
  // 描述 orc 的 iterator 属性是一个返回迭代器对象的函数
  value: function(){
    // 获取当前 orc 对象
    let that = this;
    // 当前迭代的位置
    let idx = 0;
    // 获得当前对象的属性集合
    let keys = Object.keys(that);
    // 返回一个对象: 迭代器对象
    return {
      // 该迭代器对象拥有一个 next 方法
      next: function(){
        // 该方法返回当前迭代的第 N 个元素
        return {
          // 迭代元素的 value 属性描述通过第 N 个属性获取值
          value: that[ keys[idx++] ],
          // 当下标大于该对象属性的长度时, done得到 true值
          // 表示迭代结束
          done: (idx > keys.length)
        };
      }
    };
  }
});
```

> **for-of 循环为对象动态添加属性**
``` javascript
/**
 * 有些时候, 有些对象的属性是需要动态设置的
 * 有些时候有些属性名可能也会带有特殊符号(即使不推荐这样使用)
 */
var obj = {};
var field_arr = ['name','age','born-date','.6'];
for (let f of field_arr) {
	obj[f] = f;
}
// 结果: {name: "name", age: "age", born-date: "born-date", .6: ".6"}
console.log(obj);

//console.log(obj.born-date); // 错误
//console.log(obj..6);        // 错误

console.log( obj['born-date'] );  // 正确: born-date
console.log( obj['.6'] );         // 正确: .6
```


> **for-of  循环的优点**

1.这是最简洁、最直接的遍历数组元素的语法

2.这个方法避开了for-in循环的所有缺陷

3.与forEach()不同的是，它可以正确响应break、continue和return语句

