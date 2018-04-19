## 一、词法作用域
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
(a) o2.a 返回的是undefined，说明with在定义a的时候对其进行了LHS的使用，所以创建了a变量，但是没有赋值;<br>
(b) a返回2是因为with修改时生效的是其所在的词法作用域，即foo，foo中返回的this是window，所以给了全局;<br>




##  二、块级作用域
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
 
## 三、this

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
