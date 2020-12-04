---
title: '使用 Apple (Preview 設定登入) '
description: 瞭解如何為您的 App Service 或 Azure Functions 應用程式，設定使用 Apple 作為身分識別提供者的登入。
ms.topic: article
ms.date: 11/19/2020
ms.reviewer: mikarmar
ms.openlocfilehash: b77e0613f502d003b5e4651e34be4cadbd4209a9
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2020
ms.locfileid: "96603160"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-sign-in-using-a-sign-in-with-apple-provider-preview"></a>設定您的 App Service 或 Azure Functions 應用程式以使用 Apple 提供者的登入進行登入 (預覽) 

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本文說明如何設定 Azure App Service 或 Azure Functions，以使用以 Apple 作為驗證提供者的登入。 

若要完成本文中的程式，您必須已在 Apple 開發人員計畫中註冊。 若要註冊 Apple 開發人員計畫，請移至 [developer.apple.com/programs/enroll](https://developer.apple.com/programs/enroll/)。

> [!CAUTION]
> 啟用 [使用 Apple 登入] 將會停用透過某些用戶端（例如 Azure 入口網站、Azure CLI 和 Azure PowerShell）來管理應用程式的 App Service 驗證/授權功能。 此功能依賴新的 API 介面，在預覽期間，尚未在所有管理體驗中考慮。

[comment]: <Remove this caution block when V2 becomes available for use.> 

## <a name="create-an-application-in-the-apple-developer-portal"></a><a name="createApplication"> </a>在 Apple Developer 入口網站中建立應用程式
您必須在 Apple 開發人員入口網站中建立應用程式識別碼和服務識別碼。

1. 在 Apple Developer 入口網站上，移至 [ **憑證]、[識別碼]、[& 設定檔**]。
2. 在 [ **識別碼** ] 索引標籤上，選取 [ **(+)** ] 按鈕。
3. 在 [ **註冊新識別碼** ] 頁面上，選擇 [ **應用程式** 識別碼]，然後選取 [ **繼續**]。  (的應用程式識別碼包含一或多個服務 ![ 識別碼。 ) 在 Apple 開發人員入口網站中註冊新的應用程式識別碼](media/configure-authentication-provider-apple/apple-register-app.jpg)
4. 在 [ **註冊應用程式識別碼** ] 頁面上，提供描述和套件組合識別碼，然後從 [功能] 清單中選取 [ **使用 Apple 登入** ]。 然後選取 [繼續]  。 請記下您的 **應用程式識別碼首碼 (TEAM ID)** 此步驟中，您稍後將會用到。
![在 Apple Developer 入口網站中設定新的應用程式識別碼](media/configure-authentication-provider-apple/apple-configure-app.jpg)
5. 檢查應用程式註冊資訊，然後選取 [ **註冊**]。
6. 同樣地，在 [ **識別碼** ] 索引標籤上，選取 [ **(+)** ] 按鈕。
![在 Apple Developer 入口網站中建立新的服務識別碼](media/configure-authentication-provider-apple/apple-new-app.jpg) 
7. 在 [ **註冊新識別碼** ] 頁面上，選擇 [ **服務** 識別碼]，然後選取 [ **繼續**]。
![在 Apple Developer 入口網站中註冊新的服務識別碼](media/configure-authentication-provider-apple/apple-register-service.jpg)
8. 在 [ **註冊服務識別碼** ] 頁面上，提供描述和識別碼。 描述會顯示在同意畫面上的使用者。 識別碼將是您用來設定應用程式服務的 Apple 提供者的用戶端識別碼。 然後選取 [設定]。
![提供描述和識別碼](media/configure-authentication-provider-apple/apple-configure-service-1.jpg)
9. 在快顯視窗中，將主要應用程式識別碼設定為您稍早建立的應用程式識別碼。 在 [網域] 區段中指定應用程式的網域。 若為傳回 URL，請使用 URL `<app-url>/.auth/login/apple/callback` 。 例如： `https://contoso.azurewebsites.net/.auth/login/apple/callback` 。 然後選取 [ **新增** ] 和 [ **儲存**]。
![指定註冊的網域和傳回 URL](media/configure-authentication-provider-apple/apple-configure-service-2.jpg)
10. 檢查服務註冊資訊，然後選取 [ **儲存**]。

## <a name="generate-the-client-secret"></a><a name="generateClientSecret"> </a>產生用戶端密碼
Apple 要求應用程式開發人員建立並簽署 JWT 權杖作為用戶端密碼值。 若要產生此密碼，請先從 Apple 開發人員入口網站產生並下載橢圓曲線私密金鑰。 然後，使用該金鑰以[特定](#structure-the-client-secret-jwt)承載[簽署 JWT](#sign-the-client-secret-jwt) 。

### <a name="create-and-download-the-private-key"></a>建立和下載私密金鑰
1. 在 Apple Developer 入口網站的 [ **金鑰** ] 索引標籤上，選擇 [ **建立金鑰** ] 或選取 [ **(+)** ] 按鈕。
2. 在 [ **註冊新的金鑰** ] 頁面上提供金鑰名稱，勾選 [ **使用 Apple 登入** ] 旁的方塊，然後選取 [ **設定**]。
3. 在 [ **設定金鑰** ] 頁面上，將金鑰連結至您先前建立的主要應用程式識別碼，然後選取 [ **儲存**]。
4. 確認資訊並選取 [ **繼續** ]，然後選取 [ **註冊**]，完成金鑰的建立。
5. 在 [ **下載您的金鑰** ] 頁面上，下載金鑰。 它會下載為 `.p8` (PKCS # 8) 檔案，您將使用檔案內容來簽署用戶端秘密 JWT。

### <a name="structure-the-client-secret-jwt"></a>結構用戶端秘密 JWT
Apple 要求用戶端秘密必須是 JWT 權杖的 base64 編碼。 已解碼的 JWT 權杖應具有如下範例所示的承載：
```json
{
  "alg": "ES256",
  "kid": "URKEYID001",
}.{
  "sub": "com.yourcompany.app1",
  "nbf": 1560203207,
  "exp": 1560289607,
  "iss": "ABC123DEFG",
  "aud": "https://appleid.apple.com"
}.[Signature]
```
- **sub**： Apple 用戶端識別碼也 (服務識別碼) 
- **iss**：您的 Apple 開發人員團隊識別碼
- **aud**： Apple 正在接收權杖，因此是物件
- **exp**： **nbf** 之後不超過六個月

上述承載的 base64 編碼版本看起來像這樣： ```eyJhbGciOiJFUzI1NiIsImtpZCI6IlVSS0VZSUQwMDEifQ.eyJzdWIiOiJjb20ueW91cmNvbXBhbnkuYXBwMSIsIm5iZiI6MTU2MDIwMzIwNywiZXhwIjoxNTYwMjg5NjA3LCJpc3MiOiJBQkMxMjNERUZHIiwiYXVkIjoiaHR0cHM6Ly9hcHBsZWlkLmFwcGxlLmNvbSJ9.ABSXELWuTbgqfrIUz7bLi6nXvkXAz5O8vt0jB2dSHTQTib1x1DSP4__4UrlKI-pdzNg1sgeocolPNTmDKazO8-BHAZCsdeeTNlgFEzBytIpMKFfVEQbEtGRkam5IeclUK7S9oOva4EK4jV4VmgDrr-LGWWO3TaAxAvy3_ZoKohvFFkVG```

_注意：在建立 (或 nbf) 日期之後，Apple 不接受到期日超過六個月的用戶端密碼 Jwt。這表示您必須至少每六個月輪替用戶端密碼。_

如需有關產生和驗證權杖的詳細資訊，請參閱 [Apple 開發人員檔](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens)。 

### <a name="sign-the-client-secret-jwt"></a>簽署用戶端秘密 JWT
您將使用 `.p8` 先前下載的檔案來簽署用戶端秘密 JWT。 這個檔案是 [PCKS # 8](https://en.wikipedia.org/wiki/PKCS_8) 檔案，其中包含 PEM 格式的私用簽署金鑰。 有許多程式庫可以為您建立和簽署 JWT。 

有不同類型的開放原始碼程式庫可在線上使用，以建立和簽署 JWT 權杖。 如需產生 JWT 權杖的詳細資訊，請參閱 jwt.io。 例如，產生用戶端密碼的其中一種方式是匯入 [Microsoft.identitymodel NuGet 套件](https://www.nuget.org/packages/Microsoft.IdentityModel.Tokens/) ，並執行如下所示的少量 c # 程式碼。

```csharp
using Microsoft.IdentityModel.Tokens;

public static string GetAppleClientSecret(string teamId, string clientId, string keyId, string p8key)
{
    string audience = "https://appleid.apple.com";

    string issuer = teamId;
    string subject = clientId;
    string kid = keyId;

    IList<Claim> claims = new List<Claim> {
        new Claim ("sub", subject)
    };

    CngKey cngKey = CngKey.Import(Convert.FromBase64String(p8key), CngKeyBlobFormat.Pkcs8PrivateBlob);

    SigningCredentials signingCred = new SigningCredentials(
        new ECDsaSecurityKey(new ECDsaCng(cngKey)),
        SecurityAlgorithms.EcdsaSha256
    );

    JwtSecurityToken token = new JwtSecurityToken(
        issuer,
        audience,
        claims,
        DateTime.Now,
        DateTime.Now.AddDays(180),
        signingCred
    );
    token.Header.Add("kid", kid);
    token.Header.Remove("typ");

    JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler();

    return tokenHandler.WriteToken(token);
}
```
- **teamId**：您的 Apple 開發人員團隊識別碼
- **clientId**： Apple 用戶端識別碼也 (服務識別碼) 
- **p8key**： PEM 格式索引鍵-您可以 `.p8` 在文字編輯器中開啟檔案，並複製 `-----BEGIN PRIVATE KEY-----` 與 `-----END PRIVATE KEY-----` 不含分行符號的所有專案，以取得金鑰。
- **keyId**：已下載金鑰的識別碼

傳回的這個權杖是您將用來設定 Apple 提供者的用戶端密碼值。

> [!IMPORTANT]
> 用戶端密碼是重要的安全性認證。 請勿與任何人共用此密碼，或在用戶端應用程式中加以散發。
>

使用您選擇的設定名稱，將用戶端密碼新增為應用 [程式的應用程式設定](./configure-common.md#configure-app-settings) 。 請記下此名稱以供稍後之用。

## <a name="add-provider-information-to-your-application"></a><a name="configure"> </a>將提供者資訊新增至您的應用程式

> [!NOTE]
> 需要的設定是新的 API 格式，目前僅支援以檔案 [為基礎的設定 (預覽) ](.\app-service-authentication-how-to.md#config-file)。 您必須遵循下列步驟，才能使用這類檔案。

本節將逐步引導您更新設定，以包含您的新 IDP。 範例設定如下所示。

1. 在 `identityProviders` 物件中加入物件（ `apple` 如果尚未存在的話）。
2. 使用物件 `registration` 內的物件，並選擇性地將物件指派給該索引鍵 `login` ：
    
    ```json
    "apple" : {
       "registration" : {
            "clientId": "<client id>",
            "clientSecretSettingName": "APP_SETTING_CONTAINING_APPLE_CLIENT_SECRET" 
        },
       "login": {
             "scopes": []
       }
    }
    ```
    a. 在 `registration` 物件中，將設定 `clientId` 為您所收集的用戶端識別碼。
    
    b. 在 `registration` 物件中，將設定 `clientSecretSettingName` 為您儲存用戶端密碼的應用程式設定名稱。
    
    c. 在 `login` 物件中，您可以選擇設定 `scopes` 陣列以包含向 Apple 驗證時所使用的範圍清單，例如 "name" 和 "email"。 如果設定了範圍，當使用者第一次登入時，就會在同意畫面上明確要求這些範圍。

設定此設定之後，您就可以在應用程式中使用您的 Apple 提供者進行驗證。

完整的設定看起來可能像下列範例 (其中 APPLE_GENERATED_CLIENT_SECRET 設定指向包含產生的 JWT) 的應用程式設定：

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
        "apple": {
            "registration": {
                "clientId": "com.contoso.example.client",
                "clientSecretSettingName": "APPLE_GENERATED_CLIENT_SECRET"
            },
            "login": {
                "scopes": []
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
