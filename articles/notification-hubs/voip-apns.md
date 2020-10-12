---
title: 使用 Azure 通知中樞傳送 APNS VOIP 通知
description: 瞭解如何透過 Azure 通知中樞傳送 APNS VOIP 通知 (未正式支援) 。
author: sethmanheim
ms.author: sethm
ms.date: 3/23/2020
ms.topic: how-to
ms.service: notification-hubs
ms.openlocfilehash: c99af881b8f93b75633741c2352dc5df17dd2963
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80146884"
---
# <a name="use-apns-voip-through-notification-hubs-not-officially-supported"></a>透過通知中樞使用 APNS VOIP (未正式支援) 

您可以透過 Azure 通知中樞使用 APNS VOIP 通知;不過，此案例不提供官方支援。

## <a name="considerations"></a>考量

如果您仍選擇透過通知中樞傳送 APNS VOIP 通知，請注意下列限制：

- 傳送 VOIP 通知需要將 `apns-topic` 標頭設為應用程式套件組合識別碼 + `.voip` 尾碼。 例如，針對具有套件組合識別碼的範例應用程式 `com.microsoft.nhubsample` ， `apns-topic` 標頭應設定為 `com.microsoft.nhubsample.voip.`

   這個方法不適用於 Azure 通知中樞，因為應用程式的套件組合識別碼必須設定為中樞 APNS 認證的一部分，而且值無法變更。 此外，通知中樞不允許 `apns-topic` 在執行時間覆寫標頭值。

   若要傳送 VOIP 通知，您必須使用應用程式套件組合識別碼來設定個別的通知中樞 `.voip` 。

- 傳送 VOIP 通知需要將 `apns-push-type` 標頭設定為值 `voip` 。

   為了協助客戶轉換至 iOS 13，通知中樞會嘗試推斷正確的 `apns-push-type` 標頭值。 推斷邏輯是刻意很簡單的工作，以避免中斷標準的通知。 可惜的是，這個方法會造成 VOIP 通知的問題，因為 Apple 會將 VOIP 通知視為不遵循與標準通知相同規則的特殊案例。

   若要傳送 VOIP 通知，您必須指定標頭的明確值 `apns-push-type` 。

- 通知中樞會將 APNS 承載限制為 4 KB，如 Apple 所記載。 針對 VOIP 通知，Apple 允許最多 5 KB 的承載。 通知中樞不會區分標準和 VOIP 通知;因此，所有通知的限制為 4 KB。

   若要傳送 VOIP 通知，您不得超過 4 KB 的承載大小限制。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列連結：

- [ `apns-topic` 和 `apns-push-type` 標頭和值的檔，包括 VOIP 通知的特殊案例](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns)。

- 承載[大小限制的檔](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)。

- [適用于 iOS 13 的通知中樞更新](push-notification-updates-ios-13.md#apns-push-type)。
