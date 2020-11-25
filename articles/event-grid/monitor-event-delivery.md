---
title: 查看 Azure 事件方格計量和設定警示
description: 本文說明如何使用 Azure 入口網站來查看 Azure 事件方格主題和訂用帳戶的計量，並在其上建立警示。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 518d34d39e6fbecc408fe9a44d899fe4745d60d0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008951"
---
# <a name="monitor-event-grid-message-delivery"></a>監視事件格線訊息傳遞 
本文說明如何使用入口網站來查看事件方格主題和訂用帳戶的計量，並在其上建立警示。 

## <a name="metrics"></a>計量

入口網站會顯示用於傳遞事件訊息之狀態的計量。

針對主題，以下是一些計量：

* **發行成功**：已將事件成功傳送至主題，並以 2xx 回應來處理。
* **發行失敗**：已將事件傳送至主題，但遭到拒絕且有錯誤碼。
* **不相符**：已將事件成功發佈至主題，但與事件訂閱不符。 已捨棄事件。

針對訂用帳戶，以下是一些計量：

* **傳遞成功**：已將事件成功傳遞至訂閱的端點，並已收到 2xx 回應。
* **傳遞失敗**：每次服務嘗試傳遞，且事件處理常式未傳回成功2xx 碼時，就會遞增 **傳遞失敗** 的計數器。 如果我們嘗試多次傳遞相同的事件並失敗，則每個失敗的 **傳遞失敗** 計數器都會遞增。
* **已過期事件**：事件並未傳遞且已傳送所有的重試嘗試。 已捨棄事件。
* **已比對的事件**：主題中的事件已由事件訂閱加以比對。

    > [!NOTE]
    > 如需計量的完整清單，請參閱 [Azure 事件方格支援的計量](metrics.md)。

## <a name="view-custom-topic-metrics"></a>查看自訂主題計量

如果您已發佈自訂主題，就可以檢視它的計量。 

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在主題的搜尋列中，輸入 **事件方格主題**，然後從下拉式清單中選取 [ **事件方格主題** ]。 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-event-grid-topics.png" alt-text="搜尋並選取事件方格主題":::
3. 從主題清單中選取您的自訂主題。 

    :::image type="content" source="./media/monitor-event-delivery/select-custom-topic.png" alt-text="選取您的自訂主題":::
4. 在 **事件方格主題** 頁面上，查看自訂事件主題的度量。 在下圖中，會將顯示資源群組、訂用帳戶等的 [ **基本** ] 區段降至最低。 

    :::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics.png" alt-text="檢視事件計量":::

您可以使用 **事件方格主題** 頁面的 [**計量**] 索引標籤，以支援的計量建立圖表。

:::image type="content" source="./media/monitor-event-delivery/topics-metrics-page.png" alt-text="主題-計量頁面":::

若要深入瞭解計量，請參閱 [Azure 監視器中的計量](../azure-monitor/platform/data-platform-metrics.md)

例如，請參閱 **已發佈事件** 度量的計量圖表。

:::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics-example.png" alt-text="已發佈事件度量":::


## <a name="view-subscription-metrics"></a>查看訂用帳戶計量
1. 遵循上一節中的步驟，流覽至 **事件方格主題** 頁面。 
2. 從下方窗格中選取訂用帳戶，如下列範例所示。 

    :::image type="content" source="./media/monitor-event-delivery/select-event-subscription.png" alt-text="選取事件訂用帳戶":::    

    您也可以在 Azure 入口網站的搜尋列中搜尋 **事件方格** 訂用帳戶，選取 [ **主題類型**]、[ **訂** 用帳戶] 和 [ **位置** ] 以查看事件訂用帳戶。 

    :::image type="content" source="./media/monitor-event-delivery/event-subscriptions-page.png" alt-text="從事件方格訂用帳戶頁面選取事件訂閱":::        

    針對自訂主題，請選取 [ **事件方格主題** ] 作為 **主題類型**。 針對系統主題，請選取 Azure 資源的類型，例如 **儲存體帳戶 (Blob GPv2)**。 
3. 請參閱圖表中訂用帳戶首頁上的訂用帳戶計量。 您可以查看過去1小時、6小時、12小時、1天、7天或30天的 **一般**、 **錯誤**、 **延遲** 和 **死** 信計量。 

    :::image type="content" source="./media/monitor-event-delivery/subscription-home-page-metrics.png" alt-text="訂用帳戶首頁上的計量":::    

## <a name="view-system-topic-metrics"></a>查看系統主題計量

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在主題的搜尋列中，輸入 **事件方格系統主題**，然後從下拉式清單中選取 [ **Event grid 系統主題** ]。 

    :::image type="content" source="./media/monitor-event-delivery/search-system-topics.png" alt-text="搜尋並選取事件方格系統主題":::
3. 從主題清單中選取您的系統主題。 

    :::image type="content" source="./media/monitor-event-delivery/select-system-topic.png" alt-text="選取您的系統主題":::
4. 在 **事件方格系統主題** 頁面上查看系統主題的度量。 在下圖中，會將顯示資源群組、訂用帳戶等的 [ **基本** ] 區段降至最低。 

    :::image type="content" source="./media/monitor-event-delivery/system-topic-overview-metrics.png" alt-text="在 [總覽] 頁面上查看系統主題計量":::

您可以使用 **事件方格主題** 頁面的 [**計量**] 索引標籤，以支援的計量建立圖表。

:::image type="content" source="./media/monitor-event-delivery/system-topic-metrics-page.png" alt-text="系統主題-計量頁面":::

若要深入瞭解計量，請參閱 [Azure 監視器中的計量](../azure-monitor/platform/data-platform-metrics.md)


## <a name="next-steps"></a>後續步驟
查看下列文章：

- 若要瞭解如何建立計量和活動記錄作業的警示，請參閱 [設定警示](set-alerts.md)。
- 如需事件傳遞和重試的相關資訊，請參閱[事件格線訊息傳遞與重試](delivery-and-retry.md)。
