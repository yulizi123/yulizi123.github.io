---
youku_id: XMjc0NjA3MzE1Mg
youtube_id: KBdb53NrVAc
b_av: 15997678
b_cid: 27669630
b_page: 7
title: Torch 或 Numpy
publish-date: 2017-05-05
thumbnail: "/static/thumbnail-small/torch/2-1_torch_or_numpy.jpg"
chapter: 2
description: "Torch 自称为神经网络界的 Numpy, 因为他能将 torch 产生的 tensor 放在 GPU 中加速运算 (前提是你有合适的 GPU), 就像 Numpy 会把 array 放在 CPU 中加速运算.
所以神经网络的话, 当然是用 Torch 的 tensor 形式数据最好咯. 就像 Tensorflow 当中的 tensor 一样.
当然, 我们对 Numpy 还是爱不释手的, 因为我们太习惯 numpy 的形式了. 不过 torch 看出来我们的喜爱, 他把 torch 做的和 numpy 能很好的兼容.
比如这样就能自由地转换 numpy array 和 torch tensor 了:"
post-headings:
  - 用 Numpy 还是 Torch
  - Torch 中的数学运算
---


学习资料:
  * [本节的全部代码](https://github.com/MorvanZhou/PyTorch-Tutorial/blob/master/tutorial-contents/201_torch_numpy.py){:target="_blank"}
  * [PyTorch 官网](http://pytorch.org/){:target="_blank"}
  * [PyTorch 中的常用数学计算](http://pytorch.org/docs/torch.html#math-operations){:target="_blank"}


{% include assign-heading.html %}

Torch 自称为神经网络界的 Numpy, 因为他能将 torch 产生的 tensor 放在 GPU 中加速运算 (前提是你有合适的 GPU), 就像 Numpy 会把 array 放在 CPU 中加速运算.
所以神经网络的话, 当然是用 Torch 的 tensor 形式数据最好咯. 就像 Tensorflow 当中的 tensor 一样.

当然, 我们对 Numpy 还是爱不释手的, 因为我们太习惯 numpy 的形式了. 不过 torch 看出来我们的喜爱, 他把 torch 做的和 numpy 能很好的兼容.
比如这样就能自由地转换 numpy array 和 torch tensor 了:

```python
import torch
import numpy as np

np_data = np.arange(6).reshape((2, 3))
torch_data = torch.from_numpy(np_data)
tensor2array = torch_data.numpy()
print(
    '\nnumpy array:', np_data,          # [[0 1 2], [3 4 5]]
    '\ntorch tensor:', torch_data,      #  0  1  2 \n 3  4  5    [torch.LongTensor of size 2x3]
    '\ntensor to array:', tensor2array, # [[0 1 2], [3 4 5]]
)
```


{% include google-in-article-ads.html %}

{% include assign-heading.html %}

其实 torch 中 tensor 的运算和 numpy array 的如出一辙, 我们就以对比的形式来看.
如果想了解 torch 中其它更多有用的运算符, [API就是你要去的地方](http://pytorch.org/docs/torch.html#math-operations){:target="_blank"}.

```python
# abs 绝对值计算
data = [-1, -2, 1, 2]
tensor = torch.FloatTensor(data)  # 转换成32位浮点 tensor
print(
    '\nabs',
    '\nnumpy: ', np.abs(data),          # [1 2 1 2]
    '\ntorch: ', torch.abs(tensor)      # [1 2 1 2]
)

# sin   三角函数 sin
print(
    '\nsin',
    '\nnumpy: ', np.sin(data),      # [-0.84147098 -0.90929743  0.84147098  0.90929743]
    '\ntorch: ', torch.sin(tensor)  # [-0.8415 -0.9093  0.8415  0.9093]
)

# mean  均值
print(
    '\nmean',
    '\nnumpy: ', np.mean(data),         # 0.0
    '\ntorch: ', torch.mean(tensor)     # 0.0
)
```


除了简单的计算, 矩阵运算才是神经网络中最重要的部分.
所以我们展示下矩阵的乘法. 注意一下包含了一个 numpy 中可行, 但是 torch 中不可行的方式.

```python
# matrix multiplication 矩阵点乘
data = [[1,2], [3,4]]
tensor = torch.FloatTensor(data)  # 转换成32位浮点 tensor
# correct method
print(
    '\nmatrix multiplication (matmul)',
    '\nnumpy: ', np.matmul(data, data),     # [[7, 10], [15, 22]]
    '\ntorch: ', torch.mm(tensor, tensor)   # [[7, 10], [15, 22]]
)

# !!!!  下面是错误的方法 !!!!
data = np.array(data)
print(
    '\nmatrix multiplication (dot)',
    '\nnumpy: ', data.dot(data),        # [[7, 10], [15, 22]] 在numpy 中可行
    '\ntorch: ', tensor.dot(tensor)     # torch 会转换成 [1,2,3,4].dot([1,2,3,4) = 30.0
)
```

新版本中(>=0.3.0), 关于 `tensor.dot()` 有了新的改变, 它[只能](http://pytorch.org/docs/master/torch.html){:target="_blank"}针对于一维的数组.
所以上面的有所改变.

```python
tensor.dot(tensor)     # torch 会转换成 [1,2,3,4].dot([1,2,3,4) = 30.0

# 变为
torch.dot(tensor.dot(tensor)
```