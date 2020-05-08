---
title: 使用授權碼在 JavaScript 單頁應用程式中讓使用者登入 | Azure
titleSuffix: Microsoft identity platform
description: 了解 JavaScript 應用程式如何使用 Microsoft 身分識別平台來呼叫需要存取權杖的 API。
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/22/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript
ROBOTS: NOINDEX
ms.openlocfilehash: 9663c11508b0478a67f528cb301d705a3125e4f6
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871518"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa-using-the-auth-code-flow"></a>快速入門：使用授權碼流程讓使用者登入並取得 JavaScript SPA 中的存取權杖 

> [!IMPORTANT]
> 此功能目前為預覽狀態。 若您同意[補充的使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。


本快速入門會使用 MSAL.js 2.0 搭配授權碼流程。 若要使用 MSAL.js 1.0 搭配隱含流程，請檢視[本快速入門](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-javascript)。

在本快速入門中，您會使用程式碼範例了解 JavaScript 單頁應用程式 (SPA) 如何讓使用者登入個人帳戶、公司帳戶和學校帳戶。 JavaScript SPA 也可以取得呼叫 Microsoft Graph API 或任何 Web API 的存取權杖。 如需圖例，請參閱[此範例的運作方式](#how-the-sample-works)。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶 - [免費建立 Azure 訂用帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) (用以編輯專案檔)


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>註冊並下載快速入門應用程式
> 若要啟動您的快速入門應用程式，請使用下列其中一個選項。
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>選項 1 (快速)：註冊和自動設定您的應用程式，然後下載程式碼範例
>
> 1. 登入 [Azure 入口網站](https://portal.azure.com)。
> 1. 如果您的帳戶可讓您存取多個租用戶，請在右上方選取帳戶，然後將您的入口網站工作階段設定為想要使用的 Azure Active Directory (Azure AD) 租用戶。
> 1. 選取 [應用程式註冊](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs)。
> 1. 輸入應用程式的名稱。
> 1. 在 [支援的帳戶類型]  底下，選取 [任何組織目錄中的帳戶及個人的 Microsoft 帳戶]  。
> 1. 選取 [註冊]  。
> 1. 移至快速入門窗格，然後依照指示下載並自動設定新的應用程式。
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>選項 2 (手動)：註冊並手動設定您的應用程式和程式碼範例
>
> #### <a name="step-1-register-your-application"></a>步驟 1:註冊您的應用程式
>
> 1. 登入 [Azure 入口網站](https://portal.azure.com)。
>
> 1. 如果您的帳戶可讓您存取多個租用戶，請在右上方選取帳戶，然後將您的入口網站工作階段設定為想要使用的 Azure AD 租用戶。
> 1. 選取 [應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)。
> 1. 選取 [新增註冊]  。
> 1. [註冊應用程式]  頁面出現時，輸入您應用程式的名稱。
> 1. 在 [支援的帳戶類型]  底下，選取 [任何組織目錄中的帳戶及個人的 Microsoft 帳戶]  。
> 1. 選取 [註冊]  。 在應用程式 [概觀]  頁面上，記下 [應用程式 (用戶端) 識別碼]  值以供稍後使用。
> 1. 在所註冊應用程式的左側窗格中，選取 [驗證]  。
> 1. 在 [平台設定]  下，選取 [新增平台]  。 左側會開啟一個面板。 在該處選取 [單頁應用程式]  區域。
> 1. 同樣在左側，將 [重新導向 URI]  值設定為 `http://localhost:3000/`。 
> 1. 選取 [設定]  。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>步驟 1:在 Azure 入口網站中設定您的應用程式
> 若要讓此快速入門中的程式碼範例正常運作，您必須將 `redirectUri` 新增為 `http://localhost:3000/`。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [為我進行這些變更]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![已設定](media/quickstart-v2-javascript/green-check.png) 您的應用程式已設定了這些屬性。

#### <a name="step-2-download-the-project"></a>步驟 2:下載專案

> [!div renderon="docs"]
> 若要使用 Node.js 搭配網頁伺服器來執行專案，請[下載核心專案檔](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/quickstart.zip)。

> [!div renderon="portal" class="sxs-lookup"]
> 使用 Node.js 以網頁伺服器執行專案

> [!div renderon="portal" id="autoupdate" class="nextstepaction" class="sxs-lookup"]
> [下載程式碼範例](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/quickstart.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>步驟 3：設定您的 JavaScript 應用程式
>
> 在 *app* 資料夾中編輯 *authConfig.js*，並且在 `msalConfig` 下設定 `clientID`、`authority` 與 `redirectUri` 等值。
>
> ```javascript
>
>  // Config object to be passed to Msal on creation
>  const msalConfig = {
>    auth: {
>      clientId: "Enter_the_Application_Id_Here",
>      authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
>      redirectUri: "Enter_the_Redirect_Uri_Here",
>    },
>    cache: {
>      cacheLocation: "sessionStorage", // This configures where your cache will be stored
>      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>    }
>  };
>
>```

> [!div renderon="portal" class="sxs-lookup"]
> > [!NOTE]
> > :::no-loc text="Enter_the_Supported_Account_Info_Here":::

> [!div renderon="docs"]
>
> 其中：
> - *\<Enter_the_Application_Id_Here>* 是您所註冊之應用程式的**應用程式 (用戶端) 識別碼**。
> - *\<Enter_the_Cloud_Instance_Id_Here>* 是 Azure 雲端的執行個體。 針對主要或全域 Azure 雲端，只要輸入 *https://login.microsoftonline.com/* 即可。 針對**國家**雲端 (例如中國)，請參閱[國家雲端](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud)。
> - *\<Enter_the_Tenant_info_here>* 設定為下列其中一個選項：
>    - 如果您的應用程式支援「此組織目錄中的帳戶」  ，請將此值取代為 [租用戶識別碼]  或 [租用戶名稱]  \(例如 *contoso.microsoft.com*\)。
>    - 如果您的應用程式支援「任何組織目錄中的帳戶」  ，請將此值取代為 [組織]  。
>    - 如果您的應用程式支援「任何組織目錄中的帳戶及個人的 Microsoft 帳戶」  ，請將此值取代為 [通用]  。 若要將支援範圍限制為「僅限個人 Microsoft 帳戶」  ，請將此值取代為 [取用者]  。
> - *\<Enter_the_Redirect_Uri_Here>* 為 `http://localhost:3000`
> > [!TIP]
> > 若要尋找 [應用程式 (用戶端) 識別碼]  、[目錄 (租用戶) 識別碼]  和 [支援的帳戶類型]  的值，請在 Azure 入口網站中移至應用程式註冊的 [概觀]  頁面。
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>步驟 3：您的應用程式已設定並準備好執行
> 我們已使用您的應用程式屬性值來設定您的專案。

> [!div renderon="docs"]
>
> 然後在相同的資料夾中，繼續編輯 *graphConfig.js* 檔案，以設定 `apiConfig` 物件的 `graphMeEndpoint` 和 `graphMailEndpoint`。
> ```javascript
>   // Add here the endpoints for MS Graph API services you would like to use.
>   const graphConfig = {
>     graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
>     graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
>   };
>
>   // Add here scopes for access token to be used at MS Graph API endpoints.
>   const tokenRequest = {
>       scopes: ["Mail.Read"]
>   };
> ```
>

> [!div renderon="docs"]
>
> *\<Enter_the_Graph_Endpoint_Here>* 是要對其進行 API 呼叫的端點。 針對主要或全域 Microsoft Graph API 服務，請輸入 `https://graph.microsoft.com`。 如需詳細資訊，請參閱[國家雲端部署](https://docs.microsoft.com/graph/deployments)。
>
> #### <a name="step-4-run-the-project"></a>步驟 4：執行專案

使用 [Node.js](https://nodejs.org/en/download/) 以網頁伺服器執行專案：

1. 若要啟動伺服器，請從專案目錄執行下列命令：
    ```bash
    npm install
    npm start
    ```
1. 瀏覽至 `http://localhost:3000/`。

1. 選取 [登入]  啟動登入程序，然後呼叫 Microsoft Graph API。

    第一次登入時，系統會提示您同意允許應用程式存取您的設定檔，並將您登入。 成功登入後，您的使用者設定檔資訊應該會顯示在頁面上。

## <a name="more-information"></a>詳細資訊

### <a name="how-the-sample-works"></a>此範例的運作方式

![JavaScript SPA 範例的運作方式：1. SPA 起始登入。 2. SPA 從 Microsoft 身分識別平台取得識別碼權杖。 3. SPA 呼叫取得權杖。 4. Microsoft 身分識別平台將存取權杖傳回給 SPA。 5. SPA 使用存取權杖向 Microsoft Graph API 提出 HTTP GET 要求。 6. 圖形 API 傳回 HTTP 回應給 SPA。](media/quickstart-v2-javascript/javascriptspa-intro.svg)

### <a name="msaljs"></a>msal.js

MSAL.js 程式庫會登入使用者並要求權杖，該權杖是用來存取受 Microsoft 身分識別平台保護的 API。 範例的 *index.html* 包含程式庫的參考：

```html
<script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.0/js/msal-browser.js" integrity=
"sha384-r7Qxfs6PYHyfoBR6zG62DGzptfLBxnREThAlcJyEfzJ4dq5rqExc1Xj3TPFE/9TH" crossorigin="anonymous"></script>
```
> [!TIP]
> 您可以使用 [MSAL.js 版本](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases) \(英文\) 下的最新發行版本取代上述版本。

或者，如果您已安裝 Node.js，可以利用 Node.js 套件管理員 (npm) 下載最新版本：

```batch
npm install @azure/msal-browser
```

## <a name="next-steps"></a>後續步驟

[MSAL.js GitHub 存放庫](https://github.com/AzureAD/microsoft-authentication-library-for-js)包含額外的程式庫文件、常見問題集，並提供問題支援。

如需建置本快速入門應用程式的詳細逐步指南，請參閱：

> [!div class="nextstepaction"]
> [登入和呼叫 MS Graph 的教學課程](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-javascript-auth-code)
