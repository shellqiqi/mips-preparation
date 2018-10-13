# QEMU简介

QEMU提供了诸多的处理器用来模拟, 能在没有相应硬件的环境下来测试软件. 在[QEMU](https://www.qemu.org/download/)上下载对应平台的版本. 预计占用空间为1GB, 减少安装的处理器种类可以减少安装大小. 我们这里会用到QEMU中提供的i386和mips两种处理器.

## 模拟x86下重复BIOS实验

这里以Windows平台为例, 使用类Unix系统的同学应该有能力自行解决差异.

1. 安装QEMU
2. 添加QEMU到环境变量(为了方便，不嫌麻烦输入长长的路径可以跳过此步骤)
3. 在终端中打开一个工作文件夹, 新建一个大小为512KB的镜像文件, 命名为`x86.bin`

```
qemu-img create x86.bin 512
```

4. 用二进制编辑器修改为引导扇区, 修改方法见上一节
5. 运行x86_64模拟器

```
qemu-system-x86_64 -drive format=raw,file=x86.bin
```

## 模拟mips实验

注意到QEMU中对mips的命名, 其中mips对应大端字节序, mipsel对应小端字节序. 我们这次使用的是小端字节序, CPU型号是M14K.

找到`MIPSfpga_V2.0\MIPSfpga_SOC_1_0_2016_03_11\documents\Starter_Tutorial`下的vmlinux, 运行

```
qemu-system-mipsel -cpu M14K -kernel vmlinux
```

由于这个编译版本的linux的输入输出是串口, 而串口恐怕不是按linux设备标准实现, 所以应该只能看到模拟器跑了很高的CPU占用, 各个设备却没有反应.
