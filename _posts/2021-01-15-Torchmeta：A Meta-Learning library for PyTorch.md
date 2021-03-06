---
layout: mypost
title: Torchmeta：A Meta-Learning library for PyTorch
categories: [translate, meta-learning]
---


> *Google翻译 + 个人简单排版，[原文在此](https://arxiv.org/abs/1909.06576v1)*

## 摘要
文献中不断引入标准化基准测试有助于加速元学习研究的最新进展。 它们提供了一种在不同算法之间进行公平比较的方法，而可用的广泛数据集可以完全控制此评估的复杂性。但是，对于大多数可在线使用的代码，数据管道通常特定于一个数据集，而对另一数据集进行测试需要大量的返工。 我们介绍了Torchmeta，它是一个基于PyTorch构建的库，该库通过为大多数标准基准的数据加载器提供了几步分类和回归的无缝数据，并且能够对多个数据集的元学习算法进行无缝，一致的评估，并提供了新的meta- 数据集抽象。它还具有PyTorch的一些扩展，以简化与元学习算法兼容的模型的开发。 可以在[这里](https://github.com/tristandeleu/pytorch-meta)找到代码。

## 1. 简介
与机器学习的任何子领域一样，标准化基准的存在在过去几年中我们在元学习研究中观察到的进步中起着至关重要的作用。 它们使对现有方法的评估更容易，更公平，从而为开发新的元学习算法提供了参考。 这会形成一个良性循环，植根于这些定义明确的任务套件中。 不过，与诸如MNIST（LeCun等，1998）或ImageNet（Russakovsky等，2015）之类的监督学习中的现有数据集不同，元学习的基准在于数据集的数据集中。 在大多数元学习项目实现适合其方法的自己的特定数据加载组件的程度上，这给数据管道增加了一层复杂性。 在输入级别上缺乏标准会导致围绕每种元学习算法的机制产生差异，这使得公平的比较更具挑战性。

尽管每个项目的实现可能有所不同，但是创建这些数据集的数据集的过程在各个任务中通常是相同的。 在本文中，我们介绍了Torchmeta，这是一个基于PyTorch深度学习框架构建的元学习库（Paszke et al。，2017），为大多数标准数据集提供数据加载器，以进行少量分类和回归。  Torchmeta对所有可用基准使用相同的界面，从而使不同数据集之间的转换尽可能无缝。 受先前在任务之间设计统一接口（例如OpenAI Gym（Brockman等人，2016）以进行强化学习）的启发，Torchmeta的目标是创建一个框架，研究人员可以围绕该框架构建自己的元学习算法，而不是 而不是使数据管道适应其方法。 通过将元数据集与算法本身分离，这种新的抽象促进了代码重用。

除了这些数据加载器外，Torchmeta还包括PyTorch的扩展，以简化与经典元学习算法兼容的模型的创建，这些算法有时需要更高阶的微分（Finn等人，2017; Finn，2018; Rusu等人，  2018; Grant等人，2018）。 本文概述了Torchmeta当前可用的功能，其组织如下：第2节概述了库中可用的数据加载器。 在第3节中，我们重点介绍PyTorch模块的扩展，该模块称为“元模块”，专门为元学习而设计，并在第4节中进行了讨论。

## 2. 小样本学习的数据加载器
该库提供了对应于元学习文献中经典的几次快照分类和回归问题的数据集。 创建该接口是为了支持数据集之间的模块性（分类和回归），以简化对全套基准测试的评估过程； 我们将在以下各节中详细介绍此接口。 此外，Torchmeta的数据加载器与PyTorch的标准数据组件完全兼容，例如Dataset和DataLoader。 在进入库的细节之前，我们首先简要回顾一下问题的设置。

为了平衡几次学习中固有的数据缺乏，元学习算法从数据集Dmeta = {D1，...收集了一些先验知识。  。  。  ，Dn}，称为元训练集。在少量学习的情况下，每个元素Di仅包含几个输入/输出对（x，y），其中y取决于问题的性质。 例如，这些数据集可以包含过去执行的不同任务的示例。Torchmeta提供了一种解决方案，可以使用最少的问题特定组件来自动创建每个数据集Di。

#### 2.1 小样本回归
文献中很少有人使用的回归问题中的大多数是通过不同功能的输入和输出之间的简单回归问题，其中每个功能都对应于一项任务。对这些功能进行参数设置，以允许任务之间的可变性，同时在各个任务之间保持不变的“主题”。 例如，这些函数可以是fi（x）= ai sin（x + bi）形式的正弦波，其中a和b在一定范围内变化（Finn等人，2017）。 在Torchmeta中，元训练集继承自称为MetaDataset的对象，并且每个数据集Di（i = 1，...，n，其中n由用户定义）对应于该函数的特定参数选择，所有 在元训练集创建时采样一次的参数。 一旦知道了函数的参数，我们就可以通过在给定范围内对输入进行采样并将其提供给函数来创建数据集。

该库目前包含3个玩具问题：正弦波（Finn等人，2017），谐波函数（即   两个正弦波之和（Lacoste等人，2018）以及正弦曲线和线之和（Finn等人，2018）。 以下是如何实例化正弦波问题的元训练集的示例： 
```python
torchmeta.toy.Sinusoid(num_samples_per_task=10, num_tasks=1_000_000, noise_std=None)
```

#### 2.2 小样本分类
对于小样本分类问题，数据集Di的创建通常遵循两个步骤：首先从大量候选集中采样N个类（对应于“ N向分类”中的N），然后选择k 每个班级选择示例（对应于“ k-shot学习”中的k）。这是一个分为两步的过程，它是作为继承自MetaDataset的称为CombinationMetaDataset的对象的一部分而进行的操作，只要用户指定了特定于问题的大量类候选者即可。 此外，为了鼓励元学习的可重复性，每个任务都与唯一的标识符（类标识符的N元组）相关联。 选择任务后，对象将返回数据集Di以及来自相应类集的所有示例。 在第2.3节中，我们将介绍如何将Di进一步分为元学习中常见的训练和测试数据集。

该库目前包含5个镜头分类问题：Omniglot（Lake等人，2015，2019），Mini-ImageNet（Vinyals等人，2016; Ravi和Larochelle，2017），Tiered-ImageNet（Ren等人，  2018），CIFAR-FS（贝蒂内托等人，2018）和Fewshot-CIFAR100（Oreshkin等人，2018）。 以下是如何实例化5路Mini-ImageNet的元训练集的示例：
```python
torchmeta.datasets.MiniImagenet("data", num_classes_per_task=5, meta_train=True, download=True)
```
Torchmeta还包括有用的功能，以增加具有变体的班级候选者库，例如旋转图像（Santoro等人，2016）。

#### 2.3 训练和测试数据集的拆分
在元学习中，通常将每个数据集Di分为两部分：训练集（或支持集），以使模型适应当前的任务；测试集（或查询集），用于评估和元优化。 重要的是要确保这两个部分不会重叠：尽管任务保持不变，但是训练和测试集中都没有例子。 为了确保这一点，Torchmeta在数据集上引入了一个称为Splitter的包装器，该包装器负责创建训练和测试数据集，以及可选地对数据进行混排。 这是一个如何实例化基于Mini-Imagenet的5向1发分类问题的元训练集的示例：
```python
dataset = torchmeta.datasets.MiniImagenet("data", num_classes_per_task=5, meta_train=True, download=True)
dataset = torchmeta.transforms.ClassSplitter(dataset, num_train_per_class=1, num_test_per_class=15, shuffle=True)
```
除了元训练集之外，大多数基准测试还提供了元测试集，用于对元学习算法的总体评估（以及可能的元验证集）。 创建MetaDataset对象时，可以使用meta_test = True（或meta_val = True）而不是meta_train = True来选择这些不同的元数据集。

#### 2.4 元数据加载器
可以迭代第2.1和2.2节中介绍的对象，以从元训练集中生成数据集。 这些数据集是PyTorch数据集对象，因此可以作为任何标准数据管道的一部分（与DataLoader组合）包含在内。 尽管如此，大多数元学习算法在处理一批任务时效果更好。 与在PyTorch中将示例与DataLoader一起批处理的方式类似，Torchmeta公开了一个MetaDalaoader，该对象可以在迭代时产生大量任务。 特别地，这样的元数据加载器能够输出大张量，其中包含批处理中来自不同任务的所有示例。例如：
```python
# Helper function, equivalent to Section 2.3
dataset = torchmeta.datasets.helpers.miniimagenet("data", shots=1, ways=5, meta_train=True, download=True)
dataloader = torchmeta.utils.data.BatchMetaDataLoader(dataset, batch_size=16)

for batch in dataloader:
    train_inputs, train_labels = batch["train"] # Size (16, 5, 3, 84, 84) & (16, 5)
```
## 3. 元学习模块
PyTorch中的模型是从称为模块的基本组件创建的。 相当于神经网络中某个层的每个基本模块都包含该层的计算图及其参数。 模块将其参数视为其计算图的组成部分； 在标准的监督学习中，这足以训练带有反向传播的模型。 但是，一些元学习算法需要通过参数更新（例如，梯度更新，Finn等，2017）进行反向传播，以进行元优化（或“外环”），因此涉及高阶微分。 尽管PyTorch可以将高阶微分作为其自动微分模块的一部分（Paszke et al。，2017），但可以用完整的计算图来替换基本模块的一个参数（即参数的更新），而无需改变方法渐变流，并不明显。

通过更新参数进行反向传播是基于梯度的金属化方法（Finn等人，2017; Finn，2018; Grant等人，2018; Lee等人，2019）和各种混合方法（Rusu）的关键要素 等人，2018; Zintgraf等人，2019）。 因此，适应PyTorch中的现有模块至关重要，以便它们可以处理任意计算图来替代这些参数。  Torchmeta采取的方法是扩展这些模块，并保留提供新参数作为附加输入的选项。 这些新对象称为MetaModule，它们的默认行为（即，未指定任何额外的参数）等同于它们的PyTorch对应对象。 否则，如果指定了额外的参数（例如，梯度下降的一步的结果），则MetaModule会将其视为计算图的一部分，并且反向传播将按预期进行。
![图一](https://i.loli.net/2021/01/15/imT7lzcrdoQRZhs.png)
图1显示了线性模块（称为MetaLinear）的扩展如何工作（带有和不带有附加参数）以及对渐变的影响。 左图显示了作为参数W和b的容器的元模块的实例化，以及带有权重和偏差参数占位符的计算图。 中间的图显示了MetaLinear元模块的默认行为，其中的占位符用W＆b代替：这等效于PyTorch的Linear模块。 最后，右图显示了如何使用完整的计算图来填充这些占位符，就像梯度下降的一步一样（Finn et al。，2017）。 在后一种情况下，外循环更新中必需的Louter相对于W的坡度可以正确地一直流到参数W。

## 4. 讨论
数据管道的可重复性具有挑战性。更具挑战性的是，即使早期的一些作品已经在如今成为经典的基准上进行了评估，但它们却没有披露可用于少数训练分类的元训练和元测试（以及可能的元验证）的类集。  。 例如，虽然在（Vinyals等人，2016）中引入了Mini-ImageNet数据集，但（Ravi和Larochelle，2017）中使用的分割现在已被社区广泛接受为正式数据集。  Torchmeta之类的库的优点是可以标准化这些基准，以避免造成任何混淆。

Torchmeta的另一个目标是使更大范围的社区可以访问元学习。 我们希望类似于OpenAI Gym（Brockman等，2016）如何通过在统一界面下访问多个环境来促进强化学习的进步，Torchmeta可以对元学习研究产生同等影响。 该库与PyTorch的计算机视觉库PyTorch和Torchvision完全兼容，从而简化了与现有项目的集成。

尽管Torchmeta已经具有大量的数据集，可以进行少量的回归和分类，并且涵盖了元学习文献中的大多数标准基准，但库的当前版本中一个值得注意的缺失数据集是元数据集（Triantafillou等 等（2019）。  MetaDataset是一个独特且更复杂的少数问题分类问题，每个任务具有不同数量的类。 尽管元数据集可以满足提出的抽象要求，但它需要一个漫长的初始处理阶段，这将使该库的自动下载和处理功能不切实际。 因此，将其集成留作未来的工作。 同时，我们认为，Torchmeta可以为将来创建更好的基准提供一种结构，并且是可重复进行的元学习研究的关键一步。

## 5. 致谢
我们要感谢Mila的学生对库进行了测试，他们在开发过程中提供了宝贵的反馈意见。Tristan Deleu得到了Druide Informatique的解毒剂奖学金的支持。

## 参考文献
> *此处略去，请查看原文*