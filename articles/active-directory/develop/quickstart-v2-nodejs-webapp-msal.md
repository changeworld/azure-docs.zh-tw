---
title: 快速入門：將驗證新增至具有 MSAL 節點的節點 Web 應用程式 |Azure
titleSuffix: Microsoft identity platform
description: 在本快速入門中，您會了解如何使用 Node.js Web 應用程式和適用於 Node.js 的 Microsoft 驗證程式庫 (MSAL) 實作驗證。
services: active-directory
author: amikuma
manager: saeeda
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/22/2020
ms.author: amikuma
ms.custom: aaddev, scenarios:getting-started, languages:js, devx-track-js
ms.openlocfilehash: cfe1b66662d73c18934ef9ecc884adee79493eb6
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92640974"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-node-web-app-using-the-auth-code-flow"></a>快速入門：使用授權碼流程讓使用者登入並取得 Node Web 應用程式中的存取權杖

在本快速入門中，您會執行程式碼範例，該範例會示範 Node Web 應用程式如何使用授權碼流程讓使用者登入個人帳戶、公司帳戶和學校帳戶。 此程式碼範例也會示範如何取得存取權杖來呼叫 Web API (在此案例中為 Microsoft Graph API)。 如需圖例，請參閱[此範例的運作方式](#how-the-sample-works)。

本快速入門會使用適用於 Node.js 的 Microsoft 驗證程式庫 (MSAL Node) 與授權碼流程。

> [!IMPORTANT]
> MSAL Node [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶 - [免費建立 Azure 訂用帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) 或其他程式碼編輯器

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>註冊並下載快速入門應用程式
>
> #### <a name="step-1-register-your-application"></a>步驟 1:註冊您的應用程式
>
> 1. 登入 [Azure 入口網站](https://portal.azure.com)。
> 1. 如果您的帳戶可讓您存取多個租用戶，請在右上方選取帳戶，然後將您的入口網站工作階段設定為想要使用的 Azure AD 租用戶。
> 1. 選取 [應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)。
> 1. 選取 [新增註冊]。
> 1. [註冊應用程式] 頁面出現時，輸入您應用程式的名稱。
> 1. 在 [支援的帳戶類型] 底下，選取 [任何組織目錄中的帳戶及個人的 Microsoft 帳戶]。
> 1. 將 [重新導向 URI] 值設定為 `http://localhost:3000/redirect`。
> 1. 選取 [註冊]。 
> 1. 在應用程式 [概觀] 頁面上，記下 [應用程式 (用戶端) 識別碼] 值以供稍後使用。
> 1. 在 **憑證與祕密** 下，選取 [新增用戶端密碼]。  將描述和預設到期日留白，然後按一下 [新增]。
> 1. 請注意 **用戶端密碼** 的 **值** ，以供稍後使用。

#### <a name="step-2-download-the-project"></a>步驟 2:下載專案

> [!div renderon="docs"]
> 若要使用 Node.js 搭配網頁伺服器來執行專案，請[下載核心專案檔](https://github.com/Azure-Samples/ms-identity-node/archive/main.zip)。

> [!div renderon="portal" class="sxs-lookup"]
> 使用 Node.js 以網頁伺服器執行專案

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [下載程式碼範例](https://github.com/Azure-Samples/ms-identity-node/archive/main.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-node-app"></a>步驟 3：設定您的 Node 應用程式
>
> 將專案解壓縮，然後開啟資料夾 ms-identity-node-main，然後開啟 index.js 檔案。
> 使用`clientID`應用程式 (用戶端) 識別碼 **設定** 。
> 使用 **用戶端密碼** 的 **值** 設定 `clientSecret`。
>
>```javascript
>const config = {
>    auth: {
>        clientId: "Enter_the_Application_Id_Here",
>        authority: "https://login.microsoftonline.com/common",
>        clientSecret: "Enter_the_Client_Secret_Here"
>    },
>    system: {
>        loggerOptions: {
>            loggerCallback(loglevel, message, containsPii) {
>                console.log(message);
>            },
>            piiLoggingEnabled: false,
>            logLevel: msal.LogLevel.Verbose,
>        }
>    }
>};
> ```

> [!div renderon="docs"]
>
> 修改 `config` 區段中的值，如下所述：
>
> - `Enter_the_Application_Id_Here` 是您註冊的應用程式所具備的 **應用程式 (用戶端) 識別碼** 。
> - `Enter_the_Client_Secret_Here` 是您註冊的應用程式之 **用戶端密碼** 的 **值** 。
>
> 預設 `authority` 值代表主要 (全域) Azure 雲端：
>
> ```javascript
> authority: "https://login.microsoftonline.com/common",
> ```
>
> > [!TIP]
> > 若要尋找 **應用程式 (用戶端) 識別碼** 的值，請在 Azure 入口網站中移至應用程式註冊的 **概觀** 頁面。 前往 **憑證與祕密** 下取得或產生新的 **用戶端密碼** 。
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>步驟 3：您的應用程式已設定並準備好執行
>
> [!div renderon="docs"]
>
> #### <a name="step-4-run-the-project"></a>步驟 4：執行專案

使用 Node.js 執行專案：

1. 若要啟動伺服器，請從專案目錄執行下列命令：
    ```console
    npm install
    npm start
    ```
1. 瀏覽至 `http://localhost:3000/`。

1. 選取 **登入** 以啟動登入程序。

    第一次登入時，系統會提示您同意允許應用程式存取您的設定檔，並將您登入。 成功登入之後，您會在命令列中看到一則記錄訊息。

## <a name="more-information"></a>詳細資訊

### <a name="how-the-sample-works"></a>此範例的運作方式

執行範例時，系統會在 localhost 連接埠 3000 上裝載 Web 伺服器。 當網頁瀏覽器存取此網站時，此範例會立即將使用者重新導向至 Microsoft 驗證頁面。 因此，此範例不會包含任何 html 或顯示元素。 驗證成功會顯示 "OK" 訊息。

### <a name="msal-node"></a>MSAL Node

MSAL Node 程式庫會登入使用者並要求權杖，該權杖是用來存取受 Microsoft 身分識別平台保護的 API。 您可以利用 Node.js 套件管理員 (npm) 下載最新版本：

```console
npm install @azure/msal-node
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [將驗證新增至現有的 Web 應用程式 - GitHub 程式碼範例 >](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-node-samples/standalone-samples/auth-code/readme.md)
