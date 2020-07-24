---
title: 依搜尋索引中的語言篩選
titleSuffix: Azure Cognitive Search
description: 篩選準則，以支援多重語言搜尋，將查詢執行範圍設定為語言特定欄位。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: 3bd0f01193b1e6579d60b581b43ad71ebb6001e3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87084189"
---
# <a name="how-to-filter-by-language-in-azure-cognitive-search"></a>如何使用 Azure 認知搜尋中的語言進行篩選 

在多國語言的搜尋應用程式中，有一個關鍵需求是能夠以使用者自己的語言搜尋和擷取結果。 在 Azure 認知搜尋中，符合多語系應用程式語言需求的方法之一，就是建立一系列專門用來儲存特定語言字串的欄位，然後在查詢時將全文檢索搜尋限制在這些欄位。

要求上的查詢參數可用來設定搜尋作業的範圍，接著如果欄位未提供與您想要傳遞之搜尋體驗相容的內容，則可調整所有這類欄位的結果。

| 參數 | 目的 |
|-----------|--------------|
| **searchFields** | 限制只對具名欄位清單進行全文檢索搜尋。 |
| **$select** | 調整回應，只包含您指定的欄位。 預設會傳回所有可擷取的欄位。 **$Select** 參數可讓您選擇要傳回哪些欄位。 |

這個技術的成功與否，取決於欄位內容的完整性。 Azure 認知搜尋不會轉譯字串或執行語言偵測。 必須由您來確定欄位包含預期的字串。

## <a name="define-fields-for-content-in-different-languages"></a>定義適用於不同語言內容的欄位

在 Azure 認知搜尋中，查詢會以單一索引為目標。 想要在單一搜尋體驗中提供特定語言之字串的開發人員，通常會定義專用欄位來儲存值：一個適用於英文字串的欄位、一個適用於法文，依此類推。 

下列範例來自于房地產[範例](search-get-started-portal.md)，其中有數個字串欄位包含不同語言的內容。 請注意此索引中欄位的語言分析器指派。 在與制訂來處理目標語言之語言規則的分析器配對時，包含字串的欄位在全文檢索搜尋中的執行效能較佳。

  ![螢幕擷取畫面，顯示房地產範例的 [欄位] 畫面。 欄位群組會反白顯示，以指出語言分析器指派如何對應至反白顯示欄位的語言。](./media/search-filters-language/lang-fields.png)

> [!Note]
> 如需利用語言分析器顯示欄位定義的程式碼範例，請參閱[定義索引 (.NET)](https://docs.microsoft.com/azure/search/search-create-index-dotnet) 和[定義索引 (REST)](search-create-index-rest-api.md)。

## <a name="build-and-load-an-index"></a>建置和載入索引

有個中繼 (且可能明顯) 的步驟是，您必須先[建立並填入索引](https://docs.microsoft.com/azure/search/search-create-index-dotnet)，之後才能編寫查詢。 我們基於完整性而在這裡提及此步驟。 判斷索引是否可用的方法之一是在[入口網站](https://portal.azure.com)中檢查索引清單。

## <a name="constrain-the-query-and-trim-results"></a>限制查詢與調整結果

查詢上的參數可用來限制搜尋特定欄位，然後調整任何對您案例不是很有助益之欄位的結果。 假設目標為限制搜尋包含法文字串的欄位，則您會使用 **searchFields**，將目標設為查詢包含該語言之字串的欄位。 

根據預設，搜尋會傳回所有標記為可擷取的欄位。 因此，針對您想要提供的特定語言搜尋體驗，您可以排除不符合的欄位。 具體來說，如果您限制搜尋具法文字串的欄位，則可能想要從結果中排除具英文字串的欄位。 使用 **$select** 查詢參數，可讓您控制要將哪些欄位傳回呼叫應用程式。

```csharp
parameters =
    new SearchParameters()
    {
        searchFields = "description_fr" 
        Select = new[] { "description_fr"  }
    };
```
> [!Note]
> 雖然查詢上沒有 $filter 引數，但此使用案例與篩選概念有強烈的關聯，因此會以篩選案例的形式呈現。

## <a name="see-also"></a>另請參閱

+ [Azure 認知搜尋中的篩選](search-filters.md)
+ [語言分析器](https://docs.microsoft.com/rest/api/searchservice/language-support)
+ [全文檢索搜尋如何在 Azure 認知搜尋中運作](search-lucene-query-architecture.md)
+ [搜尋檔 REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)

