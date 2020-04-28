---
title: 在 Azure 入口網站中建立搜尋索引
titleSuffix: Azure Cognitive Search
description: 瞭解如何使用內建入口網站索引設計工具，建立 Azure 認知搜尋的索引。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f2e875c625431867e6e83cfd1e0b2c6d7a2781f7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74112839"
---
# <a name="create-an-azure-cognitive-search-index-in-the-portal"></a>在入口網站中建立 Azure 認知搜尋索引

Azure 認知搜尋包含入口網站中的內建索引設計工具，適用于原型，或建立裝載于 Azure 認知搜尋服務的[搜尋索引](search-what-is-an-index.md)。 此工具使用於結構描述建構。 當您儲存定義時，空的索引會在 Azure 認知搜尋中完全表示。 您可以自行決定如何使用可搜尋的內容來載入它。

索引設計工具只是建立索引的一種方法。 或者，您可以使用 [匯[入資料] wizard](search-get-started-portal.md)來建立和載入索引。 Wizard 僅適用于其本身建立的索引。 以程式設計方式，您可使用 [.NET](search-create-index-dotnet.md) 或 [REST](search-create-index-rest-api.md) API 建立索引。

## <a name="start-index-designer"></a>啟動索引設計工具

1. 登入 [Azure 入口網站](https://portal.azure.com)並開啟服務儀表板。 您可以按一下導向列中的 [所有服務]**** 以搜尋目前訂用帳戶中的現有「搜尋服務」。 

2. 在頁面頂端的命令列中，按一下 [新增索引]**** 連結。

   ![在命令列中新增索引連結](media/search-create-index-portal/add-index.png "在命令列中新增索引連結")

3. 命名您的 Azure 認知搜尋索引。 在編製索引和查詢作業中會參考索引名稱。 索引名稱會成為端點 URL 的一部分，用來連接到索引，以及在 Azure 認知搜尋 REST API 中傳送 HTTP 要求。

   * 以字母開頭。
   * 僅使用小寫字母、數字或連字號 ("-")。
   * 將名稱長度限制為 60 個字元。

## <a name="add-fields"></a>新增欄位

索引組合包含「欄位集合」**，用以定義索引中可搜尋的資料。 總之，Fields 集合會指定您分別上傳的文件結構。 Fields 集合包含必要與選用欄位 (具名與具類型)，以及用來判斷如何使用欄位的索引屬性。

1. 新增欄位以完整指定您要上傳的文件，為每個都設定[資料類型](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) \(英文\)。 例如，如果文件包含 *hotel-id*、*hotel-name*、*address*、*city* 及 *region*，請為每一個在索引中建立對應的欄位。 請檢閱[下一節中的設計指導方針](#design)，以取得設定屬性的說明。

1. 如果內送資料本質上為階層式，則您的架構應該包含[複雜類型](search-howto-complex-data-types.md)來代表嵌套結構。 內建的範例資料集飯店會使用位址（包含多個子欄位）來說明複雜型別，這些型別與每個飯店具有一對一關聯性，還有一個會議室複雜的集合，其中有多個房間與每個飯店相關聯。 

1. 指定 Edm.String 類型的 *key* 欄位。 每個 Azure 認知搜尋索引都必須要有一個金鑰欄位，而且它必須是一個字串。 此欄位的值必須唯一地識別每個文件。 根據預設，已將該欄位命名為 id**，但您可以將它重新命名，只要字串符合[命名規則](https://docs.microsoft.com/rest/api/searchservice/Naming-rules)即可。 例如，如果您的欄位集合包含 *hotel-id*，您就會針對您的索引鍵選擇它。 

1. 在每個欄位上設定屬性。 索引設計工具會排除對資料類型為無效的任何屬性，但不會建議要包含哪些。 請檢閱下一節中的指導方針，以了解有哪些屬性及其用途為何。

    Azure 認知搜尋 API 檔包含採用簡單*飯店*索引的程式碼範例。 在以下的螢幕擷取畫面中，您可以看到索引定義，包括在索引定義期間指定的法文語言分析器，而您可以在入口網站中重新建立以當作練習。

    ![飯店示範索引](media/search-create-index-portal/field-definitions.png "飯店示範索引")

1. 完成後，按一下 [建立]****，以儲存並建立索引。

<a name="design"></a>

## <a name="set-attributes"></a>設定屬性

雖然您可以隨時新增欄位，但現有的欄位定義會在索引的存留期間加以鎖定。 基於這個理由，開發人員通常會使用入口網站來建立簡單的索引、測試概念，或使用管理入口網站頁面來查詢設定。 如果您遵循以程式碼為基礎的方法，則整個索引設計中頻繁的反覆動作就會更有效率，如此便能讓您輕鬆地重建索引。

系統會先將分析器與建議工具關聯至欄位，然後再儲存索引。 在建立索引定義時，請務必加入語言分析器或建議工具。

字串欄位通常會標示為 [可搜尋]**** 和 [可擷取]****。 用來縮小搜尋結果的欄位包括 [可排序]****、[可篩選]**** 及 [可 Facet]****。

欄位屬性會決定欄位的使用方式，例如，是否將它用於全文檢索搜尋、多面向導覽、排序作業等。 下表描述每個屬性。

|屬性|描述|  
|---------------|-----------------|  
|**線上**|可進行全文檢索搜尋，受限於語彙分析，例如，在編製索引期間執行斷字功能。 如果您為可搜尋的欄位設定像是「sunny day」的值，則系統會在內部將它分割為「sunny」和「day」這兩個個別的語彙基元。 如需詳細資訊，請參閱[全文檢索搜尋如何運作](search-lucene-query-architecture.md)。|  
|**篩選**|在 **$filter** 查詢中加以參考。 類型 `Edm.String` 或 `Collection(Edm.String)` 的可篩選欄位不會執行斷字功能，因此，只會針對完全相符的項目進行比較。 例如，如果您將欄位 f 之類的欄位設為「sunny day」，`$filter=f eq 'sunny'` 就會找不到相符項目，但 `$filter=f eq 'sunny day'` 可以。 |  
|**分類**|根據預設，系統會依分數來排序結果，但您可根據文件中的欄位設定排序。 類型 `Collection(Edm.String)` 的欄位不能是 **sortable**。 |  
|**可面向化**|通常用來呈現搜尋結果，其中包含依類別的叫用次數 (例如，特定城市中的旅館)。 此選項無法與類型 `Edm.GeographyPoint`的欄位搭配使用。 類型為 `Edm.String` 的欄位 (其為 **filterable**、**sortable** 或 **facetable**) 長度最多可達 32 KB。 如需詳細資訊，請參閱[建立索引 (REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index)。|  
|**key**|索引內文件的唯一識別碼。 您必須只選擇一個欄位作為索引鍵欄位，而它的類型必須是 `Edm.String`。|  
|**可擷取**|判斷搜尋結果中是否會傳回該欄位。 當您想要使用某個欄位 (例如 profit margin**) 作為篩選、排序或評分機制，但不想讓使用者看見該欄位時，這非常有用。 針對 `true` for `key` 。|  

## <a name="next-steps"></a>後續步驟

建立 Azure 認知搜尋索引之後，您可以移至下一個步驟：將可搜尋[的資料上傳至索引](search-what-is-data-import.md)。

或者，您可能也需要[深入探討索引](search-what-is-an-index.md)。 除了欄位集合之外，索引也會指定分析器、建議工具、評分設定檔和 CORS 設定。 入口網站提供索引標籤頁面來定義最常見的元素：欄位、分析器及建議工具。 若要建立或修改其他元素，您可以使用 REST API 或 .NET SDK。

## <a name="see-also"></a>另請參閱

 [全文檢索搜尋如何運作](search-lucene-query-architecture.md)  
 [搜尋服務 REST API](https://docs.microsoft.com/rest/api/searchservice/) [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

