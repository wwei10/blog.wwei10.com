---
title: "搭建博客 - 自动化部署"
date: 2020-08-25 17:00:00
discourse: 21
slug: automate-deployment-using-git-hooks
---

这两天每次修改下网站，就要手动做一系列事情。让人有点受不了，于是开始上网搜索有哪些自动化的方法，最后决定使用git hooks方便部署网站。

<!--more-->

## 繁琐的部署流程

从前自己本地有个改动后就要手动输入如下操作：

```
(my macbook book) $ git add .
(my macbook book) $ git ci -m "..."
(my macbook book) $ git push
(digital ocean server) $ git pull
(digital ocean server) $ go install
(digital ocean server) $ sudo systemctl stop blog
(digital ocean server) $ sudo systemctl start blog
```

大概做的事情就是把本地的改动push到github上面，然后去digital ocean的服务器上面pull最新的版本，用go install把binary准备好，放到$GOPATH/bin里面。最后用systemd重启blog这个服务，让网站得以更新

关于systemd，我是创立了blog这个[service](https://gist.github.com/wwei10/c26fee3a62d74648aa770a98c96dc859)，然后用systemd来管理这个服务。更多关于systemd的介绍看[这里](http://www.ruanyifeng.com/blog/2016/03/systemd-tutorial-commands.html)。

每次部署都这么多事儿，实在受不了。于是上网做了些研究，感觉docker是一种方法，另外一种方法是用git hooks。git hooks看着更简单也有教程于是就选择使用git hooks。

## Git Hooks

在Digital Ocean中用Git Hooks有不少资源，[这个](https://www.digitalocean.com/community/tutorials/how-to-use-git-hooks-to-automate-development-and-deployment-tasks)写的比较好。最基本的想法就是在Digital Ocean服务器端设置好git repo，在自己macbook上面设置好remote server，然后就可以把自己的改动git push到remote server。然后设置好post-receive hook，在收到push之后，自动运行代码实现部署。Git hooks挺难Debug的，过程中踩了些坑，于是记录下。


### Digital Ocean上面的改动

在云端，创立一个裸库（bare git repo），正常的库是有工作目录（working directory）的，可以直接看到代码，裸库没有工作目录，一般是多人协作时存代码用的。

```
mkdir ~/wwei10.com
cd ~/wwei10.com
git init --bare
```

创建push hooks，放在`~/wwei10.com/hooks/post-receive`，这样每当push发生后就会执行一段代码。千万别忘了让这个脚本可执行。

```
chmod +x hooks/post-receive
```


具体脚本代码如下：

```
#! /bin/bash
while read oldrev newrev ref
do
    if [[ $ref =~ .*/master$ ]];
    then
        echo "Master ref recieved. Deploying master branch to production ..."
        git --work-tree=/root/go/src/github.com/wwei10/wwei10.com --git-dir=/root/wwei10.com checkout -f
        cd /root/go/src/github.com/wwei10/wwei10.com
        if [ -f /usr/local/go/bin/go  ]
        then
            /usr/local/go/bin/go install && sudo systemctl stop blog && sudo systemctl start blog
        fi
        cd -
    else
        echo "Ref $ref successfully received."
    fi
done
```

`if [[ $ref =~ .*/master$ ]];`判断是不是push master。如果是的话就部署。

`--work-tree=/root/go/src/github.com/wwei10/wwei10.com`指明把代码checkout到哪里。 `--git-dir=/root/wwei10.com`说明了git repo的位置。两个加起来就是说把`wwei10.com`这个git bare repo里的代码放到`/root/go/src/github.com/wwei10/wwei10.com`文件夹。


之前我写的`go install`，服务器报错，说找不到go。用`which go`轻松找到路径，然后替换成`/usr/local/go/bin/go install`就修好了。


### Macbook上面的改动

在macbook上面改动很少，主要就是这一句：

```
git remote add production root@xxx.xxx.xxx.xxx:wwei10.com
```

最后部署的时候只要4个命令就可以了。


```
(my macbook) $ git add .
(my macbook) $ git ci -m "..."
(my macbook) $ git push
(my macbook) $ git push production master
```

从7个命令减到4个命令就好了，而且还不如macbook和digital ocean来回切换。给git hooks点个赞。