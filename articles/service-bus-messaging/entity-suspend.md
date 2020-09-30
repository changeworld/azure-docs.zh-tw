---
title: Azure 服務匯流排-暫止訊息實體
description: 本文說明如何)  (佇列、主題和訂用帳戶，暫時暫停和重新開機 Azure 服務匯流排的訊息實體。
ms.topic: article
ms.date: 09/29/2020
ms.openlocfilehash: f89e17e494cc777691b7f7ca47538cd29114d2dc
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91575217"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>暫止及重新啟動傳訊實體 (停用)

您可以暫時停止佇列、主題和訂用帳戶。 暫止會讓實體進入停用狀態，此時所有訊息都會保存在儲存體中。 不過，訊息無法移除或新增，而且個別的通訊協定作業都會產生錯誤。

暫止實體通常是因為緊急管理的緣故。 其中一個案例是部署了錯誤的接收者，將訊息從佇列中移除、無法處理，而且無法正確地完成訊息及移除訊息。 如果診斷出該行為，您可以停用接收佇列，直到部署更正的程式碼，而且可以預防錯誤程式碼造成更嚴重的資料遺失為止。

暫止或重新啟動可以由使用者或系統執行。 系統只會因為重大的管理原因暫止實體，如達到訂用帳戶消費限制。 使用者無法重新啟動由系統停用的實體，不過當導致暫止的原因排除後實體將能恢復正常狀態。

## <a name="queue-status"></a>佇列狀態 
可以為佇列設定的狀態為：

-   **Active**：佇列作用中。
-   **Disabled**：佇列已暫止。 這相當於設定 **SendDisabled** 和 **ReceiveDisabled**。 
-   **SendDisabled**：佇列已部分暫止，目前允許接收。
-   **ReceiveDisabled**：佇列已部分暫止，目前允許傳送。

### <a name="change-the-queue-status-in-the-azure-portal"></a>變更 Azure 入口網站中的佇列狀態： 

1. 在 Azure 入口網站中，流覽至您的服務匯流排命名空間。 
1. 選取您要變更狀態的佇列。 您會在中間窗格的底部看到佇列。 
1. 在 [ **服務匯流排佇列** ] 頁面上，以超連結的形式查看佇列的目前狀態。 如果未在左側功能表上選取 **總覽** ，請選取它以查看佇列的狀態。 選取佇列的目前狀態來變更它。 

    :::image type="content" source="./media/entity-suspend/select-state.png" alt-text="選取佇列的狀態":::
4. 選取佇列的新狀態，然後選取 **[確定]**。 

    :::image type="content" source="./media/entity-suspend/entity-state-change.png" alt-text="選取佇列的狀態":::
    
入口網站只允許完全停止佇列。 您也可以使用 .NET Framework SDK 中的服務匯流排 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API 分別停用傳送和接收作業，或是透過 Azure CLI 或 Azure PowerShell 使用 Azure Resource Manager 範本來達成。

### <a name="change-the-queue-status-using-azure-powershell"></a>使用 Azure PowerShell 變更佇列狀態
以下範例展示停用佇列的 PowerShell 命令。 重新啟動命令與前述相同，它會將 `Status` 設定為 **Active**。

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="topic-status"></a>主題狀態
變更 Azure 入口網站中的主題狀態類似于變更佇列的狀態。 當您選取主題的目前狀態時，您會看到可讓您變更狀態的下列頁面。 

:::image type="content" source="./media/entity-suspend/topic-state-change.png" alt-text="選取佇列的狀態":::

可以針對主題設定的狀態包括：
- 作用**中：主題**為作用中。
- **Disabled**：主題已暫止。
- **SendDisabled**：與 **Disabled**相同的效果。

## <a name="subscription-status"></a>訂用帳戶狀態
變更 Azure 入口網站中的訂用帳戶狀態，類似于變更主題或佇列的狀態。 當您選取訂用帳戶的目前狀態時，您會看到可讓您變更狀態的下列頁面。 

:::image type="content" source="./media/entity-suspend/subscription-state-change.png" alt-text="選取佇列的狀態":::

可以針對主題設定的狀態包括：
- 作用**中：主題**為作用中。
- **Disabled**：主題已暫止。
- **ReceiveDisabled**：與 **Disabled**相同的效果。

## <a name="other-statuses"></a>其他狀態
[EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) 列舉另定義一組只能由系統設定的可轉換狀態。 


## <a name="next-steps"></a>後續步驟

若要深入了解服務匯流排傳訊，請參閱下列主題：

* [服務匯流排佇列、主題和訂用帳戶](service-bus-queues-topics-subscriptions.md)
* [開始使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服務匯流排主題和訂用帳戶](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/entity-state-change.png

