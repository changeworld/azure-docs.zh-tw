---
title: v1.0 應用 （MSAL） 的作用域 |蔚藍
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
ms.openlocfilehash: d5b2ef57af112169fb39e0da7a60b095698ff504
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299825"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>接受 v1.0 權杖的 Web API 範圍

OAuth2 許可權是 Azure 活動目錄 （Azure AD） 面向開發人員 （v1.0） Web API （資源） 應用程式向用戶端應用程式公開的許可權範圍。 這些權限範圍可能會在同意過程中授與用戶端應用程式。 請參閱 [Azure Active Directory 應用程式資訊清單參考](reference-app-manifest.md#manifest-reference)中的 `oauth2Permissions` 相關章節。

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>要求存取 v1.0 應用程式特定 OAuth2 權限的範圍

要獲取 v1.0 應用程式的特定作用域的權杖（例如，Microsoft 圖形 API，即https://graph.microsoft.com)，通過將所需的資源識別碼與該資源所需的 OAuth2 許可權串聯來創建作用域。

例如，代表使用者存取應用程式識別碼 URI 為 `ResourceId` 的 v1.0 Web API：

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

要使用 Microsoft 圖形 API（HTTPs：\//graph.microsoft.com/）使用 MSAL.NET Azure AD 進行讀取和寫入，您需要創建作用域清單，如以下示例所示：

```csharp
string ResourceId = "https://graph.microsoft.com/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.microsoft.com/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

要編寫與 Azure 資源管理器 API（HTTPs：\//management.core.windows.net/）對應的範圍，需要請求以下作用域（請注意兩個斜杠）：

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> 您必須使用兩個斜線，因為 RAzure Resource Manager API 預期其對象宣告 (aud) 中有一個斜線，還有一個斜線用來分隔 API 名稱與範圍。

Azure AD 所用的邏輯如下所示：

- 對於具有 v1.0 訪問權杖（唯一可能）的 ADAL（Azure AD v1.0）終結點，aud_資源
- 對於 MSAL（Microsoft 標識平臺 （v2.0）） 終結點，為接受 v2.0 權杖的資源請求訪問權杖，`aud=resource.AppId`
- 對於 MSAL（v2.0 終結點），為接受 v1.0 訪問權杖的資源請求訪問權杖（如上所示），Azure AD 通過獲取最後一個斜杠之前的所有內容並將其用作資源識別碼，從請求的作用域解析所需的訪問群體。 因此，如果 HTTPs：\//database.windows.net 期望受眾"HTTPs：\//database.windows.net/"，則需要請求"HTTPs：/database.windows.net//.default"\/的範圍。 另請參閱[GitHub 問題#747： 資源 URL 的尾隨斜杠被省略，這會導致 sql auth 失敗](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)。

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

## <a name="scopes-to-request-for-a-client-credential-flowdaemon-app"></a>請求用戶端憑據流/守護進程應用的範圍

在用戶端認證流程案例中，要傳遞的範圍也會是 `/.default`。 這會告知 Azure AD：系統管理員在應用程式註冊時同意的所有應用程式層級權限。
