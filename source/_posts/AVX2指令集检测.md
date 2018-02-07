---
title: AVX2指令集检测
date: 2018-02-07 11:09:04
tags: ["SIMD", "C++"]
categories: 技术
---
通过一段内嵌汇编代码来检测CPU以及OS是否支持AVX2(Advanced Vector Extensions 2)指令集。

<!--more-->
最近在阅读《Intel® 64 and IA-32 Architectures Optimization Reference Manual》，从中学习SIMD技术的用法和规范。其中介绍了检测MMX，SSE，SSE2，SSE3，SSSE3，SSE4.1，SSE4.2，AVX，AVX2等SIMD指令集方法，而如今使用的处理器绝大多数都支持这些指令集，因此只需要检测最新、最强的AVX2是否支持，然后依次倒推。

>\#include &lt;iostream&gt;

> using namespace std;

> int main() {

>      __asm {

>	    	mov eax, 1
		cpuid
		and ecx, 018000000H
		cmp ecx, 018000000H  ;检测OSXSAVE和AVX
		jne not_supported  
		mov eax, 7
		mov ecx, 0
		cpuid
		and ebx, 20H
		cmp ebx, 20H         ;检测AVX2
		jne not_supported
		mov ecx, 0   	     ;将 XFEATURE_ENABLED_MASK 寄存器置零
		XGETBV               ;输出存于EDX：EAX
		and eax, 06H
		cmp eax, 06H         ;检测OS是否支持XMM和YMM寄存器
		jne not_supported
		mov eax, 1
		jmp $                ;都检测成功则停留于此，否则会继续执行C++ cout语句。
	    not_supported:    
	}

>	cout << "Sorry, Your process or OS do not support AVX2" << endl;
	
>	return 0;

>  }

直接将以上代码复制到VS里，编译运行，如果出现一个无输出的cmd框且不消失，则该计算机可以执行AVX2指令。

后记：以前读的教科书如微机、计组等讲的要么太旧(8086),要么太玄(不容易掌握内涵，总感觉学得很虚)，如今亲自读了Intel的手册，顿有醍醐灌顶之感，本来虚无飘渺的概念瞬间清晰起来，变得更有立体感了。所以呀，学习计算机底层架构不仅需要通过教科书了解一定的概念，建立基本知识体系，还要阅读针对性的手册，真正地深入整个架构体系和思想。毕竟计算机硬件层面的发展与工业界息息相关，教科书里的知识可能也都是从相应的手册摘取出来的，因此若想进一步加深理解，何不追溯本源从这些手册入手呢？