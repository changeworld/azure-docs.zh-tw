---
title: ResNet
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 ResNet 演算法建立影像分類模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: bd0431a8e503605c6137d948cf207c1bd2fa45b4
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91442042"
---
# <a name="resnet"></a>ResNet

本文描述如何在 Azure Machine Learning 設計工具中使用 **ResNet** 模組，以使用 ResNet 演算法建立影像分類模型。  

此分類演算法是受監督的學習方法，而且需要已加上標籤的資料集。 
> [!NOTE]
> 此模組不支援從 studio 中的 *資料標記* 所產生的標記資料集，但僅支援從 [轉換成映射目錄](convert-to-image-directory.md) 模組所產生的標記影像目錄。 

您可以將模型和加上標籤的影像目錄提供為 [定型 Pytorch 模型](train-pytorch-model.md)的輸入，藉此定型模型。 然後，您可以使用定型的模型，利用 [計分影像模型](score-image-model.md)來預測新輸入範例的值。

### <a name="more-about-resnet"></a>深入瞭解 ResNet

如需 ResNet 的詳細資訊，請參閱 [本白皮書](https://pytorch.org/docs/stable/torchvision/models.html?highlight=resnext101_32x8d#torchvision.models.resnext101_32x8d) 。

## <a name="how-to-configure-resnet"></a>如何設定 ResNet

1.  在設計工具中，將 **ResNet** 模組新增至您的管線。  

2.  針對 [ **模型名稱**]，指定特定 ResNet 結構的名稱，您可以從支援的 ResNet 中選取： ' resnet18 '、' resnet34 '、' resnet50 '、' resnet101 '、' resnet152 '、' resnet152 '、' resnext50 \_ 32x4d '、' resnext101 \_ 32x8d '、' wide_resnet50 \_ 2 '、' wide_resnet101 \_ 2 '。

3.  針對 [ **預先定型**]，指定是否要使用在 ImageNet 上預先定型的模型。 如果選取此選項，您可以根據所選預先定型的模型微調模型。如果取消選取，您可以從頭開始定型。

4.  將 **densenet-121** 模組、定型和驗證影像資料集模組的輸出連接到 [定型 Pytorch 模型](train-pytorch-model.md)。 

5. 提交管線。

## <a name="results"></a>結果

在管線執行完成後，若要使用模型進行評分，請將 [定型 Pytorch 模型](train-pytorch-model.md) 連接到 [評分影像模型](score-image-model.md)，以預測新輸入範例的值。

## <a name="technical-notes"></a>技術說明  

###  <a name="module-parameters"></a>模組參數  

| 名稱       | 範圍 | 類型    | 預設           | 描述                              |
| ---------- | ----- | ------- | ----------------- | ---------------------------------------- |
| 模型名稱 | 任意   | 模式    | resnext101 \_ 32x8d | 特定 ResNet 結構的名稱       |
| 預先定型 | 任意   | 布林值 | True              | 是否要在 ImageNet 上使用預先定型的模型 |
|            |       |         |                   |                                          |

###  <a name="output"></a>輸出  

| 名稱            | 類型                    | 描述                              |
| --------------- | ----------------------- | ---------------------------------------- |
| 未定型的模型 | UntrainedModelDirectory | 可以連接到定型 Pytorch 模型的未定型 ResNet 模型。 |

## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 