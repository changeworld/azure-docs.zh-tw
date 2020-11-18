---
title: Java 範例
titleSuffix: Azure Cognitive Search
description: 尋找使用適用于 JAVA 的 Azure .NET SDK 的 Azure 認知搜尋示範 JAVA 程式碼範例。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 26e30b42906a3d8d7a3fcdc013537104a85f32fe
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701852"
---
# <a name="java-code-samples-for-azure-cognitive-search"></a>Azure 認知搜尋的 JAVA 程式碼範例

瞭解 JAVA 程式碼範例，以示範 Azure 認知搜尋的特性和功能。 主要存放庫如下所示：

| Repository | 描述 |
|------------|-------------|
| [azure-sdk-適用于 java/樹狀結構/master/sdk/搜尋/azure-搜尋-檔/src/範例](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) | Azure SDK 團隊所產生的範例，隨附于 SDK 中的 Azure.Search.Documents 用戶端程式庫。 您也可以檢查用戶端程式庫的 [單元測試](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/test) ，以查看如何呼叫不同的 api。 |
| [Azure-範例/azure-搜尋-java-範例](https://github.com/Azure-Samples/azure-search-java-samples) | 伴隨操作說明文章的程式碼範例。 **此存放庫中的範例尚未更新為使用 AZURE SDK For JAVA**。 目前，這些範例會以 JAVA 程式碼呼叫 REST Api。|

> [!Tip]
> 請嘗試 [範例瀏覽器](/samples/browse/?languages=csharp&products=azure-cognitive-search) ，以搜尋 Github 中的 Microsoft 程式碼範例，並依產品、服務和語言進行篩選。

## <a name="java-sdk-samples"></a>Java SDK 範例

Azure SDK for JAVA 包含許多範例，以及涵蓋套件安裝的 [快速入門頁面](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) 。 此頁面也會列出各式各樣的範例。 以下列出幾個較常見的作業，方便您參考。

| 範例 | 描述 |
|---------|-------------|
| [建立搜尋索引](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) | 示範如何建立 [搜尋索引](search-what-is-an-index.md)。 |
| [建立同義字](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SynonymMapsCreateExample.java) | 示範如何建立 [同義字對應](search-synonyms.md)。  |
| [搜尋索引子建立](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) | 示範如何建立 [索引子](search-indexer-overview.md)。 |
| [搜尋索引子資料來源建立](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/DataSourceExample.java) | 示範如何建立索引子資料來源，這些是 [支援的 Azure 資料來源](search-indexer-overview.md#supported-data-sources)索引子索引所需的索引子。 |
| [技能集建立](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateSkillsetExample.java) |  示範如何建立附加索引子的 [技能集](cognitive-search-working-with-skillsets.md) ，以及在編制索引期間執行以 AI 為基礎的擴充。 |
| [載入文件](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/IndexContentManagementExample.java) | 示範如何在 [資料匯入](search-what-is-data-import.md) 作業中，將檔上傳或合併到索引中。 |
| [查詢語法](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchAsyncWithFullyTypedDocumentsExample.java) | 示範如何設定 [基本查詢](search-query-overview.md)。 |

## <a name="documentation-samples"></a>文件和範例

下列範例有 [Azure 認知搜尋檔](https://docs.microsoft.com/azure/search/)中的相關文章。

| 範例 | 描述 | 
|---------|-------------|
| [快速入門](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/quickstart) | [快速入門的原始程式碼：以 JAVA 建立搜尋索引](search-get-started-java.md)。 此範例會呼叫 REST Api。 |
| [搜尋-java-索引子-示範](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/search-java-indexer-demo) | 示範 JAVA 中的 Azure Cosmos DB 索引子。 此範例會呼叫 REST Api。 |