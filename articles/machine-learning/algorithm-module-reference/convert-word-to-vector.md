---
title: 將單字轉換成 Vector： Module 參考
titleSuffix: Azure Machine Learning
description: 了解如何使用三種提供的 Word2Vec 模型，從文字主體擷取詞彙及其對應的文字內嵌。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/19/2020
ms.openlocfilehash: 5fad3e4862b0c40c9edd00a5b9d47b245e529396
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91536727"
---
# <a name="convert-word-to-vector-module"></a>將 Word 轉換成向量模組

本文描述如何使用 Azure Machine Learning 設計工具中的 [將文字轉換成向量] 模組來執行下列工作：

- 將不同的 Word2Vec 模型套用 (Word2Vec、FastText、手套預先定型模型) 您指定為輸入的文字主體上。
- 使用 word 內嵌產生詞彙。

此模組會使用 Gensim 程式庫。 如需 Gensim 的詳細資訊，請參閱其 [官方網站](https://radimrehurek.com/gensim/apiref.html)，其中包含教學課程和演算法的說明。

### <a name="more-about-converting-words-to-vectors"></a>深入瞭解如何將單字轉換成向量

將單字轉換成向量或 word 向量化是一種自然語言處理， (NLP) 進程。 此程式會使用語言模型將文字對應至向量空間。 向量空間以實數向量表示每個單字。 它也可讓具有類似意義的單字具有類似的標記法。

使用 word 內嵌做為初始輸入，以 NLP 下游工作，例如文字分類和情感分析。

在此課程模組的各種字組內嵌技術中，我們採用了三種廣泛使用的方法。 兩個 Word2Vec 和 FastText 是線上訓練模型。 另一個是預先定型模型手套-wiki-gigaword-100。 

線上訓練模型會根據您的輸入資料進行定型。 預先定型模型會在較大的文字主體上離線定型 (例如，維琪百科、Google 新聞) ，通常會包含大約100000000000個字。 Word 內嵌在 word 向量化期間會保持不變。 預先定型字模型可提供一些優點，例如縮短定型時間、更佳的文字向量編碼，以及改善整體效能。

以下是有關這些方法的一些資訊：

+ Word2Vec 是使用淺層類神經網路學習 word 內嵌的最熱門技術之一。 這份檔中討論的理論如下：以 PDF 下載形式提供： [在向量空間中有效估計文字表示](https://arxiv.org/pdf/1301.3781.pdf)。 此課程模組中的實作為 [以 Word2Vec 的 Gensim 程式庫為](https://radimrehurek.com/gensim/models/word2vec.html)基礎。

+ 這份檔中會說明 FastText 理論，可透過 PDF 下載： [使用部分字組資訊來充實文字向量](https://arxiv.org/pdf/1607.04606.pdf)。 此課程模組中的實作為 [以 FastText 的 Gensim 程式庫為](https://radimrehurek.com/gensim/models/fasttext.html)基礎。

+ 手套預先定型模型為手套-wiki-gigaword-100。 它是預先定型向量的集合，以維琪百科文字主體為基礎，其中包含 5600000000 token 和 400000 uncased 詞彙字詞。 提供 PDF 下載： [手套：文字標記法的全域向量](https://nlp.stanford.edu/pubs/glove.pdf)。

## <a name="how-to-configure-convert-word-to-vector"></a>如何設定「將文字轉換為向量」

此模組需要包含文字資料行的資料集。 前置處理過的文字更好。

1. 在您的管線中新增**將文字轉換為向量**模組。

2. 作為模組的輸入，提供包含一或多個文字資料行的資料集。

3. 針對 [ **目標資料行**]，請只選擇一個包含要處理之文字的資料行。

    因為此模組會從文字建立詞彙，所以資料行的內容會不同，這會導致不同的詞彙內容。 這就是為什麼模組只接受一個目標資料行的原因。

4. 針對  **Word2Vec 策略**，請選擇 **手套預先定型英文模型**、 **Gensim Word2Vec**和 **Gensim FastText**。

5. 如果 **Word2Vec 策略** 是 **Gensim Word2Vec** 或 **Gensim FastText**：

    + 針對 **Word2Vec 訓練演算法**，請選擇 [ **Skip_gram** ] 和 [ **CBOW**]。 [ (PDF) 的原始紙張](https://arxiv.org/pdf/1301.3781.pdf)中會引進差異。

        預設方法是 **Skip_gram**。

    + 針對 **word**內嵌的長度，指定文字向量的維度。 此設定對應于 `size` Gensim 中的參數。

        預設的嵌入大小為100。

    + 針對 [ **內容視窗大小]**，指定要預測的單字與目前單字之間的最大距離。 此設定對應于 `window` Gensim 中的參數。

        預設視窗大小為5。

    + 針對 [ **epoch 的數目**]，指定透過主體) 的 epoch (反覆運算數目。 對應至 `iter` Gensim 中的參數。

        預設 epoch 數為5。

6. 在 [ **詞彙大小上限**] 中，指定所產生詞彙中的單字最大數目。

    如果有比最大值更多的唯一單字，請剪除不頻繁的單字。

    預設詞彙大小為10000。

7. 在 [字數 **下限**] 中，提供最小字數的字數。 模組會忽略頻率低於此值的所有單字。

    預設值為 5。

8. 提交管線。

## <a name="examples"></a>範例

此模組產生了一個輸出：

+ **具有內嵌的詞彙**：包含產生的詞彙，以及每個單字的內嵌。 一個維度會佔用一個資料行。

下列範例顯示如何將 Word 轉換成 Vector 模組。 它會使用將 Word 轉換成向量，並將預設值設定為前置處理的維琪百科 SP 500 資料集。

### <a name="source-dataset"></a>來源資料集

資料集包含類別目錄資料行，以及從維琪百科提取的完整文字。 下表顯示一些代表性範例。

|Text|
|----------|
|nasdaq 100 component s p 500 component foundation founder location city apple campus 1 infinite loop street infinite loop cupertino california cupertino california location country united states...|
|br nasdaq 100 nasdaq 100 component br s p 500 s p 500 component industry computer software foundation br founder charles geschke br john warnock location adobe systems...|
|s p 500 s p 500 component industry automotive industry automotive predecessor general motors corporation 1908 2009 successor...|
|s p 500 s p 500 component industry conglomerate company conglomerate foundation founder location city fairfield connecticut fairfield connecticut location country usa area...|
|br s p 500 s p 500 component foundation 1903 founder william s harley br arthur davidson harley davidson founder arthur davidson br walter davidson br william a davidson location...|

### <a name="output-vocabulary-with-embeddings"></a>具有內嵌的輸出詞彙

下表包含此模組的輸出，並將維琪百科 SP 500 資料集做為輸入。 最左邊的資料行表示詞彙。 它的內嵌向量會以相同資料列中其餘資料行的值來表示。

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

在此範例中，我們使用了**Word2Vec 策略**的預設**Gensim Word2Vec** ，而**定型演算法**為**Skip 語法**。 **字組內嵌的長度** 是100，因此我們有100內嵌資料行。

## <a name="technical-notes"></a>技術說明

本節包含使用提示和常見問題的解答。

+ 線上訓練和預先定型模型之間的差異：

    在這將 Word 轉換成向量模組時，我們提供三種不同的策略：兩個線上訓練模型和一個預先定型模型。 線上訓練模型會使用您的輸入資料集作為定型資料，並在定型期間產生詞彙和文字向量。 預先定型模型已經過較大的文字主體（例如維琪百科或 Twitter 文字）進行定型。 預先定型模型實際上是文字/內嵌配對的集合。  

    手套預先定型的模型會從輸入資料集匯總詞彙，並從預先定型模型產生每個單字的內嵌向量。 如果沒有線上訓練，使用預先定型模型可以節省定型時間。 它有更好的效能，特別是當輸入資料集的大小相對較大時。

+ 內嵌大小：

    一般情況下，word 內嵌的長度會設定為一百。 例如，100、200、300。 小型的內嵌大小表示較小的向量空間，這可能會造成字組內嵌衝突。  

    預先定型模型的文字內嵌長度是固定的。 在此範例中，手套-wiki-gigaword-100 的內嵌大小是100。


## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 

如需設計工具模組特定的錯誤清單，請參閱 [Machine Learning 錯誤碼](designer-error-codes.md)。
