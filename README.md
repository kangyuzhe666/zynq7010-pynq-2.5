### 蚂蚁S9矿板ZYNQ7010开发板移植PYNQ_2.5

蚂蚁S9矿板，板号：C41 V1.2，我买的版本是两颗256MB 16bit DDR3内存颗粒的版本，因此板上内存容量为1GB，移植也是基于1GB这个版本的，512MB内存版本需要进行相应的修改。

参考链接：https://pynq.readthedocs.io/en/latest/pynq_sd_card.html#building-the-image

软件环境：

`Ubuntu18.04`

`Vivado 2019.1`

`SDK  2019.1`

`Petalinux 2019.1` 

![image-20200904102113339](https://i.loli.net/2020/09/04/YLP7HnRkhUpQwa3.png)

**注意所有软件版本要一致**

### 1.在vivado中配置最小系统

![image-20200904100950872](https://i.loli.net/2020/09/04/DO615panGKwMgCU.png)

![image-20200904101550085](https://i.loli.net/2020/09/04/pu4BgxE7YQZGTqD.png)

注意勾选正确的bank电压bank500->bank0 3.3v bank501->bank1 2.5v,这里要参考蚂蚁S9原理图

![image-20200904101741824](https://i.loli.net/2020/09/04/fwKE1av5SmeZs93.png)

在DDR配置中选择256M16型号的颗粒由于是两颗DDR3选择32Bit,其他配置默认即可

### 2.导出bitstream文件和描述文件

![image-20200904101937220](https://i.loli.net/2020/09/04/RMXQagVcywDEfI2.png)

导出硬件描述，和bitstream一起导出

![image-20200904102839044](https://i.loli.net/2020/09/04/yGPioabjxm2up6R.png)

### 3.下载PYNQ源码

```
git clone https://github.com/Xilinx/PYNQ.git
```



### 4.下载[PYNQ_2.5的rootfs文件](http://bit.ly/33fftBw)

如果链接无效可以从http://www.pynq.io/board.html寻找PYNQ rootfs arm v2.5下载

### 5.创建自己的分支

```
cd PYNQ
git checkout v2.5
git checkout -b s9_pynq-2.5
```

### 6.运行环境构建脚本安装相应的依赖

```
cd sdbuild/scripts/
./setup_host.sh
```

其中docker相关的可能会失败注释掉即可，有些源码编译的依赖由于国内网络的问题需要自行解决。

### 7.配置软件的环境变量

```
source /tools/Xilinx/petalinux/settings.sh 
source /tools/Xilinx/Vivado/2019.1/settings64.sh 
source /tools/Xilinx/SDK/2019.1/settings64.sh 
export PATH=/opt/qemu/bin:/opt/crosstool-ng/bin:$PATH
```

### 8.配置移植所需要的文件和目录

在boards目录下新建一个目录命名为s9_pynq按以下创建子目录

![image-20200904103728954](https://i.loli.net/2020/09/04/LxhN7JSlzeb9Dcu.png)

其中design_1_wrapper.bit->base.bit design_1_wrapper.hdf->system.hdf

s9_pynq.spec内容如下

```
ARCH_s9_pynq := arm
BSP_s9_pynq :=
BITSTREAM_s9_pynq := base/base.bit
```

### 9.编译新的镜像

```
make BOARDS=s9_pynq \
PREBUILT=bionic.arm.2.5.img
```

### 10.在sdbuild/output目录下得到镜像文件烧录到SD卡即可

### 注意事项

可能会有网卡不能启动的问题，将PYNQ/sdbuild/packages/ethernet下的eth0复制到板子系统的/etc/network/interfaces.d目录下重启即可，jupyter默认在9090端口，用户名和密码为xilinx

我将自己编译好的镜像上传到云盘可自行下载测试

链接：https://pan.baidu.com/s/1HbiiMz_5l4CoHDzb7_j66Q 
提取码：rmr6

