---
title: PCA-Based 異常偵測：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 PCA-Based 異常偵測模組，根據主體元件分析 (PCA) 來建立異常偵測模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: fa90fcb9ebc17be9a658b08873234eada98b0fba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90898459"
---
# <a name="pca-based-anomaly-detection-module"></a>PCA-Based 異常偵測模組

本文說明如何使用 Azure Machine Learning 設計工具中的 PCA-Based 異常偵測模組，根據主體元件分析 (PCA) 來建立異常偵測模型。

此課程模組可協助您在很容易從某個類別取得定型資料（例如有效的交易）的情況下建立模型，但很難取得足夠的目標異常樣本。 

例如，若要偵測詐騙交易，您通常沒有足夠的詐騙範例來進行訓練。 但是您可能會有許多良好交易的範例。 PCA-Based 異常偵測模組會藉由分析可用的功能來判斷構成「正常」類別的內容，藉此解決此問題。 然後，模組會套用距離度量來識別代表異常狀況的案例。 這種方法可讓您使用現有的不平衡資料來定型模型。

## <a name="more-about-principal-component-analysis"></a>關於主體元件分析的詳細資訊

PCA 是機器學習中已建立的技術。 它經常用於探索式資料分析，因為它會顯示資料的內部結構，並說明資料中的差異。

PCA 藉由分析包含多個變數的資料來運作。 它會尋找變數之間的關聯性，並決定最適合擷取結果中差異的值組合。 這些結合的功能值是用來建立更精簡的功能空間，稱為 *主體元件*。

針對異常偵測，會分析每個新的輸入。 異常偵測演算法會計算其在特徵向量上的投射，以及標準化的重建錯誤。 正規化的錯誤會用來做為異常分數。 錯誤越高，表示實例越異常。

如需有關 PCA 如何運作的詳細資訊，以及有關異常偵測的執行方式，請參閱下列文章：

- [主體元件分析的隨機演算法](https://arxiv.org/abs/0809.2274)，Rokhlin、Szlan 和 Tygert

- [尋找具有隨機性的結構：概率演算法來建立大約的矩陣分解](http://users.cms.caltech.edu/~jtropp/papers/HMT11-Finding-Structure-SIREV.pdf) (PDF 下載) 、Halko、Martinsson 和 Tropp

## <a name="how-to-configure-pca-based-anomaly-detection"></a>如何設定 PCA-Based 異常偵測

1. 在設計工具中，將以 **PCA 為基礎的異常偵測** 模組新增至您的管線。 您可以在 **異常偵測** 類別中找到此模組。

2. 在模組的右面板中，選取 [ **定型模式]** 選項。 指出您是否要使用一組特定參數來定型模型，或使用參數清除來尋找最佳參數。

    如果您知道要如何設定模型，請選取 [ **單一參數** ] 選項，並提供一組特定值做為引數。

3. **若要在 PCA 中使用的元件數目**，請指定您想要的輸出功能或元件的數目。

    要包含多少個元件的決策，是使用 PCA 的實驗設計很重要的一部分。 一般指引是，您不應該包含與變數相同的 PCA 元件數目。 相反地，您應該先使用較少的元件，並增加它們，直到符合某些準則為止。

    當輸出元件的數目 *小於* 資料集中可用的特徵資料行數目時，就會取得最佳結果。

4. 指定隨機 PCA 定型期間要執行的超取樣量。 在異常偵測的問題中，不平衡資料會讓您難以套用標準 PCA 技術。 藉由指定某些超取樣量，您可以增加目標實例的數目。

    如果您指定 **1**，就不會執行任何超取樣。 如果您指定任何大於 **1**的值，則會產生額外的樣本以用於定型模型。

    有兩個選項，取決於您是否使用參數清除而定：

    - **隨機化 PCA 的超取樣參數**：輸入單一整數，代表在一般類別上超取樣少數類別的比率。  (當您使用 **單一參數** 定型方法時，就可以使用此選項。 ) 

    > [!NOTE]
    > 您無法查看 oversampled 資料集。 如需有關如何搭配使用超取樣與 PCA 的詳細資訊，請參閱 [技術](#technical-notes)提示。

5. 選取 [ **啟用輸入特徵平均** 正規化] 選項，將所有輸入功能標準化為零的平均值。 通常建議將 PCA 正規化或調整為零，因為 PCA 的目標是要最大化變數之間的差異。

    預設會選取這個選項。 如果已透過不同的方法或小數位數來正規化值，請將其取消選取。

6. 連接已加上標籤的訓練資料集和其中一個定型模組。

   如果您將 [ **建立定型模式]** 選項設為 [ **單一參數**]，請使用 [ [定型異常偵測模型](train-anomaly-detection-model.md) ] 模組。

7. 提交管線。

## <a name="results"></a>結果

定型完成後，您可以儲存定型的模型。 或者，您可以將它連接到「 [評分模型](score-model.md) 」模組，以預測異常分數。

若要評估異常偵測模型的結果：

1. 確定兩個資料集都有可用的分數資料行。

    如果您嘗試評估異常偵測模型，並收到錯誤「計分資料集中沒有分數資料行要比較」，表示您使用的是包含標籤資料行但沒有機率分數的一般評估資料集。 選擇符合異常偵測模型之架構輸出的資料集，其中包括 **評分標籤** 和 **評分** 機率資料行。

2. 確定標籤資料行已標示。

    有時，與標籤資料行相關聯的中繼資料會在管線圖形中移除。 如果發生這種情況，當您使用「 [評估模型](evaluate-model.md) 」模組來比較兩個異常偵測模型的結果時，可能會收到「計分資料集中沒有標籤資料行」的錯誤。 或者，您可能會收到錯誤「要比較的計分資料集中沒有標籤資料行」。

    您可以在「[評估模型](evaluate-model.md)」模組之前加入「[編輯中繼資料](edit-metadata.md)」模組，以避免這些錯誤。 使用資料行選取器選擇 [類別] 資料行，然後在 [ **欄位** ] 清單中選取 [ **標籤**]。

3. 您可以使用 [ [執行 Python 腳本](execute-python-script.md) ] 模組，將標籤資料行分類調整為 **1 (正、正常) ** 和 **0 (負的異常) **。

    ````
    label_column_name = 'XXX'
    anomaly_label_category = YY
    dataframe1[label_column_name] = dataframe1[label_column_name].apply(lambda x: 0 if x == anomaly_label_category else 1)
    ````

    
## <a name="technical-notes"></a>技術說明

此演算法會使用 PCA 來估計包含一般類別的子空間。 子空間是由與資料共變數矩陣之 top 特徵值相關聯的特徵向量所跨越。 

針對每個新的輸入，異常偵測器會先計算它在特徵向量上的投影，然後計算正規化的重建錯誤。 此錯誤是異常分數。 錯誤愈高，實例越異常。 如需如何計算一般空間的詳細資訊，請參閱維琪百科： [主體元件分析](https://wikipedia.org/wiki/Principal_component_analysis)。 


## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 

請參閱設計工具的 [例外狀況和錯誤碼](designer-error-codes.md) ，以取得設計工具模組特定的錯誤清單。