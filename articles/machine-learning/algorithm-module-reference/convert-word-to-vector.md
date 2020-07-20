---
title: 將 Word 轉換成向量：模組參考
titleSuffix: Azure Machine Learning
description: 了解如何使用三種提供的 Word2Vec 模型，從文字主體擷取詞彙及其對應的文字內嵌。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/19/2020
ms.openlocfilehash: 21b207ece1a2a7fd6f218716912d4c4d2c2f1ee2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84753881"
---
# <a name="convert-word-to-vector-module"></a>將 Word 轉換成 Vector 模組

本文說明如何使用 Azure Machine Learning 設計工具（預覽）中的 [將單字轉換成向量] 模組來執行下列工作：

- 在您指定為輸入的文字預先定型上套用各種 Word2Vec 模型（Word2Vec、FastText、手套主體模型）。
- 使用 word 內嵌產生詞彙。

此模組會使用 Gensim 程式庫。 如需 Gensim 的詳細資訊，請參閱其[官方網站](https://radimrehurek.com/gensim/apiref.html)，其中包括教學課程和演算法的說明。

### <a name="more-about-converting-words-to-vectors"></a>深入瞭解將單字轉換成向量

一般來說，將單字轉換成向量或 word 向量化，是自然語言處理（NLP）流程。 此程式會使用語言模型或技術，將單字對應到向量空間，也就是以實數的向量來表示每個單字。 同時，它允許具有類似意義的單字具有類似的標記法。

Word 內嵌可用來做為 NLP 下游工作（例如文字分類和情感分析）的初始輸入。

在此課程模組中，不同的字組內嵌技術中，我們會實行三個廣泛使用的方法。 兩個、Word2Vec 和 FastText 都是線上訓練模型。 另一個是預先定型模型手套-wiki-gigaword-100。 

線上-訓練模型會在您的輸入資料上定型。 預先定型模型會在較大的文字主體（例如，維琪百科、Google 新聞）上離線訓練，通常包含大約100000000000個字組。 字組內嵌會在 word 向量化期間保持不變。 預先定型 word 模型提供的優點包括：縮短定型時間、更佳的文字向量編碼，以及改善整體效能。

以下是有關方法的一些資訊：

+ Word2Vec 是使用淺層類神經網路學習 word 內嵌的其中一項最受歡迎的技術。 本檔中討論的理論是以 PDF 下載的形式提供：[在向量空間中以 Mikolov、Tomas、et 的字組表示有效率地評估](https://arxiv.org/pdf/1301.3781.pdf)。此課程模組中的實作為[以 Word2Vec 的 Gensim 程式庫為](https://radimrehurek.com/gensim/models/word2vec.html)基礎。

+ 本檔會說明 FastText 理論，以 PDF 下載的形式提供：透過[Bojanowski、Piotr、et al 的部分字組資訊來擴充文字向量](https://arxiv.org/pdf/1607.04606.pdf)。此課程模組中的實作為[以 FastText 的 Gensim 程式庫為](https://radimrehurek.com/gensim/models/fasttext.html)基礎。

+ 手套預先定型模型是手套-wiki-gigaword-100。 它是以維琪百科文字主體為基礎的預先定型向量集合，其中包含 5600000000 token 和 400000 uncased 詞彙字組。 有可用的 PDF 下載：[手套： Word 標記法的全域向量](https://nlp.stanford.edu/pubs/glove.pdf)。

## <a name="how-to-configure-convert-word-to-vector"></a>如何設定「將文字轉換為向量」

此模組需要包含文字資料行的資料集。 前置處理過的文字較佳。

1. 在您的管線中新增**將文字轉換為向量**模組。

2. 作為模組的輸入，提供包含一或多個文字資料行的資料集。

3. 針對 [**目標資料行**]，只選擇一個包含要處理之文字的資料行。

    因為此模組會從文字建立詞彙，所以資料行的內容會不同，這會導致不同的詞彙內容。 這就是為什麼模組只接受一個目標資料行的原因。

4. 針對**Word2Vec 策略**，請選擇**手套預先定型英文模型**、 **Gensim Word2Vec**和**Gensim FastText**。

5. 如果**Word2Vec 策略**為**Gensim Word2Vec**或**Gensim FastText**：

    + 針對**Word2Vec 訓練演算法**，請選擇 [從**Skip_gram**和**CBOW**]。 [原始紙張（PDF）](https://arxiv.org/pdf/1301.3781.pdf)中引進了差異。

        預設方法為**Skip_gram**。

    + 針對 [字組內嵌的**長度**]，指定單字向量的維度。 此設定會對應至 `size` Gensim 中的參數。

        預設的內嵌大小為100。

    + 針對 [**內容視窗大小]**，指定要預測之單字與目前文字之間的最大距離。 此設定會對應至 `window` Gensim 中的參數。

        預設視窗大小為5。

    + 針對 [ **Epoch 數目**]，指定主體上的 epoch 數目（反覆運算）。 此設定會對應至 `iter` Gensim 中的參數。

        預設 epoch 數為5。

6. 針對 [**詞彙大小上限**]，指定所產生詞彙中的單字數目上限。

    如果有更多不重複的單字，則會剪除不常用的字詞。

    預設詞彙大小為10000。

7. 針對 [**字數下限**]，提供字數下限。 此模組會忽略頻率低於此值的所有文字。

    預設值為 5。

8. 提交管線。

## <a name="examples"></a>範例

此模組產生了一個輸出：

+ **具有內嵌的詞彙**：包含產生的詞彙以及每個字組的內嵌。 一個維度會佔用一個資料行。

下列範例說明 Convert Word to Vector 模組的運作方式。 它會將此模組的預設設定套用至 Azure Machine Learning （預覽）中所提供的前置處理維琪百科 SP 500 資料集。

### <a name="source-dataset"></a>來源資料集

此資料集包含類別目錄資料行，以及從維琪百科取出的完整文字。 下表僅顯示幾個代表性範例。

|Text|
|----------|
|nasdaq 100 component s p 500 component foundation founder location city apple campus 1 infinite loop street infinite loop cupertino california cupertino california location country united states...|
|br nasdaq 100 nasdaq 100 component br s p 500 s p 500 component industry computer software foundation br founder charles geschke br john warnock location adobe systems...|
|s p 500 s p 500 component industry automotive industry automotive predecessor general motors corporation 1908 2009 successor...|
|s p 500 s p 500 component industry conglomerate company conglomerate foundation founder location city fairfield connecticut fairfield connecticut location country usa area...|
|br s p 500 s p 500 component foundation 1903 founder william s harley br arthur davidson harley davidson founder arthur davidson br walter davidson br william a davidson location...|

### <a name="output-vocabulary-with-embeddings"></a>具有內嵌的輸出詞彙

下表包含此模組的輸出，並接受維琪百科 SP 500 資料集做為輸入。 最左邊的資料行表示詞彙。 其內嵌向量是以相同資料列中其餘的資料行值來表示。

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

在此範例中，我們使用了**Word2Vec 策略**的預設**Gensim Word2Vec** ，而**定型演算法**會**略過語法**。 **字組內嵌的長度**為100，因此我們有100內嵌的資料行。

## <a name="technical-notes"></a>技術說明

本節包含使用提示和常見問題的解答。

+ 線上訓練與預先定型模型之間的差異：

    在這個將 Word 轉換成 Vector 模組中，我們提供三種不同的策略：兩個線上訓練模型和一個預先定型模型。 線上訓練模型會使用您的輸入資料集作為訓練資料，並在定型期間產生詞彙和單字向量。 預先定型模型已由更大的文字主體（例如維琪百科或 Twitter 文字）定型。 預先定型模型實際上是文字/內嵌配對的集合。  

    如果選擇手套預先定型的模型做為「單字向量化策略」，則會從輸入資料集匯總詞彙，並為預先定型模型中的每個單字產生內嵌向量。 如果沒有線上訓練，使用預先定型模型可以節省定型時間。 它具有較佳的效能，特別是當輸入資料集的大小相對較小時。

+ 嵌入大小：

    一般來說，word 內嵌的長度會設定為一百（例如，100、200、300）以達到良好效能。 原因是小型內嵌大小表示小型向量空間，這可能會造成 word 內嵌衝突。  

    針對預先定型模型，內嵌的字組長度是固定的。 在此執行中，內嵌大小手套-wiki-gigaword-100 為100。


## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 

如需設計工具（預覽）模組特有的錯誤清單，請參閱[Machine Learning 錯誤碼](designer-error-codes.md)。
