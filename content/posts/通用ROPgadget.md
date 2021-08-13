---
title: "通用ROPgadget"
date: 2021-08-13T14:49:18+08:00
draft: false
---

# 								通用ROPgadget

## 原理

在64位程序中，函数的前六个参数是通过寄存器传递的，但是大多时候，我们很难找到每一个寄存器对应的gadgets，这时候，我们可以利用x64下的**__libc_csu_init**中的gadgets。这个函数是用来对libc进行初始化操作，而一般的程序都会调用libc函数，所以这个函数一定会存在。

![image-20210813145240195](/images1/image-20210813145240195.png)

**第一个gadget**

```assembly
pop rbx
pop rbp
pop r12
pop r13
pop r14
pop r15
retn
```

可以看到，通过这段gadget可以控制rbx、rbp、r12、r13、r14、r15，6个寄存器，而恰好64位程序就是通过寄存器来传递参数的。

**第二个gadget**

```assembly
mov rdx,r13
mov rsi,r14
mov edi,r15
call qword ptr [r12+rbx*8]
add rbx,1
cmp rbx,rbp
jnz short loc_400580
```

可以看到，在这里rdx的值由r13决定，rsi的值由r14决定，**值得特别注意的是**，r15只能决定edi的值，而不能完全控制rdi。而随后的call，可以看到只要rbx=0，那么call便由r12所决定了。之后只要rbx+1等于rbp就不会跳转。所以可让rbp等于1。

