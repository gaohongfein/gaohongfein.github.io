---


layout:     post
title:      "Apollo Canbus dbc文件"
date:       2019-09-25 18:00:00
author:     "HongF"
header-img: "img/post-bg-miui6.jpg"
tags:
    - apollo
---

## Apollo Canbus DBC文件使用

## 1. DBC文件定义 

[link](https://blog.csdn.net/weixin_44536482/article/details/89030152)

### 关于DBC文件的格式解析

- [摘要](https://blog.csdn.net/weixin_44536482/article/details/89030152#_2)
- [1、版本与新符号](https://blog.csdn.net/weixin_44536482/article/details/89030152#1_17)
- [2、波特率定义](https://blog.csdn.net/weixin_44536482/article/details/89030152#2_24)
- [3、网络节点的定义](https://blog.csdn.net/weixin_44536482/article/details/89030152#3_30)
- [4、报文帧的定义](https://blog.csdn.net/weixin_44536482/article/details/89030152#4_36)
- [5、信号的定义](https://blog.csdn.net/weixin_44536482/article/details/89030152#5_49)
- [6、注解部分](https://blog.csdn.net/weixin_44536482/article/details/89030152#6_66)
- [7、属性定义部分](https://blog.csdn.net/weixin_44536482/article/details/89030152#7_80)
- [8、数值表部分](https://blog.csdn.net/weixin_44536482/article/details/89030152#8_98)
- [小结](https://blog.csdn.net/weixin_44536482/article/details/89030152#_112)



# 摘要

​        在之前的文章里，我们通过一条报文示例展示了创建dbc文件的操作步骤。通过记事本打开在[“关于DBC文件的创建”](https://blog.csdn.net/weixin_44536482/article/details/88914753)一文中创建的dbc文件，此时界面如下图所示：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190404172832227.?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDUzNjQ4Mg==,size_16,color_FFFFFF,t_70#pic_center)
 如上图所示，一般dbc文件中包含了如下的8种信息：
 **1、版本与新符号 2、波特率定义 3、网络节点的定义 4、报文帧的定义 5、信号的定义 6、注解部分 7、属性定义部分 8、数值表部分**

其中**第2点、第3点是必须项**，缺少这两项的dbc文件是不能用CANdb++ Editor软件打开的；接下来我们结合例子对dbc文件的这几项信息进行逐步解析介绍。

# 1、版本与新符号

​        如下图，Dbc文件头部包含着”version”与”new symbol”的信息；
​         （1）、 ”version”信息可以为空，也可以由用户自定定义；
​         （2）、 ”new symbol”信息在我们创建dbc文件时就已经自动生成；
 所以这一部分的信息我们无需过多留意，一般默认即可。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190404181704457.#pic_center)

# 2、波特率定义

​        格式如下：
​                         **BS_:[baudrate:BTR1,BTR2];**
​         其中**BS_为关键字，用于定义CAN网络的波特率**；[ ]内容表示为可选部分，可以省略（如下图例子中即把该部分省略了）；但**关键字”BS_:”必须存在，省略则会出错**。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190404181953251.#pic_center)

# 3、网络节点的定义

​        格式如下：
​                         **BU_:Nodename1 Nodename2 Nodename3 ……**
​         其中**BU_为关键字，表示网络节点**，格式中的Nodename1、Nodename2表示定义的网络节点名字，由用户自己定义；但需保证节点命名的唯一性。

​        如示例中的**BU_: AVNT ACU HUD ：表示定义了AVNT、ACU、HUD这三个网络节点**。

# 4、报文帧的定义

​        格式如下：
​                         **BO_  MessageId（10进制数表示）  MessageName:  MessageSize  Transmitter**

​        （1）、**BO_为关键字，表示报文；**
​         （2）、MessageId为定义的报文ID，是**以10进制数表示的**；如例子中的996，代表报文ID为0x3E4；
​         （3）、MessageName表示该报文的名字，命名规则和C语言变量相同；
​         （4）、MessageSize表示该**报文数据域字节数**，为无符号整型数据；
​         （5）、Transmitter表示**发送该报文的网络节点**；如果该报文没有指定发送节点，则该值需设置为” Vector__XXX”。

​        如示例中的**BO_ 996 HUD_1_B: 8 HUD：表示定义了一条由HUD这个节点发送，数据域长度为8字节，ID为996（0x3E4），名字命名为HUD_1_B的报文**。

# 5、信号的定义

​        格式如下：
​                         **SG_  SignalName : StartBit|SignalSize@ByteOrder ValueType (Factor,Offset) [Min|Max] Unit Receiver**

​        （1）、**SG_为关键字，表示信号；**
​         （2）、SignalName、 StartBit、 SignalSize分别表示该信号的名字、起始位、信号长度；
​         （3）、ByteOrder表示信号的字节顺序：**0代表Motorola格式，1代表Inter格式**；
​         （4）、 ValueType 表示该信号的数值类型：**+表示无符号数，-表示有符号数**；
​         （5）、Factor表示因子，Offset表示偏移量；这两个值于该信号的原始值与物理值之间的转换。
​         转换如下：物理值=原始值*因子+偏移量；
​         （6）、Min|Max表示该信号的最小值和最大值，即指定了该信号值的范围；这两个值为double类型；
​         （7）、Unit表示该信号的单位，为字符串类型；
​         （8）、Receiver表示**该信号的接收节点**；若该信号没有指定的接收节点，则必须设置为” Vector__XXX”。

​        如示例中的             **SG_ HUD_BrightnessLv : 15|4@0+ (1,0) [0|15] “lv”  ACU,AVNT 表示定义了一个命名为HUD_BrightnessLv的信号，其起始位是第15位，信号长度4个位；信号是Motorola格式，数值类型为无符号类型数；因子为1，偏移量为0；信号取值范围为0到15；信号单位为字符串”lv”；该信号接收节点为ACU、AVNT这两个节点。**

# 6、注解部分

​        格式如下：
​                         **CM_  Object  MessageId/NodeName  “Comment”**

​        （1）、 **CM_为关键字，表示注解信息；**
​         （2）、 Object表示进行注解的对象类型，可以是节点“BU_”、报文“BO_”、消息”SG_”；
​         （3）、 MessageId/NodeName 表示进行注解的对象，若前面的对象类型是信号或者报文，则这里的值应为报文的ID（10进制数表示）；若前面的对象类型为节点，则这里的值应为节点的名字；
​         （4）、 Comment表示进行注解的文本信息；

​        如示例中的 	**CM_ SG_ 996 HUD_HeightLv “Control hud height level”; 表示对ID为996(0x3E4)这条报文下的名为”HUD_HeightLv ”的信号进行注解说明，说明的内容为"Control hud height level"。**

​        **又如	 CM_ BU_ HUD “Head Up Display";表示对HUD这个节点进行注解说明，说明的内容为" Head Up Display "。**

# 7、属性定义部分

​        格式如下：
​                         **BA_DEF_  Object  AttributeName  ValueType  Min  Max;**
​                         **BA_DEF_DEF_  AttributeName  DefaultValue;**

​        （1）、 **BA_DEF_为关键字，表示属性定义；**
​         （2）、 Object表示属性定义的对象类型，可以是节点“BU_”、报文“BO_”、消息”SG_”、网络节点” ”（用空格表示）等；
​         （3）、 AttributeName表示进行定义的属性名字；
​         （4）、 ValueType表示属性值的类型，可以是整型、字符串、浮点型、枚举类型等；
​         （5）、Min/Max表示属性值的上下最值，即指定了取值范围（字符串类型没有此项）。
​         （6）、 **BA_DEF_DEF_为关键字，表示定义属性的初始值；**
​         （7）、 DefaultValue表示该属性的初始值。

​        **如示例中的 	BA_DEF_ SG_  “MyTry” INT 0 11;**
​                              **BA_DEF_DEF_  “MyTry” 0;**
​         **表示对定义了一个针对信号类型的属性，属性名为”MyTry”，属性值是整型数据，取值范围在0到11之间，初始值为0。**

# 8、数值表部分

​        格式如下：
​                         **VAL_  MessageId  SignalName  N “DefineN” ……  0  “Define0”;**

​        （1）、**VAL_为关键字，表示数值表定义；**
​         （2）、 MessageId表示该信号所属的报文ID（**10进制数表示**）；
​         （3）、 SignalName表示信号名；
​         （4）、 N “DefineN” ……  0  “Define0”表示定义的数值表内容，即该信号的有效值分别用什么符号表示 。

​        **如示例中的 	VAL_ 996 HUD_OffSt 1 “Active”  0  “Not Active”;**
​         **表示对ID为996(0x3E4)的这条报文下的，一个命名为”HUD_OffSt”的信号，进行其数值表的定义；用”Active”取代1；用”Not Active”取代0。**

# 小结

​        本文通过示例将常见dbc文件的格式分成了以下八点进行了阐述介绍。
​         **1、版本与新符号         2、波特率定义         3、网络节点的定义         4、报文帧的定义         5、信号的定义         6、注解部分         7、属性定义部分         8、数值表部分**
​         **其中第2点、第3点是必须项，其他项不一定都有**。当我们对dbc文件的结构比较熟悉之后，可以在文本模式下直接对dbc文件进行修改编辑；例如直接拷贝数值表进行修改定义等操作，会比在"CANdb ++ Editor"界面下操作更方便。

## 2. DBC文件如何使用







##  3. Can相关工具

[cantools](https://github.com/eerimoq/cantools)

https://coderdbc.com/ 

两个工具都支持dbc文件自动转换为c文件代码， 用于相应报文的解析发送。



## 4. Canbus相关软件

usbcan品牌广成usbmini-can， 支持windows和linux下的开发， 支持相关软件， 包含Ecan Tools和Cantest(周立功can配套软件)。

![Ecantools](/home/drl/gaohongfein.github.io/img/Ecantools.PNG)



![cantest](/home/drl/gaohongfein.github.io/img/cantest.PNG)

其中CANtest支持DBC文件加载解析， 实时显示can报文解析后的数据，支持批量发送数据， 发送文件。 