---
title: 將全文檢索搜尋新增至 Azure Blob 儲存體
titleSuffix: Azure Cognitive Search
description: 在 Azure 認知搜尋中建立全文檢索搜尋索引時，將內容解壓縮並將結構新增至 Azure Blob。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 72d00b70cf3568466715668aa441ee295614c740
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935240"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-cognitive-search"></a>使用 Azure 認知搜尋將全文檢索搜尋新增至 Azure Blob 資料

搜尋 Azure Blob 儲存體中儲存的各種內容類型可能是難以解決的問題。 不過，使用 [Azure 認知搜尋](search-what-is-azure-search.md)，您只要按幾下，即可編製索引並搜尋 Blob 內容。 Azure 認知搜尋具有內建的整合，可讓您透過 [Blob 索引子](search-howto-indexing-azure-blob-storage.md)，從 Blob 儲存體編製索引，將資料來源感知功能新增至索引編製。

## <a name="what-it-means-to-add-full-text-search-to-blob-data"></a>將全文檢索搜尋新增至 Blob 資料的意義

Azure 認知搜尋是一種雲端搜尋服務，可提供在您搜尋服務上裝載的使用者定義索引上運作的索引編製和查詢引擎。 使用雲端中的查詢引擎共置您的可搜尋內容是效能的必要項，並以使用者所預期搜尋查詢的速度傳回結果。

Azure 認知搜尋會在索引層與 Azure Blob 儲存體整合，將 Blob 內容匯入編製索引為「反向索引」的搜尋文件，以及其他支援自由格式文字查詢和篩選運算式的查詢結構。 因為您的 Blob 內容已編製索引為搜尋索引，所以對 Blob 內容的存取可以利用 Azure 認知搜尋中的完整查詢功能範圍。

建立並填入索引之後，其會獨立存在於您的 Blob 容器中，但您可以重新執行索引編製作業，以使用底層容器的變更來重新整理索引。 個別 Blob 的時間戳記資訊會用於變更偵測。 您可以選擇排程執行或隨選索引編製作為重新整理機制。

輸入是您在 Azure Blob 儲存體中的 Blob (在單一容器中)。 Blob 幾乎可以是任何類型的文字資料。 如果您的 Blob 包含影像，您可以將 [AI 擴充新增至 Blob 索引編製](search-blob-ai-integration.md)，以建立及擷取影像中的文字。

輸出一律是 Azure 認知搜尋索引，用於在用戶端應用程式中進行快速文字搜尋、擷取及探索。 介於兩者之間的是索引編製管線結構本身。 管線是以「索引子」功能為基礎，這會在此文章中進一步討論。

## <a name="start-with-services"></a>開始使用服務

您需要 Azure 認知搜尋和 Azure Blob 儲存體。 在 Blob 儲存體中，您需要提供來源內容的容器。

您可以在您的儲存體帳戶入口網站頁面中直接開始。 在左側導覽頁面的 [Blob 服務] 底下，按一下 [新增 Azure 認知搜尋] 以建立新服務，或選取現有服務。 

將 Azure 認知搜尋新增至您的儲存體帳戶之後，您可以遵循標準程序來編製 Blob 資料的索引。 我們建議 Azure 認知搜尋中的**匯入資料**精靈用於簡單初始簡介，或使用 Postman 之類的工具來呼叫 REST API。 此教學課程會逐步引導您完成在 Postman 中呼叫 REST API 的步驟：[在 Azure 認知搜尋中為半結構化資料 (JSON Blob) 編製索引及加以搜尋](search-semi-structured-data.md)。 

## <a name="use-a-blob-indexer"></a>使用 Blob 索引子

「索引子」是資料來源感知的子服務，其具備內部邏輯，可用來取樣資料、讀取中繼資料、擷取資料，以及將資料從原生格式序列化為 JSON 文件以供後續匯入。 

Azure 儲存體中的 Blob 是使用 [Azure 認知搜尋 Blob 儲存體索引子](search-howto-indexing-azure-blob-storage.md)來編製索引。 您可以使用**匯入資料**精靈、REST API 或 .NET SDK 來叫用此索引子。 在程式碼中，您可以藉由設定類型，以及提供包含 Azure 儲存體帳戶和 Blob 容器的連線資訊，來使用此索引子。 您可以藉由建立虛擬目錄來將 Blob 子集化，然後將其當作參數傳遞，或藉由篩選檔案類型副檔名來進行。

索引子會執行「文件破解」(開啟 Blob 以檢查內容)。 連線到資料來源之後，其就是管線中的第一個步驟。 若是 Blob 資料，則會在此偵測到 PDF、Office 文件和其他內容類型。 使用文字擷取的文件破解是免費的。 如果您的 Blob 包含影像內容，除非您[新增 AI 擴充](search-blob-ai-integration.md)，否則系統會忽略影像。 標準索引編製僅適用於文字內容。

Blob 索引子隨附設定參數，如果底層資料提供足夠的資訊，則支援變更追蹤。 您可以在 [Azure 認知搜尋 Blob 儲存體索引子](search-howto-indexing-azure-blob-storage.md)中深入了解核心功能。

### <a name="supported-content-types"></a>支援的內容類型

藉由在容器上執行 Blob 索引子，您可以使用單一查詢從下列內容類型中擷取文字和中繼資料：

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

### <a name="indexing-blob-metadata"></a>編製 Blob 中繼資料的索引

若要簡化任何內容類型的 Blob 排序，常見案例是為每個 Blob 的自訂中繼資料和系統屬性編製索引。 如此一來，不論是什麼文件類型，所有 Blob 的資訊都會編製成索引，儲存在搜尋服務的索引中。 使用您的新索引，接著便可以跨所有 Blob 儲存體的內容，進行排序、篩選、Facet。

> [!NOTE]
> Blob 索引標籤是由 Blob 儲存體服務以原生方式編製索引，而且會針對查詢而公開。 如果您的 Blob 機碼/值屬性需要索引編製和篩選功能，則應利用 Blob 索引標籤，而不是中繼資料。
>
> 若要深入了解 Blob 索引，請參閱[使用 Blob 索引來管理及尋找 Azure Blob 儲存體上的資料](../storage/blobs/storage-manage-find-blobs.md)。

### <a name="indexing-json-blobs"></a>編製 JSON Blob 的索引
可以將索引子設定為擷取在包含 JSON 的 Blob 中找到的結構化內容。 索引子可以讀取 JSON Blob，並將結構化內容剖析成搜尋文件的適當欄位。 索引子也會採用包含 JSON 物件陣列的 Blob，並將每個元素對應至個別的搜尋文件。 您可以設定剖析模式，以影響索引子所建立 JSON 物件的類型。

## <a name="search-blob-content-in-a-search-index"></a>搜尋搜尋索引中的 Blob 內容 

索引編製的輸出是搜尋索引，用於在用戶端應用程式中使用任意文字和篩選查詢進行互動式探索。 若要進行內容的初始探索和驗證，建議您從入口網站中的[搜尋總管](search-explorer.md)開始檢查文件結構。 您可以在搜尋總管中使用[簡單查詢語法](query-simple-syntax.md)、[完整查詢語法](query-lucene-syntax.md)和[篩選運算式語法](query-odata-filter-orderby-syntax.md)。

更永久的解決方案是收集查詢輸入，並在用戶端應用程式中將回應呈現為搜尋結果。 下列 C# 教學課程說明如何建立搜尋應用程式：[在 Azure 認知搜尋中建立您的第一個應用程式](tutorial-csharp-create-first-app.md)。

## <a name="next-steps"></a>後續步驟

+ [使用 Azure 入口網站上傳、下載及列出 Blob (Azure Blob 儲存體)](../storage/blobs/storage-quickstart-blobs-portal.md)
+ [設定 Blob 索引子 (Azure 認知搜尋)](search-howto-indexing-azure-blob-storage.md)