---
title: Azure 通知中心的路由和標記運算式
description: 瞭解如何為 Azure 通知中心路由和標記運算式。
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
ms.openlocfilehash: 2432ac41645e373ea3a87ff7e69ef02a4e30c81d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062301"
---
# <a name="routing-and-tag-expressions"></a>路由與標記運算式

## <a name="overview"></a>總覽

標記運算式可讓您在透過通知中樞傳送推播通知時，指定特定的目標裝置或更明確的註冊。

## <a name="targeting-specific-registrations"></a>指定特定的註冊

指定特定通知註冊的唯一方法，就是關聯標記與註冊，然後再指定這些標記。 如[註冊管理](notification-hubs-push-notification-registration-management.md)中所述，要接收推送通知，應用必須在通知中心上註冊設備控制碼。 應用在通知中心上創建註冊後，應用程式後端可以向其發送推送通知。 應用程式後端可以下列方式，選擇特定通知的目標註冊：

1. **廣播**：通知中樞中的所有註冊都會收到通知。
2. **標記**：所有包含指定標記的註冊都會收到通知。
3. **標記運算式**：所有標記設定符合指定運算式的註冊都會收到通知。

## <a name="tags"></a>Tags

標記可以是任何字串，最多 120 個字元，包含字母數位和以下非字母數位字元："'''，"''，"''，"''，"''，"'''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''`_` `@` `#` `.` `:` `-` 下列範例示範的應用程式可以讓您從中接收有關特定音樂群組的快顯通知。 在這種情況下，路由通知的簡單方法是使用表示不同波段的標記標記標記註冊，如下圖所示：

![標籤概述](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags.png)

在圖中，帶有**披頭士**標籤的消息只到達註冊了**披頭士**標籤的平板電腦。

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

標記不能預先預配，並且可以引用多個特定于應用的概念。 例如，此範例應用程式的使用者可能想要對所有樂團發表評論，但不想只收到他們評論及喜愛之樂團的快顯通知，也想要收到來自他們朋友之所有評論的快顯通知。 下圖突出顯示了此方案的示例：

![標記好友](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags2.png)

在此示例中，Alice 對披頭士樂隊的更新感興趣，而 Bob 對懷勒一家的更新感興趣。 鮑勃也對查理的評論感興趣，查理對威勒一家感興趣。 當發送通知，要求 Charlie 對披頭士發表評論時，通知中心會將其發送給 Alice 和 Bob。

雖然您可以在標記中編碼多個問題（例如，`band_Beatles`或`follows_Charlie`），標記是簡單的字串，而不是具有值的屬性。 註冊僅在存在或不存在特定標記時匹配。

如何使用標記傳送到您所關注之群組的完整逐步教學課程，請參閱 [即時新聞](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)。

> [!NOTE]
> Azure 通知中樞支援每註冊最多 60 個標籤。

## <a name="using-tags-to-target-users"></a>使用標記指定使用者

使用標記的另一種方法是標識與特定使用者關聯的所有設備。 您可以使用包含使用者 ID 的標記標記標記"註冊"，如下圖所示：

![標記使用者](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags3.png)

在圖中，標記`user_Alice`的消息將到達使用`user_Alice`標記的所有設備。

## <a name="tag-expressions"></a>標記運算式

在某些情況下，通知必須針對不是由單個標記標識的一組註冊，而是由使用標記的布林運算式標識的。

假設有一支運動應用程式會將提醒傳送給波士頓的每個人，告知他們有關於紅襪隊與紅雀隊之間的賽事訊息。 若用戶端應用程式註冊關於球隊與地點的標記，則通知的目標應為波士頓中位關注紅襪隊或紅雀隊的每個人。 此條件可以下列布林運算式表示：

```csharp
(follows_RedSox || follows_Cardinals) && location_Boston
```

![標記運算式](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags4.png)

標記運算式支援常見的布林運算子，`AND`如 （`&&`、 `OR` `||`和`NOT`（`!`它們還可以包含括弧。 僅使用運算子的標記`OR`運算式可以引用 20 個標記;但是，使用標記運算式就可以引用 20 個標記。運算式與`AND`運算子，但沒有`OR`運算子可以引用 10 個標記;否則，標記運算式限制為 6 個標記。

下面是使用 SDK 使用標記運算式發送通知的示例：

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
