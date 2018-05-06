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
obj.foo();
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
* 箭头函数=>绑定的this不能修改
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
