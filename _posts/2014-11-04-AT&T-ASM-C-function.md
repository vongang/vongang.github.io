---
title: AT&T汇编笔记-C语言函数执行过程
layout: post
category: ASM
---

# AT&T汇编笔记-C语言函数执行过程

- AT&T汇编的语法跟8086的很相似，之前学过8086的汇编(原谅我但是没学好)，最近正巧拿到一本书《深入理解程序设计-使用linux汇编语言》，重点理解一下程序执行过程。
- 当一个函数被调用（汇编指令:`call func(param...)`)，函数的参数会依次被压入栈中，如下所示

	参数 #N
    	...
    	参数 #2
    	参数 #1
    	返回地址 <----(%esp(栈顶))

- 参数及返回地址入栈后，被调用的函数本身还有事要做。
- 首先，函数通过```pushl %ebp```指令保存当前的基址指针寄存器`%ebp`。基址指针寄存器主要用于访问函数的参数和局部变量（后面的例子可以很直观的看到）
- 另外，它会执行```movl %esp, %ebp```，将栈顶指针%esp复制给%ebp（%ebp正是通过这个栈顶指针来访问函数的参数和局部变量的。）当然，专用寄存器%esp是不能随意使用的。

	参数 #N     <---- (%ebp指向参数列表)
    	...
    	参数 #2     <---- (%ebp)
    	参数 #1     <---- (%ebp)
    	返回地址    <---- (%ebp)
    	odd %ebp    <---- (执行 pushl %ebp，%esp和%ebp目前同时指向现在位置)
    	
##### 注：栈在内存中是向下生长的，即栈顶地址不断减小。

#### 函数示例

```asm
# power.s
# 计算2^3 + 5^2的值

#数据段为空
.section .data

#代码段
.section .text

.globl _start
_start:
    pushl $3	#压入第2个参数
	pushl $2    #压入第一个参数
	call power  #调用函数
	subl $8, %esp
	pushl %eax      #保存结果到栈

	pushl $2    #压入第2个参数
	pushl $5    #压入第1个参数
	call power 
	addl $8, %esp 

	popl %ebx   #第一次计算的结果(2^3的结果)出栈放到%ebx中

	addl %eax, %ebx     #求和

	movl $1, %eax   #退出参数
	int $0x80       #中断退出

.type power, @function
power:              #计算幂次的函数
	pushl %ebp
	movl %esp, %ebp     #这两个操作上文提到过
	subl $4, %esp       #在栈顶开缓冲区，用于存放临时计算结果

	movl 8(%ebp), %ebx      #取第一个参数
	movl 12(%ebp), %ecx     #取第二个参数

	movl %ebx, -4(%ebp)     #将临时结果存入缓冲区
power_loop:
	cmpl $1, %ecx       #幂次降到1,退出
	je end_power
	movl -4(%ebp), %eax     #从栈顶缓冲区取出先前计算的值，与基数相乘
	imull %ebx, %eax
	movl %eax, -4(%ebp)     #将乘法运算后的值放回栈顶

	decl %ecx
	jmp power_loop

end_power:
	movl -4(%ebp), %eax     #计算完成，将结果存入%eax
	movl %ebp, %esp         #恢复%esp
	popl %ebp               #恢复%ebp
	ret
```

编译、链接、运行

```sh
$ as --32 power.s -o power.o
$ ld -m elt_i386 power.o -o power
$ ./power
$ echo $?
$ 33
```

##### 注：以上汇编代码为32位，但是我的电脑是Debian 64bit，所以编译链接时需要加特殊操作，不然就要把%esp, %ebp改为%rsp, %rbp, 把pushl改为pushq以适应64位环境


