---
title: 使用 Azure Service Bus Explorer 來執行服務匯流排上的資料作業 (預覽)
description: 此文章提供有關如何使用以入口網站為基礎的 Azure Service Bus Explorer 來存取 Azure 服務匯流排資料資訊。
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: eb6610945ba4b09543308ab351d3a36ec7e587dd
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684836"
---
# <a name="use-service-bus-explorer-to-perform-data-operations-on-service-bus-preview"></a>使用 Service Bus Explorer 來執行服務匯流排上的資料作業 (預覽)

## <a name="overview"></a>概觀

Azure 服務匯流排可讓傳送者和接收者用戶端應用程式使用熟悉的點對點 (佇列) 和發行-訂閱 (主題-訂閱) 語義，分離其商務邏輯。

在 Azure 服務匯流排命名空間上執行的作業有兩種類型 
   * 管理作業 - 建立、更新、刪除服務匯流排命名空間、佇列、主題和訂閱。
   * 資料作業 - 對佇列、主題和訂閱傳送及接收訊息。

Azure Service Bus Explorer 會將入口網站功能延伸到管理作業之外，以支援佇列、主題和訂閱 (及其無效訊息子實體) 上的資料作業 (傳送、接收、預覽) - 直接從 Azure 入口網站本身執行。

> [!NOTE]
> 此文章將重點放在 Azure 入口網站上的 Azure Service Bus Explorer 功能。
>
> Azure 服務匯流排 explorer 工具是 ***而不** 是 [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer)的「社區擁有的 OSS」工具。
>

## <a name="prerequisites"></a>Prerequisites

若要使用 Service Bus Explorer 工具，您將需要執行下列工作： 

- 布建 Azure 服務匯流排命名空間。
- 建立用來傳送和接收訊息的佇列，或使用訂閱來測試功能的主題。 若要瞭解如何建立佇列、主題和訂閱，請參閱下列文章： 
    - [快速入門 - 建立佇列](service-bus-quickstart-portal.md)
    - [快速入門 - 建立主題](service-bus-quickstart-topics-subscriptions-portal.md)
- 確定您是命名空間上其中一個角色的成員： 
    - [服務匯流排資料擁有者](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner) 
    - [參與者](../role-based-access-control/built-in-roles.md#contributor) 
    - [擁有者](/role-based-access-control/built-in-roles.md#owner)


## <a name="using-the-service-bus-explorer"></a>使用 Service Bus Explorer

若要使用 Azure Service Bus Explorer，您需要瀏覽至您想要在其上執行傳送、預覽和接收作業的服務匯流排命名空間。

如果您想要對佇列執行作業，請從導覽功能表中選取 [*佇列*]。 如果您想要針對主題 (和其相關訂閱) 執行作業，請挑選 [主題]。 

:::image type="content" source="./media/service-bus-explorer/queue-topics-left-navigation.png"alt-text="實體選取":::

挑選 [佇列] 或 [主題] 之後，請挑選特定的佇列或主題。

從左側導覽功能表中，選取 [Service Bus Explorer (預覽)]

:::image type="content" source="./media/service-bus-explorer/left-navigation-menu-selected.png" alt-text="SB Explorer 左側導覽功能表":::

### <a name="sending-a-message-to-a-queue-or-topic"></a>將訊息傳送至佇列或主題

若要將訊息傳送至 **佇列** 或 **主題**，請按一下 Service Bus Explorer 上的 [*_傳送_*] 索引標籤。

在此撰寫訊息 - 

1. 將 _ *內容類型** 挑選為 ' Text/純文字 '、' Application/Xml ' 或 ' Application/Json '。
2. 新增訊息 [內容]。 請確定其符合稍早設定的 [內容類型]。
3. 設定 [進階屬性] \(選擇性\)，這些包括相互關聯識別碼、訊息識別碼、標籤、ReplyTo、存留時間 (TTL) 和已排程的排入佇列時間 (適用於排定的訊息)。
4. 設定 [自訂屬性] - 可以是針對字典索引鍵設定的任何使用者屬性。

一旦撰寫訊息之後，請點擊 [傳送]。

:::image type="content" source="./media/service-bus-explorer/send-experience.png" alt-text="撰寫訊息":::

當傳送作業成功完成時， 

* 如果傳送至佇列，[作用中訊息] 計量計數器會遞增。

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="QueueAfterSendMetrics":::

* 如果傳送至主題，在訊息路由傳送至的訂閱上，[作用中訊息] 計量計數器會遞增。

    :::image type="content" source="./media/service-bus-explorer/topic-after-send-metrics.png" alt-text="TopicAfterSendMetrics":::

### <a name="receiving-a-message-from-a-queue"></a>從佇列接收訊息

Service Bus Explorer 上的接收函式允許一次接收單一訊息。 接收作業是使用 **ReceiveAndDelete** 模式來執行。

> [!IMPORTANT]
> 請注意，Service Bus explorer 所執行的接收作業是 **_破壞性接收_* _，也就是在 Service Bus Explorer 工具上顯示訊息時，該訊息會從佇列中移除。
>
> 若要流覽訊息而不從佇列中移除訊息，請考慮使用 _*_查看_*_ 功能。
>

從佇列接收訊息 (或其無效訊息子佇列) 

1. 按一下 Service Bus Explorer 上的 [ _*_接收_*_ ] 索引標籤。
2. 檢查計量，以查看是否有 _ 使用中 *訊息** 或寄不出 **的信件訊息** 要接收。

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="QueueAfterSendMetrics":::

3. 選擇 **_Queue_* _ 或 _*_Deadletter_*_ 子佇列。

    :::image type="content" source="./media/service-bus-explorer/queue-or-deadletter.png" alt-text="QueueOrDeadletter":::

4. 按一下 [ _*_接收_*_ ] 按鈕，然後按一下 [ _*_是]_*_ ，以確認「接收和刪除」作業。


當接收作業成功時，訊息詳細資料會顯示在格線中，如下所示。 您可以從格線中選取訊息，以顯示其詳細資料。

:::image type="content" source="./media/service-bus-explorer/receive-message-from-queue-2.png" alt-text="Azure 服務匯流排 Explorer 中 [佇列] 視窗的螢幕擷取畫面，其中顯示佇列中所選取之作用中訊息的訊息詳細資料。":::


### <a name="peeking-a-message-from-a-queue"></a>預覽來自佇列的訊息

透過預覽功能，您可以使用 Service Bus Explorer 來檢視佇列或寄不出的信件佇上的前 32 個訊息。

1. 若要查看佇列中的訊息，請按一下 Service Bus Explorer 上的 [ _*_查看_*_ ] 索引標籤。

    :::image type="content" source="./media/service-bus-explorer/peek-tab-selected.png" alt-text="PeekTab":::

2. 檢查計量以查看是否有 _ 使用中 *訊息** 或寄不出 **的信件訊息** 可供查看。

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="QueueAfterSendMetrics":::

3. 然後挑選 **_Queue_* _ 或 _*_Deadletter_*_ 子佇列。

    :::image type="content" source="./media/service-bus-explorer/queue-or-deadletter.png" alt-text="QueueOrDeadletter":::

4. 按一下 [ _*_預覽_*_ ] 按鈕。 

一旦預覽作業完成，格線上最多會顯示 32 個訊息，如下所示。 若要檢視特定訊息的詳細資料，請從方格中加以選取。 

:::image type="content" source="./media/service-bus-explorer/peek-message-from-queue-2.png" alt-text="PeekMessageFromQueue":::

> [!NOTE]
>
> 由於查看不是破壞性作業，因此 *不會* 從佇列中移除訊息 _。
>

### <a name="receiving-a-message-from-a-subscription"></a>從訂閱接收訊息

就像佇列一樣，也可以對訂用帳戶 (或其 deadletter 實體) 執行 **_Receive_* _ 作業。 不過，由於訂閱存在於主題的內容中，因此會藉由瀏覽至指定主題的 Service Bus Explorer 來執行接收作業。

> [!IMPORTANT]
> 請注意，Service Bus explorer 所執行的接收作業是 _*_破壞性接收_*_，也就是在 Service Bus Explorer 工具上顯示訊息時，該訊息會從佇列中移除。
>
> 若要流覽訊息而不從佇列中移除訊息，請考慮使用 _*_查看_*_ 功能。
>

1. 按一下 [ _*_接收_*_ ] 索引標籤，然後從下拉式清單選取器選取特定的 _*_訂_*_ 用帳戶。

    :::image type="content" source="./media/service-bus-explorer/receive-subscription-tab-selected.png" alt-text="ReceiveTabSelected":::

2. 在 _*_訂_*_ 用帳戶或 _*_DeadLetter_*_ 子實體之間挑選。

    :::image type="content" source="./media/service-bus-explorer/subscription-or-deadletter.png" alt-text="SubscriptionOrDeadletter":::

3. 按一下 [ _*_接收_*_ ] 按鈕，然後按一下 [ _*_是]_*_ ，以確認「接收和刪除」作業。

當接收作業成功時，已接收訊息會顯示在格線中，如下所示。 若要檢視訊息的詳細資料，請按一下該訊息。

:::image type="content" source="./media/service-bus-explorer/receive-message-from-subscription.png" alt-text="Azure 服務匯流排 Explorer 中 [接收] 索引標籤的螢幕擷取畫面，其中顯示已接收之作用中訊息的訊息詳細資料。":::

### <a name="peeking-a-message-from-a-subscription"></a>預覽來自訂閱的訊息

若只要流覽訂用帳戶或其 deadletter 子實體上的訊息，也可以在訂用帳戶上使用 _*_查看_*_ 功能。

1. 按一下 [ _*_查看_*_ ] 索引標籤，然後從下拉式清單選取器選取特定的 _*_訂_*_ 用帳戶。

    :::image type="content" source="./media/service-bus-explorer/peek-subscription-tab-selected.png" alt-text="PeekTabSelected":::

2. 選擇 _*_訂_*_ 用帳戶或 _*_DeadLetter_*_ 列。

    :::image type="content" source="./media/service-bus-explorer/subscription-or-deadletter.png" alt-text="SubscriptionOrDeadletter":::

3. 按一下 [ _*_預覽_*_ ] 按鈕。

一旦預覽作業完成，格線上最多會顯示 32 個訊息，如下所示。 若要檢視特定訊息的詳細資料，請從方格中加以選取。 

:::image type="content" source="./media/service-bus-explorer/peek-message-from-subscription.png" alt-text="PeekMessageFromSubscription":::

> [!NOTE]
>
> 由於查看不是破壞性作業，因此 *不會* 從佇列中移除訊息 _。
>

## <a name="next-steps"></a>後續步驟

   * 深入了解服務匯流排[佇列](service-bus-queues-topics-subscriptions.md#queues)和[主題](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)
   * 深入了解[如何透過 Azure 入口網站建立服務匯流排佇列](service-bus-quickstart-portal.md)
   * 深入了解[如何透過 Azure 入口網站建立服務匯流排主題和訂閱](service-bus-quickstart-topics-subscriptions-portal.md)