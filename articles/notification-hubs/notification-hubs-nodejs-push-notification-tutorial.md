---
title: 使用 Azure 通知中樞和 Node.js 傳送推播通知
description: 了解如何使用通知中樞，從 Node.js 應用程式傳送推播通知。
keywords: 推播通知, 推播通知, node.js 推播,ios 推播
services: notification-hubs
documentationcenter: nodejs
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ded4749c-6c39-4ff8-b2cf-1927b3e92f93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 04/29/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-js
ms.openlocfilehash: 34962686ae36764a6dc16b164e91004be9ef038d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91250414"
---
# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>使用 Azure 通知中樞和 Node.js 傳送推播通知

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

## <a name="overview"></a>概觀

> [!IMPORTANT]
> 若要完成此教學課程，您必須具備有效的 Azure 帳戶。 如果沒有帳戶，您可以透過 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs)，在幾分鐘內建立一個免費試用帳戶。

本指南會示範如何透過 Azure 通知中樞的協助，直接從 [Node.js](https://nodejs.org) 應用程式傳送推播通知。

涵蓋的案例包括在下列平台將推播通知傳送至應用程式：

- Android
- iOS
- 通用 Windows 平台
- Windows Phone

## <a name="notification-hubs"></a>通知中樞

Azure 通知中樞提供易用、多平台、可調整的基礎結構，用以將推播通知傳送至行動裝置。 如需服務基礎結構的詳細資訊，請參閱 [Azure 通知中樞](/previous-versions/azure/azure-services/jj927170(v=azure.100)) 頁面。

## <a name="create-a-nodejs-application"></a>建立 Node.js 應用程式

本教學課程的第一個步驟是建立新的空白 Node.js 應用程式。 如需有關建立 Node.js 應用程式的指示，請參閱[建立 Node.js 應用程式並將其部署到 Azure 網站][nodejswebsite]、使用 Windows PowerShell 的 [Node.js 雲端服務][Node.js Cloud Service]，或[使用 WebMatrix 的網站][webmatrix]。

## <a name="configure-your-application-to-use-notification-hubs"></a>將應用程式設為使用通知中樞

若要使用 Azur通知中樞，您需要下載並使用 Node.js [azure 封裝](https://www.npmjs.com/package/azure)，這包含一組內建的協助程式庫，能與推播通知 REST 服務通訊。

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>使用 Node Package Manager (NPM) 取得套件

1. 使用命令列介面 (例如 **PowerShell** (Windows)、**終端機** (Mac) 或 **Bash** (Linux))，瀏覽至建立空白應用程式所在的資料夾。
2. 在命令視窗中執行 `npm install azure-sb`。
3. 您可以手動執行 `ls` 或 `dir` 命令來驗證已建立 `node_modules` 資料夾。
4. 在該資料夾內，找出 **azure** 套件，此套件包含您存取「通知中樞」所需的程式庫。

> [!NOTE]
> 您可以從官方 [NPM 部落格](https://blog.npmjs.org/post/85484771375/how-to-install-npm)進一步了解如何安裝 NPM。

### <a name="import-the-module"></a>匯入模組
使用文字編輯器，將下列內容新增至應用程式的 `server.js` 檔案頂端：

```javascript
var azure = require('azure-sb');
```

### <a name="set-up-an-azure-notification-hub-connection"></a>設定 Azure 通知中樞連線

`NotificationHubService` 物件可讓您使用通知中樞。 下列程式碼替名為 `hubname` 的通知中樞建立 `NotificationHubService` 物件。 請將程式碼新增至 `server.js` 檔案的頂端附近，放置在匯入 Azure 模型的陳述式後方：

```javascript
var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');
```

藉由執行下列步驟，從 [Azure 入口網站]取得連線 `connectionstring` 值：

1. 在左導覽窗格中，按一下 [瀏覽] ****。
2. 選取 [通知中樞] ****，然後尋找您要用於範例的中樞。 如果您需要建立新的通知中樞的協助，您可以參考 [Windows Store 消費者入門教學](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) 課程。
3. 選取 [設定]  。
4. 按一下 [存取原則] ****。 您會看到兩個共用和完整存取連接字串。

![Azure 入口網站 - 通知中樞](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [!NOTE]
> 您也可以使用 [Azure PowerShell](/powershell/azure/) 所提供的 **Get-AzureSbNamespace** Cmdlet，或搭配 [Azure 命令列介面 (Azure CLI)](/cli/azure/install-classic-cli) 使用 **azure sb namespace show** 命令，來擷取連接字串。

## <a name="general-architecture"></a>一般架構

`NotificationHubService` 物件會公開下列可將推播通知傳送至特定裝置和應用程式的物件執行個體：

- **Android** - 使用可在 `notificationHubService.gcm` 取得的 `GcmService`
- **iOS** - 使用可在 `notificationHubService.apns` 取得的 `ApnsService` 物件
- **Windows Phone** - 使用可在 `notificationHubService.mpns` 取得的 `MpnsService` 物件
- **通用 Windows 平台** - 使用可在 `notificationHubService.wns` 取得的 `WnsService` 物件

### <a name="how-to-send-push-notifications-to-android-applications"></a>做法：將推播通知傳送至 Android 應用程式

`GcmService` 物件會提供可用來將推播通知傳送至 Android 應用程式的 `send` 方法。 此 `send` 方法可接受下列參數：

- **Tags** - 標籤識別碼。 若未提供標籤，通知會傳送至所有用戶端。
- **Payload** - 訊息的 JSON 或原始字串承載。
- **Callback** - 回呼函數。

如需裝載格式的詳細資訊，請參閱裝載 [檔](https://distriqt.github.io/ANE-PushNotifications/m.FCM-GCM%20Payload)。

下列程式碼使用 `NotificationHubService` 所公開的 `GcmService` 執行個體，傳送推播通知至所有已註冊的用戶端。

```javascript
var payload = {
  data: {
    message: 'Hello!'
  }
};
notificationHubService.gcm.send(null, payload, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-ios-applications"></a>做法：將推播通知傳送至 iOS 應用程式

與上述的 Android 應用程式一樣，`ApnsService` 物件會提供可用來將推播通知傳送至 iOS 應用程式的 `send` 方法。 此 `send` 方法可接受下列參數：

- **Tags** - 標籤識別碼。 若未提供標籤，通知會傳送至所有用戶端。
- **Payload** - 訊息的 JSON 或字串承載。
- **Callback** - 回呼函數。

如需裝載格式的詳細資訊，請參閱[UserNotifications 指南](https://developer.apple.com/documentation/usernotifications)的**通知內容**一節。

下列程式碼使用 `NotificationHubService` 所公開的 `ApnsService` 執行個體，傳送警示訊息至所有用戶端：

```javascript
var payload={
    alert: 'Hello!'
  };
notificationHubService.apns.send(null, payload, function(error){
  if(!error){
      // notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-windows-phone-applications"></a>做法：將推播通知傳送至 Windows Phone 應用程式

`MpnsService` 物件提供可用來將推播通知傳送至 Windows Phone 應用程式的 `send` 方法。 此 `send` 方法可接受下列參數：

- **Tags** - 標籤識別碼。 若未提供標籤，通知會傳送至所有用戶端。
- **Payload** - 訊息的 XML 承載。
- **TargetName**  -  TargetName `toast`快顯通知。 `token` 代表磚通知。
- **NotificationClass** - 通知的優先順序。 如需有效值，請參閱[來自伺服器的推播通知](/previous-versions/windows/xna/bb200104(v=xnagamestudio.41))文件的＜HTTP 標頭元素＞**** 一節。
- **Options** - 選用的要求標頭。
- **Callback** - 回呼函數。

如需有效 `TargetName`、`NotificationClass` 和標頭選項的清單，請查看[來自伺服器的推播通知](/previous-versions/windows/xna/bb200104(v=xnagamestudio.41))頁面。

下列範例程式碼使用 `NotificationHubService` 所公開的 `MpnsService` 執行個體傳送快顯推播通知：

```javascript
var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>做法：將推播通知傳送至通用 Windows 平台 (UWP) 應用程式

`WnsService` 物件會提供可用來將推播通知傳送至「通用 Windows 平台」應用程式的 `send` 方法。  此 `send` 方法可接受下列參數：

- **Tags** - 標籤識別碼。 若未提供標籤，通知會傳送至所有已註冊的用戶端。
- **Payload** - XML 訊息承載。
- **Type** - 通知類型。
- **Options** - 選用的要求標頭。
- **Callback** - 回呼函數。

如需有效類型和要求標頭的清單，請參閱 [推播通知服務要求和回應標頭](/previous-versions/windows/apps/hh465435(v=win.10))。

下列程式碼使用 `NotificationHubService` 所公開的 `WnsService` 執行個體傳送快顯推播通知至 UWP 應用程式：

```javascript
var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
  if(!error){
      // notification sent
  }
});
```

## <a name="next-steps"></a>後續步驟

上述的範例程式碼片段可讓您輕鬆地建置服務基礎結構，將推播通知傳遞到各種裝置。 了解基本的透過 node.js 的通知中樞使用方式之後，請參考下列連結以了解如何進一步延伸這些功能。

- 請參閱 [Azure 通知中樞](/previous-versions/azure/azure-services/jj927170(v=azure.100))的 MSDN 參考。
- 造訪 GitHub 上的 [Azure SDK for Node] 儲存機制，以取得更多範例和實作詳細資料。

[Azure SDK for Node]: https://github.com/WindowsAzure/azure-sdk-for-node
[Next Steps]: #nextsteps
[What are Service Bus Topics and Subscriptions?]: #what-are-service-bus-topics
[Create a Service Namespace]: #create-a-service-namespace
[Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
[Create a Node.js Application]: #Create_a_Nodejs_Application
[Configure Your Application to Use Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
[How to: Create a Topic]: #How_to_Create_a_Topic
[How to: Create Subscriptions]: #How_to_Create_Subscriptions
[How to: Send Messages to a Topic]: #How_to_Send_Messages_to_a_Topic
[How to: Receive Messages from a Subscription]: #How_to_Receive_Messages_from_a_Subscription
[How to: Handle Application Crashes and Unreadable Messages]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
[How to: Delete Topics and Subscriptions]: #How_to_Delete_Topics_and_Subscriptions
[1]: #Next_Steps
[Topic Concepts]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
[image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
[2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
[3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
[4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
[5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter?view=azure-dotnet#microsoft_servicebus_messaging_sqlfilter_sqlexpression
[Azure Service Bus Notification Hubs]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[SqlFilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter?view=azure-dotnet#microsoft_servicebus_messaging_sqlfilter
[Web Site with WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
[nodejswebsite]: ../app-service/quickstart-nodejs.md
[webmatrix]: /aspnet/web-pages/videos/introduction/create-a-website-using-webmatrix
[Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
[Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
[Azure 入口網站]: https://portal.azure.com
