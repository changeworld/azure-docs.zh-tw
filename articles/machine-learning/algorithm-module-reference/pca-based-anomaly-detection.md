---
title: 基於 PCA 的異常檢測：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何使用基於 PCA 的異常檢測模組創建基於主元件分析 （PCA） 的異常檢測模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 0672b9769feae65c73a6f752a268968a7bad9e4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502980"
---
# <a name="pca-based-anomaly-detection"></a>以 PCA 為基礎的異常偵測

本文介紹如何在 Azure 機器學習設計器（預覽）中使用**基於 PCA 的異常檢測**模組，以創建基於主元件分析 （PCA） 的異常檢測模型。

此模組可説明您在易於從一個類（如有效事務）獲取訓練資料但難以獲取目標異常的足夠樣本的情況下構建模型。 

例如，要檢測欺詐易，您通常沒有足夠的欺詐示例來培訓，但有許多良好交易示例。 **基於 PCA 的異常檢測**模組通過分析可用功能來確定什麼構成"正常"類，並應用距離指標來識別表示異常的案例，從而解決了問題。 這允許您使用現有的不平衡資料訓練模型。

## <a name="more-about-principal-component-analysis"></a>進一步了解主成份分析

*主要元件分析*（通常縮寫為PCA）是機器學習中的一種成熟技術。 PCA 常用於探索資料分析，因為它會顯示資料的內部結構，並說明資料中的變異數。

PCA 藉由分析包含多個變數的資料來運作。 它會尋找變數之間的關聯性，並決定最適合擷取結果中差異的值組合。 這些組合的特徵值用於創建稱為*主元件*的更緊湊的要素空間。

對於異常檢測，對每個新輸入進行分析，異常檢測演算法計算其對eigenvector的投影，以及一個正常化的重建誤差。 正常化錯誤用作異常分數。 錯誤越高，表示實例越異常。

有關 PCA 的工作原理以及異常檢測實現的詳細資訊，請參閱以下論文：

- [主體分量分析的隨機演算法](https://arxiv.org/abs/0809.2274)。 羅克林、斯蘭和泰格特

- [查找具有隨機性的結構：用於構造近似矩陣分解](http://users.cms.caltech.edu/~jtropp/papers/HMT11-Finding-Structure-SIREV.pdf)（PDF 下載）的概率演算法。 哈爾科、馬丁松和特羅普

## <a name="how-to-configure-pca-anomaly-detection"></a>如何配置 PCA 異常檢測

1. 將**基於 PCA 的異常檢測**模組添加到設計器中的管道中。 您可以在**異常檢測**類別中找到此模組。

2. 在**基於 PCA 的異常檢測**模組的右側面板中，按一下 **"訓練模式"** 選項，並指示是使用一組特定的參數訓練模型，還是使用參數掃描來查找最佳參數。

    - **單一參數**：如果您知道如何配置模型，並提供一組特定的值作為參數，請選擇此選項。

3. **PCA 中要使用的元件數**：指定要輸出的輸出要素或元件數。

    決定要包括多少個元件是使用PCA進行實驗設計的重要組成部分。 一般指導是，不應包含與存在變數相同的 PCA 元件數。 相反，您應該從一些較小的元件開始，然後增加它們，直到滿足某些條件。

    當輸出元件數**小於**資料集中可用的要素列數時，將獲得最佳結果。

4. 指定在隨機 PCA 培訓期間要執行的過採樣量。 在異常檢測問題中，資料不平衡使得應用標準 PCA 技術變得困難。 通過指定一些過採樣量，可以增加目標實例的數量。

    如果指定 1，則不執行過採樣。 如果指定任何高於 1 的值，則將生成其他示例以用於訓練模型。

    有兩個選項，具體取決於是否使用參數掃描：

    - **隨機 PCA 的過採樣參數**：鍵入表示少數類超採樣比正常類的單整數。 （使用**單參數**訓練方法時可用。

    > [!NOTE]
    > 不能查看過採樣資料集。 有關過度取樣如何與 PCA 一起使用的詳細資訊，請參閱[技術說明](#technical-notes)。

5. **啟用輸入要素均值正常化**：選擇此選項將所有輸入要素正常化為零平均值。 通常建議 PCA 正常化或縮放為零，因為 PCA 的目標是最大化變數之間的方差。

     預設會選取此選項。 如果已使用不同的方法或比例對值進行正常化，則取消選擇此選項。

6. 連接標記的培訓資料集和其中一個訓練模組：

    - 如果將 **"創建訓練器模式"** 選項設置為 **"單一參數**"，請使用[訓練異常檢測模型](train-anomaly-detection-model.md)模組。

7. 提交管道。

## <a name="results"></a>結果

培訓完成後，您可以保存已訓練的模型，也可以將其連接到[分數模型](score-model.md)模組以預測異常分數。

評估異常檢測模型的結果需要一些其他步驟：

1. 確保分數列在兩個資料集中都可用

    如果您嘗試評估異常檢測模型並獲取錯誤，"評分資料集中沒有要比較的分數列"，則意味著您使用的是典型的評估資料集，該資料集包含標籤列，但沒有概率分數。 您需要選擇與異常檢測模型的架構輸出匹配的資料集，其中包括**評分標籤**和**評分概率**列。

2. 確保標記標籤列

    有時，與標籤列關聯的中繼資料在管道圖中被刪除。 如果發生這種情況，當您使用[評估模型](evaluate-model.md)模組比較兩個異常檢測模型的結果時，您可能會得到錯誤，"評分資料集中沒有標籤列"或"評分資料集中沒有要比較的標籤列"。

    通過在[評估模型](evaluate-model.md)模組之前添加["編輯中繼資料"](edit-metadata.md)模組，可以避免此錯誤。 使用欄選取器選擇類列，在 **"欄位**"下拉清單中選擇 **"標籤**"。

3. 使用[執行 Python 腳本](execute-python-script.md)將標籤列類別調整為 1（正、正常）和 0（負、異常）。

    ````
    label_column_name = 'XXX'
    anomaly_label_category = YY
    dataframe1[label_column_name] = dataframe1[label_column_name].apply(lambda x: 0 if x == anomaly_label_category else 1)
    ````

    
## <a name="technical-notes"></a>技術說明

此演算法使用 PCA 近似包含正常類的子空間。 子空間由與資料共變數矩陣的頂eigen值關聯的 eigenvector 跨越。 對於每個新輸入，異常檢測器首先計算其在 eigenvector 上的投影，然後計算正常化重建誤差。 此錯誤是異常分數。 錯誤越高，實例越異常。 有關如何計算正常空間的詳細資訊，請參閱維琪百科：[主要元件分析](https://wikipedia.org/wiki/Principal_component_analysis) 


## <a name="next-steps"></a>後續步驟

請參閱 Azure 機器學習[可用的模組集](module-reference.md)。 

有關特定于設計器模組的錯誤清單[，請參閱設計器的異常和錯誤代碼（預覽](designer-error-codes.md)）。