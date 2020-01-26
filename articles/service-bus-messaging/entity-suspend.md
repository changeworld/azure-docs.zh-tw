---
title: Azure 服務匯流排-暫止訊息實體
description: 本文說明如何暫時暫停和重新開機 Azure 服務匯流排訊息實體（佇列、主題和訂用帳戶）。
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 7386932f19eee064926184eb17f5e92e30add98e
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760380"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>暫止及重新啟動傳訊實體 (停用)

您可以暫時停止佇列、主題和訂用帳戶。 暫止會讓實體進入停用狀態，此時所有訊息都會保存在儲存體中。 不過，訊息無法移除或新增，而且個別的通訊協定作業都會產生錯誤。

暫止實體通常是因為緊急管理的緣故。 其中一個案例是部署了錯誤的接收者，將訊息從佇列中移除、無法處理，而且無法正確地完成訊息及移除訊息。 如果診斷出該行為，您可以停用接收佇列，直到部署更正的程式碼，而且可以預防錯誤程式碼造成更嚴重的資料遺失為止。

暫止或重新啟動可以由使用者或系統執行。 系統只會因為重大的管理原因暫止實體，如達到訂用帳戶消費限制。 使用者無法重新啟動由系統停用的實體，不過當導致暫止的原因排除後實體將能恢復正常狀態。

在入口網站中，個別實體的 [**屬性**] 區段可讓您變更狀態;下列螢幕擷取畫面顯示佇列的切換：

![][1]

入口網站只允許完全停止佇列。 您也可以使用 .NET Framework SDK 中的服務匯流排 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API 分別停用傳送和接收作業，或是透過 Azure CLI 或 Azure PowerShell 使用 Azure Resource Manager 範本來達成。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="suspension-states"></a>暫止狀態

可以為佇列設定的狀態為：

-   **Active**：佇列作用中。
-   **Disabled**：佇列已暫止。
-   **SendDisabled**：佇列已部分暫止，目前允許接收。
-   **ReceiveDisabled**：佇列已部分暫止，目前允許傳送。

對於訂用帳戶和主題，您只能設定 **Active** 和 **Disabled**。

[EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) 列舉另定義一組只能由系統設定的可轉換狀態。 以下範例展示停用佇列的 PowerShell 命令。 重新啟動命令與前述相同，它會將 `Status` 設定為 **Active**。

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="next-steps"></a>後續步驟

若要深入了解服務匯流排傳訊，請參閱下列主題：

* [服務匯流排佇列、主題和訂用帳戶](service-bus-queues-topics-subscriptions.md)
* [開始使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服務匯流排主題和訂用帳戶](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/queue-disable.png

