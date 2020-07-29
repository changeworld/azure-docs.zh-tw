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
ms.date: 05/26/2020
ms.openlocfilehash: ea8c13e134eceeb27bd064e794d46d711092a867
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84450102"
---
# <a name="resnet"></a>ResNet

本文說明如何使用 Azure Machine Learning 設計工具（預覽）中的**ResNet**模組，利用 ResNet 演算法來建立影像分類模型。  

此分類演算法是一種受監督的學習方法，而且需要已加上標籤的資料集。 如需如何取得已加上標籤之影像目錄的詳細資訊，請參閱[轉換成映射目錄](convert-to-image-directory.md)模組。 您可以藉由提供模型和加上標籤的影像目錄作為[定型 Pytorch 模型](train-pytorch-model.md)的輸入，來定型模型。 定型的模型接著可用來使用[分數影像模型](score-image-model.md)來預測新輸入範例的值。

### <a name="more-about-resnet"></a>深入瞭解 ResNet

如需 ResNet 的詳細資訊，請參閱[這篇文章](https://pytorch.org/docs/stable/torchvision/models.html?highlight=resnext101_32x8d#torchvision.models.resnext101_32x8d)。

## <a name="how-to-configure-resnet"></a>如何設定 ResNet

1.  在設計工具中，將**ResNet**模組新增至您的管線。  

2.  針對 [**模型名稱**]，指定特定 ResNet 結構的名稱，您可以從支援的 ResNet 中選取： ' resnet18 '、' resnet34 '、' resnet50 '、' resnet101 '、' resnet152 '、' resnet152 '、' resnext50 \_ 32x4d '、' resnext101 \_ 32x8d '、' wide_resnet50 \_ 2 '、' wide_resnet101 \_ 2 '。

3.  針對 [**預先定型**]，指定是否要在 ImageNet 上使用預先定型的模型。 若選取此選項，您可以根據選取的預先定型模型微調模型;如果取消選取，您可以從頭開始訓練。

4.  將 [ **DenseNet**模組]、[定型] 和 [驗證影像資料集] 模組的輸出連接到 [[定型 Pytorch] 模型](train-pytorch-model.md)。 

5. 提交管線。

## <a name="results"></a>結果

完成管線執行之後，若要使用模型進行評分，請將[定型 Pytorch 模型](train-pytorch-model.md)連接到[計分影像模型](score-image-model.md)，以預測新輸入範例的值。

## <a name="technical-notes"></a>技術說明  

###  <a name="module-parameters"></a>模組參數  

| Name       | 範圍 | 類型    | 預設           | 描述                              |
| ---------- | ----- | ------- | ----------------- | ---------------------------------------- |
| 模型名稱 | 任意   | [模式]    | resnext101 \_ 32x8d | 特定 ResNet 結構的名稱       |
| 預先定型 | 任意   | 布林值 | True              | 是否要在 ImageNet 上使用預先定型的模型 |
|            |       |         |                   |                                          |

###  <a name="output"></a>輸出  

| 名稱            | 類型                    | Description                              |
| --------------- | ----------------------- | ---------------------------------------- |
| 未定型的模型 | UntrainedModelDirectory | 可以連接到定型 Pytorch 模型的未訓練 ResNet 模型。 |

## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 