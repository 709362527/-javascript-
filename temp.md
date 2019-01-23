## 一、javascript编译
(1)LHS查询，即查找容器本身,例如var a=2;<br>
(2)RHS查询，即查找容器的值,例如console.log(a)<br>
区分LHS和RHS的意义：
（1）LHS查询会在所在作用域创建变量a，并提升变量名;
```
function a(){
  console.log(test)//undefined
  console.log(test02)//Uncaught ReferenceError: test02 is not defined
  var test="test";
}
a();
```
上例中var test="test"，进行了LHS查询，会先判断test变量是否存在，如果不存在，则创建test，并且把变量名提升至整个作用域顶部；<br>
所以，test的变量名提升后 返回undefined，而test02则完全找不到直接报错；


## 二、词法作用域
* 即是在写代码时将变量和块作用域写在哪里决定的作用域;

欺骗词法作用域的方法:<br>
(1)eval
```
function foo(str，a){
  eval(str);
  console.log(a，b)
}

var b=2;
foo("var b=3;"，1);//1，3
```
上面例子中var b=3是定义在foo作用域外部的变量，通过eval做到私有变量外部定义，这样做很可能造成错误判断，以为没有内部没有声明变量，实际上却有，造成更多不确定性;

(2)with
```
function foo(obj){
  with(obj){
    a=2;
  }
}

var o1={
  a:3
}

var o2={
  b:3
}
foo(o1);
console.log(o1.a);//2

foo(o2);
console.log(o2.a);//undefined
console.log(a);//2 
```
上例中通过foo中with来修改对象o1、o2，o1修改后一切正常，而o2因为本身没有a方法，所以with将a方法赋值给了全局window;<br>
(a) o2.a 返回的是undefined，说明with在定义a的时候对其进行了LHS的查询，所以创建了a变量，但是没有赋值;<br>
(b) a返回2是因为with修改时生效的是其所在的词法作用域，即foo，foo中返回的this是window，所以给了全局;<br>


## 三、函数作用域
* 函数作用域，就是避免内部的词法暴露到作用域外，阻止内部变量和函数被外部直接访问;<br>
函数作用域的效果经常被用作命名空间,就好像就jquery一样，防止内部方法污染全局，不过有时候函数名本身就是一种污染，那么可以使用匿名函数,最外层的函数需要立即执行，即匿名自执行函数IIFE<br>
```
(function(){

}))()
```
* 闭包
```
function a(){
  var num=1;
  return function(){
    num++; 
    console.log(num)
  }
}

var b=a();
b();//2
b();//3
b();//4
```
上例代码可以看出闭包的2个特性:<br>
(1)在function a作用域外部访问到了var num=1；<br>
(2)num在 return function中不断累加；num不断累加的原因是javascript的特殊机制造成的，javascript本身有垃圾回收机制，奇怪的是function a被用完后内部作用域return function仍然存在，没有被回收，而且始终被return function自身调用着，所以num可以记录下作用域内的变量值；


##  四、块级作用域
ES6以后，javascript引入了let，终于有了创建完整的、不受约束的块作用域的方法；
* ES6以前，javascript只有部分写法具有块级作用域的功能：<br>
(1)with<br>
(2)catch
* 在ES6以前，想要使用块级作用域就要利用catch来实现
  ```
  try{
    throw undefined;
  }catch(a){
    a=1;
    console.log(a);//1
  }
  console.log(a);//Uncaught ReferenceError: a is not defined
  ```
可以看到示例中变量a并没有暴露到全局，外部也没有函数作用域，是catch的块级作用域起了作用；
* 不过值得注意的是try catch性能很糟糕
 
## 五、this
* this在任何情况下都不指向函数的词法作用域，完全取决于函数的调用位置
* 函数内部的this指向于全局window

### this绑定方式
#### 1、默认方式
#### 2、隐式绑定
```
function foo(){
  console.log(this.a);
}

var obj={
  a:2,
  foo:foo
}
obj.foo();//2
```
##### 隐式丢失，即多层obj调用function，this会指向离他最近的object；
#### 3、显式绑定，call 、apply、bind
#### 4、new绑定
如果把null、undefined通过显示绑定的方式绑定，实际上会使用默认绑定规则;
```
function foo(){
  "use strict"
  console.log(this)//undefined
}

foo()
```
上例中使用了严格模式，所以函数中this返回了undefined
<br>
* 对象属性引用链中中只有最顶层或者说只有最后一层会影响调用位置
```
function foo(){
  console.log(this.a);
}

var obj2={
  a:42,
  foo:foo
}
var obj1={
  a:2,
  obj2:obj2
}
obj1.obj2.foo() //42
```
上例中函数this返回的是obj2，最贴近引用他的那一层；<br>

* 显示绑定的优先级要高于隐式绑定<br>
* new 绑定优先级高于隐式绑定<br>
* 箭头函数=>绑定的this不能修改<br>
* 箭头函数体内的this对象，就是定义时所在的对象，而不是使用时所在的对象;<br>
* 箭头函数无法作为构造函数<br>
* ES6之前实现箭头函数的方法（被绑定的this再次修改仍然是只想obj）：
```
function foo(){
  var self=this;
  return function(){
    setTimeout(function(){
      console.log(self.a)
    },100)
  }
}

var obj={
  a:2
}
var obj02={
  a:3
}
var bar=foo.call(obj)
bar.call(obj02)//2
```

```
var obj={
  id:'awesome'，
  cool:function(){
    console.log(this.id)
  }
}
var id='not awesome';

obj.cool();//awesome
setTimeout(obj.cool，100);//not awesome
```
上例中cool函数丢失了this的绑定<br>
* 解决this指向问题的方法：
(1)
## 六、对象
#### 一、浅复制:
##### 1、Object.assign
#### 二、深复制:
##### 1、JSON.stringify()、JSON.parse()，序列化反序列化法
##### 2、slice,只深复制了第一层，后面几层全部引用共同地址
```
var arr = ['a', 'b', 'c'];
var arrCopy = arr.slice(0);
arrCopy[0] = 'test'
console.log(arr); // ["a", "b", "c"]
console.log(arrCopy); // ["test", "b", "c"]
```
##### 3、concat
```
var arr = ['a', 'b', 'c'];
var arrCopy = arr.concat();
arrCopy[0] = 'test'
console.log(arr); // ["a", "b", "c"]
console.log(arrCopy); // ["test", "b", "c"]
```
##### 4、递归赋值，for...in 或 reflect1
(1)for...in
```
function isObject(o) {
    return (typeof o === 'object' || typeof o === 'function') && o !== null
}
// 迭代递归法：深拷贝对象与数组
function deepClone(obj) {
    if (!isObject(obj)) {
        throw new Error('obj 不是一个对象！')
    }
 
    let isArray = Array.isArray(obj)
    let cloneObj = isArray ? [] : {}
    for (let key in obj) {
        cloneObj[key] = isObject(obj[key]) ? deepClone(obj[key]) : obj[key]
    }
 
    return cloneObj
}
```
(2)reflect
```
function deepClone(obj) {
    if (!isObject(obj)) {
        throw new Error('obj 不是一个对象！')
    }
 
    let isArray = Array.isArray(obj)
    let cloneObj = isArray ? [...obj] : { ...obj }
    Reflect.ownKeys(cloneObj).forEach(key => {
        cloneObj[key] = isObject(obj[key]) ? deepClone(obj[key]) : obj[key]
    })
 
    return cloneObj
}
```
### 递归赋值注意问题：
(1) 对象成环怎么办？
```
test.loopObj = test
```
解决方法：如果有环对象，就会从栈里检测到，从而直接返回结果<br>
(2) 键值不是字符串而是 Symbol，因为 Symbol 是一种特殊的数据类型，它最大的特点便是独一无二，所以它的深拷贝就是浅拷贝<br>
解决方法：1、使用reflect；2、Object.getOwnPropertySymbols(obj)，然后遍历<br>
(3) 拷贝原型上的属性<br>
 解决方法：只有for...in可以实现，而其它三种方法(Object.keys、Reflect.ownKeys 和 JSON 方法)都不会追踪原型链上的属性；<br>
(4) 需要拷贝不可枚举的属性<br>
```
function cloneDeep(obj) {
    let family = {}
    let parent = Object.getPrototypeOf(obj)
 
    while (parent != null) {
        family = completeAssign(deepClone(family), parent)
        parent = Object.getPrototypeOf(parent)
    }
 
    // 下面这个函数会拷贝所有自有属性的属性描述符,来自于 MDN
    // https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/assign
    function completeAssign(target, ...sources) {
        sources.forEach(source => {
            let descriptors = Object.keys(source).reduce((descriptors, key) => {
                descriptors[key] = Object.getOwnPropertyDescriptor(source, key)
                return descriptors
            }, {})
 
            // Object.assign 默认也会拷贝可枚举的Symbols
            Object.getOwnPropertySymbols(source).forEach(sym => {
                let descriptor = Object.getOwnPropertyDescriptor(source, sym)
                if (descriptor.enumerable) {
                    descriptors[sym] = descriptor
                }
            })
            Object.defineProperties(target, descriptors)
        })
        return target
    }
 
    return completeAssign(deepClone(obj), family)
}
```
#### 三、属性标识符，Object.defineProperty
##### 1、configurable修改成false是单项操作，不可撤销
##### 2、禁止扩展，Object.preventExtensions(myObject),在非严格模式下，创建新的属性会静默失败，在严格模式下会报错；
##### 3、密封，Object.seal()，会调用preventExtensions 并把所有属性configurable设为false
##### 4、冻结，Object.freeze()，会调用seal 并把所有属性writable设为false

### 七、值
#### 1、delete删除数组单元后，数组length并不会发生变化
#### 2、判断NaN，ES6 Math.isNaN（）或者利用NaN不等于自身的特点
```
if(!Number.isNaN){
  Number.isNan = function(n){
    return n !== n;
  }
}
```
#### 3、Object.is()的polyfill
```
if(!Object.is){
  Object.is = function(v1,v2){
    // 判断是否是-0
    if(v1 ===0 && v2 === 0){
      return 1 / v1 === 1/v2
    }
    // 判断是否是NaN
    if(v1 !== v1 ){
      return v2 !== v2;
    }
    return v1 === v2;
  }
}
```
