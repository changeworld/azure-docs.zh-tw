---
title: DenseNet
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure Machine Learning 設計工具中使用 Densenet-121 模組，以使用 Densenet-121 演算法建立影像分類模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: d21c7443f6b30d0b7d6e8295c0c9b060a80d9a62
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421323"
---
# <a name="densenet"></a>DenseNet

本文描述如何在 Azure Machine Learning 設計工具中使用 **densenet-121** 模組，以使用 densenet-121 演算法建立影像分類模型。  

此分類演算法是受監督的學習方法，而且需要已加上標籤的影像目錄。 

> [!NOTE]
> 此模組不支援從 studio 中的 *資料標記* 所產生的標記資料集，但僅支援從 [轉換成映射目錄](convert-to-image-directory.md) 模組所產生的標記影像目錄。 

您可以提供模型和標示的影像目錄作為 [定型 Pytorch 模型](train-pytorch-model.md)的輸入，來定型模型。 然後，您可以使用定型的模型，利用 [計分影像模型](score-image-model.md)來預測新輸入範例的值。

### <a name="more-about-densenet"></a>深入瞭解 Densenet-121

如需 Densenet-121 的詳細資訊，請參閱研究白皮書 [密集連線的卷積網路](https://arxiv.org/abs/1608.06993)。

## <a name="how-to-configure-densenet"></a>如何設定 Densenet-121

1.  在設計工具中，將 **densenet-121** 模組新增至您的管線。  

2.  針對 [ **模型名稱** ]，指定特定 densenet-121 結構的名稱，您可以從支援的 densenet-121 中選取： ' densenet121 '、' densenet161 '、' densenet169 '、' densenet201 '。

3.  針對 [ **預先定型** ]，指定是否要使用在 ImageNet 上預先定型的模型。 如果選取此選項，您可以根據所選預先定型的模型微調模型。如果取消選取，您可以從頭開始定型。

4.  若要 **有效率地使用記憶體** ，請指定是否要使用檢查點，這會比記憶體效率更高，但速度較慢。 如需詳細資訊，請參閱研究白皮書、 [記憶體有效率的 DenseNets 執行](https://arxiv.org/pdf/1707.06990.pdf)。

5.  將 **densenet-121** 模組、定型和驗證影像資料集模組的輸出連接到 [定型 Pytorch 模型](train-pytorch-model.md)。 

6. 提交管線。


## <a name="results"></a>結果

在管線執行完成後，若要使用模型進行評分，請將 [定型 Pytorch 模型](train-pytorch-model.md) 連接到 [評分影像模型](score-image-model.md)，以預測新輸入範例的值。

## <a name="technical-notes"></a>技術說明  

###  <a name="module-parameters"></a>模組參數  

| 名稱             | 範圍 | 類型    | 預設     | 描述                              |
| ---------------- | ----- | ------- | ----------- | ---------------------------------------- |
| 模型名稱       | 任意   | [模式]    | densenet201 | 特定 Densenet-121 結構的名稱     |
| 預先定型       | 任意   | 布林值 | True        | 是否要在 ImageNet 上使用預先定型的模型 |
| 記憶體效率 | 任意   | 布林值 | 否       | 是否要使用檢查點，其記憶體效率更高，但速度較慢 |

###  <a name="output"></a>輸出  

| 名稱            | 類型                    | 描述                              |
| --------------- | ----------------------- | ---------------------------------------- |
| 未定型的模型 | UntrainedModelDirectory | 可以連接到定型 Pytorch 模型的未定型 Densenet-121 模型。 |

## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 
