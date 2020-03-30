---
title: 如何傳送排定通知 | Microsoft Docs
description: 本主題說明如何透過 Azure 通知中樞使用排定通知。
services: notification-hubs
documentationcenter: .net
keywords: 推播通知,推播通知,排程推播通知
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: ef5eedaa903480ae670f9bc48d0af89744a99d22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "71213012"
---
# <a name="how-to-send-scheduled-notifications"></a>作法：傳送排定通知

如果您想要在未來某個時間點傳送通知，但卻沒有簡單的方法可喚醒您的後端程式碼來傳送通知。 標準層通知中樞支援的功能可讓您安排未來 7 天的通知。


## <a name="schedule-your-notifications"></a>排程通知
發送通知時，只需在通知中心 SDK 中使用[`ScheduledNotification`類](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx)，如以下示例所示：

```csharp
Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));
```

## <a name="cancel-scheduled-notifications"></a>取消已排程的通知
此外，您也可以使用其 notificationId 取消先前已排程的通知︰

```csharp
await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);
```

您可以傳送的排定通知數目沒有限制。

## <a name="next-steps"></a>後續步驟

請參閱下列教學課程：

 - [將推播通知傳送到所有已註冊的裝置](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
 - [將通知推送至特定裝置](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)
 - [推送當地語系化的通知](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
 - [將推播通知傳送給特定使用者](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) 
 - [推送以位置為基礎的通知](notification-hubs-push-bing-spatial-data-geofencing-notification.md)
