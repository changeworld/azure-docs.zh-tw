---
title: 教學課程：建立使用 Microsoft 身分識別平台進行驗證的 JavaScript 單頁應用程式 | Azure
titleSuffix: Microsoft identity platform
description: 在本教學課程中，您會建置 JavaScript 單頁應用程式 (SPA)，使用 Microsoft 身分識別平台來登入使用者，並取得存取權杖來代表他們呼叫 Microsoft Graph API。
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/06/2020
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40, devx-track-js
ms.openlocfilehash: 51b548beae57ce1da32006b61dfd222b0a4e6218
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98015856"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>教學課程：登入使用者並從 JavaScript 單頁應用程式 (SPA) 呼叫 Microsoft Graph API

在本教學課程中，您會建置一個 JavaScript 單頁應用程式 (SPA)，使用隱含流程將使用者登入及呼叫 Microsoft Graph。 您所建置的 SPA 會使用適用於 JavaScript v1.0 的 Microsoft 驗證程式庫 (MSAL)。

本教學課程內容：

> [!div class="checklist"]
> * 使用 `npm` 建立 JavaScript 專案
> * 在 Azure 入口網站中註冊應用程式
> * 新增程式碼以支援使用者登入和登出
> * 新增程式碼以呼叫 Microsoft Graph API
> * 測試應用程式

>[!TIP]
> 本教學課程使用 MSAL.js v1.x，其僅限於使用單頁應用程式的隱含授與流程。 建議所有新應用程式改用[具有 PKCE 和 CORS 支援的 MSAL.js 2.x 和授權碼流程](tutorial-v2-javascript-auth-code.md)。

## <a name="prerequisites"></a>必要條件

* 用於執行本機網頁伺服器的 [Node.js](https://nodejs.org/en/download/)。
* 用於修改專案檔的 [Visual Studio Code](https://code.visualstudio.com/download) 或其他編輯器。
* 新式網頁瀏覽器。 由於應用程式使用 [ES6](http://www.ecma-international.org/ecma-262/6.0/) 慣例，所以您在本教學課程中建置的應用程式 **Internet Explorer** **不受支援**。

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>本指南產生之範例應用程式的運作方式

![示範本教學課程所產生的應用程式範例如何運作](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

此指南建立的範例應用程式可讓 JavaScript SPA 查詢 Microsoft Graph API，或查詢可接受來自 Microsoft 身分識別平台端點之權杖的 Web API。 在此案例中，當使用者登入之後，系統會透過授權標頭要求一個存取權杖，並將其新增到 HTTP 要求。 系統會使用此權杖以透過 **MS 圖形 API** 來取得使用者的設定檔和郵件。

權杖取得和更新作業是由[適用於 JavaScript 的 Microsoft Authentication Library (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-js) 負責處理的。

## <a name="set-up-your-web-server-or-project"></a>設定您的網頁伺服器或專案

> 想要改為下載此範例的專案嗎？ [下載專案檔](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)。
>
> 若要在您執行之前設定程式碼範例，請跳至[設定步驟](#register-your-application)。

## <a name="create-your-project"></a>建立專案

請確定您已安裝 [Node.js](https://nodejs.org/en/download/)，然後建立資料夾來裝載您的應用程式。 我們會在該處實作簡單的 [Express](https://expressjs.com/) Web 伺服器來提供 `index.html` 檔案。

1. 使用終端 (例如 Visual Studio Code 整合式終端) 找出專案資料夾，然後鍵入：

   ```console
   npm init
   ```

2. 接下來，安裝必要的相依性：

   ```console
   npm install express --save
   npm install morgan --save
   ```

1. 現在，建立名為 `server.js` 的 .js 檔案，然後新增下列程式碼：

   ```JavaScript
   const express = require('express');
   const morgan = require('morgan');
   const path = require('path');

   //initialize express.
   const app = express();

   // Initialize variables.
   const port = 3000; // process.env.PORT || 3000;

   // Configure morgan module to log all requests.
   app.use(morgan('dev'));

   // Set the front-end folder to serve public assets.
   app.use(express.static('JavaScriptSPA'))

   // Set up a route for index.html.
   app.get('*', function (req, res) {
       res.sendFile(path.join(__dirname + '/index.html'));
   });

   // Start the server.
   app.listen(port);
   console.log('Listening on port ' + port + '...');
   ```

現在，您擁有一個簡單的伺服器來提供 SPA。 本教學課程結尾處應該會有的資料夾結構如下所示：

![應該會有的 SPA 資料夾結構文字說明](./media/tutorial-v2-javascript-spa/single-page-application-folder-structure.png)

## <a name="create-the-spa-ui"></a>建立 SPA UI

1. 為您的 JavaScript SPA 建立 `index.html` 檔案。 此檔案會實作以 **Bootstrap 4 Framework** 建置的 UI，並匯入用於設定、驗證和 API 呼叫的指令碼檔案。

   在 `index.html` 檔案中新增下列程式碼：

   ```html
   <!DOCTYPE html>
   <html lang="en">
     <head>
       <meta charset="UTF-8">
       <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
       <title>Quickstart | MSAL.JS Vanilla JavaScript SPA</title>

       <!-- msal.js with a fallback to backup CDN -->
       <script type="text/javascript" src="https://alcdn.msauth.net/lib/1.2.1/js/msal.js" integrity="sha384-9TV1245fz+BaI+VvCjMYL0YDMElLBwNS84v3mY57pXNOt6xcUYch2QLImaTahcOP" crossorigin="anonymous"></script>
       <script type="text/javascript">
         if(typeof Msal === 'undefined')document.write(unescape("%3Cscript src='https://alcdn.msftauth.net/lib/1.2.1/js/msal.js' type='text/javascript' integrity='sha384-m/3NDUcz4krpIIiHgpeO0O8uxSghb+lfBTngquAo2Zuy2fEF+YgFeP08PWFo5FiJ' crossorigin='anonymous'%3E%3C/script%3E"));
       </script>

       <!-- adding Bootstrap 4 for UI components  -->
       <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
     </head>
     <body>
       <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
         <a class="navbar-brand" href="/">MS Identity Platform</a>
         <div class="btn-group ml-auto dropleft">
           <button type="button" id="signIn" class="btn btn-secondary" onclick="signIn()">Sign In</button>
           <button type="button" id="signOut" class="btn btn-success d-none" onclick="signOut()">Sign Out</button>
       </div>
       </nav>
       <br>
       <h5 class="card-header text-center">Vanilla JavaScript SPA calling MS Graph API with MSAL.JS</h5>
       <br>
       <div class="row" style="margin:auto" >
       <div id="card-div" class="col-md-3 d-none">
       <div class="card text-center">
         <div class="card-body">
           <h5 class="card-title" id="welcomeMessage">Please sign-in to see your profile and read your mails</h5>
           <div id="profile-div"></div>
           <br>
           <br>
           <button class="btn btn-primary" id="seeProfile" onclick="seeProfile()">See Profile</button>
           <br>
           <br>
           <button class="btn btn-primary d-none" id="readMail" onclick="readMail()">Read Mails</button>
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

       <!-- replace next line with authRedirect.js if you would like to use the redirect flow -->
       <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
       <script type="text/javascript" src="./authPopup.js"></script>
       <script type="text/javascript" src="./graph.js"></script>
     </body>
   </html>
   ```

   > [!TIP]
   > 您可以使用 [MSAL.js 版本](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases) 下的最新發行版本取代上述指令碼中的 MSAL.js 版本。

2. 現在，建立名為 `ui.js` 的 .js 檔案以便存取和更新 DOM 元素，然後新增下列程式碼：

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

在繼續進行驗證之前，請先在 **Azure Active Directory** 上註冊應用程式。

1. 登入 <a href="https://portal.azure.com/" target="_blank">Azure 入口網站<span class="docon docon-navigate-external x-hidden-focus"></span></a>。
1. 如果您有多個租用的存取權，請使用頂端功能表中的 **目錄 + 訂用帳戶** 篩選條件 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: 來選取要在其中註冊應用程式的租用戶。
1. 搜尋並選取 [Azure Active Directory]  。
1. 在 **管理** 下選取 [應用程式註冊] > [新增註冊]。
1. 輸入應用程式的 **名稱**。 您的應用程式使用者可能會看到此名稱，您可以稍後再變更。
1. 在 [支援的帳戶類型] 底下，選取 [任何組織目錄中的帳戶及個人的 Microsoft 帳戶]。
1. 在 [重新導向 URI] 區段中，從下拉式清單中選取 [Web] 平台，然後根據您的網頁伺服器，將值設定為應用程式的 URL。
1. 選取 [註冊]。
1. 在應用程式 [概觀] 頁面上，記下 [應用程式 (用戶端) 識別碼] 值以供稍後使用。
1. 在 [管理] 底下，選取 [驗證]。
1. 在 [隱含授與] 區段中，選取 [識別碼權杖]，然後選取 [存取權杖]。 因為此應用程式必須將使用者登入並呼叫 API，所以識別碼權杖和存取權杖都是必要權杖。
1. 選取 [儲存]。

> ### <a name="set-a-redirect-url-for-nodejs"></a>設定 Node.js 的重新導向 URL
>
> 對於 Node.js，您可以在 server.js  檔案中設定網頁伺服器連接埠。 此教學課程會使用連接埠 3000，但您可以使用任何其他可用的連接埠。
>
> 若要在應用程式註冊資訊中設定重新導向 URL，請切換回 [應用程式註冊] 窗格，並且執行下列其中一項作業：
>
> - 將 `http://localhost:3000/` 設定為 [重新導向 URL]。
> - 如果您使用自訂 TCP 連接埠，請使用 *`http://localhost:<port>/`* (其中 *\<port>* 是自訂 TCP 連接埠號碼)。
>   1. 複製 [URL] 值。
>   1. 切換回 [應用程式註冊] 窗格，並貼上複製的值作為 [重新導向 URL]。
>

### <a name="configure-your-javascript-spa"></a>設定您的 JavaScript SPA

建立名為 `authConfig.js` 的新 .js 檔案以包含用於驗證的設定參數，然後新增下列程式碼：

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
    scopes: ["Mail.Read"]
  };
```

 其中：
 - *\<Enter_the_Application_Id_Here>* 是您註冊之應用程式的 **應用程式 (用戶端) 識別碼**。
 - *\<Enter_the_Cloud_Instance_Id_Here>* 是 Azure 雲端的執行個體。 針對主要或全域 Azure 雲端，只要輸入 *https://login.microsoftonline.com* 即可。 針對 **國家** 雲端 (例如中國)，請參閱 [國家雲端](./authentication-national-cloud.md)。
 - *\<Enter_the_Tenant_info_here>* 設定為下列其中一個選項：
   - 如果您的應用程式支援「此組織目錄中的帳戶」，請將此值取代為 [租用戶識別碼] 或 [租用戶名稱] \(例如 *contoso.microsoft.com*\)。
   - 如果您的應用程式支援「任何組織目錄中的帳戶」，請將此值取代為 [組織]。
   - 如果您的應用程式支援「任何組織目錄中的帳戶及個人的 Microsoft 帳戶」，請將此值取代為 [通用]。 若要將支援範圍限制為「僅限個人 Microsoft 帳戶」，請將此值取代為 [取用者]。


## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>使用 Microsoft Authentication Library (MSAL) 登入使用者

建立名為 `authPopup.js` 的新 .js 檔案以包含驗證和權杖取得邏輯，然後新增下列程式碼：

   ```JavaScript
   const myMSALObj = new Msal.UserAgentApplication(msalConfig);

   function signIn() {
     myMSALObj.loginPopup(loginRequest)
       .then(loginResponse => {
         console.log('id_token acquired at: ' + new Date().toString());
         console.log(loginResponse);

         if (myMSALObj.getAccount()) {
           showWelcomeMessage(myMSALObj.getAccount());
         }
       }).catch(error => {
         console.log(error);
       });
   }

   function signOut() {
     myMSALObj.logout();
   }

   function callMSGraph(theUrl, accessToken, callback) {
       var xmlHttp = new XMLHttpRequest();
       xmlHttp.onreadystatechange = function () {
           if (this.readyState == 4 && this.status == 200) {
              callback(JSON.parse(this.responseText));
           }
       }
       xmlHttp.open("GET", theUrl, true); // true for asynchronous
       xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
       xmlHttp.send();
   }

   function getTokenPopup(request) {
     return myMSALObj.acquireTokenSilent(request)
       .catch(error => {
         console.log(error);
         console.log("silent token acquisition fails. acquiring token using popup");

         // fallback to interaction when silent call fails
           return myMSALObj.acquireTokenPopup(request)
             .then(tokenResponse => {
               return tokenResponse;
             }).catch(error => {
               console.log(error);
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
           console.log(error);
         });
     }
   }

   function readMail() {
     if (myMSALObj.getAccount()) {
       getTokenPopup(tokenRequest)
         .then(response => {
           callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
         }).catch(error => {
           console.log(error);
         });
     }
   }
   ```

### <a name="more-information"></a>詳細資訊

在使用者第一次選取 [登入] 按鈕之後，`signIn` 方法會呼叫 `loginPopup` 以將使用者登入。 這個方法會開啟「Microsoft 身分識別平台端點」的快顯視窗，以提示及驗證使用者的認證。 成功登入之後，使用者會重新導回到原本的 index.html 頁面。 系統會收到權杖 (由 `msal.js` 進行處理)，並快取權杖中包含的資訊。 此權杖也稱為「ID 權杖」且包含使用者的基本資訊，例如使用者顯示名稱。 如果您打算將此權杖所提供的任何資料用於任何目的，您必須確定後端伺服器已驗證此權杖，以保證權杖是發給您應用程式的有效使用者。

本指南所產生的 SPA 會呼叫 `acquireTokenSilent` 和/或 `acquireTokenPopup`，以取得用來查詢 Microsoft Graph API 中使用者設定檔資訊的「存取權杖」。 如果您需要可驗證 ID 權杖的範例，請參閱 GitHub 中的[這個](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "GitHub active-directory-javascript-singlepageapp-dotnet-webapi-v2 範例")範例應用程式。 此範例使用 ASP.NET Web API 進行權杖驗證。

#### <a name="get-a-user-token-interactively"></a>以互動方式取得使用者權杖

初次登入之後，您不希望在使用者每次必須要求權杖來存取資源時，都要求使用者重新驗證。 因此，通常應該使用 acquireTokenSilent 以取得權杖。 但是，您有可能必須強制使用者與 Microsoft 身分識別平台端點互動。 範例包括：

- 使用者需要重新輸入其認證，因為密碼已過期。
- 您的應用程式要求資源存取權，而您需要使用者同意。
- 需要雙因素驗證。

呼叫 acquireTokenPopup 開啟快顯視窗 (或呼叫 acquireTokenRedirect 將使用者重新導向至 Microsoft 身分識別平台端點)。 在該視窗中，使用者必須藉由確認其認證、同意必要的資源，或完成雙因素驗證來進行互動。

#### <a name="get-a-user-token-silently"></a>以無訊息方式取得使用者權杖

`acquireTokenSilent` 方法會處理權杖取得和更新作業，不需要與使用者進行任何互動。 在第一次執行 `loginPopup` (或 `loginRedirect`) 之後，`acquireTokenSilent` 就會成為用來取得權杖的常用方法，以在後續呼叫中使用那些權杖存取受保護的資源。 (以無訊息方式進行要求或更新權杖的呼叫。) `acquireTokenSilent` 可能會因為某些狀況而失敗。 例如，使用者的密碼可能已過期。 您的應用程式可以透過兩種方式處理此例外狀況：

1. 立即對 `acquireTokenPopup` 進行呼叫，這會觸發使用者登入提示。 此模式通常用於應用程式中沒有未經驗證內容可供使用者使用的線上應用程式。 此引導式設定所產生的範例應用程式會使用此模式。

1. 應用程式也可以提供視覺指示，讓使用者知道需要透過互動方式登入，使用者就能選取正確的登入時機，或應用程式可以在之後重試 `acquireTokenSilent`。 這是相當常用的方式，因為使用者可以在不中斷的情況下使用應用程式的其他功能。 例如，應用程式中可能會有未驗證的內容。 在此情況下，使用者可以決定何時登入以存取受保護的資源，或者重新整理過期的資訊。

> [!NOTE]
> 本快速入門預設會使用 `loginPopup` 和 `acquireTokenPopup` 方法。 如果您使用 Internet Explorer 作為瀏覽器，建議您使用 `loginRedirect` 和 `acquireTokenRedirect` 方法，這是因為有與 Internet Explorer 處理快顯視窗的方式相關的[已知問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues)。 如果您想要了解如何使用 `Redirect methods` 來達到相同結果，請[參閱](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js)。

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>使用您剛剛取得的權杖呼叫 Microsoft Graph API

1. 首先，建立名為 `graphConfig.js` 的 .js 檔案以儲存 REST 端點。 新增下列程式碼：

   ```JavaScript
      const graphConfig = {
        graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
        graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
      };
   ```

   其中：
   - *\<Enter_the_Graph_Endpoint_Here>* 是 MS 圖形 API 的執行個體。 若為全域 MS 圖形 API 端點，只要將此字串取代為 `https://graph.microsoft.com` 即可。 若為國家雲端部署，請參閱[圖形 API 文件](/graph/deployments)。

1. 接下來，建立名為 `graph.js` 的 .js 檔案以針對 Microsoft Graph API 發出 REST 呼叫，然後新增下列程式碼：

   ```javascript
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
       .catch(error => console.log(error))
   }
   ```

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>針對受保護 API 進行 REST 呼叫的相關詳細資訊

在使用此指南建立的範例應用程式中，使用 `callMSGraph()` 方法對需要權杖的受保護資源提出 HTTP `GET` 要求。 然後，該要求會將內容傳回給呼叫端。 此方法會在「HTTP 授權標頭」中加入取得的權杖。 對於本指南建立的範例應用程式，資源為 Microsoft Graph API 的 me 端點，其會顯示使用者的設定檔資訊。

## <a name="test-your-code"></a>測試您的程式碼

1. 將伺服器設定為接聽以您 *index.html* 檔案位置為基礎的 TCP 通訊埠。 對於 Node.js，請從應用程式資料夾在命令提示字元中執行命令，以啟動網頁伺服器來接聽此連接埠：

   ```bash
   npm install
   npm start
   ```
1. 在瀏覽器中輸入 **http://localhost:3000** 或 **http://localhost:{port}** ，其中 *port* 是 Web 伺服器將會接聽的連接埠。 您應該會看到 index.html 檔案和 [登入] 按鈕。

在瀏覽器載入您的 index.html 檔案之後，請選取 [登入]。 系統會提示您使用 Microsoft 身分識別平台端點登入：

![JavaScript SPA 帳戶登入視窗](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>同意應用程式存取

您第一次登入應用程式時，系統會提示您授與您設定檔的存取權，並且將您登入：

![[要求的權限] 視窗](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>檢視應用程式結果

登入之後，系統會顯示以 Microsoft Graph API 回應傳回的使用者設定檔資訊：

![Microsoft Graph API 呼叫的結果](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

### <a name="more-information-about-scopes-and-delegated-permissions"></a>與範圍和委派的權限有關的詳細資訊

Microsoft Graph API 需要 user.read 範圍才能讀取使用者的設定檔。 根據預設，在註冊入口網站上註冊的每個應用程式中，都會自動新增此範圍。 Microsoft Graph 的其他 API 與您後端伺服器的自訂 API 一樣，需要其他範圍。 例如，Microsoft Graph API 需要 Mail.Read 範圍才能列出使用者的郵件。

> [!NOTE]
> 系統可能會在您增加範圍數目時，提示使用者同意其他事項。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>下一步

在我們的多個部分情節系列中，深入了解如何在 Microsoft 身分識別平台上開發單頁應用程式 (SPA)。

> [!div class="nextstepaction"]
> [案例：單頁應用程式](scenario-spa-overview.md)
