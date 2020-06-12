---
title: 將 Word 轉換為向量
titleSuffix: Azure Machine Learning
description: 了解如何使用三種提供的 Word2Vec 模型，從文字主體擷取詞彙及其對應的文字內嵌。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/19/2020
ms.openlocfilehash: e0e796b75690bcacc6be8ef29b8b490c7faa40af
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/26/2020
ms.locfileid: "83853650"
---
# <a name="convert-word-to-vector"></a>將 Word 轉換為向量

本文說明如何使用 Azure Machine Learning 設計工具 (預覽) 中的**將文字轉換為向量**模組，對您指定作為輸入的文字主體套用各種不同的 Word2Vec 模型 (Word2Vec、FastText、預先定型的 Glove 模型)，並產生具有文字內嵌的詞彙。

此模組會使用 Gensim 程式庫。 如需 Gensim 的詳細資訊，請參閱其[官方網站](https://radimrehurek.com/gensim/apiref.html)，其中包含演算法的教學課程和說明。

### <a name="more-about-convert-word-to-vector"></a>將文字轉換為向量的相關資訊

一般而言，將文字轉換為向量 (或稱為文字向量化) 是自然語言處理程序，會使用語言模型或技術將文字對應到向量空間中，也就是說，以實數的向量表示每個字，同時允許意義相近的文字具有類似的表示法。

文字內嵌可作為 NLP 下游工作 (例如文字分類、情感分析等) 的初始輸入。

文字內嵌技術種類繁多，而在此模組中，我們實作三種廣受使用的方法，包括兩個線上訓練模型 Word2Vec 和 FastText，以及一個預先定型的模型 glove-wiki-gigaword-100。 線上訓練模型會對您的輸入資料進行定型，而預先定型的模型則會以離線方式對通常包含約 1000 億個字的較大文字主體 (例如維基百科、Google 新聞) 進行定型，隨後在文字向量化期間，文字內嵌將保持不變。 預先定型的文字模型具有某些優點，例如可縮短訓練時間、文字向量編碼效果較佳，以及可改善整體效能。

+ 在各種使用淺層類神經網路學習文字內嵌的技術中，Word2Vec 最廣受使用，下列文件會討論其理論，並以 PDF 形式供使用者下載：[有效估計向量空間中的文字表示法 (Mikolov、Tomas 等人合著)](https://arxiv.org/pdf/1301.3781.pdf)。此模組中的實作以[適用於 Word2Vec 的 Gensim 程式庫](https://radimrehurek.com/gensim/models/word2vec.html)為基礎。

+ 下列文件會說明 FastText 的理論，並以 PDF 形式供使用者下載：[使用部分字組資訊擴充文字向量 (Bojanowski、Piotr 等人合著)](https://arxiv.org/pdf/1607.04606.pdf)。此模組中的實作以[適用於 FastText 的 Gensim 程式庫](https://radimrehurek.com/gensim/models/fasttext.html)為基礎。

+ 預先定型的 Glove 模型：glove-wiki-gigaword-100，是以維基百科文字主體為基礎的預先定型向量集合，其中包含 56 億個 Token 和 40 萬個不區分大小寫的詞彙，且有 PDF 可供使用：[GloVe：文字表示法的全域向量](https://nlp.stanford.edu/pubs/glove.pdf)。

## <a name="how-to-configure-convert-word-to-vector"></a>如何設定「將文字轉換為向量」

此模組需要包含文字資料行的資料集，經過前置處理的文字較為適合。

1. 在您的管線中新增**將文字轉換為向量**模組。

2. 提供包含一或多個文字資料行的資料集，作為模組的輸入。

3. 針對**目標資料行**，選擇要處理的文字所在的單一資料行。

    一般而言，由於此模組會從文字建立詞彙，不同的資料行會有不同的內容，而導致詞彙內容不同，因此模組僅接受一個目標資料行。

4. 針對 **Word2Vec 策略**，選擇 `GloVe pretrained English Model`、`Gensim Word2Vec` 或 `Gensim FastText`。

5. 如果 **Word2Vec 策略**為 `Gensim Word2Vec` 或 `Gensim FastText`：

    + **Word2Vec 訓練演算法**。 請選擇 `Skip_gram` 或 `CBOW`。 原始[文件](https://arxiv.org/pdf/1301.3781.pdf)中說明了兩者的差異。

        預設方法為 `Skip_gram`。

    + **文字內嵌的長度**。 指定文字向量的維度。 對應於 Gensim 中的 `size` 參數。

        預設 embedding_size 為 100。

    + **內容視窗大小**。 指定要預測的文字與目前文字之間的最大距離。 對應於 Gensim 中的 `window` 參數。

        預設視窗大小為 5。

    + **Epoch 的數目**。 指定主體的 Epoch (反覆運算) 數目。 對應於 Gensim 中的 `iter` 參數。

        預設 Epoch 數目為 5。

6. 針對**詞彙大小上限**，指定產生的詞彙可包含的最大字數。

    如果不重複的文字超出此數量，則會剪除較少出現的文字。

    預設詞彙大小為 10000。

7. 針對**字數下限**，提供字數下限，讓模組忽略所有出現頻率低於此下限值的文字。

    預設值為 5。

8. 提交管線。

## <a name="examples"></a>範例

此模組產生了一個輸出：

+ **具有內嵌的詞彙**：包含產生的詞彙以及每個字的內嵌，一個維度會佔用一個資料行。

### <a name="result-examples"></a>結果範例

為了說明**將文字轉換為向量**模組的運作方式，下列範例會將此模組的預設設定套用至 Azure Machine Learning (預覽) 中經過前置處理的維基百科 SP 500 資料集。

#### <a name="source-dataset"></a>來源資料集

此資料集包含類別資料行，以及從維基百科取得的完整文字。 下表僅顯示幾個代表性範例。

|text|
|----------|
|nasdaq 100 component s p 500 component foundation founder location city apple campus 1 infinite loop street infinite loop cupertino california cupertino california location country united states...|
|br nasdaq 100 nasdaq 100 component br s p 500 s p 500 component industry computer software foundation br founder charles geschke br john warnock location adobe systems...|
|s p 500 s p 500 component industry automotive industry automotive predecessor general motors corporation 1908 2009 successor...|
|s p 500 s p 500 component industry conglomerate company conglomerate foundation founder location city fairfield connecticut fairfield connecticut location country usa area...|
|br s p 500 s p 500 component foundation 1903 founder william s harley br arthur davidson harley davidson founder arthur davidson br walter davidson br william a davidson location...|

#### <a name="output-vocabulary-with-embeddings"></a>具有內嵌的輸出詞彙

下表包含此模組以維基百科 SP 500 資料集作為輸入時的輸出。 最左邊的資料行表示詞彙，其內嵌向量以相同資料列中其餘資料行的值來表示。

|詞彙|內嵌維度 0|內嵌維度 1|內嵌維度 2|內嵌維度 3|內嵌維度 4|內嵌維度 5|...|內嵌維度 99|
|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|
|nasdaq|-0.375865|0.609234|0.812797|-0.002236|0.319071|-0.591986|...|0.364276
|component|0.081302|0.40001|0.121803|0.108181|0.043651|-0.091452|...|0.636587
|s|-0.34355|-0.037092|-0.012167|0.151542|0.601019|0.084501|...|0.149419
|p|-0.133407|0.073244|0.170396|0.326706|0.213463|-0.700355|...|0.530901
foundation|-0.166819|0.10883|-0.07933|-0.073753|0.262137|0.045725|...|0.27487
founder|-0.297408|0.493067|0.316709|-0.031651|0.455416|-0.284208|...|0.22798
location|-0.375213|0.461229|0.310698|0.213465|0.200092|0.314288|...|0.14228
city|-0.460828|0.505516|-0.074294|-0.00639|0.116545|0.494368|...|-0.2403
apple|0.05779|0.672657|0.597267|-0.898889|0.099901|0.11833|...|0.4636
campus|-0.281835|0.29312|0.106966|-0.031385|0.100777|-0.061452|...|0.05978
infinite|-0.263074|0.245753|0.07058|-0.164666|0.162857|-0.027345|...|-0.0525
loop|-0.391421|0.52366|0.141503|-0.105423|0.084503|-0.018424|...|-0.0521

在此範例中，我們使用預設 `Gensim Word2Vec` 作為 **Word2Vec 策略**，**訓練演算法**為 `Skip-gram`，**文字內嵌長度**為 100，因此，我們會有 100 個內嵌資料行。

## <a name="technical-notes"></a>技術說明

本節包含使用提示和常見問題的解答。

+ 線上定型與預先定型模型的差異

    在這個**將文字轉換為向量模組**中，我們提供了三種不同的策略，即兩個線上訓練模型，和一個預先定型的模型。 線上訓練模型會使用您的輸入資料集作為訓練資料，並在訓練期間產生詞彙和文字向量，而預先定型的模型則已使用較大的文字主體 (例如維基百科或 Twitter 文字) 進行定型，因此預先定型的模型實際上是文字/內嵌配對的集合。  

    如果選擇預先定型的 Glove 模型作為文字向量化策略，則會彙總輸入資料集中的詞彙，並從預先定型的模型產生每個字的內嵌向量，而不需要線上訓練，因此使用預先定型的模型可節省訓練時間，且在輸入資料集的大小較小時，效能尤佳。

+ 內嵌大小

    一般而言，文字內嵌的長度會設定為數百 (例如 100、200、300) 以達到良好效能，因為較小的內嵌大小意味著向量空間也較小，而可能會造成文字內嵌衝突。  

    預先定型模型的文字內嵌長度是固定的，在此實作中，glove-wiki-gigaword-100 的內嵌大小為 100。


## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 

如需設計工具 (預覽) 模組特有的錯誤清單，請參閱 [Machine Learning 錯誤碼](designer-error-codes.md)。
