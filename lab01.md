# **Minecraft门电路基础**

在这之前一直对红石毫无了解，望而生畏，只能看看大神们实现出一个个厉害的玩意（比如计算器、自动农场、电梯等）。最近稍微了解了一下红石的工作机制，虽然仍是一个门外汉，但总算是纠正了 “红石是大神们的玩具” 的观念。希望本文可以给与曾经的我类似的玩家一些小小的帮助。

<br/>

本文的内容如下：
1. 门电路介绍

2. 几种常见的门电路

3. Minecraft中与电路相关的物品

4. 七种门电路在Minecraft中的实现

<br/>

## **门电路介绍**
>*用以实现基本逻辑运算和复合逻辑运算的单元电路称为门电路。*

这句话摘自百科，咋听起来很复杂，实则非也。简单（但不一定准确）来说，用 0 表示无信号，用 1 表示有信号。一个 “门” 的作用，就是把输入的布尔值转换为输出。用 “门” （门电路的英文是“gate circuit"）这个词是很形象的，几个输入通过一扇门进入一个房间，之后再出来，就成了输出。这个房间里面就装着复杂（或许不复杂）的电路，其他人或许不必关注这些细节，仅把它当成黑盒，但是本文正是要 “解剖” 这个黑盒。

举个简单的例子就可以理解了。例如，与门（AND）。回忆一下逻辑运算里的AND，或者说 “∧”。当A与B都为1时，A ∧ B才为1，只要其中一个为0，那么A ∧ B就为0了。用电路图来表示如下：

![AND gate](https://raw.githubusercontent.com/ciaoSora/swi-homework/gh-pages/images/1-wk3_and-gate.png)

开关闭合代表1，不闭合代表0；灯泡亮代表1，不亮代表0。显然，只有当开关A和B都闭合时，灯泡才能亮，即1 AND 1 = 1。

<br/>

## **几种常见的门电路**
先是最基础的三种，其他的都是基于这三种。

类型 | 中文 | 表达 | 解释
:-:|:-:|:-:|:-:
NOT|非门|NOT A|把0变1，1变0
AND|与门|A AND B|当二者均为1时，结果为1；否则为0
OR|或门|A OR B|当二者均为0时，结果为0；否则为1。即A OR B = NOT ((NOT A) AND (NOT B))

OR的解释部分其实就是摩根定律，换一种表达方式也可以：A AND B = NOT ((NOT A) OR (NOT B))。

接下来是复合了上述若干种门电路形成的门电路。

类型 | 中文 | 表达 | 解释
:-:|:-:|:-:|:-:
NAND|与非门|A NAND B|当二者均为1时，结果为0；否则为1。即A NAND B = NOT (A AND B)
NOR|或非门|A NOR B|当二者均为0时，结果为1；否则为0。即A NOR B = NOT (A OR B)
XOR|异或门|A XOR B|当二者相等时，结果为0；否则为1。即A XOR B = (A AND (NOT B)) OR ((NOT A) AND B)
XNOR|同或门|A XNOR B|当二者相等时，结果为1；否则为0。即A XNOR B = NOT (A XOR B)

XNOR或称 “异或非门”

<br/>

## **Minecraft中与电路相关的物品**
这类物品大致可分为三类，分别为：

- 电源：顾名思义，为电路提供能量，如红石火把、按钮、拉杆
- 传输线：类似现实生活中的铜导线，可以给其他物体充能，如红石粉、红石中继器
- 接收器：接收电能后作出反应，不同的接收器有不同的反应，如红石灯、发射器

关于电路，还有很多概念，比如充能等级、红石刻等，这些概念在本文中用不到，有兴趣的可以参考一下[我的世界中文wiki][1]。

本文使用到的物品只有拉杆、红石火把、红石粉、红石灯和圆石。拉杆是作为电源用的，它有两种状态，处于某一种时，会供电；处于另一种时，不会供电。红石火把亮时，会供电；不亮时，不会供电。红石粉没什么好说的了，就是传递电能的。红石灯如果接收到了电能，会被点亮，在下文，红石灯作为输出，亮即为1，灭即为0。这里用到圆石，是因为要用到红石火把的一个特性：红石火把在附着的方块被充能后熄灭，这里我用了圆石来让红石火把附着。

该如何表示 “输入” 呢？如果仅仅用拉杆的话，虽然不是不可以，但是输入的值直观上来看不太清晰，因为拉杆的两种状态是向不同的两边旋转45°。但是借由上一段提到的红石火把的特性，可以组装成一个很直观的电源作为输入值。

先来看看一个最简单的、什么 “门” 都没有的电路吧：

![bare-lever-off](https://raw.githubusercontent.com/ciaoSora/swi-homework/gh-pages/images/1-wk3_bare-lever-off.png)
红石灯未接收到电能，处于熄灭状态。

先来解释一下这个组装的 “电源” 吧，事实上，现在这个拉杆是正在供电的，但为什么反而组装电源没有向外界供电呢？因为这个圆石被拉杆充能，附着其上的红石火把即熄灭，没有向这个电路供电。如果现在在圆石的右边连电路，就可以看出拉杆确实在供电。

现在，只要把拉杆拉向另一边，就可以看到：

![bare-lever-on](https://raw.githubusercontent.com/ciaoSora/swi-homework/gh-pages/images/1-wk3_bare-lever-on.png)

然后，再试一下，用红石火把做电源的效果：

![bare-torch](https://raw.githubusercontent.com/ciaoSora/swi-homework/gh-pages/images/1-wk3_bare-torch.png)

接下来，就要开始实现门电路了。

<br/>

## **七种门电路在Minecraft中的实现**
前文做了那么多铺垫，终于到了核心部分，接下来开始把上文说的7种门电路逐一实现。先进行一项约定，输入放在最右边，输出放在最左边。

### NOT

![not-0](https://raw.githubusercontent.com/ciaoSora/swi-homework/gh-pages/images/1-wk3_not-0.png)

这就是一个非门了！注意，虽然有两个圆石，但是只有右边那个才是组装电源，左边的只是非门电路中的一部分，别混淆了。现在的输入为0（可以看到组装电源的火把处于熄灭状态），输出为1（红石灯亮了）。红石灯发亮是由于左边的那个红石火把向其供电了。现在拨动拉杆，将输入改为1。

![not-1](https://raw.githubusercontent.com/ciaoSora/swi-homework/gh-pages/images/1-wk3_not-1.png)

灯熄灭了，即输出为0。上文讲过，红石火把在附着的方块被充能后熄灭。这里，这块圆石被组装电源充能，因此红石火把熄灭，不供电了，灯也无法接收到电能了。

输入0，输出1；输入1，输出0，完美！

### OR

![or-0](https://raw.githubusercontent.com/ciaoSora/swi-homework/gh-pages/images/1-wk3_or-0.png)


这是或门。红石粉末连成的导线从红石灯出来后，分成了两股，只要至少一股接收到了电能，红石灯即会接收到电能并发亮。上图是输入了两个1，1 OR 1 = 1得到了验证。下面验证 0 OR 1 和 0 OR 0 的情况。

![or-1](https://raw.githubusercontent.com/ciaoSora/swi-homework/gh-pages/images/1-wk3_or-1.png)

↑ 0 OR 1 = 1

![or-2](https://raw.githubusercontent.com/ciaoSora/swi-homework/gh-pages/images/1-wk3_or-2.png)

↑ 0 OR 0 = 0

这样，或门就完美地实现了。

### AND

前文提到过摩根定律，即A AND B = NOT ((NOT A) OR (NOT B))，此处就是用摩根定律来实现与门，其实就是把三个非门和一个或门组合起来。

![and-0](https://raw.githubusercontent.com/ciaoSora/swi-homework/gh-pages/images/1-wk3_and-0.png)

↑ 1 AND 1 = 1

![and-1](https://raw.githubusercontent.com/ciaoSora/swi-homework/gh-pages/images/1-wk3_and-1.png)

↑ 0 AND 1 = 0


再次提醒别忘了我们的输入是最右边的，输出是红石灯，其他火把都是逻辑电路的一部分。如果为了看起来更像一个 “门”，我们可以把中间的实现全部封装起来：

![and-2](https://raw.githubusercontent.com/ciaoSora/swi-homework/gh-pages/images/1-wk3_and-2.png)

### NAND

接下来的几个都是上面的三种比较基础的门的组合了，就不一一验证了。

与非门，就是两者先AND，结果再NOT。刚刚与门的实现原理是A AND B = NOT ((NOT A) OR (NOT B))，那么A NAND B = (NOT A) OR (NOT B),也就是说我们把最外层的那个非门去掉即可。

![nand](https://raw.githubusercontent.com/ciaoSora/swi-homework/gh-pages/images/1-wk3_nand.png)

### NOR

这个也很简单，就是在或门外层加一个非门即可。

![nor](https://raw.githubusercontent.com/ciaoSora/swi-homework/gh-pages/images/1-wk3_nor.png)

### XOR





[1]: https://minecraft-zh.gamepedia.com/%E7%BA%A2%E7%9F%B3%E7%94%B5%E8%B7%AF "我的世界中文wiki"
