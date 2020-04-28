---
title: 將全文檢索搜尋新增至 Azure Blob 儲存體
titleSuffix: Azure Cognitive Search
description: 在 Azure 認知搜尋中建立全文檢索搜尋索引時，將內容解壓縮並將結構新增至 Azure blob。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: af7d04bd74ada296b9f0e0f7c149c2a781cec579
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "73496492"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-cognitive-search"></a>使用 Azure 認知搜尋將全文檢索搜尋新增至 Azure blob 資料

搜尋 Azure Blob 儲存體中儲存的各種內容類型可能是難以解決的問題。 不過，只要按幾下，就可以使用[Azure 認知搜尋](search-what-is-azure-search.md)來編制索引和搜尋 blob 的內容。 Azure 認知搜尋具有內建的整合，可讓您透過[*blob 索引子*](search-howto-indexing-azure-blob-storage.md)從 blob 儲存體編制索引，以新增資料來源感知功能來編制索引。

## <a name="what-it-means-to-add-full-text-search-to-blob-data"></a>將全文檢索搜尋新增至 blob 資料的意義

Azure 認知搜尋是一種雲端搜尋服務，可提供在您的搜尋服務上裝載的使用者定義索引上運作的索引和查詢引擎。 使用雲端中的查詢引擎共置您的可搜尋內容是效能的必要項，並以使用者從搜尋查詢預期的速度傳回結果。

Azure 認知搜尋會在索引層與 Azure Blob 儲存體整合，將您的 Blob 內容匯入為已編制索引為*反向索引*的搜尋檔，以及其他支援自由格式文字查詢和篩選運算式的查詢結構。 因為您的 blob 內容已編制索引為搜尋索引，所以對 blob 內容的存取可以利用 Azure 認知搜尋中的完整查詢功能範圍。

建立並填入索引之後，它會獨立存在於您的 blob 容器中，但您可以重新執行索引作業，以使用基礎容器的變更來更新索引。 個別 blob 的時間戳記資訊會用於變更偵測。 您可以選擇排定執行或依需求編制索引做為重新整理機制。

輸入是您在單一容器中的 blob，位於 Azure Blob 儲存體中。 Blob 幾乎可以是任何類型的文字資料。 如果您的 blob 包含影像，您可以將[AI 擴充新增至 blob 索引](search-blob-ai-integration.md)，以建立和解壓縮影像中的文字。

輸出一律是 Azure 認知搜尋索引，用於在用戶端應用程式中進行快速文字搜尋、抓取和探索。 Between 是索引管線架構本身。 管線是以*索引子*功能為基礎，會在本文中進一步討論。

## <a name="start-with-services"></a>開始使用服務

您需要「Azure 認知搜尋」和「Azure Blob 儲存體」。 在 Blob 儲存體中，您需要提供來源內容的容器。

您可以直接在儲存體帳戶入口網站頁面中啟動。 在左側導覽頁面的 [ **Blob 服務**] 底下，按一下 [**新增 Azure 認知搜尋**] 以建立新的服務，或選取現有的服務。 

將 Azure 認知搜尋新增至您的儲存體帳戶之後，您可以遵循標準程式來編制 blob 資料的索引。 我們建議您在 Azure 認知搜尋中使用 [匯**入資料**] wizard 來進行簡單的初始簡介，或使用 Postman 之類的工具來呼叫 REST api。 本教學課程會逐步引導您完成呼叫 Postman： Index 中的 REST API [，並在 Azure 認知搜尋中搜尋半結構化資料（JSON blob）](search-semi-structured-data.md)中的步驟。 

## <a name="use-a-blob-indexer"></a>使用 Blob 索引子

*索引子*是一種資料來源感知的子服務，具備用來取樣資料、讀取中繼資料、抓取資料，以及將資料從原生格式序列化為 JSON 檔以供後續匯入的內部邏輯。 

Azure 儲存體中的 blob 會使用[Azure 認知搜尋 Blob 儲存體索引子](search-howto-indexing-azure-blob-storage.md)來編制索引。 您可以使用 [匯**入資料**]、[REST API] 或 [.net SDK] 來叫用此索引子。 在程式碼中，您可以藉由設定型別，以及提供包含 Azure 儲存體帳戶和 blob 容器的連接資訊，來使用此索引子。 您可以藉由建立虛擬目錄來將 blob 子集化，然後將它當做參數傳遞，或藉由篩選檔案類型副檔名來進行。

索引子會執行「檔破解」，開啟 blob 以檢查內容。 連接到資料來源之後，就是管線中的第一個步驟。 若是 blob 資料，則會偵測到 PDF、office 檔和其他內容類型。 使用文字解壓縮的檔破解是免費的。 如果您的 blob 包含影像內容，除非您[新增 AI 擴充](search-blob-ai-integration.md)，否則會忽略映射。 標準索引編制僅適用于文字內容。

Blob 索引子隨附設定參數，如果基礎資料提供足夠的資訊，則支援變更追蹤。 您可以在[Azure 認知搜尋 Blob 儲存體索引子](search-howto-indexing-azure-blob-storage.md)中深入瞭解核心功能。

### <a name="supported-content-types"></a>支援的內容類型

藉由在容器上執行 Blob 索引子，您可以使用單一查詢從下列內容類型中提取文字和中繼資料：

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

### <a name="indexing-blob-metadata"></a>索引 blob 中繼資料

可讓您輕鬆地透過任何內容類型的 blob 排序的常見案例，就是為每個 blob 的自訂中繼資料和系統屬性編制索引。 如此一來，所有 blob 的資訊都會編制索引，而不論檔案類型為何（儲存在搜尋服務的索引中）。 您可以使用新的索引，在所有 Blob 儲存體內容中繼續排序、篩選和 facet。

### <a name="indexing-json-blobs"></a>編制 JSON blob 的索引
索引子可以設定為在包含 JSON 的 blob 中，解壓縮找到的結構化內容。 索引子可以讀取 JSON blob，並將結構化的內容剖析成搜尋檔的適當欄位。 索引子也可以接受包含 JSON 物件陣列的 blob，並將每個元素對應至個別的搜尋檔。 您可以設定剖析模式，以影響索引子所建立之 JSON 物件的類型。

## <a name="search-blob-content-in-a-search-index"></a>搜尋搜尋索引中的 blob 內容 

索引的輸出是搜尋索引，用於在用戶端應用程式中使用自由文字和篩選查詢進行互動式探索。 對於內容的初始探索和驗證，建議您從入口網站中的[搜尋瀏覽器](search-explorer.md)開始，以檢查檔結構。 您可以在搜尋瀏覽器中使用[簡單的查詢語法](query-simple-syntax.md)、[完整查詢語法](query-lucene-syntax.md)和[篩選運算式語法](query-odata-filter-orderby-syntax.md)。

更永久的解決方案是收集查詢輸入，並在用戶端應用程式中將回應呈現為搜尋結果。 下列 c # 教學課程說明如何建立搜尋應用程式：[在 Azure 認知搜尋中建立您的第一個應用程式](tutorial-csharp-create-first-app.md)。

## <a name="next-steps"></a>後續步驟

+ [使用 Azure 入口網站（Azure Blob 儲存體）上傳、下載及列出 blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
+ [設定 blob 索引子（Azure 認知搜尋）](search-howto-indexing-azure-blob-storage.md) 
