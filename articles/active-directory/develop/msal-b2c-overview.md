---
title: 將 MSAL 與 Azure 活動目錄 B2C 學習一起使用 |蔚藍
titleSuffix: Microsoft identity platform
description: Microsoft 驗證程式庫 (MSAL) 可讓應用程式與 Azure AD B2C 整合，並取得權杖以呼叫安全的 Web API。 這些 Web API 可以是 Microsoft Graph、其他 Microsoft API、來自其他發行者的 Web API，或是您自己的 Web API。
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
ms.openlocfilehash: e25564e64410701754390024a5bcfd39321343e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696447"
---
# <a name="use-microsoft-authentication-library-to-interoperate-with-azure-active-directory-b2c"></a>使用 Microsoft 驗證程式庫來與 Azure Active Directory B2C 整合

Microsoft 驗證程式庫 (MSAL) 可讓應用程式開發人員使用 [Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/) \(部分機器翻譯\)，以社交和本機身分識別驗證使用者。 Azure AD B2C 是身分識別管理服務。 透過使用它，您可以自訂及控制客戶註冊及登入的方式，並在他們使用您的應用程式時管理其設定檔。

Azure AD B2C 也可讓您為您的應用程式設定品牌及自訂其 UI，進而為您的客戶提供順暢的體驗。

本教學課程會示範如何使用 MSAL 來與 Azure AD B2C 整合。

## <a name="prerequisites"></a>Prerequisites

如果您尚未建立自己的 [Azure AD B2C 租用戶](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) \(部分機器翻譯\)，請立即建立一個。 您也可以使用現有的 Azure AD B2C 租用戶。

## <a name="javascript"></a>JavaScript

下列步驟會示範單頁應用程式如何使用 Azure AD B2C 來進行註冊、登入，以及呼叫受保護的 Web API。

### <a name="step-1-register-your-application"></a>步驟 1：註冊您的應用程式

若要實作驗證，您必須先註冊您的應用程式。 請參閱[註冊您的應用程式](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#step-4-register-your-own-web-application-with-azure-ad-b2c) \(英文\) 以取得詳細步驟。

### <a name="step-2-download-the-sample-application"></a>第 2 步：下載應用程式範例

將範例下載為 ZIP 檔案，或是從 GitHub 複製它：

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>第 3 步：配置身份驗證

1. 開啟範例中的 **index.html** 檔案。

1. 使用註冊應用程式時較早記錄的用戶端 ID 和金鑰配置示例。 變更下列幾行程式碼，做法是將其中的值取代為您的目錄和 API 名稱：

   ```javascript
   // The current application coordinates were pre-registered in a B2C tenant.

    var appConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"],
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };

    const msalConfig = {
        auth: {
            clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902" //This is your client/application ID
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi", //This is your tenant info
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    // create UserAgentApplication instance
    const myMSALObj = new Msal.UserAgentApplication(msalConfig);

   ```

本教學課程中[使用者流程](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) \(部分機器翻譯\) 的名稱是 **B2C_1_signupsignin1**。 如果您使用的是不同的使用者流程名稱，請將 **authority** 值設定為該名稱。

### <a name="step-4-configure-your-application-to-use-b2clogincom"></a>第 4 步：將應用程式佈建為使用`b2clogin.com`

您可以使用 `b2clogin.com` 來取代 `login.microsoftonline.com` 作為重新導向 URL。 您會在針對註冊和登入設定識別提供者時，在您的 Azure AD B2C 應用程式中那麼做。

在 `https://your-tenant-name.b2clogin.com/your-tenant-guid` 的內容中使用 `b2clogin.com` 會有下列影響：

- Microsoft 服務在 Cookie 標頭中會耗用較少的空間。
- 您的 URL 不再包含對 Microsoft 的參考。 例如，您的 Azure AD B2C 應用程式可能會參考 `login.microsoftonline.com`。

 若要使用 `b2clogin.com`，您需要更新您應用程式的設定。  

- 將 **validateAuthority** 屬性設定為 `false`，以確保使用 `b2clogin.com` 的重新導向可以發生。

下列範例示範如何設定該屬性：

```javascript
// The current application coordinates were pre-registered in a B2C directory.

const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://contoso.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://contosohello.azurewebsites.net/hello',
        validateAuthority: false;

};
// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

> [!NOTE]
> 您 Azure AD B2C 應用程式可能有數個地方 (例如使用者流程參考和權杖端點) 都會參考 `login.microsoftonline.com`。 請確定您的授權端點、權杖端點及簽發者都已更新成使用 `your-tenant-name.b2clogin.com`。

遵循[此 MSAL JavaScript 範例](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#single-page-application-built-on-msaljs-with-azure-ad-b2c) \(英文\) 以了解如何使用適用於 JavaScript 的 MSAL 預覽 (MSAL.js)。 範例會取得存取權杖，並呼叫由 Azure AD B2C 所保護的 API。

## <a name="next-steps"></a>後續步驟

深入了解：

- [自訂原則](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [使用者介面自訂](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-overview)