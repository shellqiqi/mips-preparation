# 移植到Minisys

虽然与1.2版本的文档类似地建立项目, 但是项目以**参照2.0版本**的文档为基础, 并结合本文档说明的**不同点**进行操作.

## 建立项目

### 添加verilog文件到项目中

为了让项目可重置, 建立项目时从`MIPSfpga_Labs\rtl_up`引入的文件一律勾选Copy source into project.

### 添加约束文件到项目中

在加入约束文件(Add Constraints)不要引入`mfp_nexys4_ddr.xdc`, 这个文件与Minisys开发板不一致.

在这一步你可以直接跳过.

### 选择Artix-7 FPGA

选择Minisys所使用的xc7a100tfgg484-1.

### 针对Minisys的修改

#### 约束文件

在仿真之前我们还要添加Minisys对应的约束文件, 该约束在1.2版本中使用过, 请拷贝引入到项目中, 同时要对新定义的`UART_TXD_IN`接在一个已知不用的元件上, 这里使用的L4是4*4键盘的一根信号线.

```diff
  ##USB-RS232 Interface     NO CHANGE

+ set_property -dict { PACKAGE_PIN L4    IOSTANDARD LVCMOS33 } [get_ports { UART_TXD_IN }]; #IO_L7P_T1_AD6P_35 Sch=uart_txd_in
- set_property -dict { PACKAGE_PIN C4    IOSTANDARD LVCMOS33 } [get_ports { UART_TXD_IN }]; #IO_L7P_T1_AD6P_35 Sch=uart_txd_in
```

请注意, 在进行前期实验的时候请**禁用没有定义的引脚的约束**, 否则会在生成bit时失败而耗费大量重新编译的时间.

#### 头文件

在`mfp_ahb_const.vh`中定义了和LED与开关的常量, 对应Minisys有24对, 更改其中的

```diff
- `define MFP_N_LED             16
+ `define MFP_N_LED             24
- `define MFP_N_SW              16
+ `define MFP_N_SW              24
```

## 编译

与文档一致.
