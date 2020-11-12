---
title: Azure 服務匯流排-暫止訊息實體
description: 本文說明如何)  (佇列、主題和訂用帳戶，暫時暫停和重新開機 Azure 服務匯流排的訊息實體。
ms.topic: article
ms.date: 09/29/2020
ms.openlocfilehash: ea1acab3d0a86b0064f8b3eef7bfd1496bd17041
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94543046"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>暫止及重新啟動傳訊實體 (停用)

您可以暫時停止佇列、主題和訂用帳戶。 暫止會讓實體進入停用狀態，此時所有訊息都會保存在儲存體中。 不過，訊息無法移除或新增，而且個別的通訊協定作業都會產生錯誤。

基於緊急的管理理由，您可能會想要暫停實體。 例如，錯誤的接收者會將訊息從佇列中取出、處理失敗，但不正確地完成訊息並將其移除。 在此情況下，您可能會想要停用接收的佇列，直到您更正和部署程式碼為止。 

暫止或重新啟動可以由使用者或系統執行。 由於系統管理的原因，例如達到訂用帳戶的消費限制，因此系統只會暫停實體。 使用者無法重新啟動由系統停用的實體，不過當導致暫止的原因排除後實體將能恢復正常狀態。

## <a name="queue-status"></a>佇列狀態 
可以針對 **佇列** 設定的狀態包括：

-   **Active** ：佇列作用中。 您可以將訊息傳送至佇列，並從佇列接收訊息。 
-   **Disabled** ：佇列已暫止。 這相當於設定 **SendDisabled** 和 **ReceiveDisabled** 。 
-   **SendDisabled** ：您無法將訊息傳送至佇列，但您可以從中接收訊息。 如果您嘗試將訊息傳送至佇列，您將會收到例外狀況。 
-   **ReceiveDisabled** ：您可以將訊息傳送至佇列，但無法從中接收訊息。 如果您嘗試接收佇列的訊息，您將會收到例外狀況。


### <a name="change-the-queue-status-in-the-azure-portal"></a>變更 Azure 入口網站中的佇列狀態： 

1. 在 Azure 入口網站中，流覽至您的服務匯流排命名空間。 
1. 選取您要變更狀態的佇列。 您會在中間窗格的底部看到佇列。 
1. 在 [ **服務匯流排佇列** ] 頁面上，以超連結的形式查看佇列的目前狀態。 如果未在左側功能表上選取 **總覽** ，請選取它以查看佇列的狀態。 選取佇列的目前狀態來變更它。 

    :::image type="content" source="./media/entity-suspend/select-state.png" alt-text="選取佇列的狀態":::
4. 選取佇列的新狀態，然後選取 **[確定]** 。 

    :::image type="content" source="./media/entity-suspend/entity-state-change.png" alt-text="設定佇列的狀態":::
    
您也可以使用 .NET SDK 中的服務匯流排 [>namespacemanager](/dotnet/api/microsoft.servicebus.namespacemanager) api 來停用傳送和接收作業，或是透過 Azure CLI 或 Azure PowerShell 使用 Azure Resource Manager 範本。

### <a name="change-the-queue-status-using-azure-powershell"></a>使用 Azure PowerShell 變更佇列狀態
以下範例展示停用佇列的 PowerShell 命令。 重新啟動命令與前述相同，它會將 `Status` 設定為 **Active** 。

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="topic-status"></a>主題狀態
您可以在 Azure 入口網站中變更主題狀態。 選取主題的目前狀態以查看下列頁面，此頁面可讓您變更狀態。 

:::image type="content" source="./media/entity-suspend/topic-state-change.png" alt-text="變更主題狀態":::

可以針對 **主題** 設定的狀態包括：
- 作用 **中：主題** 為作用中。 您可以將訊息傳送至主題。 
- **Disabled** ：主題已暫止。 您無法將訊息傳送至主題。 
- **SendDisabled** ：與 **Disabled** 相同的效果。 您無法將訊息傳送至主題。 如果您嘗試將訊息傳送至主題，您將會收到例外狀況。 

## <a name="subscription-status"></a>訂用帳戶狀態
您可以在 Azure 入口網站中變更訂用帳戶狀態。 選取訂用帳戶的目前狀態以查看下列頁面，此頁面可讓您變更狀態。 

:::image type="content" source="./media/entity-suspend/subscription-state-change.png" alt-text="變更訂用帳戶狀態":::

可以針對 **訂** 用帳戶設定的狀態為：
- 作用中：訂用帳戶為使用 **中狀態。** 您可以接收 keyfromnode.frm 訂用帳戶的訊息。
- **Disabled** ：訂用帳戶已暫止。 您無法從訂用帳戶接收訊息。 
- **ReceiveDisabled** ：與 **Disabled** 相同的效果。 您無法從訂用帳戶接收訊息。 如果您嘗試接收訂用帳戶的訊息，將會發生例外狀況。

| 主題狀態 | 訂用帳戶狀態 | 行為 | 
| ------------ | ------------------- | -------- | 
| 使用中 | 使用中 | 您可以將訊息傳送至主題，並接收來自訂用帳戶的訊息。 | 
| 使用中 | 停用或接收已停用 | 您可以將訊息傳送至主題，但無法從訂用帳戶接收訊息 | 
| 停用或傳送已停用 | 使用中 | 您無法將訊息傳送至主題，但您可以接收已在訂用帳戶中的訊息。 | 
| 停用或傳送已停用 | 停用或接收已停用 | 您無法將訊息傳送至主題，也無法從訂用帳戶接收。 | 

## <a name="other-statuses"></a>其他狀態
[EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) 列舉另定義一組只能由系統設定的可轉換狀態。 


## <a name="next-steps"></a>後續步驟

若要深入了解服務匯流排傳訊，請參閱下列主題：

* [服務匯流排佇列、主題和訂用帳戶](service-bus-queues-topics-subscriptions.md)
* [開始使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服務匯流排主題和訂用帳戶](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/entity-state-change.png

