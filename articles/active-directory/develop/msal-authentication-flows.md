---
title: MSAL 驗證流程 | Azure
titleSuffix: Microsoft identity platform
description: 了解 Microsoft 驗證程式庫 (MSAL) 所使用的驗證流程和授權。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: ce81af90baeeda519f1b56d1e10a46923ebd22c2
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83772126"
---
# <a name="authentication-flows"></a>驗證流程

本文說明 Microsoft 驗證程式庫 (MSAL) 所提供的不同驗證流程。  這些流程可以在各種不同的應用程式案例中使用。

| Flow | 描述 | 使用於|  
| ---- | ----------- | ------- | 
| [互動式](#interactive) | 透過互動式程序取得權杖，該程序會透過瀏覽器或快顯視窗提示使用者提供認證。 | [傳統型應用程式](scenario-desktop-overview.md)、[行動應用程式](scenario-mobile-overview.md) |
| [隱含授與](#implicit-grant) | 允許應用程式取得權杖，而不需執行後端伺服器認證交換。 這可讓應用程式登入使用者、維護工作階段，並且取得用戶端 JavaScript 程式碼中所有其他 Web API 的權杖。| [單頁應用程式 (SPA)](scenario-spa-overview.md) |
| [授權碼](#authorization-code) | 使用於裝置上所安裝的應用程式中，以存取受保護的資源，例如 Web API。 這可讓您將登入和 API 存取權新增至您的行動和傳統型應用程式。 | [傳統型應用程式](scenario-desktop-overview.md)、[行動應用程式](scenario-mobile-overview.md)、[Web 應用程式](scenario-web-app-call-api-overview.md) | 
| [代理者](#on-behalf-of) | 應用程式會叫用服務或 Web API，而這又需要呼叫另一個服務或 Web API。 其概念是透過要求鏈傳播委派的使用者身分識別和權限。 | [Web API](scenario-web-api-call-api-overview.md) |
| [用戶端認證](#client-credentials) | 可讓您使用應用程式的身分識別來存取 Web 裝載資源。 通常用於必須在背景中執行 (不需與使用者直接互動) 的伺服器對伺服器互動。 | [精靈應用程式](scenario-daemon-overview.md) |
| [裝置程式碼](#device-code) | 可讓使用者登入輸入受限的裝置，例如智慧型電視、IoT 裝置或印表機。 | [傳統型/行動應用程式](scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser) |
| [整合式 Windows 驗證](scenario-desktop-acquire-token.md#integrated-windows-authentication) | 允許已加入網域或 Azure Active Directory (Azure AD) 電腦上的應用程式以無訊息方式取得權杖 (使用者不需要進行任何 UI 互動)。| [傳統型/行動應用程式](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [使用者名稱/密碼](scenario-desktop-acquire-token.md#username-and-password) | 可讓應用程式直接處理其密碼來登入使用者。 不建議使用此流程。 | [傳統型/行動應用程式](scenario-desktop-acquire-token.md#username-and-password) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>每個流程如何發出權杖和授權碼
 
視用戶端的建置方式而定，其可使用 Microsoft 身分識別平台所支援的一或多個驗證流程。  這些流程可能會產生各種權杖 (id_tokens、重新整理權杖、存取權杖) 以及授權碼，而且需要不同的權杖才能使其運作。 此圖表提供概觀：
 
|Flow | 需要 | id_token | 存取權杖 | 重新整理權杖 | 授權碼 | 
|-----|----------|----------|--------------|---------------|--------------------|
|[授權碼流程](v2-oauth2-auth-code-flow.md) | | x | x | x | x|  
|[隱含流程](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[混合式 OIDC 流程](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[重新整理權杖兌換](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | 重新整理權杖 | x | x | x| |
|[代理者流程](v2-oauth2-on-behalf-of-flow.md) | 存取權杖| x| x| x| |
|[裝置碼流程](v2-oauth2-device-code.md) | | x| x| x| |
|[用戶端認證](v2-oauth2-client-creds-grant-flow.md) | | | x (僅限應用程式)| | |
 
透過隱含模式發出的權杖具有長度限制，因為其會透過 URL 傳遞回瀏覽器 (其中 `response_mode` 是 `query` 或 `fragment`)。  有些瀏覽器對於可放在瀏覽器列中的 URL 大小有所限制，而太長時會失敗。  因此，這些權杖沒有 `groups` 或 `wids` 宣告。

## <a name="interactive"></a>互動式

MSAL 支援以互動方式提示使用者提供其認證以進行登入，並使用這些認證取得權杖。

![互動式流程的圖表](media/msal-authentication-flows/interactive.png)

如需在特定平台上使用 MSAL.NET 以互動方式取得權杖的詳細資訊，請參閱：
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [通用 Windows 平台](msal-net-uwp-considerations.md)

如需 MSAL 中互動式呼叫的詳細資訊，請參閱 [MSAL.js 互動式要求中的提示行為](msal-js-prompt-behavior.md)。

## <a name="implicit-grant"></a>隱含授與

MSAL 支援 [OAuth 2 隱含授與流程](v2-oauth2-implicit-grant-flow.md)，該流程可讓應用程式從 Microsoft 身分識別平台取得權杖，而不需執行後端伺服器認證交換。 這可讓應用程式登入使用者、維護工作階段，並且取得用戶端 JavaScript 程式碼中所有其他 Web API 的權杖。

![隱含授與流程的圖表](media/msal-authentication-flows/implicit-grant.svg)

許多新式 Web 應用程式都會建置為以 JavaScript 或 SPA 架構 (例如 Angular、Vue.js 和 React.js) 撰寫的用戶端單頁應用程式。 這些應用程式會在網頁瀏覽器中執行，且具有與傳統伺服器端 Web 應用程式不同的驗證特性。 Microsoft 身分識別平台可讓單頁應用程式登入使用者，並使用隱含授與流程來取得權杖以存取後端服務或 Web API。 隱含流程可讓應用程式取得識別碼權杖以代表經過驗證的使用者，也可以存取呼叫受保護的 API 所需的權杖。

此驗證流程不包含使用跨平台 JavaScript 架構 (例如 Electron 和 React-Native) 的應用程式案例，因為其需要進一步的功能來與原生平台互動。

## <a name="authorization-code"></a>授權碼

MSAL 支援 [OAuth 2 授權碼授與](v2-oauth2-auth-code-flow.md)。 此授與可使用於裝置上所安裝的應用程式中，以存取受保護的資源，例如 Web API。 這可讓您將登入和 API 存取權新增至您的行動和傳統型應用程式。 

當使用者登入 Web 應用程式 (網站) 時，Web 應用程式會收到授權碼。  兌換授權碼可取得權杖來呼叫 Web API。 在 ASP.NET 和 ASP.NET Core Web 應用程式中，`AcquireTokenByAuthorizationCode` 唯一的目標就是要將權杖新增至權杖快取。 然後應用程式即可使用此權杖 (通常是在控制器中，這就是 API 使用 `AcquireTokenSilent` 取得的權杖)。

![授權碼流程的圖表](media/msal-authentication-flows/authorization-code.png)

在上圖中，應用程式：

1. 要求授權碼，其可兌換為存取權杖。
2. 使用存取權杖來呼叫 Web API。

### <a name="considerations"></a>考量

- 您只能使用授權碼一次來兌換權杖。 請勿嘗試使用相同的授權碼多次取得權杖 (這是通訊協定標準規格所明確禁止的)。 如果您刻意兌換授權碼數次，或因為您未察覺到架構也會為您執行此動作，則會收到下列錯誤：`AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- 當您撰寫 ASP.NET 或 ASP.NET Core 應用程式時，如果您未告知架構您已經兌換授權碼，就可能發生這種情況。 為此，您必須呼叫 `AuthorizationCodeReceived` 事件處理常式的 `context.HandleCodeRedemption()` 方法。

- 避免與 ASP.NET 共用存取權杖，這可能會導致無法正確地進行累加式同意。 如需詳細資訊，請參閱[問題 #693](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693)。

## <a name="on-behalf-of"></a>代理者

MSAL 支援 [OAuth 2 代理者驗證流程](v2-oauth2-on-behalf-of-flow.md)。  此流程使用於應用程式叫用服務或 Web API 時，而這又需要呼叫另一個服務或 Web API。 其概念是透過要求鏈傳播委派的使用者身分識別和權限。 若要讓中介層服務向下游服務提出已驗證的要求，其需要代表使用者保護來自 Microsoft 身分識別平台的存取權杖。

![代理者流程的圖表](media/msal-authentication-flows/on-behalf-of.png)

在上圖中︰

1. 應用程式會取得 Web API 的存取權杖。
2. 用戶端 (Web、傳統型、行動或單頁應用程式) 會呼叫受保護的 Web API，並在 HTTP 要求的驗證標頭中新增存取權杖作為持有人權杖。 Web API 會驗證使用者。
3. 當用戶端呼叫 Web API 時，Web API 會代表使用者要求另一個權杖。  
4. 受保護的 Web API 會使用此權杖，代表使用者呼叫下游 Web API。  Web API 稍後也可以要求其他下游 API (但仍代表相同的使用者) 的權杖。

## <a name="client-credentials"></a>用戶端認證

MSAL 支援 [OAuth 2 用戶端認證流程](v2-oauth2-client-creds-grant-flow.md)。 此流程可讓您使用應用程式的身分識別來存取 Web 裝載資源。 這類型的授與通常用於必須在背景中執行 (不需與使用者直接互動) 的伺服器對伺服器互動。 這些類型的應用程式通常稱為精靈或服務帳戶。 

用戶端認證授與流程可允許 Web 服務 (機密用戶端) 在呼叫另一個 Web 服務時，使用自己的認證來進行驗證，而不是模擬使用者。 在此案例中，用戶端通常是中介層 Web 服務、精靈服務或網站。 對於較高層級的保證，Microsoft 身分識別平台也可讓呼叫服務使用憑證 (而非共用密碼) 做為認證。

> [!NOTE]
> 機密用戶端流程不適用於行動平台 (UWP、Xamarin 和 Xamarin)，因為這些平台僅支援公用用戶端應用程式。 公用用戶端應用程式不知道如何向識別提供者證明應用程式的身分識別。 藉由部署憑證，即可在 Web 應用程式或 Web API 後端上達成安全連線。

MSAL.NET 支援兩種類型的用戶端認證。 這些用戶端認證必須向 Azure AD 註冊。 認證會傳遞至您程式碼中機密用戶端應用程式的建構函式。

### <a name="application-secrets"></a>應用程式祕密

![具有密碼的機密用戶端圖表](media/msal-authentication-flows/confidential-client-password.png)

在上圖中，應用程式：

1. 使用應用程式秘密或密碼認證來取得權杖。
2. 使用權杖來提出資源的要求。

### <a name="certificates"></a>憑證

![具有憑證的機密用戶端圖表](media/msal-authentication-flows/confidential-client-certificate.png)

在上圖中，應用程式：

1. 使用憑證認證來取得權杖。
2. 使用權杖來提出資源的要求。

這些用戶端認證必須：
- 已向 Azure AD 註冊。
- 在您程式碼中建構機密用戶端應用程式時傳入。

## <a name="device-code"></a>裝置碼

MSAL 支援 [OAuth 2 裝置碼流程](v2-oauth2-device-code.md)，該流程允許使用者登入具有輸入限制的裝置，例如智慧型電視、物聯網裝置或印表機。 使用 Azure AD 的互動式驗證需要網頁瀏覽器。 如果裝置或作業系統不提供網頁瀏覽器，裝置碼流程可讓使用者使用另一部裝置 (例如其他電腦或行動電話) 以互動方式登入。

使用裝置碼流程，應用程式就能透過針對這些裝置或作業系統所特別設計的雙步驟程序來取得權杖。 這類應用程式的範例包括在 IoT 裝置或命令列工具 (CLI) 上執行的應用程式。 

![裝置碼流程的圖表](media/msal-authentication-flows/device-code.png)

在上圖中︰

1. 每當需要使用者驗證時，應用程式就會提供裝置碼並要求使用者使用另一部裝置 (例如連到網際網路的智慧型手機) 來前往 URL (例如 `https://microsoft.com/devicelogin`)。 接著，系統會提示使用者輸入代碼，以繼續進行一般驗證體驗，其中包括同意提示和[多重要素驗證](../authentication/concept-mfa-howitworks.md) (如有需要)。

2. 成功驗證之後，命令列應用程式會透過後端通道接收所需的權杖，並使用這些權杖來執行所需的 Web API 呼叫。

### <a name="constraints"></a>條件約束

- 裝置碼流程僅適用於公用用戶端應用程式。
- 在建構公用用戶端應用程式時傳入的授權單位，必須是下列其中一項：
  - `https://login.microsoftonline.com/{tenant}/` 形式的租用戶，其中 `{tenant}` 是代表租用戶識別碼的 GUID，或與租用戶相關聯的網域。
  - 適用於任何公司和學校帳戶 (`https://login.microsoftonline.com/organizations/`)。
- Azure AD v2.0 端點尚未支援 Microsoft 個人帳戶 (您無法使用 `/common` 或 `/consumers` 租使用者)。

## <a name="integrated-windows-authentication"></a>整合式 Windows 驗證

對於已加入網域或已加入 Azure AD 的 Windows 電腦上執行的傳統型或行動應用程式，MSAL 可支援整合式 Windows 驗證 (IWA)。 這些應用程式可以使用 IWA，以無訊息方式取得權杖 (使用者不需要任何 UI 互動)。 

![整合式 Windows 驗證的圖表](media/msal-authentication-flows/integrated-windows-authentication.png)

在上圖中，應用程式：

1. 使用整合式 Windows 驗證取得權杖。
2. 使用權杖來提出資源的要求。

### <a name="constraints"></a>條件約束

IWA 僅支援同盟使用者，這表示在 Active Directory 中建立並由 Azure AD 支援的使用者。 直接在 Azure AD 中建立的使用者若沒有 Active Directory 支援 (受控使用者)，就無法使用此驗證流程。 這項限制並不會影響[使用者名稱/密碼流程](#usernamepassword)。

IWA 適用於針對 .NET Framework、.NET Core 和通用 Windows 平台 (UWP) 平台所撰寫的應用程式。

IWA 不會略過多重要素驗證。 若已設定多重要素驗證，如果需要多重要素驗證挑戰，IWA 可能會失敗。 多重要素驗證需要使用者互動。

您無法控制何時要執行識別提供者要求雙重要素驗證。 租用戶系統管理員可以辦到。 通常，當您從不同的國家/地區登入、未透過 VPN 連線到公司網路，有時甚至是透過 VPN 連線時，都需要雙重要素驗證。 Azure AD 會使用 AI 來持續了解是否需要雙重要素驗證。 如果 IWA 失敗，您應該會回到 [互動式使用者提示] (#interactive)。

在建構公用用戶端應用程式時傳入的授權單位，必須是下列其中一項：
- `https://login.microsoftonline.com/{tenant}/` 形式的租用戶，其中 `tenant` 是代表租用戶識別碼的 guid，或與租用戶相關聯的網域。
- 適用於任何公司和學校帳戶 (`https://login.microsoftonline.com/organizations/`)。 不支援 Microsoft 個人帳戶 (您無法使用 `/common` 或 `/consumers` 租用戶)。

因為 IWA 是無訊息流程，所以必須符合下列其中一個條件：
- 您應用程式的使用者先前必須已同意才能使用應用程式。 
- 租用戶系統管理員先前必須已同意租用戶中的所有使用者，才能使用該應用程式。

這表示下列其中一個條件成立：
- 您身為開發人員，並已自行在 Azure 入口網站上選取 [授與]。
- 租用戶系統管理員已在應用程式註冊的 [API 權限] 索引標籤中，選取 [授與/撤銷 {租用戶網域} 系統管理員同意](請參閱[新增權限以存取 Web API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis))。
- 您已提供讓使用者同意應用程式的方式 (請參閱[要求個別使用者同意](v2-permissions-and-consent.md#requesting-individual-user-consent))。
- 您也提供讓租用戶系統管理員同意應用程式的方式 (請參閱[系統管理員同意](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant))。

已針對 .NET 傳統型、.NET Core 和 Windows 通用平台應用程式啟用 IWA 流程。 在 .NET Core 上，您必須將使用者名稱提供給 IWA，因為 .NET Core 無法從作業系統取得使用者名稱。
  
如需同意的詳細資訊，請參閱 [v2.0 權限和同意](v2-permissions-and-consent.md)。

## <a name="usernamepassword"></a>使用者名稱/密碼

MSAL 支援 [OAuth 2 資源擁有者密碼認證授與](v2-oauth-ropc.md)，可讓應用程式藉由直接處理其密碼來登入使用者。 在傳統型應用程式中，您可使用使用者名稱/密碼流程，以無訊息方式取得權杖。 使用此應用程式時不需要 UI。

![使用者名稱/密碼流程的圖表](media/msal-authentication-flows/username-password.png)

在上圖中，應用程式：

1. 藉由將使用者名稱和密碼傳送給識別提供者來取得權杖。
2. 使用權杖來呼叫 Web API。

> [!WARNING]
> 不建議使用此流程。 這需要高度的信任和使用者公開程度。  您應該只在其他更安全的流程都無法使用時，才使用此流程。 如需詳細資訊，請參閱[持續增加的密碼問題有何解決方案？](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)。 

在已加入網域的 Windows 電腦上以無訊息方式取得權杖的慣用流程是[整合式 Windows 驗證](#integrated-windows-authentication)。 否則，您也可使用[裝置碼流程](#device-code)。

雖然在某些情況 (DevOps 案例) 下，該流程很有用，但如果您想在提供自有 UI 的互動式案例中使用使用者名稱/密碼，請試著避免使用該流程。 藉由使用使用者名稱/密碼：
- 需要無法多重要素驗證的使用者將無法登入 (因為沒有互動)。
- 使用者將無法執行單一登入。

### <a name="constraints"></a>條件約束

除了[整合式 Windows 驗證條件約束](#integrated-windows-authentication)之外，也適用下列條件約束：

- 使用者名稱/密碼流程與條件式存取和多重要素驗證不相容。 因此，如果您的應用程式在租用戶系統管理員需要多重要素驗證的 Azure AD 租用戶中執行，您就無法使用此流程。 許多組織都會這麼做。
- 這僅適用於公司和學校帳戶 (而非 Microsoft 帳戶)。
- 此流程適用於 .NET 傳統型和 .NET Core，但不適用於通用 Windows 平台。

### <a name="azure-ad-b2c-specifics"></a>Azure AD B2C 特性

如需在 MSAL.NET 和 Azure AD B2C 中使用 ROPC 的詳細資訊，請參閱[使用 ROPC 搭配 Azure AD B2C](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc)。
