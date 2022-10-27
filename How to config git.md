#How to configure git
http://hitsz-lab.gitee.io/os-labs-2021/tools/
## Build an SSH key
1. ssh 在终端中，输入以下指令以生成ssh密钥对:
    >ssh-keygen -t ed25519 -C "name_of_ssh_keypair"  

    密钥对的名字在教程中常用邮箱地址代替，这是一个某种意义上的传统。此外，为了兼容性考虑，不建议在密钥对名字中使用除了ASCII之外的任何字符。

2. >cat ~/.ssh/id_ed25519(Private key)
      cat ~/.ssh/id_ed25519.pub(Public key)
3. open github
    >setting -> SSH and GPG keys -> New SSH key
4. copy the public key on it
5. >ssh -T git@github.com (if neccessary input "yes")
## 简单创建一个本地repository，并和相应远程仓库对应
1. under a directory, open terminal
2. >git init
3. >git status
4. >git add .
5. >git commit -m "something"
6. >git remote add origin SSH...
7. >git push -u orgin master

## 分支操作

切换分支：

```bash
git checkout branchname
```

创建新分支并切换：

```bash
git checkout -b newBranch 
```

删除分支：

```bash
git branch -d name
```

例子：如何把远程分支的东西拉到新的本地分支上，并对上面的东西进行修改，即通过本地来改变远程

首先git checkout -b newbranch origin/name

记得git pull一下（我的理解是建立对应关系，clone也是类似的），之后正常add commit push就行，不会耽误你add和commit，所以在push之前pull就行

## git远程仓库相关的操作

### 基础操作

新增远程名称（一个本地仓库，可以关联多个远程仓库）

```bash
git remote add <remote-name> <repo-address>
```

删除已存在远程名称（只会移除本地仓库与远程仓库的管理，不会删除远程仓库的代码哈）

```bash
git remote rm <remote-name>
```

修改远程名称（其实就是改名，改远程仓库在你这边的本地的叫法）

```bash
git remote rename <old-remote-name> <new-remote-name>
```

更新远程名称关联的远程仓库

```bash
git remote set-url <remote-name> <repo-address>
```

查看远程连接有哪些

```bash
git remote -v
```

### upstream（一个特殊的 remote name）

```bash
# 1. 添加上游仓库的别名
$ git remote add upstream <upstream-repo-address>

# 2. 获取上游仓库的变更
$ git fetch upstream

# 3. 有需要的话，可以通过 merge 或 rebase 方式合并到本地分支中，比如：
$ git merge upstream/main
```

### fetch和merge

通常，拉取最新代码的过程是这样的：

1. 通过 `git fetch` 拉取代码的过程：先读取 `.git/config` 文件里面的配置 `[remote <remote-name>]`，将里面的所有（因为 `fetch` 并没有指定其中一个或多个远程仓库）远程名称对应仓库的分支下载到本地，并放在 `.git/refs/remotes/<remote-name>/` 目录下。
	比如 `git fetch origin main` 会创建或更新 `.git/refs/remotes/origin/main` 的文件，此时通过 `git branch -r` 就能看到一个 `origin/main` 的分支。但注意，我们使用的时候还是用 `origin/main` 而不是 `remotes/origin/main` 哦。

2. 有时候，我们可能会通过 `git diff` 命令来对比本地分支与远程分支的一些信息，才决定要不要合并。比如，`git diff main origin/main`。

3. 通过 `git merge` 或 `git rebase` 来进行分支合并。比如 `git merge origin/main`，表示将远程分支 `origin/main` 合并至本地分支 `main` 中。

## 删除git文件管理

```bash
find . -name ".git" | xargs rm -Rf
```

## git子模块

**不抽象的描述一下**

> 主要形式为一个repository下面套着另一个repository；
>
> 这样做的好处是，主项目和子项目之间相对独立一些，子项目也有一个repository来管理，这样的话，当别的项目需要子项目时，也能方便的拉取过去;

具体步骤如下：

```bash

```


​     
