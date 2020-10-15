---
title: 教學課程：授與從桌面應用程式存取 Node.js Web API 的權限
description: 關於如何使用 Active Directory B2C 來保護 Node.js Web API，以及如何從 .NET 傳統型應用程式加以呼叫的教學課程。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 8ebfbeeb4533f21bc0fa10a5fee7b88ef069c262
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84298837"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>教學課程：使用 Azure Active Directory B2C 授與從傳統型應用程式存取 Node.js Web API 的權限

本教學課程將說明如何從 Windows Presentation Foundation (WPF) 傳統型應用程式呼叫 Azure Active Directory B2C (Azure AD B2C) 所保護的 Node.js Web API (也受 Azure AD B2C 保護)。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 新增 Web API 應用程式
> * 設定 Web API 的範圍
> * 授與 Web API 的權限
> * 更新範例以使用應用程式

## <a name="prerequisites"></a>必要條件

完成以下教學課程中的步驟和必要條件：[教學課程：在原生桌面用戶端中驗證使用者](tutorial-desktop-app.md)。

## <a name="add-a-web-api-application"></a>新增 Web API 應用程式

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>設定範圍

範圍可用來控管對受保護資源的存取。 Web API 可使用範圍來實作以範圍為基礎的存取控制。 例如，有些使用者可能同時具有讀取和寫入權限，而有些則可能只有唯讀權限。 在本教學課程中，您會定義 Web API 的讀取和寫入權限。

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

在設定傳統型應用程式時，請記錄 `demo.read` 範圍的 [範圍] 之下的值，以供後續步驟使用。 完整範圍值會類似於 `https://contosob2c.onmicrosoft.com/api/demo.read`。

## <a name="grant-permissions"></a>授與權限

若要從原生用戶端應用程式呼叫受保護的 Web API，您必須將已註冊的原生用戶端應用程式權限授與給您在 Azure AD B2C 中註冊的 Web API。

在必要的教學課程中，您已註冊名為 *nativeapp1* 的原生用戶端應用程式。 下列步驟會使用您在上一節中為 *webapi1* 公開的 API 範圍來設定該原生應用程式註冊。 這可讓傳統型應用程式從 Azure AD B2C 取得存取權杖，Web API 可將它用於驗證和提供其資源的有限範圍存取權。 您稍後會在本教學課程中設定及執行傳統型應用程式和 Web API 程式碼範例。

若要在您的 Azure AD B2C 租用戶中註冊應用程式，您可以使用我們新的整合**應用程式註冊**體驗，或使用舊版**應用程式 (舊版)** 體驗。 [深入了解新的體驗](https://aka.ms/b2cappregtraining)。

#### <a name="app-registrations"></a>[應用程式註冊](#tab/app-reg-ga/)

1. 選取 [應用程式註冊]，然後選取應可存取 API 的原生用戶端應用程式。 例如，*nativeapp1*。
1. 在 [管理] 之下選取 [API 權限]。
1. 在 [已設定的權限] 底下，選取 [新增權限]。
1. 選取 [我的 API] 索引標籤。
1. 選取原生用戶端應用程式應獲得其存取權的 API。 例如，*webapi1*。
1. 在 [權限] 下，展開 [示範]，然後選取先前定義的範圍。 例如 *demo.read* 和 *demo.write*。
1. 選取 [新增權限]。 依照指示，稍等幾分鐘，然後再繼續進行下一個步驟。
1. 選取 [授與管理員同意 (您的租用戶名稱)]。
1. 選取您目前登入的系統管理員帳戶，或使用 Azure AD B2C 租用戶中的帳戶 (該租用戶至少已指派「雲端應用程式管理員」角色) 來登入。
1. 選取 [接受]。
1. 選取 [重新整理]，然後確認 [授與...] 出現在這個範圍的 [狀態] 下方。 權限可能需要幾分鐘的時間來傳播。

#### <a name="applications-legacy"></a>[應用程式 (舊版)](#tab/applications-legacy/)

1. 選取 [應用程式 (舊版)]，然後選取 [nativeapp1]。
1. 選取 [API 存取]，然後選取 [新增]。
1. 在 [選取 API] 下拉式清單中，選取 [webapi1]。
1. 在 [選取範圍] 下拉式清單中，選取您先前定義的範圍。 例如 *demo.read* 和 *demo.write*。
1. 選取 [確定]。

* * *

使用者會向 Azure AD B2C 進行驗證以使用該 WPF 傳統型應用程式。 該傳統型應用程式會從 Azure AD B2C 取得授權授與，以存取受保護的 Web API。

## <a name="configure-the-samples"></a>設定範例

既然已註冊 Web API 並已設定範圍和權限，您可將傳統型應用程式和 Web API 範例設定為使用您的 Azure AD B2C 租用戶。

### <a name="update-the-desktop-application"></a>更新傳統型應用程式

在本文的必要條件中，您修改了 [WPF 傳統型應用程式](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop)，能夠在 Azure AD B2C 租用戶中透過使用者流程登入。 在這一節中，您會將該相同應用程式更新為參考您稍早註冊的 Web API (*webapi1*)。

1. 在 Visual Studio 中開啟 **active-directory-b2c-wpf** 方案 (`active-directory-b2c-wpf.sln`)。
1. 在 **active-directory-b2c-wpf** 專案中，開啟 *App.xaml.cs* 檔案，並尋找以下變數定義。
    1. 將 `ApiScopes` 變數的值取代為您稍早在定義 **demo.read**範圍時所記錄的值。
    1. 將 `ApiEndpoint` 變數的值取代為您稍早在租用戶中註冊 Web API (例如，*webapi1*) 時所記錄的 [重新導向 URI]。

    以下是範例：

    ```csharp
    public static string[] ApiScopes = { "https://contosob2c.onmicrosoft.com/api/demo.read" };
    public static string ApiEndpoint = "http://localhost:5000";
    ```

### <a name="get-and-update-the-nodejs-api-sample"></a>取得並更新 Node.js API 範例

接下來，從 GitHub 取得 Node.js Web API 程式碼範例，並將其設定為使用您在 Azure AD B2C 租用戶中註冊的 Web API。

[下載 zip 檔案](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip)，或從 GitHub 複製範例 Web 應用程式。

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

Node.js Web API 範例會使用 Passport.js 程式庫，讓 Azure AD B2C 能夠保護 API 的呼叫。

1. 開啟 `index.js` 檔案。
1. 請使用下列值來更新這些變數定義。 將 `<web-API-application-ID>` 變更為您稍早註冊之 Web API (*webapi1*) 的 [應用程式 (用戶端) 識別碼]。 將 `<your-b2c-tenant>` 變更為 Azure AD B2C 租用戶的名稱。

    ```nodejs
    var clientID = "<web-API-application-ID>";
    var b2cDomainHost = "<your-b2c-tenant>.b2clogin.com";
    var tenantIdGuid = "<your-b2c-tenant>.onmicrosoft.com";
    var policyName = "B2C_1_signupsignin1";
    ```
1. 由於您在本機執行 API，請將 GET 方法的路由中的路徑更新為 `/`，而不是示範應用程式的位置 `/hello`：

    ```nodejs
    app.get("/",
    ```

## <a name="run-the-samples"></a>執行範例

### <a name="run-the-nodejs-web-api"></a>執行 Node.js Web API

1. 啟動 Node.js 命令提示字元。
2. 切換至包含 Node.js 範例的目錄。 例如 `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. 執行下列命令：
    ```console
    npm install && npm update
    ```
    ```console
    node index.js
    ```

### <a name="run-the-desktop-application"></a>執行桌面應用程式

1. 在 Visual Studio 中開啟 **active-directory-b2c-wpf** 方案。
2. 按 **F5** 以執行傳統型應用程式。
3. 使用[在傳統型應用程式中使用 Azure Active Directory B2C 進行使用者驗證](tutorial-desktop-app.md)教學課程中使用的電子郵件地址和密碼進行登入。
4. 選取 [呼叫 API] 按鈕。

傳統型應用程式會對本機執行的 Web API 提出要求，並且在驗證有效的存取權杖時，顯示已登入使用者的顯示名稱。

![顯示在 WPF 傳統型應用程式頂端窗格中的顯示名稱](./media/tutorial-desktop-app-webapi/desktop-app-01-post-api-call.png)

您的傳統應用程式 (受 Azure AD B2C 保護) 會呼叫在本機執行且也受 Azure AD B2C 保護的 Web API。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 新增 Web API 應用程式
> * 設定 Web API 的範圍
> * 授與 Web API 的權限
> * 更新範例以使用應用程式

> [!div class="nextstepaction"]
> [教學課程：在 Azure Active Directory B2C 中將識別提供者新增至您的應用程式](tutorial-add-identity-providers.md)
