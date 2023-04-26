<!--
 * @Author: HUBU Unicersity GXZ 248115781@qq.com
 * @Date: 2023-03-21 01:30:24
 * @LastEditors: HUBU Unicersity GXZ 248115781@qq.com
 * @LastEditTime: 2023-03-21 16:39:02
 * @FilePath: /undefined/home/gxz/Desktop/md/从C语言到二进制文件.md
 * @Description: 这是默认设置,请设置`customMade`, 打开koroFileHeader查看配置 进行设置: https://github.com/OBKoro1/koro1FileHeader/wiki/%E9%85%8D%E7%BD%AE
-->
linux下C语言的编译
编译
```
gcc hello.c -o hello
```
具体步骤
```
gcc -E hello.c -o hello.i  # 预处理阶段
gcc -S hello.i -o hello.s  # 编译阶段
gcc -c hello.s -o hello.o  # 汇编阶段
gcc hello.o -o hello       # 链接阶段
```

gcc是如何找到头文件的呢
方法：阅读工具的日志

使用`gcc -E a.c --verbose >/dev/null`指令，丢弃预处理的部分
可以看到这是从哪里开始找的include文件，找了哪些文件夹下的文件

使用-i指令可以指定一个文件夹去扫描
echo '#warning I am wrong!' >stdio
gcc -E a.c --verbose
mkdir aaa bbb
gcc -E a.c -Iaaa -Ibbb --verbose > /dev/null        //优先空aaa和bbb目录下搜索头文件
echo '#warning I am wrong,too!' >bbb/stdio.h
echo '#define printf(...)' >>bbb/stdio.h
gcc -E a.c -Iaaa -Ibbb --verbose

预处理的时候只进行文本的粘贴，不求数值，小心优先级

预处理的其他工作
- 去掉注释
- 链接因断行符而拆分的字符串
- 处理条件编译`#ifdef/#else/#endif`

字符串化 #
标识符连接 ##
```c
#define str(x) #x
#define _concat(a,b) a##b
_concat(pr,intf)(_str(RISC-V));
```


编译是一个比较复杂的过程
词法分析，语法分析，语义分析，中间代码生成，优化，目标代码生成

借助合适的工具（clang）

词法分析
clang -fsyntax-only -Xclang -dump-tokes a.c
语法分析(abstract syntax tree)反映源程序的层次结构
clang -fsyntax-only -Xclang -ast-dump a.c

语义分析
按照C语言的定义确定AST中每个表达式的类型

中间代码生成
clang -S -emit-llvm a.c
中间代码=一种编译器定义的面向编译场景的指令集
本质上编译过程中的一个抽象层，基于抽象层进行优化很容易
可以支持多源语言和目标语言

优化
C语言可以严格执行，也可以宽松执行，宽松的情况下需要满足程序的可观测行为
- 对volatile变量访问需要严格执行
- 程序结束时，写入文件的数据需要与严格执行时一致
- 交互式设备的输入输出需要与严格执行时一致（就是说输出给用户看的东西必须严格，不能优化）

目标代码生成
将中间代码翻译成目标指令集

clang -S a.c
clang -S a.c --target=riscv64 -march=rv64g

可以通过
clang -S a.c -ftime-report
观察优化了哪些工作



二进制的世界

clang -c a.c
clang -c a.c --target=riscv64 -march=rv64g
根据指令集手册，把汇编代码翻译成二进制目标文件

二进制文件不能用文本阅读打开
需要使用binutils或者llvm的工具链    
ogjdump -d a.o
riscv64-linux-gnu objdump -d a.o

链接
合并多个目标文件，生成可执行文件
clang a.c
riscv64-linux-gnu-gcc a.c
- 哪里来的多个目标文件呢
clang a.c --verbose
clang a.c --verbose 2>&1 | tail -n 1 |tr ‘ ’ ‘\n’|grep '\.o$'
执行之后发现有很多`crtxxx.o`的文件
可以通过objdum确认

执行
把可执行文件加载到内存，跳转到程序，执行编译出的指令序列
运行时环境（宿主操作系统来加载）



# 例子


## 实现定义行为

```
printf(6 - 2147483648 > 6 ? "T" : "F");
printf(6 - 0x80000000 > 6 ? "T" : "F");
printf("\n");
```
c90/c99和32位/64位组合下结果
```
clang -w -std=c90 -m32 a.c && ./a.out
clang -w -std=c99 -m32 a.c && ./a.out
clang -w -std=c90 -m64 a.c && ./a.out
clang -w -std=c99 -m64 a.c && ./a.out
```
TT
FT
FT
FT

为什么是上述结果

**学会通过日志观察工具的行为**
直接看语法tree
`clang -fsyntax-only -Xclang -ast-dump a.c`

```
clang -fsyntax-only -Xclang -ast-dump a.c -w -std=c99 -m64
clang -fsyntax-only -Xclang -ast-dump a.c -w -std=c99 -m32
clang -fsyntax-only -Xclang -ast-dump a.c -w -std=c90 -m64
clang -fsyntax-only -Xclang -ast-dump a.c -w -std=c90 -m32
```
long
long long


2147483648在clang语法树中的类型
        32              64
c90   unsigned long   long
c99   long long       long


C90可以表示的类型int、long、usigned long
C99可以表示的类型int、long、long long


32位下
char = 1
short = 2
float = 4
double = 8
int = 4
long = 4
long long = 8
void* =4

64位
char = 1 
short = 2
float = 4
double = 8
int = 4
long = 8
long long = 8
void* = 8



C语言标准没有定义类型的长度，而是全部交给具体实现来定义

为什么这样
要支持所有的计算机系统，于是规定不能太死
一个字节是多少，多少bit都用过

要支持将来的计算机系统个，于是很多规定也要给未来留个口
    所以只定义了类型的最小范围


## 未指定行为（就是没有精确指出行为的行为）
C标准提供多种行为可选，具体实现需要选择

比如
```c
#include <stdio.h>
void f(int x, int y) {
  printf("x = %d, y = %d\n", x, y);
}
int main() {
  int i = 1;
  f(i ++, i ++);
  return 0;
}
```

这里就是C标准没有定义,fun函数执行的时候不同编译器的执行先后顺序不同

## 实现定义行为
一类特殊的未指定行为的具体实现需要将选择写到文档里
写入之后不能随便更改；
为专门特定系统开发程序，用户可以选择假设这些选择成立

也就是说在自己编写的系统手册中将未定义行为进行定义

如类型长度`assert(sizeof(long)==4);//在某特定32位系统上总是成立`

包含这种行为的程序，在相同环境的运行下可以得到相同的结果，但是移植


## 未定义行为
程序/数据不符合标准的行为
- 完全没说会发生什么，一切皆有可能


## 实现定义行为
一类特殊的未指定行为，具体实现需要写到文档里面
C99关于“implementation”的定义
在系统中需要编译器+（操作系统+库函数）
三者需要对implementation-defined behavior 达成一致的约定 

这就是ABI(Application Binary Interface)
- 处理器的指令集，寄存器的结构，栈的组织，访存类型
- 处理器可以直接访问的基本数据类型的大小，布局，对齐方式
- 调用约定，用于规定函数的参数如何传递，返回值如何获取
- 应用程序如何向操作系统发起系统调用
- 目标文件的格式，支持的库函数

**ABI手册是计算机系统软件件协同的重要体现**

查阅对应ABI手册，arm的，riscv的


最后一个例子
```
//a.c
char c = 0xff;
printf(c == 0xff ?"T" : "F");
```
直接编译就是F<br>
`gcc a.c && ./a.out`

但是使用riscv进行编译是T<br>
`riscv64-linux-gnu-gcc a.c && ./a.out`


char的符号也是implementation-defined
所以需要查看对应的ABI

比如x86架构就是把char定义成有符号 
但是riscv中char是无符号

- 所以不要用char来进行算术运算，要进行的话使用signed或者unsigned char




















