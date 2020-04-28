---
title: Azure 通知中樞中 APNS 的權杖型（HTTP/2）驗證 |Microsoft Docs
description: 瞭解如何使用適用于 APNS 的新權杖驗證。
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 11/14/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/13/2019
ms.openlocfilehash: 448b5c38371024c2eae900f4f87b343ee0a3b36a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76263807"
---
# <a name="token-based-http2-authentication-for-apns"></a>APNS 的權杖型（HTTP/2）驗證

## <a name="overview"></a>概觀

本文說明如何使用新的 APNS HTTP/2 通訊協定搭配以權杖為基礎的驗證。

使用新通訊協定的主要優點包括：

* 權杖產生相當簡單（相較于憑證）
* 不再需要顧慮到期日 – 您可以控制驗證權杖及其撤銷
* 目前的承載上限為 4 KB
* 同步的意見反應
* 您使用的是 Apple 的最新通訊協定–憑證仍然會使用已標示為即將淘汰的二進位通訊協定

使用這項新機制可以透過兩個步驟來執行：

* 從 Apple 開發人員帳戶入口網站取得必要的資訊。
* 使用新資訊設定您的通知中樞。

通知中樞現在已設定為搭配 APNS 使用新的驗證系統。

請注意，如果您使用 APNS 的憑證認證進行遷移，權杖屬性會覆寫系統中的憑證，但您的應用程式會繼續順暢地接收通知。

## <a name="obtaining-authentication-information-from-apple"></a>從 Apple 取得驗證資訊

若要啟用權杖型驗證，您需要 Apple 開發人員帳戶的下列屬性：

### <a name="key-identifier"></a>金鑰識別碼

您可以在 Apple 開發人員帳戶中，從 [**憑證]、[識別碼 & 設定檔**] 底下的 [**金鑰**] 頁面取得金鑰識別碼：

![](./media/notification-hubs-push-notification-http2-token-authentification/keys.png)

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier-and-application-name"></a>應用程式識別碼和應用程式名稱

應用程式名稱和識別碼也可在開發人員帳戶的 [**憑證]、[識別碼 & 設定檔**] 頁面中取得：

![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

### <a name="configure-via-the-net-sdk-or-the-azure-portal"></a>透過 .NET SDK 或 Azure 入口網站設定

您可以使用[最新的用戶端 SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs)或 Azure 入口網站中的，將您的中樞設定為使用權杖型驗證。 若要在入口網站中啟用權杖型驗證，請登入 Azure 入口網站並移至您的通知中樞**設定 > Apple （APNS）** ] 面板。 從 [**驗證模式]** 屬性選取 [**權杖**]，以使用所有相關的權杖屬性來更新您的中樞。

![設定權杖](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* 輸入您從 Apple 開發人員帳戶取得的屬性。
* 選擇應用程式模式（「**生產**」或「**沙箱**」）。
* 按一下 [**儲存**] 按鈕以更新您的 APNS 認證。

以權杖為基礎的認證是由下欄欄位組成：

* **金鑰**識別碼：在 Apple 開發人員入口網站中產生之私密金鑰的識別碼;例如， `2USFGKSKLT`。
* **小組識別碼**：也稱為「前置詞」或「應用程式前置詞」。 這是 Apple 開發人員入口網站中組織的識別碼;例如， `S4V3D7CHJR`。
* 套件組合**識別碼**：也稱為「應用程式識別碼」。 這是應用程式的套件組合識別碼;例如， `com.microsoft.nhubsample2019`。 請注意，您可以針對許多應用程式使用一個金鑰。 此值會在傳送`apns-topic`通知時對應至 HTTP 標頭，並用來將特定應用程式設為目標。
* **Token**：也稱為「金鑰」或「私密金鑰」。 這是從 Apple 開發人員入口網站上產生的 p8 檔案取得。 金鑰必須啟用 APNS （產生金鑰時，在 Apple 開發人員入口網站上選取此項）。 當您將此值提供給 NH 入口網站/API 時，該值必須從中移除 PEM 標頭/頁尾。
* **端點**：這是 [通知中樞入口網站] 分頁中的切換，以及 API 中的字串欄位。 有效值為 `https://api.push.apple.com` 或 `https://api.sandbox.push.apple.com`。 通知中樞在生產或沙箱環境中使用此值來傳送通知。 這必須符合應用`aps-environment`程式中的權利，否則產生的 APNS 裝置權杖不符合環境，而且通知無法傳送。

以下是說明正確使用方式的程式碼範例：

```csharp
NamespaceManager nm = NamespaceManager.CreateFromConnectionString(_endpoint);
string token = "YOUR PRIVATE KEY HERE";
string keyId = "YOUR KEY ID HERE";
string appName = "YOUR APP NAME HERE";
string appId = "YOUR APP ID HERE";
NotificationHubDescription desc = new NotificationHubDescription("PATH TO YOUR HUB");
desc.ApnsCredential = new ApnsCredential(token, keyId, appId, appName);
desc.ApnsCredential.Endpoint = @"https://api.development.push.apple.com:443/3/device";
nm.UpdateNotificationHubAsync(desc);
```

## <a name="next-steps"></a>後續步驟

* [在 Azure 入口網站中建立 Azure 通知中樞](create-notification-hub-portal.md)
* [在 Azure 入口網站中設定通知中樞](create-notification-hub-portal.md)
