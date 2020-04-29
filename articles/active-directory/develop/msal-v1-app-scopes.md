---
title: V1.0 應用程式的範圍（MSAL） |Azure
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81536177"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>接受 v1.0 權杖之 Web API 的範圍

OAuth2 許可權是適用于開發人員（v1.0） Web API （資源）應用程式公開給用戶端應用程式的 Azure Active Directory （Azure AD）許可權範圍。 這些權限範圍可能會在同意過程中授與用戶端應用程式。 請參閱 [Azure Active Directory 應用程式資訊清單參考](reference-app-manifest.md#manifest-reference)中的 `oauth2Permissions` 相關章節。

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>要求存取 v1.0 應用程式特定 OAuth2 權限的範圍

若要取得 v1.0 應用程式特定範圍的權杖（例如 Microsoft Graph API，也就是https://graph.microsoft.com)，請將所需的資源識別碼與該資源的所需 OAuth2 許可權串連，以建立範圍。

例如，代表使用者存取應用程式識別碼 URI 為 `ResourceId` 的 v1.0 Web API：

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

若要使用 Microsoft Graph API （HTTPs：\//graph.microsoft.com/）來讀取和寫入 MSAL.NET Azure AD，您必須建立範圍清單，如下列範例所示：

```csharp
string ResourceId = "https://graph.microsoft.com/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.microsoft.com/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

若要寫入與 Azure Resource Manager API （HTTPs：\//management.core.windows.net/）對應的範圍，您必須要求下列範圍（請注意兩個斜線）：

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> 您必須使用兩個斜線，因為 RAzure Resource Manager API 預期其對象宣告 (aud) 中有一個斜線，還有一個斜線用來分隔 API 名稱與範圍。

Azure AD 所用的邏輯如下所示：

- 針對具有 v1.0 存取權杖的 ADAL （Azure AD v1.0）端點（唯一可行的），aud = resource
- 若為 MSAL （Microsoft 身分識別平臺（v2.0））端點，則會針對接受 v2.0 權杖的資源要求存取權杖，`aud=resource.AppId`
- 針對 MSAL （v2.0 端點），針對接受 v1.0 存取權杖的資源（上述案例）要求存取權杖，Azure AD 從最後一個斜線前面取得所有專案，並使用它做為資源識別碼，以從要求的範圍中剖析所需的物件。 因此，如果 HTTPs：\//database.windows.net 預期物件為 "HTTPs：\//database.windows.net/"，您就必須要求 "HTTPs：\//database.windows.net//.default" 的範圍。 另請參閱 GitHub 問題[#747：省略資源 url 的尾端斜線，這會導致 sql 驗證失敗](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)。

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

## <a name="scopes-to-request-for-a-client-credential-flowdaemon-app"></a>要求用戶端認證流程/後臺應用程式的範圍

在用戶端認證流程案例中，要傳遞的範圍也會是 `/.default`。 這會告知 Azure AD：系統管理員在應用程式註冊時同意的所有應用程式層級權限。
