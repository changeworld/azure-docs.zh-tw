---
title: 使用 Azure 通知中樞向使用者傳送跨平台通知 (ASP.NET)
description: 了解如何使用通知中樞範本，在單一要求中傳送以所有平台為目標的跨平台通知。
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 11d2131b-f683-47fd-a691-4cdfc696f62b
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
ms.date: 09/30/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: fc3cec348517244c8a7f54d2d3d17298c58e4262
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127036"
---
# <a name="send-cross-platform-notifications-with-azure-notification-hubs"></a>使用 Azure 通知中心發送跨平臺通知

本教程基於前面的教程"[使用 Azure 通知中心向特定使用者發送通知]"。 本教程介紹如何將通知推送到註冊到特定經過身份驗證的使用者的所有設備。 此方法需要多個請求才能向每個受支援的用戶端平臺發送通知。 Azure 通知中心可支援範本，讓您指定特定裝置接收通知的方式。 此方法使得傳送跨平台通知變得更簡單。

本文演示如何利用範本發送針對所有平臺的通知。 本文使用單個要求傳送平臺中立通知。 有關範本的詳細資訊，請參閱[通知中心概述][Templates]。

> [!IMPORTANT]
> Visual Studio 2019 不支援 Windows Phone 專案 8.1 和更早版本。 如需詳細資訊，請參閱 [Visual Studio 2019 平台目標及相容性](/visualstudio/releases/2019/compatibility)。

> [!NOTE]
> 透過通知中樞，裝置可使用相同標籤註冊多個範本。 在此情況下，當傳入的訊息符合該標籤時，就會有多個通知傳遞至裝置 (每個通知各用於一個範本)。 透過此流程，您就能讓相同訊息顯示在多個視覺通知中，例如以徽章形式和 Windows 市集應用程式中的快顯通知形式。

## <a name="send-cross-platform-notifications-using-templates"></a>使用範本傳送跨平台通知

本節[使用使用 Azure 通知中心教程在"向特定使用者發送通知]"中構建的示例代碼。 您可以從 [GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/NotifyUsers) 下載範例。

要使用範本發送跨平臺通知，請執行以下步驟：

1. 在**解決方案資源管理器**中的視覺化工作室中，展開**控制器**資料夾，然後打開*RegisterController.cs*檔。

1. 在 `Put` 方法中找出建立新註冊的程式碼區塊，並將 `switch` 內容取代為下列程式碼：

    ```csharp
    switch (deviceUpdate.Platform)
    {
        case "mpns":
            var toastTemplate = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                    "<wp:Toast>" +
                        "<wp:Text1>$(message)</wp:Text1>" +
                    "</wp:Toast> " +
                "</wp:Notification>";
            registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "wns":
            toastTemplate = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
            registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "apns":
            var alertTemplate = "{\"aps\":{\"alert\":\"$(message)\"}}";
            registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
            break;
        case "fcm":
            var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
            registration = new FcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
            break;
        default:
            throw new HttpResponseException(HttpStatusCode.BadRequest);
    }
    ```

    這段程式碼會呼叫平台特有方法來建立範本註冊，而非原生註冊。 因為範本註冊源自原生註冊，因此不需要修改現有註冊。

1. 在 **"解決方案資源管理器**"中，在 **"控制器"** 資料夾中打開*NotificationsController.cs*檔。 以下列程式碼取代 `Post` 方法：

    ```csharp
    public async Task<HttpResponseMessage> Post()
    {
        var user = HttpContext.Current.User.Identity.Name;
        var userTag = "username:" + user;

        var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
        await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```

    此代碼同時向所有平臺發送通知。 您不指定本機有效負載。 通知中樞將以所提供的 tag 值 (指定於註冊的範本中) 建置並傳遞正確的裝載到每個裝置。

1. 重新發佈 Web API 專案。

1. 再次運行用戶端應用以驗證註冊是否成功。

1. 或者，將用戶端應用部署到第二個設備，然後運行該應用。 每個裝置上都會顯示通知。

## <a name="next-steps"></a>後續步驟

完成本教程後，請在這些文章中瞭解有關通知中心和範本的更多資訊：

* 有關使用範本的不同方案，請參閱[將通知推送到運行通用 Windows 平臺應用程式的特定 Windows 設備][Use Notification Hubs to send breaking news]教程。
* 有關範本的更多詳細資訊，請參閱[通知中心概述][Templates]。

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Push to users ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Push to users Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Visual Studio 2012 Express for Windows 8]: https://visualstudio.microsoft.com/downloads/

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: https://go.microsoft.com/fwlink/p/?LinkId=314257
[使用 Azure 通知中樞將通知傳送給特定使用者]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: https://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: https://msdn.microsoft.com/library/windowsazure/jj927172.aspx
