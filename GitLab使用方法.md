# HelloWorld

## 目录

[TOC]

## 教研室GitLab使用方法

### 1. 连接到SSH的免密登录

#### ⑴ 生成RSA密钥（若已生成过则跳过）

命令行／终端运行

```shell
ssh-keygen -t rsa -b 4096 -C "comment"
```

*注：*`comment`参数用于用户区分密钥，推荐填入以下几种值

1. 用户邮箱：`youremail@example.com`
2. 用户名和主机名：`username@hostname`
3. 或者随便什么名字

连续按回车，直到生成私钥`id_rsa`和公钥`id_rsa.pub`。

#### ⑵ 提交公钥

若在145服务器上有个人帐户，则按照SSH免密登录操作。

否则，联系服务器管理员，提交公钥（`id_rsa.pub`）。管理员将该公钥加入`git`帐户的`authorized_keys`中。

### 2. 开启SSH隧道

##### 原理

运行

```shell
ssh -fN -L 80:localhost:32223 -p 48024 git@dp.neurotic.win
ssh -fN -L 14522:localhost:32224 -p 48024 git@dp.neurotic.win
```

其中：

- `80`是本机访问HTTP的端口（可以自己指定，推荐HTTP默认端口`80`）
- `14522`是本机访问SSH的端口（可以自己指定，后文指定为`32224`）
- `32223`是145服务器访问Gitlab的网页的端口
- `32224`是145服务器访问Gitlab的SSH的端口
- `48024`是访问145服务器SSH服务的端口
- `<username>`填自己在**145服务器**上的用户名（如果没有个人帐户而是在`git`帐户添加过公钥则是`git`）<br><font color='red'>**注意：这个地方不是GitLab的用户名而是145服务器用户名！**</font>如果没有提交公钥则联系管理员提交公钥，见[1. ⑵ 提交公钥](#⑵ 提交公钥)
- `dp.neurotic.win`是145服务器的域名

需要使用时运行以上命令打开代理。当**重启**、**改变网络**和**连接中断**后，需要重新运行以建立隧道。

##### Windows上的自动化脚本参考

```bat
start /B ssh -fN -L 80:localhost:32223 -p 48024 git@dp.neurotic.win
start /B ssh -fN -L 32224:localhost:32224 -p 48024 git@dp.neurotic.win
```

保存为`.bat`文件

##### Linux上的自动化脚本参考

```bash
ssh -fN -L 80:localhost:32223 -p 48024 git@dp.neurotic.win
ssh -fN -L 32224:localhost:32224 -p 48024 git@dp.neurotic.win
```

保存为`.sh`文件

##### Mac上的自动化脚本参考

据称，Mac系统创建代理必须要root权限，因此需要使用`sudo`，继而需要手动指定私钥位置。

```sh
sudo ssh -fN -i /Users/<user>/.ssh/id_rsa -L 80:localhost:32223 -p 48024 git@dp.neurotic.win
sudo ssh -fN -i /Users/<user>/.ssh/id_rsa -L 32224:localhost:32224 -p 48024 git@dp.neurotic.win
```

保存为`.sh`文件

### 3. 额外说明

#### ⑴ 关于145服务器对于Gitlab SSH的转发

145服务器的`32224`端口已被重定向到Gitlab的SSH端口，因此访问`dp.neurotic.win:32224`等价于Gitlab的SSH。

### 4. 克隆仓库

访问 [http://localhost/](http://localhost/) 即可打开Gitlab网站。

首次使用需要申请帐号，邮箱选择自己Git commit时所用邮箱即可。帐号申请后找管理员通过。

在`用户设置`→`SSH密钥`页面添加自己的SSH密钥，使用类型选择`验证 & 签名`。缺少该设置GitLab的SSH会拒绝访问。

以开启上文所述代理后克隆`HelloWorld`工程为例：

网站地址为[http://localhost/public_test/HelloWorld](http://localhost/public_test/HelloWorld)；

克隆地址为[ssh://git@localhost:32224/public_test/HelloWorld.git]()（正向代理）／[ssh://git@dp.neurotic.win:32224/public_test/HelloWorld.git]()（145端口转发）

### 5. 一些高级玩法

#### ⑴ 设置仅可用于本机的Gitlab自定义域名

##### ① 设置Hosts

###### Windows

在hosts文件（`C:\Windows\System32\drivers\etc\hosts`）内加入

```properties
127.0.0.1 gitlab.neurotic.win
```

后即可用`http://gitlab.neurotic.win:<port>`访问Gitlab（虽然这个域名在广域网上不存在）。

##### ② 梯子的额外处理

###### Clash

自定义域名`gitlab.neurotic.win`可能会被代理脚本查询IP地址，但结果必定为无，进而导致访问失败。因此需要在`系统代理`-`绕过的域名/IP网段`里加入域名`gitlab.neurotic.win`（已知前置通配符无效），即可避免访问失败。

## Git Repo

### Getting started

To make it easy for you to get started with GitLab, here's a list of recommended next steps.

Already a pro? Just edit this README.md and make it your own. Want to make it easy? [Use the template at the bottom](#editing-this-readme)!

### Add your files

- [ ] [Create](https://docs.gitlab.com/ee/user/project/repository/web_editor.html#create-a-file) or [upload](https://docs.gitlab.com/ee/user/project/repository/web_editor.html#upload-a-file) files
- [ ] [Add files using the command line](https://docs.gitlab.com/ee/gitlab-basics/add-file.html#add-a-file-using-the-command-line) or push an existing Git repository with the following command:

```
cd existing_repo
git remote add origin http://localhost/public_test/HelloWorld.git
git branch -M master
git push -uf origin master
```

### Integrate with your tools

- [ ] [Set up project integrations](http://localhost/public_test/HelloWorld/-/settings/integrations)

### Collaborate with your team

- [ ] [Invite team members and collaborators](https://docs.gitlab.com/ee/user/project/members/)
- [ ] [Create a new merge request](https://docs.gitlab.com/ee/user/project/merge_requests/creating_merge_requests.html)
- [ ] [Automatically close issues from merge requests](https://docs.gitlab.com/ee/user/project/issues/managing_issues.html#closing-issues-automatically)
- [ ] [Enable merge request approvals](https://docs.gitlab.com/ee/user/project/merge_requests/approvals/)
- [ ] [Automatically merge when pipeline succeeds](https://docs.gitlab.com/ee/user/project/merge_requests/merge_when_pipeline_succeeds.html)

### Test and Deploy

Use the built-in continuous integration in GitLab.

- [ ] [Get started with GitLab CI/CD](https://docs.gitlab.com/ee/ci/quick_start/index.html)
- [ ] [Analyze your code for known vulnerabilities with Static Application Security Testing(SAST)](https://docs.gitlab.com/ee/user/application_security/sast/)
- [ ] [Deploy to Kubernetes, Amazon EC2, or Amazon ECS using Auto Deploy](https://docs.gitlab.com/ee/topics/autodevops/requirements.html)
- [ ] [Use pull-based deployments for improved Kubernetes management](https://docs.gitlab.com/ee/user/clusters/agent/)
- [ ] [Set up protected environments](https://docs.gitlab.com/ee/ci/environments/protected_environments.html)

***

## Editing this README

When you're ready to make this README your own, just edit this file and use the handy template below (or feel free to structure it however you want - this is just a starting point!). Thank you to [makeareadme.com](https://www.makeareadme.com/) for this template.

### Suggestions for a good README
Every project is different, so consider which of these sections apply to yours. The sections used in the template are suggestions for most open source projects. Also keep in mind that while a README can be too long and detailed, too long is better than too short. If you think your README is too long, consider utilizing another form of documentation rather than cutting out information.

### Name
Choose a self-explaining name for your project.

### Description
Let people know what your project can do specifically. Provide context and add a link to any reference visitors might be unfamiliar with. A list of Features or a Background subsection can also be added here. If there are alternatives to your project, this is a good place to list differentiating factors.

### Badges
On some READMEs, you may see small images that convey metadata, such as whether or not all the tests are passing for the project. You can use Shields to add some to your README. Many services also have instructions for adding a badge.

### Visuals
Depending on what you are making, it can be a good idea to include screenshots or even a video (you'll frequently see GIFs rather than actual videos). Tools like ttygif can help, but check out Asciinema for a more sophisticated method.

### Installation
Within a particular ecosystem, there may be a common way of installing things, such as using Yarn, NuGet, or Homebrew. However, consider the possibility that whoever is reading your README is a novice and would like more guidance. Listing specific steps helps remove ambiguity and gets people to using your project as quickly as possible. If it only runs in a specific context like a particular programming language version or operating system or has dependencies that have to be installed manually, also add a Requirements subsection.

### Usage
Use examples liberally, and show the expected output if you can. It's helpful to have inline the smallest example of usage that you can demonstrate, while providing links to more sophisticated examples if they are too long to reasonably include in the README.

### Support
Tell people where they can go to for help. It can be any combination of an issue tracker, a chat room, an email address, etc.

### Roadmap
If you have ideas for releases in the future, it is a good idea to list them in the README.

### Contributing
State if you are open to contributions and what your requirements are for accepting them.

For people who want to make changes to your project, it's helpful to have some documentation on how to get started. Perhaps there is a script that they should run or some environment variables that they need to set. Make these steps explicit. These instructions could also be useful to your future self.

You can also document commands to lint the code or run tests. These steps help to ensure high code quality and reduce the likelihood that the changes inadvertently break something. Having instructions for running tests is especially helpful if it requires external setup, such as starting a Selenium server for testing in a browser.

### Authors and acknowledgment
Show your appreciation to those who have contributed to the project.

### License
For open source projects, say how it is licensed.

### Project status
If you have run out of energy or time for your project, put a note at the top of the README saying that development has slowed down or stopped completely. Someone may choose to fork your project or volunteer to step in as a maintainer or owner, allowing your project to keep going. You can also make an explicit request for maintainers.
