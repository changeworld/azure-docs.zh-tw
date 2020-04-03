---
title: 範例設計工具管線
titleSuffix: Azure Machine Learning
description: 在 Azure Machine Learning 設計工具中使用範例，以快速了解您的機器學習管線。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 03/29/2020
ms.openlocfilehash: f9a8b0a4c51024d91e517db2f6ae10a4dba62384
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389336"
---
# <a name="designer-sample-pipelines"></a>設計工具範例管線

使用 Azure Machine Learning 設計工具中的內建範例，快速開始建置您自己的機器學習管線。 Azure Machine Learning 設計工具 [GitHub 存放庫](https://github.com/Azure/MachineLearningDesigner)包含詳細的文件，可協助您了解一些常見的機器學習案例。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://aka.ms/AMLFree)。
* 具備 Enterprise SKU 的 Azure Machine Learning 工作區。


## <a name="how-to-use-sample-pipelines"></a>如何使用範例管線

設計工具會將範例管線的複本儲存至您的工作室工作區。 您可以編輯管線以符合您的需求，並以您自己的方式加以儲存。 使用這些資訊開始進行專案。

### <a name="open-a-sample-pipeline"></a>開啟範例管線

1. 登入 <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a>，並選取您要使用的工作區。

1. 選取 [設計工具]  。

1. 在 [新增管線]  區段下，選取範例管線。

    如需完整的範例清單，請選取 [顯示更多範例]  。

### <a name="submit-a-pipeline-run"></a>提交管線執行

若要執行管線，您必須先設定要在其上執行管線的預設計算目標。

1. 在畫布右側的 [設定]  窗格中，選取 [選取計算目標]  。

1. 在出現的對話方塊中，選取現有計算目標或建立新的計算目標。 選取 [儲存]  。

1. 選取畫布頂端的 [提交]  以提交管線執行。

視範例管線和計算設定而定，執行可能需要一些時間才能完成。 預設計算設定的最小節點大小為 0，這表示設計工具必須在閒置之後配置資源。 重複的管線執行花費較少的時間，因為已經配置計算資源。 此外，設計工具會針對每個模組使用快取的結果，進一步提升效率。


### <a name="review-the-results"></a>檢閱結果

在管線完成執行之後，您可以檢閱管線及檢視每個模組的輸出，以便深入了解。

請使用下列步驟來檢視模組輸出：

1. 選取畫布中的模組。

1. 在畫布右側的 [模組詳細資料] 窗格中，選取 [輸出 + 記錄]  。 選取圖形圖示![視覺化圖示](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png)，以查看每個模組的結果。 

針對一些最常見的機器學習案例，使用範例作為起點。

## <a name="regression-samples"></a>迴歸範例

深入了解內建的迴歸範例。

| 範例標題 | 描述 | 
| --- | --- |
| [範例 1：迴歸 - 汽車價格預測 (基本)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-basic.md) | 使用線性回歸來預測汽車價格。 |
| [範例 2：迴歸 - 汽車價格預測 (進階)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-compare-algorithms.md) | 使用決策樹系和推進式決策樹迴歸輸入變數來預測汽車價格。 比較這兩個模型找出最佳的演算法。

## <a name="classification-samples"></a>分類範例

深入了解內建的分類範例。 您可以開啟範例並改為檢視模組註解，以深入了解沒有文件連結的範例。

| 範例標題 | 描述 | 
| --- | --- |
| [範例 3：具有特徵選取的二元分類 - 收入預測](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-predict-income.md) | 使用二元推進式決策樹來預測收入的高或低。 使用皮爾森相關來選取功能。
| [範例 4：具有自訂 Python 指令碼的二元分類 - 信用風險預測](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-credit-risk-cost-sensitive.md) | 將信用應用程式分類為高或低風險。 使用執行 Python 指令碼模組來為資料加權。
| [範例 5：二元分類 - 客戶關係預測](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-churn.md) | 使用二元推進式決策樹來預測客戶流失。 使用 SMOTE 來取樣偏差資料。
| [範例 7：文字分類 - 維基百科 SP 500 資料集](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-text-classification.md) | 使用多元羅吉斯迴歸將維基百科文章的公司類型分類。 |
| 範例 12：多元分類 - 字母辨識 | 建立完善的二元分類器將寫好的字母分類。 |

## <a name="recommender-samples"></a>推薦範例

深入了解內建的推薦範例。 您可以開啟範例並改為檢視模組註解，以深入了解沒有文件連結的範例。

| 範例標題 | 描述 | 
| --- | --- |
| 範例 10：建議 - 電影分級推文 | 根據電影標題和分級建置電影推薦引擎。 |

## <a name="utility-samples"></a>公用程式範例

深入了解示範機器學習公用程式和功能的範例。 您可以開啟範例並改為檢視模組註解，以深入了解沒有文件連結的範例。

| 範例標題 | 描述 | 
| --- | --- |
| [範例 6：使用自訂 R 指令碼 - 航班延遲預測](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-flight-delay.md) |
| 範例 8：二元分類的交叉驗證 - 成人收入預測 | 使用交叉驗證為成人收入建立二元分類器。
| 範例 9：排列功能重要性 | 使用排列功能重要性來計算測試資料集的重要性分數。 
| 範例 11：二元分類的參數調整 - 成人收入預測 | 使用調整模型超參數來尋找最佳的超參數，以建置二元分類器。 |

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>後續步驟

了解如何使用下列教學課程建置和部署機器學習模型：[教學課程：使用設計工具預測汽車價格](tutorial-designer-automobile-price-train-score.md)來試用設計工具
