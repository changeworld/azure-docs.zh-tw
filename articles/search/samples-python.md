---
title: Python 範例
titleSuffix: Azure Cognitive Search
description: 尋找使用適用于 Python 的 Azure .NET SDK 或 REST 的 Azure 認知搜尋示範 Python 程式碼範例。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 0d09851cf8e68cead4a67615aaa792512482f351
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955117"
---
# <a name="python-code-samples-for-azure-cognitive-search"></a>Azure 認知搜尋的 Python 程式碼範例

深入瞭解 Python 程式碼範例，以示範 Azure 認知搜尋解決方案的功能和工作流程。 這些範例會使用適用于 [**AZURE SDK For Python**](/azure/developer/python/)的 [**Azure 認知搜尋用戶端程式庫**](/python/api/overview/azure/search-documents-readme)，您可以透過下列連結流覽。

| 目標 | 連結 |
|--------|------|
| 套件下載 | [pypi.org/project/azure-search-documents/](https://pypi.org/project/azure-search-documents/) |
| API 參考資料 | [azure-search-documents](/python/api/azure-search-documents)  |
| API 測試案例 | [github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests) |
| 原始程式碼 | [github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents)  |

## <a name="sdk-samples"></a>SDK 範例

來自 Azure SDK 開發小組的程式碼範例會示範 API 的使用方式。 您可以在 azure sdk 中找到這些範例 [**-適用于 python/樹狀結構/master/sdk/搜尋/azure-搜尋-檔/範例**](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) （位於 GitHub）。

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

## <a name="doc-samples"></a>Doc 範例

來自認知搜尋團隊的程式碼範例會示範功能和工作流程。 教學課程、快速入門和操作說明文章中都會參考許多這些範例。 您可以在 Azure 中找到這些範例 [**-範例/azure-搜尋-python-**](https://github.com/Azure-Samples/azure-search-python-samples) 在 GitHub 上的範例。

| 範例 | 發行項 |
|---------|---------|
| [快速入門](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Quickstart) | [快速入門的原始程式碼：在 Python 中建立搜尋索引](search-get-started-python.md)。 本文涵蓋使用範例資料建立、載入和查詢搜尋索引的基本工作流程。 |
| [教學課程-ai-擴充](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment)  | 教學課程的原始程式碼 [：使用 Python 和 AI 從 Azure blob 產生可搜尋的內容](cognitive-search-tutorial-blob-python.md)。 本文說明如何使用認知技能集建立 blob 索引子，讓技能集建立和轉換原始內容，使其可供搜尋或取用。 |
| [AzureML-自訂技能](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)  | 原始碼 [範例：使用 Python 建立自訂技能](cognitive-search-custom-skill-python.md)。 本文示範索引子和技能集與 Azure Machine Learning 中的深度學習模型整合。 |

> [!Tip]
> 請嘗試 [範例瀏覽器](/samples/browse/?languages=python&products=azure-cognitive-search) ，以搜尋 Github 中的 Microsoft 程式碼範例，並依產品、服務和語言進行篩選。