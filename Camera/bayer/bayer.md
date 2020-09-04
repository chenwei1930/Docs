 Bayer图像 （bggr, gbrg, grbg,  rggb)

本次笔记在[https://www.cnblogs.com/qiqibaby/p/5267566.html](https://link.zhihu.com/?target=https%3A//www.cnblogs.com/qiqibaby/p/5267566.html)的基础上整理而来。

**1 图像bayer格式介绍**

bayer格式图片是伊士曼·柯达公司科学家Bryce Bayer发明的，Bryce Bayer所发明的拜耳阵列被广泛运用数字图像。

　　对于彩色图像，需要采集多种最基本的颜色，如rgb三种颜色，最简单的方法就是用滤镜的方法，红色的滤镜透过红色的波长，绿色的滤镜透过绿色的波长，蓝色的滤镜透过蓝色的波长。如果要采集rgb三个基本色，则需要三块滤镜，这样价格昂贵，且不好制造，因为三块滤镜都必须保证每一个像素点都对齐。当用bayer格式的时候，很好的解决了这个问题。bayer 格式图片在一块滤镜上设置的不同的颜色，通过分析人眼对颜色的感知发现，人眼对绿色比较敏感，所以一般bayer格式的图片绿色格式的像素是是r和g像素的和。

另外，Bayer格式是相机内部的原始图片, 一般后缀名为.raw。很多软件都可以查看, 比如PS。我们相机拍照下来存储在存储卡上的.jpeg或其它格式的图片, 都是从.raw格式转化过来的。如下图，为bayer色彩滤波阵列，由1/2的G，1/4的R，1/4的B组成。

![img](resources/v2-e46c2ab7389805eee348c704373f78ee_720w.jpg)



**2 bayer格式图像传感器硬件**

图像传感器的结构如下所示，每一个感光像素之间都有金属隔离层，光纤通过显微镜头，在色彩滤波器过滤之后，投射到相应的漏洞式硅的感光元件上。

![img](resources/v2-982874bdb4792dc764cd4d5dd0b3ee39_720w.jpg)



当Image Sensor往外逐行输出数据时，像素的序列为GRGRGR.../BGBGBG...（顺序RGB）。这样阵列的Sensor设计，使得RGB传感器减少到了全色传感器的1/3，如下所示。

![img](resources/v2-1a232bef3ed6b47c1bf6bbc49df8ab0f_720w.jpg)



**3 bayer格式插值红蓝算法实现**

每一个像素仅仅包括了光谱的一部分，必须通过插值来实现每个像素的RGB值。为了从Bayer格式得到每个像素的RGB格式，我们需要通过插值填补缺失的2个色彩。插值的方法有很多（包括邻域、线性、3*3等），下面介绍其中的一种算法：

R和B通过线性邻域插值，但这有几种不同的分布，如下图所示：（为了讲清楚，图画得有点乱，实际上不止这几种，放在后面讲）

![img](resources/v2-633f14383d096d6d94019e918a1a48f7_720w.jpg)



在（a）和（b）中，中间像素的R跟B值分别取左右邻域（或上下邻域）的平均值。

a：

![img](resources/v2-a50f8f46cd08f30178dc73c251fc8c22_720w.png)



b：

![img](resources/v2-54149cdf6fd11eb0908cbb17858cf633_720w.png)



在（c）和（d）中，中间像素的B或R值取对角邻域的平均值。

c：

![img](resources/v2-04c3bcc55fc5411921327d3cf136403e_720w.png)



d：

![img](resources/v2-86af960cd2773c8446a3e4439c104213_720w.png)



**4 bayer格式插值绿算法实现**

![img](resources/v2-b7c84ed05c8d6d959a47e985cde40f7e_720w.jpg)



由于人眼对绿光反应最敏感，对紫光和红光则反应较弱，因此为了达到更好的画质，需要对G特殊照顾。。经过相关的研究，得出计算中间像素G值的算法：

e：

![img](resources/v2-ee9437dc94a52867755568563196fdd2_720w.jpg)



f：

![img](resources/v2-c98b55432fafc27addfedf123ea025b9_720w.jpg)



为了提速，也可以直接通过取4邻域的均值作为中间像素的G值。

![img](resources/v2-530bc762e65a9acad595e0e82466712c_720w.png)



**5 代码实现**

代码展示：

![img](resources/v2-40c1aa2ef2fbed70c03c2b78b155dd15_720w.jpg)原始图片



![img](resources/v2-f66caced63d57a6c7cb11dbfddc36ef5_720w.jpg)bayer格式图像（灰度图）



![img](resources/v2-2fadb336061dcc498a0f3e9a8d0a4e2c_720w.jpg)bayer格式图像（彩色图）



![img](resources/v2-09f16827be496691336338c134dcfa8c_720w.jpg)重建图像



为什么bayer格式图像会是灰度图，这是因为每一个像素上只有一个传感器，所以只能输出一个对应通道的值，而不像RGB图像有3个通道，整张图像是单通道图像，所以虽然是彩色传感器，但是以图片输出时，还是灰度图的形式。bayer格式彩色图像的处理方式是将对应像素上缺少的两个传感器的值置0，以3通道彩色图像输出。

5.1 模拟bayer格式图像

![img](resources/v2-7c05a3cb5406b3dc7c032dfbae84a7f9_720w.jpg)



根据Bayer的色彩滤波阵列，即GRGRGR.../BGBGBG...，设计模板，如果像素上有某种颜色的传感器，就将像素对应R、G、B（openCV中的顺序是BGR）通道的该颜色置为1，其余两种颜色置为0（比如在这个像素上蓝色传感器，就将对应的B通道的值置为1，其余两个通道置为0）。这样就能得到跟图片颜色尺寸相同的模板，然后将原始图像与模板相乘，就可保留Bayer阵列对应的颜色值（其余值都变成0），最后将所有的颜色值提取出来，就变成了Bayer图像。

5.2 重建图像（代码太长就不单独放了，末尾有完整版代码）

根据上面得到Bayer图像（实质是二维的矩阵），重建彩色图像。首先先要将二维矩阵恢复成Bayer彩色图像，接下来就是对该图像进行插值。除了上面提到的几种情况，还有：

![img](resources/v2-327cde2f15291fb407db7a440c009166_720w.png)



对这种情况，我直接将第二行的值赋给第一行

![img](resources/v2-e3990e855526e4cb238bb1369ee5ea3b_720w.jpg)



类似地，将倒数第二列的数赋值给最后一列。

![img](resources/v2-0bba4d3f84ea39646ce4de7e1052c05b_720w.jpg)



对于G分量，如果顶点处值为0，直接计算两个邻域的平均值。

![img](resources/v2-7ab94306ad6015d540081171150b3647_720w.jpg)



对于边缘缺失值的情况，直接计算三邻域的平均值。

![img](resources/v2-ff5a055e2efdee962ea6b2ca7b78df44_720w.jpg)



对于第二行/列或者倒数第二行/列，如果缺失值，直接计算四邻域的平均值。（更多细节详见代码）

5.3 完整代码

```text
# -*- coding: utf-8 -*-
"""
Created on Sat Jul  6 11:10:12 2019

@author: Carl
"""

import cv2
import numpy as np
def Bayer(img_rgb):
    #模拟bayer格式图像
    mask = np.zeros(img_rgb.shape)
    #Blue channel
    mask[:, :, 0][1::2, ::2] = 1
    #Green channel
    mask[:, :, 1][::2, ::2] = 1
    mask[:, :, 1][1::2, 1::2] = 1
    #Red channel
    mask[:, :, 2][::2, 1::2] = 1
    bayer = mask * img_rgb
    #convert tuple to list
    size = list(img_rgb.shape)
    #remove the the last element
    size.pop()
    #calculate the bayer image
    img = np.zeros(tuple(size))
    for i in range(size[0]):
        for j in range(size[1]):
            img[i][j] = np.max((bayer[:,:,0][i][j], bayer[:,:,1][i][j], bayer[:,:,2][i][j]))
    img = img.astype(np.uint8)
    bayer = bayer.astype(np.uint8)
    return img, bayer 

def Interpolation(img):
    mask1 = np.zeros(img.shape)
    mask1[1::2, ::2] = 1
    img_b = img * mask1
    size = list(img_b.shape)
    flag = np.zeros(img_b.shape)
    flag[1::2, ::2] = 1
    for i in range(1, size[0]-1):
        for j in range(1, size[1]-1):
            if(img_b[i][j] == 0):
                if(img_b[i][j-1] != 0):
                    flag[i][j] = -1
                elif(img_b[i][j-1] == 0 and img_b[i-1][j] != 0):
                    flag[i][j] = 2
                else: 
                    flag[i][j] = -2
            else:
                flag[i][j] = 1
    
    for i in range(size[0]):
        for j in range(size[1]):
            if(flag[i][j] == -1):
                img_b[i][j] = (img_b[i][j-1] + img_b[i][j+1]) / 2
            if(flag[i][j] == 2):
                img_b[i][j] = (img_b[i-1][j] + img_b[i+1][j]) / 2
            if(flag[i][j] == -2):
                img_b[i][j] = (img_b[i-1][j-1] + img_b[i-1][j+1] + img_b[i+1][j-1] + img_b[i+1][j+1]) / 4
    
    #直接复制
    for i in range(size[1]):
        if(img_b[0][i] == 0):
            img_b[0][i] = img_b[1][i]
    
    #直接复制    
    for i in range(size[0]):
        if(img_b[i][size[1]-1] == 0):
            img_b[i][size[1]-1] = img_b[i][size[1]-2]
        
    for i in range(size[0]):
        if(img_b[i][0] == 0):
            img_b[i][0] = (img_b[i-1][0] + img_b[i+1][0]) / 2
    
    for i in range(size[1]):
        if(img_b[size[0]-1][i] == 0):
            img_b[size[0]-1][i] = (img_b[size[0]-1][i-1] + img_b[size[0]-1][i+1]) / 2
            
    mask3 = np.zeros(img.shape)
    mask3[::2, 1::2] = 1
    img_r = img * mask3
    
    size = list(img_r.shape)
    flag = np.zeros(img_r.shape)
    flag[::2, 1::2] = 1  
    for i in range(1, size[0]-1):
        for j in range(1, size[1]-1):
            if(img_r[i][j] == 0):
                if(img_r[i][j-1] != 0):
                    flag[i][j] = -1
                elif(img_r[i-1][j] != 0):
                    flag[i][j] = 2
                else: 
                    flag[i][j] = -2
            else:
                flag[i][j] = 1
    
    for i in range(size[0]):
        for j in range(size[1]):
            if(flag[i][j] == -1):
                img_r[i][j] = (img_r[i][j-1] + img_r[i][j+1]) / 2
            if(flag[i][j] == 2):
                img_r[i][j] = (img_r[i-1][j] + img_r[i+1][j]) / 2
            if(flag[i][j] == -2):
                img_r[i][j] = (img_r[i-1][j-1] + img_r[i-1][j+1] + img_r[i+1][j-1] + img_r[i+1][j+1]) / 4
    
    for i in range(size[0]):
        if(img_r[i][0] == 0):
            img_r[i][0] = img_r[i][1]
        
    for i in range(size[1]):
        if(img_r[size[0]-1][i] == 0):
            img_r[size[0]-1][i] = img_r[size[0]-2][i]
        
    
    
    for i in range(size[1]):
        if(img_r[0][i] == 0):
            img_r[0][i] = (img_r[0][i-1] + img_r[0][i+1]) / 2
            
    for i in range(size[0]):
        if(img_r[i][size[0]-1] == 0):
            img_r[i][size[0]-1] = (img_r[i-1][size[0]-1] + img_r[i+1][size[0]-1]) / 2
    
    
    mask2 = np.zeros(img.shape)
    mask2[::2, ::2] = 1
    mask2[1::2, 1::2] = 1
    img_g = img * mask2
    size = list(img_g.shape)
    flag = np.zeros(img_g.shape)
    mask2[::2, ::2] = 1
    mask2[1::2, 1::2] = 1
    for i in range(2, size[0]-2):
        for j in range(2, size[1]-2):
            if(img_g[i][j] == 0):
                if(mask1[i][j] == 1):
                    if(abs(img_b[i][j-2]-img_b[i][j+2]) < abs(img_b[i][j+2]-img_b[i][j-2])):
                        img_g[i][j] = (img_g[i-1][j] + img_g[i+1][j]) / 2
                    elif(abs(img_b[i][j-2]-img_b[i][j+2]) > abs(img_b[i][j+2]-img_b[i][j-2])):
                        img_g[i][j] = (img_g[i][j-1] + img_g[i+1][j]) / 2
                    else:
                        img_g[i][j] = (img_g[i-1][j] + img_g[i+1][j] + img_g[i][j-1] + img_g[i][j+1]) / 4
                if(mask3[i][j] == 1):
                    if(abs(img_r[i][j-2]-img_r[i][j+2]) < abs(img_r[i][j+2]-img_r[i][j-2])):
                        img_g[i][j] = (img_g[i-1][j] + img_g[i+1][j]) / 2
                    elif(abs(img_r[i][j-2]-img_r[i][j+2]) > abs(img_r[i][j+2]-img_r[i][j-2])):
                        img_g[i][j] = (img_g[i][j-1] + img_g[i+1][j]) / 2
                    else:
                        img_g[i][j] = (img_g[i-1][j] + img_g[i+1][j] + img_g[i][j-1] + img_g[i][j+1]) / 4
    
    if(img_g[0][size[1]-1] == 0):
        img_g[0][size[1]-1] = (img_g[0][size[1]-2] + img_g[1][size[1]-1] ) / 2
        
    if(img_g[size[0]-1][0] == 0):
        img_g[size[0]-1][0] = (img_g[size[0]-2][0] + img_g[size[0]-1][1]) / 2
    
    if(img_g[size[0]-1][size[0]-1] == 0):
        img_g[size[0]-1][size[0]-1] = (img_g[size[0]-1][size[0]-2] + img_g[size[0]-2][size[0]-1]) / 2
    
    for i in range(size[0]):
        if(img_g[i][0] == 0):
            img_g[i][0] = (img_g[i-1][0] + img_g[i+1][0] + img_g[i][1]) / 3

    for i in range(size[1]):
        if(img_g[0][i] == 0):
            img_g[0][i] = (img_g[0][i-1] + img_g[0][i+1] + img_g[1][i]) / 3
            
    for i in range(size[0]):
        if(img_g[i][size[1]-1] == 0):
            img_g[i][size[1]-1] = (img_g[i-1][size[1]-1] + img_g[i+1][size[1]-1] + img_g[i][size[1]-2]) / 3
            
    for i in range(size[1]):
        if(img_g[size[0]-1][i] == 0):
            img_g[size[0]-1][i] = (img_g[size[0]-1][i-1] + img_g[size[0]-1][i+1] + img_g[size[0]-2][i]) / 3
    
    for i in range(size[0]):
        for j in range(size[1]):
            if(img_g[i][j] == 0):
                img_g[i][j] = (img_g[i-1][j] + img_g[i+1][j] + img_g[i][j-1] + img_g[i][j+1]) / 4
    
    return img_b, img_g, img_r

if __name__ == '__main__' :
    img_rgb = cv2.imread(r'..\image\bg6.jpg')
    img, ori_img = Bayer(img_rgb)
    img_b, img_g, img_r = Interpolation(img)

    int_img = np.zeros(img_rgb.shape)
    int_img[:, :, 0] = img_b
    int_img[:, :, 1] = img_g
    int_img[:, :, 2] = img_r
    int_img = int_img.astype(np.uint8)
    
    cv2.imshow('ori', img_rgb)
    cv2.imshow('img2', ori_img)
    cv2.imwrite('gray.jpg', img)
    cv2.imwrite('ori_img.jpg', ori_img)
    cv2.imshow('img3', int_img)
    cv2.imwrite('int_img.jpg', int_img)
    #hmerge = np.hstack((img_rgb, ori_img, int_img))
    #cv2.imshow('merge', hmerge)
    
    cv2.waitKey(0)
    cv2.destroyAllWindows()
```

编辑于 01-13





 

Bayer模式是颜色模式，被广泛应用于CCD和CMOS摄像头。

它允许从一个单独平面中得到彩色图像，该平面的R/G/B像素点如下表所示安排。

| R    | G    | R    | G    | R    |
| ---- | ---- | ---- | ---- | ---- |
| G    | B    | G    | B    | G    |
| R    | G    | R    | G    | R    |
| G    | B    | G    | B    | G    |
| R    | G    | R    | G    | R    |
| G    | B    | G    | B    | G    |

对像素输出的RGB分量由该像素的1、2或者4邻域中具有相同颜色的点插值得到。Bayer模式可以通过向左或向上平移一个像素点来进行一些修改。比如说，Bayer模式具有很流行的BG类型。图像bayer格式介绍以及bayer插值原理

