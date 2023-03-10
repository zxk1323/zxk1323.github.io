---
title: 'Hadoop-Yarn'
date: 2021-07-01
permalink: /posts/2021/06/Hadoop-Yarn/
tags:
  - Hadoop
  - Yarn
---
### Yarn
### 任务提交流程
### 资源调度模型
Yarn中有三种调度器可以选择：FIFO、Capacity、Fair。
apache版本默认使用capacity。CHD版本默认使用fair。
1. FIFO Scheduler 先来先服务
- FIFO Scheduler 把应用按提交的顺序排成一个队列，这是一个先进先出队列，在进行资源分配的时候，先给队列中最头上的应用进行分配资源，待最头上的应用需求满足后再给下一个分配，以此类推。 
- FIFO Scheduler 是最简单也是最容易理解的调度器，也不需要任何配置，但它并不适用于共享集群。大的应用可能会占用所有集群资源，这就导致其它应用被阻塞，比如有个大任务在执行，占用了全部的资源，再提交一个小任务，则此小任务会一直被阻塞。 
2. Capacity Scheduler 能力调度器
3. Fair Scheduler 公平调度器