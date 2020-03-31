---
title: Azure 服務匯流排高級層和標準層
description: 本文介紹了 Azure 服務匯流排的標準層和高級層。 比較這些層並提供技術差異。
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: e211774d-821c-4d79-8563-57472d746c58
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: ef3cc8d4c7354b43389244e72c2dbc5899b8db25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774556"
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>服務匯流排進階和標準傳訊層級

服務匯流排傳訊 (包含佇列和主題等實體) 結合雲端級別的企業傳訊功能與豐富的發佈-訂閱語意。 服務匯流排傳訊作為許多縝密雲端解決方案的通訊骨幹。

*服務*匯流排消息的高級層可針對任務關鍵型應用程式的規模、性能和可用性解決常見客戶請求。 針對生產案例，建議使用進階層。 雖然功能組幾乎完全相同，但這兩層級的服務匯流排傳訊設計用來服務不同的使用案例。

下表強調大致的一些差異。

| Premium | 標準 |
| --- | --- |
| 高輸送量 |變動輸送量 |
| 可預測的效能 |變動延遲 |
| 固定價格 |隨用隨付變動價格 |
| 相應增加和相應減少工作負載的能力 |N/A |
| 訊息大小上限為 1 MB |訊息大小上限為 256 KB |

**服務匯流排進階傳訊**提供 CPU 和記憶體層級的資源隔離，讓每個客戶工作負載能隔離執行。 此資源容器稱為*消息傳遞單元*。 每個進階命名空間都會被配置至少一個傳訊單位。 您可以為每個服務匯流排高級命名空間購買 1、2、4 或 8 個消息傳遞單元。 單個工作負載或實體可以跨多個消息傳遞單元，並且可以更改消息傳遞單元的數量。 結果是您的服務匯流排方案的效能可預測並可重複。

此效能不僅更可預測並可取得，而且還更快速。 服務匯流排高級消息基於[Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)中引入的儲存引擎。 使用進階訊息，尖峰效能的速度比標準層級的速度快很多。

## <a name="premium-messaging-technical-differences"></a>進階傳訊技術差異

下列各節討論進階與標準傳訊層之間的一些差異。

### <a name="partitioned-queues-and-topics"></a>分割的佇列和主題

進階傳訊中不支援分割的佇列和主題。 如需分割的詳細資訊，請參閱[分割的佇列和主題](service-bus-partitioning.md)。

### <a name="express-entities"></a>快速實體

因為進階傳訊是在完全隔離的執行階段環境中執行，所以在進階命名空間中並不支援快速實體。 如需快速功能的詳細資訊，請參閱 [QueueDescription.EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) 屬性。

如果您的程式碼是在標準傳訊下執行，而您想要將它移植到高階層，請確定 [EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) 屬性設為 **false** (預設值)。

## <a name="premium-messaging-resource-usage"></a>高級消息資源使用方式
通常，實體上的任何操作都可能導致 CPU 和記憶體使用。 下面是一些操作： 

- 管理操作，如 CRUD（創建、檢索、更新和刪除）佇列、主題和訂閱的操作。
- 運行時操作（發送和接收消息）
- 監視操作和警報

但是，額外的 CPU 和記憶體使用量不會另加定價。 對於高級消息傳遞層，消息單元只有一個價格。

CPU 和記憶體使用方式被跟蹤並顯示給您，原因如下： 

- 為系統內部提供透明度
- 瞭解所購買資源的能力。
- 説明您決定向上/縮小規模的容量規劃。

## <a name="messaging-unit---how-many-are-needed"></a>消息傳遞單元 - 需要多少？

預配 Azure 服務匯流排高級命名空間時，必須指定分配的消息傳遞單元數。 這些消息傳遞單元是分配給命名空間的專用資源。

分配給服務匯流排高級命名空間的消息傳遞單元數可以**動態調整**，以考慮工作負載的變化（增加或減少）。

在決定體系結構的消息傳遞單元數時，需要考慮許多因素：

- 從分配給命名空間的***1 個或 2 個消息傳遞單元***開始。
- 研究命名空間的資源使用指標中的 CPU[使用率指標](service-bus-metrics-azure-monitor.md#resource-usage-metrics)。
    - 如果 CPU 使用率***低於 20%，*** 則可以***縮減***分配給命名空間的消息傳遞單元數。
    - 如果 CPU 使用率***超過 70%，*** 則應用程式將受益于***擴展***分配給命名空間的消息傳遞單元數。

可以使用[Azure 自動化 Runbook](../automation/automation-quickstart-create-runbook.md)自動擴展分配給服務匯流排命名空間的資源的過程。

> [!NOTE]
> **分配給**命名空間的資源的縮放可以是先發制人的，也可以是被動的。
>
>  * **搶佔**：如果預期會增加工作負載（由於季節性或趨勢），則可以在工作負載命中之前將更多消息傳遞單元分配給命名空間。
>
>  * **反應：** 如果通過研究資源使用方式指標來識別其他工作負荷，則可以將其他資源配置給命名空間以合併不斷增長的需求。
>
> 服務匯流排的計費表每小時一次。 在放大的情況下，您只需為使用這些資源的小時數支付額外資源。
>

## <a name="get-started-with-premium-messaging"></a>開始使用進階傳訊

開始使用進階訊息非常簡單，其程序類似於標準傳訊。 首先在 [Azure 入口網站](https://portal.azure.com)中[建立命名空間](service-bus-create-namespace-portal.md)。 請確保在**定價層**下選擇 **"高級"。** 按一下 [檢視完整定價詳細資料]**** 以查看每一層的詳細資訊。

![create-premium-namespace][create-premium-namespace]

您也可以[使用 Azure Resource Manager 範本建立進階命名空間](https://azure.microsoft.com/resources/templates/101-servicebus-pn-ar/)。

## <a name="next-steps"></a>後續步驟

若要深入了解服務匯流排傳訊，請參閱下列連結：

* [介紹 Azure 服務匯流排高級消息（博客文章）](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Azure 服務匯流排進階傳訊簡介 (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [服務匯流排消息概述](service-bus-messaging-overview.md)
* [開始使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
