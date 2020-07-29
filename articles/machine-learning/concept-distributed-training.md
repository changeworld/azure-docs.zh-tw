---
title: 什麼是分散式訓練？
titleSuffix: Azure Machine Learning
description: 深入瞭解分散式訓練，以及 Azure Machine Learning 的支援方式。
services: machine-learning
ms.service: machine-learning
author: nibaccam
ms.author: nibaccam
ms.subservice: core
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 52716e070437dd7a6b3b880a5a7f3a4afafe8738
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82995030"
---
# <a name="distributed-training-with-azure-machine-learning"></a>Azure Machine Learning 的分散式訓練

在本文中，您將瞭解分散式訓練，以及 Azure Machine Learning 如何為深度學習模型提供支援。 

在分散式訓練中，定型模型的工作負載會分割並在多個迷你處理器（稱為背景工作節點）之間共用。 這些背景工作節點會以平行方式運作，以加速模型定型。 分散式訓練可用於傳統的 ML 模型，但較適合計算和耗費大量時間的工作，例如[深入學習](concept-deep-learning-vs-machine-learning.md)訓練深度類神經網路。 

## <a name="deep-learning-and-distributed-training"></a>深度學習與分散式訓練 

分散式訓練有兩種主要類型：[資料平行](#data-parallelism)處理原則和[模型平行](#model-parallelism)處理。 針對深度學習模型的分散式訓練， [Python 中的 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)支援與熱門架構（PyTorch 和 TensorFlow）的整合。 這兩種架構都採用資料平行處理來進行分散式定型，並可利用[horovod](https://horovod.readthedocs.io/en/latest/summary_include.html)來優化計算速度。 

* [使用 PyTorch 的分散式訓練](how-to-train-pytorch.md#distributed-training)

* [使用 TensorFlow 的分散式訓練](how-to-train-tensorflow.md#distributed-training)

針對不需要分散式定型的 ML 模型，請參閱使用[Azure Machine Learning 來定型模型](concept-train-machine-learning-model.md#python-sdk)，以瞭解使用 Python SDK 來定型模型的不同方式。

## <a name="data-parallelism"></a>資料平行處理原則

資料平行處理是最簡單的方式，可執行兩種分散式訓練方法，而且對大部分的使用案例都已足夠。

在這種方法中，資料會分割成磁碟分割，其中的磁碟分割數目等於計算叢集中可用節點的總數。 此模型會在每個背景工作角色節點中複製，而每個背景工作角色會在其本身的資料子集上運作。 請記住，每個節點都必須有容量來支援正在定型的模型，這也就是模型必須完全納入每個節點上。 下圖提供此方法的視覺化示範。

![資料平行處理原則-概念-圖表](./media/concept-distributed-training/distributed-training.svg)

每個節點都會針對其定型範例和標示的輸出，分別計算其預測之間的錯誤。 接著，每個節點都會根據錯誤來更新其模型，而且必須將其所有變更傳達給其他節點，以更新其對應的模型。 這表示背景工作節點必須同步處理批次計算結尾的模型參數或漸層，以確保它們會定型一致的模型。 

## <a name="model-parallelism"></a>模型平行處理

在模型平行處理原則（也稱為網路平行處理原則）中，模型會分割成可在不同節點中同時執行的不同部分，而且每個都將在相同的資料上執行。 這個方法的擴充性取決於演算法的工作平行處理程度，而且比起資料平行處理的執行更複雜。 

在模型平行處理原則中，背景工作角色節點只需要同步處理共用參數，通常是每個向前或向後傳播步驟一次。 此外，較大的模型並不是問題，因為每個節點都是在相同的定型資料上的模型子區段上運作。

## <a name="next-steps"></a>後續步驟

* 瞭解如何使用 Python SDK[設定定型環境](how-to-set-up-training-targets.md)。
* 如需技術範例，請參閱[參考架構案例](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/training-deep-learning)。
* [使用 TensorFlow 將 ML 模型定型](how-to-train-tensorflow.md)。
* [使用 PyTorch 將 ML 模型定型](how-to-train-pytorch.md)。 