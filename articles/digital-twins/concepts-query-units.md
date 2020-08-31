---
title: Azure 數位 Twins 中的查詢單位
titleSuffix: Azure Digital Twins
description: 瞭解 Azure 數位 Twins 中查詢單位的計費概念
author: baanders
ms.author: baanders
ms.date: 8/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: c68cb8cc0ecf759b9af0e313e09663cdbc327917
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89067700"
---
# <a name="query-units-in-azure-digital-twins"></a>Azure 數位 Twins 中的查詢單位 

Azure 數位 Twins**查詢單位 (QU) **是一種隨選計算單位，可用來執行使用[查詢 API](https://docs.microsoft.com/rest/api/digital-twins/dataplane/query)的[Azure 數位 Twins 查詢](how-to-query-graph.md)。 

它會將執行 Azure 數位 Twins 所支援的查詢作業所需的系統資源（例如 CPU、IOPS 和記憶體）抽象化出來，讓您可以改為追蹤查詢單位的使用量。

執行查詢所耗用的查詢單位數量受 .。。
* 查詢的複雜度 
* 結果集的大小 (因此，傳回10個結果的查詢所耗用的 QUs，會比只傳回一個結果的類似複雜性查詢更多的情況) 

本文說明如何瞭解查詢單位和追蹤查詢單位耗用量。

## <a name="find-the-query-unit-consumption-in-azure-digital-twins"></a>在 Azure 數位 Twins 中尋找查詢單位耗用量 

當您使用 Azure 數位 Twins [查詢 API](https://docs.microsoft.com/rest/api/digital-twins/dataplane/query)執行查詢時，您可以檢查回應標頭來追蹤查詢所耗用的 QUs 數目。 在從 Azure 數位 Twins 送回的回應中尋找「查詢費用」。 

Azure 數位 Twins [sdk](how-to-use-apis-sdks.md) 可讓您從可分頁回應中解壓縮查詢費用標頭。 本節說明如何查詢數位 twins，以及如何反復查看可分頁回應以解壓縮查詢費用標頭。 

下列程式碼片段會示範如何在呼叫查詢 API 時，解壓縮所產生的查詢費用。 它會先逐一查看回應頁面以存取查詢費用標頭，然後逐一查看每個頁面內的數位對應項結果。 
 
```csharp
AsyncPageable<string> asyncPageableResponseWithCharge = client.QueryAsync("SELECT * FROM digitaltwins"); 
int pageNum = 0; 

// The "await" keyword here is required, as a call is made when fetching a new page. 

await foreach (Page<string> page in asyncPageableResponseWithCharge.AsPages()) 
{ 
    Console.WriteLine($"Page {++pageNum} results:"); 

    // Extract the query-charge header from the page 

    if (QueryChargeHelper.TryGetQueryCharge(page, out float queryCharge)) 
    { 
        Console.WriteLine($"Query charge was: {queryCharge}"); 
    } 

    // Iterate over the twin instances. 

    // The "await" keyword is not required here, as the paged response is local. 

    foreach (string response in page.Values) 
    { 
        BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(response); 
        Console.WriteLine($"Found digital twin '{twin.Id}'"); 
    } 
} 
```

## <a name="next-steps"></a>後續步驟

若要深入瞭解如何查詢 Azure 數位 Twins，請造訪：
* [*概念：查詢語言*](concepts-query-language.md)
* [*How to：查詢對應項圖形*](how-to-query-graph.md)
* [查詢 API 參考檔](https://docs.microsoft.com/rest/api/digital-twins/dataplane/query/querytwins)

您可以在下列參考中找到 Azure 數位 Twins 查詢相關限制 [*：公開預覽中的服務限制*](reference-service-limits.md)。