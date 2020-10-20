---
title: 快速入門 - 處理 SMS 事件
titleSuffix: An Azure Communication Services quickstart
description: 了解如何使用 Azure 通訊服務來處理 SMS 事件。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e66d5c4dd4fc1c6c641da975b0ac2254f459642a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91976924"
---
# <a name="quickstart-handle-sms-events"></a>快速入門：處理 SMS 事件

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

使用 Azure 事件方格來處理通訊服務 SMS 事件，以開始使用 Azure 通訊服務。 

## <a name="about-azure-event-grid"></a>關於 Azure 事件方格

[Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) 是一項雲端式事件服務。 在本文中，您將了解如何訂閱[通訊服務事件](../../concepts/event-handling.md)的事件，並觸發事件以檢視結果。 通常，您會將事件傳送至可處理事件資料及採取行動的端點。 在本文中，您會將事件傳送至可收集及顯示訊息的 Web 應用程式。

## <a name="prerequisites"></a>必要條件
- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- Azure 通訊服務資源。 如需進一步的詳細資料，請參閱[建立 Azure 通訊資源](../create-communication-resource.md)快速入門。
- 已啟用 SMS 的電話號碼。 [取得電話號碼](./get-phone-number.md)。

## <a name="setting-up"></a>設定

### <a name="enable-event-grid-resource-provider"></a>啟用事件格線資源提供者

如果您先前在 Azure 訂用帳戶中未曾使用過事件方格，您可能必須依照下列步驟註冊事件方格資源提供者：

在 Azure 入口網站中：

1. 選取左側功能表上的 [訂用帳戶]  。
2. 選取您用於事件方格的訂用帳戶。
3. 在左側功能表的 [設定]  下，選取 [資源提供者]  。
4. 尋找 **Microsoft.EventGrid**。
5. 如果未註冊，請選取 [註冊]  。 

可能需要一點時間才能完成註冊。 選取 [重新整理]  來更新狀態。 當 [狀態]  是 [已註冊]  時，代表已準備好繼續進行。

### <a name="event-grid-viewer-deployment"></a>事件方格檢視器部署

在本快速入門中，我們將使用 [Azure 事件方格檢視器範例](https://docs.microsoft.com/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/)，以近乎即時的方式檢視事件。 這會為使用者提供即時摘要的體驗。 此外，每個事件的承載應該也都可供查看。  

## <a name="subscribe-to-the-sms-events-using-web-hooks"></a>使用 Webhook 訂閱 SMS 事件

在入口網站中，瀏覽至您已建立的 Azure 通訊服務資源。 在通訊服務資源內，從 [通訊服務] 頁面的左側功能表中選取 [事件]。

:::image type="content" source="./media/handle-sms-events/select-events.png" alt-text="顯示在資源的 [事件] 頁面中選取 [事件訂用帳戶] 按鈕的螢幕擷取畫面。":::

按 [新增事件訂用帳戶]，進入建立精靈。

在 [建立事件訂用帳戶] 頁面上，輸入事件訂用帳戶的**名稱**。

您可以訂閱特定事件，以向事件方格指出您要追蹤的 SMS 事件，以及要將事件傳送至何處。 從下拉式功能表中選取您要訂閱的事件。 針對 SMS，您可以選擇 `SMS Received` 和 `SMS Delivery Report Received`。 

查看 [Azure 通訊服務支援的事件](../../concepts/event-handling.md)的完整清單。

:::image type="content" source="./media/handle-sms-events/select-events-create-eventsub.png" alt-text="顯示在資源的 [事件] 頁面中選取 [事件訂用帳戶] 按鈕的螢幕擷取畫面。":::

選取 [WebHook] 作為 [端點類型]。 

:::image type="content" source="./media/handle-sms-events/select-events-create-linkwebhook.png" alt-text="顯示在資源的 [事件] 頁面中選取 [事件訂用帳戶] 按鈕的螢幕擷取畫面。":::

針對 [端點]，按一下 [選取端點]，然後輸入 Web 應用程式的 URL。

在此案例中，我們將使用先前在本快速入門中設定的 [Azure 事件方格檢視器範例](https://docs.microsoft.com/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/)的 URL。 該範例的 URL 格式如下：`https://{{site-name}}.azurewebsites.net/api/updates`

然後，選取 [確認選取項目]。

:::image type="content" source="./media/handle-sms-events/select-events-create-selectwebhook-epadd.png" alt-text="顯示在資源的 [事件] 頁面中選取 [事件訂用帳戶] 按鈕的螢幕擷取畫面。":::

## <a name="viewing-sms-events"></a>檢視 SMS 事件

### <a name="triggering-sms-events"></a>觸發 SMS 事件

若要檢視事件觸發程序，我們必須先產生事件。

- 當通訊服務電話號碼收到文字訊息時，會產生 `SMS Received` 事件。 若要觸發事件，只需從您的電話傳送訊息到附加至通訊服務資源的電話號碼即可。
- 當您使用通訊服務電話號碼將 SMS 傳送給使用者時，將會產生 `SMS Delivery Report Received` 事件。 若要觸發事件，您必須在[已傳送簡訊](../telephony-sms/send.md)的選項中啟用 `Delivery Report`。 嘗試使用 `Delivery Report` 將訊息傳送至您的電話。 完成此動作後，您的 Azure 帳戶中會產生幾美分的少許費用。

查看 [Azure 通訊服務支援的事件](../../concepts/event-handling.md)的完整清單。

### <a name="receiving-sms-events"></a>接收 SMS 事件

當您完成上述任一動作後，您會看到有 `SMS Received` 和 `SMS Delivery Report Received` 事件傳送至您的端點。 這些事件會顯示在我們一開始設定的 [Azure 事件方格檢視器範例](https://docs.microsoft.com/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/)中。 您可以按事件旁的眼睛圖示，以查看整個承載。 事件顯示如下：

:::image type="content" source="./media/handle-sms-events/sms-received.png" alt-text="顯示在資源的 [事件] 頁面中選取 [事件訂用帳戶] 按鈕的螢幕擷取畫面。":::

:::image type="content" source="./media/handle-sms-events/sms-delivery-report-received.png" alt-text="顯示在資源的 [事件] 頁面中選取 [事件訂用帳戶] 按鈕的螢幕擷取畫面。":::

深入了解[事件結構描述和其他事件處理概念](../../concepts/event-handling.md)。

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除通訊服務訂閱，則可以刪除資源或資源群組。 刪除資源群組也會刪除其關聯的任何其他資源。 深入了解如何[清除資源](../create-communication-resource.md#clean-up-resources)。

## <a name="next-steps"></a>下一步

在本快速入門中，您已了解如何使用 SMS 事件。 您可以藉由建立事件方格訂用帳戶，來接收 SMS 訊息。

> [!div class="nextstepaction"] 
> [傳送 SMS](../telephony-sms/send.md)

您可能也會想要：

 - [了解事件處理概念](../../concepts/event-handling.md)
 - [了解事件方格](https://docs.microsoft.com/azure/event-grid/overview)
