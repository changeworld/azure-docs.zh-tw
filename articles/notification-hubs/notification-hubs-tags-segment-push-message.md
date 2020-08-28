---
title: Azure 通知中樞中的路由和標記運算式
description: 瞭解如何為 Azure 通知中樞路由及標記運算式。
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 0fffb3bb-8ed8-4e0f-89e8-0de24a47f644
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 12/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 4ff8225522e79e2be40682fb5e4823777dde2aa0
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88998297"
---
# <a name="routing-and-tag-expressions"></a>路由與標記運算式

## <a name="overview"></a>概觀

標記運算式可讓您在透過通知中樞傳送推播通知時，指定特定的目標裝置或更明確的註冊。

## <a name="targeting-specific-registrations"></a>指定特定的註冊

指定特定通知註冊的唯一方法，就是關聯標記與註冊，然後再指定這些標記。 如 [註冊管理](notification-hubs-push-notification-registration-management.md)中所述，若要接收推播通知，應用程式必須在通知中樞上註冊裝置控制碼。 一旦應用程式在通知中樞上建立註冊，應用程式後端就可以傳送推播通知給它。 應用程式後端可以下列方式，選擇特定通知的目標註冊：

1. **廣播**：通知中樞中的所有註冊都會收到通知。
2. **標記**：所有包含指定標記的註冊都會收到通知。
3. **標記運算式**：所有標記設定符合指定運算式的註冊都會收到通知。

## <a name="tags"></a>標籤

標記可以是任何字串，最多120個字元，包含英數位元及下列非英數位元： ' `_` '、' `@` '、' `#` '、' `.` '、' `:` '、' `-` '。 下列範例示範的應用程式可以讓您從中接收有關特定音樂群組的快顯通知。 在此案例中，路由傳送通知的簡單方式，是使用代表不同頻帶的標記來標記註冊，如下圖所示：

![標記總覽](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags.png)

在圖中，以 **Beatles** 標記的訊息只會抵達以標記 **Beatles**註冊的平板電腦。

如需為標記建立註冊的詳細資訊，請參閱 [註冊管理](notification-hubs-push-notification-registration-management.md)。

您可以使用將通知傳送給 [Microsoft Azure 通知中樞](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) SDK 中之 `Microsoft.Azure.NotificationHubs.NotificationHubClient` 類別的方法，將通知傳送給標記。 您也可以使用 Node.js 或推播通知 REST API。  以下是 SDK 的使用範例。

```csharp
Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

// Windows 8.1 / Windows Phone 8.1
var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
"You requested a Beatles notification</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Beatles");

// Windows 10
toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
"You requested a Wailers notification</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Wailers");
```

標記不得預先布建，而且可以參考多個應用程式專屬的概念。 例如，此範例應用程式的使用者可能想要對所有樂團發表評論，但不想只收到他們評論及喜愛之樂團的快顯通知，也想要收到來自他們朋友之所有評論的快顯通知。 下圖強調此案例的範例：

![標記朋友](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags2.png)

在此範例中，Alice 對 Beatles 的更新有興趣，而 Bob 對 Wailers 的更新有興趣。 Bob 對 Charlie 的評論也有興趣，Charlie 對 Wailers 感興趣。 針對 Beatles 上的 Charlie 批註傳送通知時，通知中樞會將它傳送給 Alice 和 Bob。

雖然您可以針對標記中的多個考慮進行編碼 (例如 `band_Beatles` 或 `follows_Charlie`) ，但標記是簡單字串，而不是具有值的屬性。 註冊僅符合特定標記的存在與否。

如何使用標記傳送到您所關注之群組的完整逐步教學課程，請參閱 [即時新聞](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)。

> [!NOTE]
> Azure 通知中樞支援每註冊最多 60 個標籤。

## <a name="using-tags-to-target-users"></a>使用標記指定使用者

使用標記的另一種方式是識別與特定使用者相關聯的所有裝置。 您可以使用包含使用者識別碼的標記來標記註冊，如下圖所示：

![標記使用者](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags3.png)

在圖中，標記的訊息會 `user_Alice` 到達標記為的所有裝置 `user_Alice` 。

## <a name="tag-expressions"></a>標記運算式

在某些情況下，通知必須以一組指定的註冊為目標，而不是由單一標記所識別，而是使用標記的布林運算式。

假設有一支運動應用程式會將提醒傳送給波士頓的每個人，告知他們有關於紅襪隊與紅雀隊之間的賽事訊息。 若用戶端應用程式註冊關於球隊與地點的標記，則通知的目標應為波士頓中位關注紅襪隊或紅雀隊的每個人。 此條件可以下列布林運算式表示：

```csharp
(follows_RedSox || follows_Cardinals) && location_Boston
```

![標記運算式](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags4.png)

標記運算式支援常見的布林運算子 `AND` ，例如 (`&&`) 、 `OR` (`||`) 和 `NOT` (`!`) ; 它們也可以包含括弧。 只有使用 `OR` 運算子的標記運算式可以參考20個標籤; expression with `AND` 運算子，但不 `OR` 能有運算子可以參考10個標記; 否則，標記運算式的限制為6個標記。

以下是使用 SDK 以標記運算式傳送通知的範例：

```csharp
Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

String userTag = "(location_Boston && !follows_Cardinals)";

// Windows 8.1 / Windows Phone 8.1
var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
"You want info on the Red Sox</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

// Windows 10
toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
"You want info on the Red Sox</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
```
