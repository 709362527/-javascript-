## 一、块级作用域
  ES6以后，javascript引入了let，终于有了创建完整的、不受约束的块作用域的方法；
  ES6以前，javascript只有部分写法具有块级作用域的功能：
  (1)with
  (2)catch
  在ES6以前，想要使用块级作用域就要利用catch来实现
  
  try{
    throw undefined;
  }catch(a){
    a=1;
    console.log(a);//1
  }
  console.log(a);//Uncaught ReferenceError: a is not defined
  
  可以看到示例中变量a并没有暴露到全局，外部也没有函数作用域，是catch的块级作用域起了作用；
  
## 一、this
