---
title: Python ADAL 至 MSAL 遷移指南 |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何將 Azure Active Directory Authentication Library (ADAL) Python 應用程式遷移至適用于 Python 的 Microsoft 驗證程式庫 (MSAL) 。
services: active-directory
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Python
ms.workload: identity
ms.date: 11/11/2019
ms.author: rayluo
ms.reviewer: marsma, rayluo, nacanuma
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 42ffc7ffba20868b23675fd8613fd3ef11b0924a
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98755049"
---
# <a name="adal-to-msal-migration-guide-for-python"></a>適用于 Python 的 ADAL 至 MSAL 遷移指南

本文強調您需要進行的變更，以將使用 Azure Active Directory Authentication Library (ADAL) 的應用程式遷移至使用 Microsoft 驗證程式庫 (MSAL) 。

## <a name="difference-highlights"></a>差異重點

ADAL 適用于 Azure Active Directory (Azure AD) v1.0 端點。 Microsoft 驗證程式庫 (MSAL) 適用于 Microsoft 身分識別平臺（先前稱為 Azure Active Directory v2.0 端點）。 Microsoft 身分識別平臺與 Azure AD v1.0 的不同之處在于：

支援：
  - 公司和學校帳戶 (Azure AD 佈建的帳戶)
  - 個人帳戶 (例如 Outlook.com 或 Hotmail.com)
  - 透過 Azure AD B2C 供應項目自攜其電子郵件或社交身分識別 (例如 LinkedIn、Facebook、Google) 的客戶

- 符合下列標準：
  - OAuth 2.0 版
  - OpenID Connect (OIDC) 

查看 [Microsoft 身分識別平臺有何不同？](../azuread-dev/azure-ad-endpoint-comparison.md) 如需詳細資訊，請參閱。

### <a name="scopes-not-resources"></a>範圍，而非資源

ADAL Python 會取得資源的權杖，但 MSAL Python 會取得範圍的權杖。 MSAL Python 中的 API 介面不再有資源參數。 您必須提供範圍做為字串清單，以宣告所需的許可權和要求的資源。 若要查看範圍的一些範例，請參閱 [Microsoft Graph 的範圍](/graph/permissions-reference)。

您可以將 `/.default` 範圍尾碼新增至資源，以協助將您的應用程式從 v1.0 端點（ (ADAL) ）遷移至 Microsoft 身分識別平臺 (MSAL) 。 例如，針對的資源值 `https://graph.microsoft.com` ，相等的範圍值為 `https://graph.microsoft.com/.default` 。  如果資源不是在 URL 格式中，而是表單的資源識別碼 `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX` ，您仍然可以使用範圍值 `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX/.default` 。

如需不同類型的範圍的詳細資訊，請參閱 [Microsoft 身分識別平臺中的許可權和同意](./v2-permissions-and-consent.md) ，以及 [接受 V1.0 權杖的 Web API 範圍](./msal-v1-app-scopes.md) 文章。

### <a name="error-handling"></a>錯誤處理

適用于 Python 的 Azure Active Directory Authentication Library (ADAL) 會使用例外狀況 `AdalError` 來指出發生問題。 MSAL for Python 通常會使用錯誤碼，而不是。 如需詳細資訊，請參閱 [MSAL For Python 錯誤處理](msal-error-handling-python.md)。

### <a name="api-changes"></a>API 變更

下表列出 ADAL 中適用于 Python 的 API，以及在 MSAL for Python 中要使用的 API：

| 適用于 Python API 的 ADAL  | 適用于 Python API 的 MSAL |
| ------------------- | ---------------------------------- |
| [AuthenticationCoNtext](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext)  | [PublicClientApplication 或 >confidentialclientapplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)  |
| N/A  | [get_authorization_request_url ( # B1 ](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.get_authorization_request_url)  |
| [acquire_token_with_authorization_code ( # B1 ](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_authorization_code) | [acquire_token_by_authorization_code ( # B1 ](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_by_authorization_code) |
| [acquire_token ( # B1 ](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token) | [acquire_token_silent ( # B1 ](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_silent) |
| [acquire_token_with_refresh_token ( # B1 ](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_refresh_token) | N/A |
| [acquire_user_code ( # B1 ](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_user_code) | [initiate_device_flow ( # B1 ](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.initiate_device_flow) |
| [acquire_token_with_device_code ( # B1 ](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_device_code) 和 [Cancel_request_to_get_token_with_device_code ( # B3 ](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.cancel_request_to_get_token_with_device_code) | [acquire_token_by_device_flow ( # B1 ](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow) |
| [acquire_token_with_username_password ( # B1 ](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_username_password) | [acquire_token_by_username_password ( # B1 ](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_username_password) |
| [acquire_token_with_client_credentials ( # B1 ](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_credentials) 和 [Acquire_token_with_client_certificate ( # B3 ](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_certificate) | [acquire_token_for_client ( # B1 ](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client) |
| N/A | [acquire_token_on_behalf_of ( # B1 ](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) |
| [TokenCache ( # B1 ](https://adal-python.readthedocs.io/en/latest/#adal.TokenCache) | [SerializableTokenCache ( # B1 ](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) |
| N/A | 具有持續性的快取，可從[MSAL 延伸](https://github.com/marstr/original-microsoft-authentication-extensions-for-python)模組取得 |

## <a name="migrate-existing-refresh-tokens-for-msal-python"></a>遷移 MSAL Python 的現有重新整理權杖

Microsoft 驗證程式庫 (MSAL) 摘要了重新整理權杖的概念。 MSAL Python 預設會提供記憶體內部權杖快取，因此您不需要儲存、查閱或更新重新整理權杖。 使用者也會看到較少的登入提示，因為重新整理權杖通常可以在沒有使用者介入的情況下更新。 如需權杖快取的詳細資訊，請參閱 [MSAL For Python 中的自訂權杖](msal-python-token-cache-serialization.md)快取序列化。

下列程式碼將協助您遷移其他 OAuth2 程式庫所管理的重新整理權杖 (包括但不限於 ADAL Python) ，以供 Python 管理。 遷移這些重新整理權杖的原因之一，是要防止現有的使用者在您將應用程式遷移至 MSAL for Python 時，不需要再次登入。

遷移重新整理權杖的方法是使用 MSAL for Python，以使用先前的重新整理權杖來取得新的存取權杖。 傳回新的重新整理權杖時，Python 的 MSAL 會將它儲存在快取中。
自 MSAL Python 1.3.0 起，我們會在 MSAL 中為此目的提供 API。
請參閱下列程式碼片段，[並以使用 MSAL Python 來遷移重新整理權杖的完整範例](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.3.0/sample/migrate_rt.py#L28-L67)括住。

```python
import msal
def get_preexisting_rt_and_their_scopes_from_elsewhere():
    # Maybe you have an ADAL-powered app like this
    #   https://github.com/AzureAD/azure-activedirectory-library-for-python/blob/1.2.3/sample/device_code_sample.py#L72
    # which uses a resource rather than a scope,
    # you need to convert your v1 resource into v2 scopes
    # See https://docs.microsoft.com/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources
    # You may be able to append "/.default" to your v1 resource to form a scope
    # See https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope

    # Or maybe you have an app already talking to the Microsoft identity platform,
    # powered by some 3rd-party auth library, and persist its tokens somehow.

    # Either way, you need to extract RTs from there, and return them like this.
    return [
        ("old_rt_1", ["scope1", "scope2"]),
        ("old_rt_2", ["scope3", "scope4"]),
        ]


# We will migrate all the old RTs into a new app powered by MSAL
app = msal.PublicClientApplication(
    "client_id", authority="...",
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# We choose a migration strategy of migrating all RTs in one loop
for old_rt, scopes in get_preexisting_rt_and_their_scopes_from_elsewhere():
    result = app.acquire_token_by_refresh_token(old_rt, scopes)
    if "error" in result:
        print("Discarding unsuccessful RT. Error: ", json.dumps(result, indent=2))

print("Migration completed")
```


## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱 [v1.0 和 v2.0 的比較](../azuread-dev/azure-ad-endpoint-comparison.md)。
