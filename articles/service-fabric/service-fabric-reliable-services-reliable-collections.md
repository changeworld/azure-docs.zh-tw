---
title: 可靠的集合簡介
description: Service Fabric 具狀態服務提供可靠的集合，可讓您撰寫高度可用、可調整且低延遲的雲端應用程式。
ms.topic: conceptual
ms.date: 3/10/2020
ms.openlocfilehash: 6e0f39b994087fe44038e62e85326945589c719a
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86245121"
---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Azure Service Fabric 具狀態服務中可靠的集合簡介

可靠的集合可讓您撰寫高度可用、可擴充且低延遲的雲端應用程式，如同您在撰寫單一電腦應用程式一般。 Microsoft.ServiceFabric.Data.Collections**** 命名空間中的類別提供一組集合，可讓您自動具有高度可用的狀態。 開發人員只需將程式設計為可靠的集合 API，並讓可靠的集合來管理複寫和本機狀態。

可靠的集合和其他高可用性技術 (例如 Redis、Azure 表格服務和 Azure 佇列服務) 之間的主要差異是狀態會保留在本機服務執行個體中，但同時也被設定為高可用性。 這表示：

* 所有讀取都在本機，可保障低延遲及高輸送量讀取。
* 所有寫入都只會產生最少的網路 IO 數，可保障低延遲和高輸送量寫入。

![集合的演化圖。](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

我們可將可靠的集合視為 **System.Collections** 類別的自然進化：它們是一組新的集合，專為雲端和多電腦應用程式量身設計，且不會對開發人員提高複雜度。 因此，可靠的集合是：

* 可複寫：進行狀態變更複寫以確保高可用性。
* 非同步：API 是非同步的，可確保在產生 IO 時不會封鎖執行緒。
* 交易式：API 會利用交易的抽象方法，讓您能夠輕鬆管理服務內多個可靠的集合。
* 保存或變動：資料可以保存到磁片中，以因應大規模中斷 (例如，資料中心斷電) 。 某些可靠的集合也支援變動性模式 (加上[警告](service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections)) 其中所有資料都會保留在記憶體中，例如複寫的記憶體內部快取。

Reliable Collection 具有增強式一致性保證，可讓您更輕鬆地推論應用程式的狀態。
增強式一致性的實現方式是藉由確保僅有在整個交易已記錄到複本多數仲裁之後 (包括主要複本)，才認可交易。
若要達成較弱的一致性，應用程式可在非同步認可傳回之前，返回向用戶端/要求者進行確認。

可靠的集合 API 是並行集合 API (位於 **System.Collections.Concurrent** 命名空間) 的一種演化：

* 非同步：會傳回工作；不同於並行集合，其作業會受到複寫及保存。
* 沒有 out 參數：使用傳回 `ConditionalValue<T>` `bool` 和值，而不是 out 參數。 `ConditionalValue<T>` 就像 `Nullable<T>`，但不需要 T 就可以成為結構。
* 交易：使用交易物件，讓使用者可在交易中的多個可靠的集合上群組動作。

現在，Microsoft.ServiceFabric.Data.Collections**** 包含三個集合：

* [可靠的字典](/dotnet/api/microsoft.servicefabric.data.collections.ireliabledictionary-2?view=azure-dotnet#microsoft_servicefabric_data_collections_ireliabledictionary_2)：表示可複寫、交易式和非同步索引鍵/值組的集合。 類似於 **ConcurrentDictionary**，其索引鍵和值可以是任何類型。
* [可靠的佇列](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1?view=azure-dotnet#microsoft_servicefabric_data_collections_ireliablequeue_1)：表示可複寫、交易式和非同步的嚴格先進先出 (FIFO) 佇列。 類似於 **ConcurrentQueue**，其值可以是任何類型。
* [可靠的並行佇列](service-fabric-reliable-services-reliable-concurrent-queue.md)︰代表儘可能最佳的複寫、交易與非同步排序序列，以達到高輸送量。 類似於 ConcurrentQueue****，其值可以是任何類型。

## <a name="next-steps"></a>後續步驟

* [可靠的集合指導方針 & 建議](service-fabric-reliable-services-reliable-collections-guidelines.md)
* [使用可靠的集合](service-fabric-work-with-reliable-collections.md)
* [交易和鎖定](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* 管理資料
  * [備份與還原](service-fabric-reliable-services-backup-restore.md)
  * [通知](service-fabric-reliable-services-notifications.md)
  * [Reliable Collection 序列化](service-fabric-reliable-services-reliable-collections-serialization.md)
  * [序列化與升級](service-fabric-application-upgrade-data-serialization.md)
  * [Reliable State Manager 組態](service-fabric-reliable-services-configuration.md)
* 其他
  * [Reliable Services 快速入門](service-fabric-reliable-services-quick-start.md)
  * [可靠的集合的開發人員參考資料](/dotnet/api/microsoft.servicefabric.data.collections?view=azure-dotnet#microsoft_servicefabric_data_collections)
