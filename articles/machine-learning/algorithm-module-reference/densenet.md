---
title: DenseNet
titleSuffix: Azure Machine Learning
description: 了解如何使用 densenet 算法创建图像分类模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: db0e2f90ee45d4e1c0173cbc037084793a66e149
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84449981"
---
# <a name="densenet"></a>DenseNet

本文介绍如何使用 Azure 机器学习设计器（预览版）中的**DenseNet**模块，通过 DenseNet 算法创建图像分类模型。  

此分类算法是一种监督式学习方法，需要一个标记的数据集。 若要详细了解如何获取标记的图像目录，请参阅 [Convert to Image Directory](convert-to-image-directory.md)（转换为图像目录）模块。 可通过提供模型和标记的图像目录作为[训练 Pytorch 模型](train-pytorch-model.md)模块的输入来训练模型。 然后，可使用训练后的模型来预测使用[为图像模型评分](score-image-model.md)模块的新输入示例的值。

### <a name="more-about-densenet"></a>详细了解 DenseNet

有关更多详细信息，请参阅 [Densely Connected Convolutional Networks](https://arxiv.org/abs/1608.06993)（密集连接的卷积网络）。

## <a name="how-to-configure-densenet"></a>如何配置 DenseNet

1.  在设计器中将 DenseNet 模块添加到管道。  

2.  对于 "**模型名称**"，请指定特定 densenet 结构的名称，并且可以从受支持的 densenet 中进行选择： "densenet121"、"densenet161"、"densenet169"、"densenet201"。

3.  对于“预先训练”，请指定是否使用在 ImageNet 上预先训练的模型。 如果选择此选项，您可以根据所选的预先训练的模型来微调模型;如果取消选择，则可以从头开始训练。

4.  对于“内存效率”，请指定是否使用检查点（使用检查点可以提高内存效率，但速度较慢）。 有关详细信息，请参阅 https://arxiv.org/pdf/1707.06990.pdf 。

5.  将**DenseNet**模块、定型和验证图像数据集模块的输出连接到[定型 Pytorch 模型](train-pytorch-model.md)。 

6. 提交管道。


## <a name="results"></a>结果

管道运行完成后，若要使用模型进行评分，请将[训练 Pytorch 模型](train-pytorch-model.md)模块连接到[为图像模型评分](score-image-model.md)模块，以预测新输入示例的值。

## <a name="technical-notes"></a>技术说明  

###  <a name="module-parameters"></a>模块参数  

| 名称             | 范围 | 类型    | 默认     | 说明                              |
| ---------------- | ----- | ------- | ----------- | ---------------------------------------- |
| 模型名称       | 任意   | Mode    | densenet201 | 特定 densenet 结构的名称     |
| 预先训练       | 任意   | 布尔 | True        | 是否使用在 ImageNet 上预先训练的模型 |
| 内存效率 | 任意   | 布尔 | False       | 是否使用检查点（使用检查点可以提高内存效率，但速度较慢） |

###  <a name="output"></a>输出  

| 名称            | 类型                    | 说明                              |
| --------------- | ----------------------- | ---------------------------------------- |
| 未训练的模型 | UntrainedModelDirectory | 可以连接到定型 Pytorch 模型的未训练的 densenet 模型。 |

## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 
