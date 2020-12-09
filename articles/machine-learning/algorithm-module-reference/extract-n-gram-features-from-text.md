---
title: 從文字模塊參考中解壓縮 N 語法特徵
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 設計工具中的 [解壓縮 N 語法] 模組來為文字資料進行特徵化。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 12/08/2019
ms.openlocfilehash: 37a10d90fa0e277fbe45d9f1377e365cb3d42996
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861443"
---
# <a name="extract-n-gram-features-from-text-module-reference"></a>從文字模塊參考中解壓縮 N 語法特徵

本文描述 Azure Machine Learning 設計工具中的模組。 使用「文字」模組中的 [從 N- *featurize* ------------語法] 

## <a name="configuration-of-the-extract-n-gram-features-from-text-module"></a>從 Text 模組設定「解壓縮 N-語法」功能

模組支援下列使用 n 語法字典的案例：

* 從任意文字的資料行[建立新的 n 語法字典](#create-a-new-n-gram-dictionary)。

* [使用一組現有的文字特徵](#use-an-existing-n-gram-dictionary) 來為任意文字資料行設定特徵。

* [評分或部署](#build-inference-pipeline-that-uses-n-grams-to-deploy-a-real-time-endpoint) 使用 n 字母的模型。

### <a name="create-a-new-n-gram-dictionary"></a>建立新的 n 語法字典

1.  將 [文字] 模組中的 [解壓縮 N-語法] 功能新增至您的管線，並連接具有您想要處理之文字的資料集。

1.  使用 [ **文字資料行** ] 來選擇包含您要解壓縮之文字的字串類型資料行。 因為結果較詳細，所以您一次只能處理一個資料行。

1. 將 [ **詞彙模式]** **設定為 [建立** ]，表示您要建立新的 n 語法功能清單。 

1. 設定 **n** 字母的大小，以指出要解壓縮和儲存的 n 字母大小 *上限* 。 

    例如，如果您輸入3，則會建立 unigrams、雙字母組和 trigrams。

1. **加權函數** 會指定如何建立檔功能向量，以及如何從檔中將詞彙解壓縮。

    * **二元權數**：將二進位存在值指派給已解壓縮的 n 字母。 在檔中存在時，每個 n 元的值為1，否則為0。

    * **TF 權數**：將詞彙頻率 (TF) 分數指派給已解壓縮的 n 字母。 每個 n 語法的值是在檔中出現的頻率。

    * **Idf 權數**：將反向檔頻率 (IDF) 分數指派給已解壓縮的 n 字母。 每個 n 語法的值是主體大小的記錄，並在整個主體中除以出現的頻率。
    
      `IDF = log of corpus_size / document_frequency`
 
    *  **TF-IDF 權數**：指派詞彙頻率/反向檔頻率 (TF/IDF) 分數設為已解壓縮的 n 字母。 每個 n 語法的值是其 TF 分數乘以其 IDF 分數。

1. 將 [ **最小文字長度** ] 設定為 [n] 語法中任何 *單一單字* 可使用的最小字母數目。

1. 使用 [ **最大文字長度** ] 來設定可用於 n 字元中任何 *單一單字* 的最大字母數目。

    根據預設，每個單字或標記最多可有25個字元。

1. 使用 [ **最少 n----------** ------------------------克] 

    例如，如果您使用預設值5，則在主體中必須至少出現五次的 n 元，才能包含在 n 語法字典中。 

1.  針對整體主體中的資料列數目，將 **最大的 n 中繼檔比例** 設定為包含特定 n 語法的資料列數目最大比例。

    例如，比率1會指出，即使每個資料列中都有特定的 n 元，也可以將 n 中繼資料加入至 n 語法字典。 一般來說，每個資料列中發生的單字都會被視為非搜尋字，而且會被移除。 若要篩選出網域相依的非搜尋字，請嘗試減少這項比例。

    > [!IMPORTANT]
    > 特定單字的出現率不一致。 它會因檔而異。 例如，如果您要分析有關特定產品的客戶意見，則產品名稱可能會非常高，且接近非搜尋字，但在其他內容中是很重要的詞彙。

1. 選取 [正規化 **n-語法] 功能向量** 來正規化特徵向量的選項。 如果啟用此選項，則會將每個 n 元特徵向量除以其 L2 標準。

1. 提交管線。

### <a name="use-an-existing-n-gram-dictionary"></a>使用現有的 n 語法字典

1.  將 [文字] 模組中的 [解壓縮 N-語法] 功能新增至您的管線，並將具有您想要處理之文字的資料集連接到 **資料集** 埠。

1.  使用 [ **文字資料行** ]，選取包含您要特徵化之文字的文字資料行。 依預設，此模組會選取 **字串** 類型的所有資料行。 為了獲得最佳結果，請一次處理一個資料行。

1. 加入儲存的資料集，其中包含先前產生的 n 語法字典，並將其連接至 **輸入詞彙** 埠。 您也可以從文字模塊連接「解壓縮 N-語法」功能之上游實例的 **結果詞彙** 輸出。

1. 若為 **詞彙模式**，請從下拉式清單中選取 [ **ReadOnly** update] 選項。

   **ReadOnly** 選項代表輸入詞彙的輸入主體。 來自輸入詞彙的 n-語法加權會依原樣套用，而不是從新的文字資料集計算詞彙頻率 (于左側輸入) 。

   > [!TIP]
   > 當您要評分文字分類器時，請使用此選項。

1.  如需其他選項，請參閱 [上一節](#create-a-new-n-gram-dictionary)中的屬性描述。

1.  提交管線。

### <a name="build-inference-pipeline-that-uses-n-grams-to-deploy-a-real-time-endpoint"></a>使用 n 元來部署即時端點的組建推斷管線

定型管線，其中包含 **從文字** 和 **評分模型** 中解壓縮 N 字母元功能，以對測試資料集進行預測，以下列結構建立：

:::image type="content" source="./media/module/extract-n-gram-training-pipeline-score-model.png" alt-text="解壓縮 N 元訓練管線範例" border="true":::

文字模塊中的圓形 **解壓縮 N 元功能** 的 **詞彙模式** 是 **建立** 的，而且連接到 **計分模型** 模組的模組 **詞彙模式** 是 **唯讀** 的。

成功提交上述定型管線之後，您可以將圓形模組的輸出註冊為資料集。

:::image type="content" source="./media/module/extract-n-gram-output-voc-register-dataset.png" alt-text="註冊資料集" border="true":::

然後，您可以建立即時推斷管線。 建立推斷管線之後，您需要手動調整您的推斷管線，如下所示：

:::image type="content" source="./media/module/extract-n-gram-inference-pipeline.png" alt-text="推斷管線" border="true":::

然後，提交推斷管線，並部署即時端點。

## <a name="results"></a>結果

[從文字中解壓縮 N 語法的功能] 模組會建立兩種類型的輸出： 

* **結果資料集**：此輸出是已分析文字的摘要，並結合了已解壓縮的 n 字母。 您未在 [ **文字資料行** ] 選項中選取的資料行會傳遞至輸出。 針對您分析的每個文字資料行，此模組會產生這些資料行：

  * **N 語法出現次數的矩陣**：此模組會針對在 total 主體中找到的每個 n 字型產生一個資料行，並在每個資料行中加入一個分數，以指出該資料列之 n 字型的加權。 

* **結果詞彙**：詞彙包含實際的 n 語法字典，以及在分析過程中產生的「頻率分數」。 您可以使用一組不同的輸入來儲存資料集，或在稍後的更新中重複使用。 您也可以重複使用詞彙來進行模型化和評分。

### <a name="result-vocabulary"></a>結果詞彙

詞彙包含 n 個語法的字典，其中包含做為分析一部分產生的頻率分數。 無論其他選項為何，都會產生 DF 和 IDF 分數。

+ **識別碼：為** 每個唯一的 n 元所產生的識別碼。
+ **NGram**： n 語法。 空格或其他文字分隔符號會由底線字元所取代。
+ **DF**：原始主體中 n 元的詞彙頻率分數。
+ **IDF**：原始主體中 n 元的反向檔頻率分數。

您可以手動更新此資料集，但可能會導致錯誤。 例如：

* 如果模組在輸入詞彙中找到具有相同索引鍵的重復資料列，則會引發錯誤。 請確定詞彙中沒有兩個數據列有相同的字組。
* 詞彙資料集的輸入架構必須完全相符，包括資料行名稱和資料行類型。 
* **識別碼** 資料行和 **DF** 資料行必須是整數類型。 
* **IDF** 資料行必須是 float 類型。

> [!Note]
> 請勿直接將資料輸出連接到「定型模型」模組。 您應該先移除可用的文字資料行，然後再將它們送入定型模型。 否則，將會將自由文字資料行視為類別功能。

## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。
