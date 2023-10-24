# 1创建版本库

> `mkdir`：创建文件夹
> 
> `cd`：进入文件夹
> 
> `git init`：初始化 Git 仓库



> 文件改动：存入[工作区]()
> 
> `git add`：存入[暂存区]()
> 
> `git commit -m ""`：提交[版本库]()



> `git status`：查看当前仓库状态
> 
> `git diff`：查看仓库改动

---

# 2版本回退

> `git log`：查看日志
> 
> > `git log --pretty=oneline`：简化版日志
> > 
> > `git log --graph`：分支合并图

```git
$ git log
commit 1094adb7b9b3807259d8cb349e7df1d4d6477073 (HEAD -> master)
# 1094adb... 叫做 commit id
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 21:06:15 2018 +0800

    append GPL 
```

+ Git 中，`HEAD`表示当前版本，`HEAD^`表示上一个版本，`HEAD^^`表示上上个版本。

> `git reset --hard HEAD^`：回退到上一版本
> 
> `git reset --hard 1094a`：回到未来某个版本



如果想恢复版本，但丢失该版本的`commit id`：

> `git reflog`：查看每一次命令

---

# 3管理修改

> `git diff HEAD -- File.txt`：查看工作区和版本库里面最新版本的区别

修改仅处于*工作区* 时：

> `git checkout -- File.txt`：丢弃*工作区*修改，让这个文件回到最近一次 add 或 commit 时的状态

修改`git add`到*暂存区* 了：

> `git reset HEAD File.txt`：丢弃*暂存区* 修改，退回工作区

修改`git commit`到*版本库* ：

> 则参考[版本回退]()一节

误删文件：

> `git checkout -- Flie.txt`

+ 其中`git checkout`本质时用版本库替换工作区版本

---

# 4远程仓库

### 生成 SSH Key

+ GitHub 需要`SSH Key 公钥`，来识别推送者身份。同时允许添加多个`key`，以便在多个设备上推送。

创建`SSH key`：

```git
$ ssh-keygen -t rsa -C "youremail@example.com"
```

`/.ssh/id_rsa`：私钥

`/.ssh/id_rsa.pub`：公钥

### 添加远程仓库

> 关联：`git remote add origin git@github.com:UserName/File.git`

- 远程库的名字默认就是`origin`

> 推送：`git push -u origin master`

+ 第一次推送时加上 `-u`

> 克隆：`git clone git@github.com:UserName/File.git`

### 删除远程库

> `git remote -v`：查看远程库信息

> `git remote rm origin`：删除远程库

+ 实质是解除了本地和远程的绑定关系，并不是物理上删除了远程库。

---

# 5分支管理

### 创建与合并

+ `master`和`dev`是[分支]()

+ `HEAD`则是指向分支的[指针]()

```ascii
                 master
                    │
                    ▼
┌───┐    ┌───┐    ┌───┐    ┌───┐
│   │───▶│   │───▶│   │───▶│   │
└───┘    └───┘    └───┘    └───┘
                             ▲
                             │
                            dev
                             ▲
                             │
                           HEAD
```

> 创建并切换至分支：`git checkout -b dev`
> 
> > 与以下命令等效
> > 
> > 创建：`git branch dev`
> > 
> > 切换：`git checkout dev`
> > 
> > + `git switch`来切换更科学

+ `-b`表示创建并切换

> 查看当前分支：`git branch`

> 合并到当前分支：`git merge dev` 

+ “Fast-forward” ，这次合并是“快速合并”

> 删除分支：`git branch -d dev`

### 解决冲突

+ `master`分支和`feature1`分支各自都分别有新的提交，无法“快速合并”

```ascii
                            HEAD
                              │
                              ▼
                           master
                              │
                              ▼
                            ┌───┐
                         ┌─▶│   │
┌───┐    ┌───┐    ┌───┐  │  └───┘
│   │───▶│   │───▶│   │──┤
└───┘    └───┘    └───┘  │  ┌───┐
                         └─▶│   │
                            └───┘
                              ▲
                              │
                          feature1
```

> `git log --graph`：分支合并图
