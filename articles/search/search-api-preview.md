---
title: REST API 中的預覽功能
titleSuffix: Azure Cognitive Search
description: Azure 認知搜索服務 REST API 版本 2019-05-06-預覽包括用於增量擴充的實驗性功能，如知識存儲和索引子緩存。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: db941152186127302680b5e659e43cd2d82a8908
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162271"
---
# <a name="preview-features-in-azure-cognitive-search"></a>預覽 Azure 認知搜索中的功能

本文列出了當前預覽中的功能。 從此清單中刪除從預覽過渡到通用可用性的功能。 有關常規可用性的公告，您可以查看[服務更新](https://azure.microsoft.com/updates/?product=search)或[新增功能](whats-new.md)。

雖然門戶和 .NET SDK 中可能提供某些預覽功能，但 REST API 始終具有預覽功能。

+ 對於搜索操作，[**`2019-05-06-Preview`**](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview)是當前預覽版本。
+ 對於管理操作，[**`2019-10-01-Preview`**](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview)是當前預覽版本。

> [!IMPORTANT]
> 預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="ai-enrichment-features"></a>AI 濃縮功能

通過[預覽搜索 API](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview)探索 AI 擴充的最新增強功能。

|||
|-|-|
| [自訂實體查找技能（預覽）](cognitive-search-skill-custom-entity-lookup.md ) | 從自訂的、使用者定義的單詞和片語清單中查找文本的認知技能。 使用此清單，它將使用任何匹配的實體標記所有文檔。 該技能還支援一定程度的模糊比對，可用於查找相似但不精確的匹配。 | 
| [PII 檢測技能（預覽）](cognitive-search-skill-pii-detection.md) | 索引期間使用的認知技能，從輸入文本中提取個人識別資訊，並允許您選擇以各種方式從該文本中遮罩這些資訊。| 
| [增量擴充（預覽）](cognitive-search-incremental-indexing-conceptual.md) | 將緩存添加到擴充管道中，如果目標修改（如對技能集或其他物件的更新）不更改內容，則可以重用現有輸出。 緩存僅適用于技能集生成的豐富文檔。| 
| [知識存放區 (預覽)](knowledge-store-concept-intro.md) | 基於 AI 的濃縮管道的新目的地。 物理資料結構存在於 Azure Blob 存儲和 Azure 表存儲中，並在運行具有附加認知技能集的索引子時創建和填充它。 知識存儲本身的定義在技能集定義中指定。 在知識存儲定義中，您可以通過確定資料形狀、資料是否存儲在表存儲或 Blob 存儲中以及是否有多個視圖*的投影*元素來控制資料的物理結構。| 

## <a name="indexing-and-query-features"></a>索引和查詢功能

索引子預覽功能在預覽搜索 API 中可用。 

|||
|-|-|
| [Cosmos DB 索引子](search-howto-index-cosmosdb.md) | 支援蒙戈DB API（預覽）、格雷姆林 API（預覽）和卡珊多拉 API（預覽）API 類型。 | 
|  [Azure 資料湖存儲第 2 代索引子（預覽版）](search-howto-index-azure-data-lake-storage.md) | 索引來自資料存儲庫第 2 代的內容和中繼資料。| 
| [更多類似于此查詢參數（預覽）](search-more-like-this.md) | 查找與特定文檔相關的文檔。 這個功能已存在舊版預覽中。 | 

## <a name="management-features"></a>管理功能

|||
|-|-|
| [專用終結點支援](service-create-private-endpoint.md) | 您可以使用安全用戶端（如虛擬機器）創建虛擬網路，然後創建使用專用終結點的搜索服務。 |
| IP 訪問限制 | 使用[`api-version=2019-10-01-Preview`](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview)管理 REST API，您可以創建一個服務，該服務對允許訪問哪些 IP 位址有限制。 |

## <a name="earlier-preview-features"></a>早期預覽功能

在早期預覽版中宣佈的功能（如果尚未轉換為通用版）仍處於公共預覽版中。 如果您使用舊版預覽 api-version 呼叫 API，則可以繼續使用該版本或切換到 `2019-05-06-Preview`，而不會更改預期的行為。

## <a name="how-to-call-a-preview-api"></a>如何呼叫預覽 API

較舊的預覽仍可運作，但是經過一段時間後會變成過時。 如果您的程式碼呼叫 `api-version=2016-09-01-Preview` 或 `api-version=2017-11-11-Preview`，那些呼叫都仍然有效。 但是，只有最新預覽版本會重新整理改進功能。 

下列的範例語法說明對預覽 API 版本的呼叫。

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Azure 認知搜索服務有多種版本。 如需詳細資訊，請參閱 [API 版本](search-api-versions.md)。

## <a name="next-steps"></a>後續步驟

查看搜索 REST API 參考文檔。 如果您遇到問題，請向我們請求[關於 StackOverflow 的説明](https://stackoverflow.com/)或[聯繫支援](https://azure.microsoft.com/support/community/?product=search)。

> [!div class="nextstepaction"]
> [搜索服務 REST API 參考](https://docs.microsoft.com/rest/api/searchservice/)