---
title: v1.0 應用 (MSAL) 的作用域 |蔚藍
description: 了解使用 Microsoft 驗證程式庫 (MSAL) 的 v1.0 應用程式範圍。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/25/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 61d07c1ba912a0e24b2f4e5fa67243b4525db367
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536177"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>接受 v1.0 權杖的 Web API 的功能

OAuth2 許可權是 Azure 活動目錄 (Azure AD) 面向開發人員 (v1.0) Web API (資源) 應用程式向用戶端應用程式公開的許可權範圍。 這些權限範圍可能會在同意過程中授與用戶端應用程式。 請參閱 [Azure Active Directory 應用程式資訊清單參考](reference-app-manifest.md#manifest-reference)中的 `oauth2Permissions` 相關章節。

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>要求存取 v1.0 應用程式特定 OAuth2 權限的範圍

要獲取 v1.0 應用程式的特定作用域的權杖(例如,Microsoft 圖形https://graph.microsoft.com)API,即,透過將所需的資源識別碼與該資源所需的 OAuth2 許可權串聯來建立作用域。

例如，代表使用者存取應用程式識別碼 URI 為 `ResourceId` 的 v1.0 Web API：

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

要使用 Microsoft 圖形 API(H.\//graph.microsoft.com/)使用 MSAL.NET Azure AD 進行讀取和寫入,您需要建立作用網域清單,如以下範例所示:

```csharp
string ResourceId = "https://graph.microsoft.com/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.microsoft.com/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

要編寫與 Azure 資源管理員 API(Hagement.core.windows.net/)\/對應的範圍,需要請求以下作用網域(請注意兩個斜杠):

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> 您必須使用兩個斜線，因為 RAzure Resource Manager API 預期其對象宣告 (aud) 中有一個斜線，還有一個斜線用來分隔 API 名稱與範圍。

Azure AD 所用的邏輯如下所示：

- 對於具有 v1.0 存取權杖(唯一可能)的 ADAL(Azure AD v1.0)終結點,aud_資源
- 對於 MSAL(Microsoft 標識平臺 (v2.0)) 終結點,為接受 v2.0 權杖的資源請求訪問權杖,`aud=resource.AppId`
- 對於 MSAL(v2.0 終結點),為接受 v1.0 訪問令牌的資源請求訪問令牌(如上所示),Azure AD 通過獲取最後一個斜杠之前的所有內容並將其用作資源標識符,從請求的作用域解析所需的訪問群體。 因此,如果 H/es:\//database.windows.net期望受眾\/"H.H/database.windows.net/",則需要請求"H:/database.windows.net//.default"\/的範圍。 另請參閱[GitHub 問題#747: 資源網址的尾隨斜杠被省略,這會導致 sql auth 失敗](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)。

## <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>要求存取 v1.0 應用程式所有權限的範圍

如果您想要取得 v1.0 應用程式所有靜態範圍的權杖，請將 ".default" 附加到 API 的應用程式識別碼 URI：

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

```javascript
var ResourceId = "someAppIDURI";
var scopes = [ ResourceId + "/.default"];
```

## <a name="scopes-to-request-for-a-client-credential-flowdaemon-app"></a>要求客戶端認證程式應用程式

在用戶端認證流程案例中，要傳遞的範圍也會是 `/.default`。 這會告知 Azure AD：系統管理員在應用程式註冊時同意的所有應用程式層級權限。
