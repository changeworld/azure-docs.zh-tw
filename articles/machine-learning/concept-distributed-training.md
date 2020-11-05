---
title: 什麼是分散式訓練？
titleSuffix: Azure Machine Learning
description: 瞭解何種類型的分散式訓練 Azure Machine Learning 支援，以及適用于分散式訓練的開放原始碼架構整合。
services: machine-learning
ms.service: machine-learning
author: nibaccam
ms.author: nibaccam
ms.subservice: core
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 4e1972e0530656bf6cc0e6319191ab4ebbfbfddb
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93358570"
---
# <a name="distributed-training-with-azure-machine-learning"></a>使用 Azure Machine Learning 的分散式訓練

在本文中，您將瞭解分散式訓練，以及 Azure Machine Learning 如何為深度學習模型提供支援。 

在分散式訓練中，定型模型的工作負載會在多個迷你處理器（稱為背景工作節點）中分割並共用。 這些背景工作節點會平行運作以加速模型定型。 分散式訓練可用於傳統 ML 模型，但更適合用於計算和耗費時間的工作，例如 [深度學習](concept-deep-learning-vs-machine-learning.md) 以定型深度類神經網路。 

## <a name="deep-learning-and-distributed-training"></a>深度學習和分散式訓練 

分散式訓練有兩種主要類型： [資料平行](#data-parallelism) 處理原則和 [模型平行](#model-parallelism)處理。 針對深度學習模型的分散式訓練， [Python 中的 AZURE MACHINE LEARNING SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) 支援與熱門架構（PyTorch 和 TensorFlow）的整合。 這兩種架構都採用資料平行處理來進行分散式訓練，並可利用 [horovod](https://horovod.readthedocs.io/en/latest/summary_include.html) 來優化計算速度。 

* [使用 PyTorch 的分散式訓練](how-to-train-pytorch.md#distributed-training)

* [使用 TensorFlow 的分散式訓練](how-to-train-tensorflow.md#distributed-training)

針對不需要分散式訓練的 ML 模型，請參閱使用 Python SDK 將模型 [定型 Azure Machine Learning](concept-train-machine-learning-model.md#python-sdk) ，以瞭解使用 Python SDK 來定型模型的不同方式。

## <a name="data-parallelism"></a>資料平行處理原則

資料平行處理是最簡單的方法，可執行兩種分散式訓練方法，且對大部分的使用案例都已足夠。

在此方法中，資料會分割成資料分割，其中的資料分割數目等於計算叢集中可用節點的總數。 此模型會在每個背景工作節點中複製，而且每個背景工作會在自己的資料子集上運作。 請記住，每個節點都必須有容量來支援正在定型的模型，也就是模型必須完全符合每個節點。 下圖提供此方法的視覺示範。

![資料平行處理原則-概念-圖表](./media/concept-distributed-training/distributed-training.svg)

每個節點都會在其定型範例和標示輸出的預測之間個別計算錯誤。 接著，每個節點會根據錯誤來更新其模型，而且必須將其所有變更傳達給其他節點，以更新其對應的模型。 這表示，背景工作節點必須在批次計算結束時同步處理模型參數或漸層，以確保它們正在定型一致的模型。 

## <a name="model-parallelism"></a>模型平行處理

在模型平行處理原則中（也稱為網路平行處理），模型會分割成不同的元件，而這些元件可以在不同的節點中同時執行，而且每個節點都會在相同的資料上執行。 這個方法的擴充性取決於演算法的工作平行處理程度，而且比資料平行處理原則更複雜。 

在模型平行處理原則中，背景工作節點只需要同步處理共用參數，通常是針對每個順向或回溯傳播步驟進行一次。 此外，較大的模型不會有問題，因為每個節點都是在相同定型資料的模型子區段上運作。

## <a name="next-steps"></a>後續步驟

* 瞭解如何搭配 Python SDK [使用計算目標進行模型定型](how-to-set-up-training-targets.md) 。
* 如需技術範例，請參閱 [參考架構案例](/azure/architecture/reference-architectures/ai/training-deep-learning)。
* [使用 TensorFlow 將 ML 模型定型](how-to-train-tensorflow.md)。
* [使用 PyTorch 將 ML 模型定型](how-to-train-pytorch.md)。