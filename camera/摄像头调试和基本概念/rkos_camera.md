
DVP，一种并行接口，即 Digital Video Port

 

## IIC

速率一般100k是标准的。



IIC 一共两根线，一根数据线和一根时钟线clk。IIC是半双工，IIC走线最长40CM。

- IIC Slave address 设备地址

一般是7位也有10位的。7位的情况下，比如0x30，这时候低位补0是写，低位补1是读。

- IIC 确认读写的寄存器地址位数。

如果是16位寄存器地址，要检查主机需要先发高8位，然后再发8位，或者反过来。

- 调试方法

检查配置后，确认无误后，还是读取摄像头ID失败了。

由于软件上第一步就是读取摄像头ID，读取失败了就终止运行了


## camera

rk_camera_init(1. 开启或创建设备链中i2c_0设备设置速率100，，2.设置vicap的clock输出提供给摄像头，又称为mclk，工作时钟可能导致异常，3 读取摄像头的设备ID,一般iic报错出在这里 )

rk_device_vicap_init
(general register file 通用寄存器文件 rk_vicapdev结构体绑定寄存器配置和中断，设置工作模式CONFIG_VICAP_WORKMODE_BLOCK_PINGPONG，
注册rk_vicap_register即创建vicacp设备、初始化引脚的iomux)

process_parse_option_parameters

## vicap

- vicap 通过DVP接口接收数据，并且通过AXI总线将数据存到系统内存里面。
  1、AHB从机配置寄存器
  2、AHB主机传输芯片内存
  3、传输输入图像数据为一个请求的格式
  4、可选择裁小图像
  5、DMA控制AXI主机

656输出的是串行数据，行场同步信号嵌入在数据流中；
601是并行数据，行场同步有单独输出;
656只是数据传输接口而已，可以说是作为601的一个传输方式。

帧低有效和高有效： 有效时候传递行信号
行低有效和高有效:  有效时候传递一行里面的有效数据信号
Y在先： 行有效的时候，一随着clk YUYVYUYV
UV在先： 行有效的时候，一随着clk UYVY

### 工作模式

有3种工作模式，实际驱动一般使用下面这种

- Block Ping-Pong mode阻塞乒乓模式

block的行数由BLOCK_LINE_NUM配置。逐一接收从块0到块1，0和1块接收后BLOCK_STATUS 置1， 用户应及时清0。如果没有清0，当前帧的剩余部分就会被丢弃。

- 在此模式下，VICAP将以块为单位工作。块的行数由BLOCK_LINE_NUM依次接收.  Block0和block1的配置。什么时候？

块0/1完成后，将设置块状态，用户应清除及时阻止状态。当下一个block0/1开始接收时，如果块状态0/1未清除，当前帧的其余部分将被丢弃。保管部YUV模式和RAW模式的区别在于YUV模式或CCIR656模式，数据将存储在Y数据缓冲区和UV数据缓冲区中，如果唯一的Y模式是所选的紫外线数据将不存储；在原始或JPEG模式下，RGB数据将存储在同一缓冲区中。另外，在YUV模式或RAW8模式下，Y，U的宽度或者V数据是内存中的一个字节；在Raw10/12或JPEG模式下，宽度是半字。作物参数START_Y和START_X定义裁剪起点的坐标。以及裁剪后的帧大小遵循“设置宽度”和“设置高度”的值。

>  如下是block 模式下日志：
>
> block0/1 接收yuv模式

```shell
[A.VICAP][000027.058036][  28C][VICAP]:(rk_vicap_hw_init_yuv_addr) enter 
[A.VICAP][000027.064253][  29C][VICAP]:(rk_vicap_hw_init_yuv_addr) init block pingpong yuv addr
[A.VICAP][000027.071314][  28C][VICAP]:(rk_vicap_hw_init_block_yuv_addr) enter 
[A.VICAP][000027.078869][  28C][VICAP]:init block0 buf[0],y:0x38210400,uv:0x3821cc00
[A.VICAP][000027.086430][  29C][VICAP]:init block1 buf[1],y:0x38223080,uv:0x3822f880
[A.VICAP][000027.093738][  29C][VICAP]:(rk_vicap_hw_init_block_yuv_addr) exit 
[A.VICAP][000027.100376][  29C][VICAP]:(rk_vicap_hw_init_yuv_addr) exit 
[A.VICAP][000027.107177][  28C][VICAP]:(rk_vicap_hw_set_format) enter 
```

初始化block0接收的行数

```
static inline void rk_vicap_hw_init_block_line_num(struct rk_vicap_dev *dev,
                                                   uint32_t num)
{
    MACRO_ASSERT(dev != RK_NULL);

    HAL_VICAP_SetBlockLineNum(dev->vicap_reg, num);
}
```

初始化帧的yuv地址

```
static ret_err_t rk_vicap_hw_init_frame_yuv_addr(struct rk_vicap_dev *dev)
{
    uint32_t i, y_addr, uv_addr, width, height, bpp, y_len, num;
    struct vicap_video_buf *buf;
    struct vicap_output_info *output_info;
    const struct vicap_output_fmt *fmt;
    ret_err_t ret = RET_SYS_EOK;

    rk_vicap_function_enter();

    output_info = &dev->output;
    fmt = output_info->output_fmt;

    if (output_info->is_crop)
    {
        width = output_info->crop.width;
        height = output_info->crop.height;
    }
    else
    {
        width = output_info->pix_format.width;
        height = output_info->pix_format.height;
    }

    bpp = rk_vicap_align_bits_per_pixel(output_info->output_fmt, 0);
    y_len = width * height * bpp / VICAP_YUV_STORED_BIT_WIDTH;
```





存储：yuv模式 数据y和数据uv是单独存放。 如果only y 模式，uv就会被丢弃。
在raw和jpeg，rga模式数据就会存储在同一个buff。

在yuv、raw8模式，y、u、v数据是一个字节宽度
在raw10、raw12模式、jpeg模式，数据宽度就是半字16位。



VIP_DVP_CTRL 控制工作模式，使能位
VIP_DVP_INTEN 各个中断。包括block0和block1结束中断。帧结束中断。

### 传输格式

Mbus-fmt，全称是 Media Bus Pixel Codes，它描述的是用于在物理总线上传输的格式.

下表列出本文中常用到的几种 Mbus-fmt。
名称 类型 Bpp Bus width Sampes per Pixel
MEDIA_BUS_FMT_SBGGR10_1X10 Bayer Raw 10 10 1
MEDIA_BUS_FMT_SGBRG10_1X10 Bayer Raw 10 10 1
MEDIA_BUS_FMT_YUYV8_2X8 YUV:422 16 8 2
MEDIA_BUS_FMT_YUYV8_1_5X8 YUV:420 12 8 1.5

### 图像格式

FourCC，全称 Four Character Codes，它用 4 个字符（即 32bit）来命名图像格式。

在 Linux Kernel 中，它是一个宏，定义如下：

```
#define v4l2_fourcc(a,b,c,d) \
(((__u32)(a)<<0)|((__u32)(b)<<8)|((__u32)(c)<<16)|((__u32)(d)<<24))
```

图像格式 FourCC
V4L2_PIX_FMT_NV12 NV12
V4L2_PIX_FMT_NV21 NV21
V4L2_PIX_FMT_NV16 NV16
V4L2_PIX_FMT_NV61 NV61
V4L2_PIX_FMT_NV12M NM12
V4L2_PIX_FMT_YUYV YUYV
V4L2_PIX_FMT_YUV420 YU12
V4L2_PIX_FMT_SBGGR10 BG10
V4L2_PIX_FMT_SGBRG10 GB10
V4L2_PIX_FMT_SGRBG10 BA10
V4L2_PIX_FMT_SRGGB10 RG10



相关日志:

其实就是通过配置的图像格式， rk_vicap_init  初始化时候看g_input_fmts 数组 表内是否包含这个图像格式，有的话，就调出该图像格式对应的信息。没有的话，就报不支持""Warning: the input format is not found"。

```
RK2206>[INFO]: BF20A6: Info: BF20A6 detected successfully !!!  chip id:0x20a6 
[A.VICAP][000025.957888][  29C][DBG]: BF20A6:(rt_bf20a6_detect_sensor) exit 
[A.VICAP][000025.964021][  29C][DBG]: BF20A6:(rk_bf20a6_init) exit 
[A.VICAP][000025.970660][  28C][DBG]: BF20A6:(rk_bf20a6_control) exit 
[A.VICAP][000025.976536][  29C][vicap_0]:init subdev succe!
[A.VICAP][000025.979848][  29C][VICAP]:(rk_vicap_init_subdev) exit 
[A.VICAP][000025.987876][  29C][vicap_0]:len of input fmts:20
[A.VICAP][000025.990974][  29C][vicap_0]:input pixelcode:0x2001,mbus_code:0x2008
[A.VICAP][000025.999993][  29C][vicap_0]:input pixelcode:0x2001,mbus_code:0x2009
[A.VICAP][000026.006960][  28C][vicap_0]:input pixelcode:0x2001,mbus_code:0x2006
[A.VICAP][000026.013927][  29C][vicap_0]:input pixelcode:0x2001,mbus_code:0x2007
[A.VICAP][000026.020913][  29C][vicap_0]:input pixelcode:0x2001,mbus_code:0x3001
[A.VICAP][000026.027885][  29C][vicap_0]:input pixelcode:0x2001,mbus_code:0x3013
[A.VICAP][000026.034852][  27C][vicap_0]:input pixelcode:0x2001,mbus_code:0x3002
[A.VICAP][000026.041818][  28C][vicap_0]:input pixelcode:0x2001,mbus_code:0x3014
[A.VICAP][000026.048780][  28C][vicap_0]:input pixelcode:0x2001,mbus_code:0x3007
[A.VICAP][000026.055749][  29C][vicap_0]:input pixelcode:0x2001,mbus_code:0x300e
[A.VICAP][000026.062724][  28C][vicap_0]:input pixelcode:0x2001,mbus_code:0x300a
[A.VICAP][000026.069693][  29C][vicap_0]:input pixelcode:0x2001,mbus_code:0x300f
[A.VICAP][000026.076665][  28C][vicap_0]:input pixelcode:0x2001,mbus_code:0x3008
[A.VICAP][000026.083640][  28C][vicap_0]:input pixelcode:0x2001,mbus_code:0x3010
[A.VICAP][000026.090612][  28C][vicap_0]:input pixelcode:0x2001,mbus_code:0x3011
[A.VICAP][000026.097581][  28C][vicap_0]:input pixelcode:0x2001,mbus_code:0x3012
[A.VICAP][000026.104553][  29C][vicap_0]:input pixelcode:0x2001,mbus_code:0x100a
[A.VICAP][000026.111528][  29C][vicap_0]:input pixelcode:0x2001,mbus_code:0x2001
[A.VICAP][000026.118497][  28C][vicap_0]:the input format is:0x2001
[A.VICAP][000026.121823][  29C][VICAP]:(rk_vicap_init) exit 
```



1、如果sensor物理上输出的是UYVY，那sensor驱动里，pixelcode是要配置成raw8的mediabus，vicap，也要设置成raw8的mediabus；
2、如果sensor物理上输出的是NV12/NV16，那sensor驱动里，pixelcode要配置成对应的yuv的mediabus，vicap也是；
3、如果sensor物理上输出的是raw8，那sensor驱动和vicap也是要对应的raw8的mediabus



### 配置

1、如果sensor物理上输出的是UYVY，那sensor驱动里，pixelcode是要配置成raw8的mediabus，vicap，也要设置成raw8的mediabus；
2、如果sensor物理上输出的是NV12/NV16，那sensor驱动里，pixelcode要配置成对应的yuv的mediabus，vicap也是；
3、如果sensor物理上输出的是raw8，那sensor驱动和vicap也是要对应的raw8的mediabus

黄江龙 17:30:05
所以 你现在要采集UYVY的话，sensor和vicap命令都要设置为raw8的mediabus；
sensor驱动的media pixelcode运行起来，vicap是不会去改的，

檵 17:34:12
现在摄像头物理是YUYV，我senseor驱动 BGGR/GBRG8/GRBG/RGGB任一中，如MEDIA_BUS_FMT_SBGGR8_1X8;   然后vicap命令必须对应是 --set-format=fourcc=BGGR



### 图像格式与内存大小

```
 
FourCC，全称 Four Character Codes，它用 4 个字符（即 32bit）来命名图像格式


采样格式          单像素所占内存大小        存放的码流

 YCbCr 4:4:4            3  byte              Y0 U0 V0 Y1 U1 V1 Y2 U2 V2 Y3 U3 V3（4像素为例）

 YCbCr 4:2:2            2  byte              Y0 U0 Y1 V1 Y2 U2 Y3 V3（4像素为例）

 YCbCr 4:2:0            1.5byte              Y0 U0 Y1 Y2 U2 Y3 Y5 V5 Y6 Y7 V7 Y8（8像素为例）

YCbCr 4:1:1            1.5byte              Y0 U0 Y1 Y2 V2 Y3（4像素为例）

YUV420格式
先Y，后V，中间是U。其中的Y是w * h，U和V是w/2 * (h/2)

如果w = 4，h = 2，则：
yyyy
yyyy
uu
vv
内存则是：yyyyyyyyuuvv
需要占用的内存：w * h * 3 / 2
采样规律是：每个像素点都采样Y，奇数行采样1/2个U，不采样V，偶数行采样1/2个V，不采样U

NV12和NV21属于YUV420格式，是一种two-plane模式，即Y和UV分为两个Plane，但是UV（CbCr）为交错存储，而不是分为三个plane。其提取方式与上一种类似，即Y'00、Y'01、Y'10、Y'11共用Cr00、Cb00
YUV420 planar数据， 以720×488大小图象YUV420 planar为例，
其存储格式是： 共大小为(720×480×3>>1)字节，
分为三个部分:Y,U和V
Y分量：    (720×480)个字节  
U(Cb)分量：(720×480>>2)个字节
V(Cr)分量：(720×480>>2)个字节

1.YUV444占用内存空间 = w * h * 3
2.YUV422占用内存空间 = w * h * 2
3.YUV420占用内存空间 = w * h * 3/2

图片的大小定 义为：w * h，宽高分别为w和h
一、YUV420格式
先Y，后V，中间是U。其中的Y是w * h，U和V是w/2 * (h/2)
如果w = 4，h = 2，则：
yyyy
yyyy
uu
vv
内存则是：yyyyyyyyuuvv
需要占用的内存：w * h * 3 / 2
采样规律是：每个像素点都采样Y，奇数行采样1/2个U，不采样V，偶数行采样1/2个V，不采样U

二、YUV422格式
本格式使用较为广泛
每两个点为一组，共占用4个字节
YUYVYUYV…
对于每一组YUYV，前面一个Y和本组中的UV组成第一个点，第二个Y和本组中的UV组成第二个点
所以，在内存中，宽高分别为w * 2、h。
如果w = 4，h = 2，则：
YUYVYUYV
YUYVYUYV
需要占用的内存：w * h * 2

三、UYUY422格式
本格式和YUYV422一样，只是YUV的位置不一样罢了
每组中YUV的排列顺序为：UYUV
需要占用的内存：w * h * 2
```



### 报错：中断错误

排查思路：检查是什么中断错误，比如说你设置的分辨率和实际输出的分辨率是否一致，这时候就需要在 rk_vicap_sample_block_irq 中加上这个函数。

```
    DBG_INFO("DVP_LAST_LINE = %d DVP_LAST_PIX= %d", READ_REG(dev->vicap_reg->DVP_LAST_LINE), READ_REG(dev->vicap_reg->DVP_LAST_PIX));

```



vicap的错误提示一般是rk_vicap_sample_block_irq函数打印出来的，表明中断错误。 pix_err_en 像素数不匹配设置的高度， line_err_en 帧的行数不匹配， dfifo_of_en  溢出， bus_err_en 总线响应错误等等。





Z:\cw\sdk\8_2206\src\driver\vicap\drv_vicap.c

下面是block工作模式下采样产生的中断。

```
static void rk_vicap_sample_block_irq(struct rk_vicap_dev *dev)
{
    uint32_t intstat;
    struct vicap_video_buf *buf = RK_NULL;

    intstat = HAL_VICAP_GetIrqStatus(dev->vicap_reg);

    if (intstat & VICAP_INT_ERR_MASK)
    {
        VICAP_INFO(dev, "vicap was triggered err,intstat:0x%x\n", intstat);
       VICAP_INFO(dev, "the vicap was triggered err,intstat:0x%x\n", intstat);
       if (intstat & VICAP_DVP_INTSTAT_BLOCK_ERR_MASK)
    
```

 pix_err_en 像素数不匹配设置的高度111s
 line_err_en 帧的行数不匹配
 dfifo_of_en  溢出
 bus_err_en 总线响应错误

 VIP_DVP_FOR  

重点关注VICAP_DVP_INTEN 这个中断错误寄存器各个标志位。

- debug

  开启

```c

[A.VICAP][000116.895076][  29C][VICAP]:(rk_rkos_vicap_control) exit 
[A.VICAP][000116.901140][  29C][VICAP]:(rk_rkos_vicap_control) enter 
[A.VICAP][000116.907082][  29C][VICAP]:(rk_rkos_vicap_control) exit 
[A.VICAP][000116.913138][  30C][VICAP]:(rk_rkos_vicap_control) enter 
[A.VICAP][000116.919095][  29C][vicap_0]:vicap was triggered err,intstat:0xa
[a][000117.042294][vicap_0]:the vicap was triggered err,intstat:0xa
[a][000117.046387][vicap_0]:VICAP_DVP_INTSTAT_PIX_ERR_MASK[vicap_0]:vicap was triggered err,intstat:0x4400
[a][000117.045459][vicap_0]:the vicap was triggered err,intstat:0x4400
[a][000117.041507][vicap_0]:VICAP_DVP_INTSTAT_BLOCK_ERR_MASK[VICAP]:(rk_rkos_vicap_control) exit 
[A.VICAP][000117.059824][  29C][VICAP]:(rk_rkos_vicap_control) enter 
[A.VICAP][000117.067978][  29C][VICAP]:(rk_rkos_vicap_control) exit 
[A.VICAP][000117.074014][  30C][VICAP]:(rk_rkos_vicap_control) enter 
```



重点关注vicap的寄存器

```c
/* VICAP Register Structure Define */
struct VICAP_REG {
    __IO uint32_t DVP_CTRL;                           /* Address Offset: 0x0000 */
    __IO uint32_t DVP_INTEN;                          /* Address Offset: 0x0004 */
    __IO uint32_t DVP_INTSTAT;                        /* Address Offset: 0x0008 */
    __IO uint32_t DVP_FOR;                            /* Address Offset: 0x000C */
    __IO uint32_t DVP_DMA_IDLE_REQ;                   /* Address Offset: 0x0010 */
    __IO uint32_t DVP_FRM0_ADDR_Y;                    /* Address Offset: 0x0014 */
    __IO uint32_t DVP_FRM0_ADDR_UV;                   /* Address Offset: 0x0018 */
    __IO uint32_t DVP_FRM1_ADDR_Y;                    /* Address Offset: 0x001C */
    __IO uint32_t DVP_FRM1_ADDR_UV;                   /* Address Offset: 0x0020 */
    __IO uint32_t DVP_VIR_LINE_WIDTH;                 /* Address Offset: 0x0024 */
    __IO uint32_t DVP_SET_SIZE;                       /* Address Offset: 0x0028 */
    __IO uint32_t DVP_BLOCK_LINE_NUM;                 /* Address Offset: 0x002C */
    __IO uint32_t DVP_BLOCK0_ADDR_Y;                  /* Address Offset: 0x0030 */
    __IO uint32_t DVP_BLOCK0_ADDR_UV;                 /* Address Offset: 0x0034 */
    __IO uint32_t DVP_BLOCK1_ADDR_Y;                  /* Address Offset: 0x0038 */
    __IO uint32_t DVP_BLOCK1_ADDR_UV;                 /* Address Offset: 0x003C */
    __IO uint32_t DVP_BLOCK_STATUS;                   /* Address Offset: 0x0040 */
    __IO uint32_t DVP_CROP;                           /* Address Offset: 0x0044 */
    __IO uint32_t DVP_PATH_SEL;                       /* Address Offset: 0x0048 */
    __IO uint32_t DVP_LINE_INT_NUM;                   /* Address Offset: 0x004C */
    __IO uint32_t DVP_WATER_LINE;                     /* Address Offset: 0x0050 */
    __IO uint32_t DVP_FIFO_ENTRY;                     /* Address Offset: 0x0054 */
         uint32_t RESERVED0058[2];                    /* Address Offset: 0x0058 */
    __I  uint32_t DVP_FRAME_STATUS;                   /* Address Offset: 0x0060 */
    __I  uint32_t DVP_CUR_DST;                        /* Address Offset: 0x0064 */
    __IO uint32_t DVP_LAST_LINE;                      /* Address Offset: 0x0068 */
    __IO uint32_t DVP_LAST_PIX;                       /* Address Offset: 0x006C */
};
```



## 基于地址

```c
Z:\sdk\8_2206\src\bsp\hal\lib\CMSIS\Device\RK2206\Include\rk2206.h

/****************************************************************************************/
/*                                                                                      */
/*                                Module Address Section                                */
/*                                                                                      */
/****************************************************************************************/
/* Memory Base */
#define TIMER0_BASE         0x40000000U /* TIMER0 base address */
#define TIMER1_BASE         0x40000020U /* TIMER1 base address */
#define TIMER2_BASE         0x40000040U /* TIMER2 base address */
#define TIMER3_BASE         0x40000060U /* TIMER3 base address */
#define TIMER4_BASE         0x40000080U /* TIMER4 base address */
#define TIMER5_BASE         0x400000A0U /* TIMER5 base address */
#define WDT0_BASE           0x40010000U /* WDT0 base address */
#define WDT1_BASE           0x40020000U /* WDT1 base address */
#define WDT2_BASE           0x40030000U /* WDT2 base address */
#define I2C0_BASE           0x40040000U /* I2C0 base address */
#define I2C1_BASE           0x40050000U /* I2C1 base address */
#define I2C2_BASE           0x40060000U /* I2C2 base address */
#define UART0_BASE          0x40070000U /* UART0 base address */
#define UART1_BASE          0x40080000U /* UART1 base address */
#define UART2_BASE          0x40090000U /* UART2 base address */
#define PWM0_BASE           0x400A0000U /* PWM0 base address */
#define PWM1_BASE           0x400B0000U /* PWM1 base address */
#define PWM2_BASE           0x400C0000U /* PWM2 base address */
#define SPI0_BASE           0x400D0000U /* SPI0 base address */
#define SPI1_BASE           0x400E0000U /* SPI1 base address */
#define EFUSE_CTL0_BASE     0x400F0000U /* EFUSE_CTL0 base address */
#define MBOX0_BASE          0x40100000U /* MBOX0 base address */
#define MBOX1_BASE          0x40110000U /* MBOX1 base address */
#define SARADC_BASE         0x40120000U /* SARADC base address */
#define INTC_BASE           0x40130000U /* INTC base address */
#define DMA_BASE            0x40200000U /* DMA base address */
#define FSPI0_BASE          0x40210000U /* FSPI0 base address */
#define FSPI1_BASE          0x40220000U /* FSPI1 base address */
#define ICACHE_BASE         0x40230000U /* ICACHE base address */
#define DCACHE_BASE         0x40234000U /* DCACHE base address */
#define VOP_BASE            0x40250000U /* VOP base address */
#define AUDIOPWM_BASE       0x40260000U /* AUDIOPWM base address */
#define HYPERBUS_BASE       0x40300000U /* HYPERBUS base address */
#define PMU_BASE            0x41000000U /* PMU base address */
#define GPIO0_BASE          0x41010000U /* GPIO0 base address */
#define GPIO1_BASE          0x41020000U /* GPIO1 base address */
#define TIMER6_BASE         0x41030000U /* TIMER6 base address */
#define ACDCDIG_BASE        0x41040000U /* ACDCDIG base address */
#define GRF_BASE            0x41050000U /* GRF base address */
#define CRU_BASE            0x41060000U /* CRU base address */
#define PVTM_BASE           0x41080000U /* PVTM base address */
#define TOUCH_SENSOR_BASE   0x41090000U /* TOUCH_SENSOR base address */
#define TSADC_BASE          0x410A0000U /* TSADC base address */
#define I2STDM0_BASE        0x41100000U /* I2STDM0 base address */
#define I2STDM1_BASE        0x41110000U /* I2STDM1 base address */
#define PDM0_BASE           0x41120000U /* PDM0 base address */
#define VAD_BASE            0x41130000U /* VAD base address */
#define LPW_SYSBUS_BASE     0x42000000U /* LPW_SYSBUS base address */
#define LPW_PBUS_BASE       0x42040000U /* LPW_PBUS base address */
#define VICAP_BASE          0x43000000U /* VICAP base address */
#define MMC_BASE            0x43010000U /* MMC base address */
#define CRYPTO_BASE         0x43020000U /* CRYPTO base address */
#define SPI2APB_BASE        0x43080000U /* SPI2APB base address */
```



## 拍照命令

```shell
Z:\cw\sdk\8_2206\src\subsys\shell\shell_vicap.c

file.setpath A:
file.mf cif.out
file.mf cif.jpeg
vicap_test dev_create
vicap_test dev_set --set-dev=vicap_0 --set-workmode=block --set-blocks=6 --set-format=fourcc=NV12,width=640,height=480 --stream-buf=8 --stream-count=1 --stream-mode=photo --skip-count=1
vicap_test dev_streamon
```



```
 static struct parameters options =
{
    .dev_name   = "vicap_0",
    .frame_num  = 1,
    .buf_num    = 6,
    .block_num  = 8,
    .skip_num   = 2,  //            options.skip_buf = options.skip_num * options.block_num;
    .workmode   = VICAP_WORKMODE_BLOCK_PINGPONG,
    .streammode = STREAM_MODE_PHOTO,
    .pixformat  = {
        .pixelformat    = V4L2_PIX_FMT_NV12,
        .width          = 640,
        .height         = 480,
    },
}; 
```



## 补充

- 字宽

  1.字（Word）：在ARM体系结构（32位机）中，字的长度为32位，而在8位/16位处理器体系结构中，字的长度一般为16位。
  2.半字（Half-Word）：在ARM体系结构（32位机）中，半字的长度为16位，与8位/16位处理器体系结构中字的长度一致。
  3.字节（Byte）：在ARM体系结构（32位机）和8位/16位处理器体系结构中，字节的长度均为8位。

- bpp输出图像的像素深度

像素深度（bits per pixel，简称bpp）。一个像素的颜色在计算机中由多少个字节数据来描述。计算机中用二进制位来表示一个像素的数据，用来表示一个像素的数据位越多，则这个像素的颜色值更加丰富、分的更细，颜色深度就更深。一般来说像素深度有这么几种：1位、8位、16位、24位、32位。



## 代码结构


 src\bsp\hal\lib\hal\src\hal_vicap.c   就是hal层对vicap寄存器的读写api

 src\driver\vicap\drv_vicap.c  这个用来条用hal_vicap.c的应用，一般报错触发的中断err也是这里。

而下面的camera 驱动的作用，主要是确认IIC的工作参数，比如iic的从机地址，芯片提供给摄像头的工作时钟mclk，以及复位脚，上电脚的控制，确认sensor ID, 控制上电时序。作用就是rk_camera_init 初始化，硬件初始化，设置捕获的图像格式。

```
cw@cw:~/sdk/8_2206/src/driver/camera$ tree
.
├── camera.c
├── drv_bf20a6.c
├── drv_gc0307.c
├── drv_gc0308.c
├── drv_gc032a.c
├── drv_gc2145.c
├── drv_sc031gs.c
└── Kconfig

```

src\oem\source\wtong\rkos_wraper\rkos_camera.c

```
/* This is one-shot init after bootup, and currently not deinit */
int vicap_device_init()
{
    if (camera_inited)
        return 0;

    if (rk_camera_init() != RET_SYS_EOK) {
        ERR("camera init failed!\n");
        return -1;
    }

    if (rk_device_vicap_init() != RET_SYS_EOK) {
        ERR("create vicap device failed!\n");
        return -1;
    }

    camera_inited = 1;
    return 0;
}
```





