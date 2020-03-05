---
title: 設計工具：預測汽車價格 (進階) 範例
titleSuffix: Azure Machine Learning
description: 使用 Azure Machine Learning 設計工具建立及比較多個 ML 迴歸模型，以根據技術特徵來預測汽車價格。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: a80a1567c84ff3c2eda8ad22391aa862bb7d9d82
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2020
ms.locfileid: "77915821"
---
# <a name="train--compare-multiple-regression-models-to-predict-car-prices-with-azure-machine-learning-designer"></a>使用 Azure Machine Learning 設計工具訓練及比較多個迴歸模型來預測汽車價格

**設計工具 (預覽) 範例 2**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

了解如何使用設計工具 (預覽) 來建立機器學習管線，而不需要撰寫任何一行程式碼。 此範例會訓練並比較多個迴歸模型，以根據汽車的技術特徵來預測其價格。 我們將提供此管線中所做選擇的基本原理，讓您可以處理自己的機器學習問題。

如果您剛開始使用機器學習，請查看此管線的[基本版本](how-to-designer-sample-regression-automobile-price-basic.md)。

以下是此管線的完整圖表：

[![管線的圖表](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/graph.png)](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/graph.png#lightbox)

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. 按一下 [範例 2] 來將其開啟。 

## <a name="pipeline-summary"></a>管線摘要

使用下列步驟來建立機器學習管線：

1. 取得資料。
1. 對資料進行前置處理。
1. 將模型定型。
1. 測試、評估和比較模型。

## <a name="get-the-data"></a>取得資料

此範例使用來自 UCI Machine Learning 存放庫的**汽車價格資料 (原始)** 資料集。 此資料集包含 26 個具有汽車相關資訊的資料行，包括品牌、型號、價格、車輛特徵 (例如圓柱數目)、MPG，以及保險風險分數。

## <a name="pre-process-the-data"></a>對資料進行前置處理

主要的資料準備工作包括資料清理、整合、轉換、縮減和離散化或量化。 您可以在設計工具左側面板的**資料轉換**群組中，找到執行這些作業和其他資料前置處理工作的模組。

使用**選取資料集中的資料行**模組來排除具有許多遺漏值的「自負虧損」。 然後我們會使用**清除遺漏資料**來移除具有遺漏值的資料列。 這有助於建立一組乾淨的訓練資料。

![資料預先處理](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/data-processing.png)

## <a name="train-the-model"></a>將模型定型

機器學習的問題不盡相同。 常見的機器學習工作包括分類、叢集、迴歸和推薦系統，這每一個都可能需要不同的演算法。 您選擇的演算法通常取決於使用案例的需求。 在您挑選演算法之後，您需要調整其參數來訓練更精確的模型。 接著，您必須根據精確度、理解度和效率等計量來評估所有模型。

由於此管線的目標是要預測汽車價格，而且因為標籤資料行 (價格) 包含實數，所以迴歸模型是不錯的選擇。

為了比較不同演算法的成效，我們會使用兩個非線性演算法 (**促進式決策樹迴歸**和**決策樹迴歸**) 來建立模型。 這兩種演算法都有您可以變更的參數，但此範例會使用此管線的預設值。

使用**分割資料**模組來隨機分割輸入資料，讓訓練資料集包含 70% 的原始資料，以及讓測試資料集包含 30% 的原始資料。

## <a name="test-evaluate-and-compare-the-models"></a>測試、評估和比較模型

您可以使用兩組隨機選取的不同資料來訓練並測試模型，如上一節所述。 分割資料集，並使用不同的資料集來訓練和測試模型，好讓模型的評估更具目標性。

模型經過訓練之後，使用**計分模型**和**評估模型**模組來產生預測的結果並評估模型。 **計分模型**會使用已訓練的模型來對測試資料集產生預測。 然後，將分數傳遞給**評估模型**以產生評估計量。



以下是結果：

![比較結果](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/result.png)

這些結果顯示，與根據**決策樹系迴歸**建立的模型相比，以**促進式決策樹迴歸**建立的模型有較低的均方根誤差。



## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>後續步驟

探索設計工具的其他範例：

- [範例 1 - 迴歸：預測汽車的價格](how-to-designer-sample-regression-automobile-price-basic.md)
- [範例 3 - 使用特徵選取進行分類：收入預測](how-to-designer-sample-classification-predict-income.md)
- [範例 4 - 分類：預測信用風險 (成本導向)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [範例 5 - 分類：預測流失](how-to-designer-sample-classification-churn.md)
- [範例 6 - 分類：預測航班誤點](how-to-designer-sample-classification-flight-delay.md)
- [範例 7 - 文字分類：Wikipedia SP 500 資料集](how-to-designer-sample-text-classification.md)
