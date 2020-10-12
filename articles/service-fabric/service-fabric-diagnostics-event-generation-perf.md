---
title: Azure Service Fabric 效能監視
description: 了解用於監視及診斷 Azure Service Fabric 叢集的效能計數器。
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 30b9b8393007033a7c2e6798cd57d9cf0128820d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "75464713"
---
# <a name="performance-metrics"></a>效能度量

您應該收集計量，以了解叢集及在叢集中執行之應用程式的效能。 針對 Service Fabric 叢集，建議您收集下列效能計數器。

## <a name="nodes"></a>節點

針對您叢集中的機器，請考慮收集下列效能計數器，以進一步了解每部機器上的負載，並做出適當的叢集調整決策。

| 計數器類別 | 計數器名稱 |
| --- | --- |
| 邏輯磁碟 | 邏輯磁碟可用空間 |
| PhysicalDisk(per Disk) | 平均磁片讀取佇列長度 |
| PhysicalDisk(per Disk) | 平均磁片寫入佇列長度 |
| PhysicalDisk(per Disk) | Avg.Disk sec/Read |
| PhysicalDisk(per Disk) | Avg.Disk sec/Write |
| PhysicalDisk(per Disk) | Disk Reads/sec |
| PhysicalDisk(per Disk) | Disk Read Bytes/sec |
| PhysicalDisk(per Disk) | Disk Writes/sec |
| PhysicalDisk(per Disk) | Disk Write Bytes/sec |
| 記憶體 | 可用的 MB |
| PagingFile | % 使用量 |
| Processor(Total) | % Processor Time |
| Process (per service) | % Processor Time |
| Process (per service) | 識別碼處理序 |
| Process (per service) | 私用位元組 |
| Process (per service) | 執行緒計數 |
| Process (per service) | 虛擬位元組 |
| Process (per service) | 工作集 |
| Process (per service) | 工作集 - 私用 |
| Network Interface(all-instances) | 記錄的位元組 |
| Network Interface(all-instances) | 傳送的位元組數 |
| Network Interface(all-instances) | 位元組總數 |
| Network Interface(all-instances) | 輸出佇列長度 |
| Network Interface(all-instances) | 已丟棄輸出封包 |
| Network Interface(all-instances) | 已丟棄接收封包 |
| Network Interface(all-instances) | 輸出封包錯誤 |
| Network Interface(all-instances) | 已收到封包錯誤 |

## <a name="net-applications-and-services"></a>.NET 應用程式與服務

如果您要將 .NET 服務部署到叢集，請收集下列計數器。 

| 計數器類別 | 計數器名稱 |
| --- | --- |
| .NET CLR 記憶體 (每一服務) | 處理序識別碼 |
| .NET CLR 記憶體 (每一服務) | 認可的位元組總數 |
| .NET CLR 記憶體 (每一服務) | 保留的位元組總數 |
| .NET CLR 記憶體 (每一服務) | 所有堆積中的位元組數 |
| .NET CLR 記憶體 (每一服務) | 大型物件堆積大小 |
| .NET CLR 記憶體 (每一服務) | # GC Handles |
| .NET CLR 記憶體 (每一服務) | 層代 0 集合數 |
| .NET CLR 記憶體 (每一服務) | 層代 1 集合數 |
| .NET CLR 記憶體 (每一服務) | 層代 2 集合數 |
| .NET CLR 記憶體 (每一服務) | 記憶體回收中的時間 % |

### <a name="service-fabrics-custom-performance-counters"></a>Service Fabric 的自訂效能計數器

Service Fabric 可產生大量的自訂效能計數器。 如果您已安裝 SDK，就可以在 Windows 機器上的 [效能監視器] 應用程式 ([開始] > [效能監視器]) 中看到完整的清單。 

在您部署到叢集的應用程式中，如果您使用 Reliable Actors，請從 `Service Fabric Actor` 和 `Service Fabric Actor Method` 類別新增計數器 (請參閱 [Service Fabric Reliable Actors 診斷](service-fabric-reliable-actors-diagnostics.md))。

如果您使用 Reliable Services 或服務遠端，我們同樣擁有應從其中收集計數器的 `Service Fabric Service` 和 `Service Fabric Service Method` 計數器類別，請參閱[使用服務遠端進行監視](service-fabric-reliable-serviceremoting-diagnostics.md)和[可靠服務效能計數器](service-fabric-reliable-services-diagnostics.md#performance-counters)。 

如果您使用「可靠的集合」，建議您從 `Service Fabric Transactional Replicator` 新增 `Avg. Transaction ms/Commit`，以收集每一交易的平均認可延遲計量。


## <a name="next-steps"></a>後續步驟

* 深入了解 Service Fabric 中的[平台層級的事件產生](service-fabric-diagnostics-event-generation-infra.md)
* 透過 [Log Analytics 代理程式](service-fabric-diagnostics-oms-agent.md)收集效能計量
