---
title: JavaScript 範例
titleSuffix: Azure Cognitive Search
description: 尋找使用適用于 JavaScript 的 Azure .NET SDK 的 Azure 認知搜尋示範 JavaScript 程式碼範例。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: f83767813ea3923d85db2ca3f0164776c610525e
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955015"
---
# <a name="javascript-code-samples-for-azure-cognitive-search"></a>Azure 認知搜尋的 JavaScript 程式碼範例

深入瞭解 JavaScript 程式碼範例，以示範 Azure 認知搜尋解決方案的功能和工作流程。 這些範例會使用適用于 [**AZURE SDK For JavaScript**](/azure/developer/javascript/)的 [**Azure 認知搜尋用戶端程式庫**](/javascript/api/overview/azure/search-documents-readme)，您可以透過下列連結流覽。

| 目標 | 連結 |
|--------|------|
| 套件下載 | [www.npmjs.com/package/@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents) |
| API 參考資料 | [@azure/search-documents](/javascript/api/@azure/search-documents/)  |
| API 測試案例 | [github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test) |
| 原始程式碼 | [github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents)  |

## <a name="sdk-samples"></a>SDK 範例

來自 Azure SDK 開發小組的程式碼範例會示範 API 的使用方式。 您可以在 [**azure sdk for js/tree/master/sdk/搜尋/搜尋-檔/範例**](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples) 中的 GitHub 上找到這些範例。

### <a name="javascript-sdk-samples"></a>JavaScript SDK 範例

| 範例 | 描述 |
|---------|-------------|
| [指標](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexes) | 示範如何建立、更新、取得、列出和刪除 [搜尋索引](search-what-is-an-index.md)。 此範例類別也包含服務統計資料範例。 |
| [索引子的 dataSourceConnections () ](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/dataSourceConnections) | 示範如何建立、更新、取得、列出和刪除 [支援的 Azure 資料來源](search-indexer-overview.md#supported-data-sources)之索引子型索引所需的索引子資料來源。 |
| [索引](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |  示範如何建立、更新、取得、列出、重設及刪除 [索引子](search-indexer-overview.md)。|
| [技能](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/skillSets) |   示範如何建立、更新、取得、列出和刪除附加索引子的 [技能集](cognitive-search-working-with-skillsets.md) ，以及在編制索引期間執行以 AI 為基礎的擴充。 |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/synonymMaps) | 示範如何建立、更新、取得、列出和刪除 [同義字對應](search-synonyms.md)。  |
| [查詢](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/readonlyQuery.js) | 示範針對 Microsoft 所裝載的唯讀公用索引執行查詢。  |

### <a name="typescript-samples"></a>TypeScript 範例

| 範例 | 描述 |
|---------|-------------|
| [指標](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexes) | 示範如何建立、更新、取得、列出和刪除 [搜尋索引](search-what-is-an-index.md)。 此範例類別也包含服務統計資料範例。 |
| [索引子的 dataSourceConnections () ](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/dataSourceConnections) | 示範如何建立、更新、取得、列出和刪除 [支援的 Azure 資料來源](search-indexer-overview.md#supported-data-sources)之索引子型索引所需的索引子資料來源。 |
| [索引](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexers) |  示範如何建立、更新、取得、列出、重設及刪除 [索引子](search-indexer-overview.md)。|
| [技能](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/skillSets) |   示範如何建立、更新、取得、列出和刪除附加索引子的 [技能集](cognitive-search-working-with-skillsets.md) ，以及在編制索引期間執行以 AI 為基礎的擴充。 |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/synonymMaps) | 示範如何建立、更新、取得、列出和刪除 [同義字對應](search-synonyms.md)。  |
| [查詢](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/typescript/src/readonlyQuery.ts) | 示範針對 Microsoft 所裝載的唯讀公用索引執行查詢。  |

## <a name="doc-samples"></a>Doc 範例

來自認知搜尋團隊的程式碼範例會示範功能和工作流程。 教學課程、快速入門和操作說明文章中都會參考許多這些範例。 您可以在 Azure 中找到這些範例 [**-範例/azure-搜尋-javascript-**](https://github.com/Azure-Samples/azure-search-javascript-samples) 在 GitHub 上的範例。

| 範例 | 發行項 |
|---------|---------|
| [快速入門](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) | [快速入門的原始程式碼：以 JavaScript 建立搜尋索引](search-get-started-javascript.md)。 本文涵蓋使用範例資料建立、載入和查詢搜尋索引的基本工作流程。 |

> [!Tip]
> 請嘗試 [範例瀏覽器](/samples/browse/?languages=javascript&products=azure-cognitive-search) ，以搜尋 Github 中的 Microsoft 程式碼範例，並依產品、服務和語言進行篩選。

## <a name="other-samples"></a>其他範例

下列範例也會由認知搜尋小組發佈，但不會在檔中參考。 相關聯的讀我檔案會提供使用指示。

| 範例 | 描述 |
|---------|-------------|
| [azure-搜尋-回應-範本](https://github.com/dereklegenzoff/azure-search-react-template) | Azure 認知搜尋 (github.com 的回應範本)  |