## webpack4.x 打包工具

1.首先搭建好环境

```shell
npm init -y 初始化一个项目

npm install webpack webpack-cli --save-dev
```

2.

npm i  安装node_moudles
文件夹

    dist
    src 
    	css
    	js
    	image
    	main.js
    	index.html
    webpack.config.js
    package.json

npm init -y  出现package.jsoon

在4.x中webpack的默认路径出来，入口出口都设置好了

src下的index,js

打包到dist下的main.js

## 1.使用import $ from 'jquery'解析不了

解决：
使用webpack处理  
webpack .\src\main.js  .\dist\bundle.js  指定出口入口
	指定处理的文件
不想手动指定入口与出口文件就创建
webpack.config.js
指定入口出口.将出口入口暴露
1.使用webpack打包

1.首先发现没有指定入口与出口
2.寻找webpack.config.js文件
3.找到配置后解析执行，找到配置对象
4.拿到对象后，进行打包

## 2.安装 webpack-dev-server实现自动打包编译

npm i webpack-dev-server -D

node nodemon
webpack webpack-dev-server
实时加载
配置package.json
script
dev:'webpack-dev-server'

注意要先安装
npm -i webpack -D

## 3.没有出现效果，在html中引入的js路径不对

在webpack-dev-server打包的没有在本地磁盘上，托管到项目的内存中
和dist src 同级

## 4.修改端口，指定首页 --hot 及时更新，没有编译打包，更新少量的代码

配置hot的两种方式
4.1 在    package.json中配置

```json
 "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "dev": "webpack-dev-server  --open --port 3000  --contentBase src --hot"
  },
```

```js

    --open  打开浏览器
    --port 3000 指定端口号
    --contentBase src 开始默认路径
     --hot  热更新
     
	4.2.三步走 

     1.引入webpack模块
     var webpack=require('webpack')
     2. devServer:{
    		contentBase:'./src',  //设置服务器访问的基本目录
    		host:'localhost', //服务器的ip地址
    		  port:8080,  //端口
    			open:true, //自动打开页面
    			hot: true
    			},
    3.在插件中配置
    	plugins:[
 			 new webpack.HotModuleReplacementPlugin()
			]
```

## 5.将页面放入到内存中

npm i html-webpack-plugin -D 这样早页面中就不用引入js

    <script src="./bundle.js"></script>

在webpack.config.js配置插件
插件自动创建一个合适的script 并且引用

## 6.使用import 导入 css

安装loader

## 7.在css样式中引入url地址 不管是图片还是字体库

npm i url-loader file-loader -D

还是在loader中配置规则，如第六点

## 8.  有.babelrc,语法转化
1.安装下载相关的包
2.添加一个.babelrc配置文件

```js
{
    "presets":["@babel/preset-env"]
}
```

3.在webpack.config.js中配置相应的规则，注意排除 
node_modules下的文件，否则很耗内存

```js
 {
      test:/\.js$/,
      exclude:/node_modules/,
      use:'babel-loader'
    }
```







### 几步走，就那么点东西

1.初始化 npm init -y

2.入口文件出口文件

3.打包入口文件的js

4.既然是打包，面对不用的文件语法，默认只识别js.所以安装插件来loader来识别

5.编译完后，要想修改webpack退出watch来监听，但是这样文件多的话修改的也多

6.所以引入了webpack-dev-server的插件来成为一个服务器，打包好的文件放入内存之中，快速访问，拥有自己的端口号，相当于webpack+服务器 它拥有自己的热部署来更新修改的内容

7.将最终编译形成的出口文件，可以被html页面应用，我用使用html-webpack-plugin插件，也可以优化html文件大小，这个的作用是定义初始访问的页面，无需在html中引入js文件

8.为了识别高级es6语法，使用.babelrc来转化低级的可以识别，但是要排除node_modules中的，否则很耗内存

9.为了优化最终形参的项目我们可以对css进行压缩处理，主要处理就是这些了