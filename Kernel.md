



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







```
cw@SYS3:~/sdk/312x_i/kernel$ cat drivers/he
headset_observe/ hello/           
cw@SYS3:~/sdk/312x_i/kernel$ cat drivers/hello/hello.c
#include <linux/init.h>
#include <linux/module.h>
 
static int __init hello_init(void) {
    printk(KERN_ALERT "Hello, world\n");
    return 0;
}
 
static void __exit hello_exit(void) {
    printk(KERN_ALERT "Goodbye, cruel world\n");
}
 
MODULE_LICENSE("Dual BSD/GPL");
 
module_init(hello_init);
module_exit(hello_exit);


diff --git a/drivers/Kconfig b/drivers/Kconfig
index c7a3c412bce1..3c94b9ea5802 100644
--- a/drivers/Kconfig
+++ b/drivers/Kconfig
@@ -202,6 +202,8 @@ source "drivers/fpga/Kconfig"
 
 source "drivers/tee/Kconfig"
 
+source "drivers/hello/Kconfig"
+
 source "drivers/rkflash/Kconfig"
 
 source "drivers/rk_nand/Kconfig"
diff --git a/drivers/Makefile b/drivers/Makefile
index ee62edb593f1..23444200230b 100644
--- a/drivers/Makefile
+++ b/drivers/Makefile
@@ -58,6 +58,9 @@ obj-y                         += gpu/
 
 obj-$(CONFIG_CONNECTOR)                += connector/
 
+# chenwei hello world
+obj-$(CONFIG_HELLO_CHENWEI)                            += hello/
+
 # i810fb and intelfb depend on char/agp/
 obj-$(CONFIG_FB_I810)           += video/fbdev/i810/
 obj-$(CONFIG_FB_INTEL)          += video/fbdev/intelfb/
```



```
  733  make ARCH=arm menuconfig
  734  make ARCH=arm savedefconfig
  735  cp defconfig arch/arm/configs/rockchip_linux_defconfig
  
  
  cw@SYS3:~/sdk/312x_i/kernel$ git diff
diff --git a/arch/arm/configs/rockchip_linux_defconfig b/arch/arm/configs/rockchip_linux_defconfig
index cabffc153fd5..0e2bb0518e00 100644
--- a/arch/arm/configs/rockchip_linux_defconfig
+++ b/arch/arm/configs/rockchip_linux_defconfig
@@ -475,6 +475,7 @@ CONFIG_PHY_ROCKCHIP_INNO_VIDEO_COMBO_PHY=y
 CONFIG_ANDROID=y
 CONFIG_NVMEM=y
 CONFIG_ROCKCHIP_EFUSE=y
+CONFIG_HELLO_CHENWEI=m
 CONFIG_RK_NAND=y
 CONFIG_ROCKCHIP_SIP=y
 CONFIG_EXT4_FS=y
```

