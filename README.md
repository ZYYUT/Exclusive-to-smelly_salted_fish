# 臭咸鱼专属代码仓库

我们这里需要实现一个老化后的壁画颜料识别。基础的思路就是获取壁画的颜色，然后对比每种壁画颜料的颜色，并列出匹配项。

## 问题点

1. 颜料存在老化的情况，老化后的颜料会发生变色。
2. 环境光与拍照设备会导致呈现于真实的颜色出现差异。
3. 灰尘、纹理等特征会让颜色出现波动（白点、变黑、变亮）。

## 设计思路
* 壁画数据——数据采集
拍摄照片时附带一张标准比色卡，使用神经网络算法学习比色卡的颜色变化特征。以计算真实的图片效果。
这个阶段不可避免的将像素从一个小色域映射到一个大色域中，同时算法也无法保证100%的正确率。
因此计算后图像的每个像素都存在一个波动的范围，假设各个通道独立，则可以得到两张图片分别对应各通道的最大值与最小值。

* 壁画数据——干扰数据过滤
对于灰尘、纹理等特征带来的细小颜色波动则使用最多相似颜色算法来剔除。
通过在局部搜索相似颜色最多的颜色作为代表色来剔除散射或是凹凸带来的干扰。
这一步会将图片转化为一个个小色块，可能会存在一些颜色丢失，推荐通过提高图片分辨率，或是拍摄局部壁画解决。

* 颜料数据（老化过程）——数据采集
颜料数据采集与壁画数据采集部分一致，通过神经网络学习颜色变化特征来消除环境光线与拍摄设备带来的差异。
同时，颜料的标准色可以从网络中直接下载，可以通过比对颜色差异来验证这个颜色矫正算法的可靠性。
这一步与壁画数据采集不同，我们仅需要采集单一的颜色，或许之后可以有专门的颜色采集算法。
但考虑到对设备的兼容性，依然对各个通道保留误差范围。
会收集图片中的所有颜色，并得到两个像素点分别对应各通道的最大值与最小值。

* 颜料数据——老化数据压缩
在颜料老化试验中，预计产生数万甚至数十万个连续的颜色信息。
我们需要使用以上信息构建一个从颜色到老化程度的映射。
理想状态下，颜料的颜色变化应该是一个随老化程度移动的3维(rgb)坐标，也就是一个线段。
具体压缩算法等到老化数据计算出来后考虑。

* 壁画材料预测
具体步骤根据压缩策略可能存在变化。目前先使用列表遍历比对每一种颜料，得到可能的颜料+可能的老化程度。
通过老化程度对颜料进行分类，老化程度相似的被分为一类。从可能的老化程度中选择最终类别最少的前数个组合列出。
老化程度的相似算法将在后续补充。