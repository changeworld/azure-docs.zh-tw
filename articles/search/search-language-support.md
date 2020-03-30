---
title: 非英語搜索查詢的多語言索引
titleSuffix: Azure Cognitive Search
description: Azure 認知搜索支援 56 種語言，利用來自 Lucene 的語言分析器和 Microsoft 的自然語言處理技術。
manager: nitinme
author: yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ca2bc66c755da2011cc7016f37b194caa6200d9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793598"
---
# <a name="how-to-create-an-index-for-multiple-languages-in-azure-cognitive-search"></a>如何在 Azure 認知搜索中為多種語言創建索引

索引可以包含包含多種語言內容的欄位，例如，為特定于語言的字串創建單個欄位。 為了在索引和查詢期間獲得最佳結果，請分配一個提供適當語言規則的語言分析器。 

Azure 認知搜索提供了來自 Lucene 和 Microsoft 的大量語言分析器，這些語言分析器可以使用 Analyzer 屬性分配給各個欄位。 您還可以在門戶中指定語言分析器，如本文所述。

## <a name="add-analyzers-to-fields"></a>將分析器添加到欄位

創建欄位時指定語言分析器。 將分析器添加到現有欄位定義需要覆蓋（和重新載入）索引，或創建與原始欄位相同的新欄位，但流量分析器分配。 然後，您可以在方便時刪除未使用的欄位。

1. 登錄到 Azure[門戶](https://portal.azure.com)並找到搜索服務。
1. 在服務儀表板頂端的命令列中按一下 [新增索引] **** 即可開始新的索引，或開啟現有索引以在您加入至現有索引的新欄位上設定分析器。
1. 通過提供名稱來啟動欄位定義。
1. 選擇 Edm.String 資料類型。 只有字串欄位是可搜索的全文。
1. 設置**可搜索**屬性以啟用分析器屬性。 欄位必須基於文本才能使用語言分析器。
1. 選擇一個可用的分析儀。 

![在欄位定義期間分配語言分析器](media/search-language-support/select-analyzer.png "在欄位定義期間分配語言分析器")

預設情況下，所有可搜索欄位都使用與語言無關[的標準 Lucene 分析器](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html)。 要查看受支援的分析器的完整清單，請參閱[將語言分析器添加到 Azure 認知搜索索引](index-add-language-analyzers.md)中。

在門戶中，分析器旨在原樣使用。 如果需要自訂或特定配置篩選器和標記器，則應在代碼中[創建自訂分析器](index-add-custom-analyzers.md)。 門戶不支援選擇或配置自訂分析器。

## <a name="query-language-specific-fields"></a>查詢特定于語言的欄位

一旦針對某個欄位選取語言分析器，它將用於該欄位的每個索引和搜尋要求。 當使用不同分析器對多個欄位發出查詢時，查詢將由為每個欄位的分配分析器獨立處理。

如果已知發出查詢之代理程式的語言，則可使用 **searchFields** 查詢參數將搜尋要求的範圍限制為特定欄位。 下列查詢只會針對波蘭文描述發出：

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=PolishContent&api-version=2019-05-06`

您可以使用[**"搜索資源管理器"**](search-explorer.md)從門戶查詢索引，以粘貼類似于上面所示的查詢。

## <a name="boost-language-specific-fields"></a>提升特定于語言的欄位

有時候不知道發出查詢之代理程式的語言，在此情況下，可以針對所有欄位同時發出查詢。 如有需要，可以使用 [評分設定檔](index-add-scoring-profiles.md)來定義特定語言之結果的喜好設定。 在下面範例中，相對於波蘭文和法文的相符項目，在英文描述中找到的相符項目會有較高的評分：

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2019-05-06`

## <a name="next-steps"></a>後續步驟

如果您是 .NET 開發人員，請注意可以使用[Azure 認知搜索 .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Search)和分析器屬性配置語言[分析器](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet)。 