# 在QEMU中模拟MIPS运行Debian

注意: 这个过程在Windows上貌似因为串口的原因无法正常运行. 请切换Linux进行.

## 过程

1. 准备10GB大小的raw磁盘镜像

```
qemu-img create hda.img 10G
```

2. 下载initrd和vmlinux, 均在[这里](http://ftp.debian.org/debian/dists/stable/main/installer-mipsel/current/images/malta/netboot/)
3. 通过挂内核和initrd来安装Debian, 这里模拟的是一块带4Kc的MIPS Malta主板

```
qemu-system-mipsel -initrd initrd.gz -kernel vmlinux-4.9.0-7-4kc-malta -nographic -hda hda.img -append "root=/dev/ram console=ttyS0" -m 256
```

4. 快安装好的时候会提示没有bootloader, 由于qemu能直接加载内核和initrd, 这个可以忽略
5. 安装好后, 挂载`hda.img`, 将`/boot`拷贝出来, 启动系统的时候我们会用到里面的initrd和vmlinux
6. 启动系统

```
qemu-system-mipsel -kernel boot/vmlinux-4.9.0-7-4kc-malta -initrd boot/initrd.img-4.9.0-7-4kc-malta -hda hda.img -append "root=/dev/sda1 console=ttyS0 nokaslr" -nographic -m 256
```

## 挂载IMG

操作均在root下进行

1. 查看IMG信息, 挂载的分区起始点为第2048个扇区, 一个扇区512字节, 即第1048576字节

```
# fdisk -l hda.img
Disk sda1.img: 10 GiB, 10737418240 bytes, 20971520 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xd57c9479

Device     Boot    Start      End  Sectors  Size Id Type
sda1.img1           2048 20103167 20101120  9.6G 83 Linux
sda1.img2       20105214 20969471   864258  422M  5 Extended
sda1.img5       20105216 20969471   864256  422M 82 Linux swap / Solaris
```

2. 查看空loop设备, 这里以`/dev/loop2`为例

```
# losetup -f
/dev/loop2
```

3. 挂载到`/mnt`

```
losetup -o 1048576 /dev/loop2 hda.img
mount /dev/loop2 /mnt
```

4. 卸载

```
umount /mnt
losetup -d /dev/loop2
```

## 参考

[Debian on an emulated MIPS(EL) machine](https://www.aurel32.net/info/debian_mips_qemu.php)
