###  git命令

```shell
- git init ：初始化仓库
- git rm --cached README.md  ：从暂存区删除
- git status : 显示工作目录和暂存区的状态
- git add -A  ：提交所有
- git add README.md  ：添加README这个文件，如果add .则代表提交全部文件
- git commit -m "first commit"  ：提交到本地仓库，并输入文字说明‘first commit’
- git remote add origin [http://XXX.git](https://links.jianshu.com/go?to=http%3A%2F%2FXXX.git) :把仓库加入到git下面
- git push -u origin master : 提交到远程仓库
```



###  分支管理

```shell
- git branch [-avv] ： 查看当前分支
- git branch <branch name> ：基于当前分支新建分支
- git branch <branch name> <commit id> ：基于提交新建分支
- git branch -d {dev}  ：删除分支
- git checkout <branch name>  ：切换分支
- git merge <merge target>  ：合并分支
```

