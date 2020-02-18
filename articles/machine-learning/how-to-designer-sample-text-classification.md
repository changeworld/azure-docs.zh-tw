---
title: 設計工具：分類書籍檢閱範例
titleSuffix: Azure Machine Learning
description: 使用 Azure Machine Learning 設計工具建立多元羅吉斯迴歸分類器，以搭配 Wikipedia SP 500 資料集來預測公司類別。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 02/11/2020
ms.openlocfilehash: f15f50e372d0bfe58018b16ebfa5d5d85644ae1a
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137777"
---
# <a name="build-a-classifier-to-predict-company-category-using-azure-machine-learning-designer"></a>使用 Azure Machine Learning 設計工具建立分類器來預測公司類別。

**設計工具 (預覽) 範例 7**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

此範例會示範如何使用文字分析模組在 Azure Machine Learning 設計工具 (預覽) 中建立文字分類管線。

文字分類的目標是要將一段文字指派給一或多個預先定義的類別。 文字片段可以是文件、新聞文章、搜尋查詢、電子郵件、推文、支援票證、客戶意見反應、使用者產品評論等等。文字分類的應用包括將報紙文章和有線新聞內容分類為主題、將網頁組織成階層式類別、篩選垃圾郵件、情感分析、從搜尋查詢預測使用者意圖、路由支援票證，以及分析客戶的意見反應。 

此管線會搭配**衍生自 Wikipedia 的 Wikipedia SP 500 資料集**來訓練**多元羅吉斯迴歸分類器**，進而預測公司類別。  

搭配文字資料訓練機器學習模型的基本步驟如下：

1. 取得資料

1. 對文字資料進行前置處理

1. 特徵設計

   使用特徵擷取模組 (例如特徵雜湊、從文字資料擷取 N-Gram 特徵) 將文字特徵轉換成數值特徵。

1. 將模型定型

1. 計分資料集

1. 評估模型

以下圖表代表我們最終將使用的完整管線。 我們將提供所有模組的基本原理，讓您可以自行做出類似的決策。

[![管線的圖表](./media/how-to-designer-sample-text-classification/nlp-modules-overall.png)](./media/how-to-designer-sample-text-classification/nlp-modules-overall.png#lightbox)

## <a name="data"></a>資料

在此管線中，我們會使用 **Wikipedia SP 500** 資料集。 該資料集是從 Wikipedia (https://www.wikipedia.org/) ) 上每家 S&P 500 公司的文章衍生而來。 上傳至 Azure Machine Learning 設計工具之前，資料集已經過下列處理：

- 擷取每家特定公司的文字內容
- 移除 wiki 格式
- 移除非英數字元
- 將所有文字轉換為小寫
- 新增了知名公司類別

因為有些公司的文章找不到，所以記錄筆數小於 500 筆。

## <a name="pre-process-the-text-data"></a>對文字資料進行前置處理

我們會使用**前置處理文字**模組來對文字資料進行前置處理，包括偵測句子、Token 化句子等等。 您可以在[**前置處理文字**](algorithm-module-reference/preprocess-text.md)一文中找到所有支援的選項。 對文字資料進行前置處理之後，我們會使用**分割資料**模組來隨機分割輸入資料，讓訓練資料集包含 50% 的原始資料，以及讓測試資料集包含 50% 的原始資料。

## <a name="feature-engineering"></a>特徵設計
在此範例中，我們將使用兩個執行特徵工程的方法。

### <a name="feature-hashing"></a>特性雜湊
我們已使用[**特徵雜湊**](algorithm-module-reference/feature-hashing.md)模組將文章的純文字轉換成整數，並使用整數值作為模型的輸入特徵。 

**特徵雜湊**模組可用來將可變長度的文字文件轉換成等長的數值特徵向量 (使用 Vowpal Wabbit 程式庫所提供的 32 位元 murmurhash v3 雜湊方法)。 使用特徵雜湊的目標是縮減維度；此外，特徵雜湊也可在分類時加快查閱特徵權數的速度，因為其使用雜湊值比較，而不是字串比較。

在範例管線中，我們會將雜湊位元數設為 14，並將 N-Gram 的數目設為 2。 透過這些設定，雜湊表可以保存 2^14 個項目，其中每個雜湊特徵代表一個或多個 N-Gram 特徵，而其值代表該 N-Gram 在文字範例中出現的頻率。 對於許多問題而言，此大小的雜湊表比已足夠，但在某些情況下，可能需要更多空間來避免發生衝突。 使用不同數目的位元數來評估機器學習解決方案的成效。 

### <a name="extract-n-gram-feature-from-text"></a>從文字擷取 N-Gram 特徵

N-Gram 是指定文字序列中 n 個字詞的連續序列。 大小為 1 的 N-Gram 稱為單字元組 (unigram)；大小為 2 的 N-Gram 稱為雙字元組 (bigram)；大小為 3 的 N-Gram 稱為三字元組 (trigram)。 大小更大的 N-Gram 有時會根據 N 的值來命名，例如「四字元組 (four-gram)」、「五字元組 (five-gram)」等等。

我們已使用[**從文字擷取 N-Gram 特徵**](algorithm-module-reference/extract-n-gram-features-from-text.md)模組作為另一個特徵工程解決方案。 此模組會先擷取 N-Gram 集合，除了 N-Gram 以外，文字中出現每個 N-Gram 的文件數目也會加以計算 (DF)。 此範例會使用 TF-IDF 計量來計算特徵值。 然後，模組會將非結構化文字資料轉換成等長的數值特徵向量，其中每項特徵都代表文字範例中 N-Gram 的 TF-IDF。

將文字資料轉換成數值特徵向量之後，**選取資料行**模組會用來移除資料集中的文字資料。 

## <a name="train-the-model"></a>將模型定型

您選擇的演算法通常取決於使用案例的需求。 因為此管線的目標是要預測公司的類別，所以多元分類器模型是不錯的選擇。 考慮到特徵的數目很大，而且是稀疏特徵，因此我們會針對此管線使用**多元羅吉斯迴歸**。

## <a name="test-evaluate-and-compare"></a>測試、評估和比較

 我們會分割資料集，並使用不同的資料集來訓練和測試模型，好讓模型的評估更具目標性。

模型經過訓練之後，我們會使用**計分模型**和**評估模型**模組來產生預測的結果並評估模型。 不過，在使用**計分模型**模組之前，必須先執行我們在訓練期間所做的特徵工程設計。 

針對**特徵雜湊**模組，將計分流程上的特徵工程執行為訓練流程相當容易。 直接使用**特徵雜湊**模組來處理輸入文字資料。

針對**從文字擷取 N-Gram 特徵**模組，我們會將**結果詞彙輸出**從訓練資料流程連結到計分資料流程上的**輸入詞彙**，並將**詞彙模式**參數設定為 **ReadOnly**。
[![分數的圖表](./media/how-to-designer-sample-text-classification/n-gram.png)](./media/how-to-designer-sample-text-classification/n-gram.png)

完成工程步驟之後，**計分模型**就可以使用經過訓練的模型來針對測試資料集產生預測。 若要檢查結果，請選取**計分模型**的輸出連接埠，然後選取 [視覺化]  。

接著，我們會將分數傳遞給**評估模型**模組，以產生評估計量。 **評估模型**有兩個輸入連接埠，因此我們可以評估和比較使用不同方法所產生的計分資料集。 在此範例中，我們會比較特徵雜湊方法和 N-Gram 方法所產生的結果成效。
若要檢查結果，請選取**評估模型**的輸出連接埠，然後選取 [視覺化]  。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>後續步驟

探索設計工具的其他範例：
- [範例 1 - 迴歸：預測汽車的價格](how-to-designer-sample-regression-automobile-price-basic.md)
- [範例 2 - 迴歸：比較汽車價格預測的演算法](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [範例 3 - 使用特徵選取進行分類：收入預測](how-to-designer-sample-classification-predict-income.md)
- [範例 4 - 分類：預測信用風險 (成本導向)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [範例 5 - 分類：預測流失](how-to-designer-sample-classification-churn.md)
- [範例 6 - 分類：預測航班誤點](how-to-designer-sample-classification-flight-delay.md)
