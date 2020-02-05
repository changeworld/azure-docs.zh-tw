---
title: 設計工具：分類及預測收入範例
titleSuffix: Azure Machine Learning
description: 遵循此範例使用 Azure Machine Learning 設計工具建立一個無程式碼分類器來預測收入。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: 560339fb04e3bbbe42c4370655e74e8536a7c015
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963357"
---
# <a name="build-a-classifier--use-feature-selection-to-predict-income-with-azure-machine-learning-designer"></a>利用 Azure Machine Learning 設計工具建立分類器及使用特徵選取來預測收入

**設計工具 (預覽) 範例 3**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

了解如何使用設計工具 (預覽) 來建立機器學習分類器，而不需要撰寫任何一行程式碼。 此範例會訓練**二元促進式決策樹**來預測成人普查收入 (>= 50K 或 <= 50K)。

因為問題是回應「哪一個？」 這稱為分類問題。 不過，您可以套用相同的基本程序來處理任何類型的機器學習問題，例如迴歸、分類或叢集等類型。

以下是此範例的最終管線圖表：

![管線的圖表](./media/how-to-designer-sample-classification-predict-income/overall-graph.png)

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. 按一下 [範例 3] 來將其開啟。



## <a name="data"></a>資料

此資料集包含 14 個特徵和一個標籤資料行。 有多種類型的特徵，包括數值和類別。 下圖是資料集的摘錄：![資料](media/how-to-designer-sample-classification-predict-income/sample3-dataset-1225.png)



## <a name="pipeline-summary"></a>管線摘要

請依照下列步驟建立管線：

1. 將成人普查收入二元資料集模組拖曳至管線畫布。
1. 新增**分割資料**模組，以建立訓練和測試集。 將第一個輸出資料集中的資料列比例設為 0.7。 此設定會指定將 70% 的資料輸出到模組的左側連接埠，並將其餘資料輸出至右側連接埠。 我們會使用左側的資料集進行訓練，而將右側資料用於測試。
1. 新增**篩選型特徵選取**模組來依據 PearsonCorreclation 選取 5 個特徵。 
1. 新增**二元促進式決策樹**模組來初始化促進式決策樹分類器。
1. 新增**訓練模型**模組。 將上一個步驟中的分類器連結到**訓練模型**的左側輸入連接埠。 將篩選型特徵選取模組中的已篩選資料集連結為訓練資料集。  **訓練模型**會訓練分類器。
1. 新增「選取資料行轉換」和「套用轉換」模組，以將相同的轉換 (篩選型特徵選取) 套用至測試資料集。
![apply-transformation](./media/how-to-designer-sample-classification-predict-income/transformation.png)
1. 新增 **計分模型**模組，並使其與**訓練模型**模組連結。 然後，將測試集 (「套用轉換模組」的輸出，該模組也會將特徵選取套用至測試集) 新增至**計分模型**。 **計分模型**會進行預測。 您可以選取其輸出連接埠來查看預測和正面類別機率。


    此管線有兩個計分模組，右側的模組已在進行預測之前排除標籤資料行。 這是為了部署即時端點而準備，因為 Web 服務輸入只會預期有特徵，而沒有標籤。 

1. 新增**評估模型**模組，並將計分後的資料集連結到其左側輸入連接埠。 若要查看評估結果，請選取**評估模型**模組的輸出連接埠，然後選取 [視覺化]  。

## <a name="results"></a>結果

![評估結果](media/how-to-designer-sample-classification-predict-income/sample3-evaluate-1225.png)

在評估結果中，您可以看到 [ROC]、[精確度與召回] 和 [混淆計量] 這類曲線。 

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>後續步驟

探索設計工具的其他範例：

- [範例 1 - 迴歸：預測汽車的價格](how-to-designer-sample-regression-automobile-price-basic.md)
- [範例 2 - 迴歸：比較汽車價格預測的演算法](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [範例 4 - 分類：預測信用風險 (成本導向)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [範例 5 - 分類：預測流失](how-to-designer-sample-classification-churn.md)
- [範例 6 - 分類：預測航班誤點](how-to-designer-sample-classification-flight-delay.md)
- [範例 7 - 文字分類：Wikipedia SP 500 資料集](how-to-designer-sample-text-classification.md)
