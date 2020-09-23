---
title: 演算法和模組參考
description: 瞭解 Azure Machine Learning 表設計工具中可用的模組
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/19/2020
ms.openlocfilehash: 9a2e21696409c320b89d1fed54fefad953b955e1
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90886396"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer"></a>Azure Machine Learning 設計工具的演算法 & 模組參考

本參考內容提供 Azure Machine Learning 設計工具中可用的每個機器學習服務演算法和模組的技術背景。

每個模組都代表一組程式碼，可以獨立執行；若提供必要的輸入內容也可執行機器學習工作。 模組可能包含特定的演算法，或執行在機器學習中很重要的工作，例如遺漏值取代或統計分析。

如需如何選擇演算法的說明，請參閱 
* [如何選取演算法](../how-to-select-algorithms.md)
* [Azure Machine Learning 演算法小祕技](../algorithm-cheat-sheet.md)

> [!TIP]
> 在設計工具的任何管線中，您可以取得特定模組的相關資訊。 當滑鼠停留在模組清單中的模組或在模組的右窗格中時，請選取模組卡片中的 [深入了解] 連結。

## <a name="data-preparation-modules"></a>資料準備模組


| 功能 | 描述 | 模組 |
| --- |--- | --- |
| 資料輸入和輸出 | 將資料從雲端來源移至您的管線。 在執行管線時將結果或中繼資料寫入 Azure 儲存體、SQL Database 或 Hive，或使用雲端儲存體在管線之間交換資料。  | [手動輸入資料](enter-data-manually.md) <br/> [匯出資料](export-data.md) <br/> [匯入資料](import-data.md) |
| 資料轉換 | 機器學習特有的資料作業，例如正規化或量化資料、維度縮減，以及在各種不同的檔案格式之間轉換資料。| [新增資料行](add-columns.md) <br/> [新增資料列](add-rows.md) <br/> [套用數學運算](apply-math-operation.md) <br/> [套用 SQL 轉換](apply-sql-transformation.md) <br/> [清除遺漏的資料](clean-missing-data.md) <br/> [剪輯值](clip-values.md) <br/> [轉換為 CSV](convert-to-csv.md) <br/> [轉換為資料集](convert-to-dataset.md) <br/> [轉換為指標值](convert-to-indicator-values.md) <br/> [編輯中繼資料](edit-metadata.md) <br/> [將資料分組成 Bin](group-data-into-bins.md) <br/> [聯結資料](join-data.md) <br/> [將資料標準化](normalize-data.md) <br/> [資料分割和取樣](partition-and-sample.md)  <br/> [移除重複的資料列](remove-duplicate-rows.md) <br/> [SMOTE](smote.md) <br/> [選取資料行轉換](select-columns-transform.md) <br/> [選取資料集中的資料行](select-columns-in-dataset.md) <br/> [資料分割](split-data.md) |
| 特徵選取 | 選取相關有用功能的子集，以用於建置分析模型。 | [以篩選為基礎的特徵選取](filter-based-feature-selection.md) <br/> [排列功能重要性](permutation-feature-importance.md) |
| 統計函數 | 提供各種與資料科學相關的統計方法。 | [摘要資料](summarize-data.md)|

## <a name="machine-learning-algorithms"></a>機器學習演算法

| 功能 | 描述 | 模組 |
| --- |--- | --- |
| 迴歸 | 預測值。 | [促進式決策樹迴歸](boosted-decision-tree-regression.md) <br/> [決策樹系迴歸](decision-forest-regression.md) <br/> [快速樹系分量迴歸](fast-forest-quantile-regression.md)  <br/> [線性迴歸](linear-regression.md)  <br/> [類神經網路迴歸](neural-network-regression.md)  <br/> [波氏迴歸](poisson-regression.md)  <br/>|
| 叢集 | 將資料群組在一起。| [K-Means 群集](k-means-clustering.md)
| 分類 | 預測類別。  選擇二進位 (二元) 或多元演算法。| [多元促進式決策樹](multiclass-boosted-decision-tree.md) <br/> [多元決策樹系](multiclass-decision-forest.md) <br/> [多元羅吉斯迴歸](multiclass-logistic-regression.md)  <br/> [多元神經網路](multiclass-neural-network.md) <br/> [「一對多」多元分類](one-vs-all-multiclass.md) <br/> [二元平均感知器](two-class-averaged-perceptron.md) <br/>  [二元促進式決策樹](two-class-boosted-decision-tree.md)  <br/> [二元決策樹系](two-class-decision-forest.md) <br/>  [二元羅吉斯迴歸](two-class-logistic-regression.md) <br/> [二元神經網路](two-class-neural-network.md) <br/> [二元支援向量機器](two-class-support-vector-machine.md) | 

## <a name="modules-for-building-and-evaluating-models"></a>用於建置和評估模型的模組

| 功能 | 描述 | 模組 |
| --- |--- | --- |
| 模型定型 | 透過演算法執行資料。 |  [將群集模型定型](train-clustering-model.md) <br/> [訓練模型](train-model.md) <br/> [定型 Pytorch 模型](train-pytorch-model.md) <br/> [微調模型超參數](tune-model-hyperparameters.md) |
| 模型評分和評估 | 測量已定型模型的精確度。 | [套用轉換](apply-transformation.md) <br/> [將資料指派給叢集](assign-data-to-clusters.md) <br/> [交叉驗證模型](cross-validate-model.md) <br/> [評估模型](evaluate-model.md) <br/> [映像計分模型](score-image-model.md) <br/> [為模型評分](score-model.md) |
| Python 語言 | 撰寫程式碼並內嵌倒模組中，以整合 Python 與您的管線。 | [建立 Python 模型](create-python-model.md) <br/> [執行 Python 指令碼](execute-python-script.md) |
| R 語言 | 撰寫程式碼並內嵌倒模組中，以整合 R 與您的管線。 | [執行 R 指令碼](execute-r-script.md) |
| 文字分析 | 提供專門用來處理結構化和非結構化文字的計算工具。 |  [將文字轉換成向量](convert-word-to-vector.md) <br/> [從文字擷取 N-Gram 特徵](extract-n-gram-features-from-text.md) <br/> [特性雜湊](feature-hashing.md) <br/> [前置處理文字](preprocess-text.md) <br/> [隱含狄利克雷分佈](latent-dirichlet-allocation.md) <br/> [計分 Vowpal Wabbit 模型](score-vowpal-wabbit-model.md) <br/> [定型 Vowpal Wabbit 模型](train-vowpal-wabbit-model.md)|
| 電腦視覺 | 影像資料前置處理和影像辨識的相關模組。 |  [套用映像轉換](apply-image-transformation.md) <br/> [轉換至映像目錄](convert-to-image-directory.md) <br/> [初始化映像轉換](init-image-transformation.md) <br/> [分割映像目錄](split-image-directory.md) <br/> [DenseNet](densenet.md) <br/> [ResNet](resnet.md) |
| 建議 | 建置建議模型。 | [評估推薦](evaluate-recommender.md) <br/> [評分 SVD 推薦](score-svd-recommender.md) <br/> [廣泛評分和深度推薦](score-wide-and-deep-recommender.md)<br/> [訓練 SVD 推薦](train-SVD-recommender.md) <br/> [廣泛訓練和深度推薦](train-wide-and-deep-recommender.md)|
| 異常偵測 | 建置異常偵測模型。 | [PCA 型異常偵測](pca-based-anomaly-detection.md) <br/> [定型異常偵測模型](train-anomaly-detection-model.md) |


## <a name="web-service"></a>Web 服務

了解 [Web 服務模組](web-service-input-output.md)，其為在 Azure Machine Learning 設計工具中進行即時推斷所需的項目。

## <a name="error-messages"></a>錯誤訊息

了解您在 Azure Machine Learning 設計工具中使用模組時可能會遇到的[錯誤訊息和例外狀況代碼](designer-error-codes.md)。

## <a name="next-steps"></a>後續步驟

* [教學課程：在設計工具中建置模型以預測自動價格](../tutorial-designer-automobile-price-train-score.md)
