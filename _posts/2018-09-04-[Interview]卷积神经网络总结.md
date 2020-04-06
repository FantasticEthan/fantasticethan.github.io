---
title: "[Interview]卷积神经网络总结"
layout: post
date: 2018-09-05 11:01
image: /assets/images/markdown.jpg
headerImage: false
tag:
- Interview
- Record
category: blog
author: ethan
description: 面试中卷积神经网络问题

---

# 卷积神经网络

## 基本层

__输入__

卷积神经网络的数据一般为图像，而图像在普通DNN的会耗费大量的参数，反向传播的计算量巨大。
以RGB图像为例，有三个通道，例如[28,28,3]。这是一个28*28形状的，3通道的图片。一般地，我们会对输入做中心化处理，这里的中心化有两种选择，第一种：求得训练集所有像素的平均值，然后用样本减去该平均值；第二种：以每个feature map为单位，求得平均值，即对应位置的元素减去所有样本中对应位置元素的平均值，然后用样本减去该均值。二者选其一即可。 

在tf里面，一般input的张量形式为[batch,img_height,img_width,img_channels]

*tf_input = tf.placeholder(tf.float32,[batch,img_height,img_width,img_channels],name='input')*

__卷积层__

卷积操作的核心是获取图像的局部信息，在我的理解为类似滤波器的操作。图像本身具有“二维空间特征”，通俗点说就是局部特性。即只需要看到猫的眼睛和嘴巴，我们就能判定这个是一张猫的图片。

卷积有两类参数，卷积核的大小，步长，还有是否进行填充。
假设$n*n$的图像，用$f*f$的卷积核，填充数量用$p$,步长为$S$,则输出为$(n+2p−f+1)×(n+2p−f+1)$。

卷积层最重要的是两个特点。

- local receptive fields（感受视野）
- shared weights（共享权值）

带来的好处最大的是局部关联。即将特征点与周围特征点联系起来。
而共享权值是指同一个深度切片的神经元时采用的滤波器是共享的，这个特点减少了参数的数量。但是在一些场景中是有歧义的，比如输入的图片是人脸，眼睛和头发位于不同的位置，希望在不同的位置学到不同的特征 (参考斯坦福大学公开课)。这对于我们提升类间距差距是一个启发。

{% highlight python %}

def weight_variable(shape):
		# 正态分布，标准差为0.1，默认最大为1，最小为-1，均值为0
    		initial = tf.truncated_normal(shape, stddev=0.1)
    		return tf.Variable(initial)
def bias_variable(shape):
		# 创建一个结构为shape矩阵也可以说是数组shape声明其行列，初始化所有值为0.1
    		initial = tf.constant(0.1, shape=shape)
    		return tf.Variable(initial)
	def conv2d(x, W):  
		# 卷积遍历各方向步数为1，SAME：边缘外自动补0，遍历相乘
  		return tf.nn.conv2d(x, W, strides=[1, 1, 1, 1], padding='SAME') 
  		 
{% endhighlight %}

*在使用中,因为一般不对Input的第一维和第四维进行卷积操作,所以strides 一般为[1,X,X,1]*


激励层主要对卷积层的输出进行一个非线性映射，因为卷积层的计算还是一种线性计算。使用的激励函数一般为ReLu函数：
f(x)=max(x,0)

卷积层和激励层通常合并在一起称为“卷积层”。


__池化层__

当输入经过卷积层时，若感受视野比较小，布长stride比较小，得到的feature map （特征图）还是比较大，可以通过池化层来对每一个 feature map 进行降维操作.
池化的优点：①特征不变性；②特征降维；③缓解一定程度的过拟合。

- Max pooling：取“池化视野”矩阵中的最大值
- Average pooling：取“池化视野”矩阵中的平均值

{% highlight python %}

def max_pool_2x2(x):  
		# 池化卷积结果（conv2d）池化层采用kernel大小为2*2，步数也为2，周围补0，取最大值。数据量缩小了4倍
  		return tf.nn.max_pool(x, ksize=[1, 2, 2, 1],strides=[1, 2, 2, 1], padding='SAME')  

{% endhighlight %}

__全连接层和输出层__

全连接和输出层一般作为分类和回归器。和普通的神经网络效果一样。

{% highlight python %}

fc_w1 = tf.Variable(tf.random_normal([2*2*5,50]))
fc_b1 =  tf.Variable(tf.random_normal([50]))
fc_out1 = tf.nn.relu(tf.matmul(max_pool2_flat, fc_w1) + fc_b1)

{% endhighlight %}


__完整的手写CNN__

{% highlight python %}
input_X = tf.placeholder(tf.float32,[batch,height,width,channel])
input_y = tf.placeholder(tf.float32,[batch,class_num])

def weights_variable(shape):
	initial = tf.truncated_normal(shape,stddev=0.1)
	return tf.Variable(initial)

del bias_variable(shape):
	initial = tf.constant(0.1,shape=shape)
	return tf.Variable(initial)

def con2d(x,W):
	return tf.nn.conv2d(x,W,stride=[1,1,1,1],padding='SAME')

def max_pooling(x):
	return tf.nn.max_pooling(x,ksize=[1,1,1,1],strides=[1,1,1,1],padding='SAME')
	
	W_conv1 = weight_variable([5, 5, 3, 32]) 
	# 对于每一个卷积核都有一个对应的偏置量。
	b_conv1 = bias_variable([32])  
	# 图片乘以卷积核，并加上偏执量，卷积结果28x28*x32
	h_conv1 = tf.nn.relu(conv2d(x_image, W_conv1) + b_conv1)  
	# 池化结果14x14x32 卷积结果乘以池化卷积核
	h_pool1 = max_pool_2x2(h_conv1) 
	
	h_pool1_flat = tf.reshape(h_pool1, [-1, 7*7*64]) 
	
	h_fc1 = tf.nn.relu(tf.matmul(h_pool2_flat, W_fc1) + b_fc1) 
	keep_prob = tf.placeholder(tf.float32) 
	h_fc1_drop = tf.nn.dropout(f_fc1,keep_prob) #对卷积结果执行dropout操作
	# 二维张量，1*1024矩阵卷积，共10个卷积，对应我们开始的ys长度为10
	W_fc2 = weight_variable([1024, 10])  
	b_fc2 = bias_variable([10])  
	# 最后的分类，结果为1*1*10 softmax和sigmoid都是基于logistic分类算法，一个是多分类一个是二分类
	y_conv=tf.nn.softmax(tf.matmul(h_fc1_drop, W_fc2) + b_fc2


{% endhighlight %}

## 基于CNN的相关面试问题

- Question: filter尺寸的选择
	- 通常尺寸多为奇数（1，3，5，7） 

- Question:输出尺寸计算公式
	- 见上文

1. Question:pooling池化的作用
	- 特征的降维
	- 缓解一定程度的过拟合
2. Question: CNN的用途
	- CNN主要用来识别位移、缩放及其他形式扭曲不变性的二维图形
3. Question:CNN最成功的应用是在CV，那为什么NLP和Speech的很多问题也可以用CNN解出来？为什么AlphaGo里也用了CNN？这几个不相关的问题的相似性在哪里？CNN通过什么手段抓住了这个共性
	- 几个不相关的问题的相关性在于，都存在局部与整体的关系，由低层次的特征经过组合，组成高层次的特征，并且得到不同特征之间的空间相关性。
CNN通过：局部感知、权值共享、池化操作、多层次结构抓住了这个共性。局部感知使网络可以提取数据的局部特征；权值共享大大降低了网络的训练难度；池化操作和多层次结构一起，实现了数据的降维，将低层次的特征组合成高层次的特征。
4. Question:什么时候用local-conv？什么时候用全卷积（每一个点用同一个filter）？
	- 当数据集具有全局的局部特征分布时，也就是说局部特征之间有较强的相关性，适合用全卷积。在不同的区域有不同的特征分布时，适合用local-Conv。
5. Question:CNN的有点
	- 共享卷积核、减少了网络自由参数的个数，对高维数据处理无压力；无需手动选取特征，训练好权重，即得特征。降低神经网络的复杂性。这种网络结构在有监督的方式下学习到了一些良好的性能：对平移、比例缩放、倾斜或其他形式的变形具有高度不变性。 
6. Question:CNN的缺点
	- 需要调参，需要大量样本 
7. Question:卷积神经网络平移的原因
	- 参数共享
8. Question:在图像中，data augumentation有哪些方式
	- 裁剪/缩放/彩色变换/翻转
	
__常见的卷积网络模型__

| 名称             |  模型描述|
|:---------------:|:-------:|
|LeNet|第一个成功应用于数字数字识别的卷积神经网络模型|
| AlexNet|1.Relu激活函数 2.多GPU并行训练 3.LRN（局部响应归一化）4.重叠池化 5.Dropout|
| VGGNet | 基本结构没有大的变化，更多的层数与卷积，有VGG16,VGG19版本|
| GoogleNet | 使用了多个不同分辨率的卷积核，最后再对它们得到的feature map 按深度融合在一起|
| ResNet| 将低层学习到的特征和高层的学习到的特征进行一个融合（加法运算,图右），这样反向传递时，导数传递得更快，减少梯度弥散的现象|
| DenseNet| 完全密集的每一层的特征传递|






