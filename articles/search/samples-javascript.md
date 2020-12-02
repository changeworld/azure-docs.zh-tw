---
title: JavaScript 範例
titleSuffix: Azure Cognitive Search
description: 尋找使用適用于 JavaScript 的 Azure .NET SDK 的 Azure 認知搜尋示範 JavaScript 程式碼範例。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 234c70fffb6f353c670d23624cc446fdaf6bd886
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498960"
---
# <a name="javascript-code-samples-for-azure-cognitive-search"></a>Azure 認知搜尋的 JavaScript 程式碼範例

深入瞭解 JavaScript 程式碼範例，以示範 Azure 認知搜尋的特性和功能。 主要存放庫如下所示：

| Repository | 描述 |
|------------|-------------|
| [azure-sdk-適用于 js/樹狀結構/master/sdk/搜尋/搜尋-檔](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents) | Azure SDK 團隊所產生的範例，隨附于 SDK 中的 Azure.Search.Documents 用戶端程式庫。 您也可以檢查用戶端程式庫的 [單元測試](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test) ，以查看如何呼叫不同的 api。 |
| [Azure-範例/azure-搜尋-javascript-範例](https://github.com/Azure-Samples/azure-search-javascript-samples) | 伴隨 how to 文章的程式碼範例，包括 [快速入門：在 JavaScript 中建立搜尋索引](search-get-started-javascript.md)。|

> [!Tip]
> 請嘗試 [範例瀏覽器](/samples/browse/?languages=csharp&products=azure-cognitive-search) ，以搜尋 Github 中的 Microsoft 程式碼範例，並依產品、服務和語言進行篩選。

## <a name="javascript-sdk-samples"></a>JavaScript SDK 範例

Azure SDK for JAVA 包含許多範例和快速入門 [頁面](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/README.md#getting-started) ，其中涵蓋了套件安裝、用戶端設定和疑難排解。 此頁面也會描述下列範例類別，以方便您參考。

| 範例 | 描述 |
|---------|-------------|
| [指標](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexes) | 示範如何建立、更新、取得、列出和刪除 [搜尋索引](search-what-is-an-index.md)。 此範例類別也包含服務統計資料範例。 |
| [索引子的 dataSourceConnections () ](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/dataSourceConnections) | 示範如何建立、更新、取得、列出和刪除 [支援的 Azure 資料來源](search-indexer-overview.md#supported-data-sources)之索引子型索引所需的索引子資料來源。 |
| [索引](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |  示範如何建立、更新、取得、列出、重設及刪除 [索引子](search-indexer-overview.md)。|
| [技能](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/skillSets) |   示範如何建立、更新、取得、列出和刪除附加索引子的 [技能集](cognitive-search-working-with-skillsets.md) ，以及在編制索引期間執行以 AI 為基礎的擴充。 |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/synonymMaps) | 示範如何建立、更新、取得、列出和刪除 [同義字對應](search-synonyms.md)。  |
| [查詢](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/readonlyQuery.js) | 示範針對 Microsoft 所裝載的唯讀公用索引執行查詢。  |

## <a name="typescript-samples"></a>TypeScript 範例

SDK 也會提供 TypeScript 範例，在這裡列出以方便您參考。

| 範例 | 描述 |
|---------|-------------|
| [指標](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexes) | 示範如何建立、更新、取得、列出和刪除 [搜尋索引](search-what-is-an-index.md)。 此範例類別也包含服務統計資料範例。 |
| [索引子的 dataSourceConnections () ](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/dataSourceConnections) | 示範如何建立、更新、取得、列出和刪除 [支援的 Azure 資料來源](search-indexer-overview.md#supported-data-sources)之索引子型索引所需的索引子資料來源。 |
| [索引](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexers) |  示範如何建立、更新、取得、列出、重設及刪除 [索引子](search-indexer-overview.md)。|
| [技能](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/skillSets) |   示範如何建立、更新、取得、列出和刪除附加索引子的 [技能集](cognitive-search-working-with-skillsets.md) ，以及在編制索引期間執行以 AI 為基礎的擴充。 |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/synonymMaps) | 示範如何建立、更新、取得、列出和刪除 [同義字對應](search-synonyms.md)。  |
| [查詢](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/typescript/src/readonlyQuery.ts) | 示範針對 Microsoft 所裝載的唯讀公用索引執行查詢。  |

## <a name="documentation-samples"></a>文件和範例

下列範例有 [Azure 認知搜尋檔](./index.yml)中的相關文章。

| 範例 | 描述 | 
|---------|-------------|
| [快速入門](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) | [快速入門的原始程式碼：以 JavaScript 建立搜尋索引](search-get-started-javascript.md)。  |

## <a name="standalone-samples"></a>獨立範例

| 範例 | 描述 |
|---------|-------------|
| [azure-搜尋-回應-範本](https://github.com/dereklegenzoff/azure-search-react-template) | Azure 認知搜尋 (github.com 的回應範本)  |