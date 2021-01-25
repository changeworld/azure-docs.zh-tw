---
title: 快速入門：在通用 Windows 平台應用程式中登入使用者並呼叫 Microsoft Graph | Azure
titleSuffix: Microsoft identity platform
description: 在本快速入門中，了解通用 Windows 平台 (UWP) 應用程式如何取得存取權杖，以及呼叫受 Microsoft 身分識別平台保護的 API。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/07/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:UWP
ms.openlocfilehash: 074b64debc0a5aa08967efce69b7e5d14b4d90e4
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98754053"
---
# <a name="quickstart-call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>快速入門：自通用 Windows 平台 (UWP) 應用程式呼叫 Microsoft Graph API

在本快速入門中，您會下載並執行程式碼範例，該範例會示範通用 Windows 平台 (UWP) 應用程式如何登入使用者，並取得存取權杖來呼叫 Microsoft Graph API。 

如需圖例，請參閱[此範例的運作方式](#how-the-sample-works)。

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>必要條件
>
> * 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)
>
> ## <a name="register-and-download-your-quickstart-app"></a>註冊並下載快速入門應用程式
> [!div renderon="docs" class="sxs-lookup"]
> 有兩個選項可用來啟動快速入門應用程式：
> * [快速] [選項 1：註冊和自動設定您的應用程式，然後下載程式碼範例](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [手動] [選項 2：註冊並手動設定您的應用程式和程式碼範例](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>選項 1：註冊和自動設定您的應用程式，然後下載程式碼範例
>
> 1. 移至 <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/UwpQuickstartPage/sourceType/docs" target="_blank">Azure 入口網站 - 應用程式註冊<span class="docon docon-navigate-external x-hidden-focus"></span></a>快速入門體驗。
> 1. 輸入應用程式的名稱，並選取 [註冊]。
> 1. 依照指示按一下滑鼠，即可下載並自動設定新的應用程式。
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>選項 2：註冊並手動設定您的應用程式和程式碼範例
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>步驟 1:註冊您的應用程式
> 若要註冊您的應用程式，並將應用程式註冊資訊新增到您的解決方案，請執行下列步驟：
> 1. 登入 <a href="https://portal.azure.com/" target="_blank">Azure 入口網站<span class="docon docon-navigate-external x-hidden-focus"></span></a>。
> 1. 如果您有多個租用的存取權，請使用頂端功能表中的 **目錄 + 訂用帳戶** 篩選條件 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: 來選取要在其中註冊應用程式的租用戶。
> 1. 搜尋並選取 [Azure Active Directory]  。
> 1. 在 **管理** 下選取 [應用程式註冊] > [新增註冊]。
> 1. 輸入應用程式的 [名稱]，例如 `UWP-App-calling-MsGraph`。 您的應用程式使用者可能會看到此名稱，您可以稍後再變更。
> 1. 在 [支援的帳戶類型] 區段中，選取 [任何組織目錄中的帳戶及個人的 Microsoft 帳戶]，例如 Skype、Xbox、Outlook.com。
> 1. 選取 [註冊] 以建立應用程式，然後記錄 [應用程式 (用戶端) 識別碼] 以供在稍後的步驟中使用。
> 1. 在 [管理] 底下，選取 [驗證]。
> 1. 選取 [新增平台] > [行動和傳統應用程式]。
> 1. 在 [重新導向 URI] 之下，選取 `https://login.microsoftonline.com/common/oauth2/nativeclient`。
> 1. 選取 [設定] 。

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-the-application"></a>步驟 1：設定應用程式
> 若要讓本快速入門中的程式碼範例能正常運作，您需要新增 **https://login.microsoftonline.com/common/oauth2/nativeclient** 作為重新導向 URI。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [為我進行這項變更]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![已設定](media/quickstart-v2-uwp/green-check.png) 您的應用程式已設定了這些屬性。

#### <a name="step-2-download-the-visual-studio-project"></a>步驟 2:下載 Visual Studio 專案

> [!div renderon="docs"]
> [下載 Visual Studio 專案](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

> [!div class="sxs-lookup" renderon="portal"]
> 使用 Visual Studio 2019 執行專案。
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [下載程式碼範例](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>步驟 3：您的應用程式已設定並準備好執行
> 我們已使用您的應用程式屬性值來設定您的專案，且專案已可供執行。

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-visual-studio-project"></a>步驟 3：設定 Visual Studio 專案
>
> 1. 將 .zip 封存檔解壓縮至靠近磁碟機根目錄的本機資料夾。 例如，解壓縮至 **C:\Azure-Samples**。
> 1. 在 Visual Studio 中開啟專案。 安裝 [通用 Windows 平台開發] 工作負載和任何個別的 SDK 元件 (如果出現提示)。
> 1. 在 MainPage.Xaml.cs 中，將 `ClientId` 變數的值變更為您稍早註冊之應用程式的 [應用程式 (用戶端) 識別碼]。
>
>    ```csharp
>    private const string ClientId = "Enter_the_Application_Id_here";
>    ```
>
>    您可以在 Azure 入口網站中應用程式的 [概觀] 窗格上找到 [應用程式 (用戶端) 識別碼] ([Azure Active Directory] > [應用程式註冊] > {您的應用程式註冊})。
> 1. 建立而後選取封裝新的自我簽署測試憑證：
>     1. 在 [方案總管] 中，按兩下 Package.appxmanifest 檔案。
>     1. 選取 [封裝] > [選擇憑證...] > [建立...]。
>     1. 輸入密碼，然後選取 [確定]。
>     1. 選取 [從檔案選取...]，接著選取您剛才建立的 Native_UWP_V2_TemporaryKey.pfx 檔案，然後選取 [確定]。
>     1. 關閉 Package.appxmanifest 檔案 (如果系統提示您儲存檔案，請選取 [確定])。
>     1. 在 [方案總管] 中，於 **Native_UWP_V2** 專案上按一下滑鼠右鍵，然後選取 [屬性]。
>     1. 選取 [簽署]，然後選取您在 [選擇強式名稱金鑰檔案] 下拉式集中建立的 .pfx。

#### <a name="step-4-run-the-application"></a>步驟 4：執行應用程式

若要在您的本機電腦上執行範例應用程式：

1. 在 Visual Studio 工具列中，選擇正確的平台 (可能是 **x64** 或 **x86**，但不是 ARM)。 目標裝置應從「裝置」變成「本機電腦」。
1. 選取 [偵錯] > [啟動但不偵錯]。
    
    如果系統提示您這麼做，您可能必須先啟用 [開發人員模式]，然後再次 [啟動但不偵錯] 以啟動應用程式。

當應用程式的視窗出現時，您可以選取 [呼叫 Microsoft Graph API] 按鈕，輸入您的認證，然後同意應用程式所要求的權限。 如果成功，應用程式會顯示一些權杖資訊，以及從呼叫 Microsoft Graph API 取得的資料。

## <a name="how-the-sample-works"></a>此範例的運作方式

![示範本快速入門所產生之範例應用程式的運作方式](media/quickstart-v2-uwp/uwp-intro.svg)

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) 是用來登入使用者並要求安全性權杖的程式庫。 安全性權杖可用來存取受 Microsoft 身分識別平臺保護的 API。 您可以在 Visual Studio 的 [套件管理員主控台] 中，透過執行下列命令來安裝 MSAL：

```powershell
Install-Package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>MSAL 初始化

您可以透過加入下列程式碼來新增 MSAL 的參考：

```csharp
using Microsoft.Identity.Client;
```

接著，系統便會使用下列程式碼將 MSAL 初始化：

```csharp
public static IPublicClientApplication PublicClientApp;
PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                                                .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
                                                    .Build();
```

`ClientId` 的值為您在 Azure 入口網站中註冊的應用程式的 [應用程式 (用戶端) 識別碼]。 您可以在 Azure 入口網站的應用程式 [概觀] 頁面中找到此值。

### <a name="requesting-tokens"></a>要求權杖

MSAL 有兩種方法可取得 UWP 應用程式中的權杖：`AcquireTokenInteractive` 與 `AcquireTokenSilent`。

#### <a name="get-a-user-token-interactively"></a>以互動方式取得使用者權杖

有些情況需要強制使用者透過快顯視窗與 Microsoft 身分識別平臺互動，以驗證其認證或提供同意。 部分範例包括：

- 使用者首次登入應用程式
- 使用者因為密碼已過期而可能需要重新輸入其認證時
- 您的應用程式要求存取的資源需要使用者同意時
- 需要雙因素驗證時

```csharp
authResult = await App.PublicClientApp.AcquireTokenInteractive(scopes)
                      .ExecuteAsync();
```

`scopes` 參數包含所要求的範圍，例如適用於 Microsoft Graph 的 `{ "user.read" }` 或適用於自訂 Web API 的 `{ "api://<Application ID>/access_as_user" }`。

#### <a name="get-a-user-token-silently"></a>以無訊息方式取得使用者權杖

在初始的 `AcquireTokenInteractive` 方法之後，使用 `AcquireTokenSilent` 方法來取得權杖以存取受保護的資源。 您不應該在每次使用者需要存取資源時都要求使用者驗證其認證。 在大部分情況下，您應該以不需要與使用者進行任何互動的方式處理權杖取得和更新作業

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
var firstAccount = accounts.FirstOrDefault();
authResult = await App.PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                      .ExecuteAsync();
```

* `scopes` 包含所要求的範圍，例如適用於 Microsoft Graph 的 `{ "user.read" }` 或適用於自訂 Web API 的 `{ "api://<Application ID>/access_as_user" }`。
* `firstAccount` 指定快取中的第一個使用者帳戶 (MSAL 在單一應用程式中支援多個使用者)。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>後續步驟

嘗試 Windows Desktop 教學課程以取得建置應用程式與新功能的完整逐步指南，包括此快速入門的完整說明。

> [!div class="nextstepaction"]
> [UWP - 呼叫圖形 API 教學課程](tutorial-v2-windows-uwp.md)
