10- git版本管理

推荐使用git进行代码的版本管理。文档管理。

网盘保存等。没有版本管理。每次提交可以进行回溯。

使用git与github进行交互

安装: yum install git

git 命令自动补全设置

![mark](http://myphoto.mtianyan.cn/blog/180129/LKHk8CL5LG.png?imageslim)

- git config : 基本配置
- git clone ： 远程github代码下载下来
- git fetch : 将远程的更新拉取到本地
- git rebase ：本地分支与另一个分支进行合并
- git init : 初始化
- git remote : 运程源的设置
- git commit : 版本
- git push : 本地到远程

```
sudo yum install git
git clone Ssh格式链接


```
没有权限因为git也是基于ssh协议的。
```
cd ~/.ssh
ssh-keygen
```

可以看到文件夹中生成了公钥和私钥。

将公钥复制到github后台的设置中。

```
git config --global user.name "mtianyan"
git config --global user.email "1147727180@qq.com"
cd .ssh
ssh-agent bash
ssh-add github
git clone git@github.com:mtianyan/flaskblog.git
```

git remote

```
git remote -v
git remote --help
```

查看已添加的远程仓库地址

```
git remote add mtianyan  https://github.com/mtianyan/hexoBlog-Github.git
git remote rm mtianyan
```

配置一个源，默认origin源。

```
git commit -am "mtianyan git"
```

am表示备注信息。`-am` a表示将变动文件添加。

```
git status
```

git push

```
git push origin master:master
git push -u origin master
```

将远程在github上的更改拉到本地

```
git fetch
git rebase origin/master
```

和origin源里面的master进行合并。

远程新建的仓库里面有readme文件。与本地的空文件产生了冲突。

```
git fetch
git rebase origin/master
```

将本地的与远程合并然后推送。

使用git将代码push到远端。然后在服务器上git clone下来。

```
git log
```

## 命令补全

git clone https://github.com/git/git.git

```
yum insatll unzip
sudo cp contrib/completion/git-completion.bash /etc/bash_completion.d/
source /etc/bash_completion.d/git-completion.bash
```

设置自动加载脚本 编辑 ~./bash_profile

```
if [ -f /etc/bash_completion.d/git-completion.bash ]; then
    source /etc/bash_completion.d/git-completion.bash
fi
```

如果这个文件是存在的。执行source命令将它加载进来。

