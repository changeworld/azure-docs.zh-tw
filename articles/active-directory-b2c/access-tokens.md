---
title: 要求存取權杖 - Azure Active Directory B2C | Microsoft Docs
description: 了解如何要求 Azure Active Directory B2C 中的存取權杖。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 36027583d64ac91432888d866440932c6e1bdd07
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83635438"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>要求 Azure Active Directory B2C 中的存取權杖

*存取權杖*中包含宣告，可讓您在 Azure Active Directory B2C (Azure AD B2C) 中用來識別為 API 授與的權限。 在呼叫資源伺服器時，HTTP 要求中必須要有存取權杖。 存取權杖在 Azure AD B2C 的回應中會以 **access_token** 表示。

本文說明如何要求 Web 應用程式和 Web API 的存取權杖。 若要進一步了解 Azure AD B2C 中的權杖，請參閱 [Azure Active Directory B2C 中的權杖概觀](tokens-overview.md)。

> [!NOTE]
> **Azure AD B2C 不支援 Web API 鏈結 (代理者)。** - 許多架構中都有一個 Web API 需要呼叫另一個下游 Web API，而兩者都受 Azure AD B2C 保護。 用戶端若具有 Web API 後端 (會接著呼叫另一個服務)，就常會有這種情況。 使用 OAuth 2.0 JWT 持有人認證授與可支援此鏈結的 Web API 案例，亦稱為「代理者流程」。 不過，Azure AD B2C 目前未實作代理者流程。

## <a name="prerequisites"></a>Prerequisites

- [建立使用者流程](tutorial-create-user-flows.md)，讓使用者註冊並登入您的應用程式。
- [將 Web API 應用程式新增至您的 Azure Active Directory B2C 租用戶](add-web-application.md) (如果您尚未這麼做)。

## <a name="scopes"></a>範圍

範圍可讓您管理受保護資源的權限。 在要求存取權杖時，用戶端應用程式必須在要求的**範圍**參數中指定所需的權限。 例如，若要將 [App ID URI] 為 `https://contoso.onmicrosoft.com/api` 之 API 的 [範圍值] 指定為 `read`，則範圍為 `https://contoso.onmicrosoft.com/api/read`。

Web API 可使用範圍來實作以範圍為基礎的存取控制。 例如，Web API 的使用者可以同時具有讀取和寫入權限，Web API 的使用者也可能只具有讀取權限。 若要在相同的要求中取得多個權限，您可以要求的在單一**範圍**參數中新增以空格分隔的多個項目。

下列範例顯示在 URL 中解碼的範圍：

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

下列範例顯示在 URL 中編碼的範圍：

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

如果您要求的範圍比針對用戶端應用程式授與的數目多，但至少已授與一個權限，則呼叫會成功。 在產生的存取權杖中，**scp** 宣告只會填入成功授與的權限。 OpenID Connect 標準會指定數個特殊的範圍值。 下列範圍代表存取使用者設定檔的權限︰

- **openid** - 要求識別碼權杖。
- **offline_access** - 使用[授權碼流程](authorization-code-flow.md)要求重新整理權杖。

如果 `/authorize` 要求中的 **response_type** 參數包含 `token`，則**範圍**參數必須包含至少一個會授與的資源範圍 (`openid` 與 `offline_access` 除外)。 否則，`/authorize` 要求將會失敗。

## <a name="request-a-token"></a>要求權杖

若要要求存取權杖，您必須要有授權碼。 以下範例說明對 `/authorize` 端點的授權碼要求。 不支援將自訂網域與存取權杖搭配使用。 在要求 URL 中，請使用您的 tenant-name.onmicrosoft.com 網域。

在下列範例中，您可以取代下列值：

- `<tenant-name>` - Azure AD B2C 租用戶的名稱。
- `<policy-name>` - 您的自訂原則或使用者流程名稱。
- `<application-ID>` - 您註冊以支援使用者流程之 Web 應用程式的應用程式識別碼。
- `<redirect-uri>` - 您註冊用戶端應用程式時所輸入的 [重新導向 URI]。

```HTTP
GET https://<tenant-name>.b2clogin.com/tfp/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&response_type=code
```

具有授權碼的回應會類似於下列範例：

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

成功接收授權碼後，您可以將其用來要求存取權杖：

```HTTP
POST <tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/token HTTP/1.1
Host: <tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&client_id=<application-ID>
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
&redirect_uri=https://jwt.ms
&client_secret=2hMG2-_:y12n10vwH...
```

您應該會看到類似下列回應的內容：

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

使用 https://jwt.ms 檢查傳回的存取權杖時，您應該會看到類似下列範例的內容：

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

- 了解如何[在 Azure AD B2C 中設定權杖](configure-tokens.md)
