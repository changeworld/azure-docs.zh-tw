---
title: 演算法和模組參考
description: 瞭解 Azure Machine Learning 設計工具中可用的模組（預覽）
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: peterclu
ms.author: peterlu
ms.date: 12/17/2019
ms.openlocfilehash: d3feb62c0c7fa24dd998add08d17ebd1d4e9ee6c
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162577"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer"></a>Azure Machine Learning 設計工具的演算法 & 模組參考

此參考內容提供 Azure Machine Learning 設計工具（預覽）中所提供的每個機器學習服務演算法和模組的技術背景。

每個模組都代表一組程式碼，可以獨立執行並執行機器學習工作（指定必要的輸入）。 模組可能包含特定的演算法，或執行在機器學習中很重要的工作，例如遺漏值取代或統計分析。

如需選擇演算法的協助，請參閱 
* [如何選取演算法](../how-to-select-algorithms.md)
* [Azure Machine Learning 演算法功能提要](../algorithm-cheat-sheet.md)

> [!TIP]
> 在設計工具的任何管線中，您可以取得特定模組的相關資訊。 選取模組，然後在 [快速說明] 窗格中選取 [更多說明] 連結。

## <a name="data-preparation-modules"></a>資料準備模組


| 功能 | 描述 | 模組 |
| --- |--- | --- |
| 資料輸入和輸出 | 將資料從雲端來源移至您的管線。 在執行管線時，將您的結果或中繼資料寫入 Azure 儲存體、SQL 資料庫或 Hive，或使用雲端儲存體在管線之間交換資料。  | [手動輸入資料](enter-data-manually.md) <br/> [匯出資料](export-data.md) <br/> [匯入資料](import-data.md) |
| 資料轉換 | 機器學習服務特有資料的作業，例如正規化或分類收納資料、維度縮減，以及在各種不同的檔案格式之間轉換資料。| [新增資料行](add-columns.md) <br/> [加入資料列](add-rows.md) <br/> [套用數學運算](apply-math-operation.md) <br/> [套用 SQL 轉換](apply-sql-transformation.md) <br/> [清除遺漏的資料](clean-missing-data.md) <br/> [剪輯值](clip-values.md) <br/> [轉換成 CSV](convert-to-csv.md) <br/> [轉換成資料集](convert-to-dataset.md) <br/> [轉換成指標值](convert-to-indicator-values.md) <br/> [編輯中繼資料](edit-metadata.md) <br/> [聯結資料](join-data.md) <br/> [標準化資料](normalize-data.md) <br/> [資料分割和範例](partition-and-sample.md)  <br/> [移除重複的資料列](remove-duplicate-rows.md) <br/> [SMOTE](smote.md) <br/> [選取資料行轉換](select-columns-transform.md) <br/> [選取資料集中的資料行](select-columns-in-dataset.md) <br/> [資料分割](split-data.md) |
| 特徵選取 | 選取相關、有用的功能子集，以用於建立分析模型。 | [以篩選器為基礎的特徵選取](filter-based-feature-selection.md) <br/> [排列功能重要性](permutation-feature-importance.md) |
| 統計函數 | 提供各種與資料科學相關的統計方法。 | [摘要資料](summarize-data.md)|

## <a name="machine-learning-algorithms"></a>機器學習演算法

| 功能 | 描述 | 模組 |
| --- |--- | --- |
| 迴歸 | 預測值。 | [促進式決策樹回歸](boosted-decision-tree-regression.md) <br/> [決策樹系回歸](decision-forest-regression.md) <br/> [線性回歸](linear-regression.md)  <br/> [類神經網路回歸](neural-network-regression.md)  <br/> |
| 叢集 | 將資料群組在一起。| [K-表示群集](k-means-clustering.md)
| 分類 | 預測類別。  選擇 [從二進位（雙類別）] 或 [多元] 演算法。| [多元促進式決策樹](multiclass-boosted-decision-tree.md) <br/> [多元決策樹系](multiclass-decision-forest.md) <br/> [多元羅吉斯回歸](multiclass-logistic-regression.md)  <br/> [多元類神經網路](multiclass-neural-network.md) <br/> [一個與所有多元](one-vs-all-multiclass.md) <br/> [雙類別平均認知](two-class-averaged-perceptron.md) <br/>  [二元促進式決策樹](two-class-boosted-decision-tree.md)  <br/> [雙類別決策樹系](two-class-decision-forest.md) <br/>  [雙類別羅吉斯回歸](two-class-logistic-regression.md) <br/> [雙類別類神經網路](two-class-neural-network.md) <br/> [兩個類別支援向量機器](two-class-support-vector-machine.md) | 

## <a name="modules-for-building-and-evaluating-models"></a>用於建立和評估模型的模組

| 功能 | 描述 | 模組 |
| --- |--- | --- |
| 模型訓練 | 透過演算法執行資料。 |  [定型群集模型](train-clustering-model.md) <br/> [訓練模型](train-model.md)  <br/> [微調模型超參數](tune-model-hyperparameters.md) |
| 模型評分和評估 | 測量定型模型的精確度。 | [套用轉換](apply-transformation.md) <br/> [將資料指派給群集](assign-data-to-clusters.md) <br/> [交叉驗證模型](cross-validate-model.md) <br/> [評估模型](evaluate-model.md) <br/> [評分模型](score-model.md) |
| Python 語言 | 撰寫程式碼並將它內嵌在模組中，以整合 Python 與您的管線。 | [建立 Python 模型](create-python-model.md) <br/> [執行 Python 腳本](execute-python-script.md) |
| R 語言 | 撰寫程式碼並將它內嵌在模組中，以將 R 與您的管線整合。 | [執行 R 腳本](execute-r-script.md) |
| 文字分析 | 提供專門用來處理結構化和非結構化文字的計算工具。 | [從文字解壓縮 N 語法特徵](extract-n-gram-features-from-text.md) <br/> [功能雜湊](feature-hashing.md) <br/> [前置處理文字](preprocess-text.md) |
| 建議 | 建立建議模型。 | [評估推薦](evaluate-recommender.md) <br/> [評分 SVD 推薦](score-svd-recommender.md) <br/> [訓練 SVD 推薦](train-SVD-recommender.md) |

## <a name="error-messages"></a>錯誤訊息

瞭解您在 Azure Machine Learning 設計工具中使用模組時可能會遇到的[錯誤訊息和例外狀況代碼](designer-error-codes.md)。

## <a name="next-steps"></a>後續步驟

* [教學課程：在設計師中建立模型以預測自動價格](../tutorial-designer-automobile-price-train-score.md)
