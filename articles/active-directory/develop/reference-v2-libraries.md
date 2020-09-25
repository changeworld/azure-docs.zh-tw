---
title: Microsoft 身分識別平臺驗證程式庫
description: 相容的用戶端程式庫和伺服器中介軟體程式庫，以及適用于 Microsoft 身分識別平臺端點的相關連結庫、來源和範例連結。
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 07/25/2019
ms.author: negoe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 072407ae64a8dc6083ffdca23ba9c758b2b35868
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91257804"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Microsoft 身分識別平臺驗證程式庫

[Microsoft 身分識別平臺端點](../azuread-dev/azure-ad-endpoint-comparison.md)支援業界標準 OAuth 2.0 和 OpenID Connect 1.0 通訊協定。 Microsoft 驗證程式庫 (MSAL) 是設計來與 Microsoft 身分識別平臺端點搭配使用。 您也可以使用支援 OAuth 2.0 和 OpenID Connect 1.0 的開放原始碼程式庫。

我們建議您使用通訊協定網域專家所撰寫的程式庫，這些專家遵循安全性開發生命週期 (SDL) 方法。 這類方法包含 [Microsoft 遵循的][Microsoft-SDL]方法。 如果您為通訊協定編寫程式碼，則應遵循 Microsoft SDL 這類方法。 請密切注意每個通訊協定的標準規格中的安全性考慮。

> [!NOTE]
> 您正在尋找 (ADAL) 的 Azure Active Directory Authentication Library 嗎？ 查看 ADAL 連結 [庫指南](../azuread-dev/active-directory-authentication-libraries.md)。

## <a name="types-of-libraries"></a>程式庫類型

Microsoft 身分識別平臺端點適用于兩種類型的程式庫：

* **用戶端程式庫**：原生用戶端和伺服器會使用用戶端程式庫來取得存取權杖，以呼叫資源（例如 Microsoft Graph）。
* **伺服器中介軟體程式庫**：Web 應用程式會使用伺服器中介軟體程式庫進行使用者登入。 Web API 會使用伺服器中介軟體程式庫來驗證原生用戶端或其他伺服器所傳送的權杖。

## <a name="library-support"></a>程式庫支援

程式庫的支援類型有兩種︰

* **Microsoft 支援**：Microsoft 可提供這些程式庫的修正程式，並已完成這些程式庫的 SDL 審查評鑑。
* **相容**： Microsoft 已在基本案例中測試這些程式庫，並確認它們可使用 Microsoft 身分識別平臺端點。 Microsoft 不會提供這些程式庫的修正程式，也不會對這些程式庫進行評論。 問題和功能要求應導向到程式庫的開放原始碼專案。

如需使用 Microsoft 身分識別平臺端點的程式庫清單，請參閱下列各節。

## <a name="microsoft-supported-client-libraries"></a>Microsoft 支援的用戶端程式庫

使用用戶端驗證程式庫來取得權杖，以呼叫受保護的 web API。

| 平台 | 程式庫 | 下載 | 原始程式碼 | 範例 | 參考 | 概念文件 | 藍圖 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ![JavaScript](media/sample-v2-code/logo_js.png) | MSAL.js  | [NPM](https://www.npmjs.com/package/msal) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/README.md) |  [單一頁面應用程式](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) | [參考](https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/) | [概念檔](msal-overview.md)| [藍圖](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
![Angular](media/sample-v2-code/logo_angular.png) | MSAL 角度 | [NPM](https://www.npmjs.com/package/@azure/msal-angular) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | [角 SPA](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) | [參考](https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-angular/) | [概念檔](msal-overview.md) | [藍圖](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
| ![.NET Framework](media/sample-v2-code/logo_NET.png) ![UWP](media/sample-v2-code/logo_windows.png) ![Xamarin](media/sample-v2-code/logo_xamarin.png) | MSAL.NET  |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [傳統型應用程式](/windows/apps/desktop/) | [MSAL.NET](/dotnet/api/microsoft.identity.client?view=azure-dotnet-preview) |[概念檔](msal-overview.md) | [藍圖](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap)
| ![Python](media/sample-v2-code/logo_python.png) | MSAL Python | [PyPI](https://pypi.org/project/msal) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [範例](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) | [ReadTheDocs](https://msal-python.rtfd.io/) | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | [藍圖](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Roadmap)
| ![Java](media/sample-v2-code/logo_java.png) | MSAL Java | [Maven](https://search.maven.org/artifact/com.microsoft.azure/msal4j) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-java) | [範例](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) | [參考](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html) | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | [藍圖](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki)
| iOS 和 macOS | MSAL iOS 和 macOS | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [iOS 應用程式](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc)， [macOS 應用程式](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) | [參考](https://azuread.github.io/microsoft-authentication-library-for-objc/index.html)  | [概念檔](msal-overview.md) | |
|![Android/Java](media/sample-v2-code/logo_Android.png) | MSAL Android | [中央儲存機制](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Android App](quickstart-v2-android.md) | [JavaDocs](https://javadoc.io/doc/com.microsoft.identity.client/msal) | [概念檔](msal-overview.md) |[藍圖](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Roadmap)

## <a name="microsoft-supported-server-middleware-libraries"></a>Microsoft 支援的伺服器中介軟體程式庫

使用中介軟體程式庫來協助保護 web 應用程式和 web Api。 使用 ASP.NET 或 ASP.NET Core 所撰寫的 web 應用程式或 web Api 會使用中介軟體程式庫。

| 平台 | 程式庫 | 下載 | 原始程式碼 | 範例 | 參考
| --- | --- | --- | --- | --- | --- |
| ![.NET](media/sample-v2-code/logo_NET.png) ![.NET Core](media/sample-v2-code/logo_NETcore.png) | ASP.NET 安全性 |[NuGet](https://www.nuget.org/packages/Microsoft.AspNet.Mvc/) |[GitHub](https://github.com/aspnet/AspNetCore) |[MVC 應用程式](quickstart-v2-aspnet-webapp.md) |[ASP.NET API 參考](/dotnet/api/?view=aspnetcore-2.0) |
| ![.NET](media/sample-v2-code/logo_NET.png)| 適用於 .NET 的身分識別模型延伸模組| |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | [MVC 應用程式](quickstart-v2-aspnet-webapp.md) |[參考](/dotnet/api/overview/azure/activedirectory/client?view=azure-dotnet) |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | Azure AD Passport |[NPM](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Web 應用程式](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs) | |

## <a name="microsoft-supported-libraries-by-os--language"></a>Microsoft 支援的程式庫（依 OS/語言）

就支援的作業系統和語言而言，對應如下：

| 平台    | Windows    | Linux      | macOS      | iOS | Android    |
|-------------|------------|------------|------------|------------|------------|
| ![JavaScript](media/sample-v2-code/logo_js.png)  |  MSAL.js | MSAL.js | MSAL.js | MSAL.js |  MSAL.js |
| <img alt="C#" src="../../cognitive-services/speech-service/media/index/logo_csharp.svg" width="64px" height="64px" /> | ASP.NET、ASP.NET Core、MSAL.Net ( .NET FW、Core、UWP) | ASP.NET Core，MSAL.Net ( .NET Core)  | ASP.NET Core，MSAL.Net (macOS)        | MSAL.Net () 的 iOS | MSAL.Net (Xamarin) |
| Swift <br> Objective-C |            |            | [適用於 iOS 和 macOS 的 MSAL](msal-overview.md) | [適用於 iOS 和 macOS 的 MSAL](msal-overview.md) |            |
| ![Java](media/sample-v2-code/logo_java.png) Java | msal4j | msal4j | msal4j | | MSAL Android |
| ![Python](media/sample-v2-code/logo_python.png) Python | MSAL Python | MSAL Python | MSAL Python |
| ![Node.Js](media/sample-v2-code/logo_nodejs.png) Node.JS | Passport. node | Passport. node | Passport. node |

另請參閱 [支援的平臺和語言案例](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)

## <a name="compatible-client-libraries"></a>相容的用戶端程式庫

| 平台 | 程式庫名稱 | 測試的版本 | 原始程式碼 | 範例 |
|:---:|:---:|:---:|:---:|:---:|
|![JavaScript](media/sample-v2-code/logo_js.png)|[Hello.js](https://adodson.com/hello.js/) | 版本1.13。5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
|![Vue](media/sample-v2-code/logo_vue.png)|[Vue MSAL](https://github.com/mvertopoulos/vue-msal) | 版本3.0。3 |[vue-msal](https://github.com/mvertopoulos/vue-msal) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Scribe Java](https://github.com/scribejava/scribejava) | [3.2.0 版](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Gluu OpenID Connect 程式庫](https://github.com/GluuFederation/oxAuth) | [版本3.0。2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | [Gluu OpenID Connect 程式庫](https://github.com/GluuFederation/oxAuth) | |
| ![Python](media/sample-v2-code/logo_python.png) | [要求-OAuthlib](https://github.com/requests/requests-oauthlib) | [版本 1.2.0](https://github.com/requests/requests-oauthlib/releases/tag/v1.2.0) | [要求-OAuthlib](https://github.com/requests/requests-oauthlib) | |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | [openid-用戶端](https://github.com/panva/node-openid-client) | [版本2.4。5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | [openid-用戶端](https://github.com/panva/node-openid-client) | |
| ![PHP](media/sample-v2-code/logo_php.png) | [PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [1.4.2 版](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/) | |
| ![Ruby](media/sample-v2-code/logo_ruby.png) |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth：1.3。1<br />omniauth-oauth2：1.4。0 |[OmniAuth](https://github.com/omniauth/omniauth)<br />[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |
| iOS、macOS、& Android  | [React Native 應用程式驗證](https://github.com/FormidableLabs/react-native-app-auth) | [4.2.0 版](https://github.com/FormidableLabs/react-native-app-auth/releases/tag/v4.2.0) | [React Native 應用程式驗證](https://github.com/FormidableLabs/react-native-app-auth) | |

針對任何符合標準的程式庫，您可以使用 Microsoft 身分識別平臺端點。 請務必瞭解支援的位置：

* 如需程式庫程式碼中的問題和新功能要求，請連絡程式庫擁有者。
* 如需服務端通訊協定實作中的問題和新功能要求，請連絡 Microsoft。
* 針對您想要在通訊協定中看到的其他功能[提出功能要求](https://feedback.azure.com/forums/169401-azure-active-directory)。
* 如果您發現 Microsoft 身分識別平臺端點不符合 OAuth 2.0 或 OpenID Connect 1.0 的問題，請[建立支援要求](../../azure-portal/supportability/how-to-create-azure-support-request.md)。

## <a name="related-content"></a>相關內容

如需 Microsoft 身分識別平臺端點的詳細資訊，請參閱 [microsoft 身分識別平臺總覽][AAD-App-Model-V2-Overview]。

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[ClientLib-NET-Lib]: https://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: ./tutorial-v2-windows-desktop.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:/
[ClientLib-Iosmac-Lib]:/
[ClientLib-Iosmac-Repo]:/
[ClientLib-Iosmac-Sample]:/
[ClientLib-Android-Lib]:/
[ClientLib-Android-Repo]:/
[ClientLib-Android-Sample]:/
[ClientLib-Js-Lib]:/
[ClientLib-Js-Repo]:/
[ClientLib-Js-Sample]:/

[Microsoft-SDL]: https://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: ./tutorial-v2-asp-webapp.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-node-web/
