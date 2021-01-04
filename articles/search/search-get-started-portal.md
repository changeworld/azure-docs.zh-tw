---
title: 在 Azure 入口網站中建立搜尋索引
titleSuffix: Azure Cognitive Search
description: 在本 Azure 入口網站快速入門中，使用「匯入資料」精靈，在 Azure 認知搜尋中建立、載入及查詢您的第一個搜尋索引。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 12/12/2020
ms.openlocfilehash: 1e9d63c88cf0cd6f65db99b2bc878797770d53cd
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368625"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-the-azure-portal"></a>快速入門：在 Azure 入口網站中建立 Azure 認知搜尋索引

使用 [匯入資料] 精靈及包含虛構飯店資料的內建範例資料來源，建立您的第一個索引。 精靈會引導您建立搜尋索引 (hotels-sample-index)，讓您在幾分鐘內撰寫有趣的查詢。 

雖然您不會在此快速入門中使用這些選項，但精靈也包含 AI 擴充頁面，因此可以影像檔案與非結構化文字中擷取文字與結構。 如需包含 AI 擴充的類似逐步解說，請參閱[快速入門：建立認知技能](cognitive-search-quickstart-blob.md)。

## <a name="prerequisites"></a>Prerequisites

開始之前，您必須具備下列條件：

+ 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/)。

+ Azure 認知搜尋服務。 在目前的訂閱下，[建立服務](search-create-service-portal.md) 或 [尋找現有的服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 您可以使用本快速入門的免費服務。 

### <a name="check-for-space"></a>檢查空間

許多客戶開始使用此免費服務。 此版本的限制為三個索引、三個資料來源，以及三個索引子。 開始之前，請先確定您有空間可容納額外的項目。 本教學課程會建立各一個物件。

服務儀表板上的區段會顯示您已有多少個索引、索引子和資料來源。 

:::image type="content" source="media/search-get-started-portal/tiles-indexers-datasources.png" alt-text="索引、索引子和資料來源的清單":::

## <a name="create-an-index-and-load-data"></a><a name="create-index"></a> 建立索引和載入資料

搜尋查詢會逐一查看[索引](search-what-is-an-index.md)，其中包含會將特定搜尋行為最佳化的可搜尋資料、中繼資料及其他建構。

在此教學課程中，我們使用可以透過 [**匯入資料精靈**](search-import-data-portal.md)使用 [索引子](search-indexer-overview.md)搜耙的內建範例資料集。 索引子是來源特定的編目程式，可以從支援的 Azure 資料來源讀取中繼資料和內容。 使用索引子時通常會採用程式設計方式，但在入口網站中，您也可以存取透過 **匯入資料** 精靈存取索引子。 

### <a name="step-1---start-the-import-data-wizard-and-create-a-data-source"></a>步驟 1 - 啟動匯入資料精靈並建立資料來源

1. 使用您的 Azure 帳戶登入 [Azure 入口網站](https://portal.azure.com/) 。

1. [尋找您的搜尋服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/)，然後在 [概觀] 頁面上，按一下命令列上的 [匯入資料]，以建立及填入搜尋索引。

   :::image type="content" source="media/search-get-started-portal/import-data-cmd.png" alt-text="匯入資料命令":::

1. 在精靈中，按一下 [連線到您的資料] > [範例] > [hotels-sample]。 此資料來源是內建的。 如果您建立自己的資料來源，則必須指定名稱、類型和連線資訊。 一旦建立，就會變成可在其他匯入作業中重複使用的「現有資料來源」。

   :::image type="content" source="media/search-get-started-portal/import-datasource-sample.png" alt-text="選取範例資料集":::

1. 繼續進行下一頁。

### <a name="step-2---skip-the-enrich-content-page"></a>步驟 2 - 略過 [擴充內容] 頁面

此精靈支援建立可將認知服務 AI 演算法併入索引編製中的 [AI 擴充管線](cognitive-search-concept-intro.md)。 

我們將暫時略過此步驟，而直接進行 **自訂目標索引**。

   :::image type="content" source="media/search-get-started-portal/skip-cog-skill-step.png" alt-text="跳過認知技術步驟":::

> [!TIP]
> 您可以透過[快速入門](cognitive-search-quickstart-blob.md)或[教學課程](cognitive-search-tutorial-blob.md)，逐步執行 AI 索引編製範例。

### <a name="step-3---configure-index"></a>步驟 3 - 設定索引

索引建立通常是以程式碼為基礎的作業，完成後才會載入資料。 但如本教學課程所指出的，精靈可以為它可搜耙的任何資料來源產生基本索引。 索引至少需要有名稱和欄位的集合；其中一個欄位應標示為文件索引鍵，以便唯一識別每份文件。 此外，如果您需要自動完成或建議查詢，您可以指定語言分析器或建議工具。

欄位具有資料類型和屬性。 上方的核取方塊為「索引屬性」，可控制欄位的使用方式。

*  表示它會出現在搜尋結果清單中。 您可以清除此核取方塊，將個別欄位標示為關閉搜尋結果的限制，例如，僅使用於篩選運算式的欄位。
* **索引鍵** 是唯一的文件識別碼。 它一律是字串，而且是必要的。
* [可篩選]、[可排序] 和 [可面向化] 可決定欄位是要用於篩選、排序還是多面向導覽結構。
*  表示欄位包含在全文檢索搜尋中。 字串可以搜尋。 數字欄位和布林值欄位通常會標示為不可搜尋。

儲存體需求不會因您的選擇而改變。 例如，如果您對多個欄位設定 [可擷取] 屬性，儲存體需求也不會因此而增加。

根據預設，精靈會掃描資料來源中的唯一識別碼做為索引鍵欄位的基礎。 「字串」具有 **可擷取** 和 **可搜尋** 的特性。 「整數」具有 **可擷取**、**可篩選**、**可排序** 和 **可 Fact 處理** 的特性。

1. 接受預設值。

   如果您使用現有的旅館資料來源再次執行精靈，系統將不會使用預設屬性來設定索引。 您必須以手動方式對未來的匯入選取屬性。 

   :::image type="content" source="media/search-get-started-portal/hotelsindex.png" alt-text="產生的旅館索引":::

2. 繼續進行下一頁。


### <a name="step-4---configure-indexer"></a>步驟 4 - 設定索引子

同樣在 [匯入資料] 精靈中，按一下 [索引子] > [名稱]，並且鍵入索引子的名稱。

此物件定義可執行的程序。 您就可以將其放入週期性排程，但現在請先使用預設選項，立即執行索引子一次。

按一下 [提交] 以建立並同時執行索引子。

  :::image type="content" source="media/search-get-started-portal/hotels-indexer.png" alt-text="旅館索引子":::

## <a name="monitor-progress"></a>監視進度

精靈應會將您導向至索引子清單，讓您可以監視進度。 若要自行瀏覽，請移至 [概觀] 頁面，然後按一下 [索引子]。

入口網站更新頁面可能需要幾分鐘的時間，但您應該會在清單中看到新建立的索引子 (其狀態顯示為「進行中」或成功)，以及已編製索引的文件數目。

   :::image type="content" source="media/search-get-started-portal/indexers-inprogress.png" alt-text="索引子進度訊息":::

## <a name="view-the-index"></a>檢視索引

主要服務頁面會提供在 Azure 認知搜尋服務中建立之資源的連結。  若要檢視您剛才建立的索引，請從連結的清單中按一下 [索引]。 

等候入口網站頁面重新整理。 幾分鐘後，您應該會看到具有文件計數和儲存體大小的索引。

   :::image type="content" source="media/search-get-started-portal/indexes-list.png" alt-text="服務儀表板上的索引清單":::

在此清單中，您可以按一下剛剛建立的 *hotels-sample* 索引、檢視索引結構描述， 以及選擇性地新增欄位。 

[欄位] 索引標籤會顯示索引結構描述。 捲動至清單底部可輸入新欄位。 在大部分情況下，您無法變更現有的欄位。 現有的欄位在 Azure 認知搜尋中具有實體表示法，因此不可修改，甚至是在程式碼中也不可修改。 若要對現有欄位進行重大變更，請建立新索引並，捨棄原始欄位。

   :::image type="content" source="media/search-get-started-portal/sample-index-def.png" alt-text="範例索引定義":::

您可以隨時新增其他建構，例如評分設定檔和 CORS 選項。

若要清楚地了解您在索引設計期間可以編輯和無法編輯的項目，請花點時間檢視索引定義選項。 灰色的選項表示無法修改或刪除選項值。 

## <a name="query-using-search-explorer"></a><a name="query-index"></a> 使用搜尋總管進行查詢

接下來，您現在應該有搜尋索引，準備好要使用內建的 [[搜尋總管]](search-explorer.md) 查詢頁面進行查詢。 它會提供搜尋方塊，讓您能夠測試任意的查詢字串。

**搜尋總管** 只能用來處理 [REST API 要求](/rest/api/searchservice/search-documents)，但可接受 [簡單查詢語法](/rest/api/searchservice/simple-query-syntax-in-azure-search)和 [完整 Lucene 查詢剖析器](/rest/api/searchservice/lucene-query-syntax-in-azure-search)的語法，以及 [搜尋文件 REST API](/rest/api/searchservice/search-documents#bkmk_examples) 作業中可用的所有搜尋參數。

> [!TIP]
> 下列步驟會在 [Azure 認知搜尋概觀影片](https://channel9.msdn.com/Events/Connect/2016/138)的 6 分 08 秒處示範。
>

1. 按一下命令列上的 [搜尋總管]  。

   :::image type="content" source="media/search-get-started-portal/search-explorer-cmd.png" alt-text="搜尋總管命令":::

2. 從 [索引] 下拉式清單中，選擇 [hotels-sample-index]。 按一下[API 版本] 下拉式清單，以查看可用的 REST API。 針對下面的查詢，請使用公開推出版本 (2020-06-30)。

   :::image type="content" source="media/search-get-started-portal/search-explorer-changeindex.png" alt-text="索引和 API 命令":::

3. 在搜尋列中貼入下列查詢字串，然後按一下 [搜尋]。

   :::image type="content" source="media/search-get-started-portal/search-explorer-query-string-example.png" alt-text="查詢字串和搜尋按鈕":::

## <a name="example-queries"></a>查詢範例

您可以輸入字詞和片語，如同您在使用 Bing、Google 搜尋或完整指定的查詢運算式時所做的。 結果會以詳細 JSON 文件的形式傳回。

### <a name="simple-query-with-top-n-results"></a>含有前 N 個結果的簡單查詢

#### <a name="example-string-query-searchspa"></a>範例 (字串查詢)：`search=spa`

* **search** 參數可用來輸入全文檢索搜尋的關鍵字搜尋，在此案例中，會傳回在文件的任何可搜尋欄位中包含 spa 的旅館資料。

* **搜尋總管** 會以 JSON 傳回結果，這是詳細資訊，而如果文件的結構很密集則難以閱讀。 這是刻意設計的；整份文件的可見性對開發目的而言很重要，特別是在測試期間。 為了獲得更好的使用者體驗，您必須撰寫程式碼，以[處理搜尋結果](search-pagination-page-layout.md)來找出重要的元素。

* 文件是由索引中標記為「可擷取」的所有欄位組成。 若要在入口網站中檢視索引屬性，請按一下 [索引] 清單中的 hotels-sample。

#### <a name="example-parameterized-query-searchspacounttruetop10"></a>範例 (參數化查詢)：`search=spa&$count=true&$top=10`

* **&** 符號用來附加搜尋參數 (可依任何順序指定)。

* **$count=true** 參數會傳回所有已傳回文件的總計數。 這個值會出現在搜尋結果的頂端附近。 您可以藉由監視 **$count=true** 所報告的變更來驗證篩選查詢。 較小的計數表示正在處理您的篩選條件。

* **$top=10** 會傳回所有文件中最高順位的 10 份文件。 根據預設，Azure 認知搜尋會傳回前 50 個最相符項目。 您可以透過 **$top** 來增加或減少數量。

### <a name="filter-the-query"></a><a name="filter-query"></a> 篩選查詢

當您附加 **$filter** 參數時，篩選器會包含在搜尋要求中。 

#### <a name="example-filtered-searchbeachfilterrating-gt-4"></a>範例 (已篩選)：`search=beach&$filter=Rating gt 4`

* **$filter** 參數會傳回符合您提供之準則的結果。 在此案例中為大於 4 的評等。

* 篩選語法是 OData 建構。 如需詳細資訊，請參閱[篩選 OData 語法](/rest/api/searchservice/odata-expression-syntax-for-azure-search)。

### <a name="facet-the-query"></a><a name="facet-query"></a> 將查詢面向化

Facet 篩選器會包含在搜尋要求中。 您可以使用 facet 參數，傳回符合您所提供之 facet 值的文件彙總計數。

#### <a name="example-faceted-with-scope-reduction-searchfacetcategorytop2"></a>範例 (多面向且範圍已縮減)：`search=*&facet=Category&$top=2`

* **search=** _ 是空的搜尋。 空的搜尋會搜尋所有一切。 提交空查詢的其中一個原因是為了篩選一組完整的文件或使其面向化。 例如，您希望多面向導覽結構包含索引中的所有旅館。
**facet** 會傳回您可傳遞至 UI 控制項的導覽結構。 它會傳回一些類別和一個計數。 在此案例中，類別以直接命名為 *Category* 的欄位為基礎。 Azure 認知搜尋中沒有彙總功能，但您可以透過 `facet` 模擬彙總，以提供各類別中的文件計數。

* **$top=2** 會傳回兩份文件，這表示您可以使用 `top` 來減少或增加結果。

#### <a name="example-facet-on-numeric-values-searchspafacetrating"></a>範例 (數值的 Facet)：`search=spa&facet=Rating`

* 此查詢是評等的 Facet，以 spa 的文字搜尋為基礎。 Rating 一詞可指定為一個 Facet，因為該欄位標示為可在索引中擷取、篩選及面向化 (1 到 5 的數值)，適合用於將清單分類。

* 只有可篩選的欄位可以面向化。 只有可擷取的欄位可以在結果中傳回。

* [評等] 欄位是雙精確度浮點數，且分組會採用精確值。 如需依間隔分組 (例如，「3 星級評等」、「4 星級評等」) 的詳細資訊，請參閱[如何在 Azure 認知搜尋中實作多面向導覽](./search-faceted-navigation.md#filter-based-on-a-range)。


### <a name="highlight-search-results"></a><a name="highlight-query"></a> 醒目提示搜尋結果

命中項目醒目提示是指關鍵字文字比對的格式設定，前提是在特定欄位中找到相符項目。 如果您的搜尋字詞深藏在描述中，您可以新增命中項目醒目提示功能，更輕鬆地發現所搜尋的字詞。

#### <a name="example-highlighter-searchbeachhighlightdescription"></a>範例 (螢光筆)：`search=beach&highlight=Description`

* 在此範例中，格式化字組 beach 可在描述欄位中更容易找出。

#### <a name="example-linguistic-analysis-searchbeacheshighlightdescription"></a>範例 (語言分析)：`search=beaches&highlight=Description`

* 全文檢索搜尋可辨識字組形式的基本變化。 在此案例中，針對在可搜尋的欄位中包含 "beach" 一字的旅館，搜尋結果會包含 "beach" 的醒目提示文字，以回應 "beaches" 關鍵字搜尋。 因為語言分析的緣故，結果中可能會出現同一個字的不同形式。 

* Azure 認知搜尋支援 Microsoft 和 Lucene 所提供的 56 個分析器。 Azure 認知搜尋依預設會使用標準 Lucene 分析器。

### <a name="try-fuzzy-search"></a><a name="fuzzy-search"></a> 嘗試模糊搜尋

根據預設，拼錯的查詢字詞 (例如將 "Seattle" 誤拼為 *seatle*) 無法在一般搜尋中傳回相符項目。 下列範例不會傳回任何結果。

#### <a name="example-misspelled-term-unhandled-searchseatle"></a>範例 (拼錯的字詞，未處理)：`search=seatle`

若要處理拼字錯誤，您可以使用模糊搜尋。 當您使用完整的 Lucene 查詢語法時，會在下列兩種情況下啟用模糊搜尋：在查詢中設定 **queryType=full** 時，以及將 **~** 附加至搜尋字串時。

#### <a name="example-misspelled-term-handled-searchseatlequerytypefull"></a>範例 (拼字錯誤的字詞，已處理)：`search=seatle~&queryType=full`

此範例現在會傳回文件，當中包含 "Seattle" 的相符項目。

若未指定 **queryType**，則會使用預設的簡單查詢剖析器。 簡單查詢剖析器的速度較快，但如果您需要模糊搜尋、規則運算式、鄰近搜尋或其他進階的查詢類型，則需要完整的語法。

模糊搜尋和萬用字元搜尋會影響搜尋輸出。 不會以這些查詢格式執行語言分析。 在使用模糊及萬用字元搜尋之前，請檢閱[全文檢索搜尋如何在 Azure 認知搜尋中運作](search-lucene-query-architecture.md#stage-2-lexical-analysis)，並尋找語彙分析例外狀況的相關章節。

如需完整查詢剖析器所啟用查詢案例的詳細資訊，請參閱 [Azure 認知搜尋中的 Lucene 查詢語法](/rest/api/searchservice/lucene-query-syntax-in-azure-search)。

### <a name="try-geospatial-search"></a><a name="geo-search"></a> 嘗試地理空間搜尋

地理空間搜尋是透過含有座標之欄位上的 [edm.GeographyPoint 資料類型](/rest/api/searchservice/supported-data-types)提供支援。 地理搜尋是在[篩選 OData 語法](/rest/api/searchservice/odata-expression-syntax-for-azure-search)中指定的一種篩選器。

#### <a name="example-geo-coordinate-filters-searchcounttruefiltergeodistancelocationgeographypoint-12212-4767-le-5"></a>範例 (地理座標篩選)：`search=*&$count=true&$filter=geo.distance(Location,geography'POINT(-122.12 47.67)') le 5`

範例查詢會篩選所有結果中的位置資料，而結果是小於距離指定點 (以緯度和經度座標指定) 5 公里。 藉由新增 **$count**，您可以看到當您變更距離或座標時傳回多少筆結果。

如果搜尋應用程式有「尋找附近地點」功能或使用地圖導航功能，地理空間搜尋便很實用。 不過，並不是全文檢索搜尋。 如果使用者有需要依照名稱搜尋城市或國家/地區，則除了座標以外，請新增包含城市或國家/地區名稱的欄位。

## <a name="takeaways"></a>重要心得

本教學課程提供了使用 Azure 入口網站執行 Azure 認知搜尋的快速簡介。

您已了解如何使用 **匯入資料** 精靈建立搜尋索引。 您已了解[索引子](search-indexer-overview.md)，以及索引設計的基本工作流程，包括[對已發佈索引支援的修改](/rest/api/searchservice/update-index)。

藉由在 Azure 入口網站中使用 **搜尋總管**，您已透過實際操作的範例了解某些基本查詢語法，這些範例示範了篩選、搜尋結果醒目提示、模糊搜尋和地理搜尋等主要功能。

您也已了解如何在入口網站中尋找索引、索引子和資料來源。 日後若有任何新的資料來源，您就可以使用入口網站以最省力的方式快速檢查其定義或欄位集合。

## <a name="clean-up-resources"></a>清除資源

使用您自己的訂用帳戶時，在專案結束後確認您是否還需要您建立的資源，是很好的做法。 資源若繼續執行，您將必須付費。 您可以個別刪除資源，或刪除資源群組以刪除整組資源。

您可以使用左導覽窗格中的 [所有資源]  或 [資源群組]  連結，在入口網站中尋找和管理資源。

如果您使用免費服務，請記住您會有三個索引、索引子和資料來源的限制。 您可以在入口網站中刪除個別項目，以避免超出限制。 

## <a name="next-steps"></a>後續步驟

使用入口網站精靈，產生可在瀏覽器中執行的立即可用 Web 應用程式。 您可以在剛才建立的小型索引上試用此精靈，也可以使用其中一個內建的資料集範例，來獲得更豐富的搜尋體驗。

> [!div class="nextstepaction"]
> [在入口網站中建立示範應用程式](search-create-app-portal.md)