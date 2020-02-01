---
title: REST API 版本 2019-05-06-Preview
titleSuffix: Azure Cognitive Search
description: Azure 認知搜尋服務 REST API 版本 2019-05-06-Preview 包含實驗性功能，例如知識存放區和增量擴充的索引子快取。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 71c6879f467823ab01f4c60ac4d9f26cffcd4eea
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76896114"
---
# <a name="azure-cognitive-search-service-rest-api-version-2019-05-06-preview"></a>Azure 認知搜尋服務 REST api-版本 2019-05-06-預覽

本文說明搜尋服務 REST API 的 `api-version=2019-05-06-Preview` 版本，並提供尚未正式推出的實驗性功能。

> [!NOTE]
> 預覽功能適用於目標為收集意見反應的測試和實驗，而且可能會有所變更。 我們強烈建議您避免在實際執行的應用程式中使用預覽 API。

## <a name="features-in-2019-05-06-preview"></a>2019-05-06-Preview 中的功能

此區段會列出具有預覽狀態的功能。 大部分都是在目前的 2019-05-06-Preview API 中新增的，但有些類似的 `moreLikeThis` 是從已推出至最新預覽 API 的舊版預覽版本進行。 

預覽功能正式推出後，就會從這份清單中移除。 您可以查看[服務更新](https://azure.microsoft.com/updates/?product=search)或有關正式運作之公告的[新功能](whats-new.md)。

+ 累加[擴充（預覽）](cognitive-search-incremental-indexing-conceptual.md)會將快取新增至擴充管線，讓您在目標修改（例如，對技能集或另一個物件的更新）不會變更內容時，重複使用現有的輸出。 快取只適用于技能集所產生的擴充檔。

+ [Cosmos DB 索引子](search-howto-index-cosmosdb.md)支援 MongoDB api （預覽）、Gremlin API （預覽）和 Cassandra API （預覽）。

+ [Azure Data Lake Storage Gen2 索引子（預覽）](search-howto-index-azure-data-lake-storage.md)可以從 Data Lake Storage Gen2 編制內容和中繼資料的索引。

+ [知識存放區](knowledge-store-concept-intro.md)是以 AI 為基礎的擴充管線的新目的地。 實體資料結構存在於 Azure Blob 儲存體和 Azure 資料表儲存體中，而且當您執行具有附加認知技能集的索引子時，它會建立並填入。 知識存放區本身的定義是在技能集定義內指定。 在知識存放區定義中，您可以透過*投射*專案來控制資料的實體結構，這些元素會決定如何塑造資料、資料是否儲存在資料表儲存體或 Blob 儲存體中，以及是否有多個視圖。

+ [moreLikeThis 查詢參數](search-more-like-this.md)，可尋找與特定文件相關的文件。 這個功能已存在舊版預覽中。 

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