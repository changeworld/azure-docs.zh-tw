---
title: 使用 Azure 監視器記錄的 Azure Service Fabric 事件分析
description: 瞭解如何使用 Azure 監視器記錄來視覺化和分析事件，以進行 Azure Service Fabric 叢集的監視和診斷。
author: srrengar
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: srrengar
ms.openlocfilehash: bd952449cb088a383f0b9241fb7856522fbeeb10
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86257673"
---
# <a name="event-analysis-and-visualization-with-azure-monitor-logs"></a>Azure 監視器記錄檔進行事件分析和視覺效果
 Azure 監視器記錄會從裝載於雲端的應用程式和服務收集和分析遙測，並提供分析工具來協助您將其可用性和效能最大化。 本文概述如何在 Azure 監視器記錄中執行查詢，以深入瞭解及疑難排解您的叢集中發生的情況。 我們將討論下列常見的問題：

* 如何針對健康情況事件進行疑難排解？
* 如何知道節點發生故障？
* 如何知道我的應用程式服務已經啟動或停止？

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview-of-the-log-analytics-workspace"></a>Log Analytics 工作區總覽

>[!NOTE] 
>雖然在叢集建立時預設會啟用診斷儲存體，但您仍必須設定 Log Analytics 工作區才能讀取診斷儲存體。

Azure 監視器記錄會從受管理的資源（包括 Azure 儲存體資料表或代理程式）收集資料，並將它保留在中央存放庫中。 此資料可接著用於分析、警示和視覺效果，或進一步匯出。 Azure 監視器記錄檔支援事件、效能資料或任何其他自訂資料。 請查看[設定診斷延伸模組來匯總事件](service-fabric-diagnostics-event-aggregation-wad.md)和步驟的步驟，[以建立 Log Analytics 工作區以讀取儲存體中的事件](service-fabric-diagnostics-oms-setup.md)，以確保資料流程入 Azure 監視器記錄。

Azure 監視器記錄收到資料之後，Azure 會有數個*監視解決方案*，這些是預先封裝的解決方案或作業儀表板，可用於監視傳入的資料（自訂于數個案例）。 其中包括 *Service Fabric 分析*解決方案和*容器*解決方案，這是與使用 Service Fabric 叢集進行診斷和監視最相關的兩個解決方案。 本文說明如何使用 Service Fabric 分析解決方案，它是用工作區建立的。

## <a name="access-the-service-fabric-analytics-solution"></a>存取 Service Fabric 分析解決方案

在[Azure 入口網站](https://portal.azure.com)中，移至您在其中建立 Service Fabric 分析解決方案的資源群組。

選取 [資源**ServiceFabric \<nameOfOMSWorkspace\> **]。

在 `Summary` 中，您會看到每一個所啟用解決方案的圖格以圖形形式顯示，其中一個屬於 Service Fabric。 按一下 [Service Fabric]**** 圖形以繼續進行「Service Fabric 分析」解決方案。

![Service Fabric 解決方案](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_summary.PNG)

下圖顯示 Service Fabric 分析解決方案的首頁。 這個首頁提供叢集中目前情況的快照集檢視。

![Service Fabric 解決方案](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_solution.PNG)

 如果您建立叢集的時候啟用了分析功能，就可以看到以下各項的事件 

* [Service Fabric 叢集事件](service-fabric-diagnostics-event-generation-operational.md)
* [Reliable Actors 程式設計模型事件](service-fabric-reliable-actors-diagnostics.md)
* [Reliable Services 程式設計模型事件](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>除了現有的 Service Fabric 事件外，[更新分析延伸模組的設定](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)可以收集更詳細的系統事件。

## <a name="view-service-fabric-events-including-actions-on-nodes"></a>檢視 Service Fabric 事件，包括節點上的動作

在 Service Fabric 分析頁面上，按一下 **Service Fabric 事件**的圖形。

![Service Fabric 解決方案操作通道](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events_selection.png)

按一下 [清單]**** 來檢視清單中的事件。 這裡您會看到所有收集的系統事件。 如需參考，這些是來自 Azure 儲存體帳戶中的**WADServiceFabricSystemEventsTable** ，同樣地，您接下來看到的可靠服務和動作專案事件是來自個別的資料表。
    
![查詢操作通道](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events.png)

或者您可以按一下左邊的放大鏡，然後使用 Kusto 查詢語言來尋找您想要的資料。 例如，如果想尋找針對叢集節點所採取的動作，您可以使用下列查詢。 下面使用的事件識別碼可在[操作通道事件參考](service-fabric-diagnostics-event-generation-operational.md)中找到。

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

您可以查詢很多欄位，例如特定的節點 (電腦)、系統服務 (TaskName) 等。

## <a name="view-service-fabric-reliable-service-and-actor-events"></a>檢視 Service Fabric Reliable Service 和 Actor 事件

在 Service Fabric 分析頁面上，按一下 **Reliable Services** 的圖形。

![Service Fabric 解決方案 Reliable Services](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_services_events_selection.png)

按一下 [清單]**** 來檢視清單中的事件。 這裡您會看到來自 Reliable Services 的事件。 啟動及完成 runasync 服務時，您可以看到不同的事件，它們通常是在部署和升級發生的。 

![查詢 Reliable Services](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_service_events.png)

以類似的方式，就可以檢視 Reliable Actor 事件。 若要為 Reliable Actor 設定更詳細的事件，您需要到分析延伸模組的設定中去變更 `scheduledTransferKeywordFilter` (如下所示)。 如需這些值的詳細資料，請[參閱可靠](service-fabric-reliable-actors-diagnostics.md#keywords)的動作專案事件參考。

```json
"EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
```

Kusto 查詢語言功能很強大。 您可以執行的另一個重要查詢是找出哪些節點會產生最多事件。 下列螢幕擷取畫面中的查詢會顯示如何使用特定服務和節點來彙總 Service Fabric 操作事件。

![每個節點的查詢事件](media/service-fabric-diagnostics-event-analysis-oms/oms_kusto_query.png)

## <a name="next-steps"></a>後續步驟

* 若要啟用基礎結構監視 (也就是監視效能計數器)，請前往[新增 Log Analytics 代理程式](service-fabric-diagnostics-oms-agent.md)。 這個代理程式會收集效能計數器，並將它們新增至現有的工作區。
* 針對內部部署叢集，Azure 監視器記錄會提供閘道 (HTTP 正向 Proxy) ，可用來將資料傳送至 Azure 監視器記錄。 深入瞭解[如何使用 Log Analytics 閘道將電腦連線到沒有網際網路存取 Azure 監視器記錄](../azure-monitor/platform/gateway.md)。
* 設定[自動化警示](../azure-monitor/platform/alerts-overview.md)，以協助偵測與診斷。
* 取得熟悉，其中包含作為 Azure 監視器記錄一部分的[記錄搜尋和查詢](../azure-monitor/log-query/log-query-overview.md)功能。
* 深入瞭解 Azure 監視器記錄及其提供的內容，請參閱[什麼是 Azure 監視器記錄？](../azure-monitor/overview.md)。
