---
title: Java 範例
titleSuffix: Azure Cognitive Search
description: 尋找使用適用于 JAVA 的 Azure .NET SDK 的 Azure 認知搜尋示範 JAVA 程式碼範例。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: b5ae38a3dc4a9324a4141314106d67c96c06c8e6
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955032"
---
# <a name="java-code-samples-for-azure-cognitive-search"></a>Azure 認知搜尋的 JAVA 程式碼範例

深入瞭解 JAVA 程式碼範例，以示範 Azure 認知搜尋解決方案的功能和工作流程。 這些範例會使用適用于 [**AZURE SDK For JAVA**](/azure/developer/java/sdk)的 [**Azure 認知搜尋用戶端程式庫**](/java/api/overview/azure/search-documents-readme)，您可以透過下列連結來探索。

| 目標 | 連結 |
|--------|------|
| 套件下載 | [search.maven.org/artifact/com.azure/azure-search-documents](https://search.maven.org/artifact/com.azure/azure-search-documents) |
| API 參考資料 | [com.azure.search.documents](/java/api/com.azure.search.documents)  |
| API 測試案例 | [github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/test](https://github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/test) |
| 原始程式碼 | [github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src](https://github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src)  |

## <a name="sdk-samples"></a>SDK 範例

來自 Azure SDK 開發小組的程式碼範例會示範 API 的使用方式。 您可以在 [**azure/azure sdk （適用于 java/tree/master/sdk/search/Azure-搜尋-檔/src/範例**](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) ）中找到這些範例。

| 範例 | 描述 |
|---------|-------------|
| [建立搜尋索引](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) | 示範如何建立 [搜尋索引](search-what-is-an-index.md)。 |
| [建立同義字](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SynonymMapsCreateExample.java) | 示範如何建立 [同義字對應](search-synonyms.md)。  |
| [搜尋索引子建立](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) | 示範如何建立 [索引子](search-indexer-overview.md)。 |
| [搜尋索引子資料來源建立](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/DataSourceExample.java) | 示範如何建立索引子資料來源，這些是 [支援的 Azure 資料來源](search-indexer-overview.md#supported-data-sources)索引子索引所需的索引子。 |
| [技能集建立](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateSkillsetExample.java) |  示範如何建立附加索引子的 [技能集](cognitive-search-working-with-skillsets.md) ，以及在編制索引期間執行以 AI 為基礎的擴充。 |
| [載入文件](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/IndexContentManagementExample.java) | 示範如何在 [資料匯入](search-what-is-data-import.md) 作業中，將檔上傳或合併到索引中。 |
| [查詢語法](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchAsyncWithFullyTypedDocumentsExample.java) | 示範如何設定 [基本查詢](search-query-overview.md)。 |

## <a name="doc-samples"></a>Doc 範例

來自認知搜尋團隊的程式碼範例會示範功能和工作流程。 教學課程、快速入門和操作說明文章中都會參考許多這些範例。 您可以在 Azure 中找到這些範例 [**-範例/azure-搜尋-java-**](https://github.com/Azure-Samples/azure-search-java-samples) 在 GitHub 上的範例。

| 範例 | 發行項 | 
|---------|-------------|
| [快速入門](https://github.com/Azure-Samples/azure-search-java-samples/tree/java-rest-api/quickstart) | [快速入門的原始程式碼：以 JAVA 和 REST 建立搜尋索引](search-get-started-java.md)。 此範例尚未針對 JAVA SDK 進行更新。 它會呼叫 REST Api。 |

> [!Tip]
> 請嘗試 [範例瀏覽器](/samples/browse/?languages=java&products=azure-cognitive-search) ，以搜尋 Github 中的 Microsoft 程式碼範例，並依產品、服務和語言進行篩選。

## <a name="other-samples"></a>其他範例

下列範例也會由認知搜尋小組發佈，但不會在檔中參考。 相關聯的讀我檔案會提供使用指示。

| 範例 | 描述 |
|---------|-------------|
| [搜尋-java-入門](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/search-java-getting-started) | 使用 JAVA SDK 用戶端程式庫來建立、載入和查詢搜尋索引。 此範例目前是獨立的。 |
| [搜尋-java-索引子-示範](https://github.com/Azure-Samples/azure-search-java-samples/tree/java-rest-api/search-java-indexer-demo) | 示範 JAVA 中的 Azure Cosmos DB 索引子。 此範例尚未針對 JAVA SDK 進行更新。 它會呼叫 REST Api。|