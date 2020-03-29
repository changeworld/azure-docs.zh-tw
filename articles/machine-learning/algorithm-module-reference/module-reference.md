---
title: 演算法和模組參考
description: 瞭解 Azure 機器學習設計器中可用的模組（預覽版）
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: peterclu
ms.author: peterlu
ms.date: 02/22/2020
ms.openlocfilehash: c24c7ce1d47a4734d79d7e17e9fb4b3a2d4d70e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064149"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer-preview"></a>Azure 機器學習設計器的演算法&模組參考（預覽）

此參考內容提供了 Azure 機器學習設計器（預覽）中提供的每個機器學習演算法和模組的技術背景。

每個模組表示一組代碼，這些代碼可以獨立運行，並在給定所需輸入的情況下執行機器學習任務。 模組可能包含特定的演算法，或執行在機器學習中很重要的工作，例如遺漏值取代或統計分析。

有關選擇演算法的説明，請參閱 
* [如何選取演算法](../how-to-select-algorithms.md)
* [Azure 機器學習演算法備忘表](../../synapse-analytics/sql-data-warehouse/cheat-sheet.md)

> [!TIP]
> 在設計器中的任何管道中，都可以獲取有關特定模組的資訊。 選取模組，然後在 [快速說明]**** 窗格中選取 [更多說明]**** 連結。

## <a name="data-preparation-modules"></a>資料準備模組


| 功能 | 描述 | 模組 |
| --- |--- | --- |
| 資料輸入和輸出 | 將資料從雲源移動到管道中。 在運行管道時，將結果或中間資料寫入 Azure 存儲、SQL 資料庫或 Hive，或使用雲存儲在管道之間交換資料。  | [手動輸入資料](enter-data-manually.md) <br/> [匯出資料](export-data.md) <br/> [導入資料](import-data.md) |
| 資料轉換 | 對機器學習獨有的資料的操作，例如正常化或分箱資料、維度縮減以及各種檔案格式之間的轉換資料。| [新增資料行](add-columns.md) <br/> [加入資料列](add-rows.md) <br/> [套用數學運算](apply-math-operation.md) <br/> [套用 SQL 轉換](apply-sql-transformation.md) <br/> [清除遺漏的資料](clean-missing-data.md) <br/> [剪輯值](clip-values.md) <br/> [轉換為 CSV](convert-to-csv.md) <br/> [轉換為資料集](convert-to-dataset.md) <br/> [轉換為指標值](convert-to-indicator-values.md) <br/> [編輯中繼資料](edit-metadata.md) <br/> [聯結資料](join-data.md) <br/> [將資料標準化](normalize-data.md) <br/> [分區和示例](partition-and-sample.md)  <br/> [移除重複的資料列](remove-duplicate-rows.md) <br/> [SMOTE](smote.md) <br/> [選取資料行轉換](select-columns-transform.md) <br/> [選取資料集中的資料行](select-columns-in-dataset.md) <br/> [分割資料](split-data.md) |
| 特徵選取 | 選擇要用於構建分析模型的相關有用功能的子集。 | [以篩選為基礎的特徵選取](filter-based-feature-selection.md) <br/> [排列功能重要性](permutation-feature-importance.md) |
| 統計函數 | 提供與資料科學相關的多種統計方法。 | [資料總結](summarize-data.md)|

## <a name="machine-learning-algorithms"></a>機器學習演算法

| 功能 | 描述 | 模組 |
| --- |--- | --- |
| 迴歸 | 預測值。 | [促進式決策樹迴歸](boosted-decision-tree-regression.md) <br/> [決策樹系迴歸](decision-forest-regression.md) <br/> [線性回歸](linear-regression.md)  <br/> [類神經網路迴歸](neural-network-regression.md)  <br/> |
| 叢集 | 將資料分組在一起。| [K-Means 叢集](k-means-clustering.md)
| 分類 | 預測類。  從二進位（雙類）或多類演算法中進行選擇。| [多元促進式決策樹](multiclass-boosted-decision-tree.md) <br/> [多元決策樹系](multiclass-decision-forest.md) <br/> [多元羅吉斯迴歸](multiclass-logistic-regression.md)  <br/> [多元神經網路](multiclass-neural-network.md) <br/> [一與全類](one-vs-all-multiclass.md) <br/> [二元平均感知器](two-class-averaged-perceptron.md) <br/>  [二元促進式決策樹](two-class-boosted-decision-tree.md)  <br/> [二元決策樹系](two-class-decision-forest.md) <br/>  [二元羅吉斯迴歸](two-class-logistic-regression.md) <br/> [二元神經網路](two-class-neural-network.md) <br/> [二元支援向量機器](two-class-support-vector-machine.md) | 

## <a name="modules-for-building-and-evaluating-models"></a>用於構建和評估模型的模組

| 功能 | 描述 | 模組 |
| --- |--- | --- |
| 模型訓練 | 通過演算法運行資料。 |  [定型叢集模型](train-clustering-model.md) <br/> [火車模型](train-model.md)  <br/> [調整模型超參數](tune-model-hyperparameters.md) |
| 模型評分和評估 | 測量經過訓練的模型的準確性。 | [套用轉換](apply-transformation.md) <br/> [將資料分配給群集](assign-data-to-clusters.md) <br/> [交叉驗證模型](cross-validate-model.md) <br/> [評估模型](evaluate-model.md) <br/> [計分模型](score-model.md) |
| Python 語言 | 編寫代碼並將其嵌入到模組中，以便將 Python 與管道集成。 | [建立 Python 模型](create-python-model.md) <br/> [執行 Python 指令碼](execute-python-script.md) |
| R 語言 | 編寫代碼並將其嵌入到模組中，以便將 R 與管道集成。 | [執行 R 指令碼](execute-r-script.md) |
| 文字分析 | 提供用於處理結構化和非結構化文本的專用計算工具。 | [從文字擷取 N-Gram 特徵](extract-n-gram-features-from-text.md) <br/> [特性雜湊](feature-hashing.md) <br/> [前置處理文字](preprocess-text.md) <br/> [潛移默化分配](latent-dirichlet-allocation.md) |
| 建議 | 生成推薦模型。 | [評估推薦](evaluate-recommender.md) <br/> [評分 SVD 推薦](score-svd-recommender.md) <br/> [訓練 SVD 推薦](train-SVD-recommender.md) |
| 異常偵測 | 構建異常檢測模型。 | [基於 PCA 的異常檢測](pca-based-anomaly-detection.md) <br/> [定型異常偵測模型](train-anomaly-detection-model.md) |


## <a name="error-messages"></a>錯誤訊息

使用 Azure 機器學習設計器中的模組瞭解可能會遇到[的錯誤訊息和異常代碼](designer-error-codes.md)。

## <a name="next-steps"></a>後續步驟

* [教程：在設計師中構建模型以預測汽車價格](../tutorial-designer-automobile-price-train-score.md)
