---
title: REST 範例
titleSuffix: Azure Cognitive Search
description: 尋找使用搜尋或管理 REST Api 的 Azure 認知搜尋示範 REST 程式碼範例。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: a7ab48759ac775c1195dedb4143d895bdcdec937
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956469"
---
# <a name="rest-code-samples-for-azure-cognitive-search"></a>Azure 認知搜尋的 REST 程式碼範例

瞭解 REST API 範例，以示範 Azure 認知搜尋解決方案的功能和工作流程。 這些範例會使用 [**搜尋 REST api**](/rest/api/searchservice)。

REST 是 Azure 認知搜尋的最終程式設計介面，而且所有可透過程式設計方式叫用的作業，都可先在 REST 中使用，然後在 Sdk 中使用。 基於這個理由，檔中的大部分範例都會利用 REST Api 來示範或說明重要的概念。

REST 範例通常會在 Postman 上開發和測試，但您可以使用任何支援 HTTP 呼叫的用戶端：

+ 開始使用 [快速入門：使用 REST Api 建立 Azure 認知搜尋索引](search-get-started-rest.md) ，以協助進行 HTTP 呼叫的編寫。
+ 如果您在 Visual Studio Code 中工作，請嘗試 [Azure 認知搜尋的 Visual Studio Code 擴充](search-get-started-vs-code.md)功能（目前為預覽狀態）。

## <a name="doc-samples"></a>Doc 範例

來自認知搜尋團隊的程式碼範例會示範功能和工作流程。 教學課程、快速入門和操作說明文章中都會參考許多這些範例。 您可以在 Azure 中找到這些範例 [**-範例/azure-搜尋-postman-**](https://github.com/Azure-Samples/azure-search-postman-samples) 在 GitHub 上的範例。

| 範例 | 發行項 |
|---------|---------|
| [快速入門](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Quickstart) | [快速入門的原始程式碼：使用 REST Api 建立搜尋索引](search-get-started-rest.md)。 本文涵蓋使用範例資料建立、載入和查詢搜尋索引的基本工作流程。 |
| [教學課程](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Tutorial) | 教學課程的原始程式碼 [：使用 REST 和 AI 從 Azure blob 產生可搜尋的內容](cognitive-search-tutorial-blob.md)。 本文說明如何建立可逐一查看 Azure blob 以解壓縮資訊和推斷結構的技能集。|
| [Debug-會話](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions) | 教學課程的原始程式碼 [：診斷、修復和認可您對技能集的變更](cognitive-search-tutorial-debug-sessions.md)。 本文說明如何在 Azure 入口網站中使用技能集的調試會話。 REST 可用來建立在 debug 期間使用的物件。|
| [自訂-分析器](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/custom-analyzers) | 教學課程的原始程式碼 [：建立電話號碼的自訂分析器](tutorial-create-custom-analyzer.md)。 本文說明如何流量分析器來保留可搜尋內容中的模式和特殊字元。|
| [知識存放區](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store) | [使用 REST 和 Postman 建立知識存放區](knowledge-store-create-rest.md)的原始程式碼。 本文說明填入用於知識挖掘工作流程之知識存放區的必要步驟。 |
| [預測](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/projections) | [如何塑造和匯出擴充](knowledge-store-projections-examples.md)的原始程式碼。 本文說明如何在知識存放區中指定實體資料結構。|
| [索引-已加密-blob](https://github.com/Azure-Samples/azure-search-postman-samples/commit/f5ebb141f1ff98f571ab84ac59dcd6fd06a46718) | [如何使用 blob 索引子和技能集為已加密的 blob 編制索引](search-howto-index-encrypted-blobs.md)的原始程式碼。 本文說明如何在先前使用 Azure Key Vault 加密的 Azure Blob 儲存體中為檔編制索引。 |

> [!Tip]
> 請嘗試 [範例瀏覽器](/samples/browse/?expanded=azure&languages=http&products=azure-cognitive-search) ，以搜尋 Github 中的 Microsoft 程式碼範例，並依產品、服務和語言進行篩選。

## <a name="other-samples"></a>其他範例

下列範例也會由認知搜尋小組發佈，但不會在檔中參考。 相關聯的讀我檔案會提供使用指示。

| 範例 | 描述 |
|---------|-------------|
| [查詢-範例](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Query-examples) | Postman 集合示範各種查詢技術，包括模糊搜尋、RegEx 和萬用字元搜尋、自動完成等等。 |