---
title: 1.0 版應用程式的範圍 (MSAL) |蔚藍
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "81536177"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>接受1.0 版權杖的 web API 範圍

OAuth2 許可權是許可權範圍，Azure Active Directory (Azure AD) 適用于開發人員 (v1.0) web API (應用程式公開給用戶端應用程式。 這些權限範圍可能會在同意過程中授與用戶端應用程式。 請參閱 [Azure Active Directory 應用程式資訊清單參考](reference-app-manifest.md#manifest-reference)中的 `oauth2Permissions` 相關章節。

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>要求存取 v1.0 應用程式特定 OAuth2 權限的範圍

若要取得 v1.0 應用程式特定範圍的權杖 (例如 Microsoft Graph API，也就是將 https://graph.microsoft.com) 所需的資源識別碼串連為該資源所需的 OAuth2 許可權來建立範圍。

例如，代表使用者存取應用程式識別碼 URI 為 `ResourceId` 的 v1.0 Web API：

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

若要使用 Microsoft Graph API (HTTPs：/graph.microsoft.com/) 讀取和寫入 MSAL.NET Azure AD \/ ，您需要建立範圍清單，如下列範例所示：

```csharp
string ResourceId = "https://graph.microsoft.com/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.microsoft.com/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

若要撰寫對應至 Azure Resource Manager API (HTTPs： \/ /management.core.windows.net/) 的範圍，您必須要求下列範圍 (注意兩個斜線) ：

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> 您必須使用兩個斜線，因為 RAzure Resource Manager API 預期其對象宣告 (aud) 中有一個斜線，還有一個斜線用來分隔 API 名稱與範圍。

Azure AD 所用的邏輯如下所示：

- 針對 ADAL (Azure AD v1.0) 端點搭配 v1.0 存取權杖 (唯一可能的) ，aud = 資源
- 針對 MSAL (Microsoft 身分識別平臺 (v2.0) # A3 端點，要求接受 v2.0 權杖之資源的存取權杖， `aud=resource.AppId`
- 針對 MSAL (v2.0 端點) 詢問可接受 v1.0 存取權杖之資源的存取權杖 (這是上述) 的情況，Azure AD 會取得最後一個斜線之前的所有內容，並將其作為資源識別碼，藉此從要求的範圍剖析所需的物件。 因此，如果 HTTPs： \/ /database.windows.net 預期有 "HTTPs： \/ /database.windows.net/" 的物件，您必須要求 "HTTPs： \/ /database.windows.net//.default" 的範圍。 另請參閱 GitHub 問題 [#747：省略資源 url 的尾端斜線，這會導致 sql 驗證失敗](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)。

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

## <a name="scopes-to-request-for-a-client-credential-flowdaemon-app"></a>用戶端認證流程/daemon 應用程式要求的範圍

在用戶端認證流程案例中，要傳遞的範圍也會是 `/.default`。 這會告知 Azure AD：系統管理員在應用程式註冊時同意的所有應用程式層級權限。
