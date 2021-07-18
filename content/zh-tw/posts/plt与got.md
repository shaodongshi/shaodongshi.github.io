---
title: "Plt与got"
date: 2021-07-11T15:07:02+08:00
draft: false
---

# 						plt与got理解

## 0x1 基础知识

### .got

GOT(Global Offset Table)全局偏移表。这是「链接器」为「外部符号」填充的实际偏移表。

### .plt

PLT（Procedure Linkage Table）程序链接表。它有两个功能，要么在 `.got.plt` 节中拿到地址，并跳转。要么当 `.got.plt` 没有所需地址的时，触发「链接器」去找到所需地址。

### .got.plt

这个是 GOT 专门为 PLT 专门准备的节。说白了，**.got.plt 中的值是 GOT 的一部分**。它包含上述 PLT 表所需地址（已经找到的和需要去触发的）。

### .plt.got

貌似没啥用。

## 0x2  理解

```c#
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char **argv)
{
    puts("Hello world!");
    puts("Hello CTF!")
    exit(0);
}
```

当调用第一次puts时，流程如下

![one](/images1/one.png)

第二次调用puts时，流程如下

![two](/images1/two.png)

## 0x3 总结

.plt 的作用简而言之就是先去 .got.plt 里面找地址，如果找的到，就去执行函数，如果是下一条指令的地址，说明没有，就会去触发链接器找到地址。

.got.plt 显而易见用来存储地址，.got.plt 确实是 GOT 的一部分。