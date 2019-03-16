---
title: TensorFlow学习笔记 
tags: TensorFlow,deepLearning
grammar_cjkRuby: true
grammar_mathjax: true
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

变量和占位符
变量是模型的参数，声明变量之后需要初始化变量
``` python
my_var = tf.Variable(tf.zeros([2,3]))
sess = tf.Session()
initiaize_op = tf.global_variables_initializer() #一次性初始化创建的所有变量
print(sess.run(my_var))
```
占位符仅仅声明数据位置，通过会话的feed_dict参数获得初始化参数，如下：
``` python
sess = tf.Session()
x = tf.placeholder(tf.float32, shape=[2,2])
#identity返回一个与输入的tensor大小和数值都一样的tensor
y = tf.identity(x)
#通过np.random.rand函数可以返回一个或一组服从“0~1”均匀分布的随机样本值
x_vals = np.random.rand(2,2)
print(sess.run(y, feed_dict={x:x_vals}))
```

## Tensorflow进阶

### 重要的机器学习数学表达和tf表达
信息熵数学公式:：
$$ H(X) = -\sum p(x_i)log(p(x_i)) $$  （这是对事件X取了加权平均的结果）


交叉熵数学公式：
$$ H(p,q) = -\sum_{i=1}^np(x_i)log(q(x_i)) $$

sigmoid公式：
$$ S(x) = \frac{1}{1+e^x} $$

正态分布：
N（μ,σ^2），其中数学期望为μ、方差为σ^2，如N(-1,1)和N(3,1)如图：


