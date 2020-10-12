---
title: Azure 通知中樞 iOS 13 更新 |Microsoft Docs
description: 瞭解 Azure 通知中樞的 iOS 13 重大變更
author: sethmanheim
ms.author: sethm
ms.date: 10/16/2019
ms.topic: article
ms.service: notification-hubs
ms.reviewer: jowargo
ms.lastreviewed: 10/16/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: df8560bec3671a9f05628ee6ed8ea95c31e9b16f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88998045"
---
# <a name="azure-notification-hubs-updates-for-ios-13"></a>適用于 iOS 13 的 Azure 通知中樞更新

Apple 最近對其公用推送服務進行了一些變更;這些變更大多與 iOS 13 和 Xcode 的版本一致。 本文說明這些變更對 Azure 通知中樞的影響。

## <a name="apns-push-payload-changes"></a>APNS 推送承載變更

### <a name="apns-push-type"></a>APNS 推送類型

Apple 現在要求開發人員透過 APNS API 中的新標頭，將通知識別為警示或背景通知 `apns-push-type` 。 根據 [Apple 的檔](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns)：「此標頭的值必須正確地反映通知內容的內容。 如果有不相符的情況，或必要系統上缺少標頭，APNs 可能會傳回錯誤、延遲通知的傳遞，或將其全部捨棄。」

開發人員現在必須在透過 Azure 通知中樞傳送通知的應用程式中設定此標頭。 由於技術限制，客戶必須針對 APNS 認證使用權杖型驗證，以提供包含此屬性的要求。 如果您要針對 APNS 認證使用憑證型驗證，則必須切換為使用權杖型驗證。

下列程式碼範例示範如何在透過 Azure 通知中樞傳送的通知要求中設定此標頭屬性。

#### <a name="template-notifications---net-sdk"></a>範本通知-.NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var tempprop = new Dictionary<string, string> {{"message", "value"}};
var notification = new TemplateNotification(tempprop);
notification.Headers = headers;
await hub.SendNotificationAsync(notification);
```

#### <a name="native-notifications---net-sdk"></a>原生通知-.NET SDK

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

為了在此轉換期間協助您，當 Azure 通知中樞偵測到沒有該設定的通知時， `apns-push-type` 服務會從通知要求推斷推播類型，並自動設定該值。 請記住，您必須將 Azure 通知中樞設定為使用權杖型驗證來設定必要的標頭;如需詳細資訊，請參閱 [權杖型 (HTTP/2) APNS 驗證](./notification-hubs-push-notification-http2-token-authentication.md)。

## <a name="apns-priority"></a>APNS 優先順序

另一個次要變更，但需要變更傳送通知的後端應用程式，則是背景通知的要求， `apns-priority` 標頭現在必須設定為5。 許多應用程式 `apns-priority` 會將標頭設定為 10 (指出立即傳遞) ，或未設定，並取得預設值 (也就是 10) 。

背景通知不再允許將此值設定為10，您必須設定每個要求的值。 如果遺漏此值，Apple 將不會傳遞背景通知。 例如：

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "background"}, { "apns-priority", "5" }};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

## <a name="sdk-changes"></a>SDK 變更

多年來，iOS 開發人員使用 `description` `deviceToken` 傳送至推播權杖委派的資料屬性，將後端應用程式用來傳送通知的推播權杖解壓縮至裝置。 使用 Xcode 11 時，該 `description` 屬性會變更為不同的格式。 開發人員用於這個屬性的現有程式碼現在已中斷。 我們已更新 Azure 通知中樞 SDK 以因應這項變更，因此請將您的應用程式所使用的 SDK 更新為 [Azure 通知中樞 IOS SDK](https://github.com/Azure/azure-notificationhubs-ios)的版本2.0.4 版或更新版本。
