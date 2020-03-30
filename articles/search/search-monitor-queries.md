---
title: 監視查詢
titleSuffix: Azure Cognitive Search
description: 監視性能和輸送量的查詢指標。 收集和分析診斷日誌中的查詢字串輸入。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: a3a313ef9cd74ba901f5a6a2d82a18e3c21145dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462511"
---
# <a name="monitor-query-requests-in-azure-cognitive-search"></a>監視 Azure 認知搜索中的查詢請求

本文介紹如何使用指標和診斷日誌記錄來衡量查詢性能和卷。 它還解釋了如何收集查詢中使用的輸入詞 - 當您需要評估搜索語料庫的效用和有效性時，必要的資訊。

饋入指標的歷史資料將保留 30 天。 要延長保留時間，或報告運算元據和查詢字串，請確保啟用[一個診斷設置](search-monitor-logs.md)，該設置指定用於持久化記錄的事件和指標的存儲選項。

最大化資料測量完整性的條件包括：

+ 使用計費服務（在基本層或標準層創建的服務）。 免費服務由多個訂閱者共用，隨著負載的轉移，它會帶來一定程度的波動。

+ 如果可能，請使用單個副本和分區來創建包含的隔離環境。 如果使用多個副本，查詢指標將跨多個節點求平均值，這可能會降低結果的精度。 同樣，多個分區意味著資料被分割，如果索引也在進行，則某些分區可能具有不同的資料。 調整查詢性能時，單個節點和分區為測試提供了更穩定的環境。

> [!Tip]
> 借助其他用戶端代碼和應用程式見解，您還可以捕獲點擊率資料，從而更深入地瞭解吸引應用程式使用者興趣的內容。 如需詳細資訊，請參閱[搜尋流量分析](search-traffic-analytics.md)。

## <a name="query-volume-qps"></a>查詢卷 （QPS）

卷測量為**每秒搜索查詢**（QPS），這是一個內置指標，可以報告為在一分鐘內執行的查詢的平均值、計數值、最小值或最大值。 指標的一分鐘間隔（時間粒 =PT1M）在系統中固定。

查詢通常以毫秒為單位執行，因此只有以秒為單位的查詢才會顯示在指標中。

| 彙總類型 | 描述 |
|------------------|-------------|
| Average | 發生查詢執行時一分鐘內的平均秒數。|
| Count | 在一分鐘間隔內向日誌發出的指標數。 |
| 最大值 | 每分鐘註冊每秒搜索查詢次數最多的一次。 |
| 最小值 | 每分鐘每秒註冊的搜索查詢數最低。  |
| Sum | 分鐘內執行的所有查詢的總和。  |

例如，在一分鐘內，您可能有一個這樣的模式：一秒的高負載，即搜索查詢Per秒的最大負載，然後是 58 秒的平均負載，最後只有一秒的查詢，這是最小值。

另一個示例：如果一個節點發出 100 個指標，其中每個指標的值為 40，則"Count"為 100，"Sum"為 4000，"平均值"為 40，"最大值"為 40。

## <a name="query-performance"></a>查詢效能

在服務範圍內，查詢性能衡量為搜索延遲（查詢完成所需的時間）和由於資源爭用而丟棄的受限查詢。

### <a name="search-latency"></a>搜索延遲

| 彙總類型 | Latency | 
|------------------|---------|
| Average | 平均查詢持續時間（以毫秒為單位）。 | 
| Count | 在一分鐘間隔內向日誌發出的指標數。 |
| 最大值 | 示例中執行時間最長的查詢。 | 
| 最小值 | 示例中運行最短的查詢。  | 
| 總計 | 在間隔（一分鐘）內執行示例中所有查詢的總執行時間。  |

請考慮以下**搜索延遲**指標示例：對 86 個查詢進行了採樣，平均持續時間為 23.26 毫秒。 最小 0 表示刪除了一些查詢。 執行時間最長的查詢需要 1000 毫秒才能完成。 總執行時間是 2 秒。

![延遲聚合](./media/search-monitor-usage/metrics-latency.png "延遲聚合")

### <a name="throttled-queries"></a>限制查詢

限制查詢是指刪除而不是進程的查詢。 在大多數情況下，限制是運行服務的正常部分。  它不一定表明出了什麼問題。

當當前處理的請求數超過可用資源時，將發生限制。 當副本從旋轉或索引期間被帶出時，您可能會看到受限制的請求增加。 查詢和索引請求都由同一組資源處理。

該服務確定是否根據資源消耗刪除請求。 跨記憶體、CPU 和磁片 IO 消耗的資源百分比在一段時間內為平均值。 如果此百分比超過閾值，則所有對索引的請求將受到限制，直到請求量減少。 

根據您的用戶端，可以通過以下方式指示被限制的請求：

+ 服務返回一個錯誤"您發送的請求太多。 請稍後再試。」 
+ 服務返回 503 錯誤代碼，指示該服務當前不可用。 
+ 如果您使用的是門戶（例如，搜索資源管理器），則查詢將靜默刪除，您需要再次按一下"搜索"。

要確認限制的查詢，請使用**節流搜索查詢**指標。 您可以在門戶中流覽指標，也可以創建如本文所述的警報指標。 對於在取樣間隔內丟棄的查詢，請使用*Total*獲取未執行的查詢的百分比。

| 彙總類型 | 節流 |
|------------------|-----------|
| Average | 在間隔內丟棄的查詢百分比。 |
| Count | 在一分鐘間隔內向日誌發出的指標數。 |
| 最大值 | 在間隔內丟棄的查詢百分比。|
| 最小值 | 在間隔內丟棄的查詢百分比。 |
| 總計 | 在間隔內丟棄的查詢百分比。 |

對於**受限搜索查詢百分比**、最小、最大、平均值和總計，所有值都具有相同的值：從一分鐘內搜索查詢總數中限制的搜索查詢的百分比。

在下面的螢幕截圖中，第一個數位是計數（或發送到日誌的指標數）。 其他聚合顯示在頂部或懸停在指標上時，包括平均值、最大值和總計。 在此示例中，未刪除任何請求。

![限制聚合](./media/search-monitor-usage/metrics-throttle.png "限制聚合")

## <a name="explore-metrics-in-the-portal"></a>在門戶中流覽指標

為了快速查看當前數位，服務概述頁上的 **"監視"** 選項卡顯示三個指標（**搜索延遲**、**每秒搜索查詢（每個搜索單元）、****限制搜索查詢百分比**）（以小時、天和周為單位的固定間隔），並選擇更改聚合類型。

為了進行更深入的探索，從 **"監視"** 功能表打開指標資源管理器，以便您可以分層、放大和視覺化資料以探索趨勢或異常。 通過完成[有關創建指標圖表的本教程](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-metrics-explorer)，瞭解有關指標資源管理器的更多資訊。

1. 在"監視"部分下，選擇**指標**以打開指標資源管理器，其範圍設置為搜索服務。

1. 在"指標"下，從下拉清單中選擇一個，並查看首選類型的可用聚合清單。 聚合定義如何在每個時間間隔內對收集的資料值進行採樣。

   ![QPS 指標的指標資源管理器](./media/search-monitor-usage/metrics-explorer-qps.png "QPS 指標的指標資源管理器")

1. 在右上角，設置時間間隔。

1. 選擇視覺化效果。 預設值為折線圖。

1. 通過選擇 **"添加指標"** 並選擇不同的聚合，將其他聚合分層。

1. 放大折線圖上感興趣的區域。 將滑鼠指標放在區域的開頭，然後按住滑鼠左鍵，拖曳至區域的另一端，再放開按鈕。 圖表將會放大該時間範圍。

## <a name="identify-strings-used-in-queries"></a>標識查詢中使用的字串

啟用診斷日誌記錄時，系統將捕獲**Azure 診斷**表中的查詢請求。 作為先決條件，您必須已啟用[診斷日誌記錄](search-monitor-logs.md)，指定日誌分析工作區或其他存儲選項。

1. 在"監視"部分下，選擇 **"日誌"** 以在日誌分析中打開一個空查詢視窗。

1. 運行以下運算式以搜索 Query.Search 操作，返回由操作名稱、查詢字串、查詢索引和找到的文檔陣列成的表格結果集。 最後兩個語句在示例索引上排除由空搜索或未指定的搜索組成的查詢字串，這減少了結果中的雜色。

   ```
   AzureDiagnostics
   | project OperationName, Query_s, IndexName_s, Documents_d
   | where OperationName == "Query.Search"
   | where Query_s != "?api-version=2019-05-06&search=*"
   | where IndexName_s != "realestate-us-sample-index"
   ```

1. 或者，在*Query_s*上設置列篩選器以搜索特定的語法或字串。 例如，可以篩選*為*`?api-version=2019-05-06&search=*&%24filter=HotelName`。

   ![記錄查詢字串](./media/search-monitor-usage/log-query-strings.png "記錄查詢字串")

雖然此技術適用于臨時調查，但生成報表允許您在更有利於分析的佈局中合併和呈現查詢字串。

## <a name="identify-long-running-queries"></a>識別長時間運行的查詢

添加持續時間列以獲取所有查詢的數位，而不僅僅是作為指標選取的查詢的數位。 對這些資料進行排序將顯示哪些查詢完成的時間最長。

1. 在"監視"部分下，選擇 **"日誌**"以查詢日誌資訊。

1. 運行以下查詢以返回按持續時間（以毫秒為單位）排序的查詢。 執行時間最長的查詢位於頂部。

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search"
   | sort by DurationMs
   ```

   ![按持續時間對查詢進行排序](./media/search-monitor-usage/azurediagnostics-table-sortby-duration.png "按持續時間對查詢進行排序")

## <a name="create-a-metric-alert"></a>建立計量警示

指標警報建立一個閾值，您可以在其中收到通知或觸發您事先定義的糾正措施。 

對於搜索服務，通常為搜索延遲和限制查詢創建指標警報。 如果您知道何時丟棄查詢，則可以查找減少負載或增加容量的補救措施。 例如，如果在索引期間限制查詢增加，則可以將其推遲到查詢活動消退。

在推送特定副本分區配置的限制時，為查詢卷閾值 （QPS） 設置警報也很有説明。

1. 在"監視"部分下，選擇 **"警報"，** 然後按一下 **"新警報規則**"。 請確保您的搜索服務已被選為資源。

1. 在"條件"下，按一下"**添加**"。

1. 配置信號邏輯。 對於信號類型，選擇**指標**，然後選擇信號。

1. 選擇信號後，您可以使用圖表來視覺化歷史資料，以便就如何繼續設置條件做出明智的決策。

1. 接下來，向下滾動到警報邏輯。 對於概念驗證，您可以為測試目的指定人為的低值。

   ![警報邏輯](./media/search-monitor-usage/alert-logic-qps.png "警報邏輯")

1. 接下來，指定或創建操作組。 這是滿足閾值時要調用的回應。 它可能是推送通知或自動回應。

1. 最後，指定警報詳細資訊。 命名並描述警報，分配嚴重性值，並指定是在啟用還是禁用狀態下創建規則。

   ![警示詳細資料](./media/search-monitor-usage/alert-details.png "警示詳細資料")

如果指定了電子郵件通知，您將收到來自"Microsoft Azure"的電子郵件，主題行為"Azure：啟動嚴重性：3"。 `<your rule name>`

<!-- ## Report query data

Power BI is an analytical reporting tool useful for visualizing data, including log information. If you are collecting data in Blob storage, a Power BI template makes it easy to spot anomalies or trends. Use this link to download the template. -->

## <a name="next-steps"></a>後續步驟

如果您尚未這樣做，請查看搜索服務監視的基礎知識，以瞭解各種監督功能。

> [!div class="nextstepaction"]
> [監視 Azure 認知搜索中的操作和活動](search-monitor-usage.md)