---
title: 設定 OpenID Connect 提供者（預覽）
description: 瞭解如何將 OpenID Connect 提供者設定為 App Service 或 Azure Functions 應用程式的身分識別提供者。
ms.topic: article
ms.date: 07/08/2020
ms.reviewer: mahender
ms.openlocfilehash: 89164061a968e37f928f8c21f5323c418e85361f
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87413913"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-login-using-an-openid-connect-provider-preview"></a>使用 OpenID Connect 提供者（預覽）將您的 App Service 或 Azure Functions 應用程式設定為登入

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本文說明如何設定 Azure App Service 或 Azure Functions，以使用符合[OpenID connect 規格](https://openid.net/connect/)的自訂驗證提供者。 OpenID Connect （OIDC）是許多身分識別提供者（Idp）所使用的業界標準。 您不需要瞭解規格的詳細資料，就能將應用程式設定為使用 adherent IDP。

您可以將應用程式設定為使用一或多個 OIDC 提供者。 每個都必須在設定中提供唯一的名稱，而且只有一個可作為預設的重新導向目標。

> [!CAUTION]
> 啟用 OpenID Connect 提供者將會停用透過某些用戶端（例如 Azure 入口網站、Azure CLI 和 Azure PowerShell）來管理應用程式的 App Service 驗證/授權功能。 此功能依賴新的 API 介面，在預覽期間，尚未在所有管理體驗中列入考慮。

## <a name="register-your-application-with-the-identity-provider"></a><a name="register"> </a>向識別提供者註冊您的應用程式

您的提供者會要求您向其註冊應用程式的詳細資料。 請參閱與該提供者相關的指示。 您必須收集應用程式的**用戶端識別碼**和**用戶端密碼**。

> [!IMPORTANT]
>  用戶端密碼是重要的安全性認證。 請勿與任何人共用此密碼，或在用戶端應用程式中加以散發。
>

> [!NOTE]
> 某些提供者可能需要額外的步驟來進行設定，以及如何使用它們所提供的值。 例如，Apple 提供的私密金鑰本身並不是用來做為 OIDC 用戶端密碼，而您必須使用它來製作 JWT，這會被視為您在應用程式設定中提供的密碼（請參閱[使用 Apple 登入](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens)的「建立用戶端密碼」一節）
>

使用您選擇的設定名稱，將用戶端密碼新增為應用[程式的應用程式設定](./configure-common.md#configure-app-settings)。 請記下此名稱以供稍後查看。

此外，您還需要提供者的 OpenID Connect 中繼資料。 這通常是透過設定[元資料檔案](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig)來公開，這是提供者的簽發者 URL （尾碼為） `/.well-known/openid-configuration` 。 收集此設定 URL。

如果您無法使用設定元資料檔案，您將需要分別收集下列值：

- 簽發者 URL （有時顯示為 `issuer` ）
- [OAuth 2.0 授權端點](https://tools.ietf.org/html/rfc6749#section-3.1)（有時顯示為 `authorization_endpoint` ）
- [OAuth 2.0 權杖端點](https://tools.ietf.org/html/rfc6749#section-3.2)（有時會顯示為 `token_endpoint` ）
- [OAuth 2.0 JSON Web 金鑰集](https://tools.ietf.org/html/rfc8414#section-2)檔的 URL （有時顯示為 `jwks_uri` ）

## <a name="add-provider-information-to-your-application"></a><a name="configure"> </a>將提供者資訊新增至您的應用程式

> [!NOTE]
> 所需的設定為新的 API 格式，目前僅支援以檔案[為基礎的設定（預覽）](.\app-service-authentication-how-to.md#config-file)。 您必須使用這類檔案來遵循下列步驟。

本節將逐步引導您更新設定，以包含您的新 IDP。 範例設定如下。

1. 在 `identityProviders` 物件中，加入 `openIdConnectProviders` 物件（如果尚未存在的話）。
1. 在 `openIdConnectProviders` 物件中，為新的提供者新增金鑰。 這是一個易記名稱，用來在設定的其餘部分參考提供者。 例如，如果您想要要求使用此提供者來驗證所有要求，請將設定 `globalValidation.unauthenticatedClientAction` 為 "RedirectToLoginPage"，並將設定 `globalValidation.unauthenticatedClientAction` 為這個相同的易記名稱。
1. 將物件指派給該索引鍵，其中包含其中的 `registration` 物件，以及選擇性的 `login` 物件：
    
    ```json
    "myCustomIDP" : {
       "registration" : {},
       "login": {
             "nameClaimType": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
             "scope": [],
             "loginParameterNames": [],
       }
    }
    ```

1. 在註冊物件中，將設定 `clientId` 為您所收集的用戶端識別碼，並將設定 `clientCredential.secretSettingName` 為您儲存用戶端密碼的應用程式設定名稱，然後建立 `openIdConnectConfiguration` 物件：

    ```json
    "registration": {
      "clientId": "bd96cf8a-3f2b-4806-b180-d3c5fd11a2be",
      "clientCredential": {
         "secretSettingName": "IDP_CLIENT_SECRET"
      },
      "openIdConnectConfiguration" : {}
    }
    ```

1. 在 `openIdConnectConfiguration` 物件中，提供您稍早收集的 OpenID connect 中繼資料。 根據您所收集的資訊，有兩個選項：

    - 將 `wellKnownOpenIdConfiguration` 屬性設為您稍早收集的設定中繼資料 URL。
    - 或者，設定所收集的四個個別值，如下所示：
        - 設定 `issuer` 為簽發者 URL
        - 設定 `authorizationEndpoint` 為授權端點
        - 設定 `tokenEndpoint` 為權杖端點
        - 設定 `certificationUri` 為 JSON Web 金鑰集檔的 URL

    這兩個選項互斥。

設定此設定之後，您就可以開始在應用程式中使用您的 OpenID Connect 提供者進行驗證。

範例設定可能如下所示（使用 Apple 的登入作為範例，其中 APPLE_GENERATED_CLIENT_SECRET 設定會指向根據[apple 檔](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens)產生的 JWT）：

```json
{
    "platform": {
        "enabled": true
    },
    "globalValidation": {
        "redirectToProvider": "apple",
        "unauthenticatedClientAction": "RedirectToLoginPage"
    },
    "identityProviders": {
        "openIdConnectProviders": {
            "apple": {
                "registration": {
                    "clientId": "com.contoso.example.client",
                    "clientCredential": {
                        "secretSettingName": "APPLE_GENERATED_CLIENT_SECRET"
                    },
                    "openIdConnectConfiguration": {
                        "wellKnownOpenIdConfiguration": "https://appleid.apple.com/.well-known/openid-configuration"
                    }
                },
                "login": {
                    "nameClaimType": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
                    "scope": [],
                    "loginParameterNames": []
                }
            }
        }
    },
    "login": {
        "tokenStore": {
            "enabled": true
        }
    }     
}
```

## <a name="next-steps"></a><a name="related-content"> </a>後續步驟

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
