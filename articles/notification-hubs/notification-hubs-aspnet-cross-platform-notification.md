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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80127036"
---
# <a name="send-cross-platform-notifications-with-azure-notification-hubs"></a>使用 Azure 通知中樞傳送跨平臺通知

本教學課程是以先前的教學課程為基礎，[使用 Azure 通知中樞將通知傳送給特定使用者]。 該教學課程說明如何將通知推送至已向特定已驗證使用者註冊的所有裝置。 該方法需要多個要求，以傳送通知給每個支援的用戶端平臺。 Azure 通知中心可支援範本，讓您指定特定裝置接收通知的方式。 此方法使得傳送跨平台通知變得更簡單。

本文示範如何利用範本來傳送以所有平臺為目標的通知。 本文使用單一要求來傳送平臺中性通知。 如需範本的詳細資訊，請參閱[通知中樞總覽][Templates]。

> [!IMPORTANT]
> Visual Studio 2019 不支援 Windows Phone 專案8.1 和更早版本。 如需詳細資訊，請參閱 [Visual Studio 2019 平台目標及相容性](/visualstudio/releases/2019/compatibility)。

> [!NOTE]
> 透過通知中樞，裝置可使用相同標籤註冊多個範本。 在此情況下，當傳入的訊息符合該標籤時，就會有多個通知傳遞至裝置 (每個通知各用於一個範本)。 透過此流程，您就能讓相同訊息顯示在多個視覺通知中，例如以徽章形式和 Windows 市集應用程式中的快顯通知形式。

## <a name="send-cross-platform-notifications-using-templates"></a>使用範本傳送跨平台通知

本節使用您在[使用 Azure 通知中樞將通知傳送至特定使用者]教學課程中所建立的範例程式碼。 您可以從 [GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/NotifyUsers) 下載範例。

若要使用範本傳送跨平臺通知，請執行下列步驟：

1. 在**方案總管**的 Visual Studio 中，展開 [**控制器**] 資料夾，然後開啟*RegisterController.cs*檔案。

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

1. 在**方案總管**的 [**控制器**] 資料夾中，開啟*NotificationsController.cs*檔案。 以下列程式碼取代 `Post` 方法：

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

    此程式碼會同時將通知傳送至所有平臺。 您未指定原生裝載。 通知中樞將以所提供的 tag 值 (指定於註冊的範本中) 建置並傳遞正確的裝載到每個裝置。

1. 重新發佈您的 Web API 專案。

1. 再次執行用戶端應用程式，以確認註冊已成功。

1. （選擇性）將用戶端應用程式部署到第二個裝置，然後執行應用程式。 每個裝置上都會顯示通知。

## <a name="next-steps"></a>後續步驟

現在您已完成本教學課程，請在下列文章中深入瞭解通知中樞和範本：

* 如需使用範本的不同案例，請參閱將[通知推送至執行通用 Windows 平臺應用程式的特定 Windows 裝置][Use Notification Hubs to send breaking news]教學課程。
* 如需範本的詳細資訊，請參閱[通知中樞總覽][Templates]。

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
