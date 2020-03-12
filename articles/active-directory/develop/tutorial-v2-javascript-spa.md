---
title: JavaScript 單頁應用程式教學課程 - Microsoft 身分識別平台 | Azure
description: JavaScript SPA 應用程式如何呼叫需要來自 Azure Active Directory v2.0 端點存取權杖的 API
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 51a456a05e1d41057e80b92e44f997e1b52cb132
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129438"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>登入使用者並從 JavaScript 單頁應用程式 (SPA) 呼叫 Microsoft 圖形 API

本指南將示範 JavaScript 單頁應用程式 (SPA) 如何執行下列動作：
- 登入個人帳戶及公司和學校帳戶 
- 取得存取權杖
- 呼叫 Microsoft Graph API 或其他需要 Microsoft 身分識別平台端點中存取權杖的 API

>[!NOTE]
> 如果您是 Microsoft 身分識別平臺的新手，建議您先從登[入使用者開始，並在 JAVASCRIPT SPA 快速入門中取得存取權杖](quickstart-v2-javascript.md)。

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>本指南產生之範例應用程式的運作方式

![示範本教學課程所產生的應用程式範例如何運作](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>詳細資訊

此指南建立的範例應用程式可讓 JavaScript SPA 查詢 Microsoft Graph API，或查詢可接受來自 Microsoft 身分識別平台端點之權杖的 Web API。 在此案例中，當使用者登入之後，系統會透過授權標頭要求一個存取權杖，並將其新增到 HTTP 要求。 權杖取得和更新作業是由 Microsoft Authentication Library (MSAL) 處理。

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>程式庫

本指南使用下列程式庫：

|程式庫|描述|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Microsoft Authentication Library for JavaScript 預覽|

> [!NOTE]
> Msal.js 的目標是「Microsoft 身分識別平台」端點，其可讓個人帳戶、學校及公司帳戶登入和取得權杖。 Microsoft 身分識別平台端點有[一些限制](../azuread-dev/azure-ad-endpoint-comparison.md#limitations)。
> 若要了解 v1.0 與 v2.0 端點之間的差異，請參閱[端點比較指南](../azuread-dev/azure-ad-endpoint-comparison.md)。

<!--end-collapse-->

## <a name="set-up-your-web-server-or-project"></a>設定您的網頁伺服器或專案

> 想要改為下載此範例的專案嗎？ 執行下列任一步驟：
> 
> - 若要使用本機網頁伺服器 (例如 Node.js) 來執行專案，請[下載專案檔](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)。
>
> - (選擇性) 若要使用 Microsoft Internet Information Services (IIS) 伺服器來執行專案，[請下載 Visual Studio 專案](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)。
>
> 若要在您執行之前設定程式碼範例，請跳至[設定步驟](#register-your-application)。

## <a name="prerequisites"></a>Prerequisites

* 若要執行本教學課程，您需要本機網頁伺服器，例如 [Node.js](https://nodejs.org/en/download/)、[.NET Core](https://www.microsoft.com/net/core) 或與 [Visual Studio 2017](https://www.visualstudio.com/downloads/) 的 IIS Express 整合。

* 如果您使用 Node.js 來執行專案，請安裝整合式開發環境 (IDE)，例如 [Visual Studio Code](https://code.visualstudio.com/download)，以編輯專案檔。

* 本指南中的指示是以 Node.js 和 Visual Studio 2017 為基礎，但是您可以使用任何其他開發環境或網頁伺服器。

## <a name="create-your-project"></a>建立專案

> ### <a name="option-1-nodejs-or-other-web-servers"></a>選項1： node.js 或其他 web 伺服器
> 請確定您已安裝 [Node.js](https://nodejs.org/en/download/)，然後建立資料夾來裝載您的應用程式。
>
> ### <a name="option-2-visual-studio"></a>選項 2：Visual Studio
> 如果您使用 Visual Studio 並且建立新專案，請遵循下列步驟：
> 1. 在 Visual Studio 中，選取 [檔案] >  [新增] >  [專案]。
> 1. 在 [Visual C#\Web] 底下，選取 [ASP.NET Web 應用程式 (.NET Framework)]。
> 1. 輸入應用程式的名稱，然後選取 [確定]。
> 1. 在 [新增 ASP.NET Web 應用程式] 底下，選取 [空白]。

## <a name="create-the-spa-ui"></a>建立 SPA UI
1. 為您的 JavaScript SPA 建立 *index.html* 檔案。 如果您使用 Visual Studio，請選取專案 (專案根資料夾)。 以滑鼠右鍵按一下並選取 [新增] > [新增項目] > [HTML 頁面]，並將檔案命名為 index.html。

1. 在 index.html 檔案中，新增下列程式碼：

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>Quickstart for MSAL JS</title>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
       <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
   </head>
   <body>
       <h2>Welcome to MSAL.js Quickstart</h2><br/>
       <h4 id="WelcomeMessage"></h4>
       <button id="SignIn" onclick="signIn()">Sign In</button><br/><br/>
       <pre id="json"></pre>
       <script>
           //JS code
       </script>
   </body>
   </html>
   ```

   > [!TIP]
   > 您可以使用 [MSAL.js 版本](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases) 下的最新發行版本取代上述指令碼中的 MSAL.js 版本。

## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>使用 Microsoft Authentication Library (MSAL) 登入使用者

將下列程式碼新增至 `index.html` 檔案的 `<script></script>` 標記內：

   ```JavaScript
   var msalConfig = {
       auth: {
           clientId: "Enter_the_Application_Id_here",
           authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here"
       },
       cache: {
           cacheLocation: "localStorage",
           storeAuthStateInCookie: true
       }
   };

   var graphConfig = {
       graphMeEndpoint: "https://graph.microsoft.com/v1.0/me"
   };

   // this can be used for login or token request, however in more complex situations
   // this can have diverging options
   var requestObj = {
        scopes: ["user.read"]
   };

   var myMSALObj = new Msal.UserAgentApplication(msalConfig);
   // Register Callbacks for redirect flow
   myMSALObj.handleRedirectCallback(authRedirectCallBack);


   function signIn() {

       myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
           //Login Success
           showWelcomeMessage();
           acquireTokenPopupAndCallMSGraph();
       }).catch(function (error) {
           console.log(error);
       });
   }

   function acquireTokenPopupAndCallMSGraph() {
       //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
       myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
            callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
       }).catch(function (error) {
            console.log(error);
            // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
            // Call acquireTokenPopup(popup window)
            if (requiresInteraction(error.errorCode)) {
                myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
                    callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
                }).catch(function (error) {
                    console.log(error);
                });
            }
       });
   }


   function graphAPICallback(data) {
       document.getElementById("json").innerHTML = JSON.stringify(data, null, 2);
   }


   function showWelcomeMessage() {
       var divWelcome = document.getElementById('WelcomeMessage');
       divWelcome.innerHTML = 'Welcome ' + myMSALObj.getAccount().userName + "to Microsoft Graph API";
       var loginbutton = document.getElementById('SignIn');
       loginbutton.innerHTML = 'Sign Out';
       loginbutton.setAttribute('onclick', 'signOut();');
   }


   //This function can be removed if you do not need to support IE
   function acquireTokenRedirectAndCallMSGraph() {
        //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
        myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
            callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
        }).catch(function (error) {
            console.log(error);
            // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
            // Call acquireTokenRedirect
            if (requiresInteraction(error.errorCode)) {
                myMSALObj.acquireTokenRedirect(requestObj);
            }
        });
   }


   function authRedirectCallBack(error, response) {
       if (error) {
           console.log(error);
       }
       else {
           if (response.tokenType === "access_token") {
               callMSGraph(graphConfig.graphEndpoint, response.accessToken, graphAPICallback);
           } else {
               console.log("token type is:" + response.tokenType);
           }
       }
   }

   function requiresInteraction(errorCode) {
       if (!errorCode || !errorCode.length) {
           return false;
       }
       return errorCode === "consent_required" ||
           errorCode === "interaction_required" ||
           errorCode === "login_required";
   }

   // Browser check variables
   var ua = window.navigator.userAgent;
   var msie = ua.indexOf('MSIE ');
   var msie11 = ua.indexOf('Trident/');
   var msedge = ua.indexOf('Edge/');
   var isIE = msie > 0 || msie11 > 0;
   var isEdge = msedge > 0;
   //If you support IE, our recommendation is that you sign-in using Redirect APIs
   //If you as a developer are testing using Edge InPrivate mode, please add "isEdge" to the if check
   // can change this to default an experience outside browser use
   var loginType = isIE ? "REDIRECT" : "POPUP";

   if (loginType === 'POPUP') {
        if (myMSALObj.getAccount()) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenPopupAndCallMSGraph();
        }
   }
   else if (loginType === 'REDIRECT') {
       document.getElementById("SignIn").onclick = function () {
            myMSALObj.loginRedirect(requestObj);
       };
       if (myMSALObj.getAccount() && !myMSALObj.isCallback(window.location.hash)) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenRedirectAndCallMSGraph();
        }
   } else {
       console.error('Please set a valid login type');
   }
   ```

<!--start-collapse-->
### <a name="more-information"></a>詳細資訊

在使用者第一次選取 [登入] 按鈕之後，`signIn` 方法會呼叫 `loginPopup` 以將使用者登入。 這個方法會開啟「Microsoft 身分識別平台端點」的快顯視窗，以提示及驗證使用者的認證。 成功登入之後，使用者會重新導回到原本的 index.html 頁面。 系統會收到權杖 (由 `msal.js` 進行處理)，並快取權杖中包含的資訊。 此權杖也稱為「ID 權杖」且包含使用者的基本資訊，例如使用者顯示名稱。 如果您打算將此權杖所提供的任何資料用於任何目的，您必須確定後端伺服器已驗證此權杖，以保證權杖是發給您應用程式的有效使用者。

本指南所產生的 SPA 會呼叫 `acquireTokenSilent` 和/或 `acquireTokenPopup`，以取得用來查詢 Microsoft Graph API 中使用者設定檔資訊的「存取權杖」。 如果您需要可驗證 ID 權杖的範例，請參閱 GitHub 中的[這個](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "GitHub active-directory-javascript-singlepageapp-dotnet-webapi-v2 範例")範例應用程式。 此範例使用 ASP.NET Web API 進行權杖驗證。

#### <a name="getting-a-user-token-interactively"></a>以互動方式取得使用者權杖

初次登入之後，您不希望在使用者每次必須要求權杖來存取資源時，都要求使用者重新驗證。 因此，通常應該使用 acquireTokenSilent 以取得權杖。 但是，您有可能必須強制使用者與 Microsoft 身分識別平台端點互動。 範例包括：

- 使用者需要重新輸入其認證，因為密碼已過期。
- 您的應用程式要求資源存取權，而您需要使用者同意。
- 需要雙因素驗證。

呼叫 acquireTokenPopup 開啟快顯視窗 (或呼叫 acquireTokenRedirect 將使用者重新導向至 Microsoft 身分識別平台端點)。 在該視窗中，使用者必須藉由確認其認證、同意必要的資源，或完成雙因素驗證來進行互動。

#### <a name="getting-a-user-token-silently"></a>以無訊息方式取得使用者權杖

`acquireTokenSilent` 方法會處理權杖取得和更新作業，不需要與使用者進行任何互動。 在第一次執行 `loginPopup` (或 `loginRedirect`) 之後，`acquireTokenSilent` 就會成為用來取得權杖的常用方法，以在後續呼叫中使用那些權杖存取受保護的資源。 (以無訊息方式進行要求或更新權杖的呼叫。) `acquireTokenSilent` 可能會因為某些狀況而失敗。 例如，使用者的密碼可能已過期。 您的應用程式可以透過兩種方式處理此例外狀況：

1. 立即對 `acquireTokenPopup` 進行呼叫，這會觸發使用者登入提示。 此模式通常用於應用程式中沒有未經驗證內容可供使用者使用的線上應用程式。 此引導式設定所產生的範例應用程式會使用此模式。

2. 應用程式也可以提供視覺指示，讓使用者知道需要透過互動方式登入，使用者就能選取正確的登入時機，或應用程式可以在之後重試 `acquireTokenSilent`。 這是相當常用的方式，因為使用者可以在不中斷的情況下使用應用程式的其他功能。 例如，應用程式中可能會有未驗證的內容。 在此情況下，使用者可以決定何時登入以存取受保護的資源，或者重新整理過期的資訊。

> [!NOTE]
> 本快速入門使用 Internet Explorer 瀏覽器適用的 `loginRedirect` 和 `acquireTokenRedirect` 方法。 我們之所以遵循這種作法，是因為 Internet Explorer 處理快顯視窗的方式有一個[已知問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues)。
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>使用您剛剛取得的權杖呼叫 Microsoft Graph API

將下列程式碼新增至 `index.html` 檔案的 `<script></script>` 標記內：

```javascript
function callMSGraph(theUrl, accessToken, callback) {
    var xmlHttp = new XMLHttpRequest();
    xmlHttp.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200)
            callback(JSON.parse(this.responseText));
    }
    xmlHttp.open("GET", theUrl, true); // true for asynchronous
    xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
    xmlHttp.send();
}
```
<!--start-collapse-->

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>針對受保護 API 進行 REST 呼叫的相關詳細資訊

在使用此指南建立的範例應用程式中，使用 `callMSGraph()` 方法對需要權杖的受保護資源提出 HTTP `GET` 要求。 然後，該要求會將內容傳回給呼叫端。 此方法會在「HTTP 授權標頭」中加入取得的權杖。 對於本指南建立的範例應用程式，資源為 Microsoft Graph API 的 me 端點，其會顯示使用者的設定檔資訊。

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>新增方法來將使用者登出

將下列程式碼新增至 `index.html` 檔案的 `<script></script>` 標記內：

```javascript
/**
 * Sign out the user
 */
 function signOut() {
     myMSALObj.logout();
 }
```

## <a name="register-your-application"></a>註冊您的應用程式

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 如果您的帳戶可讓您存取多個租用戶，請在右上方選取帳戶，然後將您的入口網站工作階段設定為想要使用的 Azure AD 租用戶。
1. 移至 Microsoft 身分識別平台，以取得開發人員的[應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)頁面。
1. [註冊應用程式] 頁面出現時，輸入您應用程式的名稱。
1. 在 [支援的帳戶類型] 底下，選取 [任何組織目錄中的帳戶及個人的 Microsoft 帳戶]。
1. 在 [重新導向 URI] 區段中，從下拉式清單中選取 [Web] 平台，然後根據您的網頁伺服器，將值設定為應用程式的 URL。

   若要了解如何設定及取得適用於 Node.js 和 Visual Studio 的重新導向 URL，請參閱下列「設定適用於 Node.js 的重新導向 URL」一節，以及[設定適用於 Visual Studio 的重新導向 URL](#set-a-redirect-url-for-visual-studio)。

1. 選取 [註冊]。
1. 在應用程式 [概觀] 頁面上，記下 [應用程式 (用戶端) 識別碼] 值以供稍後使用。
1. 本快速入門需要啟用[隱含授與流程](v2-oauth2-implicit-grant-flow.md)。 在所註冊應用程式的左側窗格中，選取 [驗證]。
1. 在 [進階設定] 的 [隱含授與] 底下，選取 [識別碼權杖] 和 [存取權杖] 核取方塊。 因為此應用程式必須將使用者登入並呼叫 API，所以識別碼權杖和存取權杖都是必要權杖。
1. 選取 [儲存]。

> #### <a name="set-a-redirect-url-for-nodejs"></a>設定 Node.js 的重新導向 URL
> 對於 Node.js，您可以在 server.js 檔案中設定網頁伺服器連接埠。 此教學課程會使用連接埠 30662，但您可以使用任何其他可用的連接埠。
>
> 若要在應用程式註冊資訊中設定重新導向 URL，請切換回 [應用程式註冊] 窗格，並且執行下列其中一項作業：
>
> - 將 *`http://localhost:30662/`* 設定為 [重新導向 URL]。
> - 如果您使用自訂 TCP 連接埠，請使用 *`http://localhost:<port>/`* (其中 *port>\<* 是自訂 TCP 連接埠號碼)。
>
> #### <a name="set-a-redirect-url-for-visual-studio"></a>設定 Visual Studio 的重新導向 URL
> 若要取得適用於 Visual Studio 的重新導向 URL，請遵循下列步驟：
> 1. 在 [方案總管] 中選取專案。
>
>    [屬性] 視窗隨即開啟。 如果未開啟，請按下 F4。
>
>    ![[JavaScriptSPA 專案屬性] 視窗](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)
>
> 1. 複製 [URL] 值。
> 1. 切換回 [應用程式註冊] 窗格，並貼上複製的值作為 [重新導向 URL]。

#### <a name="configure-your-javascript-spa"></a>設定您的 JavaScript SPA

1. 在於專案設定期間建立的 index.html 檔案中，新增應用程式註冊資訊。 在檔案頂端的 `<script></script>` 標記內，新增下列程式碼：

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "<Enter_the_Application_Id_here>",
            authority: "https://login.microsoftonline.com/<Enter_the_Tenant_info_here>"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    其中：
    - *\<Enter_the_Application_Id_here>* 是您所註冊應用程式的**應用程式 (用戶端) 識別碼**。
    - *\<Enter_the_Tenant_info_here>* 設定為下列其中一個選項：
       - 如果您的應用程式支援 [此組織目錄中的帳戶]，請將此值取代為 [租用戶識別碼] 或 [租用戶名稱] (例如，contoso.microsoft.com)。
       - 如果您的應用程式支援 [任何組織目錄中的帳戶]，請將此值取代為 [組織]。
       - 如果您的應用程式支援 [任何組織目錄中的帳戶及個人的 Microsoft 帳戶]，請將此值取代為 [通用]。 若要將支援範圍限制為 [僅限個人 Microsoft 帳戶]，請將此值取代為 [取用者]。

## <a name="test-your-code"></a>測試您的程式碼

藉由使用下列其中一個環境，來測試您的程式碼。

### <a name="test-with-nodejs"></a>以 Node.js 進行測試

如果您不是使用 Visual Studio，請確定您的 web 伺服器已啟動。

1. 將伺服器設定為接聽以您 *index.html* 檔案位置為基礎的 TCP 通訊埠。 對於 Node.js，請從應用程式資料夾在命令提示字元中執行命令，以啟動網頁伺服器來接聽此連接埠：

    ```bash
    npm install
    node server.js
    ```
1. 在您的瀏覽器中，輸入 **http://\<span>\</span>localhost:30662** or **http://\<span>\</span>localhost:{port}** ，其中 port 是您的網頁伺服器正在接聽的連接埠。 您應該會看到 index.html 檔案和 [登入] 按鈕。

### <a name="test-with-visual-studio"></a>使用 Visual Studio 進行測試

如果您使用 Visual Studio，請選取專案解決方案並且按 F5 來執行專案。 瀏覽器會開啟至 http://<span></span>localhost:{port} 位置，而且您應該會看到 [登入] 按鈕。

## <a name="test-your-application"></a>測試您的應用程式

在瀏覽器載入您的 index.html 檔案之後，請選取 [登入]。 系統會提示您使用 Microsoft 身分識別平台端點登入：

![JavaScript SPA 帳戶登入視窗](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>同意應用程式存取

您第一次登入應用程式時，系統會提示您授與您設定檔的存取權，並且將您登入：

![[要求的權限] 視窗](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>檢視應用程式結果

登入之後，系統會顯示以 Microsoft Graph API 回應傳回的使用者設定檔資訊：

![Microsoft Graph API 呼叫的結果](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>與範圍和委派的權限有關的詳細資訊

Microsoft Graph API 需要 user.read 範圍才能讀取使用者的設定檔。 根據預設，在註冊入口網站上註冊的每個應用程式中，都會自動新增此範圍。 Microsoft Graph 的其他 API 與您後端伺服器的自訂 API 一樣，需要其他範圍。 例如，Microsoft Graph API 需要 Calendars.Read 範圍才能列出使用者的行事曆。

為了在應用程式內容中存取使用者的行事曆，請將 Calendars.Read 委派權限新增至應用程式註冊資訊。 接著，將 Calendars.Read 範圍新增至 `acquireTokenSilent` 呼叫。

>[!NOTE]
>系統可能會在您增加範圍數目時，提示使用者同意其他事項。

如果後端 API 不需要範圍 (不建議)，您可以在呼叫中使用 clientId 作為範圍以取得權杖。

<!--end-collapse-->

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

協助我們改善 Microsoft 身分識別平台。 完成問卷調查簡短的兩個問題，告訴我們您的想法。

> [!div class="nextstepaction"]
> [Microsoft 身分識別平台問卷調查](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
