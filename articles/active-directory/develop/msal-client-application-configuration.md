---
title: 用戶端應用程式配置 （MSAL） |蔚藍
titleSuffix: Microsoft identity platform
description: 使用 Microsoft 身份驗證庫 （MSAL） 瞭解公共用戶端和機密用戶端應用程式的配置選項。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/27/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 0262d22ae00456ce06cb8efbf995f1a093b20043
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262889"
---
# <a name="application-configuration-options"></a>應用程式佈建選項

在代碼中，您可以初始化新的公共或機密用戶端應用程式（或 MSAL.js 的使用者代理），以驗證和獲取權杖。 在 Microsoft 身份驗證庫 （MSAL） 中初始化用戶端應用時，可以設置許多配置選項。 這些選項分為兩組：

- 註冊選項，包括：
    - [頒發機構](#authority)（由應用程式的身份提供程式[實例](#cloud-instance)和登錄[訪問群體](#application-audience)組成，可能包括租戶 ID）。
    - [用戶端 ID](#client-id)。
    - [重定向 URI](#redirect-uri)。
    - [用戶端機密](#client-secret)（用於機密用戶端應用程式）。
- [日誌記錄選項](#logging)，包括日誌級別、個人資料控制以及使用庫的元件的名稱。

## <a name="authority"></a>授權單位

該許可權是一個 URL，指示 MSAL 可以從中請求權杖的目錄。 共同當局是：

- HTTPs\:/login.microsoftonline.com/\<租戶\>/，&lt;其中&gt;租戶是 Azure 活動目錄 （Azure AD） 租戶的租戶 ID 或與此 Azure AD 租戶關聯的域。 僅用於登錄到特定組織的使用者。
- HTTPs\:/login.microsoftonline.com/common/。 用於使用工作帳戶、學校帳戶或個人 Microsoft 帳戶登錄使用者。
- HTTPs\://login.microsoftonline.com/organizations/。 用於使用工作和學校帳戶登錄使用者。
- HTTPs\:/login.microsoftonline.com/consumers/。 用於僅使用個人 Microsoft 帳戶（以前稱為 Windows Live ID 帳戶）登錄使用者。

許可權設置需要與應用程式註冊門戶中聲明的內容一致。

許可權 URL 由實例和訪問群體組成。

許可權可以是：
- Azure AD 雲頒發機構。
- Azure AD B2C 許可權。 請參閱[B2C 細節](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics)。
- 活動目錄聯合服務 （AD FS） 許可權。 請參閱[AD FS 支援](https://aka.ms/msal-net-adfs-support)。

Azure AD 雲頒發機構分為兩部分：
- 標識提供程式*實例*
- 應用程式的登錄*受眾*

實例和訪問群體可以串聯並作為許可權 URL 提供。 在 MSAL.NET 版本中早于 MSAL 3。*x，* 你必須自己組成權威，基於你想瞄準的雲和登錄觀眾。  此圖顯示了許可權 URL 的組成方式：

![如何組成許可權 URL](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>雲實例

該*實例*用於指定應用是從 Azure 公共雲或國家雲對使用者進行簽名。 在代碼中使用 MSAL，可以使用枚舉或將 URL 作為`Instance`成員（如果您知道）傳遞給[國家雲實例](authentication-national-cloud.md#azure-ad-authentication-endpoints)來設置 Azure 雲實例。

如果兩者都`Instance`指定和`AzureCloudInstance`，MSAL.NET將引發顯式異常。

如果不指定實例，你的應用將針對 Azure 公共雲實例（URL`https://login.onmicrosoftonline.com`實例）。

## <a name="application-audience"></a>應用程式受眾

登錄受眾取決於應用的業務需求：
- 如果您是業務線 （LOB） 開發人員，則可能會生成僅在組織中使用的單租戶應用程式。 在這種情況下，您需要通過其租戶 ID（Azure AD 實例的 ID）或與 Azure AD 實例關聯的功能變數名稱指定組織。
- 如果您是 ISV，則可能需要在任何組織或某些組織（多租戶應用）中使用其工作和學校帳戶登錄使用者。 但是，您可能還希望讓使用者使用其個人 Microsoft 帳戶登錄。

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>如何在代碼/配置中指定受眾

在代碼中使用 MSAL，可以使用以下值之一指定訪問群體：
- Azure AD 許可權訪問群體枚舉
- 租戶 ID，可以是：
  - 適用于單租戶應用程式的 GUID（Azure AD 實例的 ID）
  - 與 Azure AD 實例關聯的功能變數名稱（也用於單租戶應用程式）
- 這些預留位置之一作為租戶 ID 代替 Azure AD 許可權訪問群體枚舉：
    - `organizations`對於多租戶應用程式
    - `consumers`僅使用其個人帳戶登錄使用者
    - `common`使用他們的工作和學校帳戶或個人 Microsoft 帳戶登錄使用者

如果同時指定 Azure AD 許可權訪問群體和租戶 ID，MSAL 將引發有意義的異常。

如果不指定受眾，你的應用將目標為 Azure AD 和個人 Microsoft 帳戶作為受眾。 （也就是說，它行為將像`common`指定的那樣。

### <a name="effective-audience"></a>有效受眾

應用程式的有效受眾將是您在應用中設置的受眾的最小（如果有交叉點）以及應用註冊中指定的受眾。 事實上，[應用註冊](https://aka.ms/appregistrations)體驗允許您為應用指定訪問群體（支援的帳戶類型）。 有關詳細資訊，請參閱[快速入門：使用 Microsoft 標識平臺註冊應用程式](quickstart-register-app.md)。

目前，讓應用僅使用個人 Microsoft 帳戶登錄使用者的唯一方法是配置以下兩種設置：
- 將應用註冊訪問群體設置為`Work and school accounts and personal accounts`。
- 將代碼/配置中的受眾設置為`AadAuthorityAudience.PersonalMicrosoftAccount`（或`TenantID`="消費者"）。

## <a name="client-id"></a>用戶端識別碼

用戶端 ID 是 Azure AD 在註冊應用時分配給應用的唯一應用程式（用戶端）ID。

## <a name="redirect-uri"></a>重新導向 URI

重定向 URI 是標識提供程式將安全權杖發送回 URI。

### <a name="redirect-uri-for-public-client-apps"></a>公共用戶端應用重定向 URI

如果您是使用 MSAL 的公共用戶端應用開發人員：
- 您希望在桌面或 UWP 應用程式（MSAL.NET 4.1+）中使用`.WithDefaultRedirectUri()`。 此方法將公共用戶端應用程式的重定向 uri 屬性設置為公共用戶端應用程式的預設建議重定向 uri。 

  Platform  | 重新導向 URI  
  ---------  | --------------
  桌面應用 （.NET FW） | `https://login.microsoftonline.com/common/oauth2/nativeclient` 
  UWP | 的值`WebAuthenticationBroker.GetCurrentApplicationCallbackUri()`。 這將通過將值設置為 Web 身份驗證代理的結果，從而啟用 SSO
  .NET Core | `https://localhost`. 這使使用者能夠使用系統瀏覽器進行互動式身份驗證，因為 .NET Core 目前沒有嵌入式 Web 視圖的 UI。

- 如果您正在構建不支援代理的 Xamarin Android 和 iOS 應用程式（重定向 URI 自動設置為`msal{ClientId}://auth`Xamarin Android 和 iOS，則無需添加重定向 URI）

- 您需要在[應用註冊中](https://aka.ms/appregistrations)配置重定向 URI：

   ![在應用註冊中重定向 URI](media/msal-client-application-configuration/redirect-uri.png)

可以使用`RedirectUri`屬性覆蓋重定向 URI（例如，如果使用代理）。 下面是該方案重定向 URI 的一些示例：

- `RedirectUriOnAndroid`• "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc：//com.microsoft.身份.client.sample";
- `RedirectUriOnIos`= $"msauth。[捆綁.ID]";

有關其他 iOS 詳細資訊，請參閱[將使用 Microsoft 身份驗證器的應用程式從 ADAL.NET 遷移到MSAL.NET，](msal-net-migration-ios-broker.md)並在[iOS 上利用代理](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS)。
有關其他 Android 詳細資訊，請參閱[Android 中的代理 auth](brokered-auth.md)。

### <a name="redirect-uri-for-confidential-client-apps"></a>重定向用於機密用戶端應用的 URI

對於 Web 應用，重定向 URI（或答覆 URI）是 Azure AD 將用於將權杖發送回應用程式的 URI。 如果機密應用是其中之一，則此 URI 可以是 Web 應用/Web API 的 URL。 重定向 URI 需要在應用註冊中註冊。 當您部署最初在本地測試的應用時，此註冊尤其重要。 然後，您需要在應用程式註冊門戶中添加已部署應用的回復 URL。

對於守護進程應用，您無需指定重定向 URI。

## <a name="client-secret"></a>用戶端密碼

此選項指定機密用戶端應用的用戶端機密。 此機密（應用密碼）由應用程式註冊門戶提供，或在使用 PowerShell AzureAD、PowerShell AzureRM 或 Azure CLI 進行應用註冊期間提供給 Azure AD。

## <a name="logging"></a>記錄

其他配置選項支援日誌記錄和故障排除。 有關如何使用它們的詳細資訊，請參閱[日誌記錄](msal-logging.md)文章。

## <a name="next-steps"></a>後續步驟

瞭解如何[使用MSAL.NET具現化用戶端應用程式](msal-net-initializing-client-applications.md)。
瞭解如何[使用 MSAL.js 具現化用戶端應用程式](msal-js-initializing-client-applications.md)。
