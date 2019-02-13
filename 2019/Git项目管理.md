## 认识Git

### 一句话理解Git(面试专用)

Git的每个分支的管理类似于链表，每次提交都会产生一个SHA1的唯一标识符。此唯一标识符是引用的指针。后续的增删改查操作都可以基于这个指针进行索引操作。

关键字：**分布式**，**四个分区**，**链表**，**SHA1指针**

### Git管理开发者最关心的几个问题

1. 版本管理策略模型是怎么样的?
2. 怎么理解git的四个区：工作区，暂存区和本地分支和远端分支？
3. 如何修改git的基本信息, 用户名，远端仓库地址？
4. git pull 和 git pull --rebase 有什么区别？
5. 如何删除及忽略git已经提交的资源？
6. git checkout 还有哪些高级用法？
7. git reset 和 git revert有哪些区别?

### 一张图讲解大厂版本管理策略

![一张图讲解大厂版本管理策略](https://raw.githubusercontent.com/wangdaodao/GitImg/master/img/20190213074521.png)

1. **master分支**：一般会已此分支为主分支（发布分支）。主分支的意思是说开发者不会在主分支上开发， 主分支只接受外分支的合并。合并完之后，验证通过，打tag上线。
2. **develop分支**：作为日常开发分支，同时会有多人在上面提交代码，为了保证提交不冲突，尽量保证模块拆解合理，开发没有同时修改同一文件的情况。
3. **feture_a 到 feture_n分支**：这些分支是发布之后的bug修复分支，不同开发者产生的Bug，会不同分支上修复bug，最终合并到master分支上上线。

## Git核心概念

### Git四个工作区

![Git四个工作区](https://raw.githubusercontent.com/wangdaodao/GitImg/master/img/20190213074641.png)

1. **Workspace 工作区**：就是你平时存放项目代码的地方。
2. **Index / Stage 暂存区**：用于临时存放你的改动，事实上它只是一个文件，保存即将提交到文件列表信息。
3. **Repository仓库区（或版本库）**： 就是安全存放数据的位置，这里面有你提交到所有版本的数据。其中HEAD指向最新放入仓库的版本。
4. **Remote远程仓库**： 托管代码的服务器，可以简单的认为是你项目组中的一台电脑用于远程数据交换。

### Git内部构造

要理解Git内部构造的核心，我们应理解三个东西： **实体**、**引用**、 **索引**。

![Git内部构造](https://raw.githubusercontent.com/wangdaodao/GitImg/master/img/20190213074817.png)

1. **实体**：你提交到一个Git代码仓库中的所有文件，包括每个提交的说明信息（the commit info）都在目录 .git/objects/中存储为实体。一个实体以一个40字符长度的字符串（该实体内容的SHA1哈希值）来标识。
2. **引用**：Git中，一个分支（branch）、远程分支（remote branch）或一个标签（tag）（也称为轻量标签）仅是指向一个实体的一个指针，这里的实体通常是一个commit实体。这些引用以文本文件的形式存储在目录.git/refs/中。
3. **索引**：索引是一个暂存区，以二进制文件的形式存储为文件.git/index。当git add一个文件，Git将该文件的信息添加到索引中。当git commit，Git仅提交索引文件中列出的文件。

## Git初始化

### 仓库基本操作

```
git init                      //在当前目录新建一个Git代码库
git init <project-name>       // 新建一个目录，将其初始化为Git代码库
git clone <git-hub-url>       // clone git仓库
git clone <url> -b <branch>   // [高阶用法] clone git仓库并且制定分支
```

### Git授权SSH

大多数 Git 服务器都会选择使用 SSH 公钥来进行授权。例如在github或者gitlab上提交代码，我们需要把SSH公钥复制托管到Github的`personal setting -> ssh keys`

```
cd ~/.ssh             //进入ssh目录
ssh-keygen            // 生成ssh公私钥
cat ~/.ssh/id_rsa.pub // 复制ssh公钥
```

### Git忽略不应该跟踪的文件

`.gitignore`文件显式地指定了哪些文件不应被Git追踪，即被Git忽略掉。例如开发过程中 `node_module`，`.vscode`等文件不需要被跟踪和提交，可以在初始化的忽略它们。·

```
// .gitignore 文件
node_module
.vscode
```

## Git基本操作

### 如何操作一次完整提交流程

当一个文件修改，我们想把他提交到github上面， 一次完整提交路径是从`工作区 --> 缓存区 --> 本地仓库区 --> 远端仓库`

```
vi readme.md              // 修改readme文件，文件在工作区
git add readme.md         // 文件进入缓存区，缓存区的文件可以被checkout移除到工作区
git commit 'add readme'   // 文件进入提交分支，但还是在本地
git push origin master    // 提交分支 push 到远端分支
```

### 如何操作一次完整更新流程

当多人合作开发过程，开发者需要更新别人的提交代码，我们就需要更新本地代码。

```
git pull : git fetch + git merge
git pull --rebase: git fetch + git rebase
```

![如何操作一次完整更新流程](https://raw.githubusercontent.com/wangdaodao/GitImg/master/img/20190213075448.png)

## Git配置

### 修改用户信息

```
git config --list                                   // 配置信息列表
git config --global user.name "John Doe"            // 设置用户名
git config --global user.email johndoe@example.com  // 设置邮箱
```

### 设置不同的仓库源

```
git remote --help               // 查看帮助
git remote                      // 查看不同源
git remote add [name] [url]     // 添加不同地址的源，并取一个别名
git remote remove [name]        // 删除一个源
```

## 分支管理

注意：本地分支删除了，并不代表远端分支删除。如何定期清理远端分支。

```
git checkout -b daily/0.0.1       // 已当前分支为基础，创建daily/0.0.1分支

git branch -la                    // 查看本地分支及远端分支
git branch -D [branchName]        // 强制删除本地分支
git branch -d [branchName]        // 删除已经Merge过的分支
git checkout -b daily/0.0.1       // 创建一个分支

// 如何删除远端多余分支
git push -delete <remote_name> <branchName> // 大多数情况remote_name为origin
```

## Git提交信息检查

```
git diff                              // 查看当前工作区改动点
git diff commit_hash1 commit_hash2    // 提交hash1和hash2的差异
git diff branch_a branch_b            // 分支a和b的差异
git status                            // 当前改动文件
git log                               // 查看提交历史
git log --pretty=oneline              // 提交历史缩减一行查看，主要是提交Hash值
```

## Git高阶操作

### 如何使用`merge`, `cherry-pick`和`patch`

多人协同开发中我们需要合并别人的代码，使本地代码保持最新状态，git提供了三种合并的方式。对于初学者很少有人知道这些差别。下图就列举了三种操作的使用场景。

![黑科技](https://raw.githubusercontent.com/wangdaodao/GitImg/master/img/20190213075744.png)


### 如何删除git缓存文件

情况：有些情况开发者把原有不需要提交的代码提交到了远端仓库，再使用`.gitignore`忽略文件不生效。哪怕我们删除后再提交也没有办法忽略。这种情况下我们应该怎么解决？

方法：我们可以使用`git rm --cache` 删除原来git跟踪的文件缓存，再在`.gitignore`里面添加忽略文件

```
## 当我们需要删除暂存区或分支上的文件, 同时工作区也不需要这个文件了, 可以使用
git rm file_path 

## 当我们需要删除暂存区或分支上的文件, 但本地又需要使用, 只是不希望这个文件被版本控制, 可以使用
git rm --cached file_path       // PS: file_path 为文件路径
```

### 如何强制提交

情况：对于多人同时开发，有些时候我们会遇到版本管理混乱的情况，远端版本错误了，但本地版本是正确的。 如何才能让强制更新远端版本，保持和本地工作区环境一样？

方法：强制push本地正确的版本，但是慎用。因为它是不可逆转的。

```
git push origin master --force // 强制更新，慎用
```

### 如何使版本信息回退到某次提交？revert 和 reset有什么区别？

情况：有些时候开发者需要退回到某次正确的提交记录，有些时候开发者的commit错误了，这时候可以使用 `git revert` 和 `git reset`。 

- `git revert`： 撤销某次操作，此次操作之前的commit都会被保留。
- `git reset`： 撤销某次提交，但是此次之后的修改都会被退回到暂存区。

```
## 强制回退到某次提交，且需要强制提交
git reset ——hard commit_hash 
git push origin master --force 

## 回退到某提提交，保存提交commit记录, 重新commit
git revert commit_hash
git add .
git commit -m "revert"
git push origin master
```





原文链接：[https://www.yuque.com/docs/share/cc5f2761-c72b-420e-bd26-448d645691ef#d41d8cd9](https://www.yuque.com/docs/share/cc5f2761-c72b-420e-bd26-448d645691ef#d41d8cd9)

