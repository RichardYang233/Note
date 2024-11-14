# FPGA

### 计划表

| 计划 | 进行中 | 已完成 |
| ---- | ------ | ------ |
|      |        |        |
|      |        |        |
|      |        |        |

### 路径

+ **基础**
  + verilog
  + 计算机体系结构，或者微机原理，单片机原理
+ **专业**
  + 数字IC前端/FPGA基础设计
  + On-Chip-Bus（片上总线）
  + 数字IP设计：验证综合实现
+ **进阶**
  + 通信相关：SDR
  + 数字信号处理：FFT，FIR，IIR，自适应滤波器
  + 接口：UART，SPI，IIC，DDR
  + linux，makefile

ARM架构学习

> 参考：
>
> [数字IC/FPGA设计100问之——学习路径](https://zhuanlan.zhihu.com/p/356601327)
>
> [数字IC基础学这些就够了](https://zhuanlan.zhihu.com/p/670736813)

### 方向

通信，DSP，图像音频处理，高速接口，硬件加速，人工智能，CPU，SoC

### 项目

[知乎回答1](https://www.zhihu.com/question/543330723/answer/3112389636)

[数字IC/FPGA设计100问之——学习路径](https://zhuanlan.zhihu.com/p/356601327)

> 给你一条能一次性玩这三个技术栈从软到硬的路线。
>
> 买一个zynq开发板开始学，ps侧内嵌的硬核arm可以让你玩linux，pl端满足你fpga的学习需求，这期间软硬结合就是嵌入式。
>
> 等你都学差不多了，自己从原理图到pcb做一个板子。在pl侧fpga设计特殊功能模块，这个模块通过axi总线连接到ps，这样你的arm就能通过io访问pl模块内部的寄存器。然后你arm断移植启动linux，你自己再写一个驱动去控制pl的设备。

### 注意

+ 重点在理论学习

+ 知乎老莫：我一直推荐“基于比赛的学习方法”。在大学生集成电路创新创业大赛上找数字IC和FPGA赛道的题目，自己从头到位做出来一个，就够了。

+ 很多经验丰富的数字逻辑设计工程师都不一定看懂IIC协议，更别说在有限的时间内设计一个兼容性良好的IIC 主从模块了。

  把一件小事情里外彻底弄懂，会有助于你产生本质的自信。能解剖一只麻雀，不需要达到能重新组装的程度，就不愁找不到工作。

  IIC>SPI>UART

+ 不要把FPGA当单片机用

+ 分析板子的设计，再自己做板子

+ 题主提到嵌入式、linux和FPGA均衡发展，这种技术栈组合在IoT时代很有潜力。IoT设备应以中低端SoC嵌入式芯片为主，比如ARM SoC，因为传统的低性能单片机已经很难满足多样的应用需求。这些系统可以跑起标准或精简的linux，方便丰富且快速迭代的软件开发。而中低端SoC的瓶颈在于处理器性能难以应对定制化的计算密集型需求，比如图像处理、加解密，此时硬件功能可定制的FPGA就是理想的搭档。

### 岗位

---

### 相关

[一生一芯](https://ysyx.oscc.cc/)

[UWisc ECE752](https://link.zhihu.com/?target=https%3A//ece752.ece.wisc.edu/)

[stackoverflow](https://zhida.zhihu.com/search?q=stackoverflow&zhida_source=entity&is_preview=1)

[计算机体系结构网课](https://space.bilibili.com/3546571966777618/channel/collectiondetail?sid=2111048)

张颢老师的现代信号处理的课程

杜勇老师的几本书（数字滤波器，调制解调，数字同步，锁相环共四本书）

