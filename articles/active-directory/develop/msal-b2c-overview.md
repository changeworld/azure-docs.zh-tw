---
title: 搭配使用 MSAL.js 與 Azure AD B2C
titleSuffix: Microsoft identity platform
description: '適用于 JavaScript 的 Microsoft 驗證程式庫 ( # A0) 可讓應用程式使用 Azure AD B2C 和取得權杖來呼叫受保護的 web Api。 這些 Web API 可以是 Microsoft Graph、其他 Microsoft API、來自其他發行者的 Web API，或是您自己的 Web API。'
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/05/2020
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 13b478e85278827258ea2fc25a0ee4298039fb1c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88119781"
---
# <a name="use-microsoft-authentication-library-for-javascript-to-work-with-azure-ad-b2c"></a>使用適用于 JavaScript 的 Microsoft 驗證程式庫來處理 Azure AD B2C

[適用于 javascript 的 Microsoft 驗證程式庫 ( # A0) ](https://github.com/AzureAD/microsoft-authentication-library-for-js) 可讓 JavaScript 開發人員使用 [Azure Active Directory B2C](../../active-directory-b2c/overview.md) (Azure AD B2C) ，以社交和本機身分識別驗證使用者。

藉由使用 Azure AD B2C 作為身分識別管理服務，您可以自訂及控制客戶在使用您的應用程式時，註冊、登入及管理其設定檔的方式。 Azure AD B2C 也可讓您為應用程式在驗證程式期間所顯示的 UI 提供品牌和自訂。

下列各節將示範如何：

- 保護 Node.js web API
- 支援在單一頁面應用程式中 (SPA) 登入，並呼叫受保護 *的* web API
- 啟用密碼重設支援

## <a name="prerequisites"></a>必要條件

如果您還沒有這麼做，請建立 [Azure AD B2C 的租](../../active-directory-b2c/tutorial-create-tenant.md)使用者。

## <a name="nodejs-web-api"></a>Node.js Web API

下列步驟會示範 **WEB API** 如何使用 Azure AD B2C 來保護本身，並將選取的範圍公開給用戶端應用程式。

節點的 MSAL.js 目前正在開發中。 如需詳細資訊，請參閱 GitHub 上的 [藍圖](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap) 。 我們目前建議使用 [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad)，這是 Microsoft 所開發及支援之 Node.js 的驗證程式庫。

### <a name="step-1-register-your-application"></a>步驟 1:註冊您的應用程式

若要使用 Azure AD B2C 保護您的 web API，您必須先註冊它。 請參閱[註冊您的應用程式](../../active-directory-b2c/add-web-api-application.md) \(英文\) 以取得詳細步驟。

### <a name="step-2-download-the-sample-application"></a>步驟2：下載範例應用程式

將範例下載為 ZIP 檔案，或是從 GitHub 複製它：

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="step-3-configure-authentication"></a>步驟3：設定驗證

1. 開啟範例中的 `config.js` 檔案。

2. 使用您稍早在註冊應用程式時取得的應用程式認證來設定範例。 變更下列幾行程式碼，將值取代為您的 clientID、主機、tenantId 和原則名稱的名稱。

```JavaScript
const clientID = "<Application ID for your Node.js web API - found on Properties page in Azure portal e.g. 93733604-cc77-4a3c-a604-87084dd55348>";
const b2cDomainHost = "<Domain of your B2C host eg. fabrikamb2c.b2clogin.com>";
const tenantId = "<your-tenant-ID>.onmicrosoft.com"; // Alternatively, you can use your Directory (tenant) ID (GUID)
const policyName = "<Name of your sign in / sign up policy, e.g. B2C_1_signupsignin1>";
```

如需詳細資訊，請參閱此 [Node.js B2C WEB API 範例](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)。

## <a name="javascript-spa"></a>JavaScript SPA

下列步驟示範 **單頁應用程式** 如何使用 Azure AD B2C 來註冊、登入，以及呼叫受保護的 web API。

### <a name="step-1-register-your-application"></a>步驟 1:註冊您的應用程式

若要實作驗證，您必須先註冊您的應用程式。 請參閱[註冊您的應用程式](../../active-directory-b2c/tutorial-register-applications.md) \(英文\) 以取得詳細步驟。

### <a name="step-2-download-the-sample-application"></a>步驟2：下載範例應用程式

下載程式代碼範例 [。ZIP](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) 封存或複製 GitHub 存放庫：

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>步驟3：設定驗證

設定您的應用程式有兩個重點：

- 設定 API 端點和公開的範圍
- 設定驗證參數和權杖範圍

1. 開啟範例中的 *apiConfig.js* 檔案。

2. 使用您稍早在註冊 web API 時取得的參數來設定範例。 變更下列幾行程式碼，將值取代為您 web API 的位址和公開的範圍。

   ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"], //API scopes you exposed during api registration
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };
   ```

1. 開啟範例中的 *authConfig.js* 檔案。

1. 使用您稍早在註冊單一頁面應用程式時取得的參數來設定範例。 藉由將值取代為您的 ClientId、授權中繼資料和權杖要求範圍，來變更下列幾行程式碼。

   ```javascript
    // Config object to be passed to Msal on creation.
    const msalConfig = {
        auth: {
            clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/B2C_1_signupsignin1",
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage", // This configures where your cache will be stored
            storeAuthStateInCookie: false // Set this to "true" to save cache in cookies
        }
    };

    // Add here scopes for id token to be used at the MS Identity Platform endpoint
    const loginRequest = {
        scopes: ["openid", "profile"],
    };
   ```

如需詳細資訊，請參閱此 [JAVASCRIPT B2C 單一頁面應用程式範例](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)。

## <a name="support-password-reset"></a>支援密碼重設

在本節中，您會擴充單一頁面應用程式，以使用 Azure AD B2C 密碼重設使用者流程。 雖然 MSAL.js 目前並不支援多個使用者流程或自訂原則，但您可以使用程式庫來處理密碼重設之類的常見使用案例。

下列步驟假設您已遵循先前的 [JAVASCRIPT SPA](#javascript-spa) 一節中的步驟。

### <a name="step-1-define-the-authority-string-for-password-reset-user-flow"></a>步驟1：定義密碼重設使用者流程的授權字串

1. 首先，建立一個物件來儲存您的授權單位 Uri：

    ```javascript
        const b2cPolicies = {
            names: {
                signUpSignIn: "b2c_1_susi",
                forgotPassword: "b2c_1_reset"
            },
            authorities: {
                signUpSignIn: {
                    authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi",
                },
                forgotPassword: {
                    authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_reset",
                },
            },
        }
    ```

1. 接下來，使用預設的原則將 MSAL 物件初始化 `signInSignUp` (請參閱上述的程式碼片段) 。 當使用者嘗試登入時，就會看到下列畫面：

    :::image type="content" source="media/msal-b2c-overview/user-journey-01-signin.png" alt-text="Azure AD B2C 顯示的登入畫面":::

### <a name="step-2-catch-and-handle-authentication-errors-in-your-login-method"></a>步驟2：攔截並處理登入方法中的驗證錯誤

當使用者選取 **忘記密碼**時，您的應用程式會擲回您應該在程式碼中攔截的錯誤，然後藉由呈現適當的使用者流程來處理。 在此情況下， `b2c_1_reset` 密碼重設流程。

1. 擴充您的登入方法，如下所示：

    ```javascript
    function signIn() {
      myMSALObj.loginPopup(loginRequest)
        .then(loginResponse => {
            console.log("id_token acquired at: " + new Date().toString());

            if (myMSALObj.getAccount()) {
              updateUI();
            }

        }).catch(function (error) {
          console.log(error);

          // error handling
          if (error.errorMessage) {
            // check for forgot password error
            if (error.errorMessage.indexOf("AADB2C90118") > -1) {

              //call login method again with the password reset user flow
              myMSALObj.loginPopup(b2cPolicies.authorities.forgotPassword)
                .then(loginResponse => {
                  console.log(loginResponse);
                  window.alert("Password has been reset successfully. \nPlease sign-in with your new password.");
                })
            }
          }
        });
    }
    ```

1. 上述程式碼片段會示範如何在使用程式碼攔截錯誤後顯示密碼重設畫面 `AADB2C90118` 。

    重設其密碼之後，會將使用者重新傳回給應用程式，以重新登入。

    :::image type="content" source="media/msal-b2c-overview/user-journey-02-password-reset.png" alt-text="Azure AD B2C 顯示的登入畫面" border="false":::

    如需錯誤碼和處理例外狀況的詳細資訊，請參閱 [MSAL 錯誤和例外狀況代碼](msal-handling-exceptions.md)。

## <a name="next-steps"></a>後續步驟

深入瞭解這些 Azure AD B2C 概念：

- [使用者流程](../../active-directory-b2c/tutorial-create-user-flows.md)
- [自訂原則](../../active-directory-b2c/custom-policy-get-started.md)
- [UX 自訂](../../active-directory-b2c/custom-policy-configure-user-input.md)