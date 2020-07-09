---
title: 查看 Azure 事件方格計量並設定警示
description: 本文說明如何使用 Azure 入口網站來查看 Azure 事件方格主題和訂用帳戶的計量，並對其建立警示。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 518d34d39e6fbecc408fe9a44d899fe4745d60d0
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86114878"
---
# <a name="monitor-event-grid-message-delivery"></a>監視事件格線訊息傳遞 
本文說明如何使用入口網站來查看事件方格主題和訂用帳戶的計量，並對其建立警示。 

## <a name="metrics"></a>計量

入口網站會顯示用於傳遞事件訊息之狀態的計量。

針對主題，以下是一些計量：

* **發行成功**：已將事件成功傳送至主題，並以 2xx 回應來處理。
* **發行失敗**：已將事件傳送至主題，但遭到拒絕且有錯誤碼。
* **不相符**：已將事件成功發佈至主題，但與事件訂閱不符。 已捨棄事件。

針對訂用帳戶，以下是一些計量：

* **傳遞成功**：已將事件成功傳遞至訂閱的端點，並已收到 2xx 回應。
* **傳遞失敗**：每次服務嘗試傳遞時，事件處理常式不會傳回成功的2xx 代碼，[**傳遞失敗**] 計數器就會遞增。 如果我們嘗試多次傳遞相同的事件並失敗，則每次失敗都會增加 [**傳遞失敗**] 計數器。
* **已過期事件**：事件並未傳遞且已傳送所有的重試嘗試。 已捨棄事件。
* **已比對的事件**：主題中的事件已由事件訂閱加以比對。

    > [!NOTE]
    > 如需計量的完整清單，請參閱[Azure 事件方格支援的計量](metrics.md)。

## <a name="view-custom-topic-metrics"></a>查看自訂主題計量

如果您已發佈自訂主題，就可以檢視它的計量。 

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在主題的搜尋列中，輸入**事件方格主題**，然後從下拉式清單中選取 [**事件方格主題**]。 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-event-grid-topics.png" alt-text="搜尋並選取事件方格主題":::
3. 從主題清單中選取您的自訂主題。 

    :::image type="content" source="./media/monitor-event-delivery/select-custom-topic.png" alt-text="選取您的自訂主題":::
4. 在 [**事件方格主題**] 頁面上，查看自訂事件主題的計量。 在下圖中，顯示資源群組、訂用帳戶等的 [**基本**功能] 區段會最小化。 

    :::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics.png" alt-text="檢視事件計量":::

您可以使用**事件方格主題**頁面的 [**計量**] 索引標籤，建立具有支援之計量的圖表。

:::image type="content" source="./media/monitor-event-delivery/topics-metrics-page.png" alt-text="主題-計量頁面":::

若要深入瞭解計量，請參閱[中的計量 Azure 監視器](../azure-monitor/platform/data-platform-metrics.md)

例如，請參閱計量圖表以瞭解**已發佈的事件**度量。

:::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics-example.png" alt-text="已發佈事件度量":::


## <a name="view-subscription-metrics"></a>查看訂用帳戶計量
1. 遵循上一節中的步驟，流覽至 [**事件方格主題**] 頁面。 
2. 從下方窗格選取 [訂用帳戶]，如下列範例所示。 

    :::image type="content" source="./media/monitor-event-delivery/select-event-subscription.png" alt-text="選取事件訂用帳戶":::    

    您也可以在 Azure 入口網站的搜尋列中搜尋**事件方格**訂用帳戶，選取 [**主題類型**]、[**訂**用帳戶] 和 [**位置**] 以查看事件訂用帳戶。 

    :::image type="content" source="./media/monitor-event-delivery/event-subscriptions-page.png" alt-text="從 [事件方格訂閱] 頁面選取事件訂閱":::        

    針對自訂主題，請選取 [**事件方格主題**] 做為 [**主題類型**]。 針對 [系統主題]，選取 Azure 資源的類型，例如 [**儲存體帳戶（Blob、GPv2）**]。 
3. 請在圖表中的訂用帳戶首頁上查看訂用帳戶的計量。 您可以查看過去1小時、6小時、12小時、1天、7天或30天的**一般**、**錯誤**、**延遲**和寄不出**的信件**計量。 

    :::image type="content" source="./media/monitor-event-delivery/subscription-home-page-metrics.png" alt-text="訂用帳戶首頁上的計量":::    

## <a name="view-system-topic-metrics"></a>查看系統主題計量

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在主題的搜尋列中，輸入**Event Grid 系統主題**，然後從下拉式清單中選取 [**事件方格系統主題**]。 

    :::image type="content" source="./media/monitor-event-delivery/search-system-topics.png" alt-text="搜尋並選取事件方格系統主題":::
3. 從主題清單中選取您的系統主題。 

    :::image type="content" source="./media/monitor-event-delivery/select-system-topic.png" alt-text="選取您的系統主題":::
4. 在**事件方格系統主題**頁面上，查看系統的度量主題。 在下圖中，顯示資源群組、訂用帳戶等的 [**基本**功能] 區段會最小化。 

    :::image type="content" source="./media/monitor-event-delivery/system-topic-overview-metrics.png" alt-text="在 [總覽] 頁面上查看系統主題計量":::

您可以使用**事件方格主題**頁面的 [**計量**] 索引標籤，建立具有支援之計量的圖表。

:::image type="content" source="./media/monitor-event-delivery/system-topic-metrics-page.png" alt-text="系統主題-計量頁面":::

若要深入瞭解計量，請參閱[中的計量 Azure 監視器](../azure-monitor/platform/data-platform-metrics.md)


## <a name="next-steps"></a>後續步驟
查看下列文章：

- 若要瞭解如何建立計量和活動記錄作業的警示，請參閱[設定警示](set-alerts.md)。
- 如需事件傳遞和重試的相關資訊，請參閱[事件格線訊息傳遞與重試](delivery-and-retry.md)。
