---
title: 使用 Azure 通知中心發送 APNS VOIP 通知
description: 瞭解如何通過 Azure 通知中心（不受官方支援）發送 APNS VOIP 通知。
author: sethmanheim
ms.author: sethm
ms.date: 3/23/2020
ms.topic: how-to
ms.service: notification-hubs
ms.openlocfilehash: c99af881b8f93b75633741c2352dc5df17dd2963
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80146884"
---
# <a name="use-apns-voip-through-notification-hubs-not-officially-supported"></a>通過通知中心使用 APNS VOIP（不受官方支援）

可以通過 Azure 通知中心使用 APNS VOIP 通知;然而，沒有官方支援這一方案。

## <a name="considerations"></a>考量

如果您仍然選擇通過通知中心發送 APNS VOIP 通知，請注意以下限制：

- 發送 VOIP 通知需要`apns-topic`將標頭設置為應用程式捆綁包 ID 和`.voip`尾碼。 例如，對於具有捆綁 ID`com.microsoft.nhubsample`的示例應用，`apns-topic`應將標頭設置為`com.microsoft.nhubsample.voip.`

   此方法不能很好地使用 Azure 通知中心，因為應用的捆綁 ID 必須配置為集線器的 APNS 憑據的一部分，並且無法更改該值。 此外，通知中心不允許在運行時重寫`apns-topic`標頭的值。

   要發送 VOIP 通知，必須使用`.voip`應用捆綁包 ID 配置單獨的通知中心。

- 發送 VOIP 通知要求將`apns-push-type`標頭設置為 值`voip`。

   為了説明客戶過渡到 iOS 13，通知中心嘗試推斷`apns-push-type`標頭的正確值。 推理邏輯故意很簡單，以避免違反標準通知。 遺憾的是，此方法會導致 VOIP 通知出現問題，因為 Apple 將 VOIP 通知視為不符合標準通知的相同規則的特殊情況。

   要發送 VOIP 通知，必須為`apns-push-type`標頭指定顯式值。

- 通知中心將 APNS 負載限制為 4 KB，如 Apple 所記錄的那樣。 對於 VOIP 通知，Apple 允許有效負載高達 5 KB。 通知中心不區分標準和 VOIP 通知;因此，通知中心不會區分"通知"和"VOIP"通知。因此，所有通知都限制為 4 KB。

   要發送 VOIP 通知，您不得超過 4 KB 有效負載大小限制。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列連結：

- [文檔和`apns-push-type`標頭和值，包括 VOIP 通知的特殊情況。 `apns-topic` ](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns)

- [有效負載大小限制的文檔](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)。

- [iOS 13 的通知中心更新](push-notification-updates-ios-13.md#apns-push-type)。
