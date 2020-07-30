---
title: 教學課程：使用 Azure AD B2C 保護 node.js Web API 並授與單頁應用程式 (SPA) 的存取權
titleSuffix: Azure AD B2C
description: 在本教學課程中，了解如何使用 Active Directory B2C 來保護 Node.js Web API，並從單頁應用程式呼叫該 API。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc, devx-track-javascript
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: f01ef1a4cf5bc5b805da3dd4d825ef17f81ce53e
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87170202"
---
# <a name="tutorial-protect-and-grant-access-to-a-nodejs-web-api-from-a-single-page-application-with-azure-ad-b2c"></a>教學課程：使用 Azure AD B2C 從單頁應用程式保護並授與 Node.js Web API 的存取權限

本教學課程將說明如何從單頁應用程式呼叫 Azure Active Directory B2C (Azure AD B2C) 所保護的 Node.js Web API。

在本教學課程中，首先在兩部分系列中要進行第二步為：

> [!div class="checklist"]
> * 在 Azure AD B2C 租用戶中建立 Web API 應用程式註冊
> * 設定 Web API 的範圍
> * 授與 Web API 的權限
> * 修改 Web API 程式碼範例以與您的租用戶搭配使用

在此系列的[第一個教學課程](tutorial-single-page-app.md)中，您已下載程式代碼範例，並修改為在 Azure AD B2C 租用戶中以使用者流程的方式登入使用者。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

* 完成以下教學課程中的步驟和必要條件：[教學課程：在單頁應用程式中透過 Azure AD B2C 啟用驗證](tutorial-single-page-app.md)
* [Visual Studio Code](https://code.visualstudio.com/) 或其他程式碼編輯器
* [Node.js](https://nodejs.org/en/download/)

## <a name="add-a-web-api-application"></a>新增 Web API 應用程式

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>設定範圍

範圍可用來控管對受保護資源的存取。 Web API 可使用範圍來實作以範圍為基礎的存取控制。 例如，有些使用者可能同時具有讀取和寫入權限，而有些則可能只有唯讀權限。 在本教學課程中，您會定義 Web API 的讀取和寫入權限。

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

在設定單頁應用程式時，請記錄 `demo.read` 範圍的 [範圍]  下的值，以供後續步驟使用。 完整範圍值會類似於 `https://contosob2c.onmicrosoft.com/api/demo.read`。

## <a name="grant-permissions"></a>授與權限

若要從另一個應用程式呼叫受保護的 Web API，您必須為該應用程式授與對 Web API 的權限。

在必要條件教學課程中，您已建立名為 *webapp1* 的 Web 應用程式。 在本教學課程中，您會設定該應用程式，以呼叫您在上一節中建立的 Web API，即 *webapi1*。

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

系統現在已將所指定範圍之受保護 Web API 的權限授與您的單頁 web 應用程式。 使用者會向 Azure AD B2C 進行驗證，以使用該單頁應用程式。 單頁應用程式會使用授權授與流程，以 Azure AD B2C 傳回的存取權杖來存取受保護的 Web API。

## <a name="configure-the-sample"></a>設定範例

現在 Web API 已註冊且已定義範圍，您可設定 Web API 程式碼以使用您的 Azure AD B2C 租用戶。 在本教學課程中，您會設定從 GitHub 下載的範例 Node.js Web 應用程式。

[下載 \*.zip 封存檔](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip)，或從 GitHub 複製範例 Web API 專案。 您也可以直接瀏覽至 GitHub 上的 [Azure-Samples/active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) 專案。

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="configure-the-web-api"></a>設定 Web API

1. 在程式碼編輯器中開啟 config.js  檔案。
1. 修改變數值，以反映您稍早建立的應用程式註冊。 也請使用您建立為必要條件一部分的使用者流程更新 `policyName`。 例如，B2C_1_signupsignin1  。

    ```javascript
    const clientID = "<your-webapi-application-ID>"; // Application (client) ID
    const b2cDomainHost = "<your-tenant-name>.b2clogin.com";
    const tenantId = "<your-tenant-ID>.onmicrosoft.com"; // Alternatively, you can use your Directory (tenant) ID (a GUID)
    const policyName = "B2C_1_signupsignin1";
    ```

#### <a name="enable-cors"></a>啟用 CORS

若要允許單頁應用程式呼叫 Node.js Web API，您必須在 Web API 中啟用 [CORS](https://expressjs.com/en/resources/middleware/cors.html)。 在實際執行的應用程式中，您應該特別注意提出要求的網域；但在本教學課程中，允許來自任何網域的要求。

若要啟用 CORS，請使用下列中介軟體。 在本教學課程的 node.js Web API 程式碼範例中，已經將其加入 index.js  檔案中。

```javascript
app.use((req, res, next) => {
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "Authorization, Origin, X-Requested-With, Content-Type, Accept");
    next();
});
```

### <a name="configure-the-single-page-application"></a>設定單頁應用程式

本系列[上一個教學課程](tutorial-single-page-app.md)中的單頁應用程式 (SPA)，會使用 Azure AD B2C 進行使用者註冊、登入，並依預設呼叫由 frabrikamb2c  示範租用戶保護的 Node.js Web API。

在本節中，您會更新單頁 Web 應用程式，以呼叫由「您的」  Azure AD B2C 租用戶保護、且由您在本機電腦上執行的 Node.js Web API。

若要變更 SPA 中的設定：

1. 從您在上一個教學課程下載或複製的 [active-directory-b2c-javascript-msal-singlepageapp][github-js-spa] 專案中，開啟 JavaScriptSPA  中的 apiConfig.js  檔案。
1. 使用您先前建立的 *demo.read* 範圍的 URI 以及 Web API 的 URL 來設定範例。
    1. 在 `apiConfig` 定義中，將 `b2cScopes` 值取代為 demo.read  範圍的完整 URI (您先前記錄的**範圍**值)。
    1. 將 `webApi` 值中的網域變更為您在先前步驟中註冊 Web API 應用程式時所新增的重新導向 URI。

    因為 API 可在 `/hello` 端點存取，所以請將 */hello* 保留在 URI 中。

    `apiConfig` 定義應該會類似下列程式碼區塊所示，但會以您的 B2C 租用戶名稱取代 `<your-tenant-name>`：

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/hello" // '/hello' should remain in the URI
    };
    ```

## <a name="run-the-spa-and-web-api"></a>執行 SPA 和 Web API

您現在已經準備好測試單頁應用程式對 API 的範圍存取權。 在本機電腦上同時執行 Node.js Web API 和範例 JavaScript 單頁應用程式。 然後，登入單頁應用程式，並選取 [呼叫 API]  按鈕以對受保護的 API 起始要求。

雖然這兩個應用程式在您進行本教學課程時都在本機執行，但您已將其設定為使用 Azure AD B2C 進行安全的註冊/登入，並對受保護的 Web API 授與存取權。

### <a name="run-the-nodejs-web-api"></a>執行 Node.js Web API

1. 開啟主控台視窗，並變更至包含 Node.js Web API 範例的目錄。 例如：

    ```console
    cd active-directory-b2c-javascript-nodejs-webapi
    ```

1. 執行下列命令：

    ```console
    npm install && npm update
    node index.js
    ```

    主控台視窗會顯示裝載應用程式的連接埠號碼。

    ```console
    Listening on port 5000...
    ```

### <a name="run-the-single-page-app"></a>執行單頁應用程式

1. 開啟另一個主控台視窗，並變更至包含 JavaScript SPA 範例的目錄。 例如：

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```

1. 執行下列命令：

    ```console
    npm install && npm update
    npm start
    ```

    主控台視窗會顯示裝載應用程式的連接埠號碼。

    ```console
    Listening on port 6420...
    ```

1. 在瀏覽器中瀏覽至 `http://localhost:6420` 可檢視應用程式。

    ![顯示在瀏覽器中的單一頁面應用程式範例應用程式](./media/tutorial-single-page-app-webapi/tutorial-01-sample-app-browser.png)

1. 使用您在[上一個教學課程](tutorial-single-page-app.md)中使用的電子郵件地址和密碼登入。 成功登入時，您應該會看到 `User 'Your Username' logged-in` 訊息。
1. 選取 [呼叫 API]  按鈕。 SPA 會從 Azure AD B2C 取得授權，然後存取受保護的 Web API 以顯示已登使用者的名稱：

    ![瀏覽器中的單頁應用程式，顯示 API 所傳回的使用者名稱 JSON 結果](./media/tutorial-single-page-app-webapi/tutorial-02-call-api.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您：

> [!div class="checklist"]
> * 在 Azure AD B2C 租用戶中建立了 Web API 應用程式註冊
> * 設定了 Web API 的範圍
> * 授與了 Web API 的權限
> * 修改了 Web API 程式碼範例以與您的租用戶搭配使用

現在您已了解 SPA 如何向受保護的 Web API 要求資源，接下來請深入了解這些應用程式類型彼此之間以及與 Azure AD B2C 的互動方式。

> [!div class="nextstepaction"]
> [可以在 Active Directory B2C 中使用的應用程式類型 >](application-types.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
