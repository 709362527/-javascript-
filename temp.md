## 一、块级作用域
* ES6以后，javascript引入了let，终于有了创建完整的、不受约束的块作用域的方法；
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
* 可以看到示例中变量a并没有暴露到全局，外部也没有函数作用域，是catch的块级作用域起了作用；
* 不过值得注意的是try catch性能很糟糕
 
## 二、this

```
var obj={
  id:'awesome',
  cool:function(){
    console.log(this.id)
  }
}
var id='not awesome';

obj.cool();//awesome
setTimeout(obj.cool,100);//not awesome
```
上例中cool函数丢失了this的绑定
