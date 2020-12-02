---
title: 快速入門：在 Angular 單頁應用程式中將使用者登入 - Azure
titleSuffix: Microsoft identity platform
description: 在本快速入門中，您會了解 Angular 應用程式如何呼叫一個 API，此 API 需要 Microsoft 身分識別平台所發出的存取權杖。
services: active-directory
author: jasonnutter
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript, devx-track-js
ms.topic: quickstart
ms.workload: identity
ms.date: 03/18/2020
ms.author: janutter
ms.openlocfilehash: 72d9aefcdda5d224b256c6e969b7a75259a97005
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95750768"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-an-angular-single-page-application"></a>快速入門：讓使用者登入並取得 Angular 單頁應用程式中的存取權杖

在本快速入門中，您會下載並執行程式碼範例，該範例會示範 Angular 單頁應用程式 (SPA) 如何登入使用者並呼叫 Microsoft Graph。 此程式碼範例會示範如何取得存取權杖來呼叫 Microsoft Graph API 或任何 Web API。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 [建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [Node.js](https://nodejs.org/en/download/).
* [Visual Studio Code](https://code.visualstudio.com/download) (用來編輯專案檔案) 或 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (用來執行專案)。

> [!div renderon="docs"]
> ## <a name="register-and-download-the-quickstart-app"></a>註冊並下載快速入門應用程式
> 若要啟動快速入門應用程式，請使用下列其中一個選項。
>
> ### <a name="option-1-express-register-and-automatically-configure-the-app-and-then-download-the-code-sample"></a>選項 1 (快速)：註冊和自動設定您的應用程式，然後下載程式碼範例
>
> 1. 登入 [Azure 入口網站](https://portal.azure.com)。
> 1. 如果您的帳戶可存取多個租用戶，請在右上方選取帳戶，然後將您的入口網站工作階段設定為想要使用的 Azure Active Directory (Azure AD) 租用戶。
> 1. 在 Azure 入口網站中開啟新的[應用程式註冊](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs)窗格。
> 1. 輸入應用程式的名稱，然後選取 [註冊]。
> 1. 移至快速入門窗格並檢視 Angular 快速入門。 依照指示來下載並自動設定新應用程式。
>
> ### <a name="option-2-manual-register-and-manually-configure-the-application-and-code-sample"></a>選項 2 (手動)：註冊並手動設定應用程式和程式碼範例
>
> #### <a name="step-1-register-the-application"></a>步驟 1:註冊應用程式
>
> 1. 登入 [Azure 入口網站](https://portal.azure.com)。
> 1. 如果您的帳戶可存取多個租用戶，請在右上方選取帳戶，然後將您的入口網站工作階段設定為想要使用的 Azure AD 租用戶。
> 1. 依照指示，在 Azure 入口網站中[註冊單頁應用程式](./scenario-spa-app-registration.md)。
> 1. 在應用程式註冊的 [驗證] 窗格中新增平台，然後註冊重新導向 URI：`http://localhost:4200/`。
> 1. 本快速入門會使用[隱含的授與流程](v2-oauth2-implicit-grant-flow.md)。 選取 [識別碼權杖] 和 [存取權杖] 的 [隱含授與] 設定。 因為此應用程式會將使用者登入並呼叫 API，所以識別碼權杖和存取權杖都是必要權杖。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-the-azure-portal"></a>步驟 1:在 Azure 入口網站中設定應用程式
> 若要讓此快速入門中的程式碼範例正常運作，您需要將重新導向 URI 新增為 **http://localhost:4200/** ，並且啟用 [隱含授與]。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [為我進行這些變更]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![已設定](media/quickstart-v2-javascript/green-check.png) 您的應用程式已設定了這些屬性。

#### <a name="step-2-download-the-code-sample"></a>步驟 2:下載程式碼範例
>[!div renderon="docs"]
>若要使用 Node.js 搭配網頁伺服器來執行專案，請[複製範例存放庫](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular)或[下載核心專案檔案](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip)。 使用 Visual Studio Code 等編輯器開啟檔案。

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [下載程式碼範例](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip)

> [!div renderon="docs"]
>#### <a name="step-3-configure-the-javascript-app"></a>步驟 3：設定 JavaScript 應用程式
>
> 在 *src/app* 資料夾中，編輯 *app.module.ts*，並在 `MsalModule.forRoot` 底下設定 `clientId` 和 `authority` 值。
>
>```javascript
>MsalModule.forRoot({
>    auth: {
>        clientId: 'Enter_the_Application_Id_here', // This is your client ID
>        authority: 'https://login.microsoftonline.com/Enter_the_Tenant_Info_Here', // This is your tenant info
>        redirectUri: 'Enter_the_Redirect_Uri_Here' // This is your redirect URI
>    },
>    cache: {
>        cacheLocation: 'localStorage',
>        storeAuthStateInCookie: isIE, // set to true for IE 11
>    },
>},
> //... )
>```

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Enter_the_Supported_Account_Info_Here


> [!div renderon="docs"]
>
> 取代下列值：
>
>|值名稱|描述|
>|---------|---------|
>|Enter_the_Application_Id_Here|在應用程式註冊的 [概觀] 頁面中，這是您的 **應用程式 (用戶端) 識別碼** 值。 |
>|Enter_the_Cloud_Instance_Id_Here|這是 Azure 雲端的執行個體。 針對主要或全域 Azure 雲端，請輸入 **https://login.microsoftonline.com** 。 針對國家端 (例如中國)，請參閱[國家雲端](./authentication-national-cloud.md)。|
>|Enter_the_Tenant_Info_Here| 設為下列其中一個選項：如果您的應用程式支援「此組織目錄中的帳戶」，請將此值取代為目錄 (租用戶) 識別碼或租用戶名稱 (例如 **contoso.microsoft.com**)。 如果您的應用程式支援「任何組織目錄中的帳戶」，請將此值取代為 [組織]。 如果您的應用程式支援「任何組織目錄中的帳戶及個人的 Microsoft 帳戶」，請將此值取代為 [通用]。 若要將支援範圍限制為「僅限個人 Microsoft 帳戶」，請將此值取代為 [取用者]。 |
>|Enter_the_Redirect_Uri_Here|以 **http://localhost:4200** 取代。|
>|cacheLocation  | (選用) 設定驗證狀態的瀏覽器儲存體。 預設值是 **sessionStorage**。   |
>|storeAuthStateInCookie  | (選用) 識別儲存驗證要求狀態的程式庫。 需要此狀態，才能驗證瀏覽器 cookie 中的驗證流程。 此 Cookie 會針對 Internet Explorer 和 Edge 進行設定，以容納這兩個瀏覽器。 如需詳細資料，請參閱[已知問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues->on-IE-and-Edge-Browser#issues)。 |
> > [!TIP]
> > 若要尋找 [應用程式 (用戶端) 識別碼]、[目錄 (租用戶) 識別碼] 和 [支援的帳戶類型] 的值，請在 Azure 入口網站中移至應用程式的 [概觀] 頁面。

如需可用之可設定選項的詳細資訊，請參閱[將用戶端應用程式初始化](msal-js-initializing-client-applications.md) \(英文\)。

您可以在 GitHub 上的 [AzureAD/microsoft-authentication-library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) 存放庫中找到適用於 MSAL.js 程式庫的原始程式碼。

>[!div class="sxs-lookup" renderon="portal"]
>#### <a name="step-3-run-the-project"></a>步驟 3：執行專案

>[!div renderon="docs"]
>#### <a name="step-4-run-the-project"></a>步驟 4：執行專案

如果您使用 Node.js：

1. 從專案目錄執行下列命令，以啟動伺服器：

   ```console
   npm install
   npm start
   ```

1. 瀏覽至 **http://localhost:4200/** 。
1. 選取 [登入]。
1. 選取 [設定檔] 以呼叫 Microsoft Graph。

在瀏覽器載入應用程式之後，請選取 [登入]。 第一次開始登入時，系統會提示您同意允許應用程式存取您的設定檔，並將您登入。 成功登入後，選取 [設定檔]，而您的使用者設定檔資訊會顯示在頁面上。

## <a name="how-the-sample-works"></a>此範例的運作方式

![此圖顯示本快速入門中範例應用程式的運作方式](./media/quickstart-v2-angular/diagram-auth-flow-spa-angular.svg)


## <a name="next-steps"></a>後續步驟

接下來，在 Angular 教學課程中了解如何讓使用者登入並取得權杖：

> [!div class="nextstepaction"]
> [Angular 教學課程](./tutorial-v2-angular.md)