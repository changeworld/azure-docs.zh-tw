---
title: Azure 宇宙 DB .NET 更改饋送處理器 API，SDK 版本資訊
description: 了解「變更摘要處理器 API 和 SDK」的所有相關資訊，包括發行日期、停用日期，以及「.NET 變更摘要處理器 SDK」每個版本之間的變更。
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 01/30/2019
ms.author: maquaran
ms.openlocfilehash: 9252e3e41d0c639231a2abe20202499c6b3ee32a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75444865"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>.NET 變更摘要處理器 SDK：下載和版本資訊

> [!div class="op_single_selector"]
>
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET 更改源](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [非同步 Java](sql-api-sdk-async-java.md)
> * [JAVA](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [休息](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST 資源提供者](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [批量執行器 - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [批量執行器 - JAVA](sql-api-sdk-bulk-executor-java.md)

|   |   |
|---|---|
|**SDK 下載**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**API 文檔**|[變更摘要處理器程式庫 API 參考文件](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**開始使用**|[開始使用變更摘要處理器 .NET SDK](change-feed.md)|
|**目前支援的架構**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [微軟 .NET 核心](https://www.microsoft.com/net/download/core) |

> [!NOTE]
> 如果您使用的是更改源處理器，請參閱[.NET SDK](change-feed-processor.md)的最新版本 3.x，該版本內置於 SDK 中的更改源。 

## <a name="release-notes"></a>版本資訊

### <a name="v2-builds"></a>v2 組建

### <a name="228"></a><a name="2.2.8"/>2.2.8
* 穩定性和可診斷性改進：
  * 添加了用於檢測讀取更改源需要很長時間的支援。 當它花費的時間超過`ChangeFeedProcessorOptions.ChangeFeedTimeout`屬性指定的值時，將採取以下步驟：
    * 讀取有問題的分區上的更改源的操作將中止。
    * 更改饋送處理器實例放棄有問題的租約的擁有權。 下一個租約獲取步驟期間將選取丟棄的租約，該步驟將由相同或不同的更改饋送處理器實例完成。 這樣，讀取更改源將開始。
    * 問題報告給運行狀況監視器。 預設運行狀況監視器將所有報告的問題發送到跟蹤日誌。
  * 添加了新的公共屬性： `ChangeFeedProcessorOptions.ChangeFeedTimeout`. 此屬性的預設值為 10 分鐘。
  * 添加了新的公共枚舉值： `Monitoring.MonitoredOperation.ReadChangeFeed`。 當 的值`HealthMonitoringRecord.Operation`設置為`Monitoring.MonitoredOperation.ReadChangeFeed`時，它指示運行狀況問題與讀取更改源相關。

### <a name="227"></a><a name="2.2.7"/>2.2.7
* 改進了獲取所有租約的時間超過租約過期間隔（例如，由於網路問題）方案的負載平衡策略：
  * 在此方案中，負載平衡演算法用於錯誤地將租約視為過期，從而導致從活動擁有者竊取租約。 這可能引發不必要的重新平衡大量租約。
  * 此問題在此版本中已修復，在獲取擁有者未更改的過期租約時避免重試衝突，並將獲取過期租約推遲到下一次負載平衡反覆運算。

### <a name="226"></a><a name="2.2.6"/>2.2.6
* 改善觀察者例外狀況的處理。
* 更豐富的觀察者錯誤資訊：
  * 觀察者因為觀察者的 ProcessChangesAsync 所擲回的例外狀況而關閉時，CloseAsync 現在會收到設為 ChangeFeedObserverCloseReason.ObserverError 的原因參數。
  * 新增的追蹤資訊，以識別觀察者中使用者程式碼內的錯誤。

### <a name="225"></a><a name="2.2.5"/>2.2.5
* 新增處理使用共用資料庫輸送量之分割集合的支援。
  * 此版本會修正可能會在僅建立一個 (而非兩個) 子分割區索引鍵範圍之下，將結果分割為分割區重新平衡時，使用共用資料庫輸送量分割集合之期間發生的問題。 發生此情況時，變更摘要處理器可能會卡在刪除舊分割區索引鍵範圍的程序中，而無法建立新的租用。 此版本已經修正這個問題。

### <a name="224"></a><a name="2.2.4"/>2.2.4
* 已新增 ChangeFeedProcessorOptions.StartContinuation 屬性，以支援從要求接續 Token 啟動變更摘要。 只有在租用集合是空的或租用未設定 ContinuationToken 時，才會使用此屬性。 如果租用集合中的租用已設定 ContinuationToken，則會使用 ContinuationToken 並忽略 ChangeFeedProcessorOptions.StartContinuation。

### <a name="223"></a><a name="2.2.3"/>2.2.3
* 已新增使用自訂存放區來保存每個分割區之接續 Token 的支援。
  * 例如，自訂的租用存放區可以是利用任何自訂方式所分割的 Azure Cosmos DB 租用集合。
  * 自訂的租用存放區可以使用新的擴充點 ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager) 和 ILeaseStoreManager 公用介面。
  * 已在多個角色介面中重構 ILeaseManager 介面。
* 次要重大變更：已移除擴充點 ChangeFeedProcessorBuilder.WithLeaseManager(ILeaseManager)，改為使用 ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager)。

### <a name="222"></a><a name="2.2.2"/>2.2.2
* 此版本會修正在處理受監視集合中的分割以及使用已分割之租用集合期間所發生的問題。 在處理已分割之分割區的租用時，可能不會刪除對應至該分割區的租用。 此版本已經修正這個問題。

### <a name="221"></a><a name="2.2.1"/>2.2.1
* 固定式估算程式計算，適用於多重主機帳戶和新的工作階段權杖格式。

### <a name="220"></a><a name="2.2.0"/>2.2.0
* 已新增對分割區租用集合的支援。 分割區索引鍵必須定義為 /id。
* 次要重大變更：已變更 IChangeFeedDocumentClient 介面和 ChangeFeedDocumentClient 類別的方法，以包含 RequestOptions 和 CancellationToken 參數。 IChangeFeedDocumentClient 是一個高級擴充點，允許您提供文檔用戶端的自訂實現，以便與更改源處理器一起使用，例如修飾 DocumentClient 並攔截所有調用，以執行額外的跟蹤、錯誤處理等。使用此更新，將需要更改實現 IChangeFeedDocumentClient 的代碼，以在實現中包括新參數。
* 次要診斷改進。

### <a name="210"></a><a name="2.1.0"/>2.1.0
* 新增了 API：Task&lt;IReadOnlyList&lt;RemainingPartitionWork&gt;&gt; IRemainingWorkEstimator.GetEstimatedRemainingWorkPerPartitionAsync()。 這可用於取得每個資料分割的預估工作。
* 支援 Microsoft.Azure.DocumentDB SDK 2.0。 需要 Microsoft.Azure.DocumentDB 2.0 或更新版本。

### <a name="206"></a><a name="2.0.6"/>2.0.6
* 已新增 ChangeFeedEventHost.HostName 公用屬性以與 v1 相容。

### <a name="205"></a><a name="2.0.5"/>2.0.5
* 已修正會在分割區進行分割期間發生的競爭條件。 競爭條件可能會導致在取得租用後立即於分割區進行分割期間失去它，並導致競爭情形。 競爭條件問題已在此版本中修正。

### <a name="204"></a><a name="2.0.4"/>2.0.4
* GA SDK

### <a name="203-prerelease"></a><a name="2.0.3-prerelease"/>2.0.3-prerelease
* 修正下列問題：
  * 當分割區進行分割時，可能會重複處理分割前修改的文件。
  * 租用集合中沒有租用時，GetEstimatedRemainingWork API 傳回 0。

* 下列例外狀況設為公開。 實作 IPartitionProcessor 的擴充可能擲回以下例外狀況。
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.LeaseLostException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionNotFoundException.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionSplitException. 

### <a name="202-prerelease"></a><a name="2.0.2-prerelease"/>2.0.2 發行前版本
* 次要 API 變更：
  * 移除已標記為過時的 ChangeFeedProcessorOptions.IsAutoCheckpointEnabled。

### <a name="201-prerelease"></a><a name="2.0.1-prerelease"/>2.0.1 發行前版本
* 穩定性改進：
  * 改善租用存放區初始化的處理。 如果租用存放區是空的，則只有一個處理器的執行個體可以將它初始化，其他執行個體則須等候。
  * 更穩定/有效的租用更新/版本。 更新和釋放每一個分割區的租用會與其他租用的更新各自獨立。 在 V1 中，所有分割區會循序完成此動作。
* 新的 v2 API：
  * 處理器彈性建構的產生器模式：ChangeFeedProcessorBuilder 類別。
    * 可取用參數的任意組合。
    * 可將 DocumentClient 執行個體用於監視和 (或) 租用集合 (不適用於 v1)。
  * IChangeFeedObserver.ProcessChangesAsync 現在會取用 CancellationToken。
  * IRemainingWorkEstimator - 剩餘工作估算器可在處理器以外單獨使用。
  * 新的擴充點：
    * IPartitionLoadBalancingStrategy - 用於處理器執行個體之間的分割區自訂負載平衡。
    * ILease、ILeaseManager - 用於自訂租用管理。
    * IPartitionProcessor - 用於分割區上的自訂處理變更。
* 記錄 - 使用 [LibLog](https://github.com/damianh/LibLog) 程式庫。
* 可 100% 回溯相容於 v1 API。
* 新的程式碼基底。
* 與 [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.21.1 版和更新版本相容。

### <a name="v1-builds"></a>v1 組建

### <a name="133"></a><a name="1.3.3"/>1.3.3
* 已新增更多記錄。
* 已修正在多次呼叫擱置中工作估計時的 DocumentClient 流失。

### <a name="132"></a><a name="1.3.2"/>1.3.2
* 修正擱置的工作評估。

### <a name="131"></a><a name="1.3.1"/>1.3.1
* 穩定性改進。
  * 修復了處理已取消的任務問題，可能導致某些分區上的觀察者停止。
* 支援手動檢查點。
* 與 [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.21 版和更新版本相容。

### <a name="120"></a><a name="1.2.0"/>1.2.0
* 新增 .NET Standard 2.0 的支援。 套件現在支援 `netstandard2.0` 和 `net451` Framework Moniker。
* 與 [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.17.0 或以上版本相容。
* 與 [SQL .NET Core SDK](sql-api-sdk-dotnet-core.md) 1.5.1 或以上版本相容。

### <a name="111"></a><a name="1.1.1"/>1.1.1
* 變更摘要為空白或沒有已暫止的工作時，會修正計算剩餘工作估計的問題。
* 與 [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.13.2 或以上版本相容。

### <a name="110"></a><a name="1.1.0"/>1.1.0
* 新增方法以取得要在變更摘要中處理之剩餘工作的估算。
* 與 [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.13.2 或以上版本相容。

### <a name="100"></a><a name="1.0.0"/>1.0.0
* GA SDK
* 與 [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.14.1 或以上版本相容。

## <a name="release--retirement-dates"></a>發行和停用日期

Microsoft 至少會在停用 SDK 的 **12 個月** 之前提供通知，以供順利轉換至較新/支援的版本。

新的功能與最佳化項目只會新增至目前的 SDK，因此建議您一律盡早升級至最新的 SDK 版本。 

服務將會拒絕使用已停用 SDK 的任何 Cosmos DB 要求。

<br/>

| 版本 | 發行日期 | 停用日期 |
| --- | --- | --- |
| [2.2.8](#2.2.8) |2019年10月28日 |--- |
| [2.2.7](#2.2.7) |2019 年 5 月 14 日 |--- |
| [2.2.6](#2.2.6) |2019 年 1 月 29 日 |--- |
| [2.2.5](#2.2.5) |2018 年 12 月 13 日 |--- |
| [2.2.4](#2.2.4) |2018 年 11 月 29 日 |--- |
| [2.2.3](#2.2.3) |2018 年 11 月 19 日 |--- |
| [2.2.2](#2.2.2) |2018 年 10 月 31 日 |--- |
| [2.2.1](#2.2.1) |2018 年 10 月 24 日 |--- |
| [1.3.3](#1.3.3) |2018 年 5 月 8 日 |--- |
| [1.3.2](#1.3.2) |2018 年 4 月 18 日 |--- |
| [1.3.1](#1.3.1) |2018 年 3 月 13 日 |--- |
| [1.2.0](#1.2.0) |2017 年 10 月 31 日 |--- |
| [1.1.1](#1.1.1) |2017 年 8 月 29 日 |--- |
| [1.1.0](#1.1.0) |2017 年 8 月 13 日 |--- |
| [1.0.0](#1.0.0) |2017 年 7 月 7 日 |--- |

## <a name="faq"></a>常見問題集

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>另請參閱

若要深入了解 Cosmos DB，請參閱 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服務頁面。
