---
title: 常見問題集 (FAQ)
titleSuffix: Azure Cognitive Search
description: 取得 Microsoft Azure 認知搜尋服務（Microsoft Azure 上的雲端託管搜尋服務）的常見問題解答。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: 9d6acdcf9487b2d1a5964d4ec686cd23666275b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88923087"
---
# <a name="azure-cognitive-search---frequently-asked-questions-faq"></a>Azure 認知搜尋-常見問題 (常見問題) 

 尋找 Azure 認知搜尋相關概念、程式碼和案例常見問題的解答。

## <a name="platform"></a>平台

### <a name="how-is-azure-cognitive-search-different-from-full-text-search-in-my-dbms"></a>Azure 認知搜尋與我的 DBMS 中的全文檢索搜尋有何不同？

Azure 認知搜尋支援多個資料來源、 [許多語言的語言分析](/rest/api/searchservice/language-support)、 [有趣和不尋常資料輸入的自訂分析](/rest/api/searchservice/custom-analyzers-in-azure-search)、透過 [評分設定檔](/rest/api/searchservice/add-scoring-profiles-to-a-search-index)搜尋排名控制項，以及自動提示、搜尋結果醒目提示和多面向導覽等使用者體驗功能。 它也包含其他功能，例如同義字和豐富的查詢語法，但這些功能通常並無不同之處。

### <a name="can-i-pause-azure-cognitive-search-service-and-stop-billing"></a>我可以暫停 Azure 認知搜尋服務並停止計費嗎？

您無法暫停服務。 建立服務時會配置計算和儲存體資源專供您使用。 您無法視需要釋放及回收這些資源。

## <a name="indexing-operations"></a>索引作業

### <a name="move-backup-and-restore-indexes-or-index-snapshots"></a>移動、備份和還原索引或索引快照集？

在開發階段，您可能會想要在搜尋服務之間移動索引。 例如，您可以使用基本或免費定價層來開發您的索引，然後想要將它移至標準或更高層級，以供生產環境使用。 

或者，您可能會想要將索引快照集備份到稍後可用來還原的檔案。 

您可以使用此[Azure 認知搜尋 .net 範例](https://github.com/Azure-Samples/azure-search-dotnet-samples)存放庫中的**索引備份-還原範例程式**代碼來完成所有這些作業。 

您也可以使用 Azure 認知搜尋 REST API，隨時 [取得索引定義](/rest/api/searchservice/get-index) 。

Azure 入口網站中目前沒有內建的索引解壓縮、快照集或備份還原功能。 不過，我們會考慮在未來的版本中新增備份和還原功能。 如果您想要顯示您對這項功能的支援，請在 [使用者心聲](https://feedback.azure.com/forums/263029-azure-search/suggestions/8021610-backup-snapshot-of-index)上轉換投票。

### <a name="can-i-restore-my-index-or-service-once-it-is-deleted"></a>已刪除的索引或服務是否可以還原？

否，如果您刪除 Azure 認知搜尋索引或服務，就無法復原。 當您刪除 Azure 認知搜尋服務時，服務中的所有索引都會永久刪除。 如果您刪除包含一或多個 Azure 認知搜尋服務的 Azure 資源群組，則會永久刪除所有服務。  

重新建立索引、索引子、資料來源和技能集等資源，需要從程式碼重新建立。 

若要重新建立索引，您必須從外部來源重新建立資料的索引。 基於這個理由，建議您將原始資料的主要複本或備份保留在另一個資料存放區中，例如 Azure SQL Database 或 Cosmos DB。

或者，您可以使用此[Azure 認知搜尋 .net 範例](https://github.com/Azure-Samples/azure-search-dotnet-samples)存放庫中的**索引備份-還原範例程式**代碼，將索引定義和索引快照集備份至一系列的 JSON 檔案。 稍後，您可以使用工具和檔案來還原索引（如有需要）。  

### <a name="can-i-index-from-sql-database-replicas-applies-to-azure-sql-database-indexers"></a>我可以從 SQL Database 複本建立索引， (適用于 [Azure SQL Database 索引子](./search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)) 

從頭開始建立索引時，要使用主要或次要複本作為資料來源並沒有限制。 不過，使用累加式更新重新整理索引 (根據變更的記錄) 則需要主要複本。 這項需求是來自 SQL Database，以保證只對主要複本進行變更追蹤。 如果您嘗試針對索引重新整理工作負載使用次要複本，則不保證會取得所有資料。

## <a name="search-operations"></a>搜尋作業

### <a name="can-i-search-across-multiple-indexes"></a>我可以搜尋多個索引嗎？

不可以，不支援此作業。 搜尋的範圍一律是單一索引。

### <a name="can-i-restrict-search-index-access-by-user-identity"></a>我可以根據使用者身分識別來限制搜尋索引的存取權嗎？

您可以使用 `search.in()` 篩選器來實作[安全性篩選](./search-security-trimming-for-azure-search.md)。 篩選條件是利用[身分識別管理服務，例如 Azure Active Directory (AAD)](./search-security-trimming-for-azure-search-with-aad.md) 撰寫而成，能以定義的使用者群組成員資格為基礎修剪搜尋結果。

### <a name="why-are-there-zero-matches-on-terms-i-know-to-be-valid"></a>為什麼我認為有效的字詞沒有任何相符項目？

最常見的情況是不知道每種查詢類型支援不同的搜尋行為和語言分析層級。 全文檢索搜尋是主流工作負載，包括將字詞細分為根表單的語言分析階段。 這種查詢剖析可以擴展可能的相符項目範圍，因為語彙基元化的字詞會符合更多變體。

不過，萬用字元、模糊和 Regex 查詢的分析方式不像一般的字詞或片語查詢，且如果查詢不符合搜尋索引中的文字分析形式，就會造成不良的重新叫用。 如需查詢剖析和分析的詳細資訊，請參閱 [查詢架構](./search-lucene-query-architecture.md)。

### <a name="my-wildcard-searches-are-slow"></a>我的萬用字元搜尋速度很慢。

諸如前置詞、模糊和 Regex 等大部分的萬用字元搜尋查詢，都是使用搜尋索引中相符的的字詞，在內部重新寫入。 掃描搜尋索引的這項額外處理會增加延遲。 此外，以許多字詞重新撰寫的廣泛搜尋查詢 (例如 `a*`) 可能會非常緩慢。 對於高效能的萬用字元搜尋，請考量定義[自訂分析器](/rest/api/searchservice/custom-analyzers-in-azure-search)。

### <a name="why-is-the-search-rank-a-constant-or-equal-score-of-10-for-every-hit"></a>為何搜尋排名是常數或相當於 1.0 的分數 (每次命中時)？

根據預設，搜尋結果會根據[相符字詞的統計屬性](search-lucene-query-architecture.md#stage-4-scoring)進行評分，並在結果集中由高排到低。 不過，某些查詢類型 (萬用字元、前置詞、Regex) 一律會對文件的整體分數提供常數分數。 這是設計的行為。 Azure 認知搜尋會強制執行一個常數分數，以允許透過查詢擴充找到的相符專案包含在結果中，而不會影響排名。

例如，假設萬用字元搜尋中的 "教學 *" 輸入會在 "旅程"、"tourettes" 和 "tourmaline" 上產生相符專案。 考慮到這些結果的本質，便無法合理推斷哪些詞彙較其他詞彙更有價值。 基於這個理由，當評分導致萬用字元、前置詞和 Regex 的查詢類型時，我們會忽略字詞頻率。 依據部分輸入的搜尋結果會得到一個常數分數，以避免可能無法預期的相符項目偏差。

## <a name="skillset-operations"></a>技能集作業

### <a name="are-there-any-tips-or-tricks-to-reduce-cognitive-services-charges-on-ingestion"></a>是否有任何秘訣或訣竅可減少內嵌的認知服務費用？

您不想要執行內建技能或自訂技能，而不是絕對必要，特別是當您處理數百萬份檔時。 記住這一點之後，我們已新增「增量擴充」功能來技能集執行。 基本上，您可以提供快取位置 (blob 儲存體連接字串) ，此字串將用來儲存 "中繼" 擴充步驟的輸出。  這可讓擴充管線成為智慧型，並且只套用當您修改技能集時所需的擴充。 這也可以節省索引時間，因為管線將會更有效率。

深入瞭解 [增量擴充](cognitive-search-incremental-indexing-conceptual.md)

## <a name="design-patterns"></a>設計模式

### <a name="what-is-the-best-approach-for-implementing-localized-search"></a>實作當地語系化搜尋的最佳做法為何？

若要在相同索引中支援不同的地區設定 (語言)，大多數客戶都會選擇專用欄位，而不是集合。 地區設定特定的欄位可讓您指派適當的分析器。 例如，將 Microsoft 法文分析器指派給含有法文字串的欄位。 它也可以簡化篩選。 如果您知道查詢是在 fr-fr 頁面上起始，您可以將搜尋結果限制在此欄位。 或者，建立[評分設定檔](/rest/api/searchservice/add-scoring-profiles-to-a-search-index)以提高欄位的相對權重。 Azure 認知搜尋支援50以上的 [語言分析器](./search-language-support.md) 來選擇。

## <a name="next-steps"></a>接下來的步驟

您的問題是否與缺少特性或功能相關？ 請在 [User Voice 網站](https://feedback.azure.com/forums/263029-azure-search)上要求此功能。

## <a name="see-also"></a>另請參閱

 [StackOverflow： Azure 認知搜尋](https://stackoverflow.com/questions/tagged/azure-search)   
 [全文檢索搜尋如何在 Azure 認知搜尋中運作](search-lucene-query-architecture.md)  
 [什麼是 Azure 認知搜尋？](search-what-is-azure-search.md)