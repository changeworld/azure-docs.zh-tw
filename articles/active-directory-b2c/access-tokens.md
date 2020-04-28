---
title: 要求存取權杖-Azure Active Directory B2C |Microsoft Docs
description: 瞭解如何從 Azure Active Directory B2C 要求存取權杖。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79259769"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中要求存取權杖

*存取權杖*包含您可以在 Azure Active Directory B2C （Azure AD B2C）中使用的宣告，以識別授與 api 的許可權。 呼叫資源伺服器時，HTTP 要求中必須有存取權杖。 存取權杖在 Azure AD B2C 的回應中表示為**access_token** 。

本文說明如何要求 web 應用程式的存取權杖，並 Web API。 如需 Azure AD B2C 中之權杖的詳細資訊，請參閱[Azure Active Directory B2C 中的權杖總覽](tokens-overview.md)。

> [!NOTE]
> **Azure AD B2C 不支援 Web API 鏈結 (代理者)。** -許多架構都包含需要呼叫另一個下游 Web API 的 Web API，兩者都是由 Azure AD B2C 所保護。 在具有 Web API 後端的用戶端中，這種情況很常見，後者會接著呼叫另一個服務。 使用 OAuth 2.0 JWT 持有人認證授與可支援此鏈結的 Web API 案例，亦稱為「代理者流程」。 不過，Azure AD B2C 目前未實作代理者流程。

## <a name="prerequisites"></a>先決條件

- [建立使用者流程](tutorial-create-user-flows.md)，讓使用者註冊並登入您的應用程式。
- 如果您尚未這麼做，請[將 Web API 應用程式新增至您的 Azure Active Directory B2C 租使用者](add-web-application.md)。

## <a name="scopes"></a>範圍

範圍提供一種方式來管理受保護資源的許可權。 要求存取權杖時，用戶端應用程式必須在要求的**範圍**參數中指定所需的許可權。 例如， `read`若要針對具有**應用程式識別碼 URI**的`https://contoso.onmicrosoft.com/api`API 指定的**範圍值**，則範圍會是。 `https://contoso.onmicrosoft.com/api/read`

Web API 可使用範圍來實作以範圍為基礎的存取控制。 例如，Web API 的使用者可以同時具有讀取和寫入權限，Web API 的使用者也可能只具有讀取權限。 若要在相同的要求中取得多個許可權，您可以在要求的單一**範圍**參數中新增多個專案，並以空格分隔。

下列範例顯示在 URL 中解碼的範圍：

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

下列範例會顯示在 URL 中編碼的範圍：

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

如果您要求的範圍比針對用戶端應用程式授與的數目更多，則如果至少授與一個許可權，則呼叫會成功。 產生的存取權杖中的**scp**宣告只會填入成功授與的許可權。 OpenID Connect 標準會指定數個特殊範圍的值。 下列範圍代表存取使用者設定檔的許可權：

- **openid** -要求識別碼權杖。
- **offline_access** -使用[驗證碼流程](authorization-code-flow.md)要求重新整理權杖。

如果`/authorize`要求中的`token` **response_type**參數包含，**範圍**參數必須包含至少一個`openid`和將授與`offline_access`的資源範圍。 否則， `/authorize`要求就會失敗。

## <a name="request-a-token"></a>要求權杖

若要要求存取權杖，您需要授權碼。 以下是授權碼的`/authorize`端點要求範例。 不支援使用自訂網域搭配存取權杖。 在要求 URL 中使用您的 tenant-name.onmicrosoft.com 網域。

在下列範例中，您可以取代下列值：

- `<tenant-name>` - Azure AD B2C 租用戶的名稱。
- `<policy-name>` - 您的自訂原則或使用者流程名稱。
- `<application-ID>`-您註冊以支援使用者流程之 web 應用程式的應用程式識別碼。
- `<redirect-uri>` - 您註冊用戶端應用程式時所輸入的 [重新導向 URI]****。

```HTTP
GET https://<tenant-name>.b2clogin.com/tfp/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&response_type=code
```

具有授權碼的回應應該與下列範例類似：

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

成功接收授權碼之後，您就可以使用它來要求存取權杖：

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

使用https://jwt.ms檢查傳回的存取權杖時，您應該會看到類似下列範例的內容：

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

- 瞭解如何[在 Azure AD B2C 中設定權杖](configure-tokens.md)
