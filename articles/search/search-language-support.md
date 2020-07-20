---
title: 非英文搜尋查詢的多語言索引
titleSuffix: Azure Cognitive Search
description: Azure 認知搜尋支援56語言，利用來自 Microsoft 的 Lucene 和自然語言處理技術的語言分析器。
manager: nitinme
author: yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 54def7f03e28acb201d613ab5a13d9077cff121b
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86146760"
---
# <a name="how-to-create-an-index-for-multiple-languages-in-azure-cognitive-search"></a>如何在 Azure 認知搜尋中建立多個語言的索引

索引可以包含包含多種語言之內容的欄位，例如，針對特定語言的字串建立個別欄位。 若要在編制索引和查詢時獲得最佳結果，請指派語言分析器，以提供適當的語言規則。 

Azure 認知搜尋提供來自 Lucene 和 Microsoft 的大量語言分析器選擇，可流量分析器屬性指派給個別欄位。 您也可以在入口網站中指定語言分析器，如這篇文章中所述。

## <a name="add-analyzers-to-fields"></a>將分析器新增至欄位

建立欄位時，會指定語言分析器。 將分析器新增至現有的欄位定義時，需要覆寫 (並重載索引) 或建立與原始相同的新欄位，但流量分析器指派。 您接著可以在方便時刪除未使用的欄位。

1. 登入[Azure 入口網站](https://portal.azure.com)並尋找您的搜尋服務。
1. 在服務儀表板頂端的命令列中按一下 [新增索引] **** 即可開始新的索引，或開啟現有索引以在您加入至現有索引的新欄位上設定分析器。
1. 藉由提供名稱來啟動欄位定義。
1. 選擇 [Edm] 資料類型。 只有字串欄位可以進行全文檢索搜尋。
1. 設定可搜尋**的屬性，** 以啟用 Analyzer 屬性。 欄位必須是以文字為基礎，才能使用語言分析器。
1. 選擇其中一個可用的分析器。 

![在欄位定義期間指派語言分析器](media/search-language-support/select-analyzer.png "在欄位定義期間指派語言分析器")

根據預設，所有可搜尋的欄位都會使用與語言無關的[標準 Lucene 分析器](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html)。 若要查看支援分析器的完整清單，請參閱[將語言分析器新增至 Azure 認知搜尋索引](index-add-language-analyzers.md)。

在入口網站中，分析器的目的是要以原本的方式使用。 如果您需要進行自訂或篩選準則和 token 化工具的特定設定，您應該在程式碼中[建立自訂分析器](index-add-custom-analyzers.md)。 入口網站不支援選取或設定自訂分析器。

## <a name="query-language-specific-fields"></a>查詢語言特定欄位

一旦針對某個欄位選取語言分析器，它將用於該欄位的每個索引和搜尋要求。 當使用不同的分析器對多個欄位發出查詢時，查詢將會由每個欄位指派的分析器獨立處理。

如果已知發出查詢之代理程式的語言，則可使用 **searchFields** 查詢參數將搜尋要求的範圍限制為特定欄位。 下列查詢只會針對波蘭文描述發出：

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=PolishContent&api-version=2020-06-30`

您可以從入口網站查詢您的索引，使用 [[**搜尋瀏覽器**](search-explorer.md)] 貼上類似上述所示的查詢。

## <a name="boost-language-specific-fields"></a>提升語言特定欄位

有時候不知道發出查詢之代理程式的語言，在此情況下，可以針對所有欄位同時發出查詢。 如有需要，可以使用 [評分設定檔](index-add-scoring-profiles.md)來定義特定語言之結果的喜好設定。 在下面範例中，相對於波蘭文和法文的相符項目，在英文描述中找到的相符項目會有較高的評分：

```http
    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]
```

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2020-06-30`

## <a name="next-steps"></a>後續步驟

如果您是 .NET 開發人員，請注意，您可以使用[Azure 認知搜尋 .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Search)和[Analyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet)屬性來設定語言分析器。 