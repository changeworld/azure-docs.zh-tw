---
title: Azure 通知中心中的 APNS 基於權杖 （HTTP/2） 身份驗證 |微軟文檔
description: 瞭解如何對 APNS 使用新的權杖身份驗證。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263807"
---
# <a name="token-based-http2-authentication-for-apns"></a>基於權杖 （HTTP/2） 的 APNS 身份驗證

## <a name="overview"></a>總覽

本文介紹如何使用新的 APNS HTTP/2 協定進行基於權杖的身份驗證。

使用新通訊協定的主要優點包括：

* 權杖生成相對簡單（與證書相比）
* 不再需要顧慮到期日 – 您可以控制驗證權杖及其撤銷
* 目前的承載上限為 4 KB
* 同步的意見反應
* 您是在蘋果的最新協定 - 證書仍然使用二進位協定，這是標記為棄用

使用此新機制可以分兩個步驟執行：

* 從 Apple 開發人員帳戶門戶獲取必要的資訊。
* 使用新資訊配置通知中心。

通知中心現在設置為將新的身份驗證系統與 APNS 一起使用。

請注意，如果您從使用 APNS 證書憑據遷移，權杖屬性將覆蓋我們的系統中的證書，但您的應用程式將繼續無縫接收通知。

## <a name="obtaining-authentication-information-from-apple"></a>從 Apple 取得驗證資訊

要啟用基於權杖的身份驗證，您需要 Apple 開發人員帳戶中的以下屬性：

### <a name="key-identifier"></a>金鑰識別碼

金鑰識別碼可以從 Apple 開發人員帳戶中的 **"證書、識別碼&設定檔**下的 **"金鑰"** 頁面獲取：

![](./media/notification-hubs-push-notification-http2-token-authentification/keys.png)

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier-and-application-name"></a>應用程式識別碼和應用程式名稱

應用程式名稱和識別碼在開發人員帳戶中的 **"證書、識別碼&設定檔**"頁中也可用：

![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

### <a name="configure-via-the-net-sdk-or-the-azure-portal"></a>通過 .NET SDK 或 Azure 門戶進行配置

您可以使用[我們最新的用戶端 SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs)或在 Azure 門戶中配置中心以使用基於權杖的身份驗證。 要在門戶中啟用基於權杖的身份驗證，請登錄到 Azure 門戶，然後轉到通知中心的 **"設置> Apple （APNS）** 面板。 從**身份驗證模式**屬性中選擇**權杖**，以便使用所有相關權杖屬性更新中心。

![配置權杖](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* 輸入從 Apple 開發人員帳戶檢索的屬性。
* 選擇應用程式模式（**生產**或**沙箱**）。
* 按一下"**保存**"按鈕以更新您的 APNS 憑據。

基於權杖的憑據由以下欄位組成：

* **金鑰識別碼**：在 Apple 開發人員門戶中生成的私密金鑰的識別碼;例如， `2USFGKSKLT`.
* **團隊 ID**： 也稱為"首碼"或"應用首碼"。 這是 Apple 開發人員門戶中的組織的識別碼;例如， `S4V3D7CHJR`.
* **捆綁 ID**： 也稱為"應用 ID"。 這是應用程式的捆綁識別碼;例如， `com.microsoft.nhubsample2019`. 請注意，您可以為多個應用使用一個金鑰。 此值在`apns-topic`發送通知時映射到 HTTP 標頭，並用於定位特定應用程式。
* **權杖**：也稱為"金鑰"或"私密金鑰"。 這是從 Apple 開發人員門戶上生成的 .p8 檔獲得的。 金鑰必須啟用 APNS（生成金鑰時在 Apple 開發人員門戶中選擇）。 當您向 NH 門戶/API 提供 PEM 標頭/頁腳時，該值必須將其從該值中剝離出來。
* **終結點**：這是通知中心門戶邊欄選項卡中的切換，也是 API 中的字串欄位。 有效值為 `https://api.push.apple.com` 或 `https://api.sandbox.push.apple.com`。 通知中心將此值用於生產環境或沙箱環境，用於發送通知。 這必須與應用中`aps-environment`的授權匹配，否則生成的 APNS 設備權杖與環境不匹配，並且通知無法發送。

下面是一個代碼示例，其中說明了正確的用法：

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
* [在 Azure 門戶中配置通知中心](create-notification-hub-portal.md)
