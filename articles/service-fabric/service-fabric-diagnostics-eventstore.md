---
title: Azure Service Fabric 事件存放區
description: 瞭解 Azure Service Fabric 的 EventStore，這是一種瞭解及監視叢集或工作負載狀態的方法。
author: srrengar
ms.topic: conceptual
ms.date: 6/6/2019
ms.author: srrengar
ms.openlocfilehash: 0b6258e160794eaf7d0c05775f8fd3b796e3ba47
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91354814"
---
# <a name="eventstore-overview"></a>EventStore 總覽

>[!NOTE]
>截至 Service Fabric 6.4 版為止， EventStore API 僅適用於在 Azure 上執行的 Windows 叢集。 我們正在將這個功能和我們的獨立叢集移植到 Linux。

## <a name="overview"></a>概觀

6.2 版中引進，EventStore 服務是 Service Fabric 中的監視選項。 EventStore 可讓您了解您的叢集或工作負載在指定時間點的狀態。
EventStore 是具狀態的 Service Fabric 服務，可維護叢集中的事件。 此事件會透過 Service Fabric Explorer、REST 和 API 公開。 EventStore 可直接查詢叢集，取得叢集中任何實體的診斷資料，應可用來協助您：

* 診斷開發或測試中的問題，或在使用監視管線的場合中診斷問題
* 確認正確處理您針對叢集採取的管理動作
* 取得 Service Fabric 如何與特定實體互動的「快照集」

![螢幕擷取畫面：顯示 [節點] 窗格的 [事件] 索引標籤，其中包含 NodeDown 事件。](media/service-fabric-diagnostics-eventstore/eventstore.png)

若要查看 EventStore 中可用事件的完整清單，請參閱 [Service Fabric 事件](service-fabric-diagnostics-event-generation-operational.md)。

>[!NOTE]
>截至 Service Fabric 6.4 版為止， EventStore Api 和 UX 一般適用于 Azure Windows 叢集。 我們正在將這個功能和我們的獨立叢集移植到 Linux。

您可以查詢 EventStore 服務，找出叢集中每個實體和實體類型適用的事件。 這表示您可以查詢以下層級的事件：
* 叢集：叢集本身特有的事件 (例如叢集升級)
* 節點：所有節點層級事件
* 節點：某個節點 (以 `nodeName` 識別) 特有的事件
* 應用程式：所有應用程式層級事件
* 應用程式：某個應用程式 (以 `applicationId` 識別) 特有的事件
* 服務：來自叢集中所有服務的事件
* 服務：來自特定服務 (以 `serviceId` 識別) 的事件
* 分割區：來自所有分割區的事件
* 分割區：來自特定分割區 (以 `partitionId` 識別) 的事件
* 分割區複本：特定分割區 (以 `partitionId` 識別) 內所有複本 / 執行個體中的事件
* 分割區複本：特定複本 / 執行個體 (以 `replicaId` 和 `partitionId` 識別) 中的事件

若要深入瞭解 API，請參閱 [EVENTSTORE API 參考](/rest/api/servicefabric/sfclient-index-eventsstore)。

EventStore 服務還能將叢集中的事件相互關聯。 透過同時間寫入的事件，您可以追溯到彼此影響的相異實體；EventStore 服務能連結這些事件，協助您找出叢集中活動的原因。 例如，如果某個應用程式在未引發變更的情況下成為不良狀況，EventStore 也會查看平台公開的其他事件，因而可能會將這個狀況與 `Error` 或 `Warning` 事件相互關聯。 這有助於縮短偵測失敗及分析根本原因的時間。

## <a name="enable-eventstore-on-your-cluster"></a>在您的叢集上啟用 EventStore

### <a name="local-cluster"></a>本機叢集

在[您叢集的 fabricSettings.json](service-fabric-cluster-fabric-settings.md) 中，新增 EventStoreService 作為附加元件功能，以及執行叢集升級。

```json
    "addOnFeatures": [
        "EventStoreService"
    ],
```

### <a name="azure-cluster-version-65"></a>Azure cluster 6.5 版 +
如果您的 Azure 叢集升級至6.5 版或更高版本，則會自動在您的叢集上啟用 EventStore。 若要退出，您需要使用下列內容來更新叢集範本：

* 使用或更新版本的 API `2019-03-01` 
* 將下列程式碼新增至叢集中的屬性區段
  ```json  
    "fabricSettings": [
      …
    ],
    "eventStoreServiceEnabled": false
  ```

### <a name="azure-cluster-version-64"></a>Azure 叢集版本6。4

如果您使用6.4 版，您可以編輯 Azure Resource Manager 範本以開啟 EventStore 服務。 這是藉由執行叢集設定 [升級](service-fabric-cluster-config-upgrade-azure.md) 和新增下列程式碼來完成，您可以使用 PlacementConstraints 將 EventStore 服務的複本放在特定 NodeType 上，例如系統服務專用的 nodetype。 `upgradeDescription` 區段會設定要在節點上觸發重新啟動的組態升級。 您可以在另一項更新中移除此區段。

```json
    "fabricSettings": [
          …
          …
          …,
         {
            "name": "EventStoreService",
            "parameters": [
              {
                "name": "TargetReplicaSetSize",
                "value": "3"
              },
              {
                "name": "MinReplicaSetSize",
                "value": "1"
              },
              {
                "name": "PlacementConstraints",
                "value": "(NodeType==<node_type_name_here>)"
              }
            ]
          }
        ],
        "upgradeDescription": {
          "forceRestart": true,
          "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807",
          "healthCheckWaitDuration": "00:01:00",
          "healthCheckStableDuration": "00:01:00",
          "healthCheckRetryTimeout": "00:5:00",
          "upgradeTimeout": "1:00:00",
          "upgradeDomainTimeout": "00:10:00",
          "healthPolicy": {
            "maxPercentUnhealthyNodes": 100,
            "maxPercentUnhealthyApplications": 100
          },
          "deltaHealthPolicy": {
            "maxPercentDeltaUnhealthyNodes": 0,
            "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0,
            "maxPercentDeltaUnhealthyApplications": 0
          }
        }
```


## <a name="next-steps"></a>後續步驟
* 開始使用 EventStore API - [在 Azure Service Fabric 叢集中使用 EventStore API](service-fabric-diagnostics-eventstore-query.md)
* 深入了解 EventStore 所提供的事件清單 - [Service Fabric 事件](service-fabric-diagnostics-event-generation-operational.md)
* Service Fabric 的監視和診斷概觀 - [Service Fabric 的監視和診斷](service-fabric-diagnostics-overview.md)
* 檢視完整的 API 呼叫清單 - [EventStore REST API 參考](/rest/api/servicefabric/sfclient-index-eventsstore)
* 深入了解監視叢集 - [監視叢集和平台](service-fabric-diagnostics-event-generation-infra.md)。
