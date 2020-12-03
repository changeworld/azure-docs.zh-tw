---
title: 教學課程：建立使用驗證程式碼流程的 JavaScript 單頁應用程式 | Azure
titleSuffix: Microsoft identity platform
description: 在本教學課程中，您會建立可登入使用者的 JavaScript SPA，並使用驗證程式碼流程從 Microsoft 身分識別平台取得存取權杖，並且呼叫 Microsoft Graph API。
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 07/17/2020
ms.author: hahamil
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: 79fe821e2da494ab8c9e4cb407e2c2b025f75568
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96169098"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-app-spa-using-auth-code-flow"></a>教學課程：使用授權碼流程來登入使用者，並從 JavaScript 單頁應用程式 (SPA) 呼叫 Microsoft Graph API

在本教學課程中，您會建置一個 JavaScript 單頁應用程式 (SPA)，使用 PKCE 的授權碼流程將使用者登入及呼叫 Microsoft Graph。 您所建置的 SPA 會使用適用於 JavaScript v2.0 的 Microsoft 驗證程式庫 (MSAL)。

在本教學課程中：
> [!div class="checklist"]
> * 使用 PKCE 執行 OAuth 2.0 授權碼流程
> * 登入個人的 Microsoft 帳戶以及公司和學校帳戶
> * 取得存取權杖
> * 呼叫需要從 Microsoft 身分識別平台端點取得存取權杖的 Microsoft Graph API 或自有 API

MSAL.js 2.0 改良了 MSAL.js 1.0，可在瀏覽器中支援授權碼流程，而非隱含的授與流程。 MSAL.js 2.0 **不** 支援隱含流程。

## <a name="prerequisites"></a>必要條件

* 用於執行本機網頁伺服器的 [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) 或其他程式碼編輯器

## <a name="how-the-tutorial-app-works"></a>教學課程應用程式的運作方式

:::image type="content" source="media/tutorial-v2-javascript-auth-code/diagram-01-auth-code-flow.png" alt-text="顯示單頁應用程式中授權碼流程的圖表":::

您在本教學課程中建立的應用程式可讓 JavaScript SPA 從 Microsoft 身分識別平台端點取得安全性權杖，以查詢 Microsoft Graph API。 在此案例中，於使用者登入之後，系統會要求存取權杖，並將其新增到授權標頭中的 HTTP 要求內。 權杖取得和更新作業是由適用於 JavaScript 的 Microsoft Authentication Library (MSAL.js) 負責處理的。

本教學課程會使用下列程式庫：

[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) 適用於 JavaScript v2.0 的 Microsoft 驗證程式庫瀏覽器套件

## <a name="get-the-completed-code-sample"></a>取得完整的程式碼範例

想要改為下載本教學課程的已完成專案範例嗎？ 若要使用本機網頁伺服器 (例如 Node.js) 來執行專案，請複製 [ms-identity-javascript-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2) 存放庫：

`git clone https://github.com/Azure-Samples/ms-identity-javascript-v2`

然後，若要在您執行之前設定程式碼範例，請跳至[設定步驟](#register-your-application)。

若要繼續進行本教學課程並自行建置應用程式，請移至下一節[必要條件](#prerequisites)。

## <a name="create-your-project"></a>建立專案

安裝了 [Node.js](https://nodejs.org/en/download/) 之後，請建立資料夾以供裝載應用程式，例如 *msal-spa-tutorial*。

接下來，請實作小型的 [Express](https://expressjs.com/) 網頁伺服器來提供您的 *index.html* 檔案。

1. 首先，在終端機中變更至您的專案目錄，然後執行下列 `npm` 命令：
    ```console
    npm init -y
    npm install @azure/msal-browser
    npm install express
    npm install morgan
    npm install yargs
    ```
2. 接下來，建立名為 *server.js* 的檔案，然後新增下列程式碼：

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

現在，您擁有一個可用來提供 SPA 的小型網頁伺服器。 完成本教學課程的其餘部分之後，您專案的檔案和資料夾結構看起來應該會如下所示：

```
msal-spa-tutorial/
├── app
│   ├── authConfig.js
│   ├── authPopup.js
│   ├── authRedirect.js
│   ├── graphConfig.js
│   ├── graph.js
│   ├── index.html
│   └── ui.js
└── server.js
```

## <a name="create-the-spa-ui"></a>建立 SPA UI

1. 在您的專案目錄中建立 *app* 資料夾，並在其中為您的 JavaScript SPA 建立 *index.html* 檔案。 此檔案會實作以 **Bootstrap 4 Framework** 建置的 UI，並匯入用於設定、驗證和 API 呼叫的指令碼檔案。

    在 index.html 檔案中，新增下列程式碼：

    ```html
    <!DOCTYPE html>
    <html lang="en">
      <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
        <title>Tutorial | MSAL.js JavaScript SPA</title>

        <!-- IE support: add promises polyfill before msal.js  -->
        <script type="text/javascript" src="//cdn.jsdelivr.net/npm/bluebird@3.7.2/js/browser/bluebird.min.js"></script>
        <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.4/js/msal-browser.js" integrity="sha384-7sxY2tN3GMVE5jXH2RL9AdbO6s46vUh9lUid4yNCHJMUzDoj+0N4ve6rLOmR88yN" crossorigin="anonymous"></script>

        <!-- adding Bootstrap 4 for UI components  -->
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
        <link rel="SHORTCUT ICON" href="https://c.s-microsoft.com/favicon.ico?v2" type="image/x-icon">
      </head>
      <body>
        <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
          <a class="navbar-brand" href="/">Microsoft identity platform</a>
          <div class="btn-group ml-auto dropleft">
              <button type="button" id="SignIn" class="btn btn-secondary" onclick="signIn()">
                Sign In
              </button>
          </div>
        </nav>
        <br>
        <h5 class="card-header text-center">JavaScript SPA calling Microsoft Graph API with MSAL.js</h5>
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
            <button class="btn btn-primary" id="readMail" onclick="readMail()">Read Mail</button>
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

2. 接下來，同樣在 *app* 資料夾中，建立名為 *ui.js* 的檔案，並新增下列程式碼。 這個檔案將會存取和更新 DOM 元素。

    ```JavaScript
    // Select DOM elements to work with
    const welcomeDiv = document.getElementById("WelcomeMessage");
    const signInButton = document.getElementById("SignIn");
    const cardDiv = document.getElementById("card-div");
    const mailButton = document.getElementById("readMail");
    const profileButton = document.getElementById("seeProfile");
    const profileDiv = document.getElementById("profile-div");

    function showWelcomeMessage(account) {
        // Reconfiguring DOM elements
        cardDiv.style.display = 'initial';
        welcomeDiv.innerHTML = `Welcome ${account.username}`;
        signInButton.setAttribute("onclick", "signOut();");
        signInButton.setAttribute('class', "btn btn-success")
        signInButton.innerHTML = "Sign Out";
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

遵循[單頁應用程式：應用程式註冊](scenario-spa-app-registration.md)中的步驟，為您的 SPA 建立應用程式註冊。

在[重新導向 URI：MSAL.js 2.0 與授權碼流程](scenario-spa-app-registration.md#redirect-uri-msaljs-20-with-auth-code-flow)步驟中輸入 `http://localhost:3000`，這是本教學課程的應用程式執行所在的預設位置。

如果您想要使用不同的連接埠，請輸入 `http://localhost:<port>`，其中 `<port>` 是您慣用的 TCP 連接埠號碼。 如果您指定的連接埠號碼不是 `3000`，也請使用您慣用的連接埠號碼來更新 *server.js*。

### <a name="configure-your-javascript-spa"></a>設定您的 JavaScript SPA

在 *app* 資料夾中建立名為 *authConfig.js* 的檔案以包含用於驗證的設定參數，然後新增下列程式碼：

```javascript
const msalConfig = {
  auth: {
    clientId: "Enter_the_Application_Id_Here",
    authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Info_Here",
    redirectUri: "Enter_the_Redirect_Uri_Here",
  },
  cache: {
    cacheLocation: "sessionStorage", // This configures where your cache will be stored
    storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
  }
};

// Add scopes here for ID token to be used at Microsoft identity platform endpoints.
const loginRequest = {
 scopes: ["openid", "profile", "User.Read"]
};

// Add scopes here for access token to be used at Microsoft Graph API endpoints.
const tokenRequest = {
 scopes: ["User.Read", "Mail.Read"]
};
```

修改 `msalConfig` 區段中的值，如下所述：

- `Enter_the_Application_Id_Here`:所註冊應用程式的 **應用程式 (用戶端) 識別碼**。
- `Enter_the_Cloud_Instance_Id_Here`:應用程式註冊所在的 Azure 雲端執行個體。
  - 針對主要 (或 *全域*) Azure 雲端，請輸入 `https://login.microsoftonline.com`。
  - 針對 **國家** 雲端 (例如中國)，您可以在 [國家雲端](authentication-national-cloud.md)中找到適當的值。
- `Enter_the_Tenant_info_here` 應該是下列其中一項：
  - 如果您的應用程式支援 [此組織目錄中的帳戶]，請將此值取代為 [租用戶識別碼] 或 [租用戶名稱]。 例如： `contoso.microsoft.com` 。
  - 如果您的應用程式支援 [任何組織目錄中的帳戶]，請將此值取代為 `organizations`。
  - 如果您的應用程式支援 [任何組織目錄中的帳戶及個人的 Microsoft 帳戶]，請將此值取代為 `common`。
  - 若要將支援範圍限制為 [僅限個人 Microsoft 帳戶]，請將此值取代為 `consumers`。
- `Enter_the_Redirect_Uri_Here` 為 `http://localhost:3000`。

如果您使用全域 Azure 雲端，則 *authConfig.js* 中的 `authority` 值應該類似下列文字：

```javascript
authority: "https://login.microsoftonline.com/common",
```

依然在 *app* 資料夾中，建立名為 *graphConfig.js* 的檔案。 新增下列程式碼，為您的應用程式提供用於呼叫 Microsoft Graph API 的設定參數：

```javascript
// Add the endpoints here for Microsoft Graph API services you'd like to use.
const graphConfig = {
    graphMeEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me",
    graphMailEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me/messages"
};
```

修改 `graphConfig` 區段中的值，如下所述：

- `Enter_the_Graph_Endpoint_Here` 是應用程式所應通訊的 Microsoft Graph API 執行個體。
  - 針對 **全域** Microsoft Graph API 端點，請將此字串的兩個執行個體取代為 `https://graph.microsoft.com`。
  - 針對 **國家** 雲端部署中的端點，請參閱 Microsoft Graph 文件中的 [國家雲端部署](/graph/deployments)。

如果您使用全域端點，則 *graphConfig.js* 中的 `graphMeEndpoint` 和 `graphMailEndpoint` 值應如下所示：

```javascript
graphMeEndpoint: "https://graph.microsoft.com/v1.0/me",
graphMailEndpoint: "https://graph.microsoft.com/v1.0/me/messages"
```

## <a name="use-microsoft-authentication-library-msal-to-sign-in-user"></a>使用 Microsoft 驗證程式庫 (MSAL) 來登入使用者

### <a name="pop-up"></a>快顯

在 *app* 資料夾中建立名為 *authPopup.js* 的檔案，並針對登入快顯新增下列驗證和權杖取得程式碼：

```JavaScript
// Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

let username = "";

function loadPage() {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    const currentAccounts = myMSALObj.getAllAccounts();
    if (currentAccounts === null) {
        return;
    } else if (currentAccounts.length > 1) {
        // Add choose account code here
        console.warn("Multiple accounts detected.");
    } else if (currentAccounts.length === 1) {
        username = currentAccounts[0].username;
        showWelcomeMessage(currentAccounts[0]);
    }
}

function handleResponse(resp) {
    if (resp !== null) {
        username = resp.account.username;
        showWelcomeMessage(resp.account);
    } else {
        loadPage();
    }
}

function signIn() {
    myMSALObj.loginPopup(loginRequest).then(handleResponse).catch(error => {
        console.error(error);
    });
}

function signOut() {
    const logoutRequest = {
        account: myMSALObj.getAccountByUsername(username)
    };

    myMSALObj.logout(logoutRequest);
}

function getTokenPopup(request) {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    request.account = myMSALObj.getAccountByUsername(username);
    return myMSALObj.acquireTokenSilent(request).catch(error => {
        console.warn("silent token acquisition fails. acquiring token using redirect");
        if (error instanceof msal.InteractionRequiredAuthError) {
            // fallback to interaction when silent call fails
            return myMSALObj.acquireTokenPopup(request).then(tokenResponse => {
                console.log(tokenResponse);

                return tokenResponse;
            }).catch(error => {
                console.error(error);
            });
        } else {
            console.warn(error);
        }
    });
}

function seeProfile() {
    getTokenPopup(loginRequest).then(response => {
        callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
        profileButton.classList.add('d-none');
        mailButton.classList.remove('d-none');
    }).catch(error => {
        console.error(error);
    });
}

function readMail() {
    getTokenPopup(tokenRequest).then(response => {
        callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
    }).catch(error => {
        console.error(error);
    });
}

loadPage();
```

### <a name="redirect"></a>重新導向

在 *app* 資料夾中建立名為 *authRedirect.js* 的檔案，並針對登入重新導向新增下列驗證和權杖取得程式碼：

```javascript
// Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

let accessToken;
let username = "";

// Redirect: once login is successful and redirects with tokens, call Graph API
myMSALObj.handleRedirectPromise().then(handleResponse).catch(err => {
    console.error(err);
});

function handleResponse(resp) {
    if (resp !== null) {
        username = resp.account.username;
        showWelcomeMessage(resp.account);
    } else {
        /**
         * See here for more info on account retrieval:
         * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
         */
        const currentAccounts = myMSALObj.getAllAccounts();
        if (currentAccounts === null) {
            return;
        } else if (currentAccounts.length > 1) {
            // Add choose account code here
            console.warn("Multiple accounts detected.");
        } else if (currentAccounts.length === 1) {
            username = currentAccounts[0].username;
            showWelcomeMessage(currentAccounts[0]);
        }
    }
}

function signIn() {
    myMSALObj.loginRedirect(loginRequest);
}

function signOut() {
    const logoutRequest = {
        account: myMSALObj.getAccountByUsername(username)
    };

    myMSALObj.logout(logoutRequest);
}

function getTokenRedirect(request) {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    request.account = myMSALObj.getAccountByUsername(username);
    return myMSALObj.acquireTokenSilent(request).catch(error => {
            console.warn("silent token acquisition fails. acquiring token using redirect");
            if (error instanceof msal.InteractionRequiredAuthError) {
                // fallback to interaction when silent call fails
                return myMSALObj.acquireTokenRedirect(request);
            } else {
                console.warn(error);
            }
        });
}

function seeProfile() {
    getTokenRedirect(loginRequest).then(response => {
        callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
        profileButton.classList.add('d-none');
        mailButton.classList.remove('d-none');
    }).catch(error => {
        console.error(error);
    });
}

function readMail() {
    getTokenRedirect(tokenRequest).then(response => {
        callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
    }).catch(error => {
        console.error(error);
    });
}
```

### <a name="how-the-code-works"></a>程式碼的運作方式

在使用者第一次選取 [登入] 按鈕時，`signIn` 方法會呼叫 `loginPopup` 以將使用者登入。 `loginPopup` 方法會開啟「Microsoft 身分識別平台端點」的快顯視窗，以提示及驗證使用者的認證。 成功登入後，*msal.js* 便會起始 [授權碼流程](v2-oauth2-auth-code-flow.md)。

此時，系統會將受 PKCE 保護的授權碼傳送至受 CORS 保護的權杖端點，並交換權杖。 您的應用程式會接收識別碼權杖、存取權杖和重新整理權杖，並由 *msal.js* 進行處理，然後快取權杖中包含的資訊。

識別碼權杖包含使用者的基本資訊，例如其顯示名稱。 如果您打算使用識別碼權杖所提供的任何資料，則後端伺服器 *必須* 驗證資料，以保證所簽發權杖的適用對象是您應用程式的有效使用者。

存取權杖的存留期有限，24 小時後便會到期。 您可以使用重新整理權杖來以無訊息方式取得新的存取權杖。

您在本教學課程所建立的 SPA 會呼叫 `acquireTokenSilent` 和/或 `acquireTokenPopup`，以取得用來查詢 Microsoft Graph API 中使用者設定檔資訊的「存取權杖」。 如果您需要會驗證識別碼權杖的範例，請參閱 GitHub 上的 [active-directory-javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) 範例應用程式。 此範例使用 ASP.NET Web API 進行權杖驗證。

#### <a name="get-a-user-token-interactively"></a>以互動方式取得使用者權杖

使用者完成初始登入後，應用程式應該就不會在使用者每次需要存取受保護的資源時要求其重新驗證 (也就是要求權杖)。 若要避免這類重新驗證要求，請呼叫 `acquireTokenSilent`。 但是，有時候您可能必須強制要求使用者與 Microsoft 身分識別平台端點互動。 例如：

- 使用者需要重新輸入其認證，因為密碼已過期。
- 您的應用程式要求資源存取權，而您需要使用者同意。
- 需要雙因素驗證。

呼叫 `acquireTokenPopup` 會開啟快顯視窗 (呼叫 `acquireTokenRedirect` 則會將使用者重新導向至 Microsoft 身分識別平台端點)。 在該視窗中，使用者必須藉由確認其認證、同意必要的資源，或完成雙因素驗證來進行互動。

#### <a name="get-a-user-token-silently"></a>以無訊息方式取得使用者權杖

`acquireTokenSilent` 方法會處理權杖取得和更新作業，不需要與使用者進行任何互動。 在第一次執行 `loginPopup` (或 `loginRedirect`) 之後，`acquireTokenSilent` 就會成為用來取得權杖的常用方法，以在後續呼叫中使用那些權杖存取受保護的資源。 (以無訊息方式進行要求或更新權杖的呼叫。) `acquireTokenSilent` 可能會因為某些狀況而失敗。 例如，使用者的密碼可能已過期。 您的應用程式可以透過兩種方式處理此例外狀況：

1. 立即對 `acquireTokenPopup` 進行呼叫，以觸發使用者登入提示。 此模式通常用於應用程式中沒有未經驗證內容可供使用者使用的線上應用程式。 此引導式設定所產生的範例應用程式會使用此模式。
1. 以視覺方式讓使用者知道需要透過互動方式登入，使用者就能選取正確的登入時機，或應用程式可以在之後重試 `acquireTokenSilent`。 這是相當常用的技術，因為使用者可以在不中斷的情況下使用應用程式的其他功能。 例如，應用程式中可能會有未驗證的內容。 在此情況下，使用者可以決定何時登入以存取受保護的資源，或者重新整理過期的資訊。

> [!NOTE]
> 本教學課程預設會使用 `loginPopup` 和 `acquireTokenPopup` 方法。 如果您使用 Internet Explorer，建議您使用 `loginRedirect` 和 `acquireTokenRedirect` 方法，因為 Internet Explorer 和快顯視窗有[已知問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues)。 如需如何使用重新導向方法來達到相同結果的範例，請參閱 GitHub 上的 [*authRedirect.js*](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js)。

## <a name="call-the-microsoft-graph-api"></a>呼叫 Microsoft Graph API

在 *app* 資料夾中建立名為 *graph.js* 的檔案，並新增下列程式碼以對 Microsoft Graph API 發出 REST 呼叫：

```javascript
// Helper function to call Microsoft Graph API endpoint
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

在本教學課程所建立的應用程式範例中，使用 `callMSGraph()` 方法對需要權杖的受保護資源提出 HTTP `GET` 要求。 然後，該要求會將內容傳回給呼叫端。 此方法會在「HTTP 授權標頭」中加入取得的權杖。 在本教學課程所建立的應用程式範例中，受保護資源是 Microsoft Graph API 的 me 端點，其會顯示已登入使用者的設定檔資訊。

## <a name="test-your-application"></a>測試您的應用程式

您已完成應用程式的建立，現在已做好準備而可以啟動 Node.js 網頁伺服器，並測試應用程式的功能了。

1. 請從專案資料夾的根目錄內執行下列命令，以啟動 Node.js 網頁伺服器：

   ```console
   npm start
   ```
1. 在您的瀏覽器中，瀏覽至 `http://localhost:3000` 或 `http://localhost:<port>`，其中 `<port>` 是您網頁伺服器所接聽的連接埠。 您應該會看到 index.html 檔案和 [登入] 按鈕。

### <a name="sign-in-to-the-application"></a>登入應用程式

在瀏覽器載入您的 index.html 檔案之後，請選取 [登入]。 系統會提示您使用 Microsoft 身分識別平台端點登入：

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-01-signin-dialog.png" alt-text="顯示登入對話方塊的網頁瀏覽器":::

### <a name="provide-consent-for-application-access"></a>同意應用程式存取

您第一次登入應用程式時，系統會提示您授與您設定檔的存取權，並且將您登入：

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-02-consent-dialog.png" alt-text="網頁瀏覽器中顯示的內容對話方塊":::

如果您同意要求的權限，Web 應用程式便會顯示您的使用者名稱，以表示您已成功登入：

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-03-signed-in.png" alt-text="在網頁瀏覽器中成功登入的結果":::

### <a name="call-the-graph-api"></a>呼叫圖形 API

登入之後，請選取 [查看設定檔]，以檢視呼叫 Microsoft Graph API 時的回應所傳回的使用者設定檔資訊：

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-04-see-profile.png" alt-text="瀏覽器中顯示來自 Microsoft Graph 的設定檔資訊":::

### <a name="more-information-about-scopes-and-delegated-permissions"></a>與範圍和委派的權限有關的詳細資訊

Microsoft Graph API 需要 user.read 範圍才能讀取使用者的設定檔。 根據預設，在 Azure 入口網站中註冊的每個應用程式中，都會自動新增此範圍。 Microsoft Graph 的其他 API 與您後端伺服器的自訂 API 一樣，需要其他範圍。 例如，Microsoft Graph API 需要 *Mail.Read* 範圍才能列出使用者的電子郵件。

當您新增範圍時，系統可能會提示使用者針對新增的範圍另外提供同意。

如果後端 API 不需要範圍 (不建議)，您可以在呼叫中使用 `clientId` 作為範圍以取得權杖。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>後續步驟

如果您想要深入了解如何在 Microsoft 身分識別平台上開發 JavaScript 單頁應用程式，請參閱我們的多個部分案例系列：

> [!div class="nextstepaction"]
> [案例：單頁應用程式](scenario-spa-overview.md)