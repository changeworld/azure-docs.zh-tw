---
title: Python 範例
titleSuffix: Azure Cognitive Search
description: 尋找使用適用于 Python 的 Azure .NET SDK 或 REST 的 Azure 認知搜尋示範 Python 程式碼範例。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 3de630552f7ad2cc941fe23369398c10ffce5870
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686656"
---
# <a name="python-code-samples-for-azure-cognitive-search"></a>Azure 認知搜尋的 Python 程式碼範例

深入瞭解 Python 程式碼範例，以示範 Azure 認知搜尋的特性和功能。 主要存放庫如下所示：

| Repository | 描述 |
|------------|-------------|
| [azure sdk for python/tree/master/sdk/搜尋/azure-搜尋-檔/範例/](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) | Azure SDK 團隊所產生的範例，隨附于 SDK 中的 Azure.Search.Documents 用戶端程式庫。 您也可以檢查用戶端程式庫的 [單元測試](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests) ，以查看如何呼叫不同的 api。 |
| [Azure-範例/azure-搜尋-python-範例](https://github.com/Azure-Samples/azure-search-python-samples) | 伴隨操作說明文章的程式碼範例，包括 [快速入門：在 Python 中建立搜尋索引](search-get-started-python.md)。|

> [!Tip]
> 請嘗試 [範例瀏覽器](/samples/browse/?languages=csharp&products=azure-cognitive-search) ，以搜尋 Github 中的 Microsoft 程式碼範例，並依產品、服務和語言進行篩選。

## <a name="python-sdk-samples"></a>Python SDK 範例

適用于 Python 的 Azure SDK 包含許多範例，以及包含必要條件和套件安裝的 [快速入門頁面](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) 。 此頁面也包含下列範例的連結，這些範例會在此處列出以方便您參考。

| 範例 | 描述 |
|---------|-------------|
| [驗證](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_authentication.py) | 示範如何設定用戶端，並向服務進行驗證。 | 
| [索引建立-讀取-更新-刪除作業](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) | 示範如何建立、更新、取得、列出和刪除 [搜尋索引](search-what-is-an-index.md)。 |
| [索引子建立-讀取-更新-刪除作業](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) | 示範如何建立、更新、取得、列出、重設及刪除 [索引子](search-indexer-overview.md)。 |
| [搜尋索引子資料來源](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexer_datasource_skillset.py) | 示範如何建立、更新、取得、列出和刪除 [支援的 Azure 資料來源](search-indexer-overview.md#supported-data-sources)之索引子型索引所需的索引子資料來源。 |
| [同義字](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_synonym_map_operations.py) | 示範如何建立、更新、取得、列出和刪除 [同義字對應](search-synonyms.md)。  |
| [載入文件](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_crud_operations.py) | 示範如何在 [資料匯入](search-what-is-data-import.md) 作業中，將檔上傳或合併到索引中。 |
| [簡單查詢](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_simple_query.py) | 示範如何設定 [基本查詢](search-query-overview.md)。 |
| [篩選查詢](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_filter_query.py) | 示範如何設定 [篩選運算式](search-filters.md)。 |
| [Facet 查詢](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_facet_query.py) | 示範如何使用 [facet](search-filters-facets.md)。 |

## <a name="documentation-samples"></a>文件和範例

下列範例有 [Azure 認知搜尋檔](https://docs.microsoft.com/azure/search/)中的相關文章。

| 範例 | 描述 | 
|---------|-------------|
| [快速入門](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Quickstart) | [快速入門的原始程式碼：在 Python 中建立搜尋索引](search-get-started-python.md)。  |
| [教學課程-ai-擴充](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment)  | 教學課程的原始程式碼 [：使用 Python 和 AI 從 Azure blob 產生可搜尋的內容](cognitive-search-tutorial-blob-python.md)。  |
| [AzureML-自訂技能](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)  | 原始碼 [範例：使用 Python 建立自訂技能](cognitive-search-custom-skill-python.md)。  |