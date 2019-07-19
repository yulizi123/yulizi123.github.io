---
youku_id: XMjc0ODE5OTYyMA
youtube_id: FIZgQuOXk8g
b_av: 15997678
b_cid: 27669640
b_page: 17
title: Optimizer 优化器
publish-date: 2017-05-06
thumbnail: "/static/thumbnail-small/torch/3-6_optimizer.jpg"
chapter: 3
description: "这节内容主要是用 Torch 实践 这个 优化器 动画简介
中起到的几种优化器, 这几种优化器具体的优势不会在这个节内容中说了, 所以想快速了解的话, 上面的那个动画链接是很好的去处.
下图就是这节内容对比各种优化器的效果:"
post-headings:
  - 要点
  - 伪数据
  - 每个优化器优化一个神经网络
  - 优化器 Optimizer
  - 训练/出图
---


学习资料:
  * [本节的全部代码](https://github.com/MorvanZhou/PyTorch-Tutorial/blob/master/tutorial-contents/306_optimizer.py){:target="_blank"}
  * [Tensorflow 的 Optimizer 代码](https://github.com/MorvanZhou/Tensorflow-Tutorial/blob/master/tutorial-contents/304_optimizer.py){:target="_blank"}
  * [我制作的 训练优化器 动画简介]({% link _tutorials/machine-learning/ML-intro/3-06-speed-up-learning.md %})
  * [PyTorch 优化器网页](http://pytorch.org/docs/optim.html){:target="_blank"}
  * [PyTorch 官网](http://pytorch.org/){:target="_blank"}

{% include assign-heading.html %}

这节内容主要是用 Torch 实践 [这个 优化器 动画简介]({% link _tutorials/machine-learning/ML-intro/3-06-speed-up-learning.md %})
中起到的几种优化器, 这几种优化器具体的优势不会在这个节内容中说了, 所以想快速了解的话, 上面的那个动画链接是很好的去处.

下图就是这节内容对比各种优化器的效果:

{% include tut-image.html image-name="3-6-2.png" %}






{% include assign-heading.html %}

为了对比各种优化器的效果, 我们需要有一些数据, 今天我们还是自己编一些伪数据, 这批数据是这样的:

{% include tut-image.html image-name="3-6-1.png" %}


```python
import torch
import torch.utils.data as Data
import torch.nn.functional as F
import matplotlib.pyplot as plt

torch.manual_seed(1)    # reproducible

LR = 0.01
BATCH_SIZE = 32
EPOCH = 12

# fake dataset
x = torch.unsqueeze(torch.linspace(-1, 1, 1000), dim=1)
y = x.pow(2) + 0.1*torch.normal(torch.zeros(*x.size()))

# plot dataset
plt.scatter(x.numpy(), y.numpy())
plt.show()

# 使用上节内容提到的 data loader
torch_dataset = Data.TensorDataset(x, y)
loader = Data.DataLoader(dataset=torch_dataset, batch_size=BATCH_SIZE, shuffle=True, num_workers=2,)
```

{% include google-in-article-ads.html %}

{% include assign-heading.html %}

为了对比每一种优化器, 我们给他们各自创建一个神经网络, 但这个神经网络都来自同一个 `Net` 形式.

```python
# 默认的 network 形式
class Net(torch.nn.Module):
    def __init__(self):
        super(Net, self).__init__()
        self.hidden = torch.nn.Linear(1, 20)   # hidden layer
        self.predict = torch.nn.Linear(20, 1)   # output layer

    def forward(self, x):
        x = F.relu(self.hidden(x))      # activation function for hidden layer
        x = self.predict(x)             # linear output
        return x

# 为每个优化器创建一个 net
net_SGD         = Net()
net_Momentum    = Net()
net_RMSprop     = Net()
net_Adam        = Net()
nets = [net_SGD, net_Momentum, net_RMSprop, net_Adam]
```

{% include assign-heading.html %}

接下来在创建不同的优化器, 用来训练不同的网络. 并创建一个 `loss_func` 用来计算误差.
我们用几种常见的优化器, `SGD`, `Momentum`, `RMSprop`, `Adam`.

```python
# different optimizers
opt_SGD         = torch.optim.SGD(net_SGD.parameters(), lr=LR)
opt_Momentum    = torch.optim.SGD(net_Momentum.parameters(), lr=LR, momentum=0.8)
opt_RMSprop     = torch.optim.RMSprop(net_RMSprop.parameters(), lr=LR, alpha=0.9)
opt_Adam        = torch.optim.Adam(net_Adam.parameters(), lr=LR, betas=(0.9, 0.99))
optimizers = [opt_SGD, opt_Momentum, opt_RMSprop, opt_Adam]

loss_func = torch.nn.MSELoss()
losses_his = [[], [], [], []]   # 记录 training 时不同神经网络的 loss
```

{% include assign-heading.html %}

接下来训练和 loss 画图.

```python
for epoch in range(EPOCH):
    print('Epoch: ', epoch)
    for step, (b_x, b_y) in enumerate(loader):

        # 对每个优化器, 优化属于他的神经网络
        for net, opt, l_his in zip(nets, optimizers, losses_his):
            output = net(b_x)              # get output for every net
            loss = loss_func(output, b_y)  # compute loss for every net
            opt.zero_grad()                # clear gradients for next train
            loss.backward()                # backpropagation, compute gradients
            opt.step()                     # apply gradients
            l_his.append(loss.data.numpy())     # loss recoder
```

{% include tut-image.html image-name="3-6-2.png" %}

`SGD` 是最普通的优化器, 也可以说没有加速效果, 而 `Momentum` 是 `SGD` 的改良版, 它加入了动量原则.
后面的 `RMSprop` 又是 `Momentum` 的升级版. 而 `Adam` 又是 `RMSprop` 的升级版.
不过从这个结果中我们看到, `Adam` 的效果似乎比 `RMSprop` 要差一点. 所以说并不是越先进的优化器, 结果越佳.
我们在自己的试验中可以尝试不同的优化器, 找到那个最适合你数据/网络的优化器.

所以这也就是在我 [github 代码](https://github.com/MorvanZhou/PyTorch-Tutorial/blob/master/tutorial-contents/306_optimizer.py){:target="_blank"} 中的每一步的意义啦.


