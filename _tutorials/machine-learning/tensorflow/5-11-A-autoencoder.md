---
youku_id: XMTgwNDc1NjYwMA
youtube_id: w8HmXgXnVEo
b_av: 16001891
b_cid: 27636914
b_page: 36
title: 什么是自编码 (Autoencoder)
description: 自编码 autoencoder 是一种什么码呢. 他是不是 条形码? 二维码? 打码? 其中的一种呢? NONONONO. 和他们统统没有关系. 自编码是一种神经网络的形式.如果你一定要把他们扯上关系, 我想也只能这样解释啦.
chapter: 5
thumbnail: /static/thumbnail-small/ML-intro/autoencoder.png
post-headings:
  - 压缩与解压
  - 编码器 Encoder
ref-path: _tutorials/machine-learning/ML-intro/2-5-autoencoder.md
---


{% assign post = site.tutorials | where: "category", "ML-intro" | where: "path", page.ref-path %}

{{ post[0].content }}