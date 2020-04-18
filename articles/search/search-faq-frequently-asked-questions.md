---
title: 常見問題集 (FAQ)
titleSuffix: Azure Cognitive Search
description: 獲取有關 Microsoft Azure 認知搜尋服務的常見問題的解答,該服務是 Microsoft Azure 上的雲端管搜尋服務。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: 520699b81024de9491f34263f16872428ddbd487
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81618036"
---
# <a name="azure-cognitive-search---frequently-asked-questions-faq"></a>Azure 認知搜尋 ─ 常見問題 (FAQ)

 查找有關與 Azure 認知搜索相關的概念、代碼和方案的常見問題的解答。

## <a name="platform"></a>平台

### <a name="how-is-azure-cognitive-search-different-from-full-text-search-in-my-dbms"></a>Azure 認知搜索與 DBMS 中的全文搜索有什麼不同?

Azure 認知搜索支援多個資料來源、[多種語言的語言分析](https://docs.microsoft.com/rest/api/searchservice/language-support)、[有趣和異常數據輸入的自定義分析](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)、通過[評分配置檔](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)搜索排名控制項以及使用者體驗功能(如鍵入、點擊突出顯示和分面導航)。 它也包含其他功能，例如同義字和豐富的查詢語法，但這些功能通常並無不同之處。

### <a name="can-i-pause-azure-cognitive-search-service-and-stop-billing"></a>我可以暫停 Azure 認知搜索服務並停止計費嗎?

您無法暫停服務。 建立服務時會配置計算和儲存體資源專供您使用。 您無法視需要釋放及回收這些資源。

## <a name="indexing-operations"></a>索引作業

### <a name="move-backup-and-restore-indexes-or-index-snapshots"></a>移動、備份和還原索引或索引快照?

在開發階段,您可能希望在搜索服務之間移動索引。 例如,您可以使用基本定價或免費定價層來開發索引,然後希望將其移動到標準層或更高層以進行生產使用。 

或者,您可能希望將索引快照備份到可用於以後還原的檔。 

您可以使用此[Azure 認知搜尋 .NET 示例回購](https://github.com/Azure-Samples/azure-search-dotnet-samples)中的**索引備份還原**範例代碼執行所有這些操作。 

您可以隨時使用 Azure 認知搜尋 REST API[取得索引定義](https://docs.microsoft.com/rest/api/searchservice/get-index)。

Azure 門戶中當前沒有內置索引提取、快照或備份還原功能。 但是,我們正在考慮在將來的版本中添加備份和還原功能。 如果要顯示您對此功能的支援,請對[用戶語音](https://feedback.azure.com/forums/263029-azure-search/suggestions/8021610-backup-snapshot-of-index)進行投票。

### <a name="can-i-restore-my-index-or-service-once-it-is-deleted"></a>已刪除的索引或服務是否可以還原？

否,如果刪除 Azure 認知搜索索引或服務,則無法恢復它。 刪除 Azure 認知搜尋服務時,該服務中的所有索引都將永久刪除。 如果刪除包含一個或多個 Azure 認知搜尋服務的 Azure 資源組,則所有服務都將永久刪除。  

重新建立資源(如索引、索引器、資料源和技能集)需要從代碼重新創建這些資源。 

要重新創建索引,必須重新索引來自外部源的數據。 因此,建議您將原始數據的主副本或備份保留在另一個數據存儲中,如 Azure SQL 資料庫或 Cosmos DB。

作為替代方法,您可以在此[Azure 認知搜索 .NET 示例回購](https://github.com/Azure-Samples/azure-search-dotnet-samples)中使用**索引備份還原**示例代碼將索引定義和索引快照備份到一系列 JSON 檔。 稍後,如果需要,可以使用工具和檔還原索引。  

### <a name="can-i-index-from-sql-database-replicas-applies-to-azure-sql-database-indexers"></a>我可以從 SQL 資料庫複本建立索引嗎 (適用於 [Azure SQL Database 索引子](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-database-to-azure-search-using-indexers))

從頭開始建立索引時，要使用主要或次要複本作為資料來源並沒有限制。 不過，使用累加式更新重新整理索引 (根據變更的記錄) 則需要主要複本。 這項需求是來自 SQL Database，以保證只對主要複本進行變更追蹤。 如果您嘗試針對索引重新整理工作負載使用次要複本，則不保證會取得所有資料。

## <a name="search-operations"></a>搜尋作業

### <a name="can-i-search-across-multiple-indexes"></a>我可以搜尋多個索引嗎？

不可以，不支援此作業。 搜尋的範圍一律是單一索引。

### <a name="can-i-restrict-search-index-access-by-user-identity"></a>我可以限制使用者身份的搜索索引訪問嗎?

您可以使用 `search.in()` 篩選器來實作[安全性篩選](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search)。 篩選條件是利用[身分識別管理服務，例如 Azure Active Directory (AAD)](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search-with-aad) 撰寫而成，能以定義的使用者群組成員資格為基礎修剪搜尋結果。

### <a name="why-are-there-zero-matches-on-terms-i-know-to-be-valid"></a>為什麼我認為有效的字詞沒有任何相符項目？

最常見的情況是不知道每種查詢類型支援不同的搜尋行為和語言分析層級。 全文搜索是主要工作負荷,包括將術語分解為根窗體的語言分析階段。 這種查詢剖析可以擴展可能的相符項目範圍，因為語彙基元化的字詞會符合更多變體。

不過，萬用字元、模糊和 Regex 查詢的分析方式不像一般的字詞或片語查詢，且如果查詢不符合搜尋索引中的文字分析形式，就會造成不良的重新叫用。 有關查詢分析和分析的詳細資訊,請參閱[查詢體系結構](https://docs.microsoft.com/azure/search/search-lucene-query-architecture)。

### <a name="my-wildcard-searches-are-slow"></a>我的萬用字元搜尋速度很慢。

諸如前置詞、模糊和 Regex 等大部分的萬用字元搜尋查詢，都是使用搜尋索引中相符的的字詞，在內部重新寫入。 掃描搜尋索引的這項額外處理會增加延遲。 此外，以許多字詞重新撰寫的廣泛搜尋查詢 (例如 `a*`) 可能會非常緩慢。 對於高效能的萬用字元搜尋，請考量定義[自訂分析器](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)。

### <a name="why-is-the-search-rank-a-constant-or-equal-score-of-10-for-every-hit"></a>為何搜尋排名是常數或相當於 1.0 的分數 (每次命中時)？

根據預設，搜尋結果會根據[相符字詞的統計屬性](search-lucene-query-architecture.md#stage-4-scoring)進行評分，並在結果集中由高排到低。 不過，某些查詢類型 (萬用字元、前置詞、Regex) 一律會對文件的整體分數提供常數分數。 這是設計的行為。 Azure 認知搜索強制施加一個常數分數,以允許在不影響排名的情況下將通過查詢擴展找到的匹配項包含在結果中。

例如,假設通配符搜索中的"tour+"輸入在"旅遊"、"旅遊"和"Tourmaline"上生成匹配項。 考慮到這些結果的本質，便無法合理推斷哪些詞彙較其他詞彙更有價值。 基於這個理由，當評分導致萬用字元、前置詞和 Regex 的查詢類型時，我們會忽略字詞頻率。 依據部分輸入的搜尋結果會得到一個常數分數，以避免可能無法預期的相符項目偏差。

## <a name="skillset-operations"></a>技能組作業

### <a name="are-there-any-tips-or-tricks-to-reduce-cognitive-services-charges-on-ingestion"></a>是否有任何提示或技巧來降低認知服務費用的攝入?

您可以理解的是,您不想執行比絕對必要的更多內置技能或自定義技能,尤其是在處理要處理的數百萬個文檔時。 考慮到這一點,我們添加了"增量擴充"功能,以進行技能集執行。 從本質上講,您可以提供一個緩存位置(Blob 儲存連接字串),用於存儲"中間"擴充步驟的輸出。  這允許濃縮管道是智慧的,並且僅應用修改技能集時所需的擴充。 這自然會節省索引時間,因為管道效率會更高。

瞭解有關[增量擴充](cognitive-search-incremental-indexing-conceptual.md)的更多

## <a name="design-patterns"></a>設計模式

### <a name="what-is-the-best-approach-for-implementing-localized-search"></a>實作當地語系化搜尋的最佳做法為何？

若要在相同索引中支援不同的地區設定 (語言)，大多數客戶都會選擇專用欄位，而不是集合。 地區設定特定的欄位可讓您指派適當的分析器。 例如，將 Microsoft 法文分析器指派給含有法文字串的欄位。 它也可以簡化篩選。 如果您知道查詢是在 fr-fr 頁面上起始，您可以將搜尋結果限制在此欄位。 或者，建立[評分設定檔](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)以提高欄位的相對權重。 Azure 認知搜索支援[50 多個語言分析器](https://docs.microsoft.com/azure/search/search-language-support)可供選擇。

## <a name="next-steps"></a>後續步驟

您的問題是否與缺少特性或功能相關？ 請在 [User Voice 網站](https://feedback.azure.com/forums/263029-azure-search)上要求此功能。

## <a name="see-also"></a>另請參閱

 [堆疊溢出:Azure 認知搜索](https://stackoverflow.com/questions/tagged/azure-search)   
 [全文檢索搜尋如何在 Azure 認知搜尋中運作](search-lucene-query-architecture.md)  
 [什麼是 Azue 認知搜尋？](search-what-is-azure-search.md)
