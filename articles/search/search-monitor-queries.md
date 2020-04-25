---
title: 監視查詢
titleSuffix: Azure Cognitive Search
description: 監視效能和輸送量的查詢計量。 收集並分析資源記錄中的查詢字串輸入。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: da7a47bf61453c30f5c735b1282ae93d2442598c
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82127686"
---
# <a name="monitor-query-requests-in-azure-cognitive-search"></a>監視 Azure 認知搜尋中的查詢要求

本文說明如何使用計量和資源記錄來測量查詢效能和磁片區。 它也會說明當您需要評估搜尋主體的公用程式和有效性時，如何收集查詢所需的輸入詞彙。

饋送至計量的歷程記錄資料會保留30天。 針對較長的保留期，或要報告運算元據和查詢字串，請務必啟用[診斷設定](search-monitor-logs.md)，以指定保存記錄事件和計量的儲存選項。

將資料量測的完整性最大化的條件包括：

+ 使用可計費服務（在基本或標準層建立的服務）。 「免費」服務是由多個「訂閱者」共用，其中引進了特定數量的變動性作為「負載轉移」。

+ 如果可能，請使用單一複本和分割區來建立包含和隔離的環境。 如果您使用多個複本，則查詢計量會平均分佈在多個節點上，因此可以降低結果的精確度。 同樣地，多個分割區表示資料會被劃分，而如果編制索引也在進行中，可能會有部分資料分割有不同的資料。 微調查詢效能時，單一節點和資料分割會提供更穩定的環境來進行測試。

> [!Tip]
> 透過額外的用戶端程式代碼和 Application Insights，您也可以取得點選連結資料，以深入瞭解應用程式使用者的相關吸引。 如需詳細資訊，請參閱[搜尋流量分析](search-traffic-analytics.md)。

## <a name="query-volume-qps"></a>查詢磁片區（QPS）

磁片區是以**每秒搜尋查詢**（QPS）來測量，這是一個內建的計量，可以報告為在一分鐘時間範圍內執行之查詢的平均值、計數、最小值或最大值。 系統內已修正計量的一分鐘間隔（TimeGrain = "PT1M"）。

查詢通常是以毫秒執行，因此只有測量為秒的查詢才會出現在計量中。

| 彙總類型 | 說明 |
|------------------|-------------|
| Average | 在一分鐘內發生查詢執行的平均秒數。|
| Count | 一分鐘內發出至記錄的計量數目。 |
| 最大值 | 一分鐘內註冊的每秒搜尋查詢最高數目。 |
| 最小值 | 一分鐘內註冊的每秒搜尋查詢最小數目。  |
| Sum | 分鐘內執行之所有查詢的總和。  |

例如，在一分鐘內，您可能會有如下的模式：一秒的高負載，這是最大值的 SearchQueriesPerSecond，後面加上58秒的平均負載，最後一秒只有一個查詢（這是最小值）。

另一個範例：如果節點發出100計量，其中每個度量的值為40，則「計數」為100，「總和」為4000，「平均」為40，而「最大值」為40。

## <a name="query-performance"></a>查詢效能

整個服務的查詢效能會測量為搜尋延遲（查詢需要多久才能完成）和因資源爭用而捨棄的節流查詢。

### <a name="search-latency"></a>搜尋延遲

| 彙總類型 | Latency | 
|------------------|---------|
| Average | 平均查詢持續時間（以毫秒為單位）。 | 
| Count | 一分鐘內發出至記錄的計量數目。 |
| 最大值 | 範例中執行時間最長的查詢。 | 
| 最小值 | 範例中最短的執行查詢。  | 
| 總計 | 範例中所有查詢的總執行時間，在間隔內執行（一分鐘）。  |

請考慮下列**搜尋延遲**計量範例：已取樣86查詢，平均持續時間為23.26 毫秒。 最小值為0表示已捨棄一些查詢。 執行時間最長的查詢需要1000毫秒才能完成。 總執行時間為2秒。

![延遲匯總](./media/search-monitor-usage/metrics-latency.png "延遲匯總")

### <a name="throttled-queries"></a>節流查詢

節流查詢指的是卸載的查詢，而不是進程。 在大部分情況下，節流是執行服務的一般部分。  不一定表示有問題。

當目前處理的要求數目超過可用的資源時，就會進行節流。 當複本離開輪替或在編制索引期間，您可能會看到節流的要求增加。 查詢和索引編制要求都是由相同的資源集來處理。

服務會決定是否要根據資源耗用量來卸載要求。 在記憶體、CPU 和磁片 IO 之間耗用的資源百分比，是一段時間的平均值。 如果此百分比超過閾值，則會對索引的所有要求進行節流，直到減少要求量為止。 

根據您的用戶端，可透過下列方式來表示節流的要求：

+ 服務傳回「您傳送太多要求」錯誤。 請稍後再試。」 
+ 服務傳回503錯誤碼，表示服務目前無法使用。 
+ 如果您使用入口網站（例如搜尋瀏覽器），則會以無訊息方式卸載查詢，而您必須再次按一下 [搜尋]。

若要確認已節流的查詢，請使用**節流的搜尋查詢**度量。 您可以在入口網站中流覽計量，或建立警示度量，如本文中所述。 若為在取樣間隔內卸載的查詢，請使用*Total*來取得未執行查詢的百分比。

| 彙總類型 | 節流 |
|------------------|-----------|
| Average | 在間隔內捨棄的查詢百分比。 |
| Count | 一分鐘內發出至記錄的計量數目。 |
| 最大值 | 在間隔內捨棄的查詢百分比。|
| 最小值 | 在間隔內捨棄的查詢百分比。 |
| 總計 | 在間隔內捨棄的查詢百分比。 |

對於**節流的搜尋查詢百分比**、最小值、最大值、平均值和總計，全都具有相同的值 .. 已節流的搜尋查詢百分比，從搜尋查詢的總數（以一分鐘為單位）。

在下列螢幕擷取畫面中，第一個數位是計數（或傳送至記錄的計量數目）。 其他匯總會出現在上方，或當滑鼠停留在度量上時，包括平均值、最大值和總計。 在此範例中，未捨棄任何要求。

![節流的匯總](./media/search-monitor-usage/metrics-throttle.png "節流的匯總")

## <a name="explore-metrics-in-the-portal"></a>探索入口網站中的計量

若要快速查看目前的數位，[服務總覽] 頁面上的 [**監視**] 索引標籤會顯示三個計量（**搜尋延遲**、**每秒的搜尋查詢數（每個搜尋單位）**、**節流的搜尋查詢百分比**）（以小時、天和周為單位），以及變更匯總類型的選項。

如需更深入的探索，請從 [**監視**] 功能表中開啟 [計量瀏覽器]，讓您可以分層、放大和視覺化資料，以探索趨勢或異常狀況。 若要深入瞭解計量瀏覽器，請完成[建立計量圖表](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-metrics-explorer)的本教學課程。

1. 在 [監視] 區段下，選取 [**計量**] 以開啟 [計量瀏覽器]，並將範圍設定為您的搜尋服務。

1. 在 [度量] 底下，從下拉式清單中選擇一個，並查看慣用類型的可用匯總清單。 匯總會定義如何在每個時間間隔內取樣收集的值。

   ![QPS 度量的計量瀏覽器](./media/search-monitor-usage/metrics-explorer-qps.png "QPS 度量的計量瀏覽器")

1. 在右上角設定時間間隔。

1. 選擇視覺效果。 預設值為折線圖。

1. 選擇 [**新增度量**] 並選取不同的匯總，以分層額外的匯總。

1. 放大折線圖上的相關區域。 將滑鼠指標放在區域的開頭，然後按住滑鼠左鍵，拖曳至區域的另一端，再放開按鈕。 圖表將會放大該時間範圍。

## <a name="identify-strings-used-in-queries"></a>識別查詢中使用的字串

當您啟用資源記錄時，系統會在**AzureDiagnostics**資料表中捕捉查詢要求。 必要條件是您必須已啟用[資源記錄](search-monitor-logs.md)，並指定 log analytics 工作區或其他儲存體選項。

1. 在 [監視] 區段下，選取 [**記錄**] 以在 Log Analytics 中開啟空白查詢視窗。

1. 執行下列運算式來搜尋查詢。搜尋作業，傳回包含作業名稱、查詢字串、查詢的索引，以及找到的檔數目的表格式結果集。 最後兩個語句會透過範例索引排除包含空白或未指定搜尋的查詢字串，以減少結果中的雜訊。

   ```
   AzureDiagnostics
   | project OperationName, Query_s, IndexName_s, Documents_d
   | where OperationName == "Query.Search"
   | where Query_s != "?api-version=2019-05-06&search=*"
   | where IndexName_s != "realestate-us-sample-index"
   ```

1. （選擇性）在*Query_s*上設定資料行篩選，以便在特定的語法或字串上進行搜尋。 例如，您可以篩選 [*等於* `?api-version=2019-05-06&search=*&%24filter=HotelName`]）。

   ![記錄的查詢字串](./media/search-monitor-usage/log-query-strings.png "記錄的查詢字串")

雖然這項技術適用于臨機操作的調查，但建立報表可讓您將查詢字串合併和呈現，以更採用遭利用的方式進行分析。

## <a name="identify-long-running-queries"></a>識別長時間執行的查詢

新增 [持續時間] 資料行，以取得所有查詢的數目，而不只是當做計量挑選的查詢。 排序此資料會顯示哪些查詢需要最長的時間才能完成。

1. 在 [監視] 區段下，選取 [**記錄**] 以查詢記錄資訊。

1. 執行下列查詢來傳回查詢，並依持續時間（以毫秒為單位）排序。 執行時間最長的查詢位於最上方。

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search"
   | sort by DurationMs
   ```

   ![依持續時間排序查詢](./media/search-monitor-usage/azurediagnostics-table-sortby-duration.png "依持續時間排序查詢")

## <a name="create-a-metric-alert"></a>建立計量警示

計量警示會建立一個臨界值，您會在此收到通知或觸發您預先定義的矯正措施。 

針對搜尋服務，通常會針對搜尋延遲和節流查詢建立計量警示。 如果您知道何時捨棄查詢，您可以尋找降低負載或增加容量的補救方式。 例如，如果在編制索引期間增加了節流的查詢，您可以將其延後到查詢活動 contosoconcerthall。

推送特定複本分割區設定的限制時，為查詢磁片區閾值（QPS）設定警示也很有説明。

1. 在 [監視] 區段下，選取 [**警示**]，然後按一下 [ **+ 新增警示規則**]。 請確定已選取您的搜尋服務作為資源。

1. 按一下 [條件] 底下的 [**新增**]。

1. 設定信號邏輯。 針對 [信號類型]，選擇 [**計量**]，然後選取信號。

1. 選取信號之後，您可以使用圖表將歷程記錄資料視覺化，以取得如何繼續設定條件的明智決策。

1. 接下來，向下流覽至 [警示邏輯]。 對於概念證明，您可以針對測試目的指定人為低的值。

   ![警示邏輯](./media/search-monitor-usage/alert-logic-qps.png "警示邏輯")

1. 接下來，指定或建立動作群組。 這是當達到臨界值時所要叫用的回應。 這可能是推播通知或自動化回應。

1. 最後，指定警示詳細資料。 命名並描述警示、指派嚴重性值，並指定是否要在啟用或停用狀態中建立規則。

   ![警示詳細資料](./media/search-monitor-usage/alert-details.png "警示詳細資料")

如果您指定了電子郵件通知，您將會收到來自「Microsoft Azure」的電子郵件，主旨為「Azure：已啟用嚴重性`<your rule name>`：3」。

<!-- ## Report query data

Power BI is an analytical reporting tool useful for visualizing data, including log information. If you are collecting data in Blob storage, a Power BI template makes it easy to spot anomalies or trends. Use this link to download the template. -->

## <a name="next-steps"></a>後續步驟

如果您尚未這麼做，請參閱搜尋服務監視的基本概念，以瞭解完整範圍的監督功能。

> [!div class="nextstepaction"]
> [監視 Azure 認知搜尋中的作業和活動](search-monitor-usage.md)