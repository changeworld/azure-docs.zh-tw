---
title: MSAL 驗證流程 |Azure
titleSuffix: Microsoft identity platform
description: 深入瞭解 Microsoft 驗證程式庫（MSAL）所使用的驗證流程和授權。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/30/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: ace636152f6a0c9bf3896860eb17cc291bef2887
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2020
ms.locfileid: "77085123"
---
# <a name="authentication-flows"></a>驗證流程

本文說明 Microsoft Authentication Library （MSAL）所提供的不同驗證流程。  這些流程可以在各種不同的應用程式案例中使用。

| Flow | 描述 | 使用於|  
| ---- | ----------- | ------- | 
| [互動式](#interactive) | 透過互動式進程取得權杖，此程式會透過瀏覽器或快顯視窗提示使用者提供認證。 | [桌面應用程式](scenario-desktop-overview.md)、行動[應用程式](scenario-mobile-overview.md) |
| [隱含授與](#implicit-grant) | 允許應用程式取得權杖，而不需要執行後端伺服器認證交換。 這可讓應用程式在用戶端 JavaScript 程式碼內，登入使用者、維護會話，以及取得其他 web Api 的權杖。| [單一頁面應用程式（SPA）](scenario-spa-overview.md) |
| [授權碼](#authorization-code) | 用於安裝在裝置上的應用程式，以存取受保護的資源，例如 web Api。 這可讓您將登入和 API 存取權新增至您的行動和桌面應用程式。 | [桌面應用程式](scenario-desktop-overview.md)、行動[應用程式](scenario-mobile-overview.md)、 [web 應用](scenario-web-app-call-api-overview.md)程式 | 
| [代理者](#on-behalf-of) | 應用程式會叫用服務或 Web API，而這又需要呼叫另一個服務或 Web API。 其概念是透過要求鏈傳播委派的使用者身分識別和權限。 | [Web API](scenario-web-api-call-api-overview.md) |
| [用戶端認證](#client-credentials) | 可讓您使用應用程式的身分識別來存取 web 主控的資源。 通常用於必須在背景中執行的伺服器對伺服器互動，而不需要立即與使用者互動。 | [Daemon 應用程式](scenario-daemon-overview.md) |
| [裝置程式碼](#device-code) | 可讓使用者登入受輸入限制的裝置，例如智慧型電視、IoT 裝置或印表機。 | [桌面/行動應用程式](scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser) |
| [整合式 Windows 驗證](scenario-desktop-acquire-token.md#integrated-windows-authentication) | 允許已加入網域或 Azure Active Directory （Azure AD）電腦上的應用程式以無訊息方式取得權杖（不需要使用者的任何 UI 互動）。| [桌面/行動應用程式](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [使用者名稱/密碼](scenario-desktop-acquire-token.md#username-and-password) | 允許應用程式藉由直接處理其密碼來登入使用者。 不建議使用此流程。 | [桌面/行動應用程式](scenario-desktop-acquire-token.md#username-and-password) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>每個流程如何發出權杖和代碼
 
視用戶端的建立方式而定，它可以使用 Microsoft 身分識別平臺所支援的一或多個驗證流程。  這些流程可能會產生各種權杖（id_tokens、重新整理權杖、存取權杖）以及授權碼，而且需要不同的權杖才能使其運作。 此圖表提供總覽：
 
|Flow | 具備 | id_token | 存取權杖 | 重新整理權杖 | 授權碼 | 
|-----|----------|----------|--------------|---------------|--------------------|
|[授權碼流程](v2-oauth2-auth-code-flow.md) | | x | x | x | x|  
|[隱含流程](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[混合式 OIDC 流程](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[重新整理權杖兌換](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | 重新整理權杖 | x | x | x| |
|[代理者流程](v2-oauth2-on-behalf-of-flow.md) | 存取權杖| x| x| x| |
|[裝置程式碼流程](v2-oauth2-device-code.md) | | x| x| x| |
|[用戶端認證](v2-oauth2-client-creds-grant-flow.md) | | | x （僅限應用程式）| | |
 
透過隱含模式發行的權杖具有長度限制，因為會透過 URL 傳遞回瀏覽器（其中 `response_mode` 是 `query` 或 `fragment`）。  有些瀏覽器對於可以放在瀏覽器列中的 URL 大小有限制，而且太長時也會失敗。  因此，這些權杖沒有 `groups` 或 `wids` 宣告。

## <a name="interactive"></a>互動式

MSAL 支援以互動方式提示使用者輸入其認證以進行登入，並使用這些認證取得權杖的功能。

![互動式流程的圖表](media/msal-authentication-flows/interactive.png)

如需在特定平臺上使用 MSAL.NET 以互動方式取得權杖的詳細資訊，請參閱：
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [通用 Windows 平台](msal-net-uwp-considerations.md)

如需 MSAL 中互動式呼叫的詳細資訊，請參閱[MSAL 互動式要求中的提示行為](msal-js-prompt-behavior.md)。

## <a name="implicit-grant"></a>隱含授與

MSAL 支援[OAuth 2 隱含授與流程](v2-oauth2-implicit-grant-flow.md)，可讓應用程式從 Microsoft 身分識別平臺取得權杖，而不需要執行後端伺服器認證交換。 這可讓應用程式在用戶端 JavaScript 程式碼內，登入使用者、維護會話，以及取得其他 web Api 的權杖。

![隱含授與流程的圖表](media/msal-authentication-flows/implicit-grant.svg)

許多新式 web 應用程式都是以用戶端、單一頁面應用程式的形式建立，使用 JavaScript 或 SPA 架構（例如，角度、Vue 和回應 .js）來撰寫。 這些應用程式會在網頁瀏覽器中執行，並具有與傳統伺服器端 web 應用程式不同的驗證特性。 Microsoft 身分識別平臺可讓單一頁面應用程式登入使用者，並使用隱含授與流程來取得權杖，以存取後端服務或 web Api。 隱含流程可讓應用程式取得識別碼權杖以代表已驗證的使用者，也可以存取呼叫受保護的 Api 所需的權杖。

此驗證流程不包括使用跨平臺 JavaScript 架構（例如 Electron 和回應原生）的應用程式案例，因為它們需要進一步的功能來與原生平臺互動。

## <a name="authorization-code"></a>授權碼

MSAL 支援[OAuth 2 授權碼授](v2-oauth2-auth-code-flow.md)與。 此授與可用於裝置上安裝的應用程式，以取得受保護資源（例如 web Api）的存取權。 這可讓您將登入和 API 存取權新增至您的行動和桌面應用程式。 

當使用者登入 web 應用程式（網站）時，web 應用程式會收到授權碼。  授權碼會兌換以取得權杖以呼叫 web Api。 在 ASP.NET 和 ASP.NET Core web apps 中，`AcquireTokenByAuthorizationCode` 唯一的目標是要將權杖新增至權杖快取。 然後，應用程式可以使用此權杖（通常是在控制器中，使用 `AcquireTokenSilent`來取得 API 的權杖）。

![授權碼流程的圖表](media/msal-authentication-flows/authorization-code.png)

在上圖中，應用程式：

1. 要求授權碼，其兌換為存取權杖。
2. 使用存取權杖來呼叫 Web API。

### <a name="considerations"></a>考量

- 您只能使用授權碼一次來兌換權杖。 請不要嘗試使用相同的授權碼多次取得權杖（這是通訊協定標準規格明確禁止的）。 如果您刻意兌換程式碼數次，或因為您不知道架構也會為您執行，您會收到下列錯誤： `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- 如果您要撰寫 ASP.NET 或 ASP.NET Core 的應用程式，當您不知道架構已經兌換授權碼時，就可能會發生這種情況。 為此，您必須呼叫 `AuthorizationCodeReceived` 事件處理常式的 `context.HandleCodeRedemption()` 方法。

- 避免與 ASP.NET 共用存取權杖，這可能會導致無法正確地進行累加式同意。 如需詳細資訊，請參閱[問題 #693](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693)。

## <a name="on-behalf-of"></a>代理者

MSAL 支援[OAuth 2 代理者驗證流程](v2-oauth2-on-behalf-of-flow.md)。  當應用程式叫用服務或 Web API 時，就會使用這個流程，而這又需要呼叫另一個服務或 Web API。 其概念是透過要求鏈傳播委派的使用者身分識別和權限。 若要讓仲介層服務向下游服務提出已驗證的要求，它需要代表使用者保護來自 Microsoft 身分識別平臺的存取權杖。

![代理者流程的圖表](media/msal-authentication-flows/on-behalf-of.png)

在上圖中：

1. 應用程式會取得 Web API 的存取權杖。
2. 用戶端（web、桌面、行動或單頁應用程式）會呼叫受保護的 Web API，並在 HTTP 要求的驗證標頭中新增存取權杖做為持有人權杖。 Web API 會驗證使用者。
3. 當用戶端呼叫 Web API 時，Web API 會代表使用者要求另一個權杖。  
4. 受保護的 Web API 會使用此權杖，以代表使用者呼叫下游 Web API。  Web API 稍後也可以要求其他下游 Api （但仍代表相同的使用者）的權杖。

## <a name="client-credentials"></a>用戶端認證

MSAL 支援[OAuth 2 用戶端認證流程](v2-oauth2-client-creds-grant-flow.md)。 此流程可讓您使用應用程式的身分識別來存取 web 主控的資源。 這類型的授與通常用於必須在背景中執行 (不需與使用者直接互動) 的伺服器對伺服器互動。 這些類型的應用程式通常稱為「守護程式」或「服務帳戶」。 

用戶端認證授與流程可允許 web 服務（機密用戶端）在呼叫另一個 web 服務時，使用自己的認證來進行驗證，而不是模擬使用者。 在此案例中，用戶端通常是仲介層 web 服務、daemon 服務或網站。 對於較高層級的保證，Microsoft 身分識別平台也可讓呼叫服務使用憑證 (而非共用密碼) 做為認證。

> [!NOTE]
> 機密用戶端流程無法在行動平臺（UWP、Xamarin 和 Xamarin）上使用，因為這些功能僅支援公用用戶端應用程式。 公用用戶端應用程式不知道如何向識別提供者證明應用程式的身分識別。 您可以藉由部署憑證，在 web 應用程式或 Web API 後端上達成安全連線。

MSAL.NET 支援兩種類型的用戶端認證。 這些用戶端認證必須向 Azure AD 註冊。 認證會傳遞至您程式碼中的機密用戶端應用程式的函式。

### <a name="application-secrets"></a>應用程式祕密

![具有密碼的機密用戶端圖表](media/msal-authentication-flows/confidential-client-password.png)

在上圖中，應用程式：

1. 使用應用程式密碼或密碼認證來取得權杖。
2. 會使用權杖來提出資源的要求。

### <a name="certificates"></a>憑證

![具有 cert 之機密用戶端的圖表](media/msal-authentication-flows/confidential-client-certificate.png)

在上圖中，應用程式：

1. 使用憑證認證取得權杖。
2. 會使用權杖來提出資源的要求。

這些用戶端認證必須是：
- 已向 Azure AD 註冊。
- 在您的程式碼中，于機密用戶端應用程式的結構內傳入。

## <a name="device-code"></a>裝置程式碼

MSAL 支援[OAuth 2 裝置程式碼流程](v2-oauth2-device-code.md)，可讓使用者登入受輸入限制的裝置，例如智慧型電視、IoT 裝置或印表機。 使用 Azure AD 的互動式驗證需要網頁瀏覽器。 裝置程式碼流程可讓使用者使用另一個裝置（例如，另一部電腦或行動電話）以互動方式登入，其中裝置或作業系統不會提供網頁瀏覽器。

藉由使用裝置程式碼流程，應用程式會透過兩個步驟的進程取得權杖，特別針對這些裝置或作業系統所設計。 這類應用程式的範例包括在 IoT 裝置上執行的或命令列工具（CLI）。 

![裝置程式碼流程的圖表](media/msal-authentication-flows/device-code.png)

在上圖中：

1. 每當需要使用者驗證時，應用程式就會提供代碼，並要求使用者使用另一部裝置（例如連線到網際網路的 smartphone）來移至 URL （例如 https://microsoft.com/devicelogin)。 接著，系統會提示使用者輸入程式碼，並繼續進行一般驗證體驗，包括同意提示和多重要素驗證（如有需要）。

2. 成功驗證之後，命令列應用程式會透過後端通道接收所需的權杖，並使用它們來執行所需的 Web API 呼叫。

### <a name="constraints"></a>條件約束

- 裝置程式碼流程僅適用于公用用戶端應用程式。
- 在建立公用用戶端應用程式時傳入的授權單位，必須是下列其中一項：
  - 租使用者（格式 `https://login.microsoftonline.com/{tenant}/`，其中 `{tenant}` 是代表租使用者識別碼的 GUID 或與租使用者相關聯的網域）。
  - 適用于任何公司和學校帳戶（`https://login.microsoftonline.com/organizations/`）。
- Azure AD v2.0 端點尚未支援 Microsoft 個人帳戶（您無法使用 `/common` 或 `/consumers` 的租使用者）。

## <a name="integrated-windows-authentication"></a>整合式 Windows 驗證

MSAL 支援整合式 Windows 驗證（IWA），適用于在已加入網域或 Azure AD 聯結的 Windows 電腦上執行的桌面或行動應用程式。 這些應用程式可以使用 IWA，以無訊息方式取得權杖（不需要使用者的任何 UI 互動）。 

![整合式 Windows 驗證的圖表](media/msal-authentication-flows/integrated-windows-authentication.png)

在上圖中，應用程式：

1. 使用整合式 Windows 驗證取得權杖。
2. 會使用權杖來提出資源的要求。

### <a name="constraints"></a>條件約束

IWA 僅支援同盟使用者，這表示在 Active Directory 中建立的使用者，以及 Azure AD 的支援。 直接在 Azure AD 中建立的使用者若沒有 Active Directory 支援（受管理的使用者），就無法使用此驗證流程。 這項限制並不會影響使用者[名稱/密碼的流程](#usernamepassword)。

IWA 適用于針對 .NET Framework、.NET Core 和通用 Windows 平臺平臺所撰寫的應用程式。

IWA 不會略過多重要素驗證。 如果設定多重要素驗證，如果需要多重要素驗證挑戰，IWA 可能會失敗。 多重要素驗證需要使用者互動。

您不會控制身分識別提供者要求執行雙因素驗證的時間。 租使用者系統管理員執行。 當您從不同國家/地區登入時，如果您不是透過 VPN 連線到公司網路，甚至是透過 VPN 連線，則通常會需要雙因素驗證。 Azure AD 使用 AI 來持續瞭解是否需要雙因素驗證。 如果 IWA 失敗，您應該回到 [互動式使用者提示] （#interactive）。

在建立公用用戶端應用程式時傳入的授權單位，必須是下列其中一項：
- 租使用者（格式 `https://login.microsoftonline.com/{tenant}/`，其中 `tenant` 是代表租使用者識別碼的 guid 或與租使用者相關聯的網域）。
- 適用于任何公司和學校帳戶（`https://login.microsoftonline.com/organizations/`）。 不支援 Microsoft 個人帳戶（您無法使用 `/common` 或 `/consumers` 租使用者）。

因為 IWA 是無訊息流程，所以必須符合下列其中一項條件：
- 您應用程式的使用者必須先同意使用應用程式。 
- 租使用者系統管理員必須先前已同意租使用者中的所有使用者，才能使用該應用程式。

這表示下列其中一項條件成立：
- 身為開發人員，您已在自己的 Azure 入口網站上選取 **[授**與]。
- 租使用者系統管理員已在應用程式註冊的 [ **API 許可權**] 索引標籤中，選取了 **{tenant Domain} 的 [授與/撤銷管理員同意**] （請參閱[新增存取 web api 的許可權](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)）。
- 您已提供使用者同意應用程式的方式（請參閱[要求個別使用者同意](v2-permissions-and-consent.md#requesting-individual-user-consent)）。
- 您已提供一種方式，讓租使用者系統管理員同意應用程式（請參閱系統[管理員同意](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)）。

已針對 .NET desktop、.NET Core 和 Windows 通用平臺應用程式啟用 IWA 流程。 在 .NET Core 上，您必須提供使用者名稱給 IWA，因為 .NET Core 無法從作業系統取得使用者名稱。
  
如需有關同意的詳細資訊，請參閱 v2.0[許可權和同意](v2-permissions-and-consent.md)。

## <a name="usernamepassword"></a>使用者名稱/密碼

MSAL 支援[OAuth 2 資源擁有者密碼認證授](v2-oauth-ropc.md)與，讓應用程式可以直接處理其密碼來登入使用者。 在您的桌面應用程式中，您可以使用使用者名稱/密碼流程，以無訊息方式取得權杖。 使用應用程式時，不需要 UI。

![使用者名稱/密碼流程的圖表](media/msal-authentication-flows/username-password.png)

在上圖中，應用程式：

1. 藉由傳送使用者名稱和密碼給識別提供者來取得權杖。
2. 使用 token 呼叫 Web API。

> [!WARNING]
> 不建議使用此流程。 它需要高程度的信任和使用者暴露。  當其他、更安全的流程無法使用時，您應該只使用此流程。 如需詳細資訊，請參閱[不斷增加的密碼問題的解決方法？](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)。 

在已加入網域的 Windows 電腦上以無訊息方式取得權杖的慣用流程是[整合式 Windows 驗證](#integrated-windows-authentication)。 否則，您也可以使用[裝置程式碼流程](#device-code)。

雖然這在某些情況下很有用（DevOps 案例），但如果您想要在提供自己 UI 的互動式案例中使用使用者名稱/密碼，請嘗試避免此情況。 使用使用者名稱/密碼：
- 需要執行多重要素驗證的使用者將無法登入（因為沒有互動）。
- 使用者將無法執行單一登入。

### <a name="constraints"></a>條件約束

除了整合式[Windows 驗證條件約束](#integrated-windows-authentication)之外，也適用下列條件約束：

- 使用者名稱/密碼流程與條件式存取和多重要素驗證不相容。 因此，如果您的應用程式在租使用者系統管理員需要多重要素驗證的 Azure AD 租使用者中執行，您就無法使用此流程。 許多組織都會這麼做。
- 僅適用于公司和學校帳戶（非 Microsoft 帳戶）。
- 此流程適用于 .NET desktop 和 .NET Core，但不在通用 Windows 平臺上。

### <a name="azure-ad-b2c-specifics"></a>Azure AD B2C 細節

如需使用 MSAL.NET 和 Azure AD B2C 的詳細資訊，請參閱搭配[Azure AD B2C 使用 ROPC （MSAL.NET）](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc-with-azure-ad-b2c)。
