# intel WDS in RockChip 3128 firefly

�ļ���ʶ�� 

�����汾��V1.0.0

���ڣ�2020-02-20

�ļ��ܼ���������   ������   ���ڲ�����   ������

------

**��������**1

���ĵ�������״���ṩ��������о΢���ӹɷ����޹�˾��������˾������ͬ�����Ա��ĵ����κγ�������Ϣ�����ݵ�׼ȷ�ԡ��ɿ��ԡ������ԡ������ԡ��ض�Ŀ���Ժͷ���Ȩ���ṩ�κ���ʾ��ʾ��������֤�����ĵ�����Ϊʹ��ָ���Ĳο���

���ڲ�Ʒ�汾����������ԭ�򣬱��ĵ���������δ���κ�֪ͨ������£������ڽ��и��»��޸ġ�

�̱�����

��Rockchip��������о΢��������о����Ϊ����˾��ע���̱꣬�鱾��˾���С�

���ĵ������ἰ����������ע���̱���̱꣬�������ӵ�������С�

��Ȩ���� ? 2020 ������о΢���ӹɷ����޹�˾

��Խ����ʹ�÷��룬�Ǿ�����˾������ɣ��κε�λ�͸��˲�������ժ�������Ʊ��ĵ����ݵĲ��ֻ�ȫ�������������κ���ʽ������

������о΢���ӹɷ����޹�˾

Fuzhou Rockchip Electronics Co., Ltd.

��ַ��     ����ʡ������ͭ��·���԰A��18��

��ַ��     www.rock-chips.com

�ͻ�����绰�� +86-4007-700-590

�ͻ������棺 +86-591-83951833

�ͻ��������䣺 fae@rock-chips.com

------

## **ǰ��**

**����**

����ּ�ڽ���Firefly-RK3128��intel WDS����ʹ��

**��Ʒ�汾**

| **оƬ����** | **�ں˰汾**     |
| ------------ | ---------------- |
| RK2206       | FreeRTOS V10.0.1 |

**���߶���**

���ĵ�����ָ�ϣ���Ҫ���������¹���ʦ��

1. ����֧�ֹ���ʦ
2. �����������ʦ

**�޶���¼**

| **����**   | **�汾** | **����** | **�޸�˵��**           |
| ---------- | -------- | --------  | ---------------------- |
| 2020-03-12 | V1.0.0   | Conway | ��ʼ�汾               |

## **Ŀ¼**

[TOC]

## **1. INTEL WDS ����**

- ��������

Firefly-RK3128����Cortex-A7�ܹ��ĺ�1.3GHz������������Mali-400MP2 GPU��ӵ�������������ͼ�δ���������
����ǧ����̫���ڡ�2.4GHz Wi-Fi������4.0��չ�ֳ����׵�������չ�ʹ������ܣ�Firefly-RK3128 ����WiFiΪ AP6212��

- WDS

WDS��һ��⣬��ϣ����linux�Ϲ���Wi-Fi��ʾӦ�ó���Ŀ�����Աʹ��


## 2 Buildroot����

1. ��һ��

```
cw@SYS3:~/sdk/3126i$ source envsetup.sh   ѡ��3128
cw@SYS3:~/sdk/3126i$ make menuconfig      ���ü���ͼ
```

![image-20200312143607651](intelwds.assets/image-20200312143607651.png)

�ò˵����棬ֻ��AP6212A������/����AP6212

![image-20200312144606498](intelwds.assets/image-20200312144606498.png)

����/����udp ������ѡ��

![image-20200312144741803](intelwds.assets/image-20200312144741803.png)

��ͼ�п���gstream����ͼ��һ��һ�����ɣ��ر���BR2_PACKAGE_GST1_PLUGINS_BAD

![image-20200312144940739](intelwds.assets/image-20200312144940739.png)

����intel-wds

![image-20200312145955947](intelwds.assets/image-20200312145955947.png)

Ф��˵�ģ�Introspection support(�� BR2_PACKAGE_WPA_SUPPLICANT_DBUS_INTROSPECTION)���������Ϊ���P2P��������Ч�������ȿ��Ű�


2. �ڶ������沢����

```
cw@SYS3:~/sdk/3126i$ make savedefconfig
cw@SYS3:~/sdk/3126i$ ./build.sh rootfs ������ֱ��make���ȼ۵ģ�
cw@SYS3:~/sdk/3126i$ ./mkfirmware.sh  ������̼���
```

3����� rockchip_rk3128_defconfig���ٵĻ����ֶ��޸ļ���ȥ

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

4. ���ģ��wpa_supplicant������ã�����3�����ü���£��ǹصľͿ�������

```
buildroot/output/rockchip_rk3128/build/wpa_supplicant-2.6/wpa_supplicant$ vim .config 
349 CONFIG_CTRL_IFACE_DBUS_NEW=y  
488 CONFIG_P2P=y
496 CONFIG_WIFI_DISPLAY=y
```

5���ر���wpa_supplicant

```
cw@SYS3:~/sdk/3126i$ make wpa_supplicant-rebuild
cw@SYS3:~/sdk/3126i$ make ����  ��./build.sh rootfs ʵ�ʾ���ִ�У�
cw@SYS3:~/sdk/3126i$ ./mkfirmware.sh 
```

7�����ģ��connman������ã�����3�����ü���£��ǹصľͿ�������

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

8���ر���connman

```shell
cw@SYS3:~/sdk/3126i$ make connman-rebuild
cw@SYS3:~/sdk/3126i$ make (����  ./build.sh rootfs��ʵ����make)
cw@SYS3:~/sdk/3126i$ ./mkfirmware.sh 
```

## 3 kernel����

��һ�����޸��豸����

ӦΪʹ��RK��SDK����firefly���������豸�������⡣��Ҫ����һ��wifi 32K�����ŵ�PMU���档

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

�ڶ����� �޸�vop�Ĵ������������� https://10.10.10.29/c/rk/kernel/+/96675��

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

�ڶ���������wifi������

```
cw@SYS3:~/sdk/312/kernel$make  ARCH=arm rockchip_linux_defconfig  
cw@SYS3:~/sdk/3328/kernel$make menuconfig ARCH=arm

ע��kernel����32λ��make menuconfig��make savedefconfig���������ARCH=arm��
menuconfig���ú�save�ڿ�����arch/arm/configs/rockchip_linux_defconfig�� 
���� ARCH=arm�Ļ���Ĭ����64λ����ʱ����ʱ����git diff�·���rockchip_linux_defconfig���кܴ�ĸĶ����� ARCH=arm�Ļ�������32λ��������git diff�·���rockchip_linux_defconfig���Ǹղ�menuconfig����Щ�޸ġ��㿴�������ļ������ͻ�����
cw@SYS3:~/sdk/3126i/kernel$ ag -g "rockchip_linux_defconfig"
arch/arm/configs/rockchip_linux_defconfig
arch/arm64/configs/rockchip_linux_defconfig
```

![image-20200312165810044](intelwds.assets/image-20200312165810044.png)

```
��ͼ��ʾ��ע����bootupѡ���˼�ǿ�������AP6XX�������ؽ��ںˣ�֧��ap6x�ͺ�Wi-Fi

cw@SYS3:~/sdk/3126i/kernel$ make savedefconfig  ARCH=arm          
scripts/kconfig/conf  --savedefconfig=defconfig Kconfig

cw@SYS3:~/sdk/3126i/kernel$ cp defconfig arch/arm/configs/rockchip_linux_defconfig

#Ϊ
```

## 4 ���������

### 4.1 ���Գ������

�����intel-wds�Զ�����sink-test�������Ƶ��������usr/bin/Ŀ¼�¡�

 ### 4.2 ���Բ���

����������hdmi��ʾ����������һ�����ڽ���һ��adb����

1�� �����忪����kill ɱ���������̣�����У��� wpa_supplicant��weston��

```
  542 root     44912 S    weston --tty=2 --idle-time=0
  656 root      5880 S    wpa_supplicant -B -i wlan0 -c /userdata/cfg/wpa_supp
[root@rk3128:/]# kill �������̺�
```

2��insmod /bcmdhd.ko��insmod system/lib/modules/bcmdhd.ko ��  �������̳̣�����Ҫִ�У�

���WI-Fi ���ص��ں˵ģ���һ����ģ����ء��Ĳ����Ͳ���Ҫ ����Ȼ�ͻᱨ��һ�´���˵�����ظ�
����

```
[root@rk3128:/]# insmod /bcmdhd.ko��insmod system/lib/modules/bcmdhd.ko �� 
[root@rk3128:/]# insmod system/lib/modules/bcmdhd.ko
[   27.577071] bcmdhd: exports duplicate symbol bcmsdh_cfg_read (owned by kernel)
[   27.738885] bcmdhd: exports duplicate symbol bcmsdh_cfg_read (owned by kernel)
insmod: can't insert 'system/lib/modules/bcmdhd.ko': invalid module format
```

3��wpa_supplicant -u&
4��connmand
5��ִ��connmanctl���ڽ�����������ִ��

```
enable wifi 
enable p2p
agent on
```

6��ADB����

```
[root@rk3128:/usr/bin]# export GST_DEBUG=3
[root@rk3128:/usr/bin]# ./sink-test
Rga built version:version:+2017-09-28 10:12:42 
- Registering Wifi Display with IE 00000600111C440032
Warning: P2P not found in Connman technologies.�����ֻ�е�һ����¼�Ų����У������ͻ���������)
Received unknown command: 
Received unknown command: 
```

7���ֻ��ϴ�������ʾ�������ѵ�ConnMan�豸���������
 ���ڽ��棬����ʾһ��ȷ�����ӵ�yes/no,��������yes����Щ�ֻ����ᵯ����һ�����ڣ���ȷ�����


## 5 ����

### 5.1 ���� No space left on device (28)

```
18:audioringbuffer_thread_func:<alsasink0> failed to set thread priority
0:00:21.678011844   695  0x16262f0 WARN            kmsallocator gstkmsallocator.c:555:gst_kms_allocator_dmabuf_import:<KMSMemory::allocator> Failed to close GEM handle: Invalid argument 22
0:00:21.679143802   695  0x16262f0 WARN                 kmssink gstkmssink.c:1427:gst_kms_sink_sync:<kmssink0> drmModePageFlip failed: No space left on device (28)
0:00:21.756511302   695 0xb53061b0 ERROR            mppvideodec gstmppvideodec.c:707:gst_mpp_video_dec_handle_frame:<mppvideodec0> can't process this frame
0:00:21.758654469   695  0x1673860 WARN                 basesrc gstbasesrc.c:3055:gst_base_src_loop:<source> error: Internal data stream error.
0:00:21.758767927   695  0x1673860 WARN                 basesrc gstbasesrc.c:3055:gst_base_src_loop:<source> error: streaming stopped, reason error (-5)

```

�������No space left on device (28)˵�豸û�ڴ��ˣ�������ǵ����������Ǹ�kernel�����gpu��������Ҫ����overlay

### 5.2 ����Failed to send Action Frame(retry 6

```shell
���⣺
[  166.361139] CFG80211-ERROR) wl_cfg80211_send_action_frame : Failed to send Action Frame(retry 6)�е�

���:
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
���⣺

** (sink-test:1046): [1;33mWARNING[0m **: [gst-core-error-quark] Missing element 'deinterlace' - check your GStreamer installation.

** (sink-test:1046): [1;33mWARNING[0m **: [gst-core-error-quark] Missing element 'deinterlace' - check your GStreamer installation.

��� buildroot ���濪����� BR2_PACKAGE_GST1_PLUGINS_GOOD_PLUGIN_UDP
```

### 5.3 p2p ����Method "SetProperty" with signature "sv" on interface "net.connman.Technology" doesn't exist

P2P������һ����дrootfs������������⣬��λ�󶼻����������⡣cw@SYS3:~/sdk/3126i/buildroot/output/rockchip_rk3128/build/connman-1.35$ cd -

```

P2P����������⣬ֻҪ�ϵ��һ�ο����Ͳ����У���λ�󶼻�����������
20200310_16��20:47Error wifi: Already enabled
20200310_16��20:50connmanctl> enable p2p
20200310_16��20:50Error p2p: Method "SetProperty" with signature "sv" on interface "net.connman.Technology" doesn't exist
20200310_16��20:54connmanctl> agent on
```

## 6 �����͹���ʵ��

 WDS��linux��wifiͶ����ʾ��WDS �������٣���Ҫ��GStreamer ��GLib������Ϊ��ȷWIFI����ok���㻹Ҫ��װwpa_supplicant��connman

��ش���

?    buildroot/output/rockchip_rk3128/build/intel-wds-ece955a9947e8d5848223c849d2c0f3f928078d4/

- *sink��*Wi-Fi��ʾ�ۣ�����gStreer��Connman��glib����·��
- *source��*Wi-Fi��ʾԴ������gStreer��Connman��glib��ѭ����

WDS����ܹ�

- libwds������ʵ����RTSP��Wi-Fi��ʾ���ԣ���������������������Դ��ʵ��Э���߼��Լ���ص����ݽṹ���������κ��ض������ӹ�������ý���ܻ���ѭ����������˿⻹��MSVC���ݡ�
- ���磺֧����glib��ѭ����gflow�ļ��ɡ�
- P2P��֧����Connman Wifi P2P���ܵļ���

ȷ��wifi�������

- WIFI������ʹ�� Intel 7260-family ��Atheros ath9k����������������������p2p����

- [wpa_supplicant](http://w1.fi/wpa_supplicant/): �汾2.4�󣬿���	

  `CONFIG_P2P=y`

  `CONFIG_WIFI_DISPLAY=y` 

   `CONFIG_CTRL_IFACE_DBUS_NEW=y`

- [connman](https://01.org/connman): �汾1.28 �Ժ�

- gstreamer: either master branch more recent than Feb 3rd 2015 (commit d0a50be2), or 1.4 branch more recent than Feb 3rd 2005 (commit 1ce3260a638d or release 1.4.6 or later).

  


##  7��������

����

ConnMan(Connection Manager)��һ����Դ��Ŀ����Linux����ϵͳ���ṩһ����̨���̣��������������ӡ�ConnMan���С�ɣ����Ҿ����ܵļ�С��Դ���ģ�������ܺ����׵ļ��ɽ�����ƽ̨��



## 8 ͬ�ྺƷ

 ���ӹ�3����֧��AirPlay��DLNA��֧��win10ϵͳ��������ʾ���� 



DNLA��Digital Living Network Alliance�������ᡢӢ�ض���΢��ȷ����һ�� PC���ƶ��豸�����ѵ���֮�以����ͨ��Э�顣 

DLNA��ƻ����AirPlay���ܱȽ����ƣ�Э��Ҳ������ͬ�����Ƕ����������ֻ��е�ý������Ͷ�ŵ�������Ļ���ͬ�����ֻ��ϵ�DLNA ��û������Apple TV��AirPlay�ľ����ܣ�Ҳû��Apple TV ��֧�ֵ�˫������Ϸ���顣ĿǰDLNA����ֻ���ܽ��ֻ�����Ƭ����ƵͶ�͵�����Ļ�С�

���⣬������ƵҲ������DLNAģʽ���͵�������������ʾ����׿ϵͳ���ֲ������;߱�DLNA���ܣ�Ŀǰ֧���������͵���Ƶ�ͻ��������£���Ѷ��Ƶ���Ѻ���Ƶ��PPTV��Ƶ�����Խ�ԭ��Ӧ����N7��Ļ��ӰƬת�Ƶ�������Ļ�ϡ�ǰ������Ҫ����֧��DLNA�ĵ��ӻ��ߵ��ӺС�

 

Ҫ�㣺�����͹����ڵ�6��

Ӳ�������ӹ�3��

���� ��ͬ���õ�AirPlay��DLNA

�Ľ����Ľ�����gstream�Ż��ɣ����ϱȽ���



## 10�����ʼ�����

6���ֻ��ϴ�������ʾ�������ѵ�ConnMan�豸���������
7������ʧ�ܣ����ڿ�������
[ 167.877065] CFG80211-ERROR) wl_cfg80211_send_action_frame : Failed to send Acti
on Frame(retry 6)

 

���wifi�Ǽ��ؽ��ں˺�ģ����ص�

```
[root@rk3128:/]# cd /system/lib/modules/
[root@rk3128:/system/lib/modules]# ls
bcmdhd.ko
[root@rk3128:/system/lib/modules]# ./bcmdhd.ko 
-/bin/sh: ./bcmdhd.ko: Permission denied
[root@rk3128:/system/lib/modules]# 
[root@rk3128:/system/lib/modules]# chmod 777 bcmdhd.ko 
[root@rk3128:/system/lib/modules]# ./bcmdhd.ko .ko
�ں˼���ģ��ʱ��ʾusb_common: exports duplicate symbol of_usb_get_dr_mode
1.����:
��Ȼ�����ظ��ˣ���ô˵����һ�����ּȱ����뵽�ں���Ҳ�������ģ���ˣ�����ڼ���ģ��ʱ���ں˱������ظ�����ʾ

2.���
ֱ�������ں˵�ĳһ���ֱ����ģ�飬������߾�ֱ�ӽ�USB��һ���ֱ����ģ�鼴��
```

wifi��أ�

```
psɾ��wpa
ifconfig   ������wlan0�豸
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

����buildroot������δ򲹶�

```
cw@SYS3:~/sdk/3126i/buildroot/package/connman ls
0001-tethering-Reorder-header-includes.patch  0002-nat-build-failure.patch  Config.in  connman.hash  connman.mk  S45connman
```

###  ������ִ�в��Գ���

-  ����һ�����Ƶ�������target��Ŀ¼��������дrootfs

```
cw@SYS3:~/sdk/3126i/buildroot/output/rockchip_rk3128$ ag -g "sink-test"
build/intel-wds-ece955a9947e8d5848223c849d2c0f3f928078d4/sink/sink-test

cw@SYS3:~/sdk/3126i/buildroot/output/rockchip_rk3128$cp build/intel-wds-ece955a9947e8d5848223c849d2c0f3f928078d4/sink/sink-test target/

cw@SYS3:~/sdk/3126i$  make
cw@SYS3:~/sdk/3126i$  ./mkfirmware.sh
```

