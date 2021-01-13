---
title: 快速入門：在通用 Windows 平台傳統型應用程式中登入使用者並呼叫 Microsoft Graph | Azure
description: 在本快速入門中，了解 Windows 桌面 .NET (XAML) 應用程式如何取得存取權杖，以及呼叫受 Microsoft 身分識別平台端點保護的 API
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 0ffc3b40ecff1a7935eeef7fabfcb332fcd9dc71
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98017284"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-windows-desktop-app"></a>快速入門：取得權杖，並從 Windows 傳統型應用程式呼叫 Microsoft Graph API

在本快速入門中，您會下載並執行程式碼範例，該範例會示範 Windows 桌面 .NET (WPF) 應用程式如何登入使用者，並取得存取權杖來呼叫 Microsoft Graph API。 

如需圖例，請參閱[此範例的運作方式](#how-the-sample-works)。

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>必要條件
>
> * 已安裝包含[通用 Windows 平台開發](/windows/uwp/get-started/get-set-up)工作負載的 [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)
>
> ## <a name="register-and-download-your-quickstart-app"></a>註冊並下載快速入門應用程式
> 有兩個選項可用來啟動快速入門應用程式：
> * [快速] [選項 1：註冊和自動設定您的應用程式，然後下載程式碼範例](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [手動] [選項 2：註冊並手動設定您的應用程式和程式碼範例](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>選項 1：註冊和自動設定您的應用程式，然後下載程式碼範例
>
> 1. 移至新的 [Azure 入口網站 - 應用程式註冊](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs)。
> 1. 輸入應用程式的名稱，並選取 [註冊]  。
> 1. 依照指示按一下滑鼠，即可下載並自動設定新的應用程式。
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>選項 2：註冊並手動設定您的應用程式和程式碼範例
>
> #### <a name="step-1-register-your-application"></a>步驟 1:註冊您的應用程式
> 若要手動註冊您的應用程式，並將應用程式註冊資訊新增到您的解決方案，請執行下列步驟：
>
> 1. 登入 <a href="https://portal.azure.com/" target="_blank">Azure 入口網站<span class="docon docon-navigate-external x-hidden-focus"></span></a>。
> 1. 如果您有多個租用的存取權，請使用頂端功能表中的 **目錄 + 訂用帳戶** 篩選條件 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: 來選取要在其中註冊應用程式的租用戶。
> 1. 搜尋並選取 [Azure Active Directory]  。
> 1. 在 **管理** 下選取 [應用程式註冊] > [新增註冊]。
> 1. 輸入應用程式的 [名稱]，例如 `Win-App-calling-MsGraph`。 您的應用程式使用者可能會看到此名稱，您可以稍後再變更。
> 1. 在 [支援的帳戶類型] 區段中，選取 [任何組織目錄中的帳戶及個人的 Microsoft 帳戶]，例如 Skype、Xbox、Outlook.com。
> 1. 選取 [註冊] 以建立應用程式。
> 1. 在 [管理] 底下，選取 [驗證]。
> 1. 選取 [新增平台] > [行動和傳統應用程式]。
> 1. 在 [重新導向 URI] 區段中選取 `https://login.microsoftonline.com/common/oauth2/nativeclient`。
> 1. 選取 [設定] 。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>步驟 1:在 Azure 入口網站中設定您的應用程式
> 若要讓本快速入門中的程式碼範例能正常運作，您需要新增 **https://login.microsoftonline.com/common/oauth2/nativeclient** 作為回覆 URL。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [為我進行這項變更]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![已設定](media/quickstart-v2-windows-desktop/green-check.png) 您的應用程式已設定了這些屬性。

#### <a name="step-2-download-your-visual-studio-project"></a>步驟 2:下載您的 Visual Studio 專案

> [!div renderon="docs"]
> [下載 Visual Studio 專案](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip)

> [!div class="sxs-lookup" renderon="portal"]
> 使用 Visual Studio 2019 執行專案。
> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [下載程式碼範例](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>步驟 3：您的應用程式已設定並準備好執行
> 我們已使用您的應用程式屬性值來設定您的專案，且專案已可供執行。

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-visual-studio-project"></a>步驟 3：設定您的 Visual Studio 專案
> 1. 將 Zip 檔案解壓縮至磁碟根目錄附近的本機資料夾，例如 **C:\Azure-Samples**。
> 1. 在 Visual Studio 中開啟專案。
> 1. 編輯 **App.Xaml.cs**，並將欄位 `ClientId` 和 `Tenant` 的值取代為下列程式碼：
>
>    ```csharp
>    private static string ClientId = "Enter_the_Application_Id_here";
>    private static string Tenant = "Enter_the_Tenant_Info_Here";
>    ```
>
> 其中：
> - `Enter_the_Application_Id_here` - 是您註冊的應用程式所具備的 **應用程式 (用戶端) 識別碼**。
> - `Enter_the_Tenant_Info_Here` - 會設為下列其中一個選項：
>   - 如果您的應用程式支援 [此組織目錄中的帳戶]，請將此值取代為 [租用戶識別碼] 或 [租用戶名稱] (例如 contoso.microsoft.com)
>   - 如果您的應用程式支援 [任何組織目錄中的帳戶]，請將此值取代為 `organizations`
>   - 如果您的應用程式支援 [任何組織目錄中的帳戶及個人的 Microsoft 帳戶]，請將此值取代為 `common`
>
> > [!TIP]
> > 若要尋找 [應用程式 (用戶端) 識別碼]、[目錄 (租用戶) 識別碼] 和 [支援的帳戶類型] 的值，請在 Azure 入口網站中移至應用程式的 [概觀] 頁面。

## <a name="more-information"></a>詳細資訊

### <a name="how-the-sample-works"></a>此範例的運作方式
![示範本快速入門所產生之範例應用程式的運作方式](media/quickstart-v2-windows-desktop/windesktop-intro.svg)

### <a name="msalnet"></a>MSAL.NET
MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) 這個程式庫是用來登入使用者並要求權杖，該權杖會用來存取受 Microsoft 身分識別平台保護的 API。 您可以在 Visual Studio 的 [套件管理員主控台] 中，透過執行下列命令來安裝 MSAL：

```powershell
Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="msal-initialization"></a>MSAL 初始化

您可以透過加入下列程式碼來新增 MSAL 的參考：

```csharp
using Microsoft.Identity.Client;
```

接著，使用下列程式碼將 MSAL 初始化：

```csharp
public static IPublicClientApplication PublicClientApp;
PublicClientApplicationBuilder.Create(ClientId)
                .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
                .WithAuthority(AzureCloudInstance.AzurePublic, Tenant)
                .Build();
```

> |其中： | 描述 |
> |---------|---------|
> | `ClientId` | 是註冊於 Azure 入口網站中的應用程式所具備的 **應用程式 (用戶端) 識別碼**。 您可以在 Azure 入口網站的應用程式 [概觀] 頁面中找到此值。 |

### <a name="requesting-tokens"></a>要求權杖

MSAL 有兩種取得權杖的方法：`AcquireTokenInteractive` 與 `AcquireTokenSilent`。

#### <a name="get-a-user-token-interactively"></a>以互動方式取得使用者權杖

有些情況需要強制使用者透過快顯視窗與 Microsoft 身分識別平台端點進行互動，以驗證其認證或取得同意。 部分範例包括：

- 使用者首次登入應用程式
- 使用者因為密碼已過期而可能需要重新輸入其認證時
- 您的應用程式要求存取的資源需要使用者同意時
- 需要雙因素驗證時

```csharp
authResult = await App.PublicClientApp.AcquireTokenInteractive(_scopes)
                                      .ExecuteAsync();
```

> |其中：| 描述 |
> |---------|---------|
> | `_scopes` | 包含所要求的範圍，例如適用於 Microsoft Graph 的 `{ "user.read" }` 或適用於自訂 Web API 的 `{ "api://<Application ID>/access_as_user" }`。 |

#### <a name="get-a-user-token-silently"></a>以無訊息方式取得使用者權杖

您不應該在每次使用者需要存取資源時都要求使用者驗證其認證。 在大部分情況下，您應該以不需要與使用者進行任何互動的方式處理權杖取得和更新作業。 在初始 `AcquireTokenInteractive` 方法之後，您可以使用 `AcquireTokenSilent` 方法取得權杖以存取受保護的資源：

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
var firstAccount = accounts.FirstOrDefault();
authResult = await App.PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                      .ExecuteAsync();
```

> |其中： | 描述 |
> |---------|---------|
> | `scopes` | 包含所要求的範圍，例如適用於 Microsoft Graph 的 `{ "user.read" }` 或適用於自訂 Web API 的 `{ "api://<Application ID>/access_as_user" }`。 |
> | `firstAccount` | 指定快取中的第一個使用者 (MSAL 在單一應用程式中支援多個使用者)。 |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>後續步驟

嘗試 Windows Desktop 教學課程以取得建置應用程式與新功能的完整逐步指南，包括此快速入門的完整說明。

> [!div class="nextstepaction"]
> [呼叫 圖形 API 教學課程](./tutorial-v2-windows-desktop.md)
