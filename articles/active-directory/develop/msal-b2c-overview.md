---
title: 搭配 Azure Active Directory B2CLearn 使用 MSAL |Azure
titleSuffix: Microsoft identity platform
description: 適用于 JavaScript 的 Microsoft 驗證程式庫（MSAL）可讓應用程式使用 Azure AD B2C 並取得權杖來呼叫受保護的 web Api。 這些 Web API 可以是 Microsoft Graph、其他 Microsoft API、來自其他發行者的 Web API，或是您自己的 Web API。
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/16/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 8e076dfd6670265d458eb35d8e1b3e4500009a12
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81534477"
---
# <a name="use-microsoft-authentication-library-for-javascript-to-work-with-azure-active-directory-b2c"></a>使用適用于 JavaScript 的 Microsoft 驗證程式庫來處理 Azure Active Directory B2C

[適用于 javascript 的 Microsoft 驗證程式庫（MSAL）](https://github.com/AzureAD/microsoft-authentication-library-for-js)可讓 javascript 開發人員使用[Azure Active Directory B2C （Azure AD B2C）](https://docs.microsoft.com/azure/active-directory-b2c/)來驗證具有社交和本機身分識別的使用者。 藉由使用 Azure AD B2C 做為身分識別管理服務，您可以自訂和控制客戶在使用您的應用程式時，如何註冊、登入及管理其設定檔。

Azure AD B2C 也可讓您在驗證過程中，為您的應用程式提供品牌和自訂 UI，以便為客戶提供順暢的體驗。

本文示範如何使用 MSAL 來處理 Azure AD B2C，並摘要說明您應該留意的重點。 如需完整的討論和教學課程，請參閱[Azure AD B2C 檔](https://docs.microsoft.com/azure/active-directory-b2c/overview)。

## <a name="prerequisites"></a>先決條件

如果您尚未建立自己的[Azure AD B2C 租](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)使用者，請從立即建立一個來開始（您也可以使用現有的 Azure AD B2C 租使用者，如果您已經有的話）。

本示範包含兩個部分：

- 如何保護 Web API。
- 如何註冊單一頁面應用程式來進行驗證，並呼叫*該*Web API。

## <a name="nodejs-web-api"></a>Node.js Web API

> [!NOTE]
> 目前，MSAL 的節點仍在開發中（請參閱[藍圖](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)）。 在此同時，我們建議您使用[passport-azure-ad](https://github.com/AzureAD/passport-azure-ad)，這是由 Microsoft 開發及支援的 node.js 驗證程式庫。

下列步驟會示範**Web API**如何使用 Azure AD B2C 來保護自己，並將選取的範圍公開給用戶端應用程式。

### <a name="step-1-register-your-application"></a>步驟 1:註冊您的應用程式

若要使用 Azure AD B2C 保護您的 Web API，您必須先註冊它。 請參閱[註冊您的應用程式](https://docs.microsoft.com/azure/active-directory-b2c/add-web-application?tabs=applications) \(英文\) 以取得詳細步驟。

### <a name="step-2-download-the-sample-application"></a>步驟2：下載範例應用程式

將範例下載為 ZIP 檔案，或是從 GitHub 複製它：

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="step-3-configure-authentication"></a>步驟3：設定驗證

1. 開啟範例中的 `config.js` 檔案。

2. 使用您稍早在註冊應用程式時取得的應用程式認證來設定範例。 將值取代為您的 clientID、主機、tenantId 和原則名稱的名稱，以變更下列幾行程式碼。

```JavaScript
const clientID = "<Application ID for your Node.js web API - found on Properties page in Azure portal e.g. 93733604-cc77-4a3c-a604-87084dd55348>";
const b2cDomainHost = "<Domain of your B2C host eg. fabrikamb2c.b2clogin.com>";
const tenantId = "<your-tenant-ID>.onmicrosoft.com"; // Alternatively, you can use your Directory (tenant) ID (GUID)
const policyName = "<Name of your sign in / sign up policy, e.g. B2C_1_signupsignin1>";
```

如需詳細資訊，請參閱此[NODE.JS B2C Web API 範例](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)。

---

## <a name="javascript-spa"></a>JavaScript SPA

下列步驟會示範**單頁應用程式**如何使用 Azure AD B2C 來註冊、登入及呼叫受保護的 Web API。

### <a name="step-1-register-your-application"></a>步驟 1:註冊您的應用程式

若要實作驗證，您必須先註冊您的應用程式。 請參閱[註冊您的應用程式](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications) \(英文\) 以取得詳細步驟。

### <a name="step-2-download-the-sample-application"></a>步驟2：下載範例應用程式

將範例下載為 ZIP 檔案，或是從 GitHub 複製它：

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>步驟3：設定驗證

設定應用程式時有兩個重點：

- 設定 API 端點和公開的範圍
- 設定驗證參數和權杖範圍

1. 開啟範例中的 `apiConfig.js` 檔案。

2. 使用您稍早在註冊 Web API 時取得的參數來設定範例。 將值取代為您的 Web API 和公開範圍的位址，以變更下列幾行程式碼。

   ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"], //API scopes you exposed during api registration
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };
   ```

3. 開啟範例中的 `authConfig.js` 檔案。

4. 使用您稍早在註冊單一頁面應用程式時取得的參數來設定範例。 藉由將值取代為您的 ClientId、授權中繼資料和權杖要求範圍，來變更下列幾行程式碼。

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

如需詳細資訊，請參閱此[JAVASCRIPT B2C 單一頁面應用程式範例](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)。

---

## <a name="next-steps"></a>後續步驟

深入了解：
- [使用者流程](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows)
- [自訂原則](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)
- [UX 自訂](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-configure-user-input)
