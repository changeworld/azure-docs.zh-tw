---
title: 設計工具：預測客戶流失的範例
titleSuffix: Azure Machine Learning
description: 遵循此分類範例，利用 Azure Machine Learning 設計工具與促進式決策樹來預測客戶流失。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: sgilley
ms.date: 12/25/2019
ms.openlocfilehash: 701bf186080ef627ef4621e959b281fd58aa4132
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963356"
---
# <a name="use-boosted-decision-tree-to-predict-churn-with-azure-machine-learning-designer"></a>搭配使用促進式決策樹與 Azure Machine Learning 設計工具來預測客戶流失

**設計工具 (預覽) 範例 5**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

了解如何使用設計工具 (預覽) 來建立複雜的機器學習管線，而不需要撰寫任何一行程式碼。

此管線會訓練 2 個**二元推進式決策樹**分類器，以預測客戶關係管理 (CRM) 系統的常見工作：客戶流失。 資料值和標籤會分割成多個資料來源，並隨意混合成匿名的客戶資訊，不過，我們仍然可以使用設計工具來結合資料集，並使用遮蔽的值來訓練模型。

因為您嘗試回答「哪一個？」這個問題 這稱為分類問題，但您可以套用此範例中示範的相同邏輯來處理任何類型的機器學習問題，例如迴歸、分類或叢集等類型。

以下是此管線的完整圖表：

![管線圖表](./media/how-to-designer-sample-classification-churn/pipeline-graph.png)

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. 按一下 [範例 5] 來將其開啟。 

## <a name="data"></a>資料

此管線的資料來自 KDD Cup 2009。 其中有 50,000 個資料列和 230 個特徵資料行。 此工作用於預測客戶的流失、購買意願和向上銷售。 如需有關資料和工作的詳細資訊，請參閱 [KDD 網站](https://www.kdd.org/kdd-cup/view/kdd-cup-2009)。

## <a name="pipeline-summary"></a>管線摘要

設計工具中的此範例管線會顯示流失、購買意願和向上銷售的二元分類器預測，這是客戶關係管理 (CRM) 的常見工作。

首先是一些簡單的資料處理。

- 原始資料集有許多遺漏值。 使用**清除遺漏資料**模組，將遺漏的值取代為 0。

    ![清除資料集](media/how-to-designer-sample-classification-churn/sample5-dataset-1225.png)

- 特徵和對應的流失會在不同的資料集中。 使用**新增資料行**模組將標籤資料行附加至特徵資料行。 第一個資料行 (**Col1**) 是標籤資料行。 從視覺化結果中，我們可以看到資料集不對稱。 負面 (-1) 範例比正面範例 (+ 1) 多。 稍後我們會使用 **SMOTE** 模組增加不具代表性的案例。

    ![新增資料行資料集](./media/how-to-designer-sample-classification-churn/sample5-addcol-1225.png)



- 使用**分割資料**模組將資料集分割為訓練和測試集。

- 然後使用「促進式決策樹」二元分類器搭配預設參數來建立預測模型。 為每個工作建立一個模型，也就是各有一個模型用來預測向上銷售、購買意願和流失。

- 在管線的右側部分，我們使用 **SMOTE** 模組來增加正面範例的百分比。 SMOTE 百分比會設定為 100，讓正面範例加倍。 深入了解 SMOTE 模組如何與 [SMOTE 模組參考資料](algorithm-module-reference/smote.md)搭配運作。

## <a name="results"></a>結果

將**評估模型**模組的輸出視覺化，以查看測試集上的模型成效。 

![評估結果](./media/how-to-designer-sample-classification-churn/sample5-evaluate-1225.png)

 您可以移動**閾值**滑杆，並查看二元分類工作的計量變更。 

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>後續步驟

探索設計工具的其他範例：

- [範例 1 - 迴歸：預測汽車的價格](how-to-designer-sample-regression-automobile-price-basic.md)
- [範例 2 - 迴歸：比較汽車價格預測的演算法](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [範例 3 - 使用特徵選取進行分類：收入預測](how-to-designer-sample-classification-predict-income.md)
- [範例 4 - 分類：預測信用風險 (成本導向)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [範例 6 - 分類：預測航班誤點](how-to-designer-sample-classification-flight-delay.md)
- [範例 7 - 文字分類：Wikipedia SP 500 資料集](how-to-designer-sample-text-classification.md)
