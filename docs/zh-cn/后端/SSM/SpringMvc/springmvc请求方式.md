# 请求数据的几种方式以及springmvc的接收

## 一. form表单提交

### GET方式

前端表单传参

```jsp
  <form action="http://localhost:8080/test" method="get">
    <input type="text" name="username" />
    <input type="text" name="password"/>
    <input type="submit" value="Submit" />
  </form>
```

后端参数接收，因为 form 表单使用 get 方法的时候，Content type 的值默认为空。所以后台接收代码不需要指定 consumes 属性

```java
    @ResponseBody
    @RequestMapping(value = "/test", method = RequestMethod.GET)
    public String test1(@RequestParam(value="username") String username,
                        @RequestParam(value="password") String password){
        System.out.println("======GET======");
        System.out.println("username=" + username);
        System.out.println("password=" + password);
        return "SUCCESS";
    }
```

打印结果：

```shell
======GET======
username=wangbo
password=123456
```

可以看出 FORM 表单发出的 GET 请求直接通过注解 @RequestParam 进行参数接口即可。



### POST方式

前端表单传参

```html
  <form action="http://localhost:8080/test" method="post">
    <input type="text" name="username" />
    <input type="text" name="password"/>
    <input type="submit" value="Submit" />
  </form>
```

后端参数接收，

因为 form 表单使用 post 方法的时候，

Content type 的值默认为**application/x-www-form-urlencoded;charset=UTF-8。**

所以后台接收代码需要指定 consumes 属性。

```java
consumes = "application/x-www-form-urlencoded;charset=UTF-8"
```

```java
    @ResponseBody
    @RequestMapping(value = "/test", method = RequestMethod.POST, consumes = "application/x-www-form-urlencoded;charset=UTF-8")
    public String test(@RequestParam(value="username") String username,
                       @RequestParam(value="password") String password,
                       User user){
        System.out.println("======POST======");
        System.out.println("username=" + username);
        System.out.println("password=" + password);
        System.out.println("user=" + user);
        return "SUCCESS";
    }
```

打印结果

```shell
======POST======
username=wangbo
password=123456
user=username=wangbo; password=123456
```

可以看出，FORM 表单发出的 POST 请求可以直接通过注解 @RequestParam 进行参数接收，

也可以使用字段对应封装的 Java Bean 对象来接收参数。注意 Java Bean 对象上不需要注解。

User 代码，为了更清楚的打印对象，重写了 toString 代码。

## 二. resuful风格的获取get的提交

### @PathVariable

只能接收 URL 路径里的参数。

```js
function update(e) {
    window.location.href="/selectItem/"+e;
}
```

```java
   @GetMapping(value ="/selectItem/{id}")
    public String  selectItem(@PathVariable("id") Integer id,Model model){
        School school= schoolService.selectItem(id);
        model.addAttribute("school",school);
        return "updatelist";
    }
```



### @RequestParam

只能接收 URL 问号后跟着的参数，不管是 GET 还是 POST，虽然一般只有 GET 请求才会在 URL 后边跟参数，问号 ？ 后面的部分，使用 & 区分参数。

```java
http://localhost:8080/api/user/login/test?username=2222222&pass=333333333
@RequestParam("username")String username,
@RequestParam("pass")String pass
```



## 三. Post请求

### @RequestBody 

只能接收请求体中的参数，也就是只能是 POST 请求才有请求体，GET 请求没有请求体，请求体分两种情况参数

（1）使用String接收

比如前端参数在请求体中传的是 username=18514335982&pass=12345，Content type 为 text/plain;charset=UTF-8

则后台接收到的 param 即为 username=18514335982&pass=12345 格式

```java
@RequestBody String param
```

（2）使用封装的 bean 或者 JSONObject 接收（常用）

前端必须使用 JSON 格式的数据，Content-Type 必须为 application/json，请求体中参数为 {"username":"18514335982","pass":"12345"}

```java
@RequestBody User user
@RequestBody JSONObject jsonObject
```

```java
@PostMapping("/login/test")
    public ResultBuilder userLogin1(@RequestHeader(Constants.ACCEPT_VERSION)String version,
                                    @RequestHeader(Constants.ACCESS_TOKEN)String token,
                                    @RequestParam("username")String username,
                                    @RequestParam("pass")String pass,
                                    @RequestBody User user){

        logger.debug("username======" + username);
        logger.debug("pass======" + pass);
        logger.debug("user---username==" + user.getUsername());
        logger.debug("user---pass==" + user.getPass());
        return new ResultBuilder(StatusCode.SUCCESS);
    }
```

## 四.ajax发送请求

```js
var data = {'id':id,'name':name};
$.ajax({ 
            type:"POST", 
            url:"user/saveUser", 
            dataType:"json",      
            //contentType:"application/json", //不能添加这个头           
            data:data, //这里不能进行JSON.stringify,发送请求的数据在:form data
            success:function(data){ 
                                       
            } 
         });
```



```java
//看成单值
@RequestMapping(value = "save", method = {RequestMethod.POST }) 
     @ResponseBody  
     public void save(@RequestParam int id,String name) { //这里字段要跟前端一致，@RequsetParam 可以不加，默认调用的就是它
          
     }
//看成对象
@RequestMapping(value = "save", method = {RequestMethod.POST }}) 
     @ResponseBody  
     public void save(User user) { //前端字段跟对象属性一致
                                   //自动转化成改对象
     }
```

### json对象转成字符串后传值

```js
function addcart(productid,number,price){
	var cart={
				"userid":1,
				"productid":123,
				"number":213,
				"price":3
			}
	$.ajax({
		type : "POST",
		url : "${pageContext.request.contextPath}/useraddCart",
		 dataType:"json",   
		 contentType:"application/json",
		 data:JSON.stringify(cart),
		success : function(data){
		 console.log(data)
		}
		})
}
```

```java
	@RequestMapping(value="/useraddCart",method=RequestMethod.POST)
	@ResponseBody
	public HappyFarmCart useraddCart(@RequestBody HappyFarmCart happyFarmCart){
		
		System.out.println(happyFarmCart.toString());
	return happyFarmCart;
	}
```

> 将对象JSON.stringify后，以第一种方式传递，可实现对象中存对象 {'userList':users,'key1':value1}

```js
var user = {'id':id,'name':name};
var jsonStrGoods = JSON.stringify({'gid':gid,...});
$.ajax({ 
              type:"POST", 
              url:"user/saveUser", 
              dataType:"json",      
             // contentType:"application/json", //不添加这个头           
              data:{'user':JSON.stringify(user),'goods':jsonStrGoods }, //发送请求的数据在request payload
              success:function(data){ 
                                        
             } 
          });
```

```java
//看成单值
@RequestMapping(value = "save", method = {RequestMethod.POST }}) 
     @ResponseBody  
     public void save(@RequestParam String user,String goods) { //这里字段要跟前端一致，@RequsetParam 可以不加，默认调用的就是它
          User u= JSON.parseObject(user, User .class);//alibaba.fastjson转换成对象
     }
//看成对象
@RequestMapping(value = "save", method = {RequestMethod.POST }}) 
     @ResponseBody  
     public void save(UserAndGoods ug) { //没试过，猜测应该是这样，前端字段跟对象属性一致
                                   //自动转化成改对象
     }
```

> 传数组

```js
var arr = str.split(',');
    $.ajax({
        url:'/appollo-console/manage/user/names/validation',
        data:{
            names:arr
        },
		traditional: true,//必须
        type:'post',
		dataType:'json',
        success:function(res){
            alert(res);
        }
    })
```



```java
@PostMapping("/names/validation")
    @ResponseBody
    public List<String> validateUserName(String[] names){
        List<String> notValidNames = Lists.newArrayList();
        notValidNames = userService.findNotValidNames(Arrays.asList(names));
        return notValidNames;
    }
```



### 总结：

1.如果用JSON.stringify()将对象转成字符串，就需要在ajax请求中指定contentType 为 application/json,且后台需添加 @RequestBody注解；

2.如果直接传json对象则跟上面的相反，不能指定contentType为 application/json，其默认类型是 application/x-www-form-urlencoded 

## 五.axios请求方式

### get请求

```js
    testGet: function () {
      axios({
        method: 'get',
        url: '/test/greeting',
        params: {
          firstName: 'Fred',
          lastName: 'Flintstone'
        }
      }).then(function (response) {
        console.log(response);
      }).catch(function (error) {
        console.log(error);
      });
    },
```

### post请求

```js
testPost: function () {
                var params = new URLSearchParams();
                params.append('name', 'hello jdmc你好');
                params.append('id', '2');
                axios({
                  method: 'post',
                  url: '/test/greeting2',
                  data:params
        //          data: {id: '3', name: 'abc'}
                }).then(function (response) {
                  console.log(response);
                }).catch(function (error) {
                  console.log(error);
                })
              }
```

## 注意：

在使用post方式的时候传递参数有两种方式，一种是普通方式，一种是json方式，如果后台接受的是普通方式，那么使用上述方式即可。

普通的formed方式

```js
    var params = new URLSearchParams();
    params.append('name', 'hello jdmc你好');
    params.append('id', '2');
    data:params
```

后台接收参数：

```java
    public Student greeting2(int id,String name) 
```

json方式

```json
    data: {id: '3', name: 'abc'}
```

后台接收参数

```java
    public Object greeting2(@RequestBody Object data) 
```

## web请求静态资源上下文

${pageContext.request.contextPath}