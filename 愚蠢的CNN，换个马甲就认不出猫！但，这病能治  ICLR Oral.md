## 愚蠢的CNN，换个马甲就认不出猫！但，这病能治 | ICLR Oral

原创： 关注前沿科技 [量子位](javascript:void(0);) *昨天*

##### 鱼羊 栗子 发自 凹非寺  量子位 报道 | 公众号 QbitAI

![img](https://mmbiz.qpic.cn/mmbiz_jpg/YicUhk5aAGtAXnRXs5jt1drQxa93nyo2nFtmHCwE40mrZq6iaMGy80HRC78sHeCPUeP8lTd3qO2iaGtZMsPhH38wA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

“穿件马甲就不认识我了？”

如果把大象的纹理披在猫身上，CNN识别出的就是大象 (详见下文) 。

图宾根大学的博士僧发现，ImageNet训练出来的CNN做图像分类时，强烈依赖纹理/质地，居然不怎么看形状。

所以给图像做个风格迁移，轻轻松松就能蒙骗AI。

这么好骗的AI必须治，团队制造了一个**升级版ImageNet**，不让纹理再给AI任何提示，逼迫它学习形状。

调教后的AI不止更加准确，面对噪音等各种干扰的时候，还更加鲁棒了。

这项研究中选了**ICLR 2019**的Oral。

## 这个CNN有点蠢

这里有一只橘猫：

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtAXnRXs5jt1drQxa93nyo2nsV4iagBibqBKWLtu5prPo73I8DYCnJO0m9LhR4Izutib2EQTQ8j4eP4Mg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

还有一块只有纹理的大象皮：

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtAXnRXs5jt1drQxa93nyo2nCXwyWvOqTuWctNhOawarP5OXBlDz8mibOaLG51vgxic6VTkNPn3t7tEg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

把大象皮给橘猫披上，橘猫就被认成了大象？？？

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtAXnRXs5jt1drQxa93nyo2n8Bn3giaxrY3yEjqoIqsBbSXa7Aukiaz7KchTED2g2oBdbffN45wtYmzg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

CNN未免太好骗了吧？

来自德国图宾根大学的团队发现，与以往的认知不同，CNN并不是通过将低级特征（例如边缘）组合成复杂的形状，以此来对物体进行分类的。

此前已有研究表明，即使全局形状完全被破坏，CNN仍然可以出色地完成对纹理化图像的识别，似乎对于ImageNet训练出来的CNN来说，局部纹理就已经给分类提供了足够多的信息。

那么问题就来了，对于CNN图像识别来说，到底是对象纹理影响大还是全局形状影响大呢？

实践才能出真知，图宾根大学的小伙伴们为此精心设计了实验。他们利用风格迁移创建了像披着象皮的猫这样纹理与形状相冲突的图片，用它们来测试CNN到底更偏向于哪一边。

用于测试对象包括用ImageNet训练过的AlexNet、VGG-16、GoogLeNet、强大的ResNet-50，以及97名人类选手。

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtAXnRXs5jt1drQxa93nyo2nmNlvrXAujrztnh46qgicSab07rBs3BcXgEcY4c2Akeic4zLohyBqTFgQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

⬇️⬇️⬇️

橘猫，识别成功。灰掉的橘猫，也能识别。涂黑了的橘猫，emmm，有点懵比。素描风格的橘猫，呃，更加尴尬。

纹理越不明，AI越懵比。纹理 VS 形状，CNN对纹理的依赖竟然占了绝对上风。

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtAXnRXs5jt1drQxa93nyo2nrh7HbSgPgXtK2OaITRa3tejLLibp5k7bwKcq4HHoibcLNibKFdlR5NjbA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

## 别急，能治的

团队发现，CNN对局部纹理有超强的bias，而不重视全局形状。

那么，就要用强硬的方法，把bias掰到形状那一边去。

研究人员用ImageNet做基础，造起一个风格化的数据集，叫做Stylized-ImageNet，简称**SIN**：

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtAXnRXs5jt1drQxa93nyo2n8lnakTkXs1drPWcQ0pzqBb7nEvVCGichiaJI2r6WTTQh9W4ic2hfROfiaA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

把每一张图的原始纹理除掉，然后用一种随机选择的绘画风格代替。

目的就是让AI在没有纹理线索的情况下，也能准确判断物体的类别。

这里用的是AdaIN风格迁移方法，风格化系数为α=1.0。

风格的来源，则是Kaggle的**Painter by Numbers**数据集。

新数据集的搭建过程，已经开源了，请见文底传送门。

## 疗效显著

在SIN数据集上，纹理已经风格化，没有太多能帮助分类的有效信息了。

用SIN训练并用SIN测试的结果是，top-5准确率只有**79%**。

同样的网络，在普通ImageNet上训练并用ImageNet测试，top-5准确率有**92.9%**。

这说明，SIN比ImageNet的任务要难许多，少了判断依据，多了干扰信息。

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtAXnRXs5jt1drQxa93nyo2nXBKIYib3Tfgr4jrgs8mWR8BDxShQNF5ic9SNRia0ib5iaiaBy9cTmS0kybvA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

有趣的是，ImageNet的特征在SIN上的泛化能力很差：用前者训练，后者测试，top-5准确率只有**16.4%**。

相反，在SIN上学到的特征却在ImageNet上泛化不错：不做任何微调的情况下，top-5准确率高达**82.6%**。

**然后，还要详细证明一下，只学纹理的模型真的搞不定SIN。**

研究人员想起了2018年发布的**BagNet**，就是仅靠局部纹理搞定ImageNet的那只前辈：

BagNet也是ResNet-50架构，不过最大感受野大小被限定在9×9, 17×17或33×33像素。**这样就没有办法学习任何长距离的空间关系，用来分类**。

有了这些限制，BagNet在ImageNet上面表现很好 (**70%**) ，但在SIN上就不太好了 (**10%**) 。

这明确地表示，SIN数据集可以除去局部纹理给AI带去的线索，逼迫它去学习长距离的空间信息。因为BagNet自身限制没办法学习全局，才扑街了。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/YicUhk5aAGtAXnRXs5jt1drQxa93nyo2nia0whrGmPs7o6JvvHibvMRIE3kqfaCp9Ipba0Bw1wvddqskMq0pz0QPA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

更重要的是，**线索冲突实验** (Cue Conflict Experiment) 证明，用SIN训练的ResNet-50，强烈地偏爱形状线索：bias从ImageNet上的22%，提升到了SIN上的81%。

在许多类别的判断上，甚至和人类对形状的依赖一样强。

## 还有惊喜

除了学到形状特征之外，SIN还附赠许多意想不到的优势。

**第一**，重点学习了形状的ResNet，在top-1和top5准确率上，都胜过了原始的ResNet。

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtAXnRXs5jt1drQxa93nyo2nVyrTRAvNnkdHVLNeOca2QLzLMHBFsY4RlGLgUAHOhOT5ribIxeCRN3A/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

这表示，SIN很可能是一个数据扩增的好方法。

**第二**，团队还用Faster R-CNN模型，测试了新老ResNet作为Backbone的表现。

把SIN揉进训练数据，大大提升了目标检测准确率：从70.7到75.1。这也和目标检测基于形状表征的天性有关。

**第三**，研究人员系统测试了，当给图像加入一些干扰：比如均匀噪音或相位噪音、对比度改变、高通与低通滤波等等，准确度会发生怎样的变化。

虽然损失了几个百分点的准确率，用SIN训练的神经网络还是在绝大多数任务上，战胜了用IN训练的结果。

![img](https://mmbiz.qpic.cn/mmbiz_gif/YicUhk5aAGtAXnRXs5jt1drQxa93nyo2nrHiaIswV2WSia57Kf2RxBLOGypUAWjarFHFRibUTf0g7cwrY3jVkEia0Ag/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

## 传送门

论文：https://arxiv.org/abs/1811.12231

Github：https://github.com/rgeirhos/texture-vs-shape

SIN数据集：https://github.com/rgeirhos/Stylized-ImageNet

— **完** —

**AI社群 | 与优秀的人交流**

![img](https://mmbiz.qpic.cn/mmbiz_png/YicUhk5aAGtCEFSVW5ubo08Zfv1qB5iaprbZUlsy5odkbQRwlnJ4gYccCB4iaR89q8zdvoc9CEDlR3ORTzdicyLI5g/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**小程序 | 全类别AI学习教程**

[![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)](https://mp.weixin.qq.com/s?__biz=MzIzNjc1NzUzMw==&mid=2247524518&idx=2&sn=b562e5a11190ea43aedbe9c198510978&chksm=e8d0d7d4dfa75ec2f1c42b99c255cbef7d82364a1516e59ae29eeefd38e8b4a1b57bf3fbcb2f&mpshare=1&scene=1&srcid=0704KYmrYtR2JAadmaX6KL3h&key=0aa21025b9197e7f5ada12f01749ba83c5963b30c24880b5cc15b4cdb3f6a30d8a1e014730f9a72886844d731a1cc231efb5d7753964f5642b00230b0484920027a7b0cf01ad9545705b7bb26d343e34&ascene=1&uin=MjMzNDA2ODYyNQ%3D%3D&devicetype=Windows+10&version=62060833&lang=zh_CN&pass_ticket=tFNqUL0VfHxxY99IyVywfi5SR9hyyWsrjaXd5I2BiPMy%2BpgePcB11%2FXQntJivQur)

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)



**量子位** QbitAI · 头条号签约作者





վ'ᴗ' ի 追踪AI技术和产品新动态



喜欢就点「在看」吧 ! 









![img](https://mp.weixin.qq.com/mp/qrcode?scene=10000004&size=102&__biz=MzIzNjc1NzUzMw==&mid=2247524518&idx=2&sn=b562e5a11190ea43aedbe9c198510978&send_time=)

微信扫一扫
关注该公众号