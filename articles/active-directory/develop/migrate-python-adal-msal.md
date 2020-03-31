---
title: Python ADAL 到 MSAL 遷移指南 |蔚藍
description: 瞭解如何將 Azure 活動目錄身份驗證庫 （ADAL） Python 應用遷移到 Python 的 Microsoft 身份驗證庫 （MSAL）。
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
ms.openlocfilehash: fe9dc6c04fe033fd518218d1b5ea971e573405fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696550"
---
# <a name="adal-to-msal-migration-guide-for-python"></a>Python 的 ADAL 到 MSAL 遷移指南

本文重點介紹遷移使用 Azure 活動目錄身份驗證庫 （ADAL） 使用 Microsoft 身份驗證庫 （MSAL） 的應用所需的更改。

## <a name="difference-highlights"></a>差異亮點

ADAL 與 Azure 活動目錄 （Azure AD） v1.0 終結點配合使用。 Microsoft 身份驗證庫 （MSAL） 與 Microsoft 標識平臺（以前稱為 Azure 活動目錄 v2.0 終結點）配合使用。 Microsoft 標識平臺不同于 Azure AD v1.0，因為它：

支援：
  - 公司和學校帳戶 (Azure AD 佈建的帳戶)
  - 個人帳戶 (例如 Outlook.com 或 Hotmail.com)
  - 透過 Azure AD B2C 供應項目自攜其電子郵件或社交身分識別 (例如 LinkedIn、Facebook、Google) 的客戶

- 標準是否與：
  - OAuth v2.0
  - 打開 ID 連接 （OIDC）

有關詳細資訊[，請參閱 Microsoft 標識平臺 （v2.0） 終結點有何不同](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison)。

### <a name="scopes-not-resources"></a>範圍，而非資源

ADAL Python 獲取資源的權杖，但 MSAL Python 獲取作用域的權杖。 MSAL Python 中的 API 表面不再具有資源參數。 您需要提供作用域作為聲明請求所需的許可權和資源的字串清單。 要查看作用域的一些示例，請參閱[Microsoft Graph 的作用域](https://docs.microsoft.com/graph/permissions-reference)。

### <a name="error-handling"></a>錯誤處理

Python 的 Azure 活動目錄身份驗證庫 （ADAL） 使用異常`AdalError`來指示存在問題。 Python 的 MSAL 通常使用錯誤代碼。 有關詳細資訊，請參閱[MSAL 進行 Python 錯誤處理](https://docs.microsoft.com/azure/active-directory/develop/msal-handling-exceptions?tabs=python)。

### <a name="api-changes"></a>API 變更

下表列出了 Python ADAL 中的 API，以及要在其位置用於 Python 的 API：

| 用於 Python API 的 ADAL  | 用於 Python API 的 MSAL |
| ------------------- | ---------------------------------- |
| [身份驗證上下文](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext)  | [公共用戶端應用程式或機密用戶端應用程式](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)  |
| N/A  | [get_authorization_request_url（）](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.get_authorization_request_url)  |
| [acquire_token_with_authorization_code（）](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_authorization_code) | [acquire_token_by_authorization_code（）](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_by_authorization_code) |
| [acquire_token（）](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token) | [acquire_token_silent（）](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_silent) |
| [acquire_token_with_refresh_token（）](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_refresh_token) | N/A |
| [acquire_user_code（）](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_user_code) | [initiate_device_flow（）](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.initiate_device_flow) |
| [acquire_token_with_device_code（）](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_device_code)和[cancel_request_to_get_token_with_device_code（）](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.cancel_request_to_get_token_with_device_code) | [acquire_token_by_device_flow（）](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow) |
| [acquire_token_with_username_password（）](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_username_password) | [acquire_token_by_username_password（）](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_username_password) |
| [acquire_token_with_client_credentials（）](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_credentials)和[acquire_token_with_client_certificate（）](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_certificate) | [acquire_token_for_client（）](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client) |
| N/A | [acquire_token_on_behalf_of（）](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) |
| [權杖緩存（）](https://adal-python.readthedocs.io/en/latest/#adal.TokenCache) | [序列化權杖緩存（）](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) |
| N/A | 具有持久性的緩存，可從[MSAL 擴展獲得](https://github.com/marstr/original-microsoft-authentication-extensions-for-python) |

## <a name="migrate-existing-refresh-tokens-for-msal-python"></a>遷移 MSAL Python 的現有刷新權杖

Microsoft 身份驗證庫 （MSAL） 抽象了刷新權杖的概念。 預設情況下，MSAL Python 提供記憶體中權杖緩存，因此您無需存儲、查找或更新刷新權杖。 使用者還會看到較少的登錄提示，因為刷新權杖通常可以在沒有使用者干預的情況下更新。 有關權杖緩存的詳細資訊，請參閱 Python[的 MSAL 中的自訂權杖緩存序列化](msal-python-token-cache-serialization.md)。

以下代碼將説明您遷移由另一個 OAuth2 庫（包括但不限於 ADAL Python）管理的刷新權杖，以便由 MSAL 管理 Python。 遷移這些刷新權杖的一個原因是，在將應用遷移到 Python 的 MSAL 時，防止現有使用者需要再次登錄。

遷移刷新權杖的方法是使用 Python 的 MSAL 使用以前的刷新權杖獲取新的訪問權杖。 返回新的刷新權杖後，Python 的 MSAL 會將其存儲在緩存中。 下面是如何執行此操作的示例：

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
