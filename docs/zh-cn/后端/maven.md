## Maven配置

#### 1.大约在158行   settings.xml 下载源

```xml
  <mirrors>
    <!-- 配置中央仓库的下载源  用的时候释放  -->
    <mirror>
      <id>1</id>
      <mirrorOf>*</mirrorOf>
      <name>Aliyun Maven Repository</name>
      <url>http://maven.aliyun.com/nexus/content/groups/public</url>
    </mirror>
  </mirrors>
```

#### 2. maven的本地仓库

大约在53行

```xml
<localRepository>D:/respository</localRepository>
```

