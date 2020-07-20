---
title: 包含檔案
description: 包含檔案
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 11/07/2019
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 48907713082ebb1008ad963121671b36af7f2731
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "74228163"
---
在本節中，您會從 .NET 主控台應用程式將即時新聞以加註標記的範本通知形式傳送。

1. 在 Visual Studio 中，建立新的 Visual C# 主控台應用程式：
    1. 在主功能表上，選取 [檔案]   > [新增]   > [專案]  。
    1. 在 [建立新專案]  中，針對 C# 在範本清單中選取 [主控台應用程式 \(.NET Framework\)]  ，接著選取 [下一步]  。
    1. 輸入應用程式的名稱。
    1. 在 [解決方案]  中，選擇 [新增到解決方案]  ，再選取 [建立]  以建立專案。

1. 選取 [工具]   > [NuGet 套件管理員]   > [套件管理員主控台]  ，然後在主控台視窗中執行下列命令：

   ```powershell
   Install-Package Microsoft.Azure.NotificationHubs
   ```

   此動作會使用 [Microsoft.Azure.NotificationHubs] 套件來新增對 Azure 通知中樞 SDK 的參考。

1. 開啟 *Program.cs* 檔案，並新增下列 `using` 陳述式：

   ```csharp
   using Microsoft.Azure.NotificationHubs;
   ```

1. 在 `Program` 類別中，新增或取代 (如果方法已存在) 下列方法：

    ```csharp
    private static async void SendTemplateNotificationAsync()
    {
        // Define the notification hub.
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");

        // Apple requires the apns-push-type header for all requests
        var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};

        // Create an array of breaking news categories.
        var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};

        // Send the notification as a template notification. All template registrations that contain
        // "messageParam" and the proper tags will receive the notifications.
        // This includes APNS, GCM/FCM, WNS, and MPNS template registrations.

        Dictionary<string, string> templateParams = new Dictionary<string, string>();

        foreach (var category in categories)
        {
            templateParams["messageParam"] = "Breaking " + category + " News!";
            await hub.SendTemplateNotificationAsync(templateParams, category);
        }
    }
    ```

   此程式碼會分別將範本通知傳送給字串陣列中的六個標籤。 使用標籤可確保裝置只會收到已登錄類別的通知。

1. 在上述程式碼中，請使用您的通知中樞名稱及通知中樞儀表板的 *DefaultFullSharedAccessSignature* 連接字串，來取代 `<hub name>` 和 `<connection string with full access>` 預留位置。

1. 在 `Main()` 方法中新增下列程式碼行：

   ```csharp
    SendTemplateNotificationAsync();
    Console.ReadLine();
    ```

1. 建置主控台應用程式。

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[Get started with Notification Hubs]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Notification Hubs REST interface]: https://msdn.microsoft.com/library/windowsazure/dn223264.aspx
[Add push notifications for Mobile Apps]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[How to use Notification Hubs from Java or PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[Microsoft.Azure.NotificationHubs]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/
