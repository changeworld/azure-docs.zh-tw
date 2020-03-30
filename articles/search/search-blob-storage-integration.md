---
title: 將全文檢索搜尋添加到 Azure Blob 存儲
titleSuffix: Azure Cognitive Search
description: 在 Azure 認知 earch 中構建全文檢索搜尋索引時，提取內容並將結構添加到 Azure blob。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: af7d04bd74ada296b9f0e0f7c149c2a781cec579
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73496492"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-cognitive-search"></a>使用 Azure 認知搜索將全文檢索搜尋添加到 Azure Blob 資料

搜尋 Azure Blob 儲存體中儲存的各種內容類型可能是難以解決的問題。 但是，您可以使用[Azure 認知搜索](search-what-is-azure-search.md)，只需按一下幾下即可對 Blob 的內容進行索引和搜索。 Azure 認知搜索具有內置集成，用於通過[*Blob 索引子*](search-howto-indexing-azure-blob-storage.md)從 Blob 存儲中索引，該索引子將資料來源感知功能添加到索引中。

## <a name="what-it-means-to-add-full-text-search-to-blob-data"></a>向 Blob 資料添加全文檢索搜尋的含義

Azure 認知搜索是一種雲搜索服務，提供索引和查詢引擎，這些引擎在搜索服務上託管的使用者定義的索引上運行。 性能需要將可搜索內容與雲中的查詢引擎共同定位，以便以使用者期望從搜索查詢返回結果的速度返回。

Azure 認知搜索與索引層中的 Azure Blob 存儲集成，將 Blob 內容導入為搜索文檔，這些文檔被索引為*倒置索引*和支援自由形式文字查詢和篩選器運算式的其他查詢結構。 由於 Blob 內容已編制到搜索索引中，因此訪問 Blob 內容可以利用 Azure 認知搜索中的全部查詢功能。

創建和填充索引後，它獨立于 blob 容器存在，但您可以重新運行索引操作，以便通過對基礎容器的更改刷新索引。 各個 Blob 上的時間戳記資訊用於更改檢測。 您可以選擇計畫執行或按需索引作為刷新機制。

輸入是在 Azure Blob 存儲中的單個容器中的 Blob。 Blob 幾乎可以是任何類型的文本資料。 如果 Blob 包含圖像，則可以向[Blob 索引添加 AI 擴充](search-blob-ai-integration.md)以從圖像創建和提取文本。

輸出始終是 Azure 認知搜索索引，用於用戶端應用程式中的快速文本搜索、檢索和流覽。 中間是索引管道體系結構本身。 管道基於*索引子*功能，本文對此進行了進一步討論。

## <a name="start-with-services"></a>從服務開始

您需要 Azure 認知搜索和 Azure Blob 存儲。 在 Blob 存儲中，您需要一個提供源內容的容器。

您可以直接在存儲帳戶門戶頁中開始。 在左側導航頁中，按一下 **"** 添加**Azure 認知搜索"** 以創建新服務或選擇現有服務。 

將 Azure 認知搜索添加到存儲帳戶後，可以按照標準過程對 Blob 資料進行索引。 我們建議在 Azure 認知搜索中**導入資料**嚮導以進行簡單的初始介紹，或使用 Postman 等工具調用 REST API。 本教程將引導您完成在 Postman 中調用 REST API 的步驟：[在 Azure 認知搜索中索引和搜索半結構化資料 （JSON blob）。](search-semi-structured-data.md) 

## <a name="use-a-blob-indexer"></a>使用 Blob 索引子

*索引子*是資料來源感知子服務，具有用於採樣資料、讀取中繼資料資料、檢索資料和將本地格式的資料序列化到 JSON 文檔中以便後續導入的內部邏輯。 

Azure 存儲中的 Blob 使用[Azure 認知搜索 Blob 存儲索引子](search-howto-indexing-azure-blob-storage.md)進行索引。 您可以使用**導入資料**嚮導、REST API 或 .NET SDK 調用此索引子。 在代碼中，可以通過設置類型和提供包含 Azure 存儲帳戶和 Blob 容器的連接資訊來使用此索引子。 您可以通過創建虛擬目錄（然後作為參數傳遞）或篩選檔案類型副檔名來對 Blob 進行子集。

索引子執行"文檔破解"，打開 blob 以檢查內容。 連接到資料來源後，它是管道中的第一步。 對於 Blob 資料，這是檢測 PDF、辦公室文檔和其他內容類型的位置。 使用文本提取進行文檔破解不收取任何費用。 如果 blob 包含圖像內容，則除非[添加 AI 擴充](search-blob-ai-integration.md)，否則圖像將被忽略。 標準索引僅適用于文本內容。

Blob 索引子附帶配置參數，如果基礎資料提供足夠的資訊，則支援更改跟蹤。 您可以在[Azure 認知搜索 Blob 存儲索引子](search-howto-indexing-azure-blob-storage.md)中瞭解有關核心功能的更多資訊。

### <a name="supported-content-types"></a>支援的內容類型

通過在容器上運行 Blob 索引子，可以使用單個查詢從以下內容類型中提取文本和中繼資料：

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

### <a name="indexing-blob-metadata"></a>索引 Blob 中繼資料

使任何內容類型的 Blob 易於排序的常見方案是為每個 Blob 的自訂中繼資料和系統屬性編制索引。 這樣，所有 Blob 的資訊都會編制索引，而不考慮文件類型，這些資訊存儲在搜索服務中的索引中。 然後，使用新索引，可以繼續對所有 Blob 存儲內容進行排序、篩選和分面。

### <a name="indexing-json-blobs"></a>索引 JSON blob
索引子可以配置為提取包含 JSON 的 blob 中的結構化內容。 索引子可以讀取 JSON blob 並將結構化內容解析為搜索文檔的相應欄位。 索引子還可以獲取包含 JSON 物件陣列的 Blob，並將每個元素映射到單獨的搜索文檔。 您可以設置分析模式以影響索引子創建的 JSON 物件的類型。

## <a name="search-blob-content-in-a-search-index"></a>搜索搜索索引中的 Blob 內容 

索引的輸出是一個搜索索引，用於使用免費文本和用戶端應用中篩選的查詢進行互動式流覽。 對於內容的初始探索和驗證，我們建議從門戶中的[搜索資源管理器](search-explorer.md)開始檢查文檔結構。 您可以在搜索資源管理器中使用[簡單的查詢語法](query-simple-syntax.md)、[完整查詢語法](query-lucene-syntax.md)和[篩選器運算式語法](query-odata-filter-orderby-syntax.md)。

更永久的解決方案是收集查詢輸入，並將回應作為搜尋結果呈現在用戶端應用程式中。 以下 C# 教程介紹了如何構建搜索應用程式：[在 Azure 認知搜索中創建第一個應用程式](tutorial-csharp-create-first-app.md)。

## <a name="next-steps"></a>後續步驟

+ [使用 Azure 門戶（Azure Blob 存儲）上載、下載和列出 Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
+ [設置 Blob 索引子（Azure 認知搜索）](search-howto-indexing-azure-blob-storage.md) 
