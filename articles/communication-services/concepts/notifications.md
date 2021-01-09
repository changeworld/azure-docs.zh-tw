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
ms.openlocfilehash: d2b77708609f61eeb4ce33148f020027d646836b
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/30/2020
ms.locfileid: "97813593"
---
# <a name="communication-services-notifications"></a>通訊服務通知

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Azure 通訊服務聊天和通話用戶端程式庫會建立即時訊息通道，讓訊息以有效率且可靠的方式推送至已連線的用戶端。 這可讓您在應用程式中建置豐富的即時通訊功能，而不需要實作複雜的 HTTP 輪詢邏輯。 不過在行動應用程式上，只有當您的應用程式在前景作用時，此訊號通道才會保持連線狀態。 如果您想要讓使用者在您的應用程式處於背景時接收來電或聊天訊息，您應該使用推播通知。

推播通知可讓您將應用程式的資訊傳送至使用者的行動裝置。 您可以使用推播通知來顯示對話方塊、播放音效，或顯示來電 UI。 Azure 通訊服務提供與 [Azure 事件方格](../../event-grid/overview.md)和 [Azure 通知中樞](../../notification-hubs/notification-hubs-push-notification-overview.md)的整合，讓您將推播通知新增至您的應用程式。

## <a name="trigger-push-notifications-via-azure-event-grid"></a>透過 Azure 事件方格觸發推播通知

Azure 通訊服務與 [Azure 事件方格](https://azure.microsoft.com/services/event-grid/)整合，以可靠、可擴充且安全的方式傳遞即時事件通知。 您可以利用這項整合來建立通知服務，藉由建立可觸發 [Azure Function](../../azure-functions/functions-overview.md) 或 Webhook 的事件方格訂用帳戶，將行動推播通知傳遞給您的使用者。

:::image type="content" source="./media/notifications/acs-events-int.png" alt-text="圖表顯示通訊服務與事件方格的整合方式。":::

深入了解 [Azure 通訊服務中的事件處理](./event-handling.md)。

## <a name="deliver-push-notifications-via-azure-notification-hubs"></a>透過 Azure 通知中樞傳遞推播通知

您可以將 Azure 通知中樞連線到您的通訊服務資源，以便在接到來電時，自動將推播通知傳送至使用者的行動裝置。 您應該使用這些推播通知將您的應用程式從背景喚醒，並且顯示 UI，讓使用者接受或拒絕通話。 

:::image type="content" source="./media/notifications/acs-anh-int.png" alt-text="圖表顯示通訊服務與事件中樞的整合方式。":::

通訊服務會使用 Azure 通知中樞作為傳遞服務，以使用[直接傳送](/rest/api/notificationhubs/direct-send) API 與各種平台特定的推播通知服務進行通訊。 這可讓您重複使用現有的 Azure 通知中樞資源和設定，為您的應用程式提供低延遲、可靠的通話通知。

> [!NOTE]
> 目前只支援呼叫推播通知。

### <a name="notification-hub-provisioning"></a>通知中樞佈建 

若要使用通知中樞將推播通知傳遞至用戶端裝置，請在與您的通訊服務資源相同的訂用帳戶中[建立通知中樞](../../notification-hubs/create-notification-hub-portal.md)。 您必須為要使用的平台通知系統設定 Azure 通知中樞。 若要了解如何在用戶端應用程式中收到通知中樞的推播通知，請參閱[開始使用通知中心](../../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md)，並從靠近頁面頂端的下拉式清單中選取目標用戶端平台。

> [!NOTE]
> 目前支援 APN 和 FCM 平台。  
必須以權杖驗證模式設定 APN 平台。 目前不支援憑證驗證模式。 

設定通知中樞之後，您可以使用 Azure Resource Manager 用戶端或透過 Azure 入口網站提供中樞的連接字串，將其與您的「通訊服務」資源建立關聯。 連接字串應該包含 `Send` 權限。 我們建議您特別為您的中樞建立另一個具有僅限 `Send` 權限的存取原則。 深入了解[通知中樞安全性和存取原則](../../notification-hubs/notification-hubs-push-notification-security.md)

#### <a name="using-the-azure-resource-manager-client-to-link-your-notification-hub"></a>使用 Azure Resource Manager 用戶端來連結通知中樞

若要登入 Azure Resource Manager，請執行下列作業，並使用您的認證登入。

```console
armclient login
```

 成功登入之後，請執行下列動作以佈建通知中樞：

```console
armclient POST /subscriptions/<sub_id>/resourceGroups/<resource_group>/providers/Microsoft.Communication/CommunicationServices/<resource_id>/linkNotificationHub?api-version=2020-08-20-preview "{'connectionString': '<connection_string>','resourceId': '<resource_id>'}"
```

#### <a name="using-the-azure-portal-to-link-your-notification-hub"></a>使用 Azure 入口網站來連結通知中樞

在入口網站中，瀏覽至您的 Azure 通訊服務資源。 在通訊服務資源中，從 [通訊服務] 頁面的左側功能表選取 [推播通知]，然後連線您先前佈建的通知中樞。 您必須在這裡提供連接字串和資源識別碼：

:::image type="content" source="./media/notifications/acs-anh-portal-int.png" alt-text="顯示 Azure 入口網站中推播通知設定的螢幕擷取畫面。":::

> [!NOTE]
> 如果 Azure 通知中樞連接字串有更新，則必須一併更新通訊服務資源。  
中樞連結方式的任何變更，都會在最長期間 ``10`` 分鐘內反映於資料平面中 (亦即在傳送通知時)。 **如果** 之前已傳送通知，在第一次連結中樞時也適用此原則。

### <a name="device-registration"></a>裝置註冊 

請參閱[語音通話快速入門](../quickstarts/voice-video-calling/getting-started-with-calling.md)，以了解如何向通訊服務註冊您的裝置控制代碼。

### <a name="troubleshooting-guide-for-push-notifications"></a>推播通知的疑難排解指南

在裝置上看不到推播通知時，系統可能將通知捨棄在其他三個位置：

- Azure 通知中樞不接受來自 Azure 通訊服務的通知
- 平台通知系統 (例如 APN 和 FCM) 不接受來自 Azure 通知中樞的通知
- 平台通知系統未將通知傳遞給裝置。

以下涵蓋系統可能捨棄通知的第一個位置 (Azure 通知中樞不接受來自 Azure 通訊服務的通知)。 如需了解其他兩個位置，請參閱[診斷 Azure 通知中樞捨棄的通知](../../notification-hubs/notification-hubs-push-notification-fixer.md)。

若要查看您的通訊服務資源是否將通知傳送至 Azure 通知中樞，其中一種方式是從連結的 [Azure 通知中樞計量](../../azure-monitor/platform/metrics-supported.md#microsoftnotificationhubsnamespacesnotificationhubs)查看 `incoming messages` 計量。

以下是因為 Azure 通知中樞不接受來自您通訊服務資源的通知，而導致的一些常見錯誤設定。

#### <a name="azure-notification-hub-not-linked-to-the-communication-services-resource"></a>Azure 通知中樞未連結至通訊服務資源

在此情況下，您可能未將 Azure 通知中樞連結至您的通訊服務資源。 您可以查 [通知中樞佈建區段](#notification-hub-provisioning) 以了解如何連結。

#### <a name="the-linked-azure-notification-hub-isnt-configured"></a>未設定連結的 Azure 通知中樞

您必須以要使用的平台 (例如 iOS 或 Android) 平台通知系統認證來設定連結的通知中樞。 如需如何完成這項作業的詳細資訊，請參閱[在通知中樞內設定推播通知](../../notification-hubs/configure-notification-hub-portal-pns-settings.md)。

#### <a name="the-linked-azure-notification-hub-doesnt-exist"></a>連結的 Azure 通知中樞不存在

連結到您的通訊服務資源的 Azure 通知中樞已不存在。 檢查連結的通知中樞是否仍然存在。

#### <a name="the-azure-notification-hub-apns-platform-is-configured-with-certificate-authentication-mode"></a>Azure 通知中樞 APN 平台已設定憑證驗證模式

如果要使用 APN 平台搭配憑證驗證模式，目前不支援此功能。 您應該依照[在通知中樞內設定推播通知](../../notification-hubs/configure-notification-hub-portal-pns-settings.md)中所指定的權杖驗證模式來設定 APN 平台。

#### <a name="the-linked-connection-string-doesnt-have-send-permission"></a>連結的連接字串沒有 `Send` 權限

您用來將通知中樞連結到通訊服務資源的連接字串必須具有 `Send` 權限。 如需如何建立新連接字串，或從 Azure 通知中樞查看目前連接字串的詳細資訊，請參閱[通知中樞安全性和存取原則](../../notification-hubs/notification-hubs-push-notification-security.md)

#### <a name="the-linked-connection-string-or-azure-notification-hub-resourceid-arent-valid"></a>連結的連接字串或 Azure 通知中樞資源識別碼無效

請確定使用正確的連接字串和 Azure 通知中樞資源識別碼來設定通訊服務資源

#### <a name="the-linked-connection-string-is-regenerated"></a>已重新產生連結的連接字串

如果重新產生連結的 Azure 通知中樞之連接字串，您必須[重新連結通知中樞](#notification-hub-provisioning)，才能在您的通訊服務資源中更新連接字串。

## <a name="next-steps"></a>下一步

* 如需 Azure Event Grid 的簡介，請參閱[什麼是 Event Grid？](../../event-grid/overview.md)
* 若要深入了解 Azure 通知中樞的概念，請參閱 [Azure 通知中樞文件](../../notification-hubs/index.yml)