---
title: Azure 認知搜尋簡介
titleSuffix: Azure Cognitive Search
description: Azure 認知搜尋是完全受控的雲端搜尋服務，由 Microsoft 所提供。 了解使用案例、開發工作流程、比較其他 Microsoft 搜尋產品優缺點，以及如何開始使用。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 01/22/2021
ms.custom: contperf-fy21q1
ms.openlocfilehash: 893bf37a5a4c8a314e5182bf2ac4bc28502b98d9
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98699426"
---
# <a name="what-is-azure-cognitive-search"></a>什麼是 Azue 認知搜尋？

Azure 認知搜尋 ([先前稱為「Azure 搜尋服務」](whats-new.md#new-service-name)) 是一個雲端搜尋服務，可為開發人員提供 API 和工具，透過 Web、行動和企業應用程式中的私用和異質內容來建置豐富的搜尋體驗。 

搜尋服務包含下列元件：

+ 全文檢索搜尋的搜尋引擎
+ 使用者擁有的索引內容的持續性儲存體
+ 用來編制索引和查詢的 Api
+ 選擇性的 [AI 型擴充](cognitive-search-concept-intro.md)，可從影像、原始文字、應用程式檔建立可搜尋的內容
+ 選擇性地與其他 Azure 服務整合，以取得資料、機器學習服務/AI 及安全性

就架構而言，搜尋服務位於包含未編制索引之資料的外部資料存放區，以及將查詢要求傳送至搜尋索引並處理回應的用戶端應用程式之間。

![Azure 認知搜尋架構](media/search-what-is-azure-search/azure-search-diagram.svg "Azure 認知搜尋架構")

從外部開始，搜尋可與其他 Azure 服務整合，其形式為 *索引子* ，可將資料從 Azure 資料來源自動內嵌/抓取，以及結合來自認知服務的取用 AI （例如影像和文字分析，或您在 Azure Machine Learning 中 *建立的自* 定義 ai 或在 Azure Functions 內換行的自訂 ai）。

## <a name="inside-a-search-service"></a>搜尋服務內部

搜尋服務中的兩個主要工作負載是「編製索引」及「查詢」。 

+ [編制索引](search-what-is-an-index.md) 是一種可將內容載入至搜尋服務並讓其可供搜尋的吸收流程。 系統會在內部將輸入文字處理成權杖並儲存在反向索引中，以進行快速掃描。 您可以上傳任何格式為 JSON 檔的文字。

  此外，如果您的內容包含混合檔案，您可以選擇透過 [認知技能](cognitive-search-working-with-skillsets.md)新增 *AI 擴充*。 AI 擴充可以解壓縮應用程式檔中內嵌的文字，也可以藉由分析內容，從非文字檔推斷文字和結構。 

  提供分析的技能為 Microsoft 預先定義的技能，或您所建立的自訂技能。 後續的分析和轉換可能會產生先前不存在的新資訊和結構，為許多搜尋和知識挖掘案例提供高公用程式。

+ 當您的用戶端應用程式將查詢要求傳送至搜尋服務並處理回應時，可能會在索引填入可搜尋的文字時進行[查詢](search-query-overview.md)。 所有查詢執行都是在您建立、擁有並儲存在服務中的搜尋索引之上。 在您的用戶端應用程式中，搜尋體驗是使用 Azure 認知搜尋所提供的 API 來定義的，並可以包含相關性調整、自動完成、同義字比對、模糊比對、模式比對、篩選和排序。

透過簡單的 [REST API](/rest/api/searchservice/) 或 [.NET SDK](search-howto-dotnet-sdk.md) 展現功能，並同時隱蔽資訊擷取固有之複雜性。 您也可以使用 Azure 入口網站進行服務管理和內容管理，以及搭配工具建立原型和查詢您的索引和技能集。 因為服務在雲端執行，Microsoft 會管理基礎結構和可用性。

## <a name="why-use-cognitive-search"></a>為何要使用認知搜尋

Azure 認知搜尋適用於下列應用程式案例：

+ 將異質內容合併為使用者定義的私人搜尋索引。

+ 輕鬆地執行與搜尋相關的功能：相關性微調、多面向導覽、篩選 (包括地理空間搜尋) 、同義字對應和自動完成。

+ 將大型無差異的文字或影像檔，或儲存在 Azure Blob 儲存體或 Cosmos DB 中的應用程式檔，轉換成可搜尋的 JSON 檔。 您可以透過新增外部處理的 [認知技能](cognitive-search-concept-intro.md) ，在索引期間達成此目的。

+ 新增語言或自訂文字分析。 如果您有非英文的內容，Azure 認知搜尋可支援 Lucene 分析器和 Microsoft 的自然語言處理器。 您也可以設定分析器來完成原始內容的特殊處理，例如篩選出變音符號，或辨識並保留字串中的模式。

如需特定功能的詳細資訊，請參閱 [Azure 認知搜尋的功能](search-features-list.md)

## <a name="how-to-get-started"></a>如何開始使用

核心搜尋功能的端對端探索可透過四個步驟來達成：

1. 在共用的免費層 [**建立搜尋服務**](search-create-service-portal.md)，或針對您的服務所使用的專用資源建立 [計費層](https://azure.microsoft.com/pricing/details/search/)。 所有快速入門和教學課程都可以在共用服務上完成。

1. 使用入口網站、 [REST API](/rest/api/searchservice/create-index)、 [.net SDK](search-howto-dotnet-sdk.md)或其他 SDK 來 [**建立搜尋索引**](search-what-is-an-index.md)。 索引結構描述會決定可搜尋內容的結構。

1. 使用「推送」[模型](tutorial-optimize-indexing-push-api.md)[**上傳內容**](search-what-is-data-import.md)，以從任何來源推送 JSON 檔，或使用「[提取」模型 (索引子)](search-indexer-overview.md)如果您的來源資料是在 Azure 上。

1. 在入口網站、[REST API](search-get-started-rest.md)、[.NET SDK](/dotnet/api/azure.search.documents.searchclient.search) 或另一個 SDK 中使用 [搜尋瀏覽器](search-explorer.md)來 [**查詢索引**](search-query-overview.md)。

> [!TIP]
> 使用 [**匯入資料精靈**](search-get-started-portal.md)與 Azure 資料來源以盡可能精簡步驟，而在數分鐘內建立、載入和查詢索引。

## <a name="compare-search-options"></a>比較搜尋選項

客戶常會詢問 Azure 認知搜尋與其他搜尋相關解決方案有何差異。 下表摘要說明主要差異。

| 相較於 | 主要差異 |
|-------------|-----------------|
| Microsoft 搜尋 | [Microsoft 搜尋](/microsoftsearch/overview-microsoft-search)適用於需要在 SharePoint 中查詢內容之已通過 Microsoft 365 驗證的使用者。 這是由管理員啟用和設定的立即可用搜尋體驗所提供，而且能夠透過 Microsoft 和其他來源的連接器接受外部內容。 如果這與您的案例相符，您可以了解搭配 Microsoft 365 的 Microsoft 搜尋，這是一項不錯的選擇。<br/><br/>相反地，Azure 認知搜尋會查詢您定義的索引，使用不同的來源填入您所擁有的資料與文件。 Azure 認知搜尋可透過[索引子](search-indexer-overview.md)為一些 Azure 資料來源提供編目程式功能，但您可以將任何符合您索引結構描述的 JSON 文件推送到單一、彙總的可搜尋資源。 您也可以自訂編製索引管線，以包含機器學習服務和詞彙分析器。 由於認知搜尋的本質是較大型解決方案中的外掛程式元件，因此您可以在任何平台上將搜尋整合到幾乎任何應用程式。|
|Bing | [Bing Web 搜尋 API](../cognitive-services/bing-web-search/index.yml) 會在 Bing.com 上搜尋索引中符合您送出的項目。 索引是從 HTML、XML 和其他公用網站上的網站內容所建置。 根據相同的基礎，[Bing 自訂搜尋](/azure/cognitive-services/bing-custom-search/)會為網站內容類型提供相同的編目程式技術，範圍設定為個別的網站。<br/><br/>在認知搜尋中，您可以定義和填入索引。 您可以使用[索引子](search-indexer-overview.md)搜耙 Azure 資料來源上的資料，或將任何符合索引的 JSON 文件 推送至您的搜尋服務。 |
|資料庫搜尋 | 許多資料庫平台包括內建的搜尋經驗。 SQL Server 有[全文檢索搜尋](/sql/relational-databases/search/full-text-search)。 Cosmos DB 與類似技術有可供查詢的索引。 對結合搜尋和儲存體的產品進行評估時，可能不容易決定要使用哪一個。 許多解決方案會同時使用兩者：具有儲存體的 DBMS 和具有特製化搜尋功能的 Azure 認知搜尋。<br/><br/>相較於 DBMS 搜尋，Azure 認知搜尋會儲存異質來源中的內容及提供特製化文字處理功能，例如 [56 種語言](/rest/api/searchservice/language-support)的語言感知文字處理 (詞幹分析、詞形歸併還原、文字形式)。 並也支援拼字錯誤的自動校正、[同義字](/rest/api/searchservice/synonym-map-operations)、[建議](/rest/api/searchservice/suggestions)、[計分控制](/rest/api/searchservice/add-scoring-profiles-to-a-search-index)、[Facet](./search-filters-facets.md) 和[自訂 Token 化](/rest/api/searchservice/custom-analyzers-in-azure-search)。 Azure 認知搜尋中的[全文檢索搜尋引擎](search-lucene-query-architecture.md)的建置基礎為 Apache Lucene，這是資訊擷取的業界標準。 不過，當 Azure 認知搜尋以反向索引的形式保留資料時，不會取代真實的資料儲存體，我們不建議在該容量中使用此形式。 如需詳細資訊，請參閱[此論壇文章](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data)。 <br/><br/>資源使用率是此類別中的另一項轉折點。 編製索引及某些查詢作業通常會耗用大量運算資源。 將搜尋從 DBMS 卸載至雲端中的專用解決方案，可保留用於交易處理的系統資源。 此外，將搜尋外部化可以輕鬆調整級別以符合查詢量。|
|專用的搜尋解決方案 | 假設您已決定使用具有全面功能的專用搜尋，最後就是要比較內部部署解決方案或雲端服務的範圍。 許多搜尋技術會提供編製索引和查詢管線的控制權、存取更豐富的查詢及篩選語法、控制順位和相關性，以及自我引導和智慧型搜尋的功能。 <br/><br/>如果您想要擁有最少額外負荷和維護，還可調整級別的周全方案，雲端服務是正確的選擇。 <br/><br/>在雲端範式內，許多提供者提供可比較的基準功能，不但有全文檢索搜尋、地區搜尋，還能夠處理搜尋輸入中一定程度的語意模糊。 是否最適合通常取決於[特殊化功能](search-features-list.md)，或 API、工具和管理的容易性和整體簡單性。 |

就主要依賴搜尋來擷取資訊和導覽內容的應用程式而言，Azure 認知搜尋在 Azure 上的內容存放區和資料庫處理全文檢索搜尋工作負載時，功能最強大，各個雲端提供者都是如此。 

主要優點包括︰

+ 索引層的 Azure 資料整合 (編目程式)
+ Azure 私人連結整合可支援網際網路外的安全需求
+ 與 AI 處理整合，讓無法搜尋的內容類型可供文字搜尋。
+ 語言和自訂分析，還有分析器提供可靠的全文檢索搜尋，支援 56 種語言
+ [重要功能](search-features-list.md)：豐富的查詢語言、相關性調整、Facet、自動完成、同義字、地理搜尋和結果組合。
+ Azure 調整性、可靠性和世界級的可用性

在我們的客戶中，享有最多種 Azure 認知搜尋功能的客戶包括線上型錄、商務營運程式和文件探索應用程式。

## <a name="watch-this-video"></a>觀看這部影片

在這段 15 分鐘的影片中，方案經理 Luis Cabrera 引進了 Azure 認知搜尋。

>[!VIDEO https://www.youtube.com/embed/kOJU0YZodVk?version=3]