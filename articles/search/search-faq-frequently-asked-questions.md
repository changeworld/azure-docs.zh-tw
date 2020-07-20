---
title: 常見問題集 (FAQ)
titleSuffix: Azure Cognitive Search
description: 取得有關 Microsoft Azure 認知搜尋服務（Microsoft Azure 上的雲端託管搜尋服務）常見問題的解答。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: d3c5a998db5e76118b0c5a73b6df8bdedadc6dfb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85317231"
---
# <a name="azure-cognitive-search---frequently-asked-questions-faq"></a>Azure 認知搜尋-常見問題（FAQ）

 尋找與 Azure 認知搜尋相關的概念、程式碼和案例的常見問題解答。

## <a name="platform"></a>平台

### <a name="how-is-azure-cognitive-search-different-from-full-text-search-in-my-dbms"></a>Azure 認知搜尋與我 DBMS 中的全文檢索搜尋有何不同？

Azure 認知搜尋支援多個資料來源、[多種語言的語言分析](https://docs.microsoft.com/rest/api/searchservice/language-support)、[適用于有趣和不尋常資料輸入的自訂分析](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)、透過[評分設定檔](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)搜尋排名控制項，以及使用者體驗功能（例如自動提示、點擊顯示和多面向導覽）。 它也包含其他功能，例如同義字和豐富的查詢語法，但這些功能通常並無不同之處。

### <a name="can-i-pause-azure-cognitive-search-service-and-stop-billing"></a>我可以暫停 Azure 認知搜尋服務並停止計費嗎？

您無法暫停服務。 建立服務時會配置計算和儲存體資源專供您使用。 您無法視需要釋放及回收這些資源。

## <a name="indexing-operations"></a>索引作業

### <a name="move-backup-and-restore-indexes-or-index-snapshots"></a>移動、備份和還原索引或索引快照集嗎？

在開發階段，您可能會想要在搜尋服務之間移動索引。 例如，您可以使用「基本」或「免費」定價層來開發您的索引，然後想要將它移至「標準」或「更高」層以供生產環境使用。 

或者，您可能會想要將索引快照集備份至稍後可用來還原的檔案。 

您可以使用此[Azure 認知搜尋 .net 範例](https://github.com/Azure-Samples/azure-search-dotnet-samples)存放庫中的**索引備份-還原範例程式**代碼來執行所有這些動作。 

您也可以使用 Azure 認知搜尋 REST API，隨時[取得索引定義](https://docs.microsoft.com/rest/api/searchservice/get-index)。

Azure 入口網站中目前沒有內建的索引提取、快照集或備份還原功能。 不過，我們正考慮在未來的版本中新增備份和還原功能。 如果您想要顯示這項功能的支援，請將投票轉型為[使用者心聲](https://feedback.azure.com/forums/263029-azure-search/suggestions/8021610-backup-snapshot-of-index)。

### <a name="can-i-restore-my-index-or-service-once-it-is-deleted"></a>已刪除的索引或服務是否可以還原？

否，如果您刪除 Azure 認知搜尋索引或服務，它就無法復原。 當您刪除 Azure 認知搜尋服務時，服務中的所有索引會永久刪除。 如果您刪除包含一或多個 Azure 認知搜尋服務的 Azure 資源群組，所有服務都會永久刪除。  

重新建立索引、索引子、資料來源和技能集等資源，需要您從程式碼重新建立它們。 

若要重新建立索引，您必須重新編制外部來源的資料索引。 基於這個理由，建議您在另一個資料存放區中保留原始資料的主要複本或備份，例如 Azure SQL Database 或 Cosmos DB。

或者，您可以使用此[Azure 認知搜尋 .net 範例](https://github.com/Azure-Samples/azure-search-dotnet-samples)存放庫中的**索引備份-還原**範例程式碼，將索引定義和索引快照集備份至一系列 JSON 檔案。 稍後，您可以視需要使用工具和檔案來還原索引。  

### <a name="can-i-index-from-sql-database-replicas-applies-to-azure-sql-database-indexers"></a>我可以從 SQL Database 複本編制索引（適用于[Azure SQL Database 索引子](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-database-to-azure-search-using-indexers)）

從頭開始建立索引時，要使用主要或次要複本作為資料來源並沒有限制。 不過，使用累加式更新重新整理索引 (根據變更的記錄) 則需要主要複本。 這項需求是來自 SQL Database，以保證只對主要複本進行變更追蹤。 如果您嘗試針對索引重新整理工作負載使用次要複本，則不保證會取得所有資料。

## <a name="search-operations"></a>搜尋作業

### <a name="can-i-search-across-multiple-indexes"></a>我可以搜尋多個索引嗎？

不可以，不支援此作業。 搜尋的範圍一律是單一索引。

### <a name="can-i-restrict-search-index-access-by-user-identity"></a>我可以依使用者身分識別來限制搜尋索引的存取權嗎？

您可以使用 `search.in()` 篩選器來實作[安全性篩選](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search)。 篩選條件是利用[身分識別管理服務，例如 Azure Active Directory (AAD)](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search-with-aad) 撰寫而成，能以定義的使用者群組成員資格為基礎修剪搜尋結果。

### <a name="why-are-there-zero-matches-on-terms-i-know-to-be-valid"></a>為什麼我認為有效的字詞沒有任何相符項目？

最常見的情況是不知道每種查詢類型支援不同的搜尋行為和語言分析層級。 全文檢索搜尋是主要工作負載，包括將詞彙細分為根表單的語言分析階段。 這種查詢剖析可以擴展可能的相符項目範圍，因為語彙基元化的字詞會符合更多變體。

不過，萬用字元、模糊和 Regex 查詢的分析方式不像一般的字詞或片語查詢，且如果查詢不符合搜尋索引中的文字分析形式，就會造成不良的重新叫用。 如需查詢剖析和分析的詳細資訊，請參閱[查詢架構](https://docs.microsoft.com/azure/search/search-lucene-query-architecture)。

### <a name="my-wildcard-searches-are-slow"></a>我的萬用字元搜尋速度很慢。

諸如前置詞、模糊和 Regex 等大部分的萬用字元搜尋查詢，都是使用搜尋索引中相符的的字詞，在內部重新寫入。 掃描搜尋索引的這項額外處理會增加延遲。 此外，以許多字詞重新撰寫的廣泛搜尋查詢 (例如 `a*`) 可能會非常緩慢。 對於高效能的萬用字元搜尋，請考量定義[自訂分析器](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)。

### <a name="why-is-the-search-rank-a-constant-or-equal-score-of-10-for-every-hit"></a>為何搜尋排名是常數或相當於 1.0 的分數 (每次命中時)？

根據預設，搜尋結果會根據[相符字詞的統計屬性](search-lucene-query-architecture.md#stage-4-scoring)進行評分，並在結果集中由高排到低。 不過，某些查詢類型 (萬用字元、前置詞、Regex) 一律會對文件的整體分數提供常數分數。 這是設計的行為。 Azure 認知搜尋會強加常數分數，以允許透過查詢擴充找到的相符專案包含在結果中，而不會影響排名。

例如，假設萬用字元搜尋中的「導覽 *」輸入，會產生「旅程」、「tourettes」和「tourmaline」的相符專案。 考慮到這些結果的本質，便無法合理推斷哪些詞彙較其他詞彙更有價值。 基於這個理由，當評分導致萬用字元、前置詞和 Regex 的查詢類型時，我們會忽略字詞頻率。 依據部分輸入的搜尋結果會得到一個常數分數，以避免可能無法預期的相符項目偏差。

## <a name="skillset-operations"></a>技能集作業

### <a name="are-there-any-tips-or-tricks-to-reduce-cognitive-services-charges-on-ingestion"></a>是否有任何秘訣或訣竅可減少內嵌的認知服務費用？

您也不想要執行內建技能或自訂技能，而不是絕對必要，特別是當您處理數百萬份檔的處理方式時。 記住這一點之後，我們已將「累加擴充」功能新增至技能集執行。 基本上，您可以提供快取位置（blob 儲存體連接字串），用來儲存「中繼」擴充步驟的輸出。  這可讓擴充管線成為智慧型，而且只會套用在您修改技能集時所需的擴充。 這自然也會儲存索引時間，因為管線會更有效率。

深入瞭解[增量擴充](cognitive-search-incremental-indexing-conceptual.md)

## <a name="design-patterns"></a>設計模式

### <a name="what-is-the-best-approach-for-implementing-localized-search"></a>實作當地語系化搜尋的最佳做法為何？

若要在相同索引中支援不同的地區設定 (語言)，大多數客戶都會選擇專用欄位，而不是集合。 地區設定特定的欄位可讓您指派適當的分析器。 例如，將 Microsoft 法文分析器指派給含有法文字串的欄位。 它也可以簡化篩選。 如果您知道查詢是在 fr-fr 頁面上起始，您可以將搜尋結果限制在此欄位。 或者，建立[評分設定檔](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)以提高欄位的相對權重。 Azure 認知搜尋支援超過[50 種語言分析器](https://docs.microsoft.com/azure/search/search-language-support)，可供選擇。

## <a name="next-steps"></a>後續步驟

您的問題是否與缺少特性或功能相關？ 請在 [User Voice 網站](https://feedback.azure.com/forums/263029-azure-search)上要求此功能。

## <a name="see-also"></a>另請參閱

 [StackOverflow： Azure 認知搜尋](https://stackoverflow.com/questions/tagged/azure-search)   
 [全文檢索搜尋如何在 Azure 認知搜尋中運作](search-lucene-query-architecture.md)  
 [什麼是 Azue 認知搜尋？](search-what-is-azure-search.md)
