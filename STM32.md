# 三、开发环境搭建

+ 集成开发环境（IDE）：MDK    `（已安装）`

+ 仿真器驱动：ST LINK
  
  > *D:\研究生\学习资料\【正点原子】战舰STM32F103开发板V4 资料盘(A盘)\6，软件资料\1，软件\5，其他软件\ST LINK驱动及教程*
  
  + DPA仿真器免驱可调式（推荐）

+ 串口调试助手：CH340 USB 虚拟串口驱动    `（已安装）`
  
  + 为什么安装驱动：识别通信协议
  
  + 作用： USB当串口；USB当供电口

---

# 四、STM32体验

### 编译例程

 ![](C:\Users\Yang\AppData\Roaming\marktext\images\2023-09-04-17-39-01-image.png)

+ program Size：Flash 和 SRAM 的占用情况

+ FromELF：0 Error 才会创建`.hex`文件，用来下载到开发板

### 串口下载

+ 须知
  
  + M3、M4、M7 开发板支持串口下载程序
  
  + ATK-XISP.exe 只支持下载到内部 FLASH
  
  + STM32 的 ISP 下载，常用串口1下载程序
  
  + 因为使用USB虚拟串口，需要安装驱动 CH340
  
  + 不推荐使用串口下载。DAP下载才能仿真和调试

+ 硬件连接
  
  <img src="file:///C:/Users/Yang/AppData/Roaming/marktext/images/2023-09-04-18-06-39-image.png" title="" alt="" width="240">

+ 配置下载工具
  
  + ATK-XISP.exe
  
  > *D:\研究生\学习资料\【正点原子】战舰STM32F103开发板V4 资料盘(A盘)\6，软件资料\1，软件\5，其他软件\STM32串口下载软件（ATK-XISP）*
  
  > <img title="" src="file:///C:/Users/Yang/AppData/Roaming/marktext/images/2023-09-04-18-22-55-image.png" alt="" width="328" data-align="inline">
  
  > <img src="file:///C:/Users/Yang/AppData/Roaming/marktext/images/2023-09-04-18-30-41-image.png" title="" alt="" width="333">

+ STM32启动模式
  
  + 通过 BOOT0、BOOT1 引脚设置启动模式

### MDK5使用技巧

+ 文本美化

+ 代码提示和语法检测

+ 保存 MDK 设置内容
  
  > *E:\MDK5.36\UV4\global.prop*

+ 多行左移：`shift + tab`

+ 快速定位函数
  
  + 选中 + `鼠标右键` + 选择 `Go to Definition of  “xxx”`
  
  + 选中 + `F12`

+ 快速注释
  
  + 选择 + `鼠标右键` + 选择 `Advanced --> Comment Selection`
  
  + 选择 + ![](C:\Users\Yang\AppData\Roaming\marktext\images\2023-09-05-17-07-39-image.png)

+ 查找&替换技巧
  
  + 快速打开头文件：选中 + `鼠标右键` + 选择 `Open Document "xxx.h"`
  
  + 查找功能：![](C:\Users\Yang\AppData\Roaming\marktext\images\2023-09-05-17-51-12-image.png)

+ 编译问题定位
  
  + 双击 Error(s), warning(s)

+ 窗口视图管理
  
  + 恢复默认：`window --> Reset View to Defaults +Reset`

---

# 五、基础知识入门

### C语言复习

+ stdint.h 简介
  
  > E:\MDK.534\ARM\ARMCC\include
  
  > <img title="" src="file:///C:/Users/Yang/AppData/Roaming/marktext/images/2023-09-05-18-29-39-image.png" alt="" width="243">
  
  + 类型别名（typedef）
    
    ```cpp
    typedef 现有类型 新名字
    ```
    
    ```cpp
    /*结构体类型别名*/
    Struct GPIO
    {
        ...
    };
    Struct GPIO gpiox
    
    typedef struct
    {
        ...
    }GPIO;
    GPIO gpiox
    ```

+ 位操作
  
  > <img src="file:///C:/Users/Yang/AppData/Roaming/marktext/images/2023-09-05-18-51-55-image.png" title="" alt="" width="237">

+ 宏定义
  
  + 效率、可读性、易改性、核心是替换
    
    ```cpp
    #define 标识符 字符串
    标识符：  宏定义名字；大写英文；不占内存
    字符串：  常数、表达式、格式串
    
    eg、
    #define PI        3.14159
    #define HSE_VALUE 8000000U
    ```
  
  + 带参数宏定义（推荐使用）
    
    + 不受到大括号、分号、运算符优先级的影响 
      
      ```cpp
      #define LED(x) do{ x?\
          A:\
          B;\
      }while(0)
      ```
  
  + 条件编译
    
    > <img title="" src="file:///C:/Users/Yang/AppData/Roaming/marktext/images/2023-09-05-19-45-14-image.png" alt="" width="336">
    
    ```cpp
     #ifndef _LED_H
     #define _LED_H
     #include “./SYSTEM/sys/sys.h”
     code
     #endif
    ```
    
    + 避免头文件被重复引用，从而减少编译时间
  
  + extern声明
    
    + 放在函数\变量前，表示其在其他文件定义，以便本文件引用
      
      ```cpp
      extern uint16_t g_usart_rx_sta;
      extern void delay_us(uint32_t nus)
      ```
  
  + 结构体
    
    ```cpp
    struct Name
    {
        成员列表;
        ...;
    }变量名列表（可选）;
    ```
  
  + 指针
    
    + 指针：内存的地址
    
    + 指针变量：保存了指针的变量
    
    ```cpp
    char *p_str = "this is a test"
    *p_str    / 取 p_str 变量的值
    &p_str    / 取 p_str 变量的地址
    ```
    
    + 常见问题
      
      1、未分配内存就使用
      
      2、越界使用

原理图

参考手册
