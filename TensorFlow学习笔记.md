---
title: TensorFlow学习笔记 
tags: TensorFlow,deepLearning
grammar_cjkRuby: true
---

## Windows环境下安装TensorFlow
- Windows环境下TensorFlow只支持python 3.X版本，练手的话cpu版本就可以了
- 安装[anaconda3](https://www.anaconda.com/download/)
- 在cmd窗口输入安装指令`pip install --upgrade --ignore-installed tensorflow`

## TensorFlow起步
思想：先定义好图结构，再进行计算

张量（tensor）是tensorflow最主要的数据结构，对应关系如下：

0阶张量 == 标量
1阶张量 == 向量（一维数组）
2阶张量 == 二维数组
…
n阶张量 == n维数组

最简单的相加如下：
``` python
import tensorflow as tf
a = tf.constant(28)
b = tf.constant(12)
c = tf.add(a, b)
with tf.Session() as sess:
    print(sess.run(c))
```
定义一个tensor，该tensor中的数值在start到stop区间之间取等差数列（包含start和stop），注意start和stop需要是float形式

``` python
d = tf.linspace(start=0.0, stop=1.0, num=3)
```