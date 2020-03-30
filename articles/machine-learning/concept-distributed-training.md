---
title: 什麼是分散式培訓？
titleSuffix: Azure Machine Learning
description: 瞭解分散式培訓以及 Azure 機器學習如何支援它。
services: machine-learning
ms.service: machine-learning
author: nibaccam
ms.author: nibaccam
ms.subservice: core
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: a0d5bf795e4759a105b9a235770f37aa10bd6751
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385541"
---
# <a name="distributed-training-with-azure-machine-learning"></a>使用 Azure 機器學習進行分散式培訓

在本文中，您將瞭解分散式培訓以及 Azure 機器學習如何支援它用於深度學習模型。 

在分散式訓練中，訓練模型的工作負載在多個微型處理器（稱為輔助節點）之間拆分並共用。 這些輔助節點並行工作以加快模型訓練。 分散式訓練可用於傳統的 ML 模型，但更適合計算和時間密集型任務，如用於訓練深層神經網路[的深層學習](concept-deep-learning-vs-machine-learning.md)。

## <a name="deep-learning-and-distributed-training"></a>深度學習和分散式培訓 

分散式訓練主要有兩種類型：[資料並行性和](#data-parallelism)[模型並行性](#model-parallelism)。 對於深度學習模型的分散式培訓[，Python 中的 Azure 機器學習 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)支援與常用框架 PyTorch 和 TensorFlow 的集成。 這兩個框架都採用資料並行性進行分散式培訓，並可以利用[horovod](https://horovod.readthedocs.io/en/latest/summary_include.html)優化計算速度。 

* [使用 PyTorch 進行分散式培訓](how-to-train-pytorch.md#distributed-training)

* [使用 TensorFlow 進行分散式培訓](how-to-train-tensorflow.md#distributed-training)

對於不需要分散式訓練的 ML 模型，請參閱使用[Azure 機器學習訓練模型](concept-train-machine-learning-model.md#python-sdk)，瞭解使用 Python SDK 訓練模型的不同方法。

## <a name="data-parallelism"></a>資料平行處理原則

資料並行性是兩種分散式培訓方法最容易實現的，對於大多數用例來說已經足夠了。

在此方法中，資料被劃分為分區，其中分區數等於計算群集中的可用節點總數。 模型在每個輔助節點中複製，每個輔助角色都按其自己的資料子集操作。 請記住，每個節點必須具有支援正在訓練的模型的能力，即模型必須完全適合每個節點。

每個節點獨立計算其訓練樣本的預測和標記的輸出之間的錯誤。 反過來，每個節點會根據錯誤更新其模型，並且必須將其所有更改傳達給其他節點以更新其相應的模型。 這意味著輔助節點需要在批次處理計算結束時同步模型參數或梯度，以確保它們正在訓練一致的模型。 

## <a name="model-parallelism"></a>模型並行性

在模型並行性（也稱為網路並行性）中，模型被分割到不同的部分，這些部分可以在不同的節點中同時運行，並且每個部分都將在同一資料上運行。 該方法的可伸縮性取決於演算法的任務並行化程度，實現比資料並行性複雜。 

在模型並行性中，輔助節點只需要同步共用參數，通常每個向前或向後傳播步驟同步一次。 此外，較大的模型不是問題，因為每個節點都根據模型的一個子部分對相同的訓練資料進行操作。

## <a name="next-steps"></a>後續步驟

* 瞭解如何使用 Python SDK[設置訓練環境](how-to-set-up-training-targets.md)。
* 有關技術示例，請參閱[參考體系結構方案](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/training-deep-learning)。
* [用TensorFlow訓練ML模型](how-to-train-tensorflow.md)。
* [用PyTorch訓練ML模型](how-to-train-pytorch.md)。 