*** RGA** ：二维图像辅助计算单元
    这又是Rockchip的神器之一，该单元可以在极短时间内拷贝、旋转、格式转换、缩放、混合 图片，想想在PC上被CPU memcpy支配的恐惧，再回头看看能在短短几个毫秒内内存拷贝一张4K图片，还能同时缩放、旋转、把YUV转成RGB之类的格式转换，用上后就再也不会想用CPU来做这些工作了。
    不论你是Android还是Linux开发者，想用RGA的话都需要使用我们的rga lib来调用，而安卓因为hwc单元一直在使用该器件进行转屏混合工作，所以在安卓上使用rga效率远远低于linux平台。（安卓的librga位置在hardware/rockchip/librga目录里）

​	RGA优点：
\1. 不占用CPU资源
\2. DRM/CMA物理内存拷贝和转换速度很快

RGA缺点：
\1. 对malloc的不连续的虚拟内存操作速度很慢

```
RGA的设备节点挂在：
/dev/rga


RGA库安装,如果大家不想了解更底层的细节，可以直接安装这个库来使用，我们已经帮大家封装了常用的操作和细节：
sudo dnf install librockchip_rga-devel

如果想进一步了解RGA，可以研究下Android的Hardware/rockchip/librga来查看寄存器的配置等更详细的调用方式，或者使用该代码调用RGA。
本教学的下载地址：
https://github.com/Jerzha/samples-toybrick-rga.git
```



```
[root@RV1126_RV1109:/tmp]# rgaImDemo --querystring
-/bin/sh: rgaImDemo: not found
[root@RV1126_RV1109:/tmp]# ./im2d  rgaImDemo --querystring
./im2d: /lib/libstdc++.so.6: no version information available (required by librga.so)
./im2d: /lib/libstdc++.so.6: no version information available (required by librga.so)
./im2d: /lib/libstdc++.so.6: no version information available (required by librga.so)
./im2d: /lib/libstdc++.so.6: no version information available (required by librga.so)
./im2d: /lib/libstdc++.so.6: no version information available (required by librga.so)
./im2d: /lib/libstdc++.so.6: no version information available (required by librga.so)
./im2d: /lib/libstdc++.so.6: no version information available (required by librga.so)
librga:RGA_GET_VERSION:3.02,3.020000
ctx=0x28070,ctx->rgaFd=3
Rga built version:version:1.00Start selecting mode
im2d querystring ..

RGA vendor            : Rockchip Electronics Co.,Ltd.
RGA version           : RGA_2_Enhance
Max input             : 8192x8192
Max output            : 4096x4096
Scale limit           : 0.0625 ~ 16
Input support format  : RGBA_8888 RGBA_4444 RGBA_5551 RGB_565 RGB_888 YUV420/YUV422 YUV420_10bit/YUV422_10bit 
output support format : RGBA_8888 RGBA_4444 RGBA_5551 RGB_565 RGB_888 YUV420/YUV422 YUYV 
```

