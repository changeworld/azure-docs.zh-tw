---
title: Azure Service Fabric 事件清單
description: Azure Service Fabric 所提供的事件完整清單可協助監控叢集。
author: srrengar
ms.topic: reference
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: e69b407bc7d58a83616daa44272ec008ccff9fad
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79258534"
---
# <a name="list-of-service-fabric-events"></a>Service Fabric 事件清單 

Service Fabric 會公開一組主要的叢集事件，以 [Service Fabric 事件](service-fabric-diagnostics-events.md)的方式向您通知叢集的狀態。 這些是以 Service Fabric 在您的節點和叢集上執行的動作，或以叢集擁有者/操作者所做的管理決定為基礎。 您可以透過數種方式進行設定來存取這些事件，包括設定叢集的[Azure 監視器記錄](service-fabric-diagnostics-oms-setup.md)，或查詢[EventStore](service-fabric-diagnostics-eventstore.md)。 在 Windows 電腦上，這些事件會饋送至 EventLog，因此您可以在事件檢視器中看到 Service Fabric 事件。 

以下是這些事件的一些特性
* 每個事件都會繫結至叢集中的特定實體，例如應用程式、服務、節點、複本。
* 每個事件都包含一組通用欄位： EventInstanceId、事件名稱和類別目錄。
* 每個事件都包含可將事件繫結回到其相關實體的欄位。 例如，ApplicationCreated 事件具有可識別所建立應用程式名稱的欄位。
* 事件會以此方式予以結構化，可使用於各種不同的工具來進行進一步分析。 此外，事件的相關詳細資料會定義為不同的屬性，而不是很長的字串。 
* 事件是由 Service Fabric 中不同的子系統所寫入並以下面的 Source(Task) 識別。 [Service Fabric 架構](service-fabric-architecture.md)和 [Service Fabric 技術概觀](service-fabric-technical-overview.md)提供有關這些子系統的詳細資訊。

以下是依實體組織的 Service Fabric 事件清單。

## <a name="cluster-events"></a>叢集事件

**叢集升級事件**

您可以在[這裡](service-fabric-cluster-upgrade-windows-server.md)找到有關叢集升級的其他詳細資料。

| EventId | 名稱 | 類別 | 描述 |來源 (工作) | 層級 | 
| --- | --- | --- | --- | --- | --- | 
| 29627 | ClusterUpgradeStarted | 升級 | 已啟動叢集更新 | CM | 資訊 |
| 29628 | ClusterUpgradeCompleted | 升級 | 已完成叢集更新 | CM | 資訊 | 
| 29629 | ClusterUpgradeRollbackStarted | 升級 | 已開始復原叢集更新  | CM | 警告 | 
| 29630 | ClusterUpgradeRollbackCompleted | 升級 | 已完成復原叢集更新 | CM | 警告 | 
| 29631 | ClusterUpgradeDomainCompleted | 升級 | 升級網域已在叢集升級期間完成升級 | CM | 資訊 | 

## <a name="node-events"></a>節點事件

**節點生命週期事件** 

| EventId | 名稱 | 類別 | 描述 |來源 (工作) | 層級 |
| --- | --- | ---| --- | --- | --- | 
| 18602 | NodeDeactivateCompleted | StateTransition | 已完成停用節點 | FM | 資訊 | 
| 18603 | NodeUp | StateTransition | 叢集偵測到節點已啟動 | FM | 資訊 | 
| 18604 | NodeDown | StateTransition | 叢集偵測到節點已關閉。 在節點重新啟動期間，您會看到後面接著 NodeUp 事件的 NodeDown 事件 |  FM | 錯誤 | 
| 18605 | NodeAddedToCluster | StateTransition |  叢集中新增了新節點，而 Service Fabric 可以將應用程式部署到這個節點 | FM | 資訊 | 
| 18606 | NodeRemovedFromCluster | StateTransition |  已從叢集中移除節點。 Service Fabric 無法再將應用程式部署到這個節點 | FM | 資訊 | 
| 18607 | NodeDeactivateStarted | StateTransition |  已啟動停用節點作業 | FM | 資訊 | 
| 25621 | NodeOpenSucceeded | StateTransition |  節點已成功啟動 | FabricNode | 資訊 | 
| 25622 | NodeOpenFailed | StateTransition |  節點無法啟動和加入環狀 | FabricNode | 錯誤 | 
| 25624 | NodeClosed | StateTransition |  已成功關閉節點 | FabricNode | 資訊 | 
| 25626 | NodeAborted | StateTransition |  已強制關閉節點 | FabricNode | 錯誤 | 

## <a name="application-events"></a>應用程式事件

**應用程式生命週期事件**

| EventId | 名稱 | 類別 | 描述 |來源 (工作) | 層級 | 
| --- | --- | --- | --- | --- | --- | 
| 29620 | ApplicationCreated | LifeCycle | 已建立新的應用程式 | CM | 資訊 | 
| 29625 | ApplicationDeleted | LifeCycle | 已刪除現有的應用程式 | CM | 資訊 | 
| 23083 | ApplicationProcessExited | LifeCycle | 已結束應用程式中的處理程序 | 裝載 | 資訊 | 

**應用程式升級事件**

您可以在[這裡](service-fabric-application-upgrade.md)找到有關應用程式升級的其他詳細資料。

| EventId | 名稱 | 類別 | 描述 |來源 (工作) | 層級 | 
| --- | --- | ---| --- | --- | --- | 
| 29621 | ApplicationUpgradeStarted | 升級 | 已啟動應用程式更新 | CM | 資訊 | 
| 29622 | ApplicationUpgradeCompleted | 升級 | 已完成應用程式更新 | CM | 資訊 | 
| 29623 | ApplicationUpgradeRollbackStarted | 升級 | 已開始復原應用程式更新 |CM | 警告 | 
| 29624 | ApplicationUpgradeRollbackCompleted | 升級 | 已完成復原應用程式更新 | CM | 警告 | 
| 29626 | ApplicationUpgradeDomainCompleted | 升級 | 升級網域已在應用程式升級期間完成升級 | CM | 資訊 | 

## <a name="service-events"></a>服務事件

**服務生命週期事件**

| EventId | 名稱 | 類別 | 描述 |來源 (工作) | 層級 | 
| --- | --- | ---| --- | --- | --- |
| 18657 | ServiceCreated | LifeCycle | 已建立新服務 | FM | 資訊 | 
| 18658 | ServiceDeleted | LifeCycle | 已刪除現有服務 | FM | 資訊 | 

## <a name="partition-events"></a>分割事件

**分割移動事件**

| EventId | 名稱 | 類別 | 描述 |來源 (工作) | 層級 | 
| --- | --- | ---| --- | --- | --- |
| 18940 | PartitionReconfigured | LifeCycle | 已完成重新設定分割 | RA | 資訊 | 

## <a name="replica-events"></a>複本事件

**複本生命週期事件**

| EventId | 名稱 | 類別 | 描述 |來源 (工作) | 層級 |
| --- | --- | ---| --- | --- | --- |
| 61701 | ReliableDictionaryOpened | LifeCycle | 可靠的字典已開啟 | DistributedDictionary | 資訊 |
| 61702 | ReliableDictionaryClosed | LifeCycle | 可靠的字典已關閉 | DistributedDictionary | 資訊 |
| 61703 | ReliableDictionaryCheckpointRecovered | LifeCycle | 可靠的字典已復原其檢查點 | DistributedDictionary | 資訊 |
| 61704 | ReliableDictionaryCheckpointFilesSent | LifeCycle | 複本已傳送可靠字典的檢查點檔案 | DistributedDictionary | 資訊 |
| 61705 | ReliableDictionaryCheckpointFilesReceived | LifeCycle | 複本已收到可靠字典的檢查點檔案 | DistributedDictionary | 資訊 |
| 61963 | ReliableQueueOpened | LifeCycle | 可靠的佇列已開啟 | DistributedQueue | 資訊 |
| 61964 | ReliableQueueClosed | LifeCycle | 可靠的佇列已關閉 | DistributedQueue | 資訊 |
| 61965 | ReliableQueueCheckpointRecovered | LifeCycle | 可靠的佇列已復原其檢查點 | DistributedQueue | 資訊 |
| 61966 | ReliableQueueCheckpointFilesSent | LifeCycle | 複本已傳送可靠佇列的檢查點檔案 | DistributedQueue | 資訊 |
| 63647 | ReliableQueueCheckpointFilesReceived | LifeCycle | 複本已接收可靠佇列的檢查點檔案 | DistributedQueue | 資訊 |
| 63648 | ReliableConcurrentQueueOpened | LifeCycle | 可靠的並行佇列已開啟 | ReliableConcurrentQueue | 資訊 |
| 63649 | ReliableConcurrentQueueClosed | LifeCycle | 可靠的並行佇列已關閉 | ReliableConcurrentQueue | 資訊 |
| 63650 | ReliableConcurrentQueueCheckpointRecovered | LifeCycle | 可靠的並行佇列已復原其檢查點 | ReliableConcurrentQueue | 資訊 |
| 61687 | TStoreError | 失敗 | 可靠的集合收到意外的錯誤 | TStore | 錯誤 |
| 63831 | PrimaryFullCopyInitiated | LifeCycle | 主要複本已起始完整複本 | TReplicator | 資訊 |
| 63832 | PrimaryPartialCopyInitiated | LifeCycle | 主要複本已起始部分複本 | TReplicator | 資訊 |
| 16831 | BuildIdleReplicaStarted | LifeCycle | 主要複本已開始建立閒置複本 | 複寫 | 資訊 |
| 16832 | BuildIdleReplicaCompleted | LifeCycle | 主要複本已完成建立閒置複本 | 複寫 | 資訊 |
| 16833 | BuildIdleReplicaFailed | LifeCycle | 主要複本無法建立閒置複本 | 複寫 | 警告 |
| 16834 | PrimaryReplicationQueueFull | 健康情況 | 主要複本的複寫佇列已滿 | 複寫 | 警告 |
| 16835 | PrimaryReplicationQueueWarning | 健康情況 | 主要複本的複寫佇列已接近 full | 複寫 | 警告 |
| 16836 | PrimaryReplicationQueueWarningMitigated | 健康情況 | 主要複本的複寫佇列正常 | 複寫 | 資訊 |
| 16837 | SecondaryReplicationQueueFull | 健康情況 | 次要複本的複寫佇列已滿 | 複寫 | 警告 |
| 16838 | SecondaryReplicationQueueWarning | 健康情況 | 次要複本的複寫佇列已接近 full | 複寫 | 警告 |
| 16839 | SecondaryReplicationQueueWarningMitigated | 健康情況 | 次要複本的複寫佇列正常 | 複寫 | 資訊 |
| 16840 | PrimaryFaultedSlowSecondary | 健康情況 | 主要複本發生緩慢的次要複本錯誤 | 複寫 | 警告 |
| 16841 | ReplicatorFaulted | 健康情況 | 複本發生錯誤 | 複寫 | 警告 |

## <a name="container-events"></a>容器事件

**容器生命週期事件** 

| EventId | 名稱 | 描述 |來源 (工作) | 層級 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 23074 | ContainerActivated | 容器已啟動 | 裝載 | 資訊 | 1 |
| 23075 | ContainerDeactivated | 容器已停止 | 裝載 | 資訊 | 1 |
| 23082 | ContainerExited | 容器已結束 - 查看 UnexpectedTermination 旗標 | 裝載 | 資訊 | 1 |

## <a name="health-reports"></a>健康狀態報告

[Service Fabric 健康情況模型](service-fabric-health-introduction.md)可提供豐富、有彈性且可延伸的健康情況評估與報告。 從 Service Fabric 6.2 版起，健康情況資料當作平台事件寫入，以提供健康情況的歷程記錄。 為了保持較低的健康情況事件數量，我們只寫入下列 Service Fabric 事件：

* 所有 `Error` 或 `Warning` 健康情況報告
* 轉換期間的 `Ok` 健康情況報告
* 當 `Error` 或 `Warning` 健康情況事件到期時。 這可用來判斷實體狀況不良持續多久

**叢集健康情況報表事件**

| EventId | 名稱 | 描述 |來源 (工作) | 層級 | 版本 |
| --- | --- | --- | --- | --- | --- |
| 54428 | ClusterNewHealthReport | 有新的叢集健康情況報告可用 | HM | 資訊 | 1 |
| 54437 | ClusterHealthReportExpired | 現有的叢集健康情況報告已過期 | HM | 資訊 | 1 |

**節點健康情況報表事件**

| EventId | 名稱 | 描述 |來源 (工作) | 層級 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 54423 | NodeNewHealthReport | 有新的節點健康情況報告可用 | HM | 資訊 | 1 |
| 54432 | NodeHealthReportExpired | 現有的節點健康情況報告已過期 | HM | 資訊 | 1 |

**應用程式健康情況報表事件**

| EventId | 名稱 | 描述 |來源 (工作) | 層級 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 54425 | ApplicationNewHealthReport | 已建立新的應用程式健康情況報告。 這適用於已解除部署的應用程式。 | HM | 資訊 | 1 |
| 54426 | DeployedApplicationNewHealthReport | 已建立新的已部署應用程式健康情況報告 | HM | 資訊 | 1 |
| 54427 | DeployedServicePackageNewHealthReport | 已建立新的已部署服務健康情況報告 | HM | 資訊 | 1 |
| 54434 | ApplicationHealthReportExpired | 現有的應用程式健康情況報告已過期 | HM | 資訊 | 1 |
| 54435 | DeployedApplicationHealthReportExpired | 現有的已部署應用程式健康情況報告已過期 | HM | 資訊 | 1 |
| 54436 | DeployedServicePackageHealthReportExpired | 現有的已部署服務健康情況報告已過期 | HM | 資訊 | 1 |

**服務健康情況報表事件**

| EventId | 名稱 | 描述 |來源 (工作) | 層級 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 54424 | ServiceNewHealthReport | 已建立新的服務健康情況報告 | HM | 資訊 | 1 |
| 54433 | ServiceHealthReportExpired | 現有的服務健康情況報告已過期 | HM | 資訊 | 1 |

**分割健康情況報表事件**

| EventId | 名稱 | 描述 |來源 (工作) | 層級 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 54422 | PartitionNewHealthReport | 已建立新的分割健康情況報告 | HM | 資訊 | 1 |
| 54431 | PartitionHealthReportExpired | 現有的分割健康情況報告已過期 | HM | 資訊 | 1 |

**複本健康情況報表事件**

| EventId | 名稱 | 描述 |來源 (工作) | 層級 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 54429 | StatefulReplicaNewHealthReport | 已建立具狀態的複本健康情況報告 | HM | 資訊 | 1 |
| 54430 | StatelessInstanceNewHealthReport | 已建立新的具狀態執行個體健康情況報告 | HM | 資訊 | 1 |
| 54438 | StatefulReplicaHealthReportExpired | 現有的具狀態複本健康情況報告已過期 | HM | 資訊 | 1 |
| 54439 | StatelessInstanceHealthReportExpired | 現有的無狀態執行個體健康情況報告已過期 | HM | 資訊 | 1 |

## <a name="chaos-testing-events"></a>混沌測試事件 

**混沌工作階段事件**

| EventId | 名稱 | 描述 |來源 (工作) | 層級 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 50021 | ChaosStarted | 已啟動混沌測試工作階段 | 可測試性 | 資訊 | 1 |
| 50023 | ChaosStopped | 已停止混沌測試工作階段 | 可測試性 | 資訊 | 1 |

**混沌節點事件**

| EventId | 名稱 | 描述 |來源 (工作) | 層級 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 50033 | ChaosNodeRestartScheduled | 已排定將節點重新啟動為混沌測試工作階段的一部分 | 可測試性 | 資訊 | 1 |
| 50087 | ChaosNodeRestartCompleted | 已完成將節點重新啟動為混沌測試工作階段的一部分 | 可測試性 | 資訊 | 1 |

**混沌應用程式事件**

| EventId | 名稱 | 描述 |來源 (工作) | 層級 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 50053 | ChaosCodePackageRestartScheduled | 已在混沌測試工作階段期間排定程式碼套件重新啟動 | 可測試性 | 資訊 | 1 |
| 50101 | ChaosCodePackageRestartCompleted | 已在混沌測試工作階段期間完成程式碼套件重新啟動 | 可測試性 | 資訊 | 1 |

**混沌分割事件**

| EventId | 名稱 | 描述 |來源 (工作) | 層級 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 50069 | ChaosPartitionPrimaryMoveScheduled | 已排定將主要磁碟分割移為混沌測試工作階段的一部分 | 可測試性 | 資訊 | 1 |
| 50077 | ChaosPartitionSecondaryMoveScheduled | 已排定將次要磁碟分割移為混沌測試工作階段的一部分 | 可測試性 | 資訊 | 1 |
| 65003 | PartitionPrimaryMoveAnalysis | 已可更深入地分析主要磁碟分割的移動 | 可測試性 | 資訊 | 1 |

**混沌複本事件**

| EventId | 名稱 | 描述 |來源 (工作) | 層級 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 50047 | ChaosReplicaRestartScheduled | 已將複本重新啟動排定為混沌測試工作階段的一部份 | 可測試性 | 資訊 | 1 |
| 50051 | ChaosReplicaRemovalScheduled | 已將複本移除排定為混沌測試工作階段的一部份 | 可測試性 | 資訊 | 1 |
| 50093 | ChaosReplicaRemovalCompleted | 已完成將複本移除作為混沌測試工作階段的一部份 | 可測試性 | 資訊 | 1 |

## <a name="other-events"></a>其他事件

**相互關連事件**

| EventId | 名稱 | 描述 |來源 (工作) | 層級 | 版本 |
| --- | --- | ---| --- | --- | --- |
| 65011 | CorrelationOperational | 已偵測到相互關聯 | 可測試性 | 資訊 | 1 |

## <a name="events-prior-to-version-62"></a>6\.2 版之前的事件

以下是 Service Fabric 6.2 版之前所提供事件的完整清單。

| EventId | 名稱 | 來源 (工作) | 層級 |
| --- | --- | --- | --- |
| 25620 | NodeOpening | FabricNode | 資訊 |
| 25621 | NodeOpenedSuccess | FabricNode | 資訊 |
| 25622 | NodeOpenedFailed | FabricNode | 資訊 |
| 25623 | NodeClosing | FabricNode | 資訊 |
| 25624 | NodeClosed | FabricNode | 資訊 |
| 25625 | NodeAborting | FabricNode | 資訊 |
| 25626 | NodeAborted | FabricNode | 資訊 |
| 29627 | ClusterUpgradeStart | CM | 資訊 |
| 29628 | ClusterUpgradeComplete | CM | 資訊 |
| 29629 | ClusterUpgradeRollback | CM | 資訊 |
| 29630 | ClusterUpgradeRollbackComplete | CM | 資訊 |
| 29631 | ClusterUpgradeDomainComplete | CM | 資訊 |
| 23074 | ContainerActivated | 裝載 | 資訊 |
| 23075 | ContainerDeactivated | 裝載 | 資訊 |
| 29620 | ApplicationCreated | CM | 資訊 |
| 29621 | ApplicationUpgradeStart | CM | 資訊 |
| 29622 | ApplicationUpgradeComplete | CM | 資訊 |
| 29623 | ApplicationUpgradeRollback | CM | 資訊 |
| 29624 | ApplicationUpgradeRollbackComplete | CM | 資訊 |
| 29625 | ApplicationDeleted | CM | 資訊 |
| 29626 | ApplicationUpgradeDomainComplete | CM | 資訊 |
| 18566 | ServiceCreated | FM | 資訊 |
| 18567 | ServiceDeleted | FM | 資訊 |

## <a name="next-steps"></a>後續步驟

* 取得 [Service Fabric 中的診斷](service-fabric-diagnostics-overview.md)概觀
* 在 [Service Fabric Eventstore 概觀](service-fabric-diagnostics-eventstore.md)中深入了解 EventStore
* 修改您的 [Azure 診斷](service-fabric-diagnostics-event-aggregation-wad.md)設定以收集更多記錄
* [設定 Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) 以查看您的作業通道記錄
