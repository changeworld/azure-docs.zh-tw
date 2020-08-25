---
title: 呼叫受 Azure 身分識別平台保護的 ASP.NET Web API
description: 在本快速入門中，了解如何從 Windows 桌面 (WPF) 應用程式，呼叫受 Azure 身分識別平台保護的 ASP.NET Web API。 WPF 用戶端會驗證使用者、要求存取權杖，以及呼叫 Web API。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 0fc31fd397f8206f7c6f0509dd03495631dde609
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88165629"
---
# <a name="quickstart-call-an-aspnet-web-api-protected-by-microsoft-identity-platform"></a>快速入門：呼叫受 Azure 身分識別平台保護的 ASP.NET Web API

在本快速入門中，您會公開並保護 Web API，讓只有經過驗證的使用者可以存取該 Web API。 這個範例會說明如何公開 ASP.NET Web API，使其可以接受個人帳戶 (包括 outlook.com、live.com 和其他帳戶) 所簽發的權杖，以及已與 Azure 身分識別平台整合的公司或組織中公司和學校帳戶所簽發的權杖。

此範例也包含 Windows 傳統型應用程式 (WPF) 用戶端，其會示範如何要求存取權杖以存取 Web API。

## <a name="prerequisites"></a>必要條件

若要執行這個範例，您將需要下列各項：

* Visual Studio 2017 或 2019。  下載 [Visual Studio (免費版)](https://www.visualstudio.com/downloads/)。

* [Microsoft 帳戶](https://www.outlook.com)或 [Microsoft 365 開發人員計劃](/office/developer-program/office-365-developer-program)

## <a name="download-or-clone-this-sample"></a>下載或複製此範例

您可以從殼層或命令列複製這個範例：

  ```console
  git clone https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git
  ```

或者，您也可以[下載 ZIP 檔案形式的範例](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip)。

## <a name="register-your-web-api-in-the-application-registration-portal"></a>在應用程式註冊入口網站中註冊 Web API

### <a name="choose-the-azure-ad-tenant-where-you-want-to-create-your-applications"></a>選擇要在其中建立應用程式的 Azure AD 租用戶

如果您想要手動註冊應用程式，則必須先執行下列步驟：

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 如果您的帳戶出現在多個 Azure AD 租用戶中，請在頁面頂端功能表的右上角選取您的設定檔，然後**切換目錄**。
   將入口網站工作階段變更為所需的 Azure AD 租用戶。

### <a name="register-the-service-app-todolistservice"></a>註冊服務應用程式 (TodoListService)

1. 瀏覽至 Microsoft 身分識別平台，以取得開發人員的[應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)頁面。
1. 選取 [新增註冊]。
1. 當 [註冊應用程式] 頁面出現時，輸入您應用程式的註冊資訊：
   - 在 [名稱] 區段中，輸入將對應用程式使用者顯示、且有意義的應用程式名稱，例如 `AppModelv2-NativeClient-DotNet-TodoListService`。
   - 將 [支援的帳戶類型] 變更為 [任何組織目錄中的帳戶]。
   - 選取 [註冊] 以建立應用程式。

1. 在應用程式 [概觀] 頁面上，尋找 [應用程式 (用戶端) 識別碼] 值並將它記下供稍後使用。 您必須用此識別碼來設定此專案的 Visual Studio 設定檔 (`TodoListService\Web.config` 中的 `ClientId`)。
1. 選取 [公開 API] 區段，然後：
   - 選取 [新增範圍]
   - 選取 [儲存並繼續] 以接受建議的應用程式識別碼 URI (api://{clientId})
   - 輸入下列參數：
     - 針對 [範圍名稱] 使用 `access_as_user`
     - 確定已針對 [誰可以同意] 選取 [系統管理員與使用者] 選項
     - 在 [管理員同意顯示名稱] 中輸入 `Access TodoListService as a user`
     - 在 [管理員同意描述] 中輸入 `Accesses the TodoListService web API as a user`
     - 在 [使用者同意顯示名稱] 中輸入 `Access TodoListService as a user`
     - 在 [使用者同意描述] 中輸入 `Accesses the TodoListService web API as a user`
     - 讓 [狀態] 保持為 [啟用]
     - 選取 [新增範圍]

### <a name="configure-the-service-project-to-match-the-registered-web-api"></a>設定服務專案來符合已註冊的 Web API

1. 在 Visual Studio 中開啟解決方案，然後開啟 **TodoListService** 專案根目錄下的 **Web.config** 檔案。
1. 以剛才在應用程式註冊入口網站中所註冊應用程式的**用戶端識別碼 (應用程式識別碼)** 取代 `ida:ClientId` 參數的值。

### <a name="add-the-new-scope-to-the-todolistclients-appconfig"></a>將新範圍新增至 TodoListClient 的 app.config

* 開啟位於 **TodoListClient** 專案根資料夾中的 **app.config** 檔案，然後貼上剛才在 `TodoListServiceScope` 參數下針對 *TodoListService* 所註冊應用程式的**應用程式識別碼**，並取代字串 `{Enter the Application ID of your TodoListService from the app registration portal}`。

  > [!NOTE]
  > 請確定其使用下列格式：
  >
  > `api://{TodoListService-Application-ID}/access_as_user`
  >
  >(其中 {TodoListService-Application-ID} 是代表 TodoListService 應用程式識別碼的 GUID)。

## <a name="register-the-client-app-todolistclient"></a>註冊用戶端應用程式 (TodoListClient)

在此步驟中，您會在應用程式註冊入口網站中註冊新的應用程式，以設定 *TodoListClient* 專案。 如果是用戶端和伺服器會視為*相同應用程式*的情況，您也可以直接重複使用在「步驟2」中註冊的同一個應用程式。 如果您想要讓使用者以 Microsoft 個人帳戶登入，就需要使用同一個應用程式

### <a name="register-the-todolistclient-application-in-the-application-registration-portal"></a>在*應用程式註冊入口網站*中註冊 *TodoListClient* 應用程式

1. 瀏覽至 Microsoft 身分識別平台，以取得開發人員的[應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)頁面。
1. 選取 [新增註冊]。
1. 當 [註冊應用程式] 頁面出現時，輸入您應用程式的註冊資訊：
   - 在 [名稱] 區段中，輸入將對應用程式使用者顯示、且有意義的應用程式名稱，例如 `NativeClient-DotNet-TodoListClient`。
   - 將 [支援的帳戶類型] 變更為 [任何組織目錄中的帳戶]。
   - 選取 [註冊] 以建立應用程式。
   
   > [!NOTE]
   > 在 TodoListClient 專案的 **app.config** 中，`ida:Tenant` 預設值設定為 `common`。
   >
   > `common` 表示您可以使用公司或學校帳戶或 Microsoft 個人帳戶登入 (因為您選取了**任何組織目錄中的帳戶**)。
   >
   > `organizations` 表示您可以使用公司或學校帳戶登入。
   >
   > `consumers` 表示您可以使用 Microsoft 個人帳戶登入。
   >
   
1. 在應用程式的概觀頁面，選取 [驗證] 區段。
   1. 在 [平台設定] 下，選取 [新增平台] 按鈕。
   1. 若是**行動和桌面應用程式**，請選取**行動和桌面應用程式**。
   1. 若是**重新導向 URI**，請選取 **https://login.microsoftonline.com/common/oauth2/nativeclient** 核取方塊。
   1. 選取 [設定] 。   
1. 選取 [API 權限] 區段
   1. 選取 [新增權限] 按鈕。
   1. 選取 [我的 API] 索引標籤。
   1. 在 API 清單中選取 `AppModelv2-NativeClient-DotNet-TodoListService API`，或選取您為 Web API 輸入的名稱。
   1. 核取 **access_as_user** 權限 (如果尚未核取)。 如有需要請使用搜尋方塊。
   1. 選取 [新增權限] 按鈕。

### <a name="configure-your-todolistclient-project"></a>設定 *TodoListClient* 專案

1. 在*應用程式註冊入口網站*的 [概觀] 頁面中，複製 [應用程式 (用戶端) 識別碼] 的值
1. 開啟位於 **TodoListClient** 專案根資料夾中的 **app.config** 檔案，然後將值貼到 `ida:ClientId` 參數值中

## <a name="run-your-project"></a>執行專案

1. 按 `<F5>` 來執行專案。 *TodoListClient* 應該會開啟。
1. 選取右上方的 [登入]，然後使用您用來註冊應用程式的相同使用者，或使用相同目錄中的使用者來登入。
1. 至此，如果您是第一次登入，系統可能會提示您同意 *TodoListService* Web API。
1. 登入也會要求 *access_as_user* 範圍的存取權杖，以便存取 *TodoListService* Web API，並操作 *To-Do* 清單。

## <a name="pre-authorize-your-client-application"></a>預先授權用戶端應用程式

若要讓其他目錄中的使用者存取您的 Web API，其中一種方式是「預先授權」用戶端應用程式來存取您的 Web API，做法是在 Web API 的「預先授權」應用程式清單中新增用戶端應用程式的應用程式識別碼。 藉由新增預先授權的用戶端，您就不需要要求使用者同意使用您的 Web API。 請遵循下列步驟來預先授權 Web 應用程式：

1. 回到*應用程式註冊入口網站*，並開啟 **TodoListService** 的屬性。
1. 在 [公開 API] 區段中，按一下 [授權的用戶端應用程式] 區段下的 [新增用戶端應用程式]。
1. 在 [用戶端識別碼] 欄位中，貼上 `TodoListClient` 應用程式的應用程式識別碼。
1. 在 [授權的範圍] 區段中，選取此 Web API `api://<Application ID>/access_as_user` 的範圍。
1. 按頁面底部的 [新增應用程式] 按鈕。

## <a name="run-your-project"></a>執行專案

1. 按 `<F5>` 來執行專案。 *TodoListClient* 應該會開啟。
1. 選取右上方的 [登入] (或 [清除快取/登入])，然後使用個人 Microsoft 帳戶 (live.com 或 hotmail.com) 來登入或使用公司或學校帳戶來登入。

## <a name="optional-restrict-sign-in-access-to-your-application"></a>選擇性：限制對您應用程式的登入存取

根據預設，當您下載此程式碼範例，並遵循上述步驟將應用程式設定為使用 Azure Active Directory v2 端點時，個人帳戶 (例如 outlook.com、live.com 等等) 以及來自任何組織 (已與 Azure AD 整合) 的公司或學校帳戶，都可以要求權杖並存取 Web API。

若要限制哪些使用者可以登入應用程式，請使用其中一個選項：

### <a name="option-1-restrict-access-to-a-single-organization-single-tenant"></a>選項 1：將存取權限制在單一組織 (單一租用戶)

您可以將應用程式的登入存取權限制在僅限單一 Azure AD 租用戶中的使用者帳戶，包括該租用戶的*來賓帳戶*。 此案例常見於*企業營運應用程式*：

1. 開啟 **App_Start\Startup.Auth** 檔案，然後將傳遞至 `OpenIdConnectSecurityTokenProvider` 的中繼資料端點值變更為 `"https://login.microsoftonline.com/{Tenant ID}/v2.0/.well-known/openid-configuration"` (您也可以使用租用戶名稱，例如 `contoso.onmicrosoft.com`)。
2. 在相同的檔案中，將 `TokenValidationParameters` 上的 `ValidIssuer` 屬性設定為 `"https://sts.windows.net/{Tenant ID}/"`，並將 `ValidateIssuer` 引數設定為 `true`。

### <a name="option-2-use-a-custom-method-to-validate-issuers"></a>選項 2：使用自訂方法來驗證簽發者

您可以實作自訂方法，使用 **IssuerValidator** 參數來驗證簽發者。 如需此參數使用方式的詳細資訊，請閱讀 [TokenValidationParameters 類別](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters?view=azure-dotnet)。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>後續步驟
深入了解 Microsoft 身分識別平台所支援的受保護 Web API 案例：
> [!div class="nextstepaction"]
> [受保護的 Web API 案例](scenario-protected-web-api-overview.md)
