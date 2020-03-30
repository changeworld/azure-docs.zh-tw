---
title: MSAL 身份驗證流 |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解 Microsoft 身份驗證庫 （MSAL） 使用的身份驗證流和授予。
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
ms.openlocfilehash: 25c219bedbbbec9fbc0c5617c7bd9fc482faf49a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050518"
---
# <a name="authentication-flows"></a>身份驗證流

本文介紹 Microsoft 身份驗證庫 （MSAL） 提供的不同身份驗證流。  這些流可用於各種不同的應用程式方案。

| Flow | 描述 | 使用於|  
| ---- | ----------- | ------- | 
| [互動](#interactive) | 通過互動式進程獲取權杖，該進程通過瀏覽器或快顯視窗提示使用者獲取憑據。 | [桌面應用程式](scenario-desktop-overview.md)，[移動應用程式](scenario-mobile-overview.md) |
| [隱含授與](#implicit-grant) | 允許應用在不執行後端伺服器憑據交換的情況下獲取權杖。 這允許應用登錄使用者、維護會話並將權杖獲取到其他 Web API，所有這些都位於用戶端 JavaScript 代碼中。| [單頁應用程式 (SPA)](scenario-spa-overview.md) |
| [授權代碼](#authorization-code) | 用於安裝在設備上的應用，以訪問受保護的資源，如 Web API。 這允許您向移動和桌面應用添加登錄和 API 存取權限。 | [桌面應用程式](scenario-desktop-overview.md)，[移動應用程式](scenario-mobile-overview.md)，[網路應用程式](scenario-web-app-call-api-overview.md) | 
| [代表](#on-behalf-of) | 應用程式調用服務或 Web API，而服務或 Web API 又需要調用另一個服務或 Web API。 其概念是透過要求鏈傳播委派的使用者身分識別和權限。 | [Web API](scenario-web-api-call-api-overview.md) |
| [用戶端認證](#client-credentials) | 允許您使用應用程式的標識訪問 Web 託管資源。 通常用於必須在後臺運行的伺服器到伺服器的交互，而無需立即與使用者交互。 | [守護進程應用](scenario-daemon-overview.md) |
| [設備代碼](#device-code) | 允許使用者登錄到受輸入約束的設備，如智慧電視、IoT 設備或印表機。 | [桌面/移動應用](scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser) |
| [整合式 Windows 驗證](scenario-desktop-acquire-token.md#integrated-windows-authentication) | 允許域或 Azure 活動目錄 （Azure AD） 聯接電腦上的應用程式靜默地獲取權杖（無需使用者進行任何 UI 交互）。| [桌面/移動應用](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [使用者名/密碼](scenario-desktop-acquire-token.md#username-and-password) | 允許應用程式通過直接處理使用者的密碼來登錄使用者。 不建議使用此流。 | [桌面/移動應用](scenario-desktop-acquire-token.md#username-and-password) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>每個流如何發出權杖和代碼
 
根據用戶端的構建方式，它可以使用 Microsoft 標識平臺支援的身份驗證流的一個（或多個）。  這些流可以生成各種權杖（id_tokens、刷新權杖、訪問權杖）以及授權代碼，並且需要不同的權杖才能使其正常工作。 此圖表提供概述：
 
|Flow | 需要 | id_token | 存取權杖 | 刷新權杖 | 授權碼 | 
|-----|----------|----------|--------------|---------------|--------------------|
|[授權代碼流](v2-oauth2-auth-code-flow.md) | | x | x | x | x|  
|[隱含流程](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[混合 OIDC 流](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[刷新權杖兌換](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | 刷新權杖 | x | x | x| |
|[代理者流程](v2-oauth2-on-behalf-of-flow.md) | 存取權杖| x| x| x| |
|[裝置程式碼流程](v2-oauth2-device-code.md) | | x| x| x| |
|[用戶端認證](v2-oauth2-client-creds-grant-flow.md) | | | x（僅限應用）| | |
 
通過隱式模式發出的權杖由於通過 URL（或`response_mode` `query` `fragment`） 傳回瀏覽器，因此存在長度限制。  某些瀏覽器對 URL 的大小有限制，可以放入瀏覽器欄中，如果時間過長，則失敗。  因此，這些權杖沒有`groups`或`wids`聲明。

## <a name="interactive"></a>互動式

MSAL 支援以對話模式提示使用者其憑據登錄，以及使用這些憑據獲取權杖的能力。

![互動式流程圖](media/msal-authentication-flows/interactive.png)

有關使用MSAL.NET在特定平臺上以對話模式獲取權杖的詳細資訊，請參閱：
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [通用 Windows 平台](msal-net-uwp-considerations.md)

有關 MSAL.js 中的互動式調用的詳細資訊，請參閱[MSAL.js 互動式請求中的"提示行為](msal-js-prompt-behavior.md)"。

## <a name="implicit-grant"></a>隱含授與

MSAL 支援[OAuth 2 隱式授予流](v2-oauth2-implicit-grant-flow.md)，它允許應用程式從 Microsoft 標識平臺獲取權杖，而無需執行後端伺服器憑據交換。 這允許應用登錄使用者、維護會話並將權杖獲取到其他 Web API，所有這些都位於用戶端 JavaScript 代碼中。

![隱式授予流圖](media/msal-authentication-flows/implicit-grant.svg)

許多現代 Web 應用程式都是構建為用戶端、單頁應用程式，使用 JavaScript 或 SPA 框架（如 Angular、Vue.js 和 React.js）編寫。 這些應用程式在 Web 瀏覽器中運行，並且具有不同于傳統伺服器端 Web 應用程式的身份驗證特徵。 Microsoft 標識平臺允許單頁應用程式使用隱式授予流登錄使用者，並獲得權杖以訪問後端服務或 Web API。 隱式流允許應用程式獲取 ID 權杖來表示經過身份驗證的使用者，還可以訪問調用受保護的 API 所需的權杖。

此身份驗證流不包括使用跨平臺 JavaScript 框架（如 Electron 和 React-本機）的應用程式方案，因為它們需要進一步的功能才能與本機平臺進行交互。

## <a name="authorization-code"></a>授權碼

MSAL 支援[OAuth 2 授權代碼授予](v2-oauth2-auth-code-flow.md)。 此授予可用於安裝在設備上的應用，以訪問受保護的資源，如 Web API。 這允許您向移動和桌面應用添加登錄和 API 存取權限。 

當使用者登錄到 Web 應用程式（網站）時，Web 應用程式將收到授權代碼。  將兌換授權代碼以獲取調用 Web API 的權杖。 在ASP.NET和ASP.NET核心 Web 應用中，`AcquireTokenByAuthorizationCode`唯一的目標是向權杖緩存添加權杖。 然後，應用程式可以使用權杖（通常在控制器中，它只需使用`AcquireTokenSilent`獲取 API 的權杖。

![授權代碼流圖](media/msal-authentication-flows/authorization-code.png)

在上圖中，應用程式：

1. 請求授權代碼，該代碼將兌換為訪問權杖。
2. 使用訪問權杖調用 Web API。

### <a name="considerations"></a>考量

- 您只能使用授權代碼一次來兌換權杖。 不要嘗試使用相同的授權代碼多次獲取權杖（協定標準規範明確禁止權杖）。 如果您有意多次贖回代碼，或者因為您不知道框架也為您創建代碼，則您會得到以下錯誤：`AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- 如果您正在編寫ASP.NET或ASP.NET酷睿應用程式，如果您未告訴框架您已兌換授權代碼，則可能會發生這種情況。 為此，您需要調用`context.HandleCodeRedemption()``AuthorizationCodeReceived`事件處理常式的方法。

- 避免與ASP.NET共用訪問權杖，這可能會阻止正確進行增量同意。 有關詳細資訊，請參閱[問題#693](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693)。

## <a name="on-behalf-of"></a>代表

MSAL 支援[OAuth 2 代表身份驗證流](v2-oauth2-on-behalf-of-flow.md)。  當應用程式調用服務或 Web API 時使用此流，而服務或 Web API 又需要調用另一個服務或 Web API。 其概念是透過要求鏈傳播委派的使用者身分識別和權限。 要使中介層服務向下游服務發出經過身份驗證的請求，它需要代表使用者從 Microsoft 標識平臺保護訪問權杖。

![代表流量的圖表](media/msal-authentication-flows/on-behalf-of.png)

在上圖中︰

1. 應用程式獲取 Web API 的訪問權杖。
2. 用戶端（Web、桌面、移動或單頁應用程式）調用受保護的 Web API，在 HTTP 要求的身份驗證標頭中添加訪問權杖作為承載權杖。 Web API 對使用者進行身份驗證。
3. 當用戶端調用 Web API 時，Web API 代表使用者請求另一個權杖。  
4. 受保護的 Web API 使用此權杖代表使用者調用下游 Web API。  Web API 以後還可以請求其他下游 API 的權杖（但仍代表同一使用者）。

## <a name="client-credentials"></a>用戶端認證

MSAL 支援[OAuth 2 用戶端憑據流](v2-oauth2-client-creds-grant-flow.md)。 此流允許您使用應用程式的標識訪問 Web 託管資源。 這類型的授與通常用於必須在背景中執行 (不需與使用者直接互動) 的伺服器對伺服器互動。 這些類型的應用程式通常稱為精靈或服務帳戶。 

用戶端憑據授予流允許 Web 服務（機密用戶端）使用自己的憑據，而不是類比使用者，在調用另一個 Web 服務時進行身份驗證。 在這種情況下，用戶端通常是中介層 Web 服務、守護進程服務或網站。 對於較高層級的保證，Microsoft 身分識別平台也可讓呼叫服務使用憑證 (而非共用密碼) 做為認證。

> [!NOTE]
> 機密用戶端流在移動平臺（UWP、Xamarin.iOS 和 Xamarin.Android）上不可用，因為這些僅支援公共用戶端應用程式。 公共用戶端應用程式不知道如何向標識提供程式證明應用程式的身份。 通過部署證書，可以在 Web 應用或 Web API 後端實現安全連線。

MSAL.NET支援兩種類型的用戶端憑據。 這些用戶端憑據需要向 Azure AD 註冊。 憑據將傳入到代碼中機密用戶端應用程式的建構函式。

### <a name="application-secrets"></a>應用程式祕密

![帶密碼的機密用戶端圖](media/msal-authentication-flows/confidential-client-password.png)

在上圖中，應用程式：

1. 使用應用程式金鑰或密碼憑據獲取權杖。
2. 使用權杖發出資源的請求。

### <a name="certificates"></a>憑證

![帶證書的機密用戶端圖](media/msal-authentication-flows/confidential-client-certificate.png)

在上圖中，應用程式：

1. 使用證書憑據獲取權杖。
2. 使用權杖發出資源的請求。

這些用戶端憑據需要：
- 註冊為 Azure AD。
- 在代碼中構建機密用戶端應用程式時傳入。

## <a name="device-code"></a>設備代碼

MSAL 支援[OAuth 2 設備代碼流](v2-oauth2-device-code.md)，允許使用者登錄到受輸入約束的設備，如智慧電視、IoT 設備或印表機。 使用 Azure AD 進行互動式身份驗證需要 Web 瀏覽器。 設備代碼流允許使用者使用另一台設備（例如，另一台電腦或行動電話）以對話模式登錄，而設備或作業系統不提供 Web 瀏覽器。

通過使用設備代碼流，應用程式通過專為這些設備或作業系統設計的兩步過程獲取權杖。 此類應用程式的示例包括在 IoT 設備或命令列工具 （CLI） 上運行的應用程式。 

![設備代碼流圖](media/msal-authentication-flows/device-code.png)

在上圖中︰

1. 每當需要使用者身份驗證時，應用都會提供一個代碼，並要求使用者使用另一台設備（如聯網的智慧手機）轉到 URL（例如。 `https://microsoft.com/devicelogin` 然後，系統會提示使用者輸入代碼，然後通過正常的身份驗證體驗繼續，包括同意提示和在必要時的多重要素驗證。

2. 身份驗證成功後，命令列應用通過後通道接收所需的權杖，並使用它們執行所需的 Web API 呼叫。

### <a name="constraints"></a>條件約束

- 設備代碼流僅在公共用戶端應用程式上可用。
- 構造公共用戶端應用程式時傳入的許可權必須是以下之一：
  - 已租戶（其中`https://login.microsoftonline.com/{tenant}/``{tenant}`表示租戶 ID 的 GUID 或與租戶關聯的域） 的表單中）。
  - 對於任何工作和學校帳戶 （`https://login.microsoftonline.com/organizations/`）.
- Azure AD v2.0 終結點尚未支援 Microsoft 個人帳戶（不能使用 或`/common``/consumers`租戶）。

## <a name="integrated-windows-authentication"></a>整合式 Windows 驗證

MSAL 支援集成 Windows 身份驗證 （IWA），適用于在加入域或 Azure AD 加入 Windows 電腦上運行的桌面或移動應用程式。 使用 IWA，這些應用程式可以靜默地獲取權杖（無需使用者進行任何 UI 交互）。 

![集成 Windows 身份驗證圖](media/msal-authentication-flows/integrated-windows-authentication.png)

在上圖中，應用程式：

1. 使用集成 Windows 身份驗證獲取權杖。
2. 使用權杖發出資源的請求。

### <a name="constraints"></a>條件約束

IWA 僅支援聯合使用者，這意味著在活動目錄中創建並由 Azure AD 支援的使用者。 直接在 Azure AD 中創建的使用者，如果沒有活動目錄支援（託管使用者），則不能使用此身份驗證流。 此限制不會影響[使用者名/密碼流](#usernamepassword)。

IWA 適用于為 .NET 框架、.NET 核心和通用 Windows 平臺編寫的應用。

IWA 不會繞過多重要素驗證。 如果配置了多重要素驗證，則如果需要多重要素驗證質詢，IWA 可能會失敗。 多重要素驗證需要使用者交互。

您不控制標識提供程式何時請求執行雙重身份驗證。 租戶管理員會這樣做。 通常，當您從其他國家/地區登錄時，當您沒有通過 VPN 連接到公司網路時，有時甚至通過 VPN 進行連接時，也需要雙重身份驗證。 Azure AD 使用 AI 持續瞭解是否需要雙重身份驗證。 如果 IWA 失敗，應回退回 [互動式使用者提示] （#interactive）。

構造公共用戶端應用程式時傳入的許可權必須是以下之一：
- 已租戶（表示`https://login.microsoftonline.com/{tenant}/``tenant`租戶 ID 的 guid 或與租戶關聯的域的表單）。
- 對於任何工作和學校帳戶 （`https://login.microsoftonline.com/organizations/`）. 不支援 Microsoft 個人帳戶（您無法使用`/common`或`/consumers`租戶）。

由於 IWA 是一個靜默流，因此以下必須為 true：
- 應用程式的使用者必須事先同意使用該應用程式。 
- 租戶管理員必須事先同意租戶中的所有使用者使用該應用程式。

這意味著以下原因之一是正確的：
- 作為開發人員，您自己在 Azure 門戶上選擇了 **"授予**"。
- 租戶管理員在應用程式的註冊**API 許可權**選項卡中選擇 **[租戶域]授予/撤銷管理員同意**（請參閱[添加訪問 Web API 的許可權](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)）。
- 您為使用者提供了一種同意應用程式的方法（請參閱[請求個人使用者同意](v2-permissions-and-consent.md#requesting-individual-user-consent)）。
- 您為租戶管理員提供了一種同意應用程式的方法（請參閱[管理員同意](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)）。

為 .NET 桌面、.NET 核心和 Windows 通用平臺應用啟用 IWA 流。 在 .NET Core 上，您必須向 IWA 提供使用者名，因為 .NET Core 無法從作業系統獲取使用者名。
  
有關同意的詳細資訊，請參閱[v2.0 許可權和同意](v2-permissions-and-consent.md)。

## <a name="usernamepassword"></a>使用者名稱/密碼

MSAL 支援[OAuth 2 資源擁有者密碼憑據授予](v2-oauth-ropc.md)，這允許應用程式通過直接處理使用者的密碼來登錄使用者。 在桌面應用程式中，您可以使用使用者名/密碼流靜默獲取權杖。 使用應用程式時不需要 UI。

![使用者名/密碼流圖](media/msal-authentication-flows/username-password.png)

在上圖中，應用程式：

1. 通過向標識提供程式發送使用者名和密碼來獲取權杖。
2. 使用權杖調用 Web API。

> [!WARNING]
> 不建議使用此流。 它需要高度的信任和使用者曝光。  僅當無法使用其他更安全的流時，才應使用此流。 有關詳細資訊，請參閱密碼[日益嚴重的解決方案是什麼？](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/) 

在 Windows 域聯接的電腦上靜默獲取權杖的首選流是集成[Windows 身份驗證](#integrated-windows-authentication)。 否則，您還可以使用[設備代碼流](#device-code)。

儘管在某些情況下（DevOps 方案）這非常有用，但如果希望在提供自己的 UI 的互動式方案中使用使用者名/密碼，請嘗試避免這樣做。 使用使用者名/密碼：
- 需要執行多重要素驗證的使用者將無法登錄（因為沒有交互）。
- 使用者將無法執行單一登入。

### <a name="constraints"></a>條件約束

除了集成[Windows 身份驗證約束](#integrated-windows-authentication)外，以下約束還適用：

- 使用者名/密碼流與條件訪問和多重要素驗證不相容。 因此，如果應用在租戶管理員需要多重要素驗證的 Azure AD 租戶中運行，則不能使用此流。 許多組織都這樣做。
- 它僅適用于工作帳戶和學校帳戶（不適用於 Microsoft 帳戶）。
- 該流在 .NET 桌面和 .NET Core 上可用，但在通用 Windows 平臺上不可用。

### <a name="azure-ad-b2c-specifics"></a>Azure AD B2C 細節

有關使用MSAL.NET和 Azure AD B2C 的詳細資訊，請參閱[將 ROPC 與 Azure AD B2C （MSAL.NET） 一起使用](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc-with-azure-ad-b2c)。
