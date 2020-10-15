---
title: 教學課程：在單頁應用程式中啟用驗證
titleSuffix: Azure AD B2C
description: 在本教學課程中，了解如何使用 Azure Active Directory B2C 為 JavaScript 型單頁應用程式 (SPA) 提供使用者登入。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc, seo-javascript-september2019, devx-track-js
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 42c2ca777a999a4d4387646110ed88af84631183
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91258893"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-with-azure-ad-b2c"></a>教學課程：在單頁應用程式中透過 Azure AD B2C 啟用驗證

本教學課程將說明如何使用 AAzure Active Directory B2C (Azure AD B2C) 在單頁應用程式 (SPA) 中註冊和登入使用者。

在本教學課程中，首先在兩部分系列中要進行第一步為：

> [!div class="checklist"]
> * 將回覆 URL 新增至在您 Azure AD B2C 租用戶中註冊的應用程式
> * 從 GitHub 下載範例程式碼
> * 修改範例應用程式的程式碼以使用您的租用戶
> * 使用您的註冊/登入使用者流程註冊

此系列中的[下一個教學課程](tutorial-single-page-app-webapi.md)會啟用程式碼範例的 Web API 部分。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件

您需要先備妥下列 Azure AD B2C 資源，才能繼續本教學課程中的步驟：

* [Azure AD B2C 租用戶](tutorial-create-tenant.md)
* 您租用戶中[註冊的應用程式](tutorial-register-applications.md)
* 您租用戶中[建立的使用者流程](tutorial-create-user-flows.md)

此外，您的開發環境中需要下列項目：

* [Visual Studio Code](https://code.visualstudio.com/) 或其他程式碼編輯器
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>更新應用程式

在已完成的第二個必要教學課程中，您在 Azure AD B2C 中註冊了 Web 應用程式。 若要啟用本教學課程中範例程式碼的通訊，請在應用程式註冊中新增回覆 URL (也稱為重新導向 URI)。

若要更新 Azure AD B2C 租用戶中的應用程式，您可以使用我們全新整合的**應用程式註冊**體驗，或使用舊版**應用程式 (舊版)** 體驗。 [深入了解新的體驗](https://aka.ms/b2cappregtraining)。

#### <a name="app-registrations"></a>[應用程式註冊](#tab/app-reg-ga/)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶] 篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]。 或者，選取 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 選取 [應用程式註冊]、選取 [擁有的應用程式] 索引標籤，然後選取 [webapp1] 應用程式。
1. 在 [Web] 底下，選取 [新增 URI] 連結，輸入 `http://localhost:6420`。
1. 在 [隱含授與] 底下，選取 [存取權杖] 和 [識別碼權杖] 的核取方塊，然後選取 [儲存]。
1. 選取 [概觀]。
1. 當您在單頁 Web 應用程式中更新程式碼時，記錄 [應用程式 (用戶端) 識別碼]，以供後續步驟使用。

#### <a name="applications-legacy"></a>[應用程式 (舊版)](#tab/applications-legacy/)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取頂端功能表中的 [目錄 + 訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
1. 選取 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 選取 [應用程式 (舊版)]，然後選取 [webapp1] 應用程式。
1. 在 [回覆 URL] 下方，新增 `http://localhost:6420`。
1. 選取 [儲存]。
1. 在 [屬性] 頁面上，記錄 [應用程式識別碼]。 當您在單頁 Web 應用程式中更新程式碼時，您可在稍後步驟中使用應用程式識別碼。

* * *

## <a name="get-the-sample-code"></a>取得範例程式碼

在本教學課程中，您會設定從 GitHub 下載以與 B2C 租用戶搭配使用的程式碼範例。 此範例會示範單頁應用程式如何使用 Azure AD B2C 進行使用者註冊、登入及呼叫受保護的 Web API (您會在本系列中的下個教學課程啟用 Web API)。

[下載 zip 檔案](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip)，或從 GitHub 複製範例。

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>更新範例

既然您已取得範例，請使用您的 Azure AD B2C 租用戶名稱和您在先前步驟中記錄的應用程式識別碼更新程式碼。

1. 開啟 JavaScriptSPA 資料夾內的 authConfig.js 檔案。
1. 在 `msalConfig` 物件中，更新：
    * `clientId`，以您在先前步驟中記錄的**應用程式 (用戶端) 識別碼**作為值
    * *URI，以具有您 Azure AD B2C 租用戶名稱和建立為一部分必要條件的註冊/登入使用者流程名稱 (例如 `authority`B2C_1_signupsignin1*) 。

    ```javascript
    const msalConfig = {
        auth: {
          clientId: "00000000-0000-0000-0000-000000000000", // Replace this value with your Application (client) ID
          authority: b2cPolicies.authorities.signUpSignIn.authority,
          validateAuthority: false
        },
        cache: {
          cacheLocation: "localStorage",
          storeAuthStateInCookie: true
        }
    };

    const loginRequest = {
       scopes: ["openid", "profile"],
    };

    const tokenRequest = {
      scopes: apiConfig.b2cScopes // i.e. ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"]
    };
    ```

## <a name="run-the-sample"></a>執行範例

1. 開啟主控台視窗並變更至包含範例的目錄。 例如：

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```
1. 執行下列命令：

    ```console
    npm install && npm update
    npm start
    ```

    主控台視窗會顯示在本機執行的 Node.js 伺服器的連接埠號碼：

    ```console
    Listening on port 6420...
    ```
1. 瀏覽至 `http://localhost:6420`，以檢視在本機電腦上執行的 Web 應用程式。

    :::image type="content" source="media/tutorial-single-page-app/web-app-spa-01-not-logged-in.png" alt-text="網頁瀏覽器顯示在本機執行的單頁應用程式":::

### <a name="sign-up-using-an-email-address"></a>使用電子郵件地址註冊

這個範例應用程式支援註冊、登入和密碼重設。 在本教學課程中，您會使用電子郵件地址來註冊。

1. 選取 [登入]，以起始您在先前的步驟中指定的 *B2C_1_signupsignin1* 使用者流程。
1. Azure AD B2C 會顯示包含註冊連結的登入頁面。 由於您還沒有帳戶，因此請選取 [立即註冊] 連結。
1. 註冊工作流程會顯示一個使用電子郵件地址來收集並驗證使用者身分識別的頁面。 註冊工作流程也會收集使用者的密碼，以及在使用者流程中定義的要求屬性。

    請使用有效的電子郵件地址，並使用驗證碼進行驗證。 設定密碼。 輸入要求的屬性值。

    :::image type="content" source="media/tutorial-single-page-app/user-flow-sign-up-workflow-01.png" alt-text="網頁瀏覽器顯示在本機執行的單頁應用程式":::

1. 選取 [建立]，在 Azure AD B2C 目錄中建立本機帳戶。

當您選取 [建立] 時，應用程式會顯示已登入之使用者的名稱。

:::image type="content" source="media/tutorial-single-page-app/web-app-spa-02-logged-in.png" alt-text="網頁瀏覽器顯示在本機執行的單頁應用程式":::

如果想測試登入，請選取 [登出] 按鈕，然後選取 [登入]，然後以您註冊時所輸入的電子郵件地址和密碼登入。

### <a name="what-about-calling-the-api"></a>該如何呼叫 API？

如果在登入後選取 [呼叫 API] 按鈕，則系統會顯示註冊/登入使用者流程頁面，而不是 API 呼叫的結果。 這是預期的情況，因為您尚未設定應用程式的 API 部分，無法與 Azure AD B2C 租用戶中註冊的 Web API 應用程式進行通訊。

此時，應用程式仍在嘗試與示範租用戶 (fabrikamb2c.onmicrosoft.com) 中註冊的 API 通訊，而因為您未使用該租用戶進行驗證，所以會顯示註冊/登入頁面。

繼續進行中的系列中的下一個教學課程，以啟用受保護的 API (請參閱[後續步驟](#next-steps)一節)。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已設定單頁應用程式，以在 Azure AD B2C 租用戶中使用使用者流程，以提供註冊和登入功能。 您已完成下列步驟：

> [!div class="checklist"]
> * 已將回覆 URL 新增到在您 Azure AD B2C 租用戶中註冊的應用程式
> * 已從 GitHub 下載範例程式碼
> * 已修改範例應用程式的程式碼以使用您的租用戶
> * 已使用您的註冊/登入使用者流程註冊

現在移至本系列的下一個教學課程，以授與從 SPA 存取受保護 Web API 的權限：

> [!div class="nextstepaction"]
> [教學課程：從單頁應用程式保護並授與 Web API 的存取權限 >](tutorial-single-page-app-webapi.md)
