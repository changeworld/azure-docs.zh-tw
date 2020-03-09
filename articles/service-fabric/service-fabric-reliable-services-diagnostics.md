---
title: Azure Service Fabric 具狀態 Reliable Services 診斷
description: Azure Service Fabric 中可設定狀態 Reliable Services 的診斷功能
author: dkkapur
ms.topic: conceptual
ms.date: 8/24/2018
ms.author: dekapur
ms.openlocfilehash: 37162287e130b05dc41453c579b3a628ac878fca
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388401"
---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>具狀態 Reliable Services 的診斷功能
Azure Service Fabric 具狀態的 Reliable Services StatefulServiceBase 類別會發出[EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx)事件，可用來對服務進行偵錯工具、提供執行時間運作方式的深入解析，以及協助進行疑難排解。

## <a name="eventsource-events"></a>EventSource 事件
具狀態 Reliable Services StatefulServiceBase 類別的 EventSource 名稱是 "Microsoft ServiceFabric-Services"。 當服務[在 Visual Studio 中進行調試](service-fabric-debugging-your-application.md)時，此事件來源的事件會出現在 [[診斷事件](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio)] 視窗中。

可協助您收集和/或查看 EventSource 事件的工具和技術範例包括[PerfView](https://www.microsoft.com/download/details.aspx?id=28567)、 [Azure 診斷](../cloud-services/cloud-services-dotnet-diagnostics.md)和[Microsoft TraceEvent Library](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent)。

## <a name="events"></a>事件
| 事件名稱 | 事件識別碼 | 二級 | 事件描述 |
| --- | --- | --- | --- |
| StatefulRunAsyncInvocation |sha-1 |供 |啟動服務 RunAsync 工作時發出 |
| StatefulRunAsyncCancellation |2 |供 |在服務 RunAsync 工作取消時發出 |
| StatefulRunAsyncCompletion |第 |供 |在服務 RunAsync 工作完成時發出 |
| StatefulRunAsyncSlowCancellation |4gb |Warning |服務 RunAsync 工作花太多時間完成取消時發出 |
| StatefulRunAsyncFailure |第 |糾錯 |在服務 RunAsync 工作擲回例外狀況時發出 |

## <a name="interpret-events"></a>解讀事件
StatefulRunAsyncInvocation、StatefulRunAsyncCompletion 和 StatefulRunAsyncCancellation 事件可讓服務撰寫者瞭解服務的生命週期，以及服務啟動、取消或完成的時機。 這種資訊在調試服務問題或瞭解服務生命週期時很有用。

服務寫入器應密切注意 StatefulRunAsyncSlowCancellation 和 StatefulRunAsyncFailure 事件，因為它們表示服務的問題。

每當服務 RunAsync （）工作擲回例外狀況時，就會發出 StatefulRunAsyncFailure。 通常，擲回的例外狀況表示服務中的錯誤或 bug。 此外，例外狀況會導致服務失敗，因此它會移至不同的節點。 這種作業可能會很耗費資源，而且可能會在服務移動時延遲傳入的要求。 服務寫入器應判斷例外狀況的原因，並在可能的情況下降低其風險。

每當 RunAsync 工作的取消要求花費的時間超過四秒時，就會發出 StatefulRunAsyncSlowCancellation。 當服務完成取消所花費的時間太久，它會影響服務在另一個節點上快速重新開機的能力。 此案例可能會影響服務的整體可用性。

## <a name="performance-counters"></a>效能計數器
Reliable Services 執行時間會定義下列效能計數器類別：

| Category | 描述 |
| --- | --- |
| Service Fabric 異動複寫器 |Azure Service Fabric 交易式複寫器特定的計數器 |
| Service Fabric TStore |Azure Service Fabric TStore 特定的計數器 |

[可靠狀態管理員](service-fabric-reliable-services-reliable-collections-internals.md)會使用 Service Fabric 交易式複寫器來複寫指定[複本](service-fabric-concepts-replica-lifecycle.md)集內的交易。

Service Fabric TStore 是用來在可靠的[集合](service-fabric-reliable-services-reliable-collections-internals.md)中儲存和抓取索引鍵/值組的元件。

Windows 作業系統中預設可用的[Windows 效能監視器](https://technet.microsoft.com/library/cc749249.aspx)應用程式，可以用來收集和查看效能計數器資料。 [Azure 診斷](../cloud-services/cloud-services-dotnet-diagnostics.md)是另一個收集效能計數器資料，並將它上傳至 Azure 資料表的選項。

### <a name="performance-counter-instance-names"></a>效能計數器實例名稱
具有大量可靠服務或可靠服務分割區的叢集，會有大量的交易式複寫器效能計數器實例。 這也是 TStore 效能計數器的情況，但也會乘以所使用的可靠字典和可靠佇列的數目。 效能計數器實例名稱可以協助識別效能計數器實例相關聯的特定資料[分割](service-fabric-concepts-partitioning.md)、服務複本和狀態提供者（TStore 時）。

#### <a name="service-fabric-transactional-replicator-category"></a>Service Fabric 交易式複製器類別
針對 類別目錄 `Service Fabric Transactional Replicator`，計數器實例名稱的格式如下：

`ServiceFabricPartitionId:ServiceFabricReplicaId`

*ServiceFabricPartitionId*是與效能計數器實例相關聯之 Service Fabric 分割區識別碼的字串表示。 資料分割識別碼是 GUID，其字串標記法是透過格式規範為 "D" 的[`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx)所產生。

*ServiceFabricReplicaId*是與可靠服務的指定複本相關聯的識別碼。 複本識別碼包含在效能計數器實例名稱中，以確保其唯一性，並避免與相同分割區所產生的其他效能計數器實例發生衝突。 如需複本及其在可靠服務中的角色的詳細資訊，請參閱[這裡](service-fabric-concepts-replica-lifecycle.md)。

下列計數器實例名稱一般適用于 [`Service Fabric Transactional Replicator`] 類別下的計數器：

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571`

在上述範例中，`00d0126d-3e36-4d68-98da-cc4f7195d85e` 是 Service Fabric 資料分割識別碼的字串表示，而 `131652217797162571` 是複本識別碼。

#### <a name="service-fabric-tstore-category"></a>Service Fabric TStore 類別
針對 類別目錄 `Service Fabric TStore`，計數器實例名稱的格式如下：

`ServiceFabricPartitionId:ServiceFabricReplicaId:StateProviderId_PerformanceCounterInstanceDifferentiator_StateProviderName`

*ServiceFabricPartitionId*是與效能計數器實例相關聯之 Service Fabric 分割區識別碼的字串表示。 資料分割識別碼是 GUID，其字串標記法是透過格式規範為 "D" 的[`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx)所產生。

*ServiceFabricReplicaId*是與可靠服務的指定複本相關聯的識別碼。 複本識別碼包含在效能計數器實例名稱中，以確保其唯一性，並避免與相同分割區所產生的其他效能計數器實例發生衝突。 如需複本及其在可靠服務中的角色的詳細資訊，請參閱[這裡](service-fabric-concepts-replica-lifecycle.md)。

*StateProviderId*是與可靠服務內的狀態供應器相關聯的識別碼。 狀態提供者識別碼包含在效能計數器實例名稱中，以區別 TStore 與另一個。

*PerformanceCounterInstanceDifferentiator*是與狀態提供者中的效能計數器實例相關聯的區分識別碼。 這項區別會包含在效能計數器實例名稱中，以確保其唯一性，並避免與相同狀態提供者所產生的其他效能計數器實例發生衝突。

*StateProviderName*是與可靠服務內的狀態供應器相關聯的名稱。 狀態提供者名稱會包含在效能計數器實例名稱中，以供使用者輕鬆地識別它所提供的狀態。

下列計數器實例名稱一般適用于 [`Service Fabric TStore`] 類別下的計數器：

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571:142652217797162571_1337_urn:MyReliableDictionary/dataStore`

在上述範例中，`00d0126d-3e36-4d68-98da-cc4f7195d85e` 是 Service Fabric 資料分割識別碼的字串表示，`131652217797162571` 是複本識別碼，`142652217797162571` 是狀態提供者識別碼，而 `1337` 是效能計數器實例的區別。 `urn:MyReliableDictionary/dataStore` 是儲存名為 `urn:MyReliableDictionary`之集合資料的狀態供應器名稱。

### <a name="transactional-replicator-performance-counters"></a>異動複寫器效能計數器

Reliable Services 執行時間會在 `Service Fabric Transactional Replicator` 類別下發出下列事件

 計數器名稱 | 描述 |
| --- | --- |
| Begin Txn 作業數/秒 | 每秒建立的新寫入交易數目。|
| Txn 作業數/秒 | 每秒在可靠的集合上執行的新增/更新/刪除作業數目。|
| 記錄檔排清位元組/秒 | 異動複寫器每秒排清到磁片的位元組數目 |
| 節流作業數/秒 | 異動複寫器每秒因為節流而拒絕的作業數目。 |
| 平均交易毫秒/認可 | 每一交易的平均認可延遲（以毫秒為單位） |
| Avg. Flush 延遲（毫秒） | 異動複寫器起始之磁片排清作業的平均持續時間（以毫秒為單位） |

### <a name="tstore-performance-counters"></a>TStore 效能計數器

Reliable Services 執行時間會在 `Service Fabric TStore` 類別下發出下列事件

 計數器名稱 | 描述 |
| --- | --- |
| 專案計數 | 存放區中的專案數。|
| 磁片大小 | 存放區檢查點檔案的磁片大小總計（以位元組為單位）。|
| 檢查點檔案寫入位元組/秒 | 最近檢查點檔案的每秒寫入位元組數。|
| 複製磁片傳輸位元組/秒 | 存放區複本時，每秒讀取的磁片位元組數（在主要複本上）或寫入（在次要複本上）。|

## <a name="next-steps"></a>後續步驟
[PerfView 中的 EventSource 提供者](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
