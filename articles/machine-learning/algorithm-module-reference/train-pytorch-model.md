---
title: 將 PyTorch 模型定型
titleSuffix: Azure Machine Learning
description: 瞭解如何從頭開始訓練 pytorch 模型或微調。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: af14d4770d032c23216b805045eb27fadded5954
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86170253"
---
# <a name="train-pytorch-model"></a>將 PyTorch 模型定型

本文說明如何使用 Azure Machine Learning 設計工具中的 [**定型 Pytorch 模型**] 模組 (預覽) 來訓練 DenseNet 之類的 Pytorch 模型。 定型會在您定義模型並設定其參數之後進行，而且需要加上標籤的資料。 

## <a name="how-to-use-train-pytorch-model"></a>如何使用定型 Pytorch 模型 

1. 在設計工具中，將[DenseNet](densenet.md)模組或[ResNet](resnet.md)新增至管線草稿。

2. 將**訓練 Pytorch 模型**模組新增至管線。 您可以在**模型訓練**類別下找到此模組。 展開 [**定型**]，然後將 [**定型 Pytorch 模型**] 模組拖曳至您的管線中。

   > [!NOTE]
   > **定型 Pytorch 模型**模組較適合在大型資料集的**GPU**類型計算上執行，否則您的管線將會失敗。 您可以藉由設定 [**使用其他計算目標**]，在模組的右窗格中選取特定模組的 [計算]。

3.  在左側輸入中，附加未定型的模型。 將訓練資料集和驗證資料集附加至**定型 Pytorch 模型**的中間和右側輸入。

    針對未定型的模型，它必須是 pytorch 模型，例如 DenseNet;否則，將會擲回 ' InvalidModelDirectoryError '。

    針對資料集，訓練資料集必須是已加上標籤的影像目錄。 如需如何取得已加上標籤之影像目錄的詳細說明，請參閱**轉換成圖像目錄**。 如果未加上標籤，則會擲回 ' NotLabeledDatasetError '。

    訓練資料集和驗證資料集具有相同的標籤分類，否則會擲回 InvalidDatasetError。

4.  針對 [ **epoch**]，指定您想要定型的 epoch 數目。 在每個 epoch 中，會逐一查看整個資料集，預設為5。

5.  針對 [**批次大小**]，指定批次中要定型的實例數目（依預設為16）。

6.  針對 [**學習速率**]，指定*學習速率*的值。 學習速率值會控制在每次測試和更正模型時，優化工具中所使用的步驟大小，例如 sgd。

    藉由讓速率變小，您可以更頻繁地測試模型，但有可能會卡在本機高原中的風險。 如果階梯較大，聚合速度會較快，但會有超過實際最小值的風險。 預設為0.001。

7.  針對 [**隨機種子**]，選擇性地輸入要用來做為種子的整數值。 如果您想要確保跨回合執行實驗的重現性，建議使用種子。

8.  針對 [**耐心**等候]，指定在驗證遺失不連續減少時，要及早停止定型的 epoch 數目。 預設值為3。

9.  提交管線。 如果您的資料集大小較大，則需要一些時間。

## <a name="results"></a>結果

完成管線執行之後，若要使用模型進行評分，請將[定型 Pytorch 模型](train-pytorch-model.md)連接到[計分影像模型](score-image-model.md)，以預測新輸入範例的值。

## <a name="technical-notes"></a>技術說明
###  <a name="expected-inputs"></a>預期的輸入  

| 名稱               | 類型                    | 說明                              |
| ------------------ | ----------------------- | ---------------------------------------- |
| 未定型的模型    | UntrainedModelDirectory | 未定型模型，需要 pytorch         |
| 定型資料集   | ImageDirectory          | 定型資料集                         |
| 驗證資料集 | ImageDirectory          | 評估每個 epoch 的驗證資料集 |

###  <a name="module-parameters"></a>模組參數  

| 名稱          | 範圍            | 類型    | 預設 | 描述                              |
| ------------- | ---------------- | ------- | ------- | ---------------------------------------- |
| Epoch        | >0               | 整數 | 5       | 選取包含標籤或結果資料行的資料行 |
| 批次大小    | >0               | 整數 | 16      | 要在批次中定型的實例數目   |
| 學習率 | >=double.Epsilon | Float   | 0.001   | 隨機梯度下降最佳化工具的初始學習速率。 |
| 隨機種子   | 任何              | 整數 | 1       | 模型使用的亂數產生器的種子。 |
| 聽      | >0               | 整數 | 3       | 提前停止訓練的 epoch 數   |

###  <a name="outputs"></a>輸出  

| 名稱          | 類型           | 說明   |
| ------------- | -------------- | ------------- |
| 定型的模型 | ModelDirectory | 定型的模型 |

## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 



