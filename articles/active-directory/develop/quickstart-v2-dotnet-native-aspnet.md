---
title: 快速入門：呼叫受 Azure 身分識別平台保護的 ASP.NET Web API | Azure
titleSuffix: Microsoft identity platform
description: 在本快速入門中，了解如何從 Windows 桌面 (WPF) 應用程式，呼叫受 Azure 身分識別平台保護的 ASP.NET Web API。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/05/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 786f566b121d5f0d5d64e7b8b269c7cdfab9e4a6
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91825073"
---
# <a name="quickstart-call-an-aspnet-web-api-thats-protected-by-microsoft-identity-platform"></a>快速入門：呼叫受 Azure 身分識別平台保護的 ASP.NET Web API

在本快速入門中，您會公開並保護 Web API，讓只有經過驗證的使用者可以存取該 Web API。 本文說明如何公開 ASP.NET Web API，使其可以接受個人帳戶 (例如 outlook.com 或 live.com) 所簽發的權杖，以及已與 Microsoft 身分識別平台整合的任何公司或組織，其所提供的公司和學校帳戶。

本文也會使用 Windows Presentation Foundation (WPF) 應用程式來示範如何要求存取權杖來存取 Web API。

## <a name="prerequisites"></a>必要條件

* 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* Visual Studio 2017 或 2019。 下載 [Visual Studio (免費版)](https://www.visualstudio.com/downloads/)。

## <a name="clone-or-download-the-sample"></a>複製或下載範例

您可以透過下列兩種方式的其中一種來取得範例：

* 從殼層或命令列複製範例：
   ```console
   git clone https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git
   ```
* [將其下載為 ZIP 檔案](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip)。

## <a name="register-your-web-api"></a>註冊您的 Web API

在本節中，您會在 Azure 入口網站的 [應用程式註冊] 註冊您的 Web API。

### <a name="choose-your-azure-ad-tenant"></a>選擇 Azure AD 租用戶

若要手動註冊您的應用程式，請選擇您要在其中建立應用程式的 Azure Active Directory (Azure AD) 租用戶。

1. 使用公司、學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 如果您的帳戶存在於多個 Azure AD 租用戶中，請在右上方選取您的設定檔，然後選取 [切換目錄]。
1. 將您的入口網站工作階段變更為您想要的 Azure AD 租用戶。

### <a name="register-the-todolistservice-app"></a>註冊 TodoListService 應用程式

1. 移至 Microsoft 身分識別平台，以取得開發人員的[應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)入口網站。
1. 選取 [新增註冊]。
1. 當 [註冊應用程式] 頁面開啟時，輸入您應用程式的註冊資訊：

    1. 在 [名稱] 區段中，輸入將對應用程式使用者顯示、且有意義的應用程式名稱。 例如，輸入 **AppModelv2-NativeClient-DotNet-TodoListService**。
    1. 針對 [支援的帳戶類型]，選取 [任何組織目錄中的帳戶]。
    1. 選取 [註冊] 以建立應用程式。

1. 在應用程式 [概觀] 頁面上，尋找 [應用程式 (用戶端) 識別碼] 值，然後將其記下以供稍後使用。 您必須用此識別碼來設定此專案的 Visual Studio組態檔 (亦即，*TodoListService\Web.config* 檔案中的 `ClientId`)。

1. 在 [公開 API] 區段中，選取 [新增範圍]、選取 [儲存並繼續] 來接受建議的應用程式識別碼 URI (`api://{clientId}`)，然後輸入下列資訊：

    1. 針對 [範圍名稱]，請輸入 **access_as_user**。
    1. 針對 [誰可以同意]，確保已選取 [管理員與使用者] 選項。
    1. 在 [管理員同意顯示名稱] 方塊中，輸入 **Access TodoListService as a user**。
    1. 在 [管理員同意描述] 方塊中，輸入 **Accesses the TodoListService web API as a user**。
    1. 在 [使用者同意顯示名稱] 方塊中，輸入 **Access TodoListService as a user**。
    1. 在 [使用者同意描述] 方塊中，輸入 **Accesses the TodoListService Web API as a user**。
    1. 針對 [狀態]，保持 [已啟用]。
    1. 選取 [新增範圍]。

### <a name="configure-the-service-project"></a>設定服務專案

執行下列動作設定服務專案來比對已註冊的 Web API：

1. 在 Visual Studio 中開啟解決方案，然後開啟 TodoListService 專案根目錄下的 *Web.config* 檔案。

1. 以剛才在`ida:ClientId`應用程式註冊**入口網站中所註冊應用程式的用戶端識別碼 (應用程式識別碼) 值取代**  參數的值。

### <a name="add-the-new-scope-to-the-appconfig-file"></a>將新範圍新增至 app.config 檔案

若要將新範圍新增至 TodoListClient *app.config* 檔案，請執行下列動作：

1. 在 TodoListClient 專案根資料夾中，開啟 *app.config* 檔案。

1. 在 `TodoListServiceScope` 參數中，貼上您剛剛為 TodoListService 專案註冊的應用程式，其所提供的應用程式識別碼，取代 `{Enter the Application ID of your TodoListService from the app registration portal}` 字串。

  > [!NOTE]
  > 確定應用程式識別碼使用下列格式：`api://{TodoListService-Application-ID}/access_as_user` (其中 `{TodoListService-Application-ID}` 是代表 TodoListService 應用程式識別碼的 GUID)。

## <a name="register-the-todolistclient-client-app"></a>註冊 TodoListClient 用戶端應用程式

在本節中，您會在 Azure 入口網站中的**應用程式註冊**中註冊您的 TodoListClient 應用程式，然後設定 TodoListClient 專案中的程式碼。 如果用戶端和伺服器被視為「相同的應用程式」，則您可以重複使用在步驟 2 中註冊的應用程式。 如果您想要讓使用者以 Microsoft 個人帳戶登入，請使用同一個應用程式。

### <a name="register-the-app"></a>註冊應用程式

若要註冊 TodoListClient 應用程式，請執行下列動作：

1. 移至 Microsoft 身分識別平台，以取得開發人員的[應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)入口網站。
1. 選取 [新增註冊]。
1. 當 [註冊應用程式] 頁面開啟時，輸入您應用程式的註冊資訊：

    1. 在 [名稱] 區段中，輸入將對應用程式使用者顯示、且有意義的應用程式名稱 (例如，**NativeClient-DotNet-TodoListClient**)。
    1. 針對 [支援的帳戶類型]，選取 [任何組織目錄中的帳戶]。
    1. 選取 [註冊] 以建立應用程式。

   > [!NOTE]
   > 在 TodoListClient 專案 *app.config* 檔案中，`ida:Tenant` 的預設值會設定為 `common`。 可能的值包括：
   > - `common`：您可以使用公司或學校帳戶或 Microsoft 個人帳戶登入 (因為您在步驟 3b　選取了 [任何組織目錄中的帳戶])。
   > - `organizations`：您可以使用公司或學校帳戶登入。
   > - `consumers`：您可以使用 Microsoft 個人帳戶登入。

1. 在應用程式 [概觀] 頁面上選取 [驗證]，然後執行下列動作：

    1. 在 [平台設定] 下，選取 [新增平台] 按鈕。
    1. 若是**行動應用程式與傳統型應用程式**，請選取 [行動應用程式與傳統型應用程式]。
    1. 若是**重新導向 URI**，請選取 **https://login.microsoftonline.com/common/oauth2/nativeclient** 核取方塊。
    1. 選取 [設定] 。

1. 選取 [API 權限]，然後執行下列動作：

    1. 選取 [新增權限] 按鈕。
    1. 選取 [我的 API] 索引標籤。
    1. 在 API 清單中，選取 **AppModelv2-NativeClient-DotNet-TodoListService API** 或您已針對 Web API 輸入的名稱。
    1. 選取 **access_as_user** 權限核取方塊 (如果尚未選取)。 如有需要請使用搜尋方塊。
    1. 選取 [新增權限] 按鈕。

### <a name="configure-your-project"></a>設定您的專案

若要設定您的 TodoListClient 專案，請執行下列動作：

1. 在**應用程式註冊**入口網站的 [概觀] 頁面上，複製 [應用程式 (用戶端) 識別碼] 的值。

1. 從 TodoListClient 專案根資料夾中，開啟 *app.config* 檔案，然將應用程式識別碼值貼到 `ida:ClientId` 參數中。

## <a name="run-your-todolistclient-project"></a>執行您的 TodoListClient 專案

若要執行您的 TodoListClient 專案，請執行下列動作：

1. 按 F5 以開啟您的 TodoListClient 專案。 專案頁面應該會開啟。

1. 選取右上方的 [登入]，然後使用您用來註冊應用程式的同一認證，或以同一目錄中的使用者來登入。

   如果您是第一次登入，系統可能會提示您同意 TodoListService Web API。

   為了協助您存取 TodoListService Web API，並操作 *To-Do* 清單，登入也會要求 *access_as_user* 範圍的存取權杖。

## <a name="pre-authorize-your-client-application"></a>預先授權用戶端應用程式

您可以允許其他目錄中的使用者存取 Web API 的一種方式，就是預先授權用戶端應用程式存取您的 Web API。 若要這麼做，您可以將用戶端應用程式中的應用程式識別碼新增至 Web API 的預先授權應用程式清單。 藉由新增預先授權的用戶端，您可以讓使用者存取您的 Web API，而不需要提供同意。 若要預先授權您的用戶端應用程式，請執行下列動作：

1. 在**應用程式註冊**入口網站中，開啟 TodoListService 應用程式的屬性。
1. 在 [公開 API] 區段中，選取 [授權的用戶端應用程式] 下的 [新增用戶端應用程式]。
1. 在 [用戶端識別碼] 方塊中，貼上 TodoListClient 應用程式的應用程式識別碼。
1. 在 [授權的範圍] 區段中，選取 `api://<Application ID>/access_as_user` Web API 的範圍。
1. 選取 [新增應用程式] 。

### <a name="run-your-project"></a>執行專案

1. 請按 F5 執行您的專案。 您的 TodoListClient 應用程式應該會開啟。
1. 選取右上方的 [登入] (或 [清除快取/登入])，然後使用個人 Microsoft 帳戶 (例如 live.com 或 hotmail.com) 來登入或使用公司或學校帳戶來登入。

## <a name="optional-limit-sign-in-access-to-certain-users"></a>選用：限制特定使用者的登入存取權

根據預設，當您遵循了上述步驟時，任何個人帳戶 (例如 outlook.com 或 live.com) 或來自組織 (與 Azure AD 整合) 的公司或學校帳戶，都可以要求權杖並存取您的 Web API。

若要指定誰可以登入您的應用程式，請使用其中一個選項：

### <a name="option-1-limit-access-to-a-single-organization-single-tenant"></a>選項 1：將存取權限制在單一組織 (單一租用戶)

您可以將應用程式的登入存取權限制在單一 Azure AD 租用戶中的使用者帳戶，包括該租用戶的「來賓帳戶」。 此案例常見於「企業營運應用程式」。

1. 開啟 *App_Start\Startup.Auth* 檔案，然後將傳遞至 `OpenIdConnectSecurityTokenProvider` 的中繼資料端點值變更為 `"https://login.microsoftonline.com/{Tenant ID}/v2.0/.well-known/openid-configuration"`。 您也可以使用租用戶名稱，例如 `contoso.onmicrosoft.com`。
2. 在同一檔案中，將 `TokenValidationParameters` 上的 `ValidIssuer` 屬性設定為 `"https://sts.windows.net/{Tenant ID}/"`，並將 `ValidateIssuer` 引數設定為 `true`。

### <a name="option-2-use-a-custom-method-to-validate-issuers"></a>選項 2：使用自訂方法來驗證簽發者

您可以實作自訂方法，使用 `IssuerValidator` 參數來驗證簽發者。 如需此參數的詳細資訊，請參閱 [TokenValidationParameters 類別](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters?view=azure-dotnet&preserve-view=true)。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>後續步驟
深入了解 Microsoft 身分識別平台所支援的受保護 Web API 案例：
> [!div class="nextstepaction"]
> [受保護的 Web API 案例](scenario-protected-web-api-overview.md)
