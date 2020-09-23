---
title: Azure 服務匯流排 premium 和標準層
description: 本文描述 Azure 服務匯流排的標準層和進階層。 比較這些層級，並提供技術差異。
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 31c53a1375078cd5d185945cba55a6e5a6dd5ffb
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90966788"
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>服務匯流排進階和標準傳訊層級

服務匯流排傳訊 (包含佇列和主題等實體) 結合雲端級別的企業傳訊功能與豐富的發佈-訂閱語意。 服務匯流排傳訊作為許多縝密雲端解決方案的通訊骨幹。

服務匯流排訊息的進階層可解決針對要徑任務應用 *程式的規模* 、效能和可用性等常見客戶要求。 針對生產案例，建議使用進階層。 雖然功能組幾乎完全相同，但這兩層級的服務匯流排傳訊設計用來服務不同的使用案例。

下表強調大致的一些差異。

| Premium | 標準 |
| --- | --- |
| 高輸送量 |變動輸送量 |
| 可預測的效能 |變動延遲 |
| 固定價格 |隨用隨付變動價格 |
| 相應增加和相應減少工作負載的能力 |N/A |
| 訊息大小上限為 1 MB。 未來可能會引發這項限制。 如需服務的最新重要更新，請參閱 [Azure blog 上的訊息](https://techcommunity.microsoft.com/t5/messaging-on-azure/bg-p/MessagingonAzureBlog)。 |訊息大小上限為 256 KB |

**服務匯流排進階傳訊**提供 CPU 和記憶體層級的資源隔離，讓每個客戶工作負載能隔離執行。 此資源容器稱為 *訊息單位*。 每個進階命名空間都會被配置至少一個傳訊單位。 您可以針對每個服務匯流排 Premium 命名空間購買1、2、4或8個訊息單位。 單一工作負載或實體可以橫跨多個訊息單位，而且可以隨時變更訊息單位數目。 結果是您的服務匯流排方案的效能可預測並可重複。

此效能不僅更可預測並可取得，而且還更快速。 服務匯流排 Premium 訊息是以 [Azure 事件中樞](https://azure.microsoft.com/services/event-hubs/)引進的儲存引擎為基礎。 使用進階訊息，尖峰效能的速度比標準層級的速度快很多。

## <a name="premium-messaging-technical-differences"></a>進階傳訊技術差異

下列各節討論進階與標準傳訊層之間的一些差異。

### <a name="partitioned-queues-and-topics"></a>分割的佇列和主題

Premium 訊息中不支援分割的佇列和主題。 如需分割的詳細資訊，請參閱[分割的佇列和主題](service-bus-partitioning.md)。

### <a name="express-entities"></a>快速實體

由於高階訊息是在隔離的執行時間環境中執行，因此 Premium 命名空間中不支援快速實體。 如需快速功能的詳細資訊，請參閱 [QueueDescription.EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) 屬性。

如果您的程式碼是在標準傳訊下執行，而您想要將它移植到高階層，請確定 [EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) 屬性設為 **false** (預設值)。

## <a name="premium-messaging-resource-usage"></a>Premium 訊息資源使用量
一般來說，實體上的任何作業可能會造成 CPU 和記憶體使用量。 以下是其中一些作業： 

- CRUD (建立、取出、更新和刪除佇列、主題和訂用帳戶) 作業等管理作業。
- 執行時間作業 (傳送和接收訊息) 
- 監視作業和警示

除此之外，額外的 CPU 和記憶體使用量也不是定價。 針對 Premium 訊息層，訊息單位有單一價格。

系統會追蹤並顯示 CPU 和記憶體使用量，原因如下： 

- 提供透明的系統內部
- 瞭解購買的資源容量。
- 容量規劃可協助您決定要擴大/縮小。

## <a name="messaging-unit---how-many-are-needed"></a>訊息單位-需要多少？

布建 Azure 服務匯流排 Premium 命名空間時，必須指定所配置的訊息單位數目。 這些訊息單位是配置給命名空間的專用資源。

配置給服務匯流排高階命名空間的訊息單位數目可以 **動態調整** 以考慮變更 (增加或減少工作負載中的) 。

在決定您的架構的訊息單位數目時，需要考慮幾個因素：

- 從配置給您的命名空間的 ***1 或2個訊息單位*** 開始。
- 研究您的命名空間的 [資源使用計量](service-bus-metrics-azure-monitor.md#resource-usage-metrics) 中的 CPU 使用量計量。
    - 如果 CPU 使用率 ***低於 20%***，您可能可以 ***縮小*** 配置給命名空間的訊息單位數目。
    - 如果 CPU 使用率 ***高於 70%***，則您的應用程式將可受益于 ***調整配置*** 給命名空間的訊息單位數目。

若要瞭解如何設定服務匯流排命名空間，以自動調整 (增加或減少訊息單位) ，請參閱 [自動更新訊息單位](automate-update-messaging-units.md)。

> [!NOTE]
> 配置給命名空間的資源**調整**可以是先占式或被動。
>
>  * 先占**式：如果**預期需要額外的工作負載 (由於季節性或趨勢) ，您可以在工作負載達到之前，繼續將更多訊息單位配置給命名空間。
>
>  * **回應**：如果透過研究資源使用計量來識別額外的工作負載，則可以將額外的資源配置給命名空間，以納入增加的需求。
>
> 服務匯流排的計費計量為每小時。 在相應增加的情況下，您只需針對這些使用的時數支付額外資源的費用。
>

## <a name="get-started-with-premium-messaging"></a>開始使用進階傳訊

開始使用進階訊息非常簡單，其程序類似於標準傳訊。 首先在 [Azure 入口網站](https://portal.azure.com)中[建立命名空間](service-bus-create-namespace-portal.md)。 請確定選取 [**定價層**] 下的 [ **Premium** ]。 按一下 [檢視完整定價詳細資料]**** 以查看每一層的詳細資訊。

![create-premium-namespace][create-premium-namespace]

您也可以[使用 Azure Resource Manager 範本建立進階命名空間](https://azure.microsoft.com/resources/templates/101-servicebus-pn-ar/)。

## <a name="next-steps"></a>下一步

若要深入了解服務匯流排傳訊，請參閱下列連結：

- [自動更新訊息單位](automate-update-messaging-units.md)。
- [簡介 Azure 服務匯流排 Premium 訊息 (blog 文章) ](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [Azure 服務匯流排進階傳訊簡介 (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
