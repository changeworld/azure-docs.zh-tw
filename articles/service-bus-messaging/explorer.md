---
title: 使用 Azure Service Bus Explorer 來執行服務匯流排上的資料作業 (預覽)
description: 此文章提供有關如何使用以入口網站為基礎的 Azure Service Bus Explorer 來存取 Azure 服務匯流排資料資訊。
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: e5e97c6860c2cc01048f4f7caf9f40f9e07592d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91295595"
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
> Azure Service Bus Explorer 工具「不」是社群擁有的 OSS 工具 [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) \(英文\)。
>

## <a name="prerequisites"></a>Prerequisites

若要使用 Service Bus Explorer 工具，您將必須佈建 Azure 服務匯流排命名空間。 

佈建服務匯流排命名空間之後，您必須建立佇列來傳送及接收訊息，或建立包含訂閱的主題以測試功能。

若要深入了解如何建立佇列、主題和訂閱，請參閱以下連結
   * [快速入門 - 建立佇列](service-bus-quickstart-portal.md)
   * [快速入門 - 建立主題](service-bus-quickstart-topics-subscriptions-portal.md)


## <a name="using-the-service-bus-explorer"></a>使用 Service Bus Explorer

若要使用 Azure Service Bus Explorer，您需要瀏覽至您想要在其上執行傳送、預覽和接收作業的服務匯流排命名空間。

如果您想要對佇列執行作業，請從導覽功能表中挑選 [佇列]。 如果您想要針對主題 (和其相關訂閱) 執行作業，請挑選 [主題]。 

:::image type="content" source="./media/service-bus-explorer/queue-topics-left-navigation.png"alt-text="實體選取":::

挑選 [佇列] 或 [主題] 之後，請挑選特定的佇列或主題。

從左側導覽功能表中，選取 [Service Bus Explorer (預覽)]

:::image type="content" source="./media/service-bus-explorer/left-navigation-menu-selected.png" alt-text="實體選取":::

### <a name="sending-a-message-to-a-queue-or-topic"></a>將訊息傳送至佇列或主題

若要將訊息傳送至**佇列**或**主題**，請在 Service Bus Explorer 上，按一下 [傳送] 索引標籤。

在此撰寫訊息 - 

1. 挑選 [內容類型] 為 [文字/純文字]、[應用程式/XML] 或 [應用程式/JSON]。
2. 新增訊息 [內容]。 請確定其符合稍早設定的 [內容類型]。
3. 設定 [進階屬性] \(選擇性\)，這些包括相互關聯識別碼、訊息識別碼、標籤、ReplyTo、存留時間 (TTL) 和已排程的排入佇列時間 (適用於排定的訊息)。
4. 設定 [自訂屬性] - 可以是針對字典索引鍵設定的任何使用者屬性。

一旦撰寫訊息之後，請點擊 [傳送]。

:::image type="content" source="./media/service-bus-explorer/send-experience.png" alt-text="實體選取":::

當傳送作業成功完成時， 

* 如果傳送至佇列，[作用中訊息] 計量計數器會遞增。

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="實體選取":::

* 如果傳送至主題，在訊息路由傳送至的訂閱上，[作用中訊息] 計量計數器會遞增。

    :::image type="content" source="./media/service-bus-explorer/topic-after-send-metrics.png" alt-text="實體選取":::

### <a name="receiving-a-message-from-a-queue"></a>從佇列接收訊息

Service Bus Explorer 上的接收函式允許一次接收單一訊息。 接收作業是使用 **ReceiveAndDelete** 模式來執行。

> [!IMPORTANT]
> 請注意，Service Bus Explorer 執行的接收作業是「破壞性接收」，也就是當訊息顯示在 Service Bus Explorer 工具上時，會從佇列中加以移除。
>
> 若要瀏覽訊息，而不將其從佇列中移除，請考慮使用「預覽」功能。
>

從佇列接收訊息 (或其無效訊息子佇列) 

1. 按一下 Service Bus Explorer 上的 [接收] 索引標籤。
2. 檢查計量以查看是否有可以接收的 [作用中訊息] 或 [無效訊息]。

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="實體選取":::

3. 在 [佇列] 或 [DeadLetter] 子佇列之間挑選。

    :::image type="content" source="./media/service-bus-explorer/queue-or-deadletter.png" alt-text="實體選取":::

4. 按一下 [接收] 按鈕，接著按一下 [是]，以確認「接收並刪除」作業。


當接收作業成功時，訊息詳細資料會顯示在格線中，如下所示。 您可以從格線中選取訊息，以顯示其詳細資料。

:::image type="content" source="./media/service-bus-explorer/receive-message-from-queue-2.png" alt-text="實體選取":::


### <a name="peeking-a-message-from-a-queue"></a>預覽來自佇列的訊息

透過預覽功能，您可以使用 Service Bus Explorer 來檢視佇列或寄不出的信件佇上的前 32 個訊息。

1. 若要預覽佇列上的訊息，請按一下 [Service Bus Explorer] 上的 [預覽] 索引標籤。

    :::image type="content" source="./media/service-bus-explorer/peek-tab-selected.png" alt-text="實體選取":::

2. 檢查計量以查看是否有可以預覽的 [作用中訊息] 或 [無效訊息]。

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="實體選取":::

3. 然後，在 [佇列] 或 [DeadLetter] 子佇列之間挑選。

    :::image type="content" source="./media/service-bus-explorer/queue-or-deadletter.png" alt-text="實體選取":::

4. 按一下 [預覽] 按鈕。 

一旦預覽作業完成，格線上最多會顯示 32 個訊息，如下所示。 若要檢視特定訊息的詳細資料，請從方格中加以選取。 

:::image type="content" source="./media/service-bus-explorer/peek-message-from-queue-2.png" alt-text="實體選取":::

> [!NOTE]
>
> 因為預覽不是破壞性作業，所以**不會**從佇列中移除訊息。
>

### <a name="receiving-a-message-from-a-subscription"></a>從訂閱接收訊息

就像佇列一樣，您可以對訂閱 (或其 Deadletter 實體) 執行「接收」作業。 不過，由於訂閱存在於主題的內容中，因此會藉由瀏覽至指定主題的 Service Bus Explorer 來執行接收作業。

> [!IMPORTANT]
> 請注意，Service Bus Explorer 執行的接收作業是「破壞性接收」，也就是當訊息顯示在 Service Bus Explorer 工具上時，會從佇列中加以移除。
>
> 若要瀏覽訊息，而不將其從佇列中移除，請考慮使用「預覽」功能。
>

1. 按一下 [接收] 索引標籤，然後從下拉式清單選取器中選取特定的「訂閱」。

    :::image type="content" source="./media/service-bus-explorer/receive-subscription-tab-selected.png" alt-text="實體選取":::

2. 在 [訂閱] 或 [DeadLetter] 子實體之間挑選。

    :::image type="content" source="./media/service-bus-explorer/subscription-or-deadletter.png" alt-text="實體選取":::

3. 按一下 [接收] 按鈕，接著按一下 [是]，以確認「接收並刪除」作業。

當接收作業成功時，已接收訊息會顯示在格線中，如下所示。 若要檢視訊息的詳細資料，請按一下該訊息。

:::image type="content" source="./media/service-bus-explorer/receive-message-from-subscription.png" alt-text="實體選取":::

### <a name="peeking-a-message-from-a-subscription"></a>預覽來自訂閱的訊息

若只要瀏覽訂閱或其 Deadletter 子實體上的訊息，也可以在訂閱上使用「預覽」功能。

1. 按一下 [預覽] 索引標籤，然後從下拉式清單選取器中選取特定的「訂閱」。

    :::image type="content" source="./media/service-bus-explorer/peek-subscription-tab-selected.png" alt-text="實體選取":::

2. 在 [訂閱] 或 [DeadLetter] 子實體之間挑選。

    :::image type="content" source="./media/service-bus-explorer/subscription-or-deadletter.png" alt-text="實體選取":::

3. 按一下 [預覽] 按鈕。

一旦預覽作業完成，格線上最多會顯示 32 個訊息，如下所示。 若要檢視特定訊息的詳細資料，請從方格中加以選取。 

:::image type="content" source="./media/service-bus-explorer/peek-message-from-subscription.png" alt-text="實體選取":::

> [!NOTE]
>
> 因為預覽不是破壞性作業，所以**不會**從佇列中移除訊息。
>

## <a name="next-steps"></a>後續步驟

   * 深入了解服務匯流排[佇列](service-bus-queues-topics-subscriptions.md#queues)和[主題](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)
   * 深入了解[如何透過 Azure 入口網站建立服務匯流排佇列](service-bus-quickstart-portal.md)
   * 深入了解[如何透過 Azure 入口網站建立服務匯流排主題和訂閱](service-bus-quickstart-topics-subscriptions-portal.md)