# 配置SSH并在VS Code中使用Remote SSH

<center>更新日期：2023年12月21日</center>

## 〇、绪论

#### 关于SSH工具

SSH工具可以在客户端和服务端之间建立加密连接，实现包括远程登录（命令行）、文件传递等功能。SSH的身份验证方式包括口令、密钥（即免密登录）等方法。

SSH工具既可以直接通过终端登录服务端计算机、在服务端和客户端间传递文件，也可以通过第三方软件调用实现其他远程连接的功能（例如下文使用VS Code的Remote SSH插件实现远程编辑，以及WinSCP实现图形化界面文件传递）。

注：一般将发起连接的终端称为“客户端”，被连接的终端称为“服务端”。

#### 关于本教程

第一章介绍服务器端软件的安装和配置方法（包括Linux、Windows）；第二章介绍服务器端的配置方法（包括终端登录、配置免密、配置VS Code Remote SSH）。

## 一、服务器端设置

*只配置客户端可以直接跳过看[下一章](#二、客户端设置)。*

### Ⓐ Linux服务器端设置

以下说明以Ubuntu为例。

#### ⑴ 安装OpenSSH服务器

运行

```shell
sudo apt-get install openssh-server
```

安装OpenSSH服务器

#### ⑵ 启动服务

启动sshd服务（一次性）

```shell
sudo service ssh start
```

设置开机启动sshd服务^[9]^

```shell
sudo systemctl enable sshd.service
# 或
sudo systemctl enable --now sshd.service  # 立刻启动
```

查看sshd状态

```shell
sudo service ssh status
# 或
sudo systemctl status sshd.service
```

#### ⑶ 配置SSH^[8]^

打开`/etc/ssh/sshd_config`

```properties sshd_config
# 可能需要新加一行（SSH2后被弃用，参考[10]）
RSAAuthentication    yes
# 确保以下2条配置没有被注释
PubkeyAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys
# 若要禁止口令登录，则修改如下
PasswordAuthentication no
```

重启SSH服务器服务

```shell
sudo systemctl restart sshd
# 或
sudo service ssh restart
```

### Ⓑ Windows服务器端设置(Windows 7 及以前)

参考GitHub上的[Win32-OpenSSH](https://github.com/powershell/Win32-OpenSSH)项目和参考文档[11]。

主要内容：

1. [下载](https://github.com/PowerShell/Win32-OpenSSH/releases)并解压软件包

2. 将软件包路径加入环境变量

3. PowerShell安装sshd服务
   运行
   ```bat
   powershell.exe -ExecutionPolicy Bypass -File install-sshd.ps1
   ```

4. 设置防火墙
   运行
   ```cmd
   netsh advfirewall firewall add rule name=sshd dir=in action=allow protocol=TCP localport=22
   ```

5. 配置sshd服务
   运行
   ```bat
   ::开机自启动
   sc config sshd start=auto
   ::开启服务
   net start sshd
   ```

*注意：该项目的`ssh`工具代码页为65001，与旧版Windows有诸多兼容问题，故不推荐使用，而是使用Git for Windows的`ssh`。*

### Ⓒ Windows服务器端设置(Windows 10)

#### ⑴ 安装OpenSSH服务器^[5]^

在"设置"-"管理可选功能"中打开`OpenSSH 服务器`。

或者（未测试过）打开PowerShell，键入

```powershell
Get-WindowsCapability -Online | ? Name -like 'OpenSSH*'
```

输出应有

```powershell
Name  : OpenSSH.Client~~~~0.0.1.0
State : NotPresent
Name  : OpenSSH.Server~~~~0.0.1.0
State : NotPresent
```

则键入如下代码安装：

```powershell
Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0
```

#### ⑵ 启动服务

服务里启动`OpenSSH SSH Server`服务（`sshd`）并设置为自动启动。

或在PowerShell里运行

```powershell
Start-Service sshd
Set-Service -Name sshd -StartupType 'Automatic'
```

#### ⑶ 检查防火墙

PowerShell运行

```powershell
Get-NetFirewallRule -Name OpenSSH-Server-In-TCP
```

检查`OpenSSH-Server-In-TCP`的返回值是否正确。

若没有这一项，则运行

```powershell
New-NetFirewallRule -Name sshd -DisplayName 'OpenSSH Server (sshd)' -Enabled True -Direction Inbound -Protocol TCP -Action Allow -LocalPort 22
```

加入防火墙规则^[7]^（未测试过）。

#### ⑷ 配置SSH^[6]^

打开`C:\ProgramData\ssh\sshd_config`

```properties sshd_config
# 确保以下2条配置没有被注释
PubkeyAuthentication yes
AuthorizedKeysFile	.ssh/authorized_keys

# 此行代码若填no会拒绝口令登录，填yes时似乎注释掉也不影响
# To disable tunneled clear text passwords, change to no here!
PasswordAuthentication yes

# 确保以下2条有注释掉
#Match Group administrators
#       AuthorizedKeysFile __PROGRAMDATA__/ssh/administrators_authorized_keys
```

PowerShell中重启SSH服务器服务

```powershell
Restart-Service sshd
```

## 二、客户端设置

### 1. 安装OpenSSH客户端

#### ⓐ (Windows 7 及以前) 安装 Git for Windows^[2]^

为了获得Mintty以及包含的SSH工具。

#### ⓑ (Windows 10) 自带 ^[3]^

在设置的"管理可选功能"中打开OpenSSH客户端。

#### ⓒ Linux

一般的Linux发行版都安装了SSH客户端。如果没有，则运行

```shell
sudo apt-get install openssh-client
```

安装OpenSSH客户端。

### 2. 安装 VS Code 及其 Remote SSH 插件

扩展（Ctrl+Shift+X）选项卡搜索`Remote - SSH`，作者`Microsoft`。或者浏览器打开[Remote - SSH - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh)安装。

### 3. 测试SSH是否可用

*如果允许使用密码，则在这一步可以进行测试，否则直接跳转到[下一步](#4. 设置免密登录)。*

命令行／终端运行（相关字段根据环境填写）

```shell
ssh <destination> -p <port> -l <login_name>
# 或
ssh <login_name>@<destination> -p <port>
```

*注：允许密码登录时在这一步可以成功登录，若只允许密钥登录则先完成[下一步](#4. 设置免密登录)。*

##### 举例

被连接端IP`192.168.1.100`，SSH端口`10022`，被登录账户`user`；发起连接端IP`192.168.1.101`，则运行

```shell
ssh 192.168.1.100 -p 10022 -l user
# 或
ssh user@192.168.1.100 -p 10022
```

### 4. 设置免密登录

如果不需要设置请跳到下一步。若不设置免密登录则每次登陆都要键入密码。

#### ⑴ 客户端生成RSA密钥

命令行／终端运行

```shell
ssh-keygen -t rsa -b 4096 -C "comment"
```

*注：*`comment`参数用于用户区分密钥，推荐填入以下几种值

1. 用户邮箱：`youremail@example.com`
2. 用户名和主机名：`username@hostname`
3. 或者随便什么名字

连续按回车，直到生成私钥`id_rsa`和公钥`id_rsa.pub`。

#### ⑵ 在服务端登记RSA密钥

将客户端的**公钥**`~/.ssh/id_rsa.pub`的内容复制到服务端上的`~/.ssh/authorized_keys`文件（末尾新建一行），即可实现免密登录。

注：`~`在客户端上是登录服务端时使用的用户的家目录；在服务端上是被登录用户的家目录。在Windows的对应位置为`%USERPROFILE%\.ssh\`，即`~`替换为`%USERPROFILE%`。`%USERPROFILE%`是环境变量，在资源管理器的地址栏输入可以打开其指向的目录。`~`在Windows上仅在Git Bash（Mintty）工具中可以使用。

#### ⑶ ==（重要）==权限设置

##### ⓐ Linux系统的权限设置

Linux系统下需要以下额外设置，否则会不起效。

修改`~`目录、`.ssh`和`authorized_keys`的权限：

```shell
chmod 755 ~		# 如果默认不是，一定要执行一次，不能给766、777之类的值，限制其他用户的写权限
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

必须按照该权限设置，以满足SSH的安全要求，否则会被拒绝登录。

*文件所有者与当前用户对是否对应对免密登录可用性的影响尚不明确。*

##### ⓑ Windows系统的权限设置

使用Windows系统的默认权限即可。但是，如果其他用户获得了欲登录用户的`.ssh`目录的访问权限，则亦会被拒绝登录。此时在`.ssh`文件夹的`属性`—`安全`选项卡中改回默认即可（`.ssh`文件夹和其中的文件所有者必须是当前用户，且除默认管理员组外其他用户无访问权限）。

### 5. VS Code Remote SSH

#### ⑴ ==（重要）== 旧版Windows额外设置

因为旧版Windows（包括早期Windows 10）不带SSH，因此在路径中找不到`ssh.exe`（CMD中无法运行`ssh.exe`，但是Mintty中可以）。

将Mintty路径加入到环境变量会导致与Windows CMD指令的冲突，故不推荐。

最佳解决方法是在VS Code的`settgings.json`中加入一行配置：

```json settgings.json
{
	"remote.SSH.path": "......\\ssh.exe"
}
```

#### ⑵ 填写远程服务器配置

`~/.ssh/config`或`%USERPROFILE%\.ssh\config`文件：

```properties config
Host servername
	HostName destination
	Port port
	User login_name
	[IdentityFile "filepath"] # 若不使用默认文件位置
```

### 6. 连接测试

理论上此时已经可以使用了。按照[前文](#3. 测试SSH是否可用)中所述的命令测试一下。

需要注意的是，以上设置仅能使客户端能免密登录服务端（即免密登录是单向的）。若要让两台计算机可以互相登录，则需要分别在两台计算机上按照服务端和客户端的方法各设置一次。

## 三、已知问题

1. 如果`authorized_keys`文件及其所在文件夹权限有误会导致`Permission denied (publickey)`错误。
2. Windows 7上SSH工具有代码页导致的兼容性问题，使用Git Bash（Mintty）的方案解决。



## 参考文档

1. [VS Code Remote Development](https://code.visualstudio.com/docs/remote/remote-overview)；
2. [Installing a supported SSH client](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client)；
3. [Installation of OpenSSH For Windows Server 2019 and Windows 10](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse)；
4. [Linux之ssh免密登录](https://blog.csdn.net/mmd0308/article/details/73825953)；
5. [如何使用 SSH 远程控制一台 Windows 服务器](https://cloud.tencent.com/developer/article/1420930)；
6. [多台WIN10之间的SSH免密登录](https://zhuanlan.zhihu.com/p/111812831)；
7. [vscode Remote-ssh 远程控制windows主机](https://zhuanlan.zhihu.com/p/122999157)；
8. [群晖SSH密钥免密登录配置小记](https://blog.csdn.net/leirace/article/details/104547270)；
9. [SSH服务未设置开机时自启动 - 阿里云帮助中心](https://help.aliyun.com/document_detail/295967.html)；
10. [CentOS7.4配置SSH登录密码与密钥身份验证踩坑](https://www.cnblogs.com/Leroscox/p/9627809.html)；
11. [Windows7之SSH，安装OpenSSH实现SSH客户端及服务](https://blog.csdn.net/jerrygaoling/article/details/115308184)；
