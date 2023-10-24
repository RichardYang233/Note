[跟我一起写 Makefile（完整版）_跟我一起写makefile_赏善罚饿的博客-CSDN博客](https://blog.csdn.net/whitefish520/article/details/103968609?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522169236149016777224428511%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=169236149016777224428511&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-103968609-null-null.142^v93^chatgptT3_1&utm_term=%E8%B7%9F%E6%88%91%E4%B8%80%E8%B5%B7%E5%86%99makefile&spm=1018.2226.3001.4187)

# 1、编译 compile

从`.c（源文件）`到`.obj（中间代码文件）`

---

# 2、链接 link

把大量`.obj`合成为`.exe（执行文件）`

    *链接函数*：不管源文件，只链接`.obj`

    *库文件*：中间文件太多，所以要打包成`.lib（库文件）`

    *全局变量*：

---

# 3、Makefile 介绍

### 规则

```makefile
target ... : prerequisites ...   # 目标文件 # 执行所需文件 
            command    # 执行命令
            ...
            ...
```

### 示例

 （3个头文件，8个.C文件）

```makefile
# 可执行文件
edit : main.o kbd.o command.o display.o /  
            insert.o search.o files.o utils.o
            # 依赖文件
            cc -o edit main.o kbd.o command.o display.o /    
            insert.o search.o files.o utils.o
            # 将这些 .o 文件链接成 edit.exe

# 中间目标文件
main.o : main.c defs.h    # 依赖文件
            cc -c main.c    # 编译 main.c 命令
kbd.o : kbd.c defs.h command.h
            cc -c kbd.c
command.o : command.c defs.h command.h
            cc -c command.c
display.o : display.c defs.h buffer.h
            cc -c display.c
insert.o : insert.c defs.h buffer.h
            cc -c insert.c
search.o : search.c defs.h buffer.h
            cc -c search.c
files.o : files.c defs.h buffer.h command.h
            cc -c files.c
utils.o : utils.c defs.h
            cc -c utils.c

# 伪目标文件
clean :    # 不是一个文件，是一个动作的名字，make clean 调用
rm edit main.o kbd.o command.o display.o /
            insert.o search.o files.o utils.o
```

### 变量

    当文件过多，为了避免手动输入，可以使用变量

    `Makefile`中的变量其实就是`C/C++`中的宏

```makefile
# 变量定义
objects = main.o kbd.o command.o display.o /
        insert.o search.o files.o utils.o
# 最终目标文件
edit : $(objects)
        cc -o edit $(objects)
# 中间目标文件
main.o : main.c defs.h
        cc -c main.c
kbd.o : kbd.c defs.h command.h
        cc -c kbd.c
command.o : command.c defs.h command.h
        cc -c command.c
display.o : display.c defs.h buffer.h
        cc -c display.c
insert.o : insert.c defs.h buffer.h
        cc -c insert.c
search.o : search.c defs.h buffer.h
        cc -c search.c
files.o : files.c defs.h buffer.h command.h
        cc -c files.c
utils.o : utils.c defs.h
        cc -c utils.c
# 伪目标文件
clean :
        rm edit $(objects)
```

### 自动推导

    对于 makefile ，看到`.o`会自动把`.c`加入依赖，同时`cc -c .c`也会被推导出来，所以 makefile不用写这么复杂

```makefile
# 变量定义
objects = main.o kbd.o command.o display.o /
        insert.o search.o files.o utils.o
# 最终目标文件
edit : $(objects)
        cc -o edit $(objects)
# 中间目标文件
main.o : defs.h
kbd.o : defs.h command.h
command.o : defs.h command.h
display.o : defs.h buffer.h
insert.o : defs.h buffer.h
search.o : defs.h buffer.h
files.o : defs.h buffer.h command.h
utils.o : defs.h
# 伪目标文件            
.PHONY : clean    # .PHONY 表示 clean 是个伪目标文件
clean :
        rm edit $(objects)
```

### 清空目标文件

    在`rm`命令前面加了一个`小减号`的意思是：也许某些文件出现问题，但不要管，继续做后面的事。

    `clean`的规则不要放在文件的开头，不成文的规矩是——clean从来都是放在文件的最后

```makefile
clean:
        rm edit $(objects)

# 下面更稳健
.PHONY : clean
clean :
        -rm edit $(objects)
```

---

# 4、总述

### Makefile 组成

- 显式规则
- 隐晦规则
- 变量定义
- 文件指示
- 注释

### Makefile 文件名

会在**当前目录**下按顺序找寻文件名为`GNUmakefile`、`makefile`、`Makefile`的文件

### 引用

```makefile
include <filename>
```

### 环境变量

TODO

### make 的工作方式

- 读入所有的`Makefile`。
- 读入被`include`的其它`Makefile`。
- 初始化文件中的变量。
- 推导隐晦规则，并分析所有规则。
- 为所有的目标文件创建依赖关系链。
- 根据依赖关系，决定哪些目标要重新生成。
- 执行生成命令。

---

# 5、书写规则

规则包含 2 个部分：

+ 依赖关系

+ 生成目标的方法

`Makefile`中只应该有一个 <u>最终目标</u>

第一条规则中的目标 将被确立为最终的目标

`make`所完成的也就是这个目标

### 语法

+ `targets`是文件名，以空格分开，可以使用通配符

+ `command`是命令行，必须以**Tab键**开头

+ 反斜线 `/` 作为换行符

### 通配符

+ `~/test`，这就表示当前用户的`$HOME`目录下的`test`目录

+ `~Yang/test`则表示用户`Yang`的宿主目录下的`test`目录

+ `*.c`表示所有后缀为c的文件
  
  eg、

```makefile
clean:
        rm -f *.o
```

### 文件搜寻

有大量的`.c`，我们通常的做法是把这许多的源文件分类，并存放在不同的目录中

最好的方法是把一个路径告诉`make`，让`make`自动去找

```makefile
VPATH = src:../headers
```

指定了，`src` 和 `../headers`两个目录，目录由`冒号:`分隔

<u>还有关于`vpath`的运用，暂不展开...</u>

### 伪目标

为了避免和文件重名的这种情况，我们可以使用一个特殊的标记`.PHONY`来显示地指明一个目标是`伪目标`

```makefile
.PHONY: clean
clean:
        rm *.o temp
```

<u>还有关于伪目标生成多个目标，和用作依赖的知识，暂不展开...</u>

.

.

.

# 6、书写命令
