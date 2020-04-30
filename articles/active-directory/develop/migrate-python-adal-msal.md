---
title: Python ADAL 至 MSAL 遷移指南 |Azure
description: 瞭解如何將您的 Azure Active Directory Authentication Library （ADAL） Python 應用程式遷移至適用于 Python 的 Microsoft 驗證程式庫（MSAL）。
services: active-directory
titleSuffix: Microsoft identity platform
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Python
ms.workload: identity
ms.date: 11/11/2019
ms.author: rayluo
ms.reviewer: rayluo, nacanuma, twhitney
ms.custom: aaddev
ms.openlocfilehash: a3f95383979fd47b3baaec946f724533461729b8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82128052"
---
# <a name="adal-to-msal-migration-guide-for-python"></a>適用于 Python 的 MSAL 遷移指南的 ADAL

本文重點說明您需要進行的變更，以遷移使用 Azure Active Directory Authentication Library （ADAL）的應用程式來使用 Microsoft Authentication Library （MSAL）。

## <a name="difference-highlights"></a>差異重點

ADAL 適用于 Azure Active Directory （Azure AD） v1.0 端點。 Microsoft 驗證程式庫（MSAL）適用于 Microsoft 身分識別平臺，先前稱為 Azure Active Directory v2.0 端點。 Microsoft 身分識別平臺與 Azure AD v1.0 的不同之處在于：

支援：
  - 公司和學校帳戶 (Azure AD 佈建的帳戶)
  - 個人帳戶 (例如 Outlook.com 或 Hotmail.com)
  - 透過 Azure AD B2C 供應項目自攜其電子郵件或社交身分識別 (例如 LinkedIn、Facebook、Google) 的客戶

- 標準與相容：
  - OAuth v2。0
  - OpenID Connect （OIDC）

如需詳細資訊，請參閱[Microsoft 身分識別平臺（v2.0）端點的差異](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison)。

### <a name="scopes-not-resources"></a>範圍，而非資源

ADAL Python 會取得資源的權杖，但 MSAL Python 會取得範圍的權杖。 MSAL Python 中的 API 介面已不再有資源參數。 您需要提供範圍做為字串清單，以宣告所需的許可權和所要求的資源。 若要查看範圍的一些範例，請參閱[Microsoft Graph 的範圍](https://docs.microsoft.com/graph/permissions-reference)。

您可以將`/.default`範圍尾碼新增至資源，以協助將您的應用程式從 v1.0 端點（ADAL）遷移至 Microsoft 身分識別平臺端點（MSAL）。 例如，針對的資源值`https://graph.microsoft.com`，對等的範圍值為。 `https://graph.microsoft.com/.default`  如果資源不是在 URL 表單中，而是表單`XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX`的資源識別碼，您仍然可以使用範圍值做為`XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX/.default`。

如需不同類型範圍的詳細資訊，請參閱[Microsoft 身分識別平臺中的許可權和同意](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)和[接受 V1.0 權杖的 Web API 範圍一](https://docs.microsoft.com/azure/active-directory/develop/msal-v1-app-scopes)文。

### <a name="error-handling"></a>錯誤處理

適用于 Python 的 Azure Active Directory Authentication Library （ADAL）會`AdalError`使用例外狀況來表示發生問題。 MSAL for Python 通常會改為使用錯誤碼。 如需詳細資訊，請參閱[MSAL For Python error 處理](https://docs.microsoft.com/azure/active-directory/develop/msal-handling-exceptions?tabs=python)。

### <a name="api-changes"></a>API 變更

下表列出適用于 Python 的 ADAL 中的 API，以及要在 MSAL for Python 中使用的 API：

| 適用于 Python API 的 ADAL  | 適用于 Python API 的 MSAL |
| ------------------- | ---------------------------------- |
| [AuthenticationCoNtext](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext)  | [PublicClientApplication 或 ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)  |
| N/A  | [get_authorization_request_url （）](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.get_authorization_request_url)  |
| [acquire_token_with_authorization_code （）](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_authorization_code) | [acquire_token_by_authorization_code （）](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_by_authorization_code) |
| [acquire_token （）](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token) | [acquire_token_silent （）](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_silent) |
| [acquire_token_with_refresh_token （）](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_refresh_token) | N/A |
| [acquire_user_code （）](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_user_code) | [initiate_device_flow （）](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.initiate_device_flow) |
| [acquire_token_with_device_code （）](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_device_code)和[cancel_request_to_get_token_with_device_code （）](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.cancel_request_to_get_token_with_device_code) | [acquire_token_by_device_flow （）](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow) |
| [acquire_token_with_username_password （）](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_username_password) | [acquire_token_by_username_password （）](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_username_password) |
| [acquire_token_with_client_credentials （）](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_credentials)和[acquire_token_with_client_certificate （）](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_certificate) | [acquire_token_for_client （）](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client) |
| N/A | [acquire_token_on_behalf_of （）](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) |
| [TokenCache （）](https://adal-python.readthedocs.io/en/latest/#adal.TokenCache) | [SerializableTokenCache()](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) |
| N/A | 具有持續性的快取，可從[MSAL 擴充](https://github.com/marstr/original-microsoft-authentication-extensions-for-python)功能取得 |

## <a name="migrate-existing-refresh-tokens-for-msal-python"></a>遷移 MSAL Python 的現有重新整理權杖

Microsoft 驗證程式庫（MSAL）會抽象化重新整理權杖的概念。 MSAL Python 預設會提供記憶體中的權杖快取，因此您不需要儲存、查閱或更新重新整理權杖。 使用者也會看到較少的登入提示，因為重新整理權杖通常可以在不需要使用者介入的情況下更新。 如需權杖快取的詳細資訊，請參閱[MSAL For Python 中的自訂權杖](msal-python-token-cache-serialization.md)快取序列化。

下列程式碼將協助您將由另一個 OAuth2 程式庫（包括但不限於 ADAL Python）所管理的重新整理權杖，遷移至 MSAL for Python 管理。 遷移這些重新整理權杖的其中一個原因是，當您將應用程式遷移至適用于 Python 的 MSAL 時，讓現有使用者不需要再次登入。

遷移重新整理權杖的方法是使用適用于 Python 的 MSAL，以使用先前的重新整理權杖來取得新的存取權杖。 傳回新的重新整理權杖時，適用于 Python 的 MSAL 會將它儲存在快取中。 以下是如何執行此動作的範例：

```python
from msal import PublicClientApplication

def get_preexisting_rt_and_their_scopes_from_elsewhere(...):
    raise NotImplementedError("You will need to implement this by yourself")

app = PublicClientApplication(..., token_cache=...)

for old_rt, old_scope in get_preexisting_rt_and_their_scopes_from_elsewhere(...):
    # Assuming the old scope could be a space-delimited string.
    # MSAL expects a list, like ["scope1", "scope2"].
    scopes = old_scope.split()
        # If your old refresh token came from ADAL for Python, which uses a resource rather than a scope,
        # you need to convert your v1 resource into v2 scopes
        # See https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison#scopes-not-resources
        # You may be able to append "/.default" to your v1 resource to form a scope
        # See https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope

    result = app.client.obtain_token_by_refresh_token(old_rt, scope=scopes)
    # When this call returns the new token(s), a new refresh token is issued by the Microsoft identity platform and MSAL for Python
    # stores it in the token cache.
```

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱 [v1.0 和 v2.0 的比較](active-directory-v2-compare.md)。
