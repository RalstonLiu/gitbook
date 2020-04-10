## 用beetle做一个贪玩棋盘


小小的beetle完成一个棋盘？当然不行，所以要请出我们的猪脚----IO扩展板

大扎好，唔系……不好意思拿错稿子了。
大家好，我是玩Arduino一月半的电子爱好者，喜欢划水、摸鱼、炸电容、写BUG。
前几天出门捡到一个快递，迫不及待的拿回家拆开，wow~awesome~
等等，这是啥东东？好像是上次活动与然后PY交易得到的内部工程板。

![](https://mc.dfrobot.com.cn/data/attachment/forum/202004/01/145819rdhwxwewhwf1ju41.png)

研究了一段时间后……总算看出来了，这是一个IIC的IO扩展板。
在我正准备扔在角落吃灰的时候，我想到了很久以前看到的一个动画片——围棋少年（似乎暴露年龄了）


![](https://mc.dfrobot.com.cn/data/attachment/forum/202004/01/150316saka0bgqua10i3qa.png)

那个时候感觉

![](https://mc.dfrobot.com.cn/data/attachment/forum/202004/01/150324t8fxd8jyyvwyvybb.jpg)

落子的时候气势+1+1+1，疯狂模仿，以至于弄坏了许多副棋……额，似乎又跑题了……有了想法就动手去实施，要做什么？往下看就知道了。

---

当当当，自带特效的国际象棋。
这是当所有棋子摆放完过后的效果

![](https://mc.dfrobot.com.cn/data/attachment/forum/202004/01/150947n1jf2fihwrr2zruw.gif)

行走棋子的时候显示蓝色光路径:

![](https://mc.dfrobot.com.cn/data/attachment/forum/202004/01/151110a2224awkpwakt2vk.gif)

吃掉对方棋子的时候显示红色路径:

![](https://mc.dfrobot.com.cn/data/attachment/forum/202004/01/151113h9zjfeat9fmby6tj.gif)

上面的所有显示都是可以通过改程序任意改变的，只有你想不到没有你玩不到。

这个比拇指大一丢丢的beetle控制器，使用了IO扩展板，扩展出了这么多的引脚。

为什么是国际象棋？因为我只拿到了四个IO扩展板（小声BB，好气呀，只白嫖了四个），看介绍可以同时使用8个IO扩展板，也就是扩展128个IO口。等我再白嫖几个后我就做一个中国象棋（咕咕咕~~~）

![](https://mc.dfrobot.com.cn/data/attachment/forum/202004/01/151220wuuezsztus3ummsz.png)


#### 材料准备：
1. beetle(1个)
2. IO扩展板(4个)
3. WS2812灯带(64颗)
4. 霍尔传感器3144(64个)
5. 1K电阻(64个)
6. 国际象棋棋子(1套)
7. 铷磁铁(如果霍尔传感器能感应到棋子则不需要，但是市面上棋子磁力似乎都很小,磁铁大小根据棋子决定)(32颗)
8. 漆包线
9. 5V电源(1个)
10. 纸板一大块（垃圾佬专属，如果条件允许可以使用亚克力板）

这是需要的材料，只展示了一部分:

![](https://mc.dfrobot.com.cn/data/attachment/forum/202004/01/151624lzia9gtd22te55y7.png)


-----------------------------制作开始-----------------------------
本文结构：
一、硬件介绍
    1. IO扩展板
    2. 霍尔传感器
    3. WS2818灯带
二、接线
    1. 接线图
    2. 焊接
三、程序编写
    1. 思路
    2. 编写程序
    3. 烧录演示

#### 一、硬件介绍
在开始制作前我们先了解一下元器件：
1. IO扩展板

我们所用的是IIC接口的16位数字IO口扩展板，最多可以并联8个这样的扩展板，也就是可以扩展出128个IO口！虽然是扩展出的IO口，但是这些IO口功能可不弱，每个IO口都可独立被设置为输入、输出、上拉输入或中断等IO模式。而且这个扩展板还有2个中断信号引脚INTA和INTB，当这两个引脚与主控板的中断IO口连接后可以实现真正的中断！在这个作品中我们只用到了扩展板的输入功能。

2. 霍尔传感器

![](https://mc.dfrobot.com.cn/data/attachment/forum/202004/01/151855b2uz3z27s9e4vbbo.png)

太小了，拍的不清楚，下面是我简单的画的霍尔传感器3144

![](https://mc.dfrobot.com.cn/data/attachment/forum/202004/01/151915fqcozf8ontfqfnc7.png)

网上对霍尔传感器噼里啪啦的介绍了一大堆，简单来说，霍尔传感器就是检测磁场强度的一个传感器。我们这里使用的是单极性开关型霍尔传感器。当有磁铁时输出低电平，没有就输出高电平。我用铷磁铁测试感应距离大约8mm，不同磁性的磁铁感应距离也不一样，下面是测试原理图和结果图：

![](https://mc.dfrobot.com.cn/data/attachment/forum/202004/01/151939gwrryjrdjgy67zoe.png)

测试

![](https://mc.dfrobot.com.cn/data/attachment/forum/202004/01/152012avljfnyneytzvj74.gif)

磁铁磁性越大，感应距离大，磁铁磁性越小，感应距离小。而且磁铁磁性越大，棋子之间的间隔也就越大，建议先测试确定数据之后再动手，并且建议这样测试每个传感器的好坏。

3. WS2818灯带

![](https://mc.dfrobot.com.cn/data/attachment/forum/202004/01/152056hvc5q33ru3duuuqx.png)

WS2812 RGB灯带是由全彩LED组成。只需一个IO口就可以控制所有LED灯，使用起来非常的方便。而且灯带柔性较大，可随意对接，弯曲，裁剪，非常符合我们的要求。
需要注意：灯带之间千万不要接反

![](https://mc.dfrobot.com.cn/data/attachment/forum/202004/01/152116kxwi11zwwvic1g33.png)

#### 二、接线
1. 接线图

![](https://mc.dfrobot.com.cn/data/attachment/forum/202004/01/152142l28r2cxab21ajacj.png)

LED灯带连接beetle的D9引脚，扩展板的VCC、GND、SDA、SCL分别与beetle上的相应引脚焊接上，扩展板的传感器全部IO口都按照接线图接上霍尔传感器。当LED灯最高亮度点亮白色时，单的灯电流可达60mA，LED灯比较多，建议外接电源，虽然暂时不接电源也能正常运行，但是为了以后的可拓展性，建议还是加上。

2.焊接
先拿出我的小纸板，在上面画出棋盘所有格子

![](https://mc.dfrobot.com.cn/data/attachment/forum/202004/01/152321scbndcucys8wghu5.png)

我们在上面焊接一个霍尔传感器（焊接都是采用的漆包线）

![](https://mc.dfrobot.com.cn/data/attachment/forum/202004/01/152335tagr87ckfak8g80k.png)

然后我们接着焊接几个

![](https://mc.dfrobot.com.cn/data/attachment/forum/202004/01/152404qmmlrbbrxrb8vmbm.png)

然后去睡一觉后就自己焊接好了![](http://df.yirenliu.cn/FqwxAaJ2wdASw7hmM-hv8jmY9m2X)

![](https://mc.dfrobot.com.cn/data/attachment/forum/202004/01/152535p77zfpb7xx3faf1z.png)

我们接下来焊接灯带，8个一条，我们需要八条，间距根据你格子大小确定。建议测试焊接好的灯带是否能用。

![](https://mc.dfrobot.com.cn/data/attachment/forum/202004/01/152556yv00byr0vjk905r9.png)

我们先在焊接完的霍尔传感器上面铺上一层白纸（防止短路），然后将灯带按下面的示意图焊接好，并贴在每个霍尔传感器的上面

![](https://mc.dfrobot.com.cn/data/attachment/forum/202004/01/152613hjb4v4jhu9jqm29k.png)

睡一觉过后又自动焊接好了

![](https://mc.dfrobot.com.cn/data/attachment/forum/202004/01/152707o94j9hjf7umxj8h7.png)

我们在上面再铺一层白纸，画出棋盘格子

![](https://mc.dfrobot.com.cn/data/attachment/forum/202004/01/152726vrlycalxtrlfp75x.png)

再稍加装饰

![](https://mc.dfrobot.com.cn/data/attachment/forum/202004/01/152819a0wmo7raa8hcr3wd.png)

记得组装的时候留出接口

![](https://mc.dfrobot.com.cn/data/attachment/forum/202004/01/152851lxb7yrrq8q1rbuby.png)

3. 改造棋子
我们将棋子里面的磁铁替换掉

![](https://mc.dfrobot.com.cn/data/attachment/forum/202004/01/152913a0odddvvmerflfff.png)

三、程序编写
1.思路
主程序就是整个思路，下面会一步一步讲解

![](https://mc.dfrobot.com.cn/data/attachment/forum/202004/02/091810deil72i55flznwif.png)

第一步：初始化LED灯和IO扩展板（这步比较简单就不细说了）

![](https://mc.dfrobot.com.cn/data/attachment/forum/202004/02/090116h1y1tlc1j9k1t78a.png)

第二步：点亮棋子摆放位置的灯，等待棋子摆放完成
因为棋子摆放在1.2.7.8行，所以只要这四行电平全为低，意味着摆放完成

![](https://mc.dfrobot.com.cn/data/attachment/forum/202004/02/090235t6dhkh6c3ypy407t.png)

第三步：棋子摆放完成后展现开局灯光
这一步主要时点灯，想玩出什么花样都行

第四步：扫描IO扩展板IO口
先将原来的IO口电平信息保存在num0中，然后读取新的信息保存到num中

![](https://mc.dfrobot.com.cn/data/attachment/forum/202004/02/090949crj4xrwiw744nr63.png)

第五步：判断是否拿放棋子，计算位置，判断走子还是吃子
通过新旧数据的对比很容易判断出哪行哪列的电平出现变化，就能得到坐标。
通过判断是0变1，还是1变0，就能知道是拿起还是放下

![](https://mc.dfrobot.com.cn/data/attachment/forum/202004/02/091134j1pg8hpg2gpqpjtg.png)

第六步：点亮相应的灯光
最后就是通过两个坐标，点亮你想要的灯就行了

2.编写程序
说到这个程序刚开始没有什么思路，然后我去请教大佬。

![](https://mc.dfrobot.com.cn/data/attachment/forum/202004/01/152949snmem02rerpivpim.jpg)

大佬的思维我这种小萌新实在搞不懂，打扰了，告辞。
随着我一阵噼里啪啦乱敲过后……

![](https://mc.dfrobot.com.cn/data/attachment/forum/202004/01/153006xu3d4z3annwuw0ar.png)

幸好我不是走专业程序员的路，不然铁定被祭天。
还是求助大佬吧……最后在大佬的帮助下完成了代码(文章末尾附上)。

3.烧录程序GO!GO!GO!
上电-烧录……
欢迎来到贪玩象棋
这是等待双方摆放棋子

![](https://mc.dfrobot.com.cn/data/attachment/forum/202004/01/153043lttn1oko0kbz15k1.png)

由于身边的人都不会下，所以我就演示一下棋盘效果（由于我代码太渣，所以只有按照国际象棋规则走才会显示相应的灯光），想要什么光效完全可以自己编写，发挥自己的脑洞。
国际象棋的运动轨迹有4种：横、直、斜还有马的走法。我就这里就按照走法简单演示一下。走子显示蓝色的路径，吃子显示红色的路径（附件上的代码解决视屏中第一步行走显示红色光的BUG）

<iframe height=498 width=100% src="//player.bilibili.com/player.html?aid=67128192&bvid=BV1WJ411N7py&cid=116398943&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" width=100% height=100% allowfullscreen="true"> </iframe>

这是1.0版本，如果我没有咕咕咕，以后会做一个2.0版本（硬件方面改动很小，加一个喇叭和DFPlayer Mini），当拿起一个棋子之后会发出相应的声音，如：拿起马，会发出马叫，吃子会发出兵戎相见的声音……
这个还可以做成类似于开心消消乐的游戏，亮多种颜色的灯，用磁铁调换两个灯，来消除。
8个扩展板最多可以扩展出128个IO口，玩法肯定不止这些，等待大佬们的创意。
