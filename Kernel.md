



# kerne

## 1 编译

RK3308 EVB 开发板配置与编译如下 RK3128H EVB board：
cd kernel;
make ARCH=arm rockchip_linux_defconfig;
make ARCH=arm rk3128h-box.img CROSS_COMPILE=arm-linux-gnueabihf- -j16
编译完成后，kernel 根目录，生成以下二个镜像文件.
kernel/
├── kernel.img
├── resource.img



4.3 KERNEL  编译步骤
进入工程目录根目录执行以下命令自动完成 Kernel 的编译及打包：
./build.sh kernel
编译后在kernel目录生成zboot.img, zboot.img包含zImage和resource.img, resource.img打包了
dtb 和 uboot\kernel 的 logo 文件

```
 ARCH=arm 代表32位机器，不加的话默认64位机器
```

配置文件目录

```
cw@SYS3:~/sdk/312x_i/kernel/arch/arm/configs$ ls rockchip_linux_defconfig 
rockchip_linux_defconfig

配置文件里条目以CONFIG_开头
CONFIG_CRC7=y
# CONFIG_XZ_DEC_X86 is not set
```

