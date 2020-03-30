---
title: 請求訪問權杖 - Azure 活動目錄 B2C |微軟文檔
description: 瞭解如何從 Azure 活動目錄 B2C 請求訪問權杖。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8358d3378ea892ebeef653bcb51243c9f1aa0b8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259769"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>在 Azure 活動目錄 B2C 中請求訪問權杖

*訪問權杖*包含可在 Azure 活動目錄 B2C（Azure AD B2C） 中標識對 API 授予的許可權的聲明。 調用資源伺服器時，HTTP 要求中必須存在訪問權杖。 訪問權杖在 Azure AD B2C 的回應中表示為**access_token。**

本文介紹如何請求 Web 應用程式和 Web API 的訪問權杖。 有關 Azure AD B2C 中的權杖的詳細資訊，請參閱[Azure 活動目錄 B2C 中的權杖概述](tokens-overview.md)。

> [!NOTE]
> **Azure AD B2C 不支援 Web API 鏈結 (代理者)。** - 許多體系結構包括需要調用另一個下游 Web API 的 Web API，這兩種 API 都由 Azure AD B2C 保護。 此方案在具有 Web API 後端的用戶端中很常見，而用戶端又調用另一個服務。 使用 OAuth 2.0 JWT 持有人認證授與可支援此鏈結的 Web API 案例，亦稱為「代理者流程」。 不過，Azure AD B2C 目前未實作代理者流程。

## <a name="prerequisites"></a>Prerequisites

- [建立使用者流程](tutorial-create-user-flows.md)，讓使用者註冊並登入您的應用程式。
- 如果尚未這樣做，請向 Azure[活動目錄 B2C 租戶添加 Web API 應用程式](add-web-application.md)。

## <a name="scopes"></a>範圍

作用域提供了一種管理受保護資源的許可權的方法。 請求訪問權杖時，用戶端應用程式需要在請求**的範圍**參數中指定所需的許可權。 例如，要指定 具有 應用`read`ID **URI**的`https://contoso.onmicrosoft.com/api`API`https://contoso.onmicrosoft.com/api/read`**的範圍值**，範圍將為 。

Web API 可使用範圍來實作以範圍為基礎的存取控制。 例如，Web API 的使用者可以同時具有讀取和寫入權限，Web API 的使用者也可能只具有讀取權限。 要在同一請求中獲取多個許可權，可以在請求的單個**作用域**參數中添加多個條目，這些條目由空格分隔。

下面的示例顯示了在 URL 中解碼的範圍：

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

下面的示例顯示了在 URL 中編碼的範圍：

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

如果請求的範圍多於為用戶端應用程式授予的範圍，則如果至少授予一個許可權，則調用將成功。 生成的訪問權杖中的**scp**聲明僅填充成功授予的許可權。 OpenID 連接標準指定多個特殊範圍值。 以下作用域表示訪問使用者設定檔的許可權：

- **openid** - 請求 ID 權杖。
- **offline_access** - 使用[身份驗證代碼流](authorization-code-flow.md)請求刷新權杖。

如果`/authorize`請求中的`token`**response_type**參數包括 ，**則範圍**參數必須至少包括一個資源作用域，而不是`openid`將`offline_access`授予的。 否則，`/authorize`請求將失敗。

## <a name="request-a-token"></a>要求權杖

要請求訪問權杖，您需要一個授權代碼。 下面是向`/authorize`終結點請求授權代碼的示例。 不支援自訂域用於訪問權杖。 在請求 URL 中使用tenant-name.onmicrosoft.com域。

在下列範例中，您可以取代下列值：

- `<tenant-name>` - Azure AD B2C 租用戶的名稱。
- `<policy-name>` - 您的自訂原則或使用者流程名稱。
- `<application-ID>`- 您註冊以支援使用者流的 Web 應用程式的應用程式識別碼。
- `<redirect-uri>` - 您註冊用戶端應用程式時所輸入的 [重新導向 URI]****。

```HTTP
GET https://<tenant-name>.b2clogin.com/tfp/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&response_type=code
```

具有授權代碼的回應應類似于此示例：

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

成功接收授權代碼後，可以使用它請求訪問權杖：

```HTTP
POST <tenant-name>.onmicrosoft.com/oauth2/v2.0/token?p=<policy-name> HTTP/1.1
Host: <tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&client_id=<application-ID>
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
&redirect_uri=https://jwt.ms
&client_secret=2hMG2-_:y12n10vwH...
```

您應該會看到類似于以下回應的內容：

```JSON
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrN...",
    "token_type": "Bearer",
    "not_before": 1549647431,
    "expires_in": 3600,
    "expires_on": 1549651031,
    "resource": "f2a76e08-93f2-4350-833c-965c02483b11",
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiJjNjRhNGY3ZC0zMDkxLTRjNzMtYTcyMi1hM2YwNjk0Z..."
}
```

使用檢查https://jwt.ms返回的訪問權杖時，應看到類似于以下示例的內容：

```JSON
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dl..."
}.{
  "iss": "https://contoso0926tenant.b2clogin.com/c64a4f7d-3091-4c73-a7.../v2.0/",
  "exp": 1549651031,
  "nbf": 1549647431,
  "aud": "f2a76e08-93f2-4350-833c-965...",
  "oid": "1558f87f-452b-4757-bcd1-883...",
  "sub": "1558f87f-452b-4757-bcd1-883...",
  "name": "David",
  "tfp": "B2C_1_signupsignin1",
  "nonce": "anyRandomValue",
  "scp": "read",
  "azp": "38307aee-303c-4fff-8087-d8d2...",
  "ver": "1.0",
  "iat": 1549647431
}.[Signature]
```

## <a name="next-steps"></a>後續步驟

- 瞭解如何[在 Azure AD B2C 中配置權杖](configure-tokens.md)
