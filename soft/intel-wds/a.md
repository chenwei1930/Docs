# intel WDS in RockChip 3128 firefly

ÎÄ¼ş±êÊ¶£º 

·¢²¼°æ±¾£ºV1.0.0

ÈÕÆÚ£º2020-02-20

ÎÄ¼şÃÜ¼¶£º¡õ¾øÃÜ   ¡õÃØÃÜ   ¡õÄÚ²¿×ÊÁÏ   ¡ö¹«¿ª

------

**ÃâÔğÉùÃ÷**1

±¾ÎÄµµ°´¡°ÏÖ×´¡±Ìá¹©£¬¸£ÖİÈğĞ¾Î¢µç×Ó¹É·İÓĞÏŞ¹«Ë¾£¨¡°±¾¹«Ë¾¡±£¬ÏÂÍ¬£©²»¶Ô±¾ÎÄµµµÄÈÎºÎ³ÂÊö¡¢ĞÅÏ¢ºÍÄÚÈİµÄ×¼È·ĞÔ¡¢¿É¿¿ĞÔ¡¢ÍêÕûĞÔ¡¢ÊÊÏúĞÔ¡¢ÌØ¶¨Ä¿µÄĞÔºÍ·ÇÇÖÈ¨ĞÔÌá¹©ÈÎºÎÃ÷Ê¾»ò°µÊ¾µÄÉùÃ÷»ò±£Ö¤¡£±¾ÎÄµµ½ö×÷ÎªÊ¹ÓÃÖ¸µ¼µÄ²Î¿¼¡£

ÓÉÓÚ²úÆ·°æ±¾Éı¼¶»òÆäËûÔ­Òò£¬±¾ÎÄµµ½«¿ÉÄÜÔÚÎ´¾­ÈÎºÎÍ¨ÖªµÄÇé¿öÏÂ£¬²»¶¨ÆÚ½øĞĞ¸üĞÂ»òĞŞ¸Ä¡£

ÉÌ±êÉùÃ÷

¡°Rockchip¡±¡¢¡°ÈğĞ¾Î¢¡±¡¢¡°ÈğĞ¾¡±¾ùÎª±¾¹«Ë¾µÄ×¢²áÉÌ±ê£¬¹é±¾¹«Ë¾ËùÓĞ¡£

±¾ÎÄµµ¿ÉÄÜÌá¼°µÄÆäËûËùÓĞ×¢²áÉÌ±ê»òÉÌ±ê£¬ÓÉÆä¸÷×ÔÓµÓĞÕßËùÓĞ¡£

°æÈ¨ËùÓĞ ? 2020 ¸£ÖİÈğĞ¾Î¢µç×Ó¹É·İÓĞÏŞ¹«Ë¾

³¬Ô½ºÏÀíÊ¹ÓÃ·¶³ë£¬·Ç¾­±¾¹«Ë¾ÊéÃæĞí¿É£¬ÈÎºÎµ¥Î»ºÍ¸öÈË²»µÃÉÃ×ÔÕª³­¡¢¸´ÖÆ±¾ÎÄµµÄÚÈİµÄ²¿·Ö»òÈ«²¿£¬²¢²»µÃÒÔÈÎºÎĞÎÊ½´«²¥¡£

¸£ÖİÈğĞ¾Î¢µç×Ó¹É·İÓĞÏŞ¹«Ë¾

Fuzhou Rockchip Electronics Co., Ltd.

µØÖ·£º     ¸£½¨Ê¡¸£ÖİÊĞÍ­ÅÌÂ·Èí¼şÔ°AÇø18ºÅ

ÍøÖ·£º     www.rock-chips.com

¿Í»§·şÎñµç»°£º +86-4007-700-590

¿Í»§·şÎñ´«Õæ£º +86-591-83951833

¿Í»§·şÎñÓÊÏä£º fae@rock-chips.com

------

## **Ç°ÑÔ**

**¸ÅÊö**

±¾ÎÄÖ¼ÔÚ½éÉÜFirefly-RK3128µÄintel WDSÅäÖÃÊ¹ÓÃ

**²úÆ·°æ±¾**

| **Ğ¾Æ¬Ãû³Æ** | **ÄÚºË°æ±¾**     |
| ------------ | ---------------- |
| RK2206       | FreeRTOS V10.0.1 |

**¶ÁÕß¶ÔÏó**

±¾ÎÄµµ£¨±¾Ö¸ÄÏ£©Ö÷ÒªÊÊÓÃÓÚÒÔÏÂ¹¤³ÌÊ¦£º

1. ¼¼ÊõÖ§³Ö¹¤³ÌÊ¦
2. Èí¼ş¿ª·¢¹¤³ÌÊ¦

**ĞŞ¶©¼ÇÂ¼**

| **ÈÕÆÚ**   | **°æ±¾** | **×÷Õß** | **ĞŞ¸ÄËµÃ÷**           |
| ---------- | -------- | --------  | ---------------------- |
| 2020-03-12 | V1.0.0   | Conway | ³õÊ¼°æ±¾               |

## **Ä¿Â¼**

[TOC]

## **1. INTEL WDS ½éÉÜ**

- ¿ª·¢»·¾³

Firefly-RK3128²ÉÓÃCortex-A7¼Ü¹¹ËÄºË1.3GHz´¦ÀíÆ÷£¬¼¯³ÉMali-400MP2 GPU£¬ÓµÓĞÓÅĞãµÄÔËËãÓëÍ¼ĞÎ´¦ÀíÄÜÁ¦£»
°åÔØÇ§Õ×ÒÔÌ«Íø¿Ú¡¢2.4GHz Wi-FiºÍÀ¶ÑÀ4.0£¬Õ¹ÏÖ³ö²»Ë×µÄÍøÂçÀ©Õ¹ºÍ´«ÊäĞÔÄÜ£¬Firefly-RK3128 °åÔØWiFiÎª AP6212¡£

- WDS

WDSÊÇÒ»×é¿â£¬¹©Ï£ÍûÔÚlinuxÉÏ¹¹½¨Wi-FiÏÔÊ¾Ó¦ÓÃ³ÌĞòµÄ¿ª·¢ÈËÔ±Ê¹ÓÃ


## 2 BuildrootÅäÖÃ

1. µÚÒ»²½

```
cw@SYS3:~/sdk/3126i$ source envsetup.sh   Ñ¡Ôñ3128
cw@SYS3:~/sdk/3126i$ make menuconfig      ÅäÖÃ¼ûÏÂÍ¼
```

![image-20200312143607651](intelwds.assets/image-20200312143607651.png)

¸Ã²Ëµ¥ÏÂÃæ£¬Ö»¿ªAP6212A¡£¿ÉÓÃ/ËÑË÷AP6212

![image-20200312144606498](intelwds.assets/image-20200312144606498.png)

¿ÉÓÃ/ËÑË÷udp £¨¼´£©Ñ¡ÉÏ

![image-20200312144741803](intelwds.assets/image-20200312144741803.png)

°´Í¼ÖĞ¿ªÆôgstream£¬ºÍÍ¼ÖĞÒ»ÃşÒ»Ñù¼´¿É£¬ÌØ±ğÊÇBR2_PACKAGE_GST1_PLUGINS_BAD

![image-20200312144940739](intelwds.assets/image-20200312144940739.png)

¿ªÆôintel-wds

![image-20200312145955947](intelwds.assets/image-20200312145955947.png)

Ğ¤ˆËµµÄ£ºIntrospection support(¼´ BR2_PACKAGE_WPA_SUPPLICANT_DBUS_INTROSPECTION)¿ªÆô£¬Õâ¸öÎª½â¾öP2P£¬µ«ÊÇÎŞĞ§£¬ÏÖÔÚÏÈ¿ª×Å°É


2. µÚ¶ş²½±£´æ²¢±àÒë

```
cw@SYS3:~/sdk/3126i$ make savedefconfig
cw@SYS3:~/sdk/3126i$ ./build.sh rootfs £¨»òÕßÖ±½Ómake£¬µÈ¼ÛµÄ£©
cw@SYS3:~/sdk/3126i$ ./mkfirmware.sh  £¨´ò°ü¹Ì¼ş£©
```

3¡¢¼ì²é rockchip_rk3128_defconfig£¬ÉÙµÄ»°¾ÍÊÖ¶¯ĞŞ¸Ä¼ÓÉÏÈ¥

```
cw@SYS3:~/sdk/3126i/buildroot$ git diff
diff --git a/configs/rockchip_rk3128_defconfig b/configs/rockchip_rk3128_defconfig
index 4232fac868..4ea55ad722 100644
--- a/configs/rockchip_rk3128_defconfig
+++ b/configs/rockchip_rk3128_defconfig
@@ -5,7 +5,9 @@
 #include "display.config"
 #include "video_mpp.config"
 #include "video_gst.config"
+#include "video_gst_rtsp.config"
 #include "audio.config"
+#include "audio_gst.config"
 #include "camera.config"
 #include "camera_gst.config"
 #include "test.config"
@@ -18,6 +20,12 @@
 #include "qt_app.config"
 BR2_TARGET_GENERIC_HOSTNAME="rk3128"
 BR2_TARGET_GENERIC_ISSUE="Welcome to RK3128 Buildroot"
-BR2_PACKAGE_BLUEZ5_UTILS=y
-BR2_PACKAGE_RKWIFIBT_RTL8723DS=y
+BR2_PACKAGE_RKWIFIBT_AP6212A1=y
+BR2_PACKAGE_GST1_PLUGINS_GOOD_PLUGIN_DEINTERLACE=y
+BR2_PACKAGE_QT5BASE_CONCURRENT=y
+BR2_PACKAGE_QT5BASE_DBUS=y
 BR2_PACKAGE_SBC=y
+BR2_PACKAGE_INTEL_WDS=y
+BR2_PACKAGE_LIBBSD=y
+BR2_PACKAGE_LIBICAL=y
+BR2_PACKAGE_WPA_SUPPLICANT_DBUS_INTROSPECTION=y
```

4. ¼ì²éÄ£¿éwpa_supplicantÏà¹ØÅäÖÃ£¬ÏÂÃæ3ÏîÅäÖÃ¼ì²éÏÂ£¬ÊÇ¹ØµÄ¾Í¿ªÆğÀ´¡£

```
buildroot/output/rockchip_rk3128/build/wpa_supplicant-2.6/wpa_supplicant$ vim .config 
349 CONFIG_CTRL_IFACE_DBUS_NEW=y  
488 CONFIG_P2P=y
496 CONFIG_WIFI_DISPLAY=y
```

5¡¢ÖØ±àÒëwpa_supplicant

```
cw@SYS3:~/sdk/3126i$ make wpa_supplicant-rebuild
cw@SYS3:~/sdk/3126i$ make »òÕß  £¨./build.sh rootfs Êµ¼Ê¾ÍÊÇÖ´ĞĞ£©
cw@SYS3:~/sdk/3126i$ ./mkfirmware.sh 
```

7¡¢¼ì²éÄ£¿éconnmanÏà¹ØÅäÖÃ£¬ÏÂÃæ3ÏîÅäÖÃ¼ì²éÏÂ£¬ÊÇ¹ØµÄ¾Í¿ªÆğÀ´¡£

```shell
cw@SYS3:~/sdk/3126i/buildroot/output/rockchip_rk3128/build/connman-1.35/gsupplicant$ vim supplicant.c 

diff --git a/gsupplicant/supplicant.c b/gsupplicant/supplicant.c
index f56b595..c7dd5b2 100644cd on
--- a/gsupplicant/supplicant.c
+++ b/gsupplicant/supplicant.c
@@ -5433,7 +5433,7 @@ static void interface_p2p_connect_params(DBusMessageIter *iter, void *user_data)
    supplicant_dbus_dict_open(iter, &dict);

    if (data->peer->master)
\-        go_intent = 15;
\+        go_intent = 7;
```

8¡¢ÖØ±àÒëconnman

```shell
cw@SYS3:~/sdk/3126i$ make connman-rebuild
cw@SYS3:~/sdk/3126i$ make (»òÕß  ./build.sh rootfsÆäÊµ¾ÍÊÇmake)
cw@SYS3:~/sdk/3126i$ ./mkfirmware.sh 
```

## 3 kernelÅäÖÃ

µÚÒ»²½£ºĞŞ¸ÄÉè±¸Ê÷£º

Ó¦ÎªÊ¹ÓÃRKµÄSDK£¬´Ófirefly³­¹ıÀ´µÄÉè±¸Ê÷ÓĞÎÊÌâ¡£ĞèÒªÅäÖÃÒ»¸öwifi 32KµÄÒı½Åµ½PMUÉÏÃæ¡£

```shell
w@SYS3:~/sdk/3126i/kernel$ git show
commit 84de4a74ed2b5ac7d41d1f629f4cea97916c9b81 (HEAD -> 39)
Author: chenwei <wei.chen@rock-chips.com>
Date:   Fri Mar 6 18:20:58 2020 +0800

    ARM: dts: rk3128-fireprime: config pmic clock enable Wi-Fi
    
    Change-Id: Icad7c244fd57c9de3943563440e7eef7aed71fa4
    Signed-off-by: chenwei <wei.chen@rock-chips.com>

diff --git a/arch/arm/boot/dts/rk3128-fireprime.dts b/arch/arm/boot/dts/rk3128-fireprime.dts
index 0f54e87689f2..041dd108c0c9 100644
--- a/arch/arm/boot/dts/rk3128-fireprime.dts
+++ b/arch/arm/boot/dts/rk3128-fireprime.dts
@@ -121,6 +121,8 @@
 
        sdio_pwrseq: sdio-pwrseq{
                compatible = "mmc-pwrseq-simple";
+               clocks = <&rk818 1>;
+               clock-names = "ext_clock";
                pinctrl-name = "default";
                pinctrl-0 = <&wifi_enable_h>;
                reset-gpios = <&gpio1 RK_PB3 GPIO_ACTIVE_LOW>;
@@ -617,8 +619,8 @@
 };
 
 &sdio {
-       clock-frequency = <50000000>;
-       clock-freq-min-max = <200000 50000000>;
+       clock-frequency = <20000000>;
+       clock-freq-min-max = <200000 20000000>;
        supports-sdio;
        disable-wp;
        cap-sd-highspeed;

```

µÚ¶ş²½£º ĞŞ¸Ävop¼Ä´æÆ÷Çı¶¯£¨²¹¶¡ https://10.10.10.29/c/rk/kernel/+/96675£©

```shell
cw@SYS3:~/sdk/3126i/kernel$ git diff
diff --git a/drivers/gpu/drm/rockchip/rockchip_vop_reg.c b/drivers/gpu/drm/rockchip/rockchip_vop_reg.c
index 6fb3c0f63b71..83adba87f35c 100644
--- a/drivers/gpu/drm/rockchip/rockchip_vop_reg.c
+++ b/drivers/gpu/drm/rockchip/rockchip_vop_reg.c
@@ -1439,9 +1439,9 @@ static const struct vop_win_phy rk3126_win1_data = {
 
 static const struct vop_win_data rk3126_vop_win_data[] = {
        { .base = 0x00, .phy = &rk3036_win0_data,
-         .type = DRM_PLANE_TYPE_PRIMARY },
+         .type = DRM_PLANE_TYPE_OVERLAY },
        { .base = 0x00, .phy = &rk3126_win1_data,
-         .type = DRM_PLANE_TYPE_CURSOR },
+         .type = DRM_PLANE_TYPE_PRIMARY },
 };
```

µÚ¶ş²½£º¿ªÆôwifi²¢±àÒë

```
cw@SYS3:~/sdk/312/kernel$make  ARCH=arm rockchip_linux_defconfig  
cw@SYS3:~/sdk/3328/kernel$make menuconfig ARCH=arm

×¢Òâkernel¶ÔÓÚ32Î»£¬make menuconfigºÍmake savedefconfig¶¼±ØĞë¼ÓÉÏARCH=arm£¬
menuconfigÅäÖÃºósaveÔÚ¿½±´µ½arch/arm/configs/rockchip_linux_defconfig¡£ 
²»¼Ó ARCH=armµÄ»°£¬Ä¬ÈÏÊÇ64Î»£¬ÕâÊ±ºò£¬ÕâÊ±ºòÄãgit diffÏÂ·¢ÏÖrockchip_linux_defconfig»áÓĞºÜ´óµÄ¸Ä¶¯¡£¼Ó ARCH=armµÄ»°£¬¾ÍÊÇ32Î»»úÆ÷£¬Äãgit diffÏÂ·¢ÏÖrockchip_linux_defconfig¾ÍÊÇ¸Õ²ÅmenuconfigµÄÄÇĞ©ĞŞ¸Ä¡£Äã¿´ÏÂÏÂÃæÎÄ¼şËÑË÷¾Í»áÃ÷°×
cw@SYS3:~/sdk/3126i/kernel$ ag -g "rockchip_linux_defconfig"
arch/arm/configs/rockchip_linux_defconfig
arch/arm64/configs/rockchip_linux_defconfig
```

![image-20200312165810044](intelwds.assets/image-20200312165810044.png)

```
ÈçÍ¼ËùÊ¾£¬×¢ÒâÊÇbootupÑ¡Ïî£¬ÒâË¼ÊÇ¿ª»úÆô¶¯AP6XXÇı¶¯¼ÓÔØ½øÄÚºË£¬Ö§³Öap6xĞÍºÅWi-Fi

cw@SYS3:~/sdk/3126i/kernel$ make savedefconfig  ARCH=arm          
scripts/kconfig/conf  --savedefconfig=defconfig Kconfig

cw@SYS3:~/sdk/3126i/kernel$ cp defconfig arch/arm/configs/rockchip_linux_defconfig

#Îª
```

## 4 ¿ª·¢°å²Ù×÷

### 4.1 ²âÊÔ³ÌĞò½éÉÜ

±àÒëºóintel-wds×Ô¶¯Éú³Ésink-test£¬²¢¸´ÖÆµ½¿ª·¢°åµÄusr/bin/Ä¿Â¼ÏÂ¡£

 ### 4.2 ²âÊÔ²½Öè

¿ª·¢°åÁ¬½ÓhdmiÏÔÊ¾Æ÷£¬¿ª»úºó¿ªÒ»¸ö´®¿Ú½çÃæÒ»¸öadb½çÃæ

1¡¢ ¿ª·¢°å¿ª»úºókill É±µôÁ½¸ö½ø³Ì£¨Èç¹ûÓĞ£©£º wpa_supplicantºÍweston¡£

```
  542 root     44912 S    weston --tty=2 --idle-time=0
  656 root      5880 S    wpa_supplicant -B -i wlan0 -c /userdata/cfg/wpa_supp
[root@rk3128:/]# kill Á½¸ö½ø³ÌºÅ
```

2¡¢insmod /bcmdhd.ko£¨insmod system/lib/modules/bcmdhd.ko £©  £¨°´±¾½Ì³Ì£¬²»ĞèÒªÖ´ĞĞ£©

Èç¹ûWI-Fi ¼ÓÔØµ½ÄÚºËµÄ£¬ÕâÒ»²½¡°Ä£¿é¼ÓÔØ¡±µÄ²Ù×÷¾Í²»ĞèÒª £¬²»È»¾Í»á±¨´íÒ»ÏÂ´íÎó£¬Ëµ·ûºÅÖØ¸´
ÈçÏÂ

```
[root@rk3128:/]# insmod /bcmdhd.ko£¨insmod system/lib/modules/bcmdhd.ko £© 
[root@rk3128:/]# insmod system/lib/modules/bcmdhd.ko
[   27.577071] bcmdhd: exports duplicate symbol bcmsdh_cfg_read (owned by kernel)
[   27.738885] bcmdhd: exports duplicate symbol bcmsdh_cfg_read (owned by kernel)
insmod: can't insert 'system/lib/modules/bcmdhd.ko': invalid module format
```

3¡¢wpa_supplicant -u&
4¡¢connmand
5¡¢Ö´ĞĞconnmanctl£¬ÔÚ½»»¥ÃüÁîÒÀ´ÎÖ´ĞĞ

```
enable wifi 
enable p2p
agent on
```

6¡¢ADB½çÃæ

```
[root@rk3128:/usr/bin]# export GST_DEBUG=3
[root@rk3128:/usr/bin]# ./sink-test
Rga built version:version:+2017-09-28 10:12:42 
- Registering Wifi Display with IE 00000600111C440032
Warning: P2P not found in Connman technologies.£¨Õâ¸öÖ»ÓĞµÚÒ»´ÎÉÕÂ¼²Å²»»áÓĞ£¬ÖØÆô¾Í»áÕâÖÖÎÊÌâ)
Received unknown command: 
Received unknown command: 
```

7¡¢ÊÖ»úÉÏ´ò¿ªÎŞÏßÏÔÊ¾£¬¿ÉÒÔËÑµ½ConnManÉè±¸£¬µã»÷Á¬½Ó
 ´®¿Ú½çÃæ£¬»áÏÔÊ¾Ò»¸öÈ·ÈÏÁ¬½ÓµÄyes/no,ËùÒÔÊäÈëyes£¬ÓĞĞ©ÊÖ»ú»¹»áµ¯³öÀ´Ò»¸ö´°¿Ú£¬ÎÊÈ·¶¨Óë·ñ¡£


## 5 ´íÎó

### 5.1 ´íÎó No space left on device (28)

```
18:audioringbuffer_thread_func:<alsasink0> failed to set thread priority
0:00:21.678011844   695  0x16262f0 WARN            kmsallocator gstkmsallocator.c:555:gst_kms_allocator_dmabuf_import:<KMSMemory::allocator> Failed to close GEM handle: Invalid argument 22
0:00:21.679143802   695  0x16262f0 WARN                 kmssink gstkmssink.c:1427:gst_kms_sink_sync:<kmssink0> drmModePageFlip failed: No space left on device (28)
0:00:21.756511302   695 0xb53061b0 ERROR            mppvideodec gstmppvideodec.c:707:gst_mpp_video_dec_handle_frame:<mppvideodec0> can't process this frame
0:00:21.758654469   695  0x1673860 WARN                 basesrc gstbasesrc.c:3055:gst_base_src_loop:<source> error: Internal data stream error.
0:00:21.758767927   695  0x1673860 WARN                 basesrc gstbasesrc.c:3055:gst_base_src_loop:<source> error: streaming stopped, reason error (-5)

```

Õâ¸ö´íÎóNo space left on device (28)ËµÉè±¸Ã»ÄÚ´íÁË£¬½â¾ö¾ÍÊÇµÚÈıÕÂÉÏÃæÄÇ¸ökernelÏÂÃæµÄgpu²¹¶¡£¬ĞèÒªÉèÖÃoverlay

### 5.2 ´íÎóFailed to send Action Frame(retry 6

```shell
ÎÊÌâ£º
[  166.361139] CFG80211-ERROR) wl_cfg80211_send_action_frame : Failed to send Action Frame(retry 6)ÓĞµÄ

½â¾ö:
connman$ git diff .
diff --git a/gsupplicant/supplicant.c b/gsupplicant/supplicant.c
index f56b595..c7dd5b2 100644
--- a/gsupplicant/supplicant.c
+++ b/gsupplicant/supplicant.c
@@ -5433,7 +5433,7 @@ static void interface_p2p_connect_params(DBusMessageIter *iter, void *user_data)
        supplicant_dbus_dict_open(iter, &dict);
 
        if (data->peer->master)
-               go_intent = 15;
+               go_intent = 7;


```

### 5.3 check your GStreamer installation.

```
ÎÊÌâ£º

** (sink-test:1046): [1;33mWARNING[0m **: [gst-core-error-quark] Missing element 'deinterlace' - check your GStreamer installation.

** (sink-test:1046): [1;33mWARNING[0m **: [gst-core-error-quark] Missing element 'deinterlace' - check your GStreamer installation.

½â¾ö buildroot ÏÂÃæ¿ªÆôÕâ¸ö BR2_PACKAGE_GST1_PLUGINS_GOOD_PLUGIN_UDP
```

### 5.3 p2p ±¨´íMethod "SetProperty" with signature "sv" on interface "net.connman.Technology" doesn't exist

P2P±¨´í£¬µÚÒ»´ÎÉÕĞ´rootfs²»»áÓĞÕâ¸öÎÊÌâ£¬¸´Î»ºó¶¼»á³öÏÖÕâ¸öÎÊÌâ¡£cw@SYS3:~/sdk/3126i/buildroot/output/rockchip_rk3128/build/connman-1.35$ cd -

```

P2P±¨´í£¬Õâ¸öÎÊÌâ£¬Ö»Òª¶ÏµçµÚÒ»´Î¿ª»ú¾Í²»»áÓĞ£¬¸´Î»ºó¶¼»á³öÏÖÕâ¸öÎÊÌâ
20200310_16£º20:47Error wifi: Already enabled
20200310_16£º20:50connmanctl> enable p2p
20200310_16£º20:50Error p2p: Method "SetProperty" with signature "sv" on interface "net.connman.Technology" doesn't exist
20200310_16£º20:54connmanctl> agent on
```

## 6 ÒÀÀµºÍ¹¦ÄÜÊµÏÖ

 WDS¼´linuxÉÏwifiÍ¶ÆÁÏÔÊ¾¡£WDS ÒÀÀµºÜÉÙ£¬Ö÷ÒªÊÇGStreamer ºÍGLib¡£µ«ÊÇÎªÁËÈ·WIFI¹¦ÄÜok£¬Äã»¹Òª°²×°wpa_supplicant¡¢connman

Ïà¹Ø´úÂë

?    buildroot/output/rockchip_rk3128/build/intel-wds-ece955a9947e8d5848223c849d2c0f3f928078d4/

- *sink£º*Wi-FiÏÔÊ¾²Û£¬ÒÀÀµgStreer¡¢ConnmanºÍglibÖ÷»ØÂ·¡£
- *source£º*Wi-FiÏÔÊ¾Ô´£¬ÒÀÀµgStreer¡¢ConnmanºÍglibÖ÷Ñ­»·¡£

WDSÈí¼ş¼Ü¹¹

- libwds£ºÖ÷¿âÊµÏÖÁËRTSPµÄWi-FiÏÔÊ¾·½ÑÔ£¬°üÀ¨½âÎöÆ÷¡¢½ÓÊÕÆ÷ºÍÔ´µÄÊµ¼ÊĞ­ÉÌÂß¼­ÒÔ¼°Ïà¹ØµÄÊı¾İ½á¹¹¡£Ëü²»ÓëÈÎºÎÌØ¶¨µÄÁ¬½Ó¹ÜÀíÆ÷¡¢Ã½Ìå¿ò¼Ü»òÖ÷Ñ­»·Ïà¹ØÁª¡£´Ë¿â»¹ÓëMSVC¼æÈİ¡£
- ÍøÂç£ºÖ§³ÖÓëglibÖ÷Ñ­»·ºÍgflowµÄ¼¯³É¡£
- P2P£ºÖ§³ÖÓëConnman Wifi P2P¹¦ÄÜµÄ¼¯³É

È·±£wifiÕı³£Ïà¹Ø

- WIFIÊÊÅäÆ÷Ê¹ÓÃ Intel 7260-family »òAtheros ath9k£¬ÔÚÆäËûµÄÊÊÅäÆ÷¿ÉÄÜÓĞp2pÎÊÌâ

- [wpa_supplicant](http://w1.fi/wpa_supplicant/): °æ±¾2.4ºó£¬¿ªÆô	

  `CONFIG_P2P=y`

  `CONFIG_WIFI_DISPLAY=y` 

   `CONFIG_CTRL_IFACE_DBUS_NEW=y`

- [connman](https://01.org/connman): °æ±¾1.28 ÒÔºó

- gstreamer: either master branch more recent than Feb 3rd 2015 (commit d0a50be2), or 1.4 branch more recent than Feb 3rd 2005 (commit 1ce3260a638d or release 1.4.6 or later).

  


##  7ÆäËû¸ÅÄî

ÒÀÀµ

ConnMan(Connection Manager)ÊÇÒ»¸ö¿ªÔ´ÏîÄ¿£¬ÔÚLinux²Ù×÷ÏµÍ³ÖĞÌá¹©Ò»¸öºóÌ¨½ø³Ì£¬À´¹ÜÀíÍøÂçÁ¬½Ó¡£ConnManÉè¼ÆĞ¡ÇÉ£¬²¢ÇÒ¾¡¿ÉÄÜµÄ¼õĞ¡×ÊÔ´ÏûºÄ£¬Òò´ËËûÄÜºÜÈİÒ×µÄ¼¯³É½øÆäËûÆ½Ì¨¡£



## 8 Í¬Àà¾ºÆ·

 µçÊÓ¹û3³ıÁËÖ§³ÖAirPlay¡¢DLNA»¹Ö§³Öwin10ÏµÍ³µÄÎŞÏßÏÔÊ¾¹¦ÄÜ 



DNLA£¬Digital Living Network Alliance£¬ÊÇË÷Äá¡¢Ó¢ÌØ¶û¡¢Î¢ÈíµÈ·¢ÆğµÄÒ»Ì× PC¡¢ÒÆ¶¯Éè±¸¡¢Ïû·ÑµçÆ÷Ö®¼ä»¥Áª»¥Í¨µÄĞ­Òé¡£ 

DLNAÓëÆ»¹ûµÄAirPlay¹¦ÄÜ±È½ÏÀàËÆ£¬Ğ­ÒéÒ²´óÌåÏàÍ¬£¬ËûÃÇ¶¼¿ÉÒÔÈÃÄãÊÖ»úÖĞµÄÃ½ÌåÄÚÈİÍ¶·Åµ½µçÊÓÆÁÄ»Àï¡£²»Í¬µÄÊÇÊÖ»úÉÏµÄDLNA ²¢Ã»ÓĞÀàËÆApple TVµÄAirPlayµÄ¾µÏñ¹¦ÄÜ£¬Ò²Ã»ÓĞApple TV ËùÖ§³ÖµÄË«ÆÁÌåÓÎÏ·ÌåÑé¡£Ä¿Ç°DLNA¸ü¶àÖ»ÊÇÄÜ½«ÊÖ»úµÄÕÕÆ¬ºÍÊÓÆµÍ¶ËÍµ½´óÆÁÄ»ÖĞ¡£

ÁíÍâ£¬ÔÚÏßÊÓÆµÒ²¿ÉÒÔÓÃDLNAÄ£Ê½ÍÆËÍµ½¿ÍÌüµçÊÓÉÏÏÔÊ¾£¬°²×¿ÏµÍ³²¿·Ö²¥·ÅÆ÷¾Í¾ß±¸DLNA¹¦ÄÜ£¬Ä¿Ç°Ö§³ÖÎŞÏßÍÆËÍµÄÊÓÆµ¿Í»§¶ËÓĞÒÔÏÂ£ºÌÚÑ¶ÊÓÆµ¡¢ËÑºüÊÓÆµ¡¢PPTVÊÓÆµ¡£¿ÉÒÔ½«Ô­À´Ó¦¸ÃÔÚN7ÆÁÄ»µÄÓ°Æ¬×ªÒÆµ½µçÊÓÆÁÄ»ÉÏ¡£Ç°ÌáÊÇÄãÒªÓĞÄÜÖ§³ÖDLNAµÄµçÊÓ»òÕßµçÊÓºĞ¡£

 

Òªµã£ºÒÀÀµºÍ¹¦ÄÜÔÚµÚ6ÕÂ

Ó²¼ş£ºµçÊÓ¹û3µÈ

¹¦ÄÜ £ºÍ¬ÀàÓÃµÄAirPlay¡¢DLNA

¸Ä½ø£º¸Ä½ø¾ÍÊÇgstreamÓÅ»¯°É£¬×ÊÁÏ±È½ÏÉÙ



## 10ÆäËû±Ê¼ÇÀ¬»ø

6¡¢ÊÖ»úÉÏ´ò¿ªÎŞÏßÏÔÊ¾£¬¿ÉÒÔËÑµ½ConnManÉè±¸£¬µã»÷Á¬½Ó
7¡¢Á¬½ÓÊ§°Ü£¬´®¿Ú¿´µ½±¨´í£º
[ 167.877065] CFG80211-ERROR) wl_cfg80211_send_action_frame : Failed to send Acti
on Frame(retry 6)

 

Èç¹ûwifiÊÇ¼ÓÔØ½øÄÚºËºÍÄ£¿é¼ÓÔØµÄ

```
[root@rk3128:/]# cd /system/lib/modules/
[root@rk3128:/system/lib/modules]# ls
bcmdhd.ko
[root@rk3128:/system/lib/modules]# ./bcmdhd.ko 
-/bin/sh: ./bcmdhd.ko: Permission denied
[root@rk3128:/system/lib/modules]# 
[root@rk3128:/system/lib/modules]# chmod 777 bcmdhd.ko 
[root@rk3128:/system/lib/modules]# ./bcmdhd.ko .ko
ÄÚºË¼ÓÔØÄ£¿éÊ±ÌáÊ¾usb_common: exports duplicate symbol of_usb_get_dr_mode
1.·ÖÎö:
¼ÈÈ»·ûºÅÖØ¸´ÁË£¬ÄÇÃ´ËµÃ÷ÓĞÒ»¸ö²¿·Ö¼È±»±àÒëµ½ÄÚºËÖĞÒ²±»±àÒë³ÉÄ£¿éÁË£¬Òò´ËÔÚ¼ÓÔØÄ£¿éÊ±£¬ÄÚºË±¨·ûºÅÖØ¸´µÄÌáÊ¾

2.½â¾ö
Ö±½ÓÅäÖÃÄÚºËµÄÄ³Ò»²¿·Ö±àÒë³ÉÄ£¿é£¬ÀıÈç±ÊÕß¾ÍÖ±½Ó½«USBÕâÒ»²¿·Ö±àÒë³ÉÄ£¿é¼´¿É
```

wifiÏà¹Ø£º

```
psÉ¾³ıwpa
ifconfig   ¿´²»µ½wlan0Éè±¸
echo 1 > sys/class/rfkill/rfkill1/state     
ifconfig wlan0 up
ps
wpa_supplicant -B -i wlan0 -c /data/cfg/wpa_supplicant.conf
wpa_cli -i wlan0 -p /var/run/wpa_supplicant scan
wpa_cli -i wlan0 -p /var/run/wpa_supplicant scan_results
```

```
#TARGET_BOARD=rk3128
#OUTPUT_DIR=output/rockchip_rk3128
#CONFIG=rockchip_rk3128_defconfig
```

```
network={
  ssid="aaabbb"
  psk="a123456789"
  key_mgmt=WPA-PSK
}
```

```
cw@SYS3:~/sdk/3126i/buildroot/output/rockchip_rk3128/build$ cd g
glibc-2.29-11-ge28ad442e73b00ae2047d89c8cc7f9b2a0de5436/ gst1-plugins-base-1.14.4/
glmark2-9b1070fe9c5cf908f323909d3c8cbed08022abe8/        gst1-plugins-good-1.14.4/
glmarktest-0.1/                                          gst1-plugins-ugly-1.14.4/
gst1-plugins-bad-1.14.4/                                 
cw@SYS3:~/sdk/3126i/buildroot/output/rockchip_rk3128/build$ rm -rf  gst1*
```

```
cw@SYS3:~/sdk/3126i/buildroot/output/rockchip_rk3128/build/intel-wds-ece955a9947e8d5848223c849d2c0f3f928078d4/sink$ ls
CMakeFiles           CMakeLists.txt       gst_sink_media_manager.cpp  main.cpp  sink-app.cpp  sink.cpp  sink-test
cmake_install.cmake  CTestTestfile.cmake  gst_sink_media_manager.h    Makefile  sink-app.h    sink.h


adb push D:\ADBplatform-tools\sink-test /
chmod 777 sink-test
./sink-test


```

¹ØÓÚbuildrootÏÂÃæÈçºÎ´ò²¹¶¡

```
cw@SYS3:~/sdk/3126i/buildroot/package/connman ls
0001-tethering-Reorder-header-includes.patch  0002-nat-build-failure.patch  Config.in  connman.hash  connman.mk  S45connman
```

###  ¿ª·¢°åÖ´ĞĞ²âÊÔ³ÌĞò

-  ·½·¨Ò»£º¸´ÖÆµ½¿ª·¢°åtarget¸ùÄ¿Â¼£¬ÖØĞÂÉÕĞ´rootfs

```
cw@SYS3:~/sdk/3126i/buildroot/output/rockchip_rk3128$ ag -g "sink-test"
build/intel-wds-ece955a9947e8d5848223c849d2c0f3f928078d4/sink/sink-test

cw@SYS3:~/sdk/3126i/buildroot/output/rockchip_rk3128$cp build/intel-wds-ece955a9947e8d5848223c849d2c0f3f928078d4/sink/sink-test target/

cw@SYS3:~/sdk/3126i$  make
cw@SYS3:~/sdk/3126i$  ./mkfirmware.sh
```

