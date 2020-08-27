---
title: 監視查詢
titleSuffix: Azure Cognitive Search
description: 監視查詢計量的效能和輸送量。 收集並分析資源記錄檔中的查詢字串輸入。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: a5589a46a63437fb395db280222f8a9e84775df3
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935070"
---
# <a name="monitor-query-requests-in-azure-cognitive-search"></a>監視 Azure 認知搜尋中的查詢要求

本文說明如何使用計量和資源記錄來測量查詢效能和磁片區。 此外，也會說明當您需要評估搜尋主體的公用程式和效率時，如何收集查詢中所使用的輸入詞彙。

送入計量的歷程記錄資料會保留30天。 若要保留較長的保留期，或要報告運算元據和查詢字串，請務必啟用 [診斷設定](search-monitor-logs.md) ，以指定保存記錄事件和計量的儲存選項。

最大化資料量值完整性的條件包括：

+ 使用「計費服務」 (在「基本」或「標準」層) 建立的服務。 免費服務是由多個訂閱者所共用，而這會將特定數量的變動性導入的負載轉移。

+ 如果可能的話，請使用單一複本和分割區，以建立包含和隔離的環境。 如果您使用多個複本，查詢計量會平均跨多個節點，而這可能會降低結果的精確度。 同樣地，多個資料分割表示資料已分割，而且如果索引編制也正在進行，可能會有一些資料分割可能會有不同的資料。 微調查詢效能時，單一節點和資料分割可提供更穩定的環境來進行測試。

> [!Tip]
> 使用額外的用戶端程式代碼和 Application Insights，您也可以取得點選連結資料，以深入瞭解您的應用程式使用者感興趣的吸引。 如需詳細資訊，請參閱[搜尋流量分析](search-traffic-analytics.md)。

## <a name="query-volume-qps"></a>查詢磁片區 (QPS) 

磁片區是以 **每秒的搜尋查詢數** 來測量 (QPS) ，這是一個內建的計量，可回報為一分鐘內執行之查詢的平均、計數、最小值或最大值。 系統內已修正度量的一分鐘間隔 (TimeGrain = "PT1M" ) 。

查詢的執行通常是以毫秒為單位，因此只會在度量中出現測量為秒的查詢。

| 彙總類型 | 描述 |
|------------------|-------------|
| Average | 在一分鐘內發生查詢執行的平均秒數。|
| Count | 在一分鐘的間隔內發出至記錄的計量數目。 |
| 最大值 | 每秒在一分鐘內註冊的搜尋查詢最高數目。 |
| 最低需求 | 每秒在一分鐘內註冊的搜尋查詢數目下限。  |
| Sum | 分鐘內執行之所有查詢的總和。  |

例如，在一分鐘內，您可能會有如下所示的模式：高負載的一秒，這是 SearchQueriesPerSecond 的最大值，之後是平均負載的58秒，最後一秒只有一個查詢，這是最小值。

另一個範例：如果節點發出100計量，其中每個計量的值為40，則「計數」為100、「總和」為4000、「平均」為「40」和「最大值」為40。

## <a name="query-performance"></a>查詢效能

在整個服務中，查詢效能會以搜尋延遲的形式來測量， (查詢完成) 和節流查詢（因為資源爭用而卸載）的時間。

### <a name="search-latency"></a>搜尋延遲

| 彙總類型 | Latency | 
|------------------|---------|
| Average | 平均查詢持續時間（以毫秒為單位）。 | 
| Count | 在一分鐘的間隔內發出至記錄的計量數目。 |
| 最大值 | 範例中的最長執行查詢。 | 
| 最低需求 | 範例中的最短執行查詢。  | 
| 總計 | 範例中所有查詢的總執行時間， (一分鐘的時間內執行) 。  |

請考慮下列 **搜尋延遲** 計量範例：86查詢已取樣，平均持續時間為23.26 毫秒。 最小值為0表示部分查詢已卸載。 執行時間最長的查詢需要1000毫秒的時間才能完成。 總執行時間為2秒。

![延遲匯總](./media/search-monitor-usage/metrics-latency.png "延遲匯總")

### <a name="throttled-queries"></a>節流查詢

節流查詢指的是卸載的查詢，而不是處理常式。 在大部分的情況下，節流是執行服務的一般部分。  這不一定表示有問題。

當目前處理的要求數目超過可用的資源時，就會發生節流。 當複本離開旋轉或在編制索引期間，您可能會看到節流要求的增加。 查詢和編制索引要求都是由相同的資源集來處理。

服務會根據資源耗用量來決定是否要卸載要求。 在記憶體、CPU 和磁片 IO 上耗用的資源百分比平均是一段時間。 如果此百分比超過閾值，則會對索引的所有要求進行節流處理，直到減少要求量為止。 

根據您的用戶端而定，可透過下列方式指出節流的要求：

+ 服務傳回錯誤「您正在傳送太多要求。 請稍後再試。」 
+ 服務會傳回503錯誤碼，指出服務目前無法使用。 
+ 如果您使用入口網站 (例如，搜尋 Explorer) ，系統會以無訊息方式卸載查詢，然後您必須再次按一下 [搜尋]。

若要確認節流的查詢，請使用 **節流的搜尋查詢** 度量。 您可以在入口網站中探索計量，或建立警示度量，如本文中所述。 針對在取樣間隔內卸載的查詢，使用 *Total* 來取得未執行的查詢百分比。

| 彙總類型 | 節流 |
|------------------|-----------|
| Average | 間隔內捨棄的查詢百分比。 |
| Count | 在一分鐘的間隔內發出至記錄的計量數目。 |
| 最大值 | 間隔內捨棄的查詢百分比。|
| 最低需求 | 間隔內捨棄的查詢百分比。 |
| 總計 | 間隔內捨棄的查詢百分比。 |

針對 **節流的搜尋查詢百分比**、最小值、最大值、平均值和總計，全都具有相同的值：已節流的搜尋查詢百分比，從搜尋查詢總數（以一分鐘為單位）。

在以下螢幕擷取畫面中，第一個數位是 (的計數，或傳送至記錄) 的度量數目。 其他匯總（出現在頂端或停留在計量上方時），包括平均、最大值和總計。 在此範例中，未捨棄任何要求。

![節流的匯總](./media/search-monitor-usage/metrics-throttle.png "節流的匯總")

## <a name="explore-metrics-in-the-portal"></a>在入口網站中探索計量

為了快速查看目前的數位，服務總覽頁面上的 [ **監視** ] 索引標籤會顯示三個計量 (**搜尋延遲**、每 **秒搜尋查詢數 (每個搜尋單位) **、 **節流搜尋查詢百分比**) 超過以小時、天和周為單位的固定間隔，以及選項變更匯總類型。

如需更深入的探索，請從 [ **監視** ] 功能表開啟 [計量瀏覽器]，讓您可以分層、放大和視覺化資料，以探索趨勢或異常。 完成本教學課程，以深入瞭解計量瀏覽器，以瞭解如何 [建立度量圖表](../azure-monitor/learn/tutorial-metrics-explorer.md)。

1. 在 [監視] 區段下，選取 [ **計量** ] 以開啟 [計量瀏覽器]，並將範圍設定為您的搜尋服務。

1. 在 [度量] 下，從下拉式清單中選擇一個，並檢查慣用類型的可用匯總清單。 匯總會定義如何在每個時間間隔內取樣收集到的值。

   ![適用于 QPS 計量的計量瀏覽器](./media/search-monitor-usage/metrics-explorer-qps.png "適用于 QPS 計量的計量瀏覽器")

1. 在右上角設定時間間隔。

1. 選擇視覺效果。 預設值為折線圖。

1. 選擇 [ **加入度量** ] 並選取不同的匯總，以分層其他匯總。

1. 放大折線圖上感興趣的區域。 將滑鼠指標放在區域的開頭，然後按住滑鼠左鍵，拖曳至區域的另一端，再放開按鈕。 圖表將會放大該時間範圍。

## <a name="identify-strings-used-in-queries"></a>識別查詢中使用的字串

當您啟用資源記錄時，系統會在 **AzureDiagnostics** 資料表中捕捉查詢要求。 先決條件是，您必須已經啟用 [資源記錄](search-monitor-logs.md)，並指定 log analytics 工作區或其他儲存體選項。

1. 在 [監視] 區段下，選取 [ **記錄** ]，以在 Log Analytics 中開啟空白的查詢視窗。

1. 執行下列運算式來搜尋查詢。搜尋作業會傳回表格式結果集，其中包含作業名稱、查詢字串、查詢的索引，以及找到的檔數目。 最後兩個語句會透過範例索引來排除包含空白或未指定搜尋的查詢字串，以減少結果中的雜訊。

   ```
   AzureDiagnostics
   | project OperationName, Query_s, IndexName_s, Documents_d
   | where OperationName == "Query.Search"
   | where Query_s != "?api-version=2020-06-30&search=*"
   | where IndexName_s != "realestate-us-sample-index"
   ```

1. （選擇性）在 *Query_s* 上設定資料行篩選，以搜尋特定的語法或字串。 例如，您可以篩選 [ *等於*] `?api-version=2020-06-30&search=*&%24filter=HotelName`) 。

   ![記錄的查詢字串](./media/search-monitor-usage/log-query-strings.png "記錄的查詢字串")

雖然這項技術適用于臨機操作調查，但建立報表可讓您將查詢字串合併並呈現在版面配置中，以更採用遭利用的方式進行分析。

## <a name="identify-long-running-queries"></a>識別長時間執行的查詢

新增 [持續時間] 資料行，以取得所有查詢的數目，而不只是視為度量的查詢數目。 排序此資料會顯示哪些查詢需要最長的時間才能完成。

1. 在 [監視] 區段下，選取 [ **記錄** ] 以查詢記錄資訊。

1. 執行下列查詢，以傳回依持續時間（以毫秒為單位）排序的查詢。 執行時間最長的查詢位於最上方。

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search"
   | sort by DurationMs
   ```

   ![依持續時間排序查詢](./media/search-monitor-usage/azurediagnostics-table-sortby-duration.png "依持續時間排序查詢")

## <a name="create-a-metric-alert"></a>建立計量警示

計量警示會建立閾值，您將會收到通知或觸發您事先定義的矯正措施。 

針對搜尋服務，通常會針對搜尋延遲和節流查詢建立計量警示。 如果您知道卸載查詢的時間，您可以尋找降低負載或增加容量的補救方式。 例如，如果在編制索引期間增加節流的查詢，您可以將它延後，直到查詢活動消退為止。

當推送特定複本分割區設定的限制時， (QPS) 設定查詢磁片區閾值的警示也很有説明。

1. 在 [監視] 區段下，選取 [ **警示** ]，然後按一下 [ **+ 新增警示規則**]。 請確定已選取您的搜尋服務作為資源。

1. 在 [條件] 底下，按一下 [ **新增**]。

1. 設定信號邏輯。 在 [信號類型] 中，選擇 [ **計量** ]，然後選取信號。

1. 選取信號之後，您可以使用圖表來視覺化歷程記錄資料，以取得如何繼續設定條件的明智決策。

1. 接下來，向下滾動至警示邏輯。 針對概念證明，您可以針對測試用途指定按人為低的值。

   ![警示邏輯](./media/search-monitor-usage/alert-logic-qps.png "警示邏輯")

1. 接下來，指定或建立動作群組。 這是符合臨界值時要叫用的回應。 它可能是推播通知或自動化回應。

1. 最後，指定警示詳細資料。 命名和描述警示、指派嚴重性值，並指定是否要在啟用或停用狀態中建立規則。

   ![警示詳細資料](./media/search-monitor-usage/alert-details.png "警示詳細資料")

如果您指定了電子郵件通知，您會收到 "Microsoft Azure" 的電子郵件，其主旨列為「Azure：啟用嚴重性：3」 `<your rule name>` 。

<!-- ## Report query data

Power BI is an analytical reporting tool useful for visualizing data, including log information. If you are collecting data in Blob storage, a Power BI template makes it easy to spot anomalies or trends. Use this link to download the template. -->

## <a name="next-steps"></a>後續步驟

如果您尚未這麼做，請參閱搜尋服務監視的基本概念，以瞭解全面的監督功能。

> [!div class="nextstepaction"]
> [在 Azure 認知搜尋中監視作業和活動](search-monitor-usage.md)