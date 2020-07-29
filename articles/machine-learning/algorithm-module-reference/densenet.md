---
title: DenseNet
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 densenet 演算法建立影像分類模型。
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
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84449982"
---
# <a name="densenet"></a>DenseNet

本文說明如何使用 Azure Machine Learning 設計工具（預覽）中的**DenseNet**模組，來建立使用 DenseNet 演算法的影像分類模型。  

此分類演算法是一種受監督的學習方法，而且需要已加上標籤的資料集。 如需如何取得已加上標籤之影像目錄的詳細指示，請參閱[轉換成映射目錄](convert-to-image-directory.md)模組。 您可以將模型和加上標籤的影像目錄當做[定型 Pytorch 模型](train-pytorch-model.md)的輸入，來定型模型。 定型的模型接著可用來使用[分數影像模型](score-image-model.md)來預測新輸入範例的值。

### <a name="more-about-densenet"></a>深入瞭解 DenseNet

如需詳細資訊，請參閱[密集連線的迴旋網路](https://arxiv.org/abs/1608.06993)。

## <a name="how-to-configure-densenet"></a>如何設定 DenseNet

1.  在設計工具中，將**DenseNet**模組新增至您的管線。  

2.  針對 [**模型名稱**]，指定特定 densenet 結構的名稱，您可以從支援的 densenet 中選取： ' densenet121 '、' densenet161 '、' densenet169 '、' densenet201 '。

3.  針對 [**預先定型**]，指定是否要在 ImageNet 上使用預先定型的模型。 若選取此選項，您可以根據選取的預先定型模型微調模型;如果取消選取，您可以從頭開始訓練。

4.  為了讓**記憶體有效率**，請指定是否要使用檢查點，這會比記憶體更有效率，但速度較慢。 如需相關資訊，請參閱 https://arxiv.org/pdf/1707.06990.pdf 。

5.  將 [ **DenseNet**模組]、[定型] 和 [驗證影像資料集] 模組的輸出連接到 [[定型 Pytorch] 模型](train-pytorch-model.md)。 

6. 提交管線。


## <a name="results"></a>結果

完成管線執行之後，若要使用模型進行評分，請將[定型 Pytorch 模型](train-pytorch-model.md)連接到[計分影像模型](score-image-model.md)，以預測新輸入範例的值。

## <a name="technical-notes"></a>技術說明  

###  <a name="module-parameters"></a>模組參數  

| Name             | 範圍 | 類型    | 預設     | 描述                              |
| ---------------- | ----- | ------- | ----------- | ---------------------------------------- |
| 模型名稱       | 任意   | [模式]    | densenet201 | 特定 densenet 結構的名稱     |
| 預先定型       | 任意   | 布林值 | True        | 是否要在 ImageNet 上使用預先定型的模型 |
| 記憶體效率 | 任意   | Boolean | False       | 是否要使用檢查點，這比記憶體更有效率，但速度較慢 |

###  <a name="output"></a>輸出  

| 名稱            | 類型                    | Description                              |
| --------------- | ----------------------- | ---------------------------------------- |
| 未定型的模型 | UntrainedModelDirectory | 可以連接到定型 Pytorch 模型的未訓練 densenet 模型。 |

## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 
