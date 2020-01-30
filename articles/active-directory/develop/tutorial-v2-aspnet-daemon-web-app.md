---
title: 建置會使用 Microsoft 身分識別平台端點的多租用戶精靈
description: 在本教學課程中，了解如何從 Windows 桌面 (WPF) 應用程式，呼叫受 Azure Active Directory 保護的 ASP.NET Web API。 WPF 用戶端會驗證使用者、要求存取權杖，以及呼叫 Web API。
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: ec6664e7c55057c29c5b741203b326ce460c6e91
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701224"
---
# <a name="tutorial-build-a-multitenant-daemon-that-uses-the-microsoft-identity-platform-endpoint"></a>教學課程：建置會使用 Microsoft 身分識別平台端點的多租用戶精靈

在本教學課程中，您將了解如何使用 Microsoft 身分識別平台，在長時間執行的非互動式程序中存取 Microsoft 企業客戶的資料。 精靈範例會使用 [OAuth2 用戶端認證授與](v2-oauth2-client-creds-grant-flow.md)來取得存取權杖。 然後，此精靈會使用權杖來呼叫 [Microsoft Graph](https://graph.microsoft.io) 並存取組織資料。

> [!div class="checklist"]
> * 整合精靈應用程式與 Microsoft 身分識別平台
> * 由系統管理員將應用程式權限直接授予應用程式
> * 取得存取權杖以呼叫 Microsoft Graph API
> * 呼叫 Microsoft Graph API。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

此應用程式會建置為 ASP.NET MVC 應用程式。 其使用 OWIN OpenID Connect 中介軟體來讓使用者登入。  

此範例中的「精靈」元件是一個 API 控制器 (`SyncController.cs`)。 控制器收到呼叫時會從 Microsoft Graph 提取客戶的 Azure Active Directory (Azure AD) 租用戶使用者清單。 `SyncController.cs` 會由 Web 應用程式中的 AJAX 呼叫加以觸發。 其會使用[適用於 .NET 的 Microsoft 驗證程式庫 (MSAL)](msal-overview.md) 來取得 Microsoft Graph 的存取權杖。

對於較簡單的主控台精靈應用程式，請參閱 [.NET Core 精靈快速入門](quickstart-v2-netcore-daemon.md)。

## <a name="scenario"></a>狀況

由於此應用程式是供 Microsoft 企業客戶使用的多租用戶應用程式，因此必須提供方法讓客戶可以「註冊」應用程式或將應用程式「連線」至其公司資料。 在連線過程中，公司管理員會先將「應用程式權限」  直接授與應用程式，讓它能夠在沒有登入使用者的情況下，以非互動方式存取公司資料。 此範例中的邏輯主要將說明如何使用身分識別平台的[管理員同意](v2-permissions-and-consent.md#using-the-admin-consent-endpoint)端點來完成此連線流程。

![拓撲](./media/tutorial-v2-aspnet-daemon-webapp/topology.png)

若要進一步了解此範例中使用的概念，請閱讀[適用於身分識別平台端點的用戶端認證通訊協定文件](v2-oauth2-client-creds-grant-flow.md)。

## <a name="prerequisites"></a>Prerequisites

若要執行本快速入門中的範例，您將需要：

- [Visual Studio 2017 或 2019](https://visualstudio.microsoft.com/downloads/)。
- Azure AD 租用戶。 如需詳細資訊，請參閱[如何取得 Azure AD 租用戶](quickstart-create-new-tenant.md)。
- Azure AD 租用戶中的一或多個使用者帳戶。 此範例不適用於 Microsoft 帳戶 (前身為 Windows Live 帳戶)。 如果您使用 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)，且您從未在目錄中建立使用者帳戶，您就必須於此時建立。

## <a name="clone-or-download-this-repository"></a>複製或下載此存放庫

從殼層或命令列輸入下列命令：

```Shell
git clone https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2.git
```

或者，[下載 ZIP 檔案中的範例](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/archive/master.zip)。

## <a name="register-your-application"></a>註冊您的應用程式

此範例有一個專案。 若要向您的 Azure AD 租用戶註冊應用程式，您可以：

- 遵循[向 Azure Active Directory 租用戶註冊範例](#register-your-application)和[設定範例以使用 Azure AD 租用戶](#choose-the-azure-ad-tenant)中的步驟。
- 使用 PowerShell 指令碼來：
  - 「自動」  為您建立 Azure AD 應用程式和相關物件 (密碼、權限、相依性)。
  - 修改 Visual Studio 專案的組態檔。

如果您想要使用自動化功能：

1. 在 Windows 上，執行 PowerShell 並移至所複製目錄的根目錄。
1. 請執行這個命令：

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
   ```

1. 執行建立 Azure AD 應用程式的指令碼，並據以設定範例應用程式的程式碼：

   ```PowerShell
   .\AppCreationScripts\Configure.ps1
   ```

   [應用程式建立指令碼](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/blob/master/AppCreationScripts/AppCreationScripts.md)中會有其他指令碼執行方式的說明。

1. 開啟 Visual Studio 解決方案，然後選取 [啟動]  以執行程式碼。

如果您不想要使用自動化功能，則請使用下列各節中的步驟。

### <a name="choose-the-azure-ad-tenant"></a>選擇 Azure AD 租用戶

1. 使用公司或學校帳戶或個人 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 如果您的帳戶位於多個 Azure AD 租用戶，請選取頁面頂端功能表上的設定檔，然後選取 [切換目錄]  。
1. 將入口網站工作階段變更為所需的 Azure AD 租用戶。

### <a name="register-the-client-app-dotnet-web-daemon-v2"></a>註冊用戶端應用程式 (dotnet-web-daemon-v2)

1. 移至開發人員所適用 Microsoft 身分識別平台中的[應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)頁面。
1. 選取 [新增註冊]  。
1. 當 [註冊應用程式]  頁面出現時，輸入您應用程式的註冊資訊：
   - 在 [名稱]  區段中，輸入將對應用程式使用者顯示、且有意義的應用程式名稱。 例如，輸入 **dotnet-web-daemon-v2**。
   - 在 [支援的帳戶類型]  區段中，選取 [任何組織目錄中的帳戶]  。
   - 在 [重新導向 URI (選用)]  區段中，選取下拉式方塊中的 [Web]  ，然後輸入下列重新導向 URI：
       - **https://localhost:44316/**
       - **https://localhost:44316/Account/GrantPermissions**
          
     如果有超過兩個的重新導向 URI，您稍後必須在應用程式建立成功之後，從 [驗證]  索引標籤新增這些 URI。
1. 選取 [註冊]  以建立應用程式。
1. 在應用程式的 [概觀]  頁面上，尋找 [應用程式 (用戶端) 識別碼]  值並將它記下供稍後使用。 您必須用此識別碼來設定此專案的 Visual Studio 組態檔。
1. 在應用程式頁面清單中，選取 [驗證]  。 然後：
   - 在 [進階設定]  區段中，將 [登出 URL]  設定為 **https://localhost:44316/Account/EndSession** 。
   - 在 [進階設定]   > [隱含授與]  區段中，選取 [存取權杖]  和 [識別碼權杖]  。 此範例需要啟用[隱含授與流程](v2-oauth2-implicit-grant-flow.md)才能讓使用者登入並呼叫 API。
1. 選取 [儲存]  。
1. 從 [憑證和祕密]  頁面的 [用戶端密碼]  區段中，選取 [新增用戶端密碼]  。 然後：

   1. 輸入金鑰描述 (例如，**應用程式祕密**)。
   1. 選取金鑰持續時間，此值可為 [1 年]  、[2 年]  或 [永不過期]  。
   1. 選取 [新增]  按鈕。 
   1. 金鑰值出現時，將其複製並儲存到安全的位置。 稍後您將需要用此金鑰在 Visual Studio 中設定專案。 金鑰值不會再次顯示，也無法透過任何其他方式來取得。
1. 在應用程式頁面清單中，選取 [API 權限]  。 然後：
   1. 選取 [新增權限]  按鈕。
   1. 確定已選取 [Microsoft API]  索引標籤。
   1. 在 [常用的 Microsoft API]  區段中，選取 [Microsoft Graph]  。
   1. 在 [應用程式權限]  區段中，確定已選取正確的權限：**User.Read.All**。
   1. 選取 [新增權限]  按鈕。

## <a name="configure-the-sample-to-use-your-azure-ad-tenant"></a>設定範例以使用您的 Azure AD 租用戶

在下列步驟中，**ClientID** 與「應用程式識別碼」或 **AppId** 相同。

在 Visual Studio 中開啟解決方案以設定專案。

### <a name="configure-the-client-project"></a>設定用戶端專案

如果您使用安裝指令碼，系統會為您套用下列變更。

1. 開啟 **UserSync\Web.Config** 檔案。
1. 尋找應用程式金鑰 **ida:ClientId**。 將現有值取代為從 Azure 入口網站複製得來的 **dotnet-web-daemon-v2** 應用程式識別碼。
1. 尋找應用程式金鑰 **ida:ClientSecret**。 將現有值取代為您在 Azure 入口網站中建立 **dotnet-web-daemon-v2** 應用程式時所儲存的金鑰。

## <a name="run-the-sample"></a>執行範例

清除解決方案、重建解決方案、執行 UserSync 應用程式，然後在 Azure AD 租用戶中以管理員身分登入。 如果您沒有 Azure AD 租用戶可用於測試，您可以[依照這些指示](quickstart-create-new-tenant.md)取得一個。

當您登入時，應用程式會先要求您提供權限，以便讓您登入並讀取您的使用者設定檔。 此同意可讓應用程式確保您是企業使用者。

![使用者同意](./media/tutorial-v2-aspnet-daemon-webapp/firstconsent.png)

應用程式接著會嘗試透過 Microsoft Graph，從您的 Azure AD 租用戶同步處理使用者清單。 如果無法這麼做，便會要求您 (租用戶管理員) 將租用戶連線至應用程式。

然後，應用程式會要求您提供權限，以便讀取租用戶中的使用者清單。

![系統管理員同意](./media/tutorial-v2-aspnet-daemon-webapp/adminconsent.PNG)

授與權限之後，您就會從應用程式登出。 讓您登出可確保 Microsoft Graph 的任何現有存取權杖都會從權杖快取中移除。 當您再次登入時，所取得的全新權杖就會有可對 MS Graph 發出呼叫的必要權限。


當您授與權限時，應用程式便可在任何時間點查詢使用者。 您可以選取 [同步使用者]  按鈕，然後重新整理使用者清單來確認這一點。 嘗試新增或移除使用者，並重新同步處理此清單。 (但請注意，應用程式只會同步處理第一頁的使用者)。

## <a name="about-the-code"></a>關於程式碼

此範例的相關程式碼位於下列檔案中：

- **App_Start\Startup.Auth.cs**、**Controllers\AccountController.cs**：初始登入。 特別是，控制器上的動作具有**授權**屬性，會強制使用者登入。 應用程式會使用[授權碼流程](v2-oauth2-auth-code-flow.md)來登入使用者。
- **Controllers\SyncController.cs**：將使用者清單同步至本機記憶體內部存放區。
- **Controllers\UserController.cs**：顯示本機記憶體內部存放區中的使用者清單。
- **Controllers\AccountController.cs**：使用管理員同意端點，取得租用戶管理員提供的權限。

## <a name="re-create-the-sample-app"></a>重新建立應用程式範例

1. 在 Visual Studio 中，建立新的 **Visual C#** **ASP.NET Web 應用程式 (.NET Framework)** 專案。 
1. 在下一個畫面中，選擇 **MVC** 專案範本。 此外，也請為 **Web API** 新增資料夾和核心參考，因為稍後會新增 Web API 控制器。 將專案選擇的驗證模式保留為預設值：**沒有驗證**。
1. 在 [方案總管]  視窗中選取專案，然後選取 **F4** 鍵。 
1. 在 [專案屬性] 中，將 [已啟用 SSL]  設定為 **True**。 請記下 **SSL URL** 中的資訊。 在 Azure 入口網站中設定此應用程式的註冊時，將會用到這項資料。
1. 新增下列 ASP.NET OWIN 中介軟體 NuGet 套件： 
   - Microsoft.Owin.Security.ActiveDirectory
   - Microsoft.Owin.Security.Cookies
   - Microsoft.Owin.Host.SystemWeb
   - Microsoft.IdentityModel.Protocol.Extensions
   - Microsoft.Owin.Security.OpenIdConnect
   - Microsoft.Identity.Client 
1. 在 **App_Start** 資料夾中：
   1. 建立名為 **Startup.Auth.cs** 的類別。 
   1. 從命名空間名稱中移除 **.App_Start**。 
   1. 將**啟動**類別的程式碼取代為應用程式範例相同檔案內的程式碼。       
   請務必採用整個類別定義。 定義會從**公用類別啟動**變更為**公用部分類別啟動**。
1. 在 **Startup.Auth.cs** 中，藉由新增 Visual Studio IntelliSense 所建議的 **using** 陳述式來解析遺漏的參考。
1. 以滑鼠右鍵按一下專案，選取 [新增]  ，然後選取 [類別]  。
1. 在搜尋方塊中，輸入 **OWIN**。 **OWIN 啟動類別**便會顯示為選取項目。 加以選取，並將類別命名為 **Startup.cs**。
1. 在 **Startup.cs** 中，將**啟動**類別的程式碼取代為應用程式範例相同檔案內的程式碼。 同樣地，請注意定義會從**公用類別啟動**變更為**公用部分類別啟動**。
1. 在 **Models** 資料夾中，新增名為 **MsGraphUser.cs** 的新類別。 請將實作取代為範例中同名檔案的內容。
1. 新增名為 **AccountController** 的 **MVC 5 控制器 - 空白**執行個體。 請將實作取代為範例中同名檔案的內容。
1. 新增名為 **UserController** 的 **MVC 5 控制器 - 空白**執行個體。 請將實作取代為範例中同名檔案的內容。
1. 新增名為 **SyncController** 的 **Web API 2 5 控制器 - 空白**執行個體。 請將實作取代為範例中同名檔案的內容。
1. 針對使用者介面，在 **Views\Account** 資料夾中，新增三個**空白 (不含模型) 檢視**執行個體，分別命名為 **GrantPermissions**、**Index** 和 **UserMismatch**。 在 **Views\User** 資料夾中，新增一個名為 **Index** 的執行個體。 請將實作取代為範例中同名檔案的內容。
1. 更新 **Shared\_Layout.cshtml** 和 **Home\Index.cshtml**，以將不同的檢視正確地連結在一起。

## <a name="deploy-the-sample-to-azure"></a>將範例部署至 Azure

此專案具有 Web 應用程式和 Web API 專案。 若要將這些專案部署至 Azure 網站，請對每個專案執行下列步驟：

1. 建立 Azure 網站。
1. 將 Web 應用程式和 Web API 發佈至網站。
1. 更新用戶端以呼叫網站，而非呼叫 IIS Express。

### <a name="create-and-publish-dotnet-web-daemon-v2-to-an-azure-website"></a>建立 dotnet-web-daemon-v2 並將其發佈至 Azure 網站

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左上角，選取 [建立資源]  。
1. 選取 [Web]   > [Web 應用程式]  ，然後為網站提供名稱。 例如，將網站命名為 **dotnet-web-daemon-v2-contoso.azurewebsites.net**。
1. 選取 [訂用帳戶]  、[資源群組]  和 [App Service 方案和位置]  的資訊。 [OS]  是 [Windows]  ，[發佈]  則是 [程式碼]  。
1. 選取 [建立]  ，並等待 App Service 建立。
1. 當您收到**部署成功**通知時，請選取 [移至資源]  以移至新建立的 App Service。
1. 網站建立好之後，請在 [儀表板]  中找到網站，然後加以選取以開啟 App Service 的 [概觀]  畫面。
1. 從 App Service 的 [概觀]  索引標籤中，選取 [取得發行設定檔]  連結以下載發行設定檔，並加以儲存。 您可以使用其他部署機制，例如從原始檔控制進行部署。
1. 切換至 Visual Studio，然後：
   1. 移至 **dotnet-web-daemon-v2** 專案。 
   1. 在 [方案總管] 中以滑鼠右鍵按一下專案，然後選取 [發佈]  。
   1. 選取底部列上的 [匯入設定檔]  ，然後匯入您先前下載的發行設定檔。
1. 選取 [設定]  。
1. 在 [連線]  索引標籤上，更新目的地 URL 使其使用 "https"。 例如，使用 [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net)。 選取 [下一步]  。
1. 在 [設定]  索引標籤上，確定已清除 [啟用組織驗證]  。  
1. 選取 [儲存]  。 選取主畫面上的 [發佈]  。

Visual Studio 會發佈專案，並自動開啟瀏覽器並導向至專案的 URL。 如果您看到專案的預設網頁，表示發佈成功。

### <a name="update-the-azure-ad-tenant-application-registration-for-dotnet-web-daemon-v2"></a>更新 dotnet-web-daemon-v2 的 Azure AD 租用戶應用程式註冊

1. 回到 [Azure 入口網站](https://portal.azure.com)。
1. 在左窗格中，選取 [Azure Active Directory]  服務，然後選取 [應用程式註冊]  。
1. 選取 **dotnet-web-daemon-v2** 應用程式。
1. 在您應用程式的 [驗證]  頁面上，以您的服務位址更新 [登出 URL]  欄位。 例如，使用 [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net)。
1. 從 [商標]  功能表，將 [首頁 URL]  更新為您的服務位址。 例如，使用 [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net)。
1. 儲存組態。
1. 在 [驗證]   > [重新導向 URI]  功能表的值清單中，新增相同的 URL。 如果您有多個重新導向 URL，請確定在使用應用程式服務的 URI 作為每個重新導向 URL 時，都有新的輸入項目。

## <a name="clean-up-resources"></a>清除資源
如果不再需要，請刪除您在[註冊應用程式](#register-your-application)步驟中所建立的應用程式物件。  若要移除應用程式，請遵循[移除您或貴組織所編寫的應用程式](quickstart-remove-app.md#remove-an-application-authored-by-you-or-your-organization)中的指示。

## <a name="get-help"></a>取得說明

使用 [Stack Overflow](http://stackoverflow.com/questions/tagged/msal) 取得來自社群的支援。
請先在 Stack Overflow 上詢問您的問題，並瀏覽現有的問題，以查看先前是否有人已提出您的問題。
請確定您的問題或意見已標記 "adal"、"msal" 和 "dotnet"。

如果您在範例中發現錯誤 (bug)，請在 [GitHub 問題](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/issues)上提出問題。

如果您在 MSAL.NET 中發現錯誤 (bug)，請在 [MSAL.NET GitHub 問題](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues)上提出問題。

若要提供建議，請移至 [User Voice 頁面](https://feedback.azure.com/forums/169401-azure-active-directory)。

## <a name="next-steps"></a>後續步驟
深入了解 Microsoft 身分識別平台所支援的不同[驗證流程和應用程式案例](authentication-flows-app-scenarios.md)。

如需詳細資訊，請參閱下列概念文件：

- [Azure Active Directory 中的租用](single-and-multi-tenant-apps.md)
- [了解 Azure AD 應用程式同意體驗](application-consent-experience.md)
- [讓任何 Azure Active Directory (AD) 使用者以多租用戶應用程式的模式登入](howto-convert-app-to-be-multi-tenant.md)
- [了解使用者和管理員同意](howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)
- [Azure Active Directory 中的應用程式和服務主體物件](app-objects-and-service-principals.md)
- [快速入門：使用 Microsoft 身分識別平台來註冊應用程式](quickstart-register-app.md)
- [快速入門：設定用戶端應用程式以存取 Web API](quickstart-configure-app-access-web-apis.md)
- [取得具有用戶端認證流程的應用程式適用的權杖](msal-client-applications.md)

對於較簡單的多租用戶主控台精靈應用程式，請參閱 [.NET Core 精靈快速入門](quickstart-v2-netcore-daemon.md)。
