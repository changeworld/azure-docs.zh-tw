---
title: REST API 中的預覽功能
titleSuffix: Azure Cognitive Search
description: Azure 認知搜尋服務 REST API 2019-05-06 版-Preview 包含實驗性功能，例如知識存放區和增量擴充的索引子快取。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: db941152186127302680b5e659e43cd2d82a8908
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77162271"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Azure 認知搜尋中的預覽功能

本文列出目前處於預覽狀態的功能。 從預覽轉換為正式運作的功能會從此清單中移除。 您可以查看[服務更新](https://azure.microsoft.com/updates/?product=search)或有關正式運作之公告的[新功能](whats-new.md)。

雖然某些預覽功能可能會在入口網站和 .NET SDK 中提供，但 REST API 一律會有預覽功能。

+ 針對搜尋作業， [**`2019-05-06-Preview`**](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview)是目前的預覽版本。
+ 針對管理作業， [**`2019-10-01-Preview`**](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview)是目前的預覽版本。

> [!IMPORTANT]
> 預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="ai-enrichment-features"></a>AI 擴充功能

透過[預覽搜尋 API](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview)，探索 AI 擴充的最新增強功能。

|||
|-|-|
| [自訂實體查閱技能（預覽）](cognitive-search-skill-custom-entity-lookup.md ) | 一種認知技能，可從自訂、使用者定義的單字和片語清單中尋找文字。 使用這份清單，它會以任何相符的實體標記所有檔。 此技能也支援某種程度的模糊比對，可套用來尋找類似但不完全精確的相符專案。 | 
| [PII 偵測技能（預覽）](cognitive-search-skill-pii-detection.md) | 在編制索引期間使用的認知技能，可從輸入文字中解壓縮個人識別資訊，並可讓您選擇以各種方式從該文字遮罩它。| 
| [增量擴充（預覽）](cognitive-search-incremental-indexing-conceptual.md) | 將快取新增至擴充管線，讓您在目標修改（例如技能集或另一個物件的更新）不會變更內容時重複使用現有的輸出。 快取只適用于技能集所產生的擴充檔。| 
| [知識存放區 (預覽)](knowledge-store-concept-intro.md) | 以 AI 為基礎之擴充管線的新目的地。 實體資料結構存在於 Azure Blob 儲存體和 Azure 資料表儲存體中，而且當您執行具有附加認知技能集的索引子時，它會建立並填入。 知識存放區本身的定義是在技能集定義內指定。 在知識存放區定義中，您可以透過*投射*專案來控制資料的實體結構，這些元素會決定如何塑造資料、資料是否儲存在資料表儲存體或 Blob 儲存體中，以及是否有多個視圖。| 

## <a name="indexing-and-query-features"></a>編制索引和查詢功能

預覽搜尋 API 中提供索引子預覽功能。 

|||
|-|-|
| [Cosmos DB 索引子](search-howto-index-cosmosdb.md) | 支援 MongoDB API （預覽）、Gremlin API （預覽）和 Cassandra API （預覽） API 類型。 | 
|  [Azure Data Lake Storage Gen2 索引子（預覽）](search-howto-index-azure-data-lake-storage.md) | 從 Data Lake Storage Gen2 編制內容和中繼資料的索引。| 
| [moreLikeThis 查詢參數（預覽）](search-more-like-this.md) | 尋找與特定檔相關的檔。 這個功能已存在舊版預覽中。 | 

## <a name="management-features"></a>管理功能

|||
|-|-|
| [私用端點支援](service-create-private-endpoint.md) | 您可以使用安全的用戶端（例如虛擬機器）來建立虛擬網路，然後建立使用私用端點的搜尋服務。 |
| IP 存取限制 | 使用[`api-version=2019-10-01-Preview`](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview)管理 REST API，您可以建立一個服務，其限制允許存取的 IP 位址。 |

## <a name="earlier-preview-features"></a>舊版預覽功能

在先前預覽中宣佈的功能（如果尚未轉換為正式運作）仍處於公開預覽狀態。 如果您使用舊版預覽 api-version 呼叫 API，則可以繼續使用該版本或切換到 `2019-05-06-Preview`，而不會更改預期的行為。

## <a name="how-to-call-a-preview-api"></a>如何呼叫預覽 API

較舊的預覽仍可運作，但是經過一段時間後會變成過時。 如果您的程式碼呼叫 `api-version=2016-09-01-Preview` 或 `api-version=2017-11-11-Preview`，那些呼叫都仍然有效。 但是，只有最新預覽版本會重新整理改進功能。 

下列的範例語法說明對預覽 API 版本的呼叫。

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Azure 認知搜尋服務提供多個版本。 如需詳細資訊，請參閱 [API 版本](search-api-versions.md)。

## <a name="next-steps"></a>後續步驟

請參閱搜尋 REST API 參考檔。 如果您遇到問題，請向我們尋求[StackOverflow](https://stackoverflow.com/)或[連絡人支援](https://azure.microsoft.com/support/community/?product=search)的協助。

> [!div class="nextstepaction"]
> [搜尋服務 REST API 參考](https://docs.microsoft.com/rest/api/searchservice/)