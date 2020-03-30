---
title: Azure 通知中心 iOS 13 更新 |微軟文檔
description: 瞭解 Azure 通知中心中的 iOS 13 重大更改
author: sethmanheim
ms.author: sethm
ms.date: 10/16/2019
ms.topic: article
ms.service: notification-hubs
ms.reviewer: jowargo
ms.lastreviewed: 10/16/2019
ms.openlocfilehash: 697e8ba9c9f27e8d5644e3a78950ff006290efe7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74228149"
---
# <a name="azure-notification-hubs-updates-for-ios-13"></a>iOS 13 的 Azure 通知中心更新

蘋果最近對他們的公共推送服務做了一些改變;更改大多與 iOS 13 和 Xcode 版本一致。 本文介紹這些更改對 Azure 通知中心的影響。

## <a name="apns-push-payload-changes"></a>APNS 推送有效負載更改

### <a name="apns-push-type"></a>APNS 推送類型

Apple 現在要求開發人員通過 APNS API 中的新`apns-push-type`標頭將通知標識為警報或後臺通知。 根據[Apple 的文檔](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns)："此標頭的值必須準確反映通知有效負載的內容。 如果不匹配，或者所需系統上缺少標頭，APN 可能會返回錯誤、延遲通知的傳遞或完全刪除通知。

開發人員現在必須在通過 Azure 通知中心發送通知的應用程式中設置此標頭。 由於技術限制，客戶必須使用基於權杖的身份驗證來訪問包含此屬性的請求的 APNS 憑據。 如果對 APNS 憑據使用基於證書的身份驗證，則必須切換到使用基於權杖的身份驗證。

以下代碼示例演示如何在通過 Azure 通知中心發送的通知請求中設置此標頭屬性。

#### <a name="template-notifications---net-sdk"></a>範本通知 - .NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var tempprop = new Dictionary<string, string> {{"message", "value"}};
var notification = new TemplateNotification(tempprop);
notification.Headers = headers;
await hub.SendNotificationAsync(notification);
```

#### <a name="native-notifications---net-sdk"></a>本機通知 - .NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

#### <a name="direct-rest-calls"></a>直接 REST 呼叫

```csharp
var request = new HttpRequestMessage(method, $"<resourceUri>?api-version=2017-04");
request.Headers.Add("Authorization", createToken(resourceUri, KEY_NAME, KEY_VALUE));
request.Headers.Add("ServiceBusNotification-Format", "apple");
request.Headers.Add("apns-push-type", "alert");
```

為了在此過渡期間為您提供説明，當 Azure 通知中心檢測到沒有`apns-push-type`該集的通知時，服務會從通知請求中推斷推送類型並自動設置值。 請記住，必須配置 Azure 通知中心，以便使用基於權杖的身份驗證來設置所需的標頭;因此，必須配置 Azure 通知中心，以便使用基於權杖的身份驗證來設置所需的標頭。有關詳細資訊，請參閱[APNS 的基於權杖 （HTTP/2） 身份驗證](notification-hubs-push-notification-http2-token-authentification.md)。

## <a name="apns-priority"></a>APNS 優先順序

另一個小更改（但需要更改發送通知的後端應用程式）是要求現在必須將`apns-priority`標頭設置為 5。 許多應用程式將`apns-priority`標頭設置為 10（指示立即傳遞），或者不設置標頭並獲取預設值（也是 10）。

對於後臺通知，不再允許將此值設置為 10，您必須為每個請求設置值。 如果缺少此值，Apple 將不會提供後臺通知。 例如：

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "background"}, { "apns-priority", "5" }};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

## <a name="sdk-changes"></a>SDK 更改

多年來，iOS 開發人員使用發送到`description`推送權杖委託`deviceToken`的資料的屬性來提取後端應用程式用於向設備發送通知的推送權杖。 使用 Xcode 11`description`時，該屬性更改為其他格式。 開發人員用於此屬性的現有代碼現在已損壞。 我們更新了 Azure 通知中心 SDK 以適應此更改，因此請將應用程式使用的 SDK 更新到[Azure 通知中心 iOS SDK](https://github.com/Azure/azure-notificationhubs-ios)的版本 2.0.4 或更新版本 。
