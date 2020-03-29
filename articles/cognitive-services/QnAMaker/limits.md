---
title: 限制和界限 - QnA Maker
description: QnA Maker 知識庫和服務的某些部分有中繼資料的限制。 請務必將您的知識庫保持在這些限制範圍內，以便進行測試及發佈。
ms.topic: reference
ms.date: 02/14/2020
ms.openlocfilehash: 6375a6c6efc0c7016d9947e04e9479385aa80af5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273341"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>QnA Maker 知識庫的限制和界限

下面提供的 QnA Maker 限制是[Azure 認知搜索定價層限制](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)和[QnA 製造商定價層限制](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)的組合。 您需要瞭解兩組限制，以瞭解每個資源可以創建多少知識庫，以及每個知識庫可以增長多大。

## <a name="knowledge-bases"></a>知識庫

知識庫的最大數量基於[Azure 認知搜索層限制](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)。

|**Azure 認知搜索層** | **自由** | **基本** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|允許的已發佈知識庫數目上限|2|14|49|199|199|2,999|

 比方說，如果您的層次有 15 個允許的索引，您可以發佈 14 個知識庫 (每個已發佈的知識庫 1 個索引)。 第十五個索引 (`testkb`) 用於撰寫和測試所有知識庫。

## <a name="extraction-limits"></a>擷取限制

### <a name="file-naming-constraints"></a>檔命名約束

檔案名不能包括以下字元：

|不要使用字元|
|--|
|單報價`'`|
|雙報價`"`|

### <a name="maximum-file-size"></a>檔案大小上限

|[格式]|最大檔案大小 （MB）|
|--|--|
|`.docx`|10|
|`.pdf`|25|
|`.tsv`|10|
|`.txt`|10|
|`.xlsx`|3|

### <a name="maximum-number-of-files"></a>檔的最大數量

可以提取的檔的最大數量和最大檔案大小基於**[QnA Maker 定價層限制](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)**。

### <a name="maximum-number-of-deep-links-from-url"></a>來自 URL 的深層連結的最大數量

可以從 URL 頁提取 QNA 可爬網的最大深度連結數為**20**。

## <a name="metadata-limits"></a>中繼資料限制

中繼資料以基於文本的鍵：值對的形式顯示，如`product:windows 10`。 它被存儲和比較在小寫。

### <a name="by-azure-cognitive-search-pricing-tier"></a>按 Azure 認知搜索定價層

每個知識庫的最大元資料欄位數基於**[Azure 認知搜索層限制](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)**。

|**Azure 認知搜索層** | **自由** | **基本** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|每項 QnA Maker 服務的中繼資料欄位數上限 (橫跨所有的知識庫)|1,000|100*|1,000|1,000|1,000|1,000|

### <a name="by-name-and-value"></a>按名稱和值

下表列出了中繼資料名稱和值的長度和可接受字元。

|Item|允許的字元|雷格斯模式匹配|最大字元|
|--|--|--|--|
|名稱（鍵）|允許<br>字母數位（字母和數位）<br>`_`（底線）<br> 不能包含空格。|`^[a-zA-Z0-9_]+$`|100|
|值|允許除<br>`:`（結腸）<br>`|`（垂直管道）<br>只允許一個值。|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>知識庫的內容限制
知識庫內容的整體限制：
* 解答文字的長度：25,000
* 問題文字的長度：1,000
* 中繼資料索引鍵/值文字的長度：100
* 中繼資料名稱的支援字元：字母、數位和`_`
* 中繼資料值的支援字元：除`:`和`|`
* 檔案名稱的長度：200
* 支援的檔案格式：".tsv"、".pdf"、".txt"、".docx"、".xlsx"。
* 備用問題的最大數量： 300
* 答案對的最大數量：取決於所選的 Azure**[認知搜索層](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits)**。 問答對映射到 Azure 認知搜索索引上的文檔。
* URL/HTML 頁面：100 萬個字元

## <a name="create-knowledge-base-call-limits"></a>建立知識庫呼叫限制：
這些代表每個建立知識庫動作的限制；也就是，按一下 [建立知識庫]** 或呼叫 CreateKnowledgeBase API。
* 每個答案的備用問題的最大數量： 300
* URL 數目上限︰10
* 檔案數目上限︰10

## <a name="update-knowledge-base-call-limits"></a>更新知識庫呼叫限制
這些代表每個更新動作的限制；也就是，按一下 [儲存及訓練]** 或呼叫 UpdateKnowledgeBase API。
* 每個來源名稱的長度：300
* 添加或刪除的備用問題的最大數量：300
* 新增或刪除的中繼資料欄位數目上限：10
* 可重新整理的 URL 數目上限：5

## <a name="next-steps"></a>後續步驟

瞭解何時以及如何更改[服務定價層](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku)。
