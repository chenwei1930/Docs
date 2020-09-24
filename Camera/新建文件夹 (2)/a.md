[![img](10.10.10.190/cw/story/internal-docs/Dept3/RTOS/FreeRTOS/Develop/Rockchip_RK2206_Developer_Guide_PCBA_CN/相机内部摄像头数据输出格式_files/nlogo.jpg)](http://www.360doc.com/index.html)

[![img](10.10.10.190/cw/story/internal-docs/Dept3/RTOS/FreeRTOS/Develop/Rockchip_RK2206_Developer_Guide_PCBA_CN/相机内部摄像头数据输出格式_files/dingtu.gif)我的图书馆](http://www.360doc.com/login.aspx?reurl=http://www.360doc.com/content/19/1223/09/33093582_881511187.shtml)

[留言交流](http://www.360doc.com/advice.html)



[小经济学家](http://www.360doc.com/userhome/33093582) / [精华](http://www.360doc.com/userhome.aspx?userid=33093582&cid=1295) / [相机内部摄像头数据输出格式](javascript:void(0))

[全屏](javascript:void(0))[打印](javascript:void(0))[转藏](javascript:void(0))



<iframe id="ifradarttopgoogle" frameborder="0" scrolling="no" src="./相机内部摄像头数据输出格式_files/adgooglearttop.html" style="width: 970px; height: 90px; border: 0px;"></iframe>



分享

[更多](javascript:void(0);)

![img](10.10.10.190/cw/story/internal-docs/Dept3/RTOS/FreeRTOS/Develop/Rockchip_RK2206_Developer_Guide_PCBA_CN/相机内部摄像头数据输出格式_files/bgcolor.jpg)

![img](10.10.10.190/cw/story/internal-docs/Dept3/RTOS/FreeRTOS/Develop/Rockchip_RK2206_Developer_Guide_PCBA_CN/相机内部摄像头数据输出格式_files/fontSize.jpg)

## 相机内部摄像头数据输出格式

2019-12-23 [小经济学家](http://www.360doc.com/userhome/33093582)  阅 166 

参考：https://blog.csdn.net/u011425939/article/details/53437000对于彩色图像，需要采集多种最基本的颜色，如rgb三种颜色，最简单的方法就是用滤镜的方法，红色的滤镜透过红色的波长，绿色的滤镜透过绿色的波长，蓝色的滤镜透过蓝色的波长。如果要采集rgb三个基本色，则需要三块滤镜，这样价格昂贵，且不好制造，因为三块滤镜都必须保证每一个像素点都对齐。当用bayer格式的时候，很好的解决了这个问题。bayer 格式图片在一块滤镜上设置的不同的颜色，通过分析人眼对颜色的感知发现，人眼对绿色比较敏感，所以一般bayer格式的图片绿色格式的像素是是r和g像素的和。先看看网上的一种说法“摄像头的数据输出格式一般分为**CCIR601、CCIR656、RAW RGB**等格式”大嘴评述：这里的摄像头严格来说应该是传感器(sensor)，个人觉得**CCIR601和CCIR656更应该看做是一种标准和计算方式，而不应该是数据格式**，这里我觉得有些误导，不必深究，具体关于CCIR601和CCIR656感兴趣的朋友请自行查阅资料，这里只做简单介绍。**一、Sensor的感光原理：**     **Sensor的感光原理是通过一个一个的感光点对光进行采样和量化，但在Sensor中，每一个感光点只能感光RGB三基色中的一种颜色（这个颜色可以理解为像素的一个颜色分量，并不是最终的图像显示的颜色，最终图像显示的颜色是由RGB三个颜色分量组合构成，根据RGB三个颜色分量的值不同，组合成不同的颜色）。所以，通常所说的30万像素或130万像素等，指的是有30万或130万个感光点，每一个感光点只能感光一种颜色**。**二、CCIR601或656的格式**    **要还原一个真正图像，需要每一个点都有RGB三种颜色**，所以，对于CCIR601或656的格式，在Sensor模组的内部会有一个ISP模块，会将Sensor采集到的数据进行插值和特效处理，例如：**如果一个感光点感应的颜色是R，那么，ISP模块就会根据这个感光点周围的G、B感光点的数值来计算出此点的G、B值，那么，这一点的RGB值就被还原了**，然后在编码成601或656的格式传送给Host。**三、RGB RAW格式**    RGB RAW格式的Sensor是将每个感光点感应到的RGB数值直接传送给Host，由Host来进行**插值**和特效处理。由此可见RGB RAW DATA才是真正的原始数据。**RGB RAW DATA是指原始的数据，单个pixle只能感应一种颜色（RGB中的一种）。****四、 Bayer格式**    **如果这个原始数据的排列格式是 RGRG/GBGB排列的，我们叫做 Bayer pattern（这个最最常见）**。所以 Bayer RGB是属于 RGB RAW data的，但是 **RGB RAW data不一定是bayer pattern，不同厂家的sensor,其RGB RAW DATA排列是不同的， 不过对于我们来说不必过于关心扫描格式，反正厂家都会提供API.**   **Bayer格式是相机内部的原始图片, 一般后缀名为.raw。很多软件都可以查看, 比如PS。我们相机拍照下来存储在存储卡上的.jpeg或其它格式的图片, 都是从.raw格式转化过来的。如下图，为bayer色彩滤波阵列，由一半的G，1/4的R，1/4的B组成。****一般bayer格式的图片绿色像素是r和g像素的和。**                                      ![img](10.10.10.190/cw/story/internal-docs/Dept3/RTOS/FreeRTOS/Develop/Rockchip_RK2206_Developer_Guide_PCBA_CN/相机内部摄像头数据输出格式_files/178622869_1_20191223090752925)Bayer数据，其一般格式为： 奇数扫描行输出 RGRG…… 偶数扫描行输出 GBGB……　　根据人眼对彩色的响应带宽不高的大面积着色特点，每个像素没有必要同时输出3种颜色。因此，数据采样时，奇数扫描行的第1，2，3，4，…象素分别采样和输出R，G，R，G，…数据；偶数扫描行的第1，2，3，4，…象素分别采样和输出G，B，G，B，…数据。**在实际处理时，每个**像素**的R，G，B信号由**像素**本身输出的某一种颜色信号和相邻**像**素输出的其他颜色信号构成**。这种采样方式在基本不降低图像质量的同时，可以将采样频率降低60％以上。









本站是提供个人知识管理的网络存储空间，所有内容均由用户发布，不代表本站观点。如发现有害或侵权内容，请点击这里 或 拨打24小时举报电话：4000070609 与我们联系。

[转藏到我的图书馆](javascript:void(0);)[献花（0）](javascript:void(0);)分享：[微信](javascript:void(0);)

来自： [小经济学家](http://www.360doc.com/userhome/33093582) > [《精华》](http://www.360doc.com/userhome.aspx?userid=33093582&cid=1295)

[举报](javascript:void(0);)

推荐：[发原创得奖金，“原创奖励计划”来了！](http://www.360doc.com/pages/Wallet/bonus.html) | [品味北京的文化之美，有奖征文一起去记录！](http://www.360doc.com/pages/activity/activity47.html)

上一篇：[摄像头sensor的数据输出格式。](http://www.360doc.com/content/19/1223/09/33093582_881510178.shtml)

下一篇：[摄像头sensor的数据输出格式。](http://www.360doc.com/content/19/1223/09/33093582_881513111.shtml)





<iframe id="youlikead" frameborder="0" scrolling="no" src="./相机内部摄像头数据输出格式_files/adbaiduyoulike.html" style="list-style: none outside; margin: 0px; padding: 0px; font-family: &quot;microsoft yahei&quot;, arial, simsun; width: 676px; height: 280px; border: 0px;"></iframe>