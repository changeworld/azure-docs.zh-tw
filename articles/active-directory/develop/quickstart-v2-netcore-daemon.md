---
title: 快速入門：在主控台應用程式中取得權杖和呼叫 Microsoft Graph | Azure
titleSuffix: Microsoft identity platform
description: 在本快速入門中，您會了解 .NET Core 範例應用程式如何使用用戶端認證流程，來取得權杖並呼叫 Microsoft Graph。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/05/2020
ms.author: jmprieur
ms.reviewer: marsma
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: c5c89b285e8ef98f83d0bfa923aaca402491315d
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98754214"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-using-console-apps-identity"></a>快速入門：使用主控台應用程式的身分識別來取得權杖並呼叫 Microsoft Graph API

在本快速入門中，您會下載並執行程式碼範例，該範例會示範 .NET Core 主控台應用程式如何取得存取權杖來呼叫 Microsoft Graph API，以及顯示目錄中的[使用者清單](/graph/api/user-list)。 此程式碼範例也會示範作業或 Windows 服務如何使用應用程式識別來執行，而不是以使用者的身分識別執行。 

如需圖例，請參閱[此範例的運作方式](#how-the-sample-works)。

## <a name="prerequisites"></a>必要條件

本快速入門需要 [.NET Core 3.1](https://www.microsoft.com/net/download/dotnet-core)。

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>註冊並下載快速入門應用程式

> [!div renderon="docs" class="sxs-lookup"]
>
> 有兩個選項可用來啟動快速入門應用程式：快速 (下面的選項 1) 和手動 (選項 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>選項 1：註冊和自動設定您的應用程式，然後下載程式碼範例
>
> 1. 移至 <a href="https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/DotNetCoreDaemonQuickstartPage/sourceType/docs" target="_blank">Azure 入口網站 - 應用程式註冊<span class="docon docon-navigate-external x-hidden-focus"></span></a>快速入門體驗。
> 1. 輸入應用程式的名稱，並選取 [註冊]。
> 1. 依照指示按一下滑鼠，即可下載並自動設定新的應用程式。
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>選項 2：註冊並手動設定您的應用程式和程式碼範例

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>步驟 1:註冊您的應用程式
> 若要手動註冊您的應用程式，並將應用程式註冊資訊新增到您的解決方案，請執行下列步驟：
>
> 1. 登入 <a href="https://portal.azure.com/" target="_blank">Azure 入口網站<span class="docon docon-navigate-external x-hidden-focus"></span></a>。
> 1. 如果您有多個租用的存取權，請使用頂端功能表中的 **目錄 + 訂用帳戶** 篩選條件 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: 來選取要在其中註冊應用程式的租用戶。
> 1. 搜尋並選取 [Azure Active Directory]  。
> 1. 在 **管理** 下選取 [應用程式註冊] > [新增註冊]。
> 1. 輸入應用程式的 [名稱]，例如 `Daemon-console`。 您的應用程式使用者可能會看到此名稱，您可以稍後再變更。
> 1. 選取 [註冊] 以建立應用程式。
> 1. 在 [管理]  下，選取 [憑證和密碼]  。
> 1. 在 [用戶端密碼] 底下，選取 [新增用戶端密碼] 並輸入名稱，然後選取 [新增]。 將祕密值記錄在安全的位置，以便在稍後的步驟中使用。
> 1. 在 [管理] 底下，選取 [API 權限] > [新增權限]。 選取 [Microsoft Graph]。
> 1. 選取 [應用程式權限]。
> 1. 在 [使用者] 節點底下，選取 [User.Read.All]，然後選取 [新增權限]。

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>下載並設定您的快速入門應用程式
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>步驟 1:在 Azure 入口網站中設定您的應用程式
> 若要讓本快速入門中的程式碼範例能夠運作，您需要建立用戶端密碼，並新增 Graph API 的 **User.Read.All** 應用程式權限。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [為我進行這些變更]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![已設定](media/quickstart-v2-netcore-daemon/green-check.png) 您的應用程式已設定了這些屬性。

#### <a name="step-2-download-your-visual-studio-project"></a>步驟 2:下載您的 Visual Studio 專案

> [!div renderon="docs"]
> [下載 Visual Studio 專案](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)
>
> 您可以在 Visual Studio 或 Visual Studio for Mac 中執行所提供的專案。


> [!div class="sxs-lookup" renderon="portal"]
> 使用 Visual Studio 2019 執行專案。
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [下載程式碼範例](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-visual-studio-project"></a>步驟 3：設定您的 Visual Studio 專案
>
> 1. 將 Zip 檔案解壓縮至磁碟根目錄附近的本機資料夾，例如 **C:\Azure-Samples**。
> 1. 在 Visual Studio 中開啟解決方案 - **1-Call-MSGraph\daemon-console.sln** (選擇性)。
> 1. 編輯 **appsettings.json**，並將欄位 `ClientId`、`Tenant` 和 `ClientSecret` 的值取代為下列值：
>
>    ```json
>    "Tenant": "Enter_the_Tenant_Id_Here",
>    "ClientId": "Enter_the_Application_Id_Here",
>    "ClientSecret": "Enter_the_Client_Secret_Here"
>    ```
>   其中：
>   - `Enter_the_Application_Id_Here` - 是您註冊的應用程式所具備的 **應用程式 (用戶端) 識別碼**。
>   - `Enter_the_Tenant_Id_Here` - 請將此值取代為 [租用戶識別碼] 或 [租用戶名稱] (例如 contoso.microsoft.com)
>   - `Enter_the_Client_Secret_Here` - 請將此值取代為步驟 1 所建立的用戶端密碼。

> [!div renderon="docs"]
> > [!TIP]
> > 若要尋找 [應用程式 (用戶端) 識別碼]、[目錄 (租用戶) 識別碼] 的值，請在 Azure 入口網站中移至應用程式的 [概觀] 頁面。 若要產生新的金鑰，請移至 [憑證和祕密] 頁面。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>步驟 3：系統管理員同意

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>步驟 4：系統管理員同意

如果您嘗試在此時執行應用程式，您將會收到「HTTP 403 - 禁止」錯誤：`Insufficient privileges to complete the operation`。 這是因為任何「僅限應用程式權限」都需要管理員同意，也就是目錄的全域管理員必須對應用程式表示同意。 請根據您的角色選取下列其中一個選項：

##### <a name="global-tenant-administrator"></a>全域租用戶管理員

> [!div renderon="docs"]
> 如果您是全域租用戶系統管理員，請在 Azure 入口網站中瀏覽至 [企業應用程式] > 選取您的應用程式註冊 > 從左側瀏覽窗格的 [安全性] 區段中選擇 [權限]。 選取標示為 [代表 {Tenant Name} 授與管理員同意] (其中 {Tenant Name} 是您目錄的名稱) 的大按鈕。

> [!div renderon="portal" class="sxs-lookup"]
> 如果您是全域管理員，請移至 [API 權限] 頁面，選取 [代表 Enter_the_Tenant_Name_Here 授與管理員同意]
> > [!div id="apipermissionspage"]
> > [移至 [API 權限] 頁面]()

##### <a name="standard-user"></a>標準使用者

如果您是租用戶的標準使用者，則需要請全域管理員針對應用程式授與管理員同意。 若要這樣做，請提供下列 URL 給管理員：

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> 其中：
>> * `Enter_the_Tenant_Id_Here` - 請將此值取代為 [租用戶識別碼] 或 [租用戶名稱] (例如 contoso.microsoft.com)
>> * `Enter_the_Application_Id_Here` - 是您註冊的應用程式所具備的 **應用程式 (用戶端) 識別碼**。

> [!NOTE]
> 使用上述 URL 對應用程式授與同意之後，您可能會看到錯誤「AADSTS50011：未針對應用程式註冊任何回覆地址」。 之所以發生此錯誤，是因為此應用程式和 URL 沒有重新導向 URI - 請忽略此錯誤。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>步驟 4：執行應用程式

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>步驟 5：執行應用程式

如果您是使用 Visual Studio 或 Visual Studio for Mac，請按 **F5** 來執行應用程式，否則請透過命令提示字元、主控台或終端機執行應用程式：

```dotnetcli
cd {ProjectFolder}\1-Call-MSGraph\daemon-console
dotnet run
```

> 其中：
> * {ProjectFolder} 是 ZIP 檔案解壓縮所在的資料夾。 範例 **C:\Azure-Samples\active-directory-dotnetcore-daemon-v2**

因此，您應該會在 Azure AD 目錄中看到使用者的清單。

> [!IMPORTANT]
> 此快速入門應用程式會使用用戶端密碼，將自己識別為機密用戶端。 由於用戶端密碼會以純文字形式新增至您的專案檔，因此，基於安全考量，在考慮將應用程式當作生產應用程式之前，建議您使用憑證，而非用戶端密碼。 如需有關如何使用憑證的詳細資訊，請從 GitHub 存放庫中參閱此範例的[這些指示](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/#variation-daemon-application-using-client-credentials-with-certificates)。

## <a name="more-information"></a>詳細資訊

### <a name="how-the-sample-works"></a>此範例的運作方式
![示範本快速入門所產生之範例應用程式的運作方式](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) 這個程式庫是用來登入使用者並要求權杖，該權杖會用來存取受 Microsoft 身分識別平台保護的 API。 如前所述，本快速入門會使用應用程式本身的身分識別 (而非委派的權限) 來要求權杖。 此案例所使用的驗證流程稱為[用戶端認證 OAuth 流程](v2-oauth2-client-creds-grant-flow.md)。 如需如何搭配使用 MSAL.NET 與用戶端認證流程的詳細資訊，請參閱[這篇文章](https://aka.ms/msal-net-client-credentials)。

 您可以在 Visual Studio 的 [套件管理員主控台] 中，透過執行下列命令來安裝 MSAL.NET：

```dotnetcli
dotnet add package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>MSAL 初始化

您可以透過加入下列程式碼來新增 MSAL 的參考：

```csharp
using Microsoft.Identity.Client;
```

接著，使用下列程式碼將 MSAL 初始化：

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientSecret(config.ClientSecret)
                                          .WithAuthority(new Uri(config.Authority))
                                          .Build();
```

> | 其中： | 描述 |
> |---------|---------|
> | `config.ClientSecret` | 在 Azure 入口網站中為應用程式建立的用戶端密碼。 |
> | `config.ClientId` | 是註冊於 Azure 入口網站中的應用程式所具備的 **應用程式 (用戶端) 識別碼**。 您可以在 Azure 入口網站的應用程式 [概觀] 頁面中找到此值。 |
> | `config.Authority`    | (選擇性) 供使用者用於驗證的 STS 端點。 若為公用雲端，通常是 `https://login.microsoftonline.com/{tenant}`，其中 {tenant} 是租用戶的名稱或租用戶識別碼。|

如需詳細資訊，請參閱[下列項目的參考文件：`ConfidentialClientApplication`](/dotnet/api/microsoft.identity.client.iconfidentialclientapplication)

### <a name="requesting-tokens"></a>要求權杖

若要使用應用程式的身分識別來要求權杖，請使用 `AcquireTokenForClient` 方法：

```csharp
result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
```

> |其中：| 描述 |
> |---------|---------|
> | `scopes` | 包含所要求的範圍。 針對機密用戶端，這應該使用類似 `{Application ID URI}/.default` 的格式，以指出所要求的範圍是 Azure 入口網站中所設定應用程式物件中以靜態方式定義的範圍 (若為 Microsoft Graph，`{Application ID URI}` 會指向 `https://graph.microsoft.com`)。 若為自訂 Web API，在 Azure 入口網站「應用程式註冊 (預覽)」中的 [公開 API] 區段底下會定義 `{Application ID URI}`。 |

如需詳細資訊，請參閱[下列項目的參考文件：`AcquireTokenForClient`](/dotnet/api/microsoft.identity.client.confidentialclientapplication.acquiretokenforclient)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>後續步驟

若要深入了解精靈應用程式，請參閱案例概觀：

> [!div class="nextstepaction"]
> [呼叫 Web API 的精靈應用程式](scenario-daemon-overview.md)
