---
title: 設計工具：預測信用風險範例
titleSuffix: Azure Machine Learning
description: 利用 Azure Machine Learning 設計工具建立分類器及使用自訂 Python 指令碼來預測信用風險。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: ed8ee9b1c711ee0056377154379b8df56e0785df
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964595"
---
# <a name="build-a-classifier--use-python-scripts-to-predict-credit-risk-using-azure-machine-learning-designer"></a>利用 Azure Machine Learning 設計工具建立分類器及使用 Python 指令碼來預測信用風險

**設計工具 (預覽) 範例 4**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

本文將說明如何使用設計工具 (預覽) 來建立複雜的機器學習管線。 您將了解如何使用 Python 指令碼來實作自訂邏輯，並比較多個模型來選擇最佳選項。

此範例會使用信用額度應用程式資訊 (例如信用額度記錄、年齡和信用卡號碼) 來訓練用於預測信用風險的分類器。 不過，您也可以套用本文中的概念來處理您自己的機器學習問題。

以下是此管線的完整圖表：

[![管線的圖表](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png)](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. 按一下 [範例 4] 來將其開啟。

## <a name="data"></a>資料

此範例會使用來自 UC Irvine 存放庫的德國信用卡資料集。 其中包含具有 20 項特徵和一個標籤的 1,000 個範例。 每個範例代表一名人員。 20 個特徵包含數值和類別特徵。 如需有關資料集的詳細資訊，請參閱 [UCI 網站](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29)。 最後一個資料行是代表信用風險的標籤，只有兩個可能值：高信用風險 = 2 和低信用風險 = 1。

## <a name="pipeline-summary"></a>管線摘要

在此管線中，您會比較兩種不同方法來產生解決此問題的模型：

- 使用原始資料集進行訓練。
- 使用複寫的資料集進行訓練。

使用這兩種方法時，您都會透過複寫使用測試資料集來評估模型，以確保結果與成本函式一致。 使用這兩種方法測試兩個分類器：**二元支援向量機器**和**二元促進式決策樹**。

將低風險範例誤判為高風險的成本為 1，而將高風險範例誤判為低風險的成本為 5。 我們使用**執行 Python 指令碼**模組來處理此誤判成本。

以下是管線的圖表：

[![管線的圖表](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png)](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="data-processing"></a>資料處理

一開始使用**中繼資料編輯器**模組來加入資料行名稱，以更有意義的名稱 (從 UCI 網站上的資料集描述中取得) 取代預設的資料行名稱。 在**中繼資料編輯器**的 [新增資料行名稱]  欄位中，以逗號分隔值的格式提供新資料行名稱。

接下來，產生用來開發風險預測模型的訓練和測試集。 使用**分割資料**模組，將原始資料集分割成相同大小的訓練和測試集。 若要建立相同大小的集合，請將**第一個輸出資料集中的資料列比例**設定為 0.7。

### <a name="generate-the-new-dataset"></a>產生新資料集

由於低估風險的成本很高，因此請設定誤判的成本，如下所示：

- 高風險誤判為低風險的情況：5
- 低風險誤判為高風險的情況：1

若要反映此成本函式，請產生新的資料集。 在此新資料集中，每個高風險範例都會複寫五次，但低風險範例的數目則不變。 在複寫之前請將資料分割成訓練和測試集，以避免這兩個集合中出現相同的資料列。

若要複寫高風險的資料，請將此 Python 程式碼放入**執行 Python 指令碼**模組：

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    df_label_1 = dataframe1[dataframe1.iloc[:, 20] == 1]
    df_label_2 = dataframe1[dataframe1.iloc[:, 20] == 2]

    result = df_label_1.append([df_label_2] * 5, ignore_index=True)
    return result,
```

**執行 Python 指令碼**模組會複寫訓練和測試資料集。

### <a name="feature-engineering"></a>特徵設計

**二元支援向量機器**演算法需要正規化資料。 因此，請使用**正規化資料**模組搭配 `tanh` 轉換，將所有數值特徵範圍正規化。 `tanh` 轉換會將所有數值特徵轉換成 0 和 1 範圍內的值，同時保留值的整體分佈。

**二元支援向量機器**模組會處理字串特徵，將其轉換成類別特徵，然後再轉換為值為零或 1 的二進位特徵。 因此，您不需要將這些特徵正規化。

## <a name="models"></a>模型

由於您已套用兩個分類器：**二元支援向量機器** (SVM) 和**二元促進式決策樹**，以及兩個資料集，因此總共會產生四個模型：

- 以原始資料訓練的 SVM。
- 以複寫資料訓練的 SVM。
- 以原始資料訓練的促進式決策樹。
- 以複寫資料訓練的促進式決策樹。

此範例會使用標準資料科學工作流程來建立、訓練和測試模型：

1. 使用**二元支援向量機器**和**二元促進式決策樹**初始化學習演算法。
1. 使用**訓練模型**將演算法套用至資料，並建立實際的模型。
1. 使用**計分模型**和測試範例來產生分數。

下圖顯示此管線的一部分，其中使用原始和已複寫的訓練集來訓練兩個不同的 SVM 模型。 **訓練模型**會連結到訓練集，而**計分模型**會連結到測試集。

![管線圖表](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/score-part.png)

在管線的評估階段中，您會為四個模型各別計算精確度。 針對此管線，請使用**評估模型**來比較具有相同誤判成本的範例。

**評估模型**模組可以計算最多兩個計分模型的效能計量。 因此，您可以使用一個**評估模型**實例來評估兩個 SVM 模型，以及使用另一個**評估模型**來評估兩個促進式決策樹模型。

請注意，複寫的測試資料集會用來作為**計分模型**的輸入。 換句話說，最終的精確度分數會包含標籤錯誤的成本。

## <a name="combine-multiple-results"></a>結合多個結果

**評估模型**模組會產生一個具有單一資料列的資料表，而該資料列會包含各種計量。 若要建立一組精確度結果，我們會先使用**加入資料列**來將結果合併成單一資料表。 接著，我們會使用**執行 Python 指令碼**模組中的下列 Python 指令碼，為結果資料表中的每個資料列新增模型名稱和訓練方法：

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    new_cols = pd.DataFrame(
            columns=["Algorithm","Training"],
            data=[
                ["SVM", "weighted"],
                ["SVM", "unweighted"],
                ["Boosted Decision Tree","weighted"],
                ["Boosted Decision Tree","unweighted"]
            ])

    result = pd.concat([new_cols, dataframe1], axis=1)
    return result,
```

## <a name="results"></a>結果

若要查看管線的結果，您可以用滑鼠右鍵按一下最後一個「選取資料集中的資料行」  模組的「視覺化」輸出。

![將輸出視覺化](media/how-to-designer-sample-classification-credit-risk-cost-sensitive/sample4-lastselect-1225.png)

第一個資料行列出用來產生模型的機器學習演算法。

第二個資料行表示訓練集的類型。

第三個資料行包含成本導向的精確度值。

您可以從這些結果中看到模型提供的最佳準確度，而該模型是由**二元支援向量機器**所建立，並根據複寫的訓練資料集進行訓練。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>後續步驟

探索設計工具的其他範例：

- [範例 1 - 迴歸：預測汽車的價格](how-to-designer-sample-regression-automobile-price-basic.md)
- [範例 2 - 迴歸：比較汽車價格預測的演算法](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [範例 3 - 使用特徵選取進行分類：收入預測](how-to-designer-sample-classification-predict-income.md)
- [範例 5 - 分類：預測流失](how-to-designer-sample-classification-churn.md)
- [範例 6 - 分類：預測航班誤點](how-to-designer-sample-classification-flight-delay.md)
- [範例 7 - 文字分類：Wikipedia SP 500 資料集](how-to-designer-sample-text-classification.md)
