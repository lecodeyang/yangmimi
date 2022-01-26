# Linux练习

> sudo apt-get update    下载要更新软件包

###  systemctl start docker

#### 使用xshell连接

在ubuntu安装ssh服务

```shell
sudo apt-get  install  openssh-server
```

查看服务是否启动

```shell
ps -e | grep ssh 
```

启动服务

```shell
/etc/init.d/ssh start
```

在ubuntu上使用yum

```
sudo apt-get install yum
sudo apt-get install build-essential
cd /etc/yum/repos.d/
sudo wget http://mirrors.163.com/.help/fedora-163.repo
sudo wget http://mirrors.163.com/.help/fedora-updates-163.repo
find ./ -type f -name "*.repo" | xargs sudo sed -i 's#$releasever#17#g'
sudo yum makecache
```

# Centos

查看SSH是否安装。

　　输入命令：rpm -qa | grep ssh

　　注：若没安装SSH则可输入：yum install openssh-server安装。

启动SSH服务。

　　输入命令：systemctl restart sshd 重启SSH服务。

　　命令：systemctl start sshd 启动服务 | 命令：systemctl stop sshd 停止服务 

　　重启后可输入：netstat -antp | grep sshd 查看是否启动22端口（可略）。

输入命令：systemctl enable sshd 即可。

　　注：若是systemctl disable sshd 则禁止SSH开机启动。



### docker安装redis

#### 查找镜像

```shell
docker search redis
```

#### 取最新版的 Redis 镜像

这里我们拉取官方的最新版本的镜像：

```
$ docker pull redis:latest
```

查看镜像

```
 docker images
```

运行容器

```shell
docker run -itd --name redis-test -p 6379:6379 redis
```

参数说明：

- **-p 6379:6379**：映射容器服务的 6379 端口到宿主机的 6379 端口。外部可以直接通过宿主机ip:6379 访问到 Redis 的服务。

安装成功

最后我们可以通过 **docker ps** 命令查看容器的运行信息：

接着我们通过 redis-cli 连接测试使用 redis 服务。交互式命令

```shell
docker exec -it redis-test /bin/bash
```



### Centos7查看与关闭防火墙

```shell
[root@localhost /]# firewall-cmd --state
running
[root@localhost /]# systemctl stop firewalld.service
[root@localhost /]# systemctl disable firewalld.service
[root@localhost /]# firewall-cmd --state
not running
```

### 安装Nginx

更上面同理,查看nginx的版本

```shell
docker search nginx
```

拉取最新版本的镜像

```shell
docker pull nginx:latest
```

运行容器

```shell
docker run --name nginx-test -p 8080:80 -d nginx
```

参数说明：

- **--name nginx-test**：容器名称。
- **-p 8080:80**： 端口进行映射，将本地 8080 端口映射到容器内部的 80 端口。
- **-d nginx**： 设置容器在在后台一直运行。

查看容器相关信息

```shell
docker inspect nginx
```

#### 自行配置nginx

两种操作，一个挂在到本地文件，一个进入nginx，交互

```shell
docker exec -it 容器id /bin/bash
```

创建目录
创建一个目录用来存放文件,方便我们进行修改

> mkdir  -p  /everything/nginx/conf /everything/nginx/html

创建配置文件
在我们创建的目录下创建一个配置文件

> touch  /everything/nginx/conf/nginx.conf

修改配置文件
把我们创建的目录下的 nginx.conf 修改为以下内容

```shell
#工作进程数  1 ,不要超过计算机的核数,四核配置4,八核配置8
worker_processes  1;

#工作连接数,也就是线程,一个进程有1024个线程,
events {
    worker_connections  1024;
}

#http请求配置
http {
    default_type  application/octet-stream;

    #sendfile为发送文件,要on开启
    sendfile        on;
    
    #keepalive_timeout超时时间
    keepalive_timeout  65;
    
    server {
        #监听的端口,这里为80
        listen   80;
        #server_name就是域名,
        server_name  localhost;
        
        #location域名代理地址
        # / 代表所有请求路径
        location / {
            root /usr/share/nginx/html;
            index  index.html;
        }
    }

}
```


创建html
创建在 /everything/nginx/html目录下创建index.html

> touch /everything/nginx/html/index.html

index.html内容为

``` 
<html>
<body>
<meta charset="utf-8">
<h1>我是index.html...</h1>
</body>
</html>
```

启动容器

>docker run --name nginx01 -p 3304:80 -v /everything/nginx/conf/nginx.conf:/etc/nginx/nginx.conf -v /everything/nginx/html:/usr/share/nginx/html --restart always -d nginx

访问

Nginx的案例

###  反向代理+负载均衡

准备工作

1. 在tomcat中拉取tomcat镜像

   ```shell
   docker pull tomcat   #自动拉取最新版
   ```

2. 显示的tomcat内容要不一样 ,便于区分

   启动tomcat 1 直接使用命令启动

   ```shell
   docker run -d -p 3355:8080 --name tomcat01 tomcat  #3355映射tomcat01
   
   docker exec -it tomcat01 /bin/bash  # 进入Tomcat的交互页面
   
   root@d3c05bb2bf7f:/usr/local/tomcat# ls   #查看文件
   BUILDING.txt	 NOTICE		RUNNING.txt  lib	     temp	   work
   CONTRIBUTING.md  README.md	bin	     logs	     webapps
   LICENSE		 RELEASE-NOTES	conf	     native-jni-lib  webapps.dist
   
   一开始访问为404 将webapps.dist下的东西拷贝到webapps
   
   cp -r webapps.dist/* webapps
   
   exit #退出交互页面
   
   curl localhost:3355 #测试是否启动成功
   
   ```

3. 第二中区别就在 我们将webapps下的文件挂载到 自己本地linux自创的文件夹

   ```shell
   docker run -d -p 3356:8080 -v /tomcat/webapps/:/usr/local/tomcat/webapps/  --name   tomcat02 tomcat
   ```

   在webapps文件创建一个ROOT文件 里面放入 index.jsp文件，显示不一样的内容。

   测试 curl  localhost:3356

   ```shell
   [root@localhost /]# curl localhost:3356
   <html>
   
   <head>
   <meta charset="utf-8">
   <title>菜鸟教程(runoob.com)</title>
   </head>
   <body>
   <h2>我是tomcat02，我又被访问到了</h2>
   </body>
   ```

   **注意 ** 这里为什么不在交互的时候直接在tomcat中改index.jsp  以为在容器中没有安装vim 等一些包，所以无法改变

   ```shell
   root@b61c6c5ccfce:/usr/local/tomcat/webapps/ROOT# vim index.jsp
   bash: vim: command not found
   root@b61c6c5ccfce:/usr/local/tomcat/webapps/ROOT# vi
   bash: vi: command not found
   ```

配置nginx.conf文件

```shell
#工作连接数,也就是线程,一个进程有1024个线程,
events {
    worker_connections  1024;
}
 


#http请求配置
http {
		
		#注意在docker中，使用本机电脑访问而不是在虚拟机中访问的时候 使用的是虚拟机的ip地址，在虚拟机中使用的lcoalhost
		# 这里一定要注意访问路径，不然访问不到  weight 权衡 数值越大等级越高，这个地方就是负载均衡的体现
	 upstream  aaa{
       		server    192.168.159.130:3355 weight=1;
       		server    192.168.159.130:3356 weight=3;
   	}
	

    default_type  application/octet-stream; 
    #sendfile为发送文件,要on开启
    sendfile    on;
    
    #keepalive_timeout超时时间
    keepalive_timeout  65;
    
    server {
        #监听的端口,这里为80
        listen       80;
        #server_name就是域名,
        server_name  localhost;
        
        #location域名代理地址
        # / 代表所有请求路径
   location / {
   	# 请求转发，这个地方就是反向代理的体现
	if (!-e $request_filename){
                 proxy_pass http://aaa;
             } 
        }  


	location /www/ { 
		root  /data/;
		index  index.html index.htm;
      }	

	location /image/ {
		root /data/;
		autoindex on;
		autoindex_exact_size off;
		autoindex_localtime on;
	 	}
	 
	error_page   500 502 503 504  /50x.html;
       location = /50x.html {
            root   html;
        }

	}
}

```

配置完服务器之后要重启，重启加载配置文件。

### 动静分离案例



```shell
#工作连接数,也就是线程,一个进程有1024个线程,
events {
    worker_connections  1024;
}
 
#http请求配置
http {
    default_type  application/octet-stream;
    
    #sendfile为发送文件,要on开启
    sendfile    on;
    
    #keepalive_timeout超时时间
    keepalive_timeout  65;
    
    server {
        #监听的端口,这里为80
        listen       80;
        #server_name就是域名,
        server_name  localhost;
        
        #location域名代理地址
        # / 代表所有请求路径
   location / {
            root /usr/share/nginx/html;
            index  index.html;
        }  


	location /www/ { 
		root  /data/;
		index  index.html index.htm;
      }	

	location /image/ {
		root /data/;
		autoindex on;
		autoindex_exact_size off;
		autoindex_localtime on;
	 	}
	 
	error_page   500 502 503 504  /50x.html;
       location = /50x.html {
            root   html;
        }

	}
}

```

配置完文件之后挂载配置文件，重启服务

```shell
[root@localhost conf]# docker run --name nginx01 -p 3044:80 -v /everything/nginx/conf/nginx.conf:/etc/nginx/nginx.conf -v /everything/nginx/html:/usr/share/nginx/html -v  /data/www/:/data/www/ -v  /data/image/:/data/image/ --restart always -d nginx
0fd218c39317972ac1a155153a74c9daff04c222cc3d01a2724b16a21e70bea7
[root@localhost conf]# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS         PORTS                                   NAMES
0fd218c39317   nginx     "/docker-entrypoint.…"   10 seconds ago   Up 8 seconds   0.0.0.0:3044->80/tcp, :::3044->80/tcp   nginx01
[root@localhost conf]# 


```

##  docker安装数据库

>  docker run mysql:5.7        先去查看是否有 没有的话就执行pull 操作拉取镜像

