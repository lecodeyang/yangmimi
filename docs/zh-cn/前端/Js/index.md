### Es6知识点

1. ### ES5 变量

   ### var 声明

   1.可以重复声明 不报错

   ```js
   var a=12;
   var a=5;
   alert(a) //5
   ```

   2.无法限制修改

   ```js
   PI（圆周率）可以修改PI
   ```

   3.没有块级作用域（语法块）

   ```
   {
   	变量=123
   }
   for(...){
   	var a=12
   }
   alert(a) 12
   ```

   aa

   ### ES6语法 let  const 不能重复声明

   ####  let 是变量

   ```js
   let a=12;
   a=5
   alert(a)  5
   ```

    #### const 常量

   ```js
   const a=12
   a=5
   alert(a) 报错
   ```

   #### 两者皆为块级作用域  更容易合作开发

   ```js
   if(true){
       let a=1;
       const b=1;
   }
   alert (a=1)报错 未定义
   alert (b=1)报错 未定义
   ```

2. 函数

   #### 箭头函数

   #### 1.如果只有一个参数（）可以省

   ```js
   resp=>{
   console.log(resp)
   }
   let show=(n1,n2)=>{
       alert(n1+n2)
   }
   ```

   #### 2.如果只有一个返回值

   ```js
   (a)=>a*2
   等价
   (a)=>{
       return a*2
   }
   ```

   #### 3.this的指向问题(<span style="color:red">重点</span>)

   ```js
   普通函数：根据调用我的人（谁调用我，我的this就指向谁）
   
   箭头函数：根据所在的环境（我再哪个环境中，this就指向谁）
   ```

   #### 4.Es6的传参问题 ...args 必须放最后

   ```js
   let show=(a,b,...args)=>{
      
       alert(a,b,...args)
   }
       
   show(12,23,54,)
   ```

   #### 5.展开数组

   ```js
   let arr=[1,2,3];
   ...arr;
   let show=(a,b,c)=>{
       alert(a)
       alert(b)
       alert(c)
   }
   show(1,2,3)
   等价于
   show(...arr)
   ```

   #### 6.解构赋值(两边结构一致)语法糖

   ```js
   //原始
   let arr=[1,2,3]
   let a=arr[0];
   let b=arr[1];
   let c=arr[2]
   
   //一一对应，右边的给左边 
   let [a,b,c]=[1,2,3]
   let {a,c,d}={a:12,c:13,d:14}
   
   //----------------------------------------
   let{{a,b},[n1,n2,n3],num,str}=
      {{a:1,b:2},[1,2,3],12,"str"}
   let{json,arr,num,str}=
      {{a:1,b:2},[1,2,3],12,"str"}
      
   console.log(a,b,c,n1,n2,n3,num,str)
   console.log(json,arr,num,str)
   
   //-----------------------------------------
   let {a,b}={12,13}
   console.log(a,b)报错 因为右边不是个东西 非数组，非对象，非json
   //将值打印出来
   ```

   

3. 数组

   #### 数组新方法

   ```js
   map                  映射        一个对一个
   [12,99,78]
   ["不及格","优秀","良好"]
   let score=[12,99,78]
   //返回一个新数组
   let result=score.map(item=>item>=60?"及格":"不及格")
   alert(result)
   //-------------------------------------------
   let arr=[12,5,6]
   let result=arr.map((item)=>{
       return item*2 //数值乘以2
   })
   alert(result)
   ```

   ```js
   reduce               汇总        一堆出来一个
   [19,59,99]  算总分 平均分
   let arr=[19,59,99] 
   tmp 中间值
   item 数值
   index 下标
   总分
   let sum=arr.reduce((tmp,item,index)=>{
       return tmp+item
   }
   alert(sum)
   平均分
   let average=arr.reduce((tmp,item,index)=>{
       if(index!=arr.length-1){
       return tmp+item
       }else{
        return (tmp+item)/index
       }
   })
   alert(average)
   ```

   ```js
   filter               过滤器            选择过滤
   let arr=[19,59,99] 
   let result=arr.filter(item=>{
       //return true;//全部保留
       //return false //全部不保留
    /* 本身就是boolen值
            if(item>20){
               return true;
               }else{
               return false;
               }
   	*/ 
   	等价于
       return item>20
   })
   
   //------------------------------------------
        let objs=[
            {name:"杨乐乐",age:18},
            {name:"谷咕咕",age:22},
            {name:"阿姜",age:21},
            {name:"周某人",age:22},
        ]
       let result=objs.filter(json=>json.age=22)
       console.log(result)
        返回/*
           {name:"周某人",age:22},
           {name:"谷咕咕",age:22},
           */
   
   ```

   ```js
   forEach              迭代          循环遍历
   let arr=[1,2,3]
   let result=arr.forEach((item,index)=>{
       alert(item +index)
   })
   ```

   

4. 字符串

   #### 字符串操作

   ##### （1）多了两个新方法

   ###### startsWith

   ```js
   let str="http://baidu.com"
   if(str.startsWith("http://")){
       alert("普通网址")
   }   //是一个网址 返回值时boolen类型
   ```

   ###### endsWith 进行邮箱的验证，文件类型的扩展名都可以

   ##### （2）字符串模板 不需要换行符，可以折行

   字符串拼接 反单引号

   ```js
   let str=`abc`;
   let str2=`a${str}bc`
   alert(str2)//aabcbc
   ```

5. 面向对象

6. promise-承诺

   | 异步                                                        | 同步                           |
   | :---------------------------------------------------------- | ------------------------------ |
   | 操作之间没关系，同时进行多个操作。回调地狱,一直使用ajax请求 | 同时能做一件事。一个再走下一个 |
   | 代码复杂                                                    | 代码简单                       |

   promise==消除异步操作

   用同步的方式书写异步代码

   ```js
   let p=new Promise(function(resolve,reject){
       /*	 resolve 成功推荐
      		 reject  失败回调
       */
       $.ajax({
           url:'aaa.txt',
           dataType:'json',
           success:(arr)=>{
           resolve(arr);
       		},
            error:(err)=>{
           reject(err);
      		 }
       })
   })
   p.then(function(){
       alert("成功回调")
   },function(){
       alert("失败回调")  
          });
   
   ```

   好处显现

   ```js
   let p=new Promise(function(resolve,reject){
       /*	 resolve 成功推荐
      		 reject  失败回调
       */
       $.ajax({
           url:'aaa.txt',
           dataType:'json',
           success:(arr)=>{
           resolve(arr);
       		},
            error:(err)=>{
           reject(err);
      		 }
       })
   })
   let p1=new Promise(function(resolve,reject){
       /*	 resolve 成功推荐
      		 reject  失败回调
       */
       $.ajax({
           url:'bbb.txt',
           dataType:'json',
           success:(arr)=>{
           resolve(arr);
       		},
            error:(err)=>{
           reject(err);
      		 }
       })
   })
   Promise.all([
       p,p1
   ]).then(function(arr){
       let [res1,res2]=arr;
       //arr数组作为结果
       console.log(res1) //aaa.txt文件内容
        console.log(res2)//bbb.txt文件内容
       alert('全部成功了')
   },function(){
       alert('至少一个失败了')
   })
   ```

   简化操作 封装promise

   ```js
   function createPromise(url){
       return new Promise(function(resolve,reject){
         $.ajax({
           url:url,
           dataType:'json',
           success:(arr)=>{
           resolve(arr);
       		},
            error:(err)=>{
           reject(err);
      		 }
       })
       } )
   }
   Promise.all([
      createPromise('aaa.txt')
      createPromise('bbb.txt')
   ]).then(function(arr){
       let [res1,res2]=arr;
       //arr数组作为结果
       console.log(res1) //aaa.txt文件内容
       console.log(res2)//bbb.txt文件内容
       alert('全部成功了')
   },function(){
       alert('至少一个失败了')
   })
   ```

   更进一步 jquery的封装promise

   ```js
   let p=$.ajax({url:'aaa.txt',dataType:'json'})
   返回的是promise对象
   直接
   Promise.all([
     $.ajax({url:'aaa.txt',dataType:'json'})
     $.ajax({url:'bbb.txt',dataType:'json'})
   ]).then(function(arr){
       let [res1,res2]=arr;
       //arr数组作为结果
       console.log(res1) //aaa.txt文件内容
       console.log(res2)//bbb.txt文件内容
       alert('全部成功了')
   },function(){
       alert('至少一个失败了')
   })
   ```

   最简单大致的写法

   ```js
   Promise.all([ajax1,ajax2]).then(success=>{},error=>())
   ```

   ```js
   封装为一个promise方法
   console.log(111);
       let promise = new Promise((resolve, reject) => {
           setTimeout(function () {
               console.log(222);
               resolve("hello word")
           },3000)
       })
       
   promise.then((data)=>{
   		console.log(333);
   		console.log(data);
   }).catch(()=>{
   console.log(444);//错误进入
   })
   /*
   111
   222
   333
   hello word
   */
   ```

   

   ### Promise.race([])	优先加载速度快的

   

7. generator

8. 模块化

   在一个单独的js文件中export抛出模块

   ```js
   export  function show(){
       console.log("模块化执行show的方法")
   }
   
   export let show1=()=>{
       console.log("模块化执行show1的方法")
   }
   ```

   ```js
   import {show,show1} from './js/es6'
   show();
   show1();
   //分别执行控制台打印的内容
   ```

   1、export
       一个模块就是一个独立的文件。该文件内部的所有变量，外部无法获取。如果你希望外部能够读取模块内部的某个变量，就必须使用export关键字输出该变量。下面是一个 JS 文件，里面使用export命令输出变量。

   export写法一般如下：

   ```js
   var firstName = 'name';
   var lastName = 'last';
   var year = 1996;
   
   export { firstName, lastName, year };
   ```

   

   通常情况下，export输出的变量就是本来的名字，但是可以使用as关键字重命名。例：

   ```js
   function v1() { ... }
   function v2() { ... }
   
   export {
     v1 as streamV1,
     v2 as streamV2,
     v2 as streamLatestVersion
   };
   ```

   上面代码使用as关键字，重命名了函数v1和v2的对外接口。重命名后，v2可以用不同的名字输出两次。

   2、import
   使用export命令定义了模块的对外接口以后，其他 JS 文件就可以通过import命令加载这个模块。例：

   ```js
   import { firstName, lastName, year } from './profile.js';
   
   function setName(element) {
     element.textContent = firstName + ' ' + lastName;
   }
   ```

   当然，如果想为输入的变量重新取一个名字，import命令要使用as关键字，将输入的变量重命名，这也是上文说通常情况下，export输出的变量名不变的原因。例：

   ```js
   import { lastName as surname } from './profile.js';
   ```

   另外，注意import命令具有提升效果，会提升到整个模块的头部，首先执行。

   3、export default
      从前面的例子可以看出，使用import命令的时候，用户需要知道所要加载的变量名或函数名，否则无法加载。这就产生一个问题，使用模块者不愿阅读文档，不想知道变量名。此时就要用到export default命令，为模块指定默认输出。

   ```js
   person.js文件
   export default {
       firstName:'first',
       lastName:'last',
       age: 11
   }
   此时import导入写成：
   
   import person from 'person'; 
   
   console.log(person.age)  //11
   ```

   本质上，export default就是输出一个叫做default的变量或方法，然后系统允许你为它取任意名字。

   4、总结拓展
   export default 向外暴露的成员，可以使用任意变量名来import
   在一个模块中，export default 只允许向外暴露一次。
   在一个模块中，可以同时使用export default 和export 向外暴露成员
   export可以向外暴露多个成员，同时，如果某些成员，在import导入时不需要，可以在import时不写。
   使用export导出的成员，必须严格按照导出时候的名称，来使用{ }按需接收

## 数组专题

**arr.push() 从后面添加元素，返回值为添加完后的数组的长度**

```js
let arr = [1,2,3,4,5]
console.log(arr.push(5))   // 6
console.log(arr) // [1,2,3,4,5,5]
```

**2 arr.pop() 从后面删除元素，只能是一个，返回值是删除的元素**

```js
let arr = [1,2,3,4,5]
console.log(arr.pop())     // 5
console.log(arr)  //[1,2,3,4]
```

**3 arr.shift() 从前面删除元素，只能删除一个 返回值是删除的元素**

```js
let arr = [1,2,3,4,5]
console.log(arr.shift())  // 1
console.log(arr)   // [2,3,4,5]
```

**4 arr.unshift() 从前面添加元素, 返回值是添加完后的数组的长度**

```js
let arr = [1,2,3,4,5]
console.log(arr.unshift(2))    // 6
console.log(arr)  //[2,1,2,3,4,5]
```

**5 arr.splice(i,n) 删除从i(索引值)开始之后的那个元素。返回值是删除的元素**

　　参数： i 索引值      n 个数

```js
let arr = [1,2,3,4,5]
console.log(arr.splice(2,2))     //[3,4]
console.log(arr)    // [1,2,5]
```

**6 arr.concat() 连接两个数组 返回值为连接后的新数组**

```js
let arr = [1,2,3,4,5]
console.log(arr.concat([1,2]))  // [1,2,3,4,5,1,2]
console.log(arr)   // [1,2,3,4,5]
```

**7 str.split() 将字符串转化为数组**

```js
let str = '123456'
console.log(str.split('')) // ["1", "2", "3", "4", "5", "6"]
```

**8 arr.sort() 将数组进行排序,返回值是排好的数组，默认是按照最左边的数字进行排序，不是按照数字大小排序的，见例子。**

```js
let arr = [2,10,6,1,4,22,3]
console.log(arr.sort())   // [1, 10, 2, 22, 3, 4, 6]
let arr1 = arr.sort((a, b) =>a - b)  
console.log(arr1)   // [1, 2, 3, 4, 6, 10, 22]
let arr2 = arr.sort((a, b) =>b-a)  
console.log(arr2)  // [22, 10, 6, 4, 3, 2, 1]
```

**9 arr.reverse() 将数组反转,返回值是反转后的数组**

```js
let arr = [1,2,3,4,5]
console.log(arr.reverse())    // [5,4,3,2,1]
console.log(arr)    // [5,4,3,2,1]
```

**10 arr.slice(start,end) 切去索引值start到索引值end的数组，不包含end索引的值，返回值是切出来的数组**

```js
let arr = [1,2,3,4,5]
console.log(arr.slice(1,3))   // [2,3]
console.log(arr)    //  [1,2,3,4,5]
```

