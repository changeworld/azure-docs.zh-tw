---
title: 將 PyTorch 模型定型
titleSuffix: Azure Machine Learning
description: 使用 Azure Machine Learning 設計工具中的 [定型 Pytorch 模型] 模組，從頭訓練模型，或微調現有的模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: 2d88069f33995bdbe9dd479afe9a4e72ab9939b6
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420660"
---
# <a name="train-pytorch-model"></a>將 PyTorch 模型定型

本文描述如何使用 Azure Machine Learning 設計工具中的 [ **定型 Pytorch 模型** ] 模組，來定型 Pytorch 模型（例如 densenet-121）。 定型會在您定義模型並設定其參數之後進行，且需要標記的資料。 

## <a name="how-to-use-train-pytorch-model"></a>如何使用定型 Pytorch 模型 

1. 在設計工具中，將 [densenet-121](densenet.md) 模組或 [ResNet](resnet.md) 新增至您的管線草稿。

2. 將 **定型 Pytorch 模型** 模組加入至管線。 您可以在 **模型定型** 類別下找到此模組。 展開 [ **定型** ]，然後將 [ **定型 Pytorch 模型** ] 模組拖曳到您的管線中。

   > [!NOTE]
   > **定型 Pytorch 模型** 模組更適合在大型資料集的 **GPU** 類型計算上執行，否則您的管線將會失敗。 您可以設定 [ **使用其他計算目標** ]，在模組的右窗格中選取特定模組的 [計算]。

3.  在左側輸入中，附加未定型的模型。 將訓練資料集和驗證資料集附加至 **定型 Pytorch 模型** 的中間和右側輸入。

    針對未定型的模型，它必須是 pytorch 模型，例如 Densenet-121;否則，將會擲回 ' InvalidModelDirectoryError '。

    針對資料集，訓練資料集必須是加上標籤的影像目錄。 請參閱 **轉換為影像目錄** ，以瞭解如何取得加上標籤的影像目錄。 如果未加上標籤，則會擲回 ' NotLabeledDatasetError '。

    訓練資料集和驗證資料集具有相同的標籤分類，否則會擲回 InvalidDatasetError。

4.  若為 **epoch** ，請指定您想要定型的 epoch 數目。 系統預設會在每個 epoch 中逐一查看整個資料集，預設為5。

5.  針對 [ **批次大小** ]，指定批次中要定型的實例數目（預設為16）。

6.  針對 **學習速率** ，請指定 *學習速率* 的值。 學習速率值會控制在每次測試和更正模型時，在優化工具中使用的步驟大小（例如 sgd）。

    藉由讓速率變小，您可以更頻繁地測試模型，因為您可能會陷入本機高原的風險。 如果階梯較大，聚合速度會較快，但會有超過實際最小值的風險。 預設為0.001。

7.  針對 **隨機種子** ，選擇性地輸入要做為種子的整數值。 如果您想要確保在執行期間重現性實驗，建議使用種子。

8.  若為 **耐心等候** ，請指定如果驗證遺失未連續減少，要提早停止定型的 epoch 數目。 預設為3。

9.  提交管線。 如果您的資料集大小較大，則需要一些時間，而且建議使用 GPU 計算。

## <a name="results"></a>結果

在管線執行完成後，若要使用模型進行評分，請將 [定型 Pytorch 模型](train-pytorch-model.md) 連接到 [評分影像模型](score-image-model.md)，以預測新輸入範例的值。

## <a name="technical-notes"></a>技術說明
###  <a name="expected-inputs"></a>預期的輸入  

| 名稱               | 類型                    | 描述                              |
| ------------------ | ----------------------- | ---------------------------------------- |
| 未定型的模型    | UntrainedModelDirectory | 未定型的模型，需要 pytorch         |
| 定型資料集   | ImageDirectory          | 定型資料集                         |
| 驗證資料集 | ImageDirectory          | 每個 epoch 評估的驗證資料集 |

###  <a name="module-parameters"></a>模組參數  

| 名稱          | 範圍            | 類型    | 預設 | 描述                              |
| ------------- | ---------------- | ------- | ------- | ---------------------------------------- |
| 時代        | >0               | 整數 | 5       | 選取包含標籤或結果資料行的資料行 |
| 批次大小    | >0               | 整數 | 16      | 批次中定型的實例數目   |
| 學習率 | >=double.Epsilon | Float   | 0.001   | 隨機梯度下降最佳化工具的初始學習速率。 |
| 隨機種子   | 任意              | 整數 | 1       | 模型使用的亂數產生器的種子。 |
| 耐心      | >0               | 整數 | 3       | 提前停止訓練的 epoch 數目   |

###  <a name="outputs"></a>輸出  

| 名稱          | 類型           | 描述   |
| ------------- | -------------- | ------------- |
| 定型的模型 | ModelDirectory | 定型的模型 |

## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 



