---
title: 使用 Azure 通知中樞將推播通知傳送至 Xamarin | Microsoft Docs
description: 在本教學課程中，您將了解如何使用 Azure 通知中樞將推播通知傳送至 Xamarin.iOS 應用程式。
services: notification-hubs
keywords: ios 推播通知,推播訊息,推播通知
documentationcenter: xamarin
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/12/2021
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 05/23/2019
ms.openlocfilehash: ff1e5edad05ebd7157f71ad2e099ea88905be4f3
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98221131"
---
# <a name="tutorial-send-push-notifications-to-xamarinios-apps-using-azure-notification-hubs"></a>教學課程：使用 Azure 通知中樞將推播通知傳送至 Xamarin.iOS 應用程式

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>概觀

本教學課程示範如何使用 Azure 通知中樞將推播通知傳送至 iOS 應用程式。 您會使用 [Apple Push Notification Service (APNS)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html) 建立可接收推播通知的空白 Xamarin.iOS 應用程式。

完成之後，您將可使用通知中樞，將推播通知廣播到所有執行您的應用程式的裝置。 [NotificationHubs 應用程式][GitHub]範例中提供完成的程式碼。

在本教學課程中，您會建立/更新程式碼以執行下列工作：

> [!div class="checklist"]
> * 產生憑證簽署要求檔案
> * 針對推播通知註冊應用程式
> * 建立應用程式的佈建設定檔
> * 針對 iOS 推播通知設定您的通知中樞
> * 傳送測試推播通知

## <a name="prerequisites"></a>Prerequisites

* **Azure 訂用帳戶**。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 最新版的 [Xcode][Install Xcode]
* iOS 10 (或更新版本) 相容的裝置
* [Apple Developer Program](https://developer.apple.com/programs/) 成員資格。
* [Visual Studio for Mac]
  
  > [!NOTE]
  > 基於 iOS 推播通知的組態需求，您必須在實體 iOS 裝置 (iPhone 或 iPad) 上部署和測試範例應用程式，而不是在模擬器中。

完成本教學課程是 Xamarin.iOS 應用程式的所有其他通知中樞教學課程的先決條件。

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-app-to-the-notification-hub"></a>將您的應用程式連接到通知中樞

### <a name="create-a-new-project"></a>建立新專案

1. 在 Visual Studio 中建立新的 iOS 專案，並選取 [單一檢視應用程式]  範本，然後按 [下一步] 

     ![Visual Studio - 選取應用程式類型][31]

2. 輸入應用程式名稱和組織識別碼，然後按 [下一步]  和 [建立]  。

3. 在 [方案] 檢視中，按兩下 *Into.plist*，並確定 [身分識別]  下方的套件組合識別碼符合您在建立佈建設定檔時所使用的套件組合識別碼。 在 [簽署]  下方，確定您已於 [小組]  下方選取您的開發人員帳戶，已選取 [自動管理簽署]，並已自動選取您的簽署憑證和佈建設定檔。

    ![Visual Studio- iOS 應用程式組態][32]

4. 在 [解決方案] 檢視中按兩下 `Entitlements.plist`，並確定已核取 [啟用推播通知]  。

    ![Visual Studio- iOS 權利組態][33]

5. 新增 Azure 傳訊套件。 在 [方案] 檢視中，以滑鼠右鍵按一下專案，然後選取 [新增]   > [新增 NuGet 套件]  。 搜尋 **Xamarin.Azure.NotificationHubs.iOS**，並將此套件新增至專案中。

6. 在您的類別中新增檔案，將其命名為 `Constants.cs`，然後新增下列變數並以 先前記下的 `hubname` 和 `DefaultListenSharedAccessSignature` 取代字串常值預留位置。

    ```csharp
    // Azure app-specific connection string and hub path
    public const string ListenConnectionString = "<Azure DefaultListenSharedAccess Connection String>";
    public const string NotificationHubName = "<Azure Notification Hub Name>";
    ```

7. 在 `AppDelegate.cs` 中新增下列 using 陳述式：

    ```csharp
    using WindowsAzure.Messaging.NotificationHubs;
    using UserNotifications
    ```

8. 在中建立的實作為 `MSNotificationHubDelegate` `AppDelegate.cs` ：

    ```csharp
    public class AzureNotificationHubListener : MSNotificationHubDelegate
    {
        public override void DidReceivePushNotification(MSNotificationHub notificationHub, MSNotificationHubMessage message)
        {

        }
    }
    ```

9. 在 `AppDelegate.cs` 中，更新 `FinishedLaunching()` 以符合下列程式碼：

    ```csharp
    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        // Set the Message listener
        MSNotificationHub.SetDelegate(new AzureNotificationHubListener());
        
        // Start the SDK
        MSNotificationHub.Start(ListenConnectionString, NotificationHubName);

        return true;
    }
    ```

10. 在中 `AppDelegate.cs` ，執行 `DidReceivePushNotification` 類別的方法 `AzureNotificationHubListener` ：

    ```csharp
    public override void DidReceivePushNotification(MSNotificationHub notificationHub, MSNotificationHubMessage message)
    {
        // This sample assumes { aps: { alert: { title: "Hello", body: "World" } } }
        var alertTitle = message.Title ?? "Notification";
        var alertBody = message.Body;

        var myAlert = UIAlertController.Create(alertTitle, alertBody, UIAlertControllerStyle.Alert);
        myAlert.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
        UIApplication.SharedApplication.KeyWindow.RootViewController.PresentViewController(myAlert, true, null);
    }
    ```

11. 在您的裝置上執行應用程式。

## <a name="send-test-push-notifications"></a>傳送測試推播通知

您可以在 [Azure 入口網站] 中，使用 [測試傳送] 選項測試應用程式能否接收通知。 它會將測試推播通知傳送至您的裝置。

![Azure 入口網站 - 測試傳送][30]

推播通知通常會以後端服務傳送，例如 Mobile Apps 或使用相容程式庫的 ASP.NET。 如果您的後端無法使用程式庫，您也可以直接使用 REST API 來傳送通知訊息。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已將廣播通知傳送至所有向後端註冊的 iOS 裝置。 若要了解如何將通知推送至特定 iOS 裝置，請繼續進行下列教學課程：

> [!div class="nextstepaction"]
>[將通知推送至特定裝置](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png
[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png
[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png
[32]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-app-settings.png
[33]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-entitlements-settings.png

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: https://go.microsoft.com/fwlink/p/?LinkId=272456
[Visual Studio for Mac]: https://visualstudio.microsoft.com/vs/mac/
[Local and Push Notification Programming Guide]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/HandlingRemoteNotifications.html#//apple_ref/doc/uid/TP40008194-CH6-SW1
[Apple Push Notification Service]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[Apple Push Notification Service fwlink]: https://go.microsoft.com/fwlink/p/?LinkId=272584
[GitHub]: https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs
[Azure 入口網站]: https://portal.azure.com
