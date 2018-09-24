# BIOS实验

## 环境

- x86或x64平台
- 虚拟机软件, 如VirtualBox, VMWare等
- 二进制编辑器, 如WinHex(收费), HxD(免费)等

## 理论

系统启动时BIOS检查外存, 读取每个外存的Cylinder 0, Head 0, Sector 0的扇区, 如果这个扇区以`0xaa55`结尾, 则为引导扇区.

## 操作

1. 新建一个虚拟电脑, 分配一个4MB固定大小的VHD格式的硬盘
2. 打开虚拟电脑，提示没有操作系统
3. 用二进制编辑器打开新建的虚拟磁盘, 修改前三个字节为`0xe9`, `0xfd`和`0xff`, 修改512字节的前两个字节为`0x55`, `0xaa`

```
e9 fd ff 00 00 00 00 00 00 00 00 00 00 00 00 00
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
......
00 00 00 00 00 00 00 00 00 00 00 00 00 00 55 aa
```

4. 打开虚拟电脑, 系统不会显示未找到操作系统, 忙等

## 解释

前三个字节是跳转指令, `JMP 0xFFFD`, 也就是往回调转三个字节, 无限循环. 扇区最后的`0xaa55`告诉BIOS这是一个引导扇区.