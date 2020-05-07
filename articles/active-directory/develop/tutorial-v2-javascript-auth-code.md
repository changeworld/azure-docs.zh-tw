---
title: JavaScript 單頁應用程式教學課程 2.0 - Microsoft 身分識別平台 | Azure
description: JavaScript SPA 應用程式如何使用授權碼流程來呼叫需要來自 Azure Active Directory v2.0 端點存取權杖的 API
services: active-directory
documentationcenter: dev-center-name
author: hahamil
manager: CelesteDG
ROBOTS: NOINDEX
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 04/22/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: ee156c8118a67061d0a000867ee64fe1f3ebd18c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82181880"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa---msaljs-20"></a>登入使用者並從 JavaScript 單頁應用程式 (SPA) 呼叫 Microsoft 圖形 API - MSAL.js 2.0

> [!IMPORTANT]
> 此功能目前為預覽狀態。 若您同意[補充的使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

本教學課程使用的 MSAL.js 版本會使用 OAuth 2.0 授權碼流程搭配 PKCE。 若要深入了解此通訊協定，以及隱含流程與授權碼流程之間的差異，請參閱[文件](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow)。 如果您要尋找使用隱含流程的教學課程，請參閱 [MSAL.js v1 教學課程](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-javascript-spa)。

這個版本的 MSAL.js 會在目前的 MSAL 核心程式庫上進行改善，並在瀏覽器中使用授權碼流程。 此版本會提供舊程式庫中可用的大部分功能，但兩者的驗證流程有細微差異。 這個版本**不**支援隱含流程。

本指南將示範 JavaScript 單頁應用程式 (SPA) 如何執行下列動作：
- 登入個人帳戶及公司和學校帳戶
- 取得存取權杖
- 呼叫 Microsoft Graph API 或其他需要 Microsoft 身分識別平台端點  中存取權杖的 API

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>本指南產生之範例應用程式的運作方式

![示範本教學課程所產生的應用程式範例如何運作](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

### <a name="more-information"></a>詳細資訊

此指南建立的範例應用程式可讓 JavaScript SPA 查詢 Microsoft Graph API，或查詢可接受來自 Microsoft 身分識別平台端點之權杖的 Web API。 在此案例中，當使用者登入之後，系統會透過授權標頭要求一個存取權杖，並將其新增到 HTTP 要求。 權杖取得和更新作業是由 Microsoft Authentication Library (MSAL) 處理。

### <a name="libraries"></a>程式庫

本指南使用下列程式庫：

|程式庫|描述|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)|適用於 JavaScript 瀏覽器套件的 Microsoft 驗證程式庫|

## <a name="set-up-your-web-server-or-project"></a>設定您的網頁伺服器或專案

想要改為下載此範例的專案嗎？ 若要使用本機網頁伺服器 (例如 Node.js) 來執行專案，請複製專案檔：

`git clone https://github.com/Azure-Samples/ms-identity-javascript-v2`

若要在您執行之前設定程式碼範例，請跳至[設定步驟](#register-your-application)。

## <a name="prerequisites"></a>Prerequisites

* 若要執行本教學課程，您需要本機網頁伺服器，例如 [Node.js](https://nodejs.org/en/download/) 或 [.NET Core](https://www.microsoft.com/net/core)。

* 如果您使用 Node.js 來執行專案，請安裝整合式開發環境 (IDE)，例如 [Visual Studio Code](https://code.visualstudio.com/download)，以編輯專案檔。

* 本教學課程中的指示是以 Node.js 作為基礎。

## <a name="create-your-project"></a>建立專案

請確定您已安裝 [Node.js](https://nodejs.org/en/download/)，然後建立資料夾來裝載您的應用程式。 接下來，請實作小型的 [Express](https://expressjs.com/) 網頁伺服器來提供您的 `index.html` 檔案。

1. 首先，瀏覽至終端機中的專案資料夾，然後執行下列 NPM 命令。
    ```console
    npm init -y
    npm install @azure/msal-Browser
    npm install express
    ```
2. 接下來，建立名為 *server.js* 的 .js 檔案，然後新增下列程式碼：

   ```JavaScript
   const express = require('express');
   const morgan = require('morgan');
   const path = require('path');
   const argv = require('yargs')
      .usage('Usage: $0 -p [PORT]')
      .alias('p', 'port')
      .describe('port', '(Optional) Port Number - default is 3000')
      .strict()
      .argv;

   const DEFAULT_PORT = 3000;

   //initialize express.
   const app = express();

   // Initialize variables.
   let port = DEFAULT_PORT; // -p {PORT} || 3000;
   if (argv.p) {
      port = argv.p;
   }

   // Configure morgan module to log all requests.
   app.use(morgan('dev'));

   // Set the front-end folder to serve public assets.
   app.use("/lib", express.static(path.join(__dirname, "../../lib/msal-browser/lib")));

   // Setup app folders
   app.use(express.static('app'));

   // Set up a route for index.html.
   app.get('*', function (req, res) {
      res.sendFile(path.join(__dirname + '/index.html'));
   });

   // Start the server.
   app.listen(port);
   console.log(`Listening on port ${port}...`);
    ```

現在，您擁有一個簡單的伺服器來提供 SPA。 本教學課程結尾處應該會有的資料夾結構如下所示：

![應該會有的 SPA 資料夾結構文字說明](./media/tutorial-v2-javascript-spa/single-page-application-folder-structure.png)

## <a name="create-the-spa-ui"></a>建立 SPA UI

1. 在 *app* 資料夾中，為您的 JavaScript SPA 建立 *index.html* 檔案。 此檔案會實作以 **Bootstrap 4 Framework** 建置的 UI，並匯入用於設定、驗證和 API 呼叫的指令碼檔案。

   在 index.html  檔案中，新增下列程式碼：

   ```html
   <!DOCTYPE html>
   <html lang="en">
      <head>
         <meta charset="UTF-8">
         <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
         <title>Quickstart | MSAL.JS Vanilla JavaScript SPA</title>

         <!-- IE support: add promises polyfill before msal.js  -->
         <script type="text/javascript" src="//cdn.jsdelivr.net/npm/bluebird@3.7.2/js/browser/bluebird.min.js"></script>
         <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.0/js/msal-browser.js"></script>

         <!-- adding Bootstrap 4 for UI components  -->
         <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
         <link rel="SHORTCUT ICON" href="https://c.s-microsoft.com/favicon.ico?v2" type="image/x-icon">
      </head>
      <body>
         <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
            <a class="navbar-brand" href="/">MS Identity Platform</a>
            <div class="btn-group ml-auto dropleft">
               <button type="button" id="SignIn" class="btn btn-secondary" onclick="signIn()">
                  Sign In
               </button>
            </div>
         </nav>
         <br>
         <h5 class="card-header text-center">Vanilla JavaScript SPA calling MS Graph API with MSAL.JS</h5>
         <br>
         <div class="row" style="margin:auto" >
         <div id="card-div" class="col-md-3" style="display:none">
         <div class="card text-center">
            <div class="card-body">
            <h5 class="card-title" id="WelcomeMessage">Please sign-in to see your profile and read your mails</h5>
            <div id="profile-div"></div>
            <br>
            <br>
            <button class="btn btn-primary" id="seeProfile" onclick="seeProfile()">See Profile</button>
            <br>
            <br>
            <button class="btn btn-primary" id="readMail" onclick="readMail()">Read Mails</button>
            </div>
         </div>
         </div>
         <br>
         <br>
            <div class="col-md-4">
            <div class="list-group" id="list-tab" role="tablist">
            </div>
            </div>
            <div class="col-md-5">
             <div class="tab-content" id="nav-tabContent">
            </div>
            </div>
         </div>
         <br>
         <br>

         <!-- importing bootstrap.js and supporting js libraries -->
         <script src="https://code.jquery.com/jquery-3.4.1.slim.min.js" integrity="sha384-J6qa4849blE2+poT4WnyKhv5vZF5SrPo0iEjwBvKU7imGFAV0wwj1yYfoRSJoZ+n" crossorigin="anonymous"></script>
         <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script>
         <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js" integrity="sha384-wfSDF2E50Y2D1uUdj0O3uMBJnjuUD4Ih7YwaYd1iqfktj0Uod8GCExl3Og8ifwB6" crossorigin="anonymous"></script>

         <!-- importing app scripts (load order is important) -->
         <script type="text/javascript" src="./authConfig.js"></script>
         <script type="text/javascript" src="./graphConfig.js"></script>
         <script type="text/javascript" src="./ui.js"></script>

         <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
         <!-- uncomment the above line and comment the line below if you would like to use the redirect flow -->
         <script type="text/javascript" src="./authPopup.js"></script>
         <script type="text/javascript" src="./graph.js"></script>
      </body>
     </html>
     ```

   > [!TIP]
   > 您可以使用 [MSAL.js 版本](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases) 下的最新發行版本取代上述指令碼中的 MSAL.js 版本。


2. 現在，建立名為 *ui.js* 的 .js 檔案以便存取和更新 DOM 元素，然後新增下列程式碼：

   ```JavaScript
   // Select DOM elements to work with
   const welcomeDiv = document.getElementById("welcomeMessage");
   const signInButton = document.getElementById("signIn");
   const signOutButton = document.getElementById('signOut');
   const cardDiv = document.getElementById("card-div");
   const mailButton = document.getElementById("readMail");
   const profileButton = document.getElementById("seeProfile");
   const profileDiv = document.getElementById("profile-div");

   function showWelcomeMessage(account) {
     // Reconfiguring DOM elements
     cardDiv.classList.remove('d-none');
     welcomeDiv.innerHTML = `Welcome ${account.name}`;
     signInButton.classList.add('d-none');
     signOutButton.classList.remove('d-none');
   }

   function updateUI(data, endpoint) {
     console.log('Graph API responded at: ' + new Date().toString());

     if (endpoint === graphConfig.graphMeEndpoint) {
       const title = document.createElement('p');
       title.innerHTML = "<strong>Title: </strong>" + data.jobTitle;
       const email = document.createElement('p');
       email.innerHTML = "<strong>Mail: </strong>" + data.mail;
       const phone = document.createElement('p');
       phone.innerHTML = "<strong>Phone: </strong>" + data.businessPhones[0];
       const address = document.createElement('p');
       address.innerHTML = "<strong>Location: </strong>" + data.officeLocation;
       profileDiv.appendChild(title);
       profileDiv.appendChild(email);
       profileDiv.appendChild(phone);
       profileDiv.appendChild(address);

     } else if (endpoint === graphConfig.graphMailEndpoint) {
         if (data.value.length < 1) {
           alert("Your mailbox is empty!")
         } else {
           const tabList = document.getElementById("list-tab");
           tabList.innerHTML = ''; // clear tabList at each readMail call
           const tabContent = document.getElementById("nav-tabContent");

           data.value.map((d, i) => {
             // Keeping it simple
             if (i < 10) {
               const listItem = document.createElement("a");
               listItem.setAttribute("class", "list-group-item list-group-item-action")
               listItem.setAttribute("id", "list" + i + "list")
               listItem.setAttribute("data-toggle", "list")
               listItem.setAttribute("href", "#list" + i)
               listItem.setAttribute("role", "tab")
               listItem.setAttribute("aria-controls", i)
               listItem.innerHTML = d.subject;
               tabList.appendChild(listItem)

               const contentItem = document.createElement("div");
               contentItem.setAttribute("class", "tab-pane fade")
               contentItem.setAttribute("id", "list" + i)
               contentItem.setAttribute("role", "tabpanel")
               contentItem.setAttribute("aria-labelledby", "list" + i + "list")
               contentItem.innerHTML = "<strong> from: " + d.from.emailAddress.address + "</strong><br><br>" + d.bodyPreview + "...";
               tabContent.appendChild(contentItem);
             }
           });
         }
     }
   }
   ```

## <a name="register-your-application"></a>註冊您的應用程式

遵循指示來[註冊新的單頁應用程式](https://docs.microsoft.com/zure/active-directory/develop/scenario-spa-app-registration)。

#### <a name="set-a-redirect-url-for-nodejs"></a>設定 Node.js 的重新導向 URL

對於 Node.js，您可以在 server.js  檔案中設定網頁伺服器連接埠。 此教學課程會使用連接埠 3000，但您可以使用任何其他可用的連接埠。

若要在應用程式註冊資訊中設定重新導向 URL，請切換回 [應用程式註冊]  窗格，並使用下列其中一種方法註冊新的 **SPA**：

- 將 `http://localhost:3000/` 設定為 [重新導向 URL]  。
- 如果您使用自訂 TCP 連接埠，請使用 `http://localhost:<port>/` (其中 \<port>  是自訂 TCP 連接埠號碼)。

### <a name="configure-your-javascript-spa"></a>設定您的 JavaScript SPA

建立名為 *authConfig.js* 的新 .js 檔案以包含用於驗證的設定參數，然後新增下列程式碼：

```javascript
  const msalConfig = {
    auth: {
      clientId: "Enter_the_Application_Id_Here",
      authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
      redirectUri: "Enter_the_Redirect_Uri_Here",
    },
    cache: {
      cacheLocation: "sessionStorage", // This configures where your cache will be stored
      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
    }
  };

  // Add here scopes for id token to be used at MS Identity Platform endpoints.
  const loginRequest = {
   scopes: ["openid", "profile", "User.Read"]
  };

   // Add here scopes for access token to be used at MS Graph API endpoints.
  const tokenRequest = {
   scopes: ["User.Read", "Mail.Read"]
  };

```

 其中：
 - *\<Enter_the_Application_Id_Here>* 是您所註冊之應用程式的**應用程式 (用戶端) 識別碼**。
 - *\<Enter_the_Cloud_Instance_Id_Here>* 是 Azure 雲端的執行個體。 針對主要或全域 Azure 雲端，只要輸入 *https://login.microsoftonline.com* 即可。 針對**國家**雲端 (例如中國)，請參閱[國家雲端](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud)。
 - *\<Enter_the_Tenant_info_here>* 設定為下列其中一個選項：
   - 如果您的應用程式支援「此組織目錄中的帳戶」  ，請將此值取代為 [租用戶識別碼]  或 [租用戶名稱]  \(例如 *contoso.microsoft.com*\)。
   - 如果您的應用程式支援「任何組織目錄中的帳戶」  ，請將此值取代為 [組織]  。
   - 如果您的應用程式支援「任何組織目錄中的帳戶及個人的 Microsoft 帳戶」  ，請將此值取代為 [通用]  。 若要將支援範圍限制為「僅限個人 Microsoft 帳戶」  ，請將此值取代為 [取用者]  。
- *\Enter_the_Redirect_Uri_Here>* 是您在入口網站中所註冊的連接埠 ( *`http://localhost:3000/`* )


建立名為 `graphConfig.js` 的新 .js 檔案以包含會呼叫 Microsoft Graph API 的設定參數，然後新增下列程式碼：
```javascript
// Add here the endpoints for MS Graph API services you would like to use.
const graphConfig = {
    graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
    graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
};
```
- *\<Enter_the_Graph_Endpoint_Here>* 是 MS 圖形 API 的執行個體。 若為全域 MS 圖形 API 端點，只要將此字串取代為 `https://graph.microsoft.com` 即可。 若為國家雲端部署，請參閱[圖形 API 文件](https://docs.microsoft.com/graph/deployments)。

## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>使用 Microsoft Authentication Library (MSAL) 登入使用者

### <a name="popup"></a>PopUp
建立名為 `authPopup.js` 的新 .js 檔案以包含用於登入快顯的驗證和權杖取得邏輯，然後新增下列程式碼：

   ```JavaScript
  // Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

function signIn() {
    myMSALObj.loginPopup(loginRequest)
        .then(loginResponse => {
            console.log('id_token acquired at: ' + new Date().toString());

            if (myMSALObj.getAccount()) {
                showWelcomeMessage(myMSALObj.getAccount());
            }
        }).catch(error => {
            console.error(error);
        });
}

function signOut() {
    myMSALObj.logout();
}

function getTokenPopup(request) {
    return myMSALObj.acquireTokenSilent(request)
        .catch(error => {
            console.warn(error);
            console.warn("silent token acquisition fails. acquiring token using popup");

            // fallback to interaction when silent call fails
            return myMSALObj.acquireTokenPopup(request)
                .then(tokenResponse => {
                    return tokenResponse;
                }).catch(error => {
                    console.error(error);
                });
        });
}

function seeProfile() {
    if (myMSALObj.getAccount()) {
        getTokenPopup(loginRequest)
            .then(response => {
                callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
                profileButton.classList.add('d-none');
                mailButton.classList.remove('d-none');
            }).catch(error => {
                console.error(error);
            });
    }
}

function readMail() {
    if (myMSALObj.getAccount()) {
        getTokenPopup(tokenRequest)
            .then(response => {
                callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
            }).catch(error => {
                console.error(error);
            });
    }
}
   ```
### <a name="redirect"></a>重新導向
建立名為 `authRedirect.js` 的新 .js 檔案以包含用於登入重新導向的驗證和權杖取得邏輯，然後新增下列程式碼：

```javascript
// Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

let accessToken;

// Register Callbacks for Redirect flow
myMSALObj.handleRedirectCallback(authRedirectCallBack);

function authRedirectCallBack(error, response) {
    if (error) {
        console.error(error);
    } else {
        if (myMSALObj.getAccount()) {
            console.log('id_token acquired at: ' + new Date().toString());
            showWelcomeMessage(myMSALObj.getAccount());
            getTokenRedirect(loginRequest);
        } else if (response.tokenType === "Bearer") {
            console.log('access_token acquired at: ' + new Date().toString());
        } else {
            console.log("token type is:" + response.tokenType);
        }
    }
}

// Redirect: once login is successful and redirects with tokens, call Graph API
if (myMSALObj.getAccount()) {
    showWelcomeMessage(myMSALObj.getAccount());
}

function signIn() {
    myMSALObj.loginRedirect(loginRequest);
}

function signOut() {
    myMSALObj.logout();
}

// This function can be removed if you do not need to support IE
function getTokenRedirect(request) {
    return myMSALObj.acquireTokenSilent(request)
        .then((response) => {
            console.log(response);
            if (response.accessToken) {
                console.log('access_token acquired at: ' + new Date().toString());
                accessToken = response.accessToken;

                callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
                profileButton.style.display = 'none';
                mailButton.style.display = 'initial';
            }
        })
        .catch(error => {
            console.warn("silent token acquisition fails. acquiring token using redirect");
            // fallback to interaction when silent call fails
            return myMSALObj.acquireTokenRedirect(request);
        });
}

function seeProfile() {
    getTokenRedirect(loginRequest);
}

function readMail() {
    getTokenRedirect(tokenRequest);
}
```

### <a name="more-information"></a>詳細資訊

在使用者第一次選取 [登入]  按鈕之後，`signIn` 方法會呼叫 `loginPopup` 以將使用者登入。 這個方法會開啟「Microsoft 身分識別平台端點」  的快顯視窗，以提示及驗證使用者的認證。 成功登入後，*msal.js* 便會起始[授權碼流程](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow)。

此時，系統會將受 PKCE 保護的授權碼傳送至受 CORS 保護的權杖端點，並交換權杖。 系統會接收識別碼權杖、存取權杖和重新整理權杖，並由 *msal.js* 進行處理，然後快取權杖中包含的資訊。

識別碼權杖包含使用者的基本資訊，例如其顯示名稱。 如果您打算使用此權杖所提供的任何資料，則必須由後端伺服器進行驗證，以確保權杖已核發給應用程式的有效使用者。 重新整理權杖的存留期有限，24 小時後便會到期。 您可以使用重新整理權杖來以無訊息方式取得新的存取權杖。

本指南所產生的 SPA 會呼叫 `acquireTokenSilent` 和/或 `acquireTokenPopup`，以取得用來查詢 Microsoft Graph API 中使用者設定檔資訊的「存取權杖」  。 如果您需要會驗證識別碼權杖的範例，請參閱 GitHub 上的 [active-directory-javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) 範例應用程式。 此範例使用 ASP.NET Web API 進行權杖驗證。

#### <a name="get-a-user-token-interactively"></a>以互動方式取得使用者權杖

在初始登入過後，使用者應該就不會在每次必須要求權杖來存取資源時，都收到重新驗證的要求。 若要避免這類重新驗證要求，請使用 `acquireTokenSilent`。 但是，有時候您可能必須強制要求使用者與 Microsoft 身分識別平台端點互動。 例如：

- 使用者需要重新輸入其認證，因為密碼已過期。
- 您的應用程式要求資源存取權，而您需要使用者同意。
- 需要雙因素驗證。

呼叫 `acquireTokenPopup` 會開啟快顯視窗 (呼叫 `acquireTokenRedirect` 則會將使用者重新導向至 Microsoft 身分識別平台端點)。 在該視窗中，使用者必須藉由確認其認證、同意必要的資源，或完成雙因素驗證來進行互動。

#### <a name="get-a-user-token-silently"></a>以無訊息方式取得使用者權杖

`acquireTokenSilent` 方法會處理權杖取得和更新作業，不需要與使用者進行任何互動。 在第一次執行 `loginPopup` (或 `loginRedirect`) 之後，`acquireTokenSilent` 就會成為用來取得權杖的常用方法，以在後續呼叫中使用那些權杖存取受保護的資源。 (以無訊息方式進行要求或更新權杖的呼叫。) `acquireTokenSilent` 可能會因為某些狀況而失敗。 例如，使用者的密碼可能已過期。 您的應用程式可以透過兩種方式處理此例外狀況：

1. 立即對 `acquireTokenPopup` 進行呼叫，以觸發使用者登入提示。 此模式通常用於應用程式中沒有未經驗證內容可供使用者使用的線上應用程式。 此引導式設定所產生的範例應用程式會使用此模式。

1. 以視覺方式讓使用者知道需要透過互動方式登入，使用者就能選取正確的登入時機，或應用程式可以在之後重試 `acquireTokenSilent`。 這是相當常用的技術，因為使用者可以在不中斷的情況下使用應用程式的其他功能。 例如，應用程式中可能會有未驗證的內容。 在此情況下，使用者可以決定何時登入以存取受保護的資源，或者重新整理過期的資訊。

> [!NOTE]
> 本快速入門預設會使用 `loginPopup` 和 `acquireTokenPopup` 方法。 如果您使用 Internet Explorer 作為瀏覽器，建議您使用 `loginRedirect` 和 `acquireTokenRedirect` 方法，這是因為有與 Internet Explorer 處理快顯視窗的方式相關的[已知問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues)。 如果您想要了解如何使用重新導向方法來達到相同的結果，請參閱 [*authRedirect.js*](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js)。

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>使用您剛剛取得的權杖呼叫 Microsoft Graph API


 建立名為 *graph.js* 的 .js 檔案以針對 Microsoft 圖形 API 發出 REST 呼叫，然後新增下列程式碼：

   ```javascript

// Helper function to call MS Graph API endpoint
// using authorization bearer token scheme
function callMSGraph(endpoint, token, callback) {
    const headers = new Headers();
    const bearer = `Bearer ${token}`;

    headers.append("Authorization", bearer);

    const options = {
        method: "GET",
        headers: headers
    };

    console.log('request made to Graph API at: ' + new Date().toString());

    fetch(endpoint, options)
        .then(response => response.json())
        .then(response => callback(response, endpoint))
        .catch(error => console.log(error));
}
   ```

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>針對受保護 API 進行 REST 呼叫的相關詳細資訊

在使用此指南建立的範例應用程式中，使用 `callMSGraph()` 方法對需要權杖的受保護資源提出 HTTP `GET` 要求。 然後，該要求會將內容傳回給呼叫端。 此方法會在「HTTP 授權標頭」  中加入取得的權杖。 對於本指南建立的範例應用程式，資源為 Microsoft Graph API 的 me  端點，其會顯示使用者的設定檔資訊。

## <a name="test-your-code"></a>測試您的程式碼

1. 針對 Node.js，請從應用程式資料夾中執行下列命令來啟動網頁伺服器：

   ```bash
   npm install
   npm start
   ```
1. 在瀏覽器中輸入 **http://localhost:3000** 或 **http://localhost:{port}** ，其中 *port* 是網頁伺服器接聽所在的連接埠。 您應該會看到 index.html  檔案和 [登入]  按鈕。

## <a name="test-your-application"></a>測試您的應用程式

在瀏覽器載入您的 index.html  檔案之後，請選取 [登入]  。 系統會提示您使用 Microsoft 身分識別平台端點登入：

![JavaScript SPA 帳戶登入視窗](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>同意應用程式存取

您第一次登入應用程式時，系統會提示您授與您設定檔的存取權，並且將您登入：

![[要求的權限] 視窗](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>檢視應用程式結果

登入之後，系統會顯示以 Microsoft Graph API 回應傳回的使用者設定檔資訊：

![Microsoft Graph API 呼叫的結果](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

### <a name="more-information-about-scopes-and-delegated-permissions"></a>與範圍和委派的權限有關的詳細資訊

Microsoft Graph API 需要 user.read  範圍才能讀取使用者的設定檔。 根據預設，在 Azure 入口網站中註冊的每個應用程式中，都會自動新增此範圍。 Microsoft Graph 的其他 API 與您後端伺服器的自訂 API 一樣，需要其他範圍。 例如，Microsoft Graph API 需要 *Mail.Read* 範圍才能列出使用者的郵件。

> [!NOTE]
> 當您新增範圍時，系統可能會提示使用者另外同意。

如果後端 API 不需要範圍 (不建議)，您可以在呼叫中使用 clientId  作為範圍以取得權杖。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>後續步驟

[MSAL.js GitHub 存放庫](https://github.com/AzureAD/microsoft-authentication-library-for-js)包含額外的程式庫文件、常見問題集，並提供問題支援。
