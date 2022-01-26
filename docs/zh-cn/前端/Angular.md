



# 1.安装脚手架

```shell
    npm install -g @angular/cli
```

安装cnpm

```shell
  npm install -g cnpm --registry=https://registry.npm.taobao.org
```

如果有警告修改环境变量



# 2.新建项目 cd 文件



   ```shell
 ng new 项目名
   ```

​    跳过依赖包

```shell
 ng new 项目名 --skip-install
```

#  3. 编译运行

​    ng serve --open



# 4.在vscode中编译，代码高亮 安装插件

# 5.安装组件

```shell
 ng g component components/home
```

 创建提个home组件

# 6.各个目录结构的含义

# 7.ts的空模板

```js
  import { Component, OnInit } from '@angular/core';
```

​       

```js
  @Component({
    selector: 'app-cart',
    templateUrl: './cart.component.html',
    styleUrls: ['./cart.component.css']
  })
  export class CartComponent implements OnInit {
   //第一变量
    constructor() { }
   
    ngOnInit() {
    页面一开始执行的
    }
    //定义方法
   
  }
```

# 8.路由跳转script中

​    点击事件触发

```js
    {
        path: 'tab3',
        component:Tab3Component,
        children: []
    },
```


```html
<button (click)="tab3()">进入tab3</button>
```


```js
import {Router } from '@angular/router';
constructor(private router:Router ) { }

tab3(){
    this.router.navigateByUrl("tab3")
    //或者是以下这种
    this.router.navigate(["tab3"])
  }
```

# 9.父子组件之间的传值

​      父组件

```html
<app-header [title]="title"></app-header>
```

​      定义

```js
 public title:any='我是父组件的title'
```

​      子组件
​      1.引入input模块

```js
 import { NgModule,Input } from '@angular/core';
```

```js
  @Input() title:any;
```

# 10.父组件传方法 父组件自己的全部给子组件

​      父组件 分别是属性 方法 自身全部

```html
     <app-header [title]='title' [run]='run' [home]='this></app-header>
```

​    子组件

```html
  <button (click)="getMethod()">点击</button>
  @Input() run:any;
  getMethod(){
      this.run();//加括号表示执行
    }
    执行方法


传整体
  <app-header [home]="this"></app-header>
  子组件
  <button (click)="getMethod()">点击</button>

  传方法与父组件全部都要经过事件处理
```

```js
 import { NgModule,Input } from '@angular/core';

@Input() home:any;

  getMethod(){
  this.home.run()
  }
```



# 11.父组件获取子组件

      ### @ViewChild

​      子组件定义一个属性

```js
public childinfo:any="我是子组件的数据"

childmethod(){
  	alert("我是子组件的方法")
  }
```



```js
  父组件
  import { NgModule,ViewChild } from '@angular/core';
	
 	@ViewChild('newschild',{ static: true })  newschild:any;
//定义方法
  childrun(){
      alert(this.newschild.message)
  }

  getChildMessage(){
    this.newschild.childMethod();
  }
```

HTML  通过事件触发

```html
<app-news-child  #newschild></app-news-child><!--使用id标识-->
<button (click)="childrun()">点击获取子组件的值</button>
<button (click)="getChildMessage()">点击执行子组件的方法</button>
```



# 12.post请求数据

app.moudle.ts

```js
import { HttpClientModule,HttpClientJsonpModule,HttpHeaders } from'@angular/common/http';
```

​    引入模块

```js
 imports: [
    HttpClientModule,
    HttpClientJsonpModule,//引入jsonp的模块
    HttpHeaders
  ],
```

引入的页面jsonp

```js
import {HttpClient} from "@angular/common/http";
constructor(public http:HttpClient) { }
  getdata(){
    var api = "http://a.itying.com/api/productlist";
    this.http.jsonp(api,'callback').subscribe((response:any) => {
    //callback 必须要有个回调
      console.log(response);
});
```

​    设置消息头
​    post传递参数

```js
const httpOptions = {
 headers: new HttpHeaders({ 'Content-Type': 'application/json' })
};
var api = "http://127.0.0.1:3000/doLogin";
this.http.post(api,{username:'张三',age:'20'},httpOptions).subscribe((response:any) => {
 console.log(response);
});
```

get方式

```js
var api = "http://a.itying.com/api/productlist";
this.http.get(api).subscribe((response:any) => {
 console.log(response);
});
```



接受后台返回值

 ### 执行时服务器必须允许跨域



# 13.判断后台

​    判断一个服务器是否支持后端
​    http://a.itying.com/api/productlist?callback=aaa
​    返回
​    :aaa({"result":数据})

# 14.新建一个服务(待完善)

    ```shell
    ng service  services/httpservice
    ```

封装axios



# 15.安装axios的第三方请求方式

​        npm install --save//--save表示写入package.json文件

**4.安装**

```js
使用 npm
 	npm install  axios --save       
使用 bower:
	$ bower install axios
使用 cdn:
	<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
```

  引入 import axios from 'axios'

```js
//执行get请求
// Make a request for a user with a given ID
axios.get('/user?ID=12345')
  .then(function (response) {
    console.log(response);
  }).catch(function (error) {
    console.log(error);
  });

// Optionally the request above could also be done as
axios.get('/user', {
    params: {
      ID: 12345
    }
  }).then(function (response) {
    console.log(response);
  }).catch(function (error) {
    console.log(error);
  });
```

.post 注意： axios中post请求如果你直接使用npmjs.com官网文档， 会有坑
     解决步骤： 

1. 先设置请求头 

2. 2.实例化 URLSearchParams的构造器函数得到params对象

3. 使用params对象身上的append方法进行数据的传参
   // 统一设置请求头

   ```js
   axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded'; 
   let params = new URLSearchParams()
   // params.append(key,value)
   params.append('a',1)
   params.append('b',2)
   
   axios({
       url: 'http://localhost/post.php',
       method: 'post',
       data: params，
         headers: {  //单个请求设置请求头
          'Content-Type': "application/x-www-form-urlencoded"
       }
   })
   .then(res => {
       console.log( res )
   })
   .catch( error => {
       if( error ){
       throw error
   }
   })
   ```

   ### 在vue中传参时

   ```js
    created(){
       var params = new URLSearchParams();
       params.append('currentPage', this.currentPage);
       params.append('pageSize', 3);
         let url="/api/user/itemsPages2";
     axios({
       url: url,
       method: 'post',
       data:params,
     }).then((resp)=>{
        this.tableData=resp.data
        this.tolal=resp.data.totalNum
        this.totalPage=resp.data.totalPage,
         console.log(resp.data+"app/vue")
        }).catch((error)=>{
           console.log(error);
     })
     },
   ```

   ### 原因

   默认axios是使用的是Payload形式提交数据，也就是Content-Type:"application/json"

   如何切换呢，我们需用应用以下方式：

   ```js
   1. var params = new  URLSearchParams();  
   2. params.append('param1', 'value1');  
   3. params.append('param2', 'value2');  
   ```

   

   它的意思，其实就是把这样的数据（对象）{ name:"yangxu",age:23 } 转换成这样的数据（字符串） "name=yangxu&age=23"这样的查询字符串。

# 16.使用get传值跳到页面或者动态路由的方式

# 17.动态js的跳转

​    路由的层级关系

```js
{
  path: 'product',
  component:ProductComponent,
  children: [
    {
      path: 'ptype',
      component:PtypeComponent,
    },
    {
      path: 'plist',
      component:PlistComponent,
    },
    {
      path: 'details/:id',
      component:DetailsComponent,
    },
    {
      path: '**',
      redirectTo:'ptype',
    },

  ]
},
```

根据Id显示商品的详情

### plist组件

####  1.页面的跳转 it._id获取的动态值 

```html
 <a [routerLink]="[ '/product/details/',it._id]"> {{it._id}}+{{it.title}}</a>
```

#### 2.js中路由跳转

```js
import {Router} from '@angular/router'    在控制器里
  constructor(private router:Router){
      
  }
    //路由跳转
  this.router.navigate(['/news',配置动态路由])
   // 路由中
    //引入界面，然后配置路由
  path:'production/:pid',component:组件
```

###  detalis组件.接收

```js
import {ActivatedRoute} from '@angular/router'

constructor(private router:ActivatedRoute,public http:HttpClient) { }
//页面初始化
  ngOnInit() {
    this.router.params.subscribe((data:any)=>{
      console.log(data)
      var api='http://a.itying.com/api/productcontent?id='+data.id
      this.http.get(api).subscribe((response:any)=>{
        console.log(response.result)
        this.details=response.result[0]
      })

    })
  }
```

# 18.父子组件的路由嵌套的运用与菜单

  在路由中引用

LoaderMan
学习笔记<言简-实用-高效>
angular 中父子路由

1. 创建组件引入组件

   ```js
   import { HomeComponent } from './components/home/home.component';
   import { SettingComponent } from './components/home/setting/setting.component';
   import { WelcomeComponent } from './components/home/welcome/welcome.component';
   ```

2. 配置路由

   ```js
     {
       path: 'home',
       component:HomeComponent,
       children: [
         {
           path: 'welcome',
           component:WelcomeComponent,
         },
         {
           path: 'setting',
           component:SettingComponent,
         },
         {
           path: '**',
           redirectTo:'welcome',
         }
       ]
    },
   ```

   Home组件

   ```js
   <div>
   <a [routerLink]="[ '/home/welcome']">欢迎首页</a>
   <a [routerLink]="[ '/home/setting']">系统设置</a>
   <div class="center" style="margin-top: -20px">
      <!--子组件存在的位置不能丢-->
       <router-outlet></router-outlet>
   </div>
   </div>
   ```

路径是父路由与子路由的搭配

# 19.自定义封装服务（待完善）

 1.创建服务
 2.在app.module.ts中引入，在provider加入模块
 3.在相应的模块上引入provider加入的模块

# 20.自定义模块（待完善）

​    ng -g moudle moudle/user
目录解构
  module
​      -user                             文件夹
​        -component                      文件夹
​            -login                      文件夹
​               -login.component.html    文件
​                 -...
​            -register                   文件夹
​               -register.component.html 文件
​                 -...
​        -user.component.html             文件
​        -...
​        -user.module.ts                  文件
模块化，针对不用的模块给予不一样的模块库
user模块与app全局模块的比较
1.都有一个相对于自己模块的根组件
2.在user模块中要想被其他使用组件，就要暴露exports:[UserComponent]
3.app全局模块可以引用user模块
4.本模块的组件只能在本模块使用 <app-login></app-login>

## 21.数组的循环以及一些属性的使用

### 1.循环

### 2.判断

#### ng-show

```html
ng-show 指令
ng-show 指令隐藏或显示一个 HTML 元素。

AngularJS 实例
<div ng-app="">

<p ng-show="true">我是可见的。</p>

<p ng-show="false">我是不可见的。</p>

</div>

ng-show 指令根据 value 的值来显示（隐藏）HTML 元素。

你可以使用表达式来计算布尔值（ true 或 false）:
 
```



#### ng-disabled

```html
<div ng-app="" ng-init="mySwitch=true"> 一开始的默认值
<p>
<button ng-disabled="mySwitch">点我!</button>
</p>
<p>
<input type="checkbox" ng-model="mySwitch"/>按钮
</p>
<p>
{{ mySwitch }}
</p>
</div> 
```



### 3.数据的双向绑定（引入一个模块）

### 4. 样式的动态绑定

### 5.事件的绑定(点击，滑动，键盘，表单改变，鼠标触摸...）

​	在事件中传入$event参数

### 6.路由页面的跳转

### 7. Select(选择框)

### 8.显示html文本 

html='< h3>html文本 </ h3>'

```html
<span [innerHTML]='html'></span>
```



在 app.module.ts 中引入 HttpClientModule 并注入
```js
    import {HttpClientModule} from '@angular/common/http';
    imports: [
      BrowserModule,
      HttpClientModule
    ]
```

**Angular get 请求数据**
在用到的地方引入 HttpClient 并在构造函数声明

```js
    import {HttpClient} from "@angular/common/http";
    constructor(public http:HttpClient) { }
```
get 请求数据

```js
    var api = "http://a.itying.com/api/productlist";
    this.http.get(api).subscribe(response => {
      console.log(response);
    });`在这里插入代码片`
```

**Angular post 提交数据**
在用到的地方引入 HttpClient、HttpHeaders 并在构造函数声明 HttpClient

```js
    import {HttpClient,HttpHeaders} from "@angular/common/http";
    constructor(public http:HttpClient) { }
```

post 提交数据

```js
    const httpOptions = {
      headers: new HttpHeaders({ 'Content-Type': 'application/json' })
    };
    var api = "http://127.0.0.1:3000/doLogin";
    this.http.post(api,{username:'张三',age:'20'},httpOptions).subscribe(response => {
      console.log(response);
    });

```

**Angular Jsonp 请求数据**
在 app.module.ts 中引入 HttpClientModule、HttpClientJsonpModule 并注入

```js

    import {HttpClientModule,HttpClientJsonpModule} from
    '@angular/common/http';
    imports: [
      BrowserModule,
      HttpClientModule,
      HttpClientJsonpModule
    ]
```

在用到的地方引入 HttpClient 并在构造函数声明

```js
    import {HttpClient} from "@angular/common/http";
    constructor(public http:HttpClient) { }

```

jsonp 请求数据

```js
    var api = "http://a.itying.com/api/productlist";
    this.http.jsonp(api,'callback').subscribe(response => {
    console.log(response);
    });
```

Angular 中使用第三方模块 axios 请求数据
安装 axios

    cnpm install axios --save


用到的地方引入 axios

```js
    import axios from 'axios';
    axios.get('/user?ID=12345')
    .then(function (response) {
    // handle success
    console.log(response);
    })
    .catch(function (error) {
    // handle error
    console.log(error);
    })
    .then(function () {
    // always executed
    });
```

**封装service作为http服务**

```js
    import { Injectable } from '@angular/core';
    import axios from 'axios';
    @Injectable({
      providedIn: 'root'
    })
    export class HttpserviceService {
      constructor() { }
      axiosGet(api){
         return new Promise((resolve,reject)=>{
            axios.get(api)
              .then(function (response) {
                // handle success     
                resolve(response)
              });
        })
    
      }  
    }


```









