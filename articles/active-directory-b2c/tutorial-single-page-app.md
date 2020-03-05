---
title: 教學課程：在單頁應用程式中啟用驗證
titleSuffix: Azure AD B2C
description: 在本教學課程中，了解如何使用 Azure Active Directory B2C 為 JavaScript 型單頁應用程式 (SPA) 提供使用者登入。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/14/2019
ms.custom: mvc, seo-javascript-september2019
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 435800d9c6bfd9131d50681a9808f9836104fac0
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183328"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-using-azure-active-directory-b2c-azure-ad-b2c"></a>教學課程：使用 Azure Active Directory B2C (Azure AD B2C) 在單頁應用程式中啟用驗證

本教學課程將說明如何使用 AAzure Active Directory B2C (Azure AD B2C) 在單頁應用程式 (SPA) 中登入和註冊使用者。 Azure AD B2C 可讓您的應用程式使用開放式標準通訊協定向社交帳戶、企業帳戶和 Azure Active Directory 帳戶進行驗證。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 在 Azure AD B2C 中更新應用程式
> * 設定範例以使用應用程式
> * 利用使用者流程註冊

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

您需要先備妥下列 Azure AD B2C 資源，才能繼續本教學課程中的步驟：

* [Azure AD B2C 租用戶](tutorial-create-tenant.md)
* 您租用戶中[註冊的應用程式](tutorial-register-applications.md)
* 您租用戶中[建立的使用者流程](tutorial-create-user-flows.md)

此外，您的開發環境中需要下列項目：

* 程式碼編輯器，例如 [Visual Studio Code](https://code.visualstudio.com/) 或 [Visual Studio 2019](https://www.visualstudio.com/downloads/)
* [.NET Core SDK 2.2](https://dotnet.microsoft.com/download) 或更新版本
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>更新應用程式

在已完成的第二個必要教學課程中，您在 Azure AD B2C 中註冊了 Web 應用程式。 若要啟用範例與教學課程中的通訊，您需要新增 Azure AD B2C 中應用程式的重新導向 URI。

您可以使用目前的 [應用程式]  體驗，或使用新整合的 [應用程式註冊 (預覽)]  體驗來更新應用程式。 [深入了解新的體驗](https://aka.ms/b2cappregintro)。

#### <a name="applications"></a>[應用程式](#tab/applications/)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取頂端功能表中的 [目錄 + 訂用帳戶]  篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
1. 選取 Azure 入口網站左上角的 [所有服務]  ，然後搜尋並選取 [Azure AD B2C]  。
1. 選取 [應用程式]  ，然後選取 [webapp1]  應用程式。
1. 在 [回覆 URL]  下方，新增 `http://localhost:6420`。
1. 選取 [儲存]  。
1. 在 [屬性] 頁面上，記錄 [應用程式識別碼]  。 當您在單頁 Web 應用程式中更新程式碼時，您可在稍後步驟中使用應用程式識別碼。

#### <a name="app-registrations-preview"></a>[應用程式註冊 (預覽)](#tab/app-reg-preview/)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶]  篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]  。 或者，選取 [所有服務]  ，然後搜尋並選取 [Azure AD B2C]  。
1. 選取 [應用程式註冊 (預覽)]  ，選取 [擁有的應用程式]  索引標籤，然後選取 *webapp1* 應用程式。
1. 選取 [驗證]  ，然後選取 [試用全新體驗]  (若已顯示)。
1. 在 [Web]  底下，選取 [新增 URI]  連結，輸入 `http://localhost:6420`，然後選取 [儲存]  。
1. 選取 [概觀]  。
1. 當您在單頁 Web 應用程式中更新程式碼時，記錄 [應用程式 (用戶端) 識別碼]  ，以供後續步驟使用。

* * *

## <a name="get-the-sample-code"></a>取得範例程式碼

在本教學課程中，您會設定您從 GitHub 下載的程式碼範例。 此範例會示範單頁應用程式如何使用 Azure AD B2C 進行使用者註冊、登入及呼叫受保護的 Web API。

[下載 zip 檔案](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip)，或從 GitHub 複製範例。

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>更新範例

既然您已取得範例，請使用您的 Azure AD B2C 租用戶名稱和您在先前步驟中記錄的應用程式識別碼更新程式碼。

1. 開啟範例根目錄中的 `index.html` 檔案。
1. 在 `msalConfig` 定義中，以您在先前步驟中記錄的應用程式識別碼修改 **clientId** 值。 接下來，以您的 Azure AD B2C 租用戶名稱更新 **authority** URI 值。 此外，以您在其中一個必要條件中建立的註冊/登入使用者流程名稱 (例如 *B2C_1_signupsignin1*) 更新 URI。

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "00000000-0000-0000-0000-000000000000", //This is your client ID
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi", //This is your tenant info
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    本教學課程中使用的使用者流程名稱是 **B2C_1_signupsignin1**。 如果您使用不同的使用者流程名稱，請在 `authority` 值中指定該名稱。

## <a name="run-the-sample"></a>執行範例

1. 開啟主控台視窗並變更至包含範例的目錄。 例如：

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```
1. 執行下列命令：

    ```
    npm install && npm update
    node server.js
    ```

    主控台視窗會顯示在本機執行的 Node.js 伺服器的連接埠號碼：

    ```
    Listening on port 6420...
    ```

1. 在瀏覽器中移至 `http://localhost:6420` 可檢視應用程式。

此範例支援註冊、登入、設定檔編輯和密碼重設。 本教學課程特別說明使用者如何以電子郵件地址註冊。

### <a name="sign-up-using-an-email-address"></a>使用電子郵件地址註冊

> [!WARNING]
> 註冊或登入之後，您可能會看到[權限不足錯誤](#error-insufficient-permissions)。 由於程式碼範例的目前實作，因此預期會發生此錯誤。 此問題將在程式碼範例的未來版本中解決，屆時將會移除此警告。

1. 選取 [登入]  ，以起始您在先前的步驟中指定的 *B2C_1_signupsignin1* 使用者流程。
1. Azure AD B2C 會顯示含有註冊連結的登入頁面。 由於您還沒有帳戶，因此請選取 [立即註冊]  連結。
1. 註冊工作流程會顯示一個使用電子郵件地址來收集並驗證使用者身分識別的頁面。 註冊工作流程也會收集使用者的密碼，以及在使用者流程中定義的要求屬性。

    請使用有效的電子郵件地址，並使用驗證碼進行驗證。 設定密碼。 輸入要求的屬性值。

    ![登入/註冊使用者流程所顯示的註冊頁面](./media/tutorial-single-page-app/azure-ad-b2c-sign-up-workflow.png)

1. 選取 [建立]  ，在 Azure AD B2C 目錄中建立本機帳戶。

當您選取 [建立]  時，登入頁面會關閉後再度出現。

您現在可以使用您的電子郵件地址和密碼來登入應用程式。

### <a name="error-insufficient-permissions"></a>錯誤：權限不足

登入之後，應用程式可能會傳回權限不足錯誤：

```Output
ServerError: AADB2C90205: This application does not have sufficient permissions against this web resource to perform the operation.
Correlation ID: ce15bbcc-0000-0000-0000-494a52e95cd7
Timestamp: 2019-07-20 22:17:27Z
```

由於 Web 應用程式嘗試存取的 Web API 受到示範目錄 *fabrikamb2c* 保護，因此您會收到此錯誤。 由於您的存取權杖只對您的 Azure AD 目錄有效，所以此 API 呼叫未經授權。

若要修正此錯誤，請繼續進行本系列中的下一個教學課程 (請參閱[後續步驟](#next-steps))，為您的目錄建立受保護的 Web API。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何：

> [!div class="checklist"]
> * 在 Azure AD B2C 中更新應用程式
> * 設定範例以使用應用程式
> * 利用使用者流程註冊

現在移至本系列的下一個教學課程，以授與從 SPA 存取受保護 Web API 的權限：

> [!div class="nextstepaction"]
> [教學課程：使用 Azure AD B2C 授與從 SPA 存取 ASP.NET Core Web API 的權限 >](tutorial-single-page-app-webapi.md)
