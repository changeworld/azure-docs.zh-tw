---
title: 將 MSAL 與 Azure 活動目錄 B2C 學習一起使用 |蔚藍
titleSuffix: Microsoft identity platform
description: 適用於 JavaScript 的 Microsoft 驗證函式庫 (MSAL.js) 使應用程式能夠使用 Azure AD B2C 並取得權碼來呼叫安全的 Web API。 這些 Web API 可以是 Microsoft Graph、其他 Microsoft API、來自其他發行者的 Web API，或是您自己的 Web API。
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
ms.openlocfilehash: dc8a330bc09f37f7941534ed7c17d1ffd14d08c5
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875957"
---
# <a name="use-microsoft-authentication-library-for-javascript-to-work-with-azure-active-directory-b2c"></a>使用 JavaScript 的 Microsoft 驗證函式庫使用 Azure 活動目錄 B2C

[適用於 JavaScript (MSAL.js) 的 Microsoft 身份驗證庫](https://github.com/AzureAD/microsoft-authentication-library-for-js)使 JavaScript 開發人員能夠使用[Azure 活動目錄 B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/)對具有社交和本地識別的使用者進行身份驗證。 通過使用 Azure AD B2C 作為身份管理服務,您可以自訂和控制客戶在使用應用程式時註冊、登錄和管理其配置檔的方式。

Azure AD B2C 還使您能夠在身份驗證過程中對應用程式的 UI 進行品牌認證和自定義,以便為客戶提供無縫體驗。

本文演示如何使用 MSAL.js 使用 Azure AD B2C,並總結了您應該注意的要點。 有關完整的討論和教程,請參閱[Azure AD B2C 文件](https://docs.microsoft.com/azure/active-directory-b2c/overview)。

## <a name="prerequisites"></a>Prerequisites

如果尚未創建自己的[Azure AD B2C 租戶](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant),請從現在創建租戶開始(如果已有 Azure AD B2C 租戶,也可以使用現有的 Azure AD B2C 租戶)。

此展示包含兩個部分:

- 如何保護 Web API。
- 如何註冊單頁應用程式以驗證和調用*該*Web API。

## <a name="nodejs-web-api"></a>Node.js Web API

> [!NOTE]
> 此時,Node 的 MSAL.js 仍處於開發階段(請參閱[路線圖](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap))。 同時,我們建議使用由 Microsoft 開發和支援的 Node.js 的[身份驗證庫「護照 azure-ad」。。](https://github.com/AzureAD/passport-azure-ad)

以下步驟演示了 Web **API**如何使用 Azure AD B2C 來保護自己並將選定的作用域公開給用戶端應用程式。

### <a name="step-1-register-your-application"></a>步驟 1：註冊您的應用程式

要使用 Azure AD B2C 保護 Web API,首先需要註冊它。 請參閱[註冊您的應用程式](https://docs.microsoft.com/azure/active-directory-b2c/add-web-application?tabs=applications) \(英文\) 以取得詳細步驟。

### <a name="step-2-download-the-sample-application"></a>第二步:下載範例應用程式

將範例下載為 ZIP 檔案，或是從 GitHub 複製它：

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="step-3-configure-authentication"></a>第三步:設定認證

1. 開啟範例中的 `config.js` 檔案。

2. 使用之前註冊應用程式時獲得的應用程式憑據配置範例。 通過將值替換為客戶端 ID、主機、租戶 Id 和策略名稱的名稱來更改以下代碼行。

```JavaScript
const clientID = "<Application ID for your Node.js Web API - found on Properties page in Azure portal e.g. 93733604-cc77-4a3c-a604-87084dd55348>";
const b2cDomainHost = "<Domain of your B2C host eg. fabrikamb2c.b2clogin.com>";
const tenantId = "<your-tenant-ID>.onmicrosoft.com"; // Alternatively, you can use your Directory (tenant) ID (GUID)
const policyName = "<Name of your sign in / sign up policy, e.g. B2C_1_signupsignin1>";
```

有關詳細資訊,請查看此[Node.js B2C Web API 範例](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)。

---

## <a name="javascript-spa"></a>JavaScript SPA

以下步驟演示**了單頁應用程式**如何使用 Azure AD B2C 註冊、登錄和調用受保護的 Web API。

### <a name="step-1-register-your-application"></a>步驟 1：註冊您的應用程式

若要實作驗證，您必須先註冊您的應用程式。 請參閱[註冊您的應用程式](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications) \(英文\) 以取得詳細步驟。

### <a name="step-2-download-the-sample-application"></a>第二步:下載範例應用程式

將範例下載為 ZIP 檔案，或是從 GitHub 複製它：

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>第三步:設定認證

設定應用程式有兩點興趣:

- 設定 API 終結點與公開作用區
- 設定驗證參數與權杖範圍

1. 開啟範例中的 `apiConfig.js` 檔案。

2. 使用註冊 Web API 時之前獲得的參數配置範例。 通過將值替換為 Web API 位址和公開作用域,更改以下代碼行。

   ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"], //API scopes you exposed during api registration
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello" 
    };
   ```

3. 開啟範例中的 `authConfig.js` 檔案。

4. 使用之前在註冊單頁應用程式時獲得的參數配置範例。 通過將值替換為 ClientId、許可權中繼資料和權杖請求作用域來更改以下代碼行。

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

關於詳細資訊,請檢視此[JavaScript B2C 單頁應用程式範例](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)。

---

## <a name="next-steps"></a>後續步驟

深入了解：
- [使用者流](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows)
- [自訂原則](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)
- [UX 自訂](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-configure-user-input)
