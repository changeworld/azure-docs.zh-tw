---
title: Azure 通訊服務中的通知
titleSuffix: An Azure Communication Services concept document
description: 將通知傳送給內建在 Azure 通訊服務上應用程式的使用者。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 3e68e65a5c2ed73a8fb6d8e5d01c645e05ca5157
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92320709"
---
# <a name="communication-services-notifications"></a>通訊服務通知

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Azure 通訊服務聊天和通話用戶端程式庫會建立即時訊息通道，讓訊息以有效率且可靠的方式推送至已連線的用戶端。 這可讓您在應用程式中建置豐富的即時通訊功能，而不需要實作複雜的 HTTP 輪詢邏輯。 不過在行動應用程式上，只有當您的應用程式在前景作用時，此訊號通道才會保持連線狀態。 如果您想要讓使用者在您的應用程式處於背景時接收來電或聊天訊息，您應該使用推播通知。

推播通知可讓您將應用程式的資訊傳送至使用者的行動裝置。 您可以使用推播通知來顯示對話方塊、播放音效，或顯示來電 UI。 Azure 通訊服務提供與 [Azure 事件方格](https://docs.microsoft.com/azure/event-grid/overview)和 [Azure 通知中樞](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-overview)的整合，讓您將推播通知新增至您的應用程式。

## <a name="trigger-push-notifications-via-azure-event-grid"></a>透過 Azure 事件方格觸發推播通知

Azure 通訊服務與 [Azure 事件方格](https://azure.microsoft.com/services/event-grid/)整合，以可靠、可擴充且安全的方式傳遞即時事件通知。 您可以利用這項整合來建立通知服務，藉由建立可觸發 [Azure Function](https://docs.microsoft.com/azure/azure-functions/functions-overview) 或 Webhook 的事件方格訂用帳戶，將行動推播通知傳遞給您的使用者。

:::image type="content" source="./media/notifications/acs-events-int.png" alt-text="圖表顯示通訊服務與事件方格的整合方式。":::

深入了解 [Azure 通訊服務中的事件處理](./event-handling.md)。

## <a name="deliver-push-notifications-via-azure-notification-hubs"></a>透過 Azure 通知中樞傳遞推播通知

您可以將 Azure 通知中樞連線到您的通訊服務資源，以便在接到來電時，自動將推播通知傳送至使用者的行動裝置。 您應該使用這些推播通知將您的應用程式從背景喚醒，並且顯示 UI，讓使用者接受或拒絕通話。 

:::image type="content" source="./media/notifications/acs-anh-int.png" alt-text="圖表顯示通訊服務與事件方格的整合方式。":::

通訊服務會使用 Azure 通知中樞作為傳遞服務，以使用[直接傳送](https://docs.microsoft.com/rest/api/notificationhubs/direct-send) API 與各種平台特定的推播通知服務進行通訊。 這可讓您重複使用現有的 Azure 通知中樞資源和設定，為您的應用程式提供低延遲、可靠的通話通知。

> [!NOTE]
> 目前只支援呼叫推播通知。

### <a name="notification-hub-provisioning"></a>通知中樞佈建 

若要使用通知中樞將推播通知傳遞至用戶端裝置，請在與您的通訊服務資源相同的訂用帳戶中[建立通知中樞](https://docs.microsoft.com/azure/notification-hubs/create-notification-hub-portal)。 必須為您要使用的平台通知服務設定 Azure 通知中樞。 若要了解如何在用戶端應用程式中收到通知中樞的推播通知，請參閱[開始使用通知中心](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started)，並從靠近頁面頂端的下拉式清單中選取目標用戶端平台。

> [!NOTE]
> 目前支援 APN 和 FCM 平台。

設定通知中樞之後，您可以使用 Azure Resource Manager 用戶端或透過 Azure 入口網站提供中樞的連接字串，將其與您的「通訊服務」資源建立關聯。 連接字串應該包含「傳送」權限。 我們建議您特別為您的中樞建立另一個具有僅限「傳送」權限的存取原則。 深入了解[通知中樞安全性和存取原則](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-security)

> [!IMPORTANT]
> 若要啟用 Apple Push Notification Service VOIP 通知，您必須將通知中樞的名稱設定為具有 `.voip` 尾碼的應用程式套件組合識別碼。 請參閱[透過通知中樞使用 APNS VOIP](https://docs.microsoft.com/azure/notification-hubs/voip-apns)。

#### <a name="using-the-azure-resource-manager-client-to-configure-the-notification-hub"></a>使用 Azure Resource Manager 用戶端來設定通知中樞

若要登入 Azure Resource Manager，請執行下列作業，並使用您的認證登入。

```console
armclient login
```

 成功登入之後，請執行下列動作以佈建通知中樞：

```console
armclient POST /subscriptions/<sub_id>/resourceGroups/<resource_group>/providers/Microsoft.Communication/CommunicationServices/<resource_id>/linkNotificationHub?api-version=2020-08-20-preview "{'connectionString': '<connection_string>','resourceId': '<resource_id>'}"
```

#### <a name="using-the-azure-portal-to-configure-the-notification-hub"></a>使用 Azure 入口網站設定通知中樞

在入口網站中，瀏覽至您的 Azure 通訊服務資源。 在通訊服務資源中，從 [通訊服務] 頁面的左側功能表選取 [推播通知]，然後連線您先前佈建的通知中樞。 您必須在這裡提供您的連接字串和資源識別碼：

:::image type="content" source="./media/notifications/acs-anh-portal-int.png" alt-text="圖表顯示通訊服務與事件方格的整合方式。":::

> [!NOTE]
> 如果 Azure 通知中樞連接字串有更新，則必須一併更新通訊服務資源。

#### <a name="device-registration"></a>裝置註冊 

請參閱[語音通話快速入門](../quickstarts/voice-video-calling/getting-started-with-calling.md)，以了解如何向通訊服務註冊您的裝置控制代碼。

## <a name="next-steps"></a>下一步

* 如需 Azure Event Grid 的簡介，請參閱[什麼是 Event Grid？](https://docs.microsoft.com/azure/event-grid/overview)
* 若要深入了解 Azure 通知中樞的概念，請參閱 [Azure 通知中樞文件](https://docs.microsoft.com/azure/notification-hubs/)
