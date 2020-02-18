---
title: 設計工具：預測汽車價格 (基本) 範例
titleSuffix: Azure Machine Learning
description: 在不需要撰寫程式碼的情況下，使用 Azure Machine Learning 設計工具建立 ML 迴歸模型來預測汽車價格。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 02/11/2020
ms.openlocfilehash: 58adbc7607b0b32e79123b701c37f55ce7cc1d2e
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138121"
---
# <a name="use-regression-to-predict-car-prices-with-azure-machine-learning-designer"></a>透過 Azure Machine Learning 設計工具使用迴歸來預測汽車價格

**設計工具 (預覽) 範例 1**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

了解如何使用設計工具 (預覽) 來建立機器學習迴歸模型，而不需要撰寫任何一行程式碼。

此管線會訓練**線性迴歸輸入變數**，以根據品牌、型號、馬力和大小等技術特徵來預測汽車的價格。 因為您嘗試回答「多少錢？」這個問題 這稱為迴歸問題。 不過，您可以套用此範例中的相同基本步驟來處理任何類型的機器學習問題，例如迴歸、分類或叢集等類型。

訓練機器學習模型的基本步驟如下：

1. 取得資料
1. 對資料進行前置處理
1. 將模型定型
1. 評估模型

以下是最終的完整管線圖表。 本文將提供所有模組的基本原理，讓您可以自行做出類似的決策。

![管線的圖表](./media/how-to-designer-sample-regression-automobile-price-basic/overall-graph.png)

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. 按一下 [範例 1] 來將其開啟。


## <a name="get-the-data"></a>取得資料

此範例使用來自 UCI Machine Learning 存放庫的**汽車價格資料 (原始)** 資料集。 該資料集包含 26 個具有汽車相關資訊的資料行，包括品牌、型號、價格、車輛特徵 (例如圓柱數目)、MPG，以及保險風險分數。 此範例的目標是要預測汽車的價格。

## <a name="pre-process-the-data"></a>對資料進行前置處理

主要的資料準備工作包括資料清理、整合、轉換、縮減和離散化或量化。 您可以在設計工具左側面板的**資料轉換**群組中，找到執行這些作業和其他資料前置處理工作的模組。

使用**選取資料集中的資料行**模組來排除具有許多遺漏值的「自負虧損」。 然後使用**清除遺漏資料**來移除具有遺漏值的資料列。 這有助於建立一組乾淨的訓練資料。

![資料預先處理](./media/how-to-designer-sample-regression-automobile-price-basic/data-processing.png)

## <a name="train-the-model"></a>將模型定型

機器學習的問題不盡相同。 常見的機器學習工作包括分類、叢集、迴歸和推薦系統，這每一個都可能需要不同的演算法。 您選擇的演算法通常取決於使用案例的需求。 在您挑選演算法之後，您需要調整其參數來訓練更精確的模型。 接著，您必須根據精確度、理解度和效率等計量來評估所有模型。

由於此範例的目標是要預測汽車價格，而且因為標籤資料行 (價格) 是連續資料，所以迴歸模型是不錯的選擇。 我們會針對此管線使用**線性迴歸**。

使用**分割資料**模組來隨機分割輸入資料，讓訓練資料集包含 70% 的原始資料，以及讓測試資料集包含 30% 的原始資料。

## <a name="test-evaluate-and-compare"></a>測試、評估和比較

分割資料集，並使用不同的資料集來訓練和測試模型，好讓模型的評估更具目標性。

模型經過訓練之後，您可以使用**計分模型**和**評估模型**模組來產生預測的結果並評估模型。

**計分模型**會使用已訓練的模型來對測試資料集產生預測。 若要檢查結果，請選取**計分模型**的輸出連接埠，然後選取 [視覺化]  。

![分數結果](./media/how-to-designer-sample-regression-automobile-price-basic/sample1-score-1225.png)

將分數傳遞給**評估模型**模組，以產生評估計量。 若要檢查結果，請選取**評估模型**的輸出連接埠，然後選取 [視覺化]  。

![評估結果](./media/how-to-designer-sample-regression-automobile-price-basic/sample1-evaluate-1225.png)

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>後續步驟

探索設計工具的其他範例：

- [範例 2 - 迴歸：比較汽車價格預測的演算法](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [範例 3 - 使用特徵選取進行分類：收入預測](how-to-designer-sample-classification-predict-income.md)
- [範例 4 - 分類：預測信用風險 (成本導向)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [範例 5 - 分類：預測流失](how-to-designer-sample-classification-churn.md)
- [範例 6 - 分類：預測航班誤點](how-to-designer-sample-classification-flight-delay.md)
- [範例 7 - 文字分類：Wikipedia SP 500 資料集](how-to-designer-sample-text-classification.md)