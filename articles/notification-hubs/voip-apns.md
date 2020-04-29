---
title: 使用 Azure 通知中樞傳送 APNS VOIP 通知
description: 瞭解如何透過 Azure 通知中樞（未正式支援）傳送 APNS VOIP 通知。
author: sethmanheim
ms.author: sethm
ms.date: 3/23/2020
ms.topic: how-to
ms.service: notification-hubs
ms.openlocfilehash: c99af881b8f93b75633741c2352dc5df17dd2963
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80146884"
---
# <a name="use-apns-voip-through-notification-hubs-not-officially-supported"></a>透過通知中樞使用 APNS VOIP （未正式支援）

您可以透過 Azure 通知中樞來使用 APNS VOIP 通知;不過，此案例沒有官方支援。

## <a name="considerations"></a>考量

如果您仍然選擇透過通知中樞傳送 APNS VOIP 通知，請注意下列限制：

- 傳送 VOIP 通知需要將`apns-topic`標頭設定為應用程式套件組合識別碼 + `.voip`尾碼。 例如，針對具有配套識別碼`com.microsoft.nhubsample`的範例應用程式， `apns-topic`標頭應該設定為`com.microsoft.nhubsample.voip.`

   這個方法不適用於 Azure 通知中樞，因為應用程式的套件組合識別碼必須設定為中樞的 APNS 認證的一部分，而且無法變更此值。 此外，通知中樞不允許在執行時間覆寫`apns-topic`標頭的值。

   若要傳送 VOIP 通知，您必須使用`.voip`應用程式套件組合識別碼來設定個別的通知中樞。

- 傳送 VOIP 通知需要將`apns-push-type`標頭設定為值。 `voip`

   為了協助客戶轉換到 iOS 13，通知中樞嘗試推斷正確的`apns-push-type`標頭值。 推斷邏輯是刻意簡單的工作，以避免中斷標準通知。 可惜的是，此方法會導致 VOIP 通知的問題，因為 Apple 會將 VOIP 通知視為不遵循與標準通知相同規則的特殊案例。

   若要傳送 VOIP 通知，您必須為`apns-push-type`標頭指定明確的值。

- 通知中樞會將 APNS 承載限制為 4 KB，如 Apple 所記載。 針對 VOIP 通知，Apple 允許最多 5 KB 的承載。 通知中樞不會區分標準和 VOIP 通知;因此，所有通知都限制為 4 KB。

   若要傳送 VOIP 通知，您不得超過 4 KB 的承載大小限制。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列連結：

- 和標頭[ `apns-push-type`和值的檔，包括 VOIP 通知的特殊`apns-topic`案例](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns)。

- 裝載[大小限制的檔](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)。

- [通知中樞 iOS 13 的更新](push-notification-updates-ios-13.md#apns-push-type)。
