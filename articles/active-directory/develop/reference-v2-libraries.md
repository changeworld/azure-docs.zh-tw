---
title: 微軟身份平臺身份驗證庫 |微軟文檔
description: 用於 Microsoft 標識平臺終結點的相容用戶端庫和伺服器中介軟體庫以及相關庫、源和示例連結。
services: active-directory
documentationcenter: ''
author: negoe
manager: CelesteDG
editor: ''
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/25/2019
ms.author: negoe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 9d2df175fa9d1ed33eb17ae85e01a4fd7a24e728
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77611933"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>微軟身份平臺身份驗證庫

[Microsoft 標識平臺終結點](active-directory-v2-compare.md)支援行業標準 OAuth 2.0 和 OpenID Connect 1.0 協定。 微軟身份驗證庫 （MSAL） 旨在與 Microsoft 標識平臺終結點配合使用。 您還可以使用支援 OAuth 2.0 和 OpenID Connect 1.0 的開源庫。

我們建議您使用遵循安全開發生命週期 （SDL） 方法的協定域專家編寫的庫。 這些方法包括[微軟遵循的方法][Microsoft-SDL]。 如果為協定提供代碼，則應遵循 Microsoft SDL 等方法。 密切關注每個協定的標準規範中的安全注意事項。

> [!NOTE]
> 您是否正在尋找 Azure 活動目錄身份驗證庫 （ADAL）？ 查看[ADAL 圖書館指南](../azuread-dev/active-directory-authentication-libraries.md)。

## <a name="types-of-libraries"></a>程式庫類型

Microsoft 標識平臺終結點適用于兩種類型的庫：

* **用戶端庫**：本機用戶端和伺服器使用用戶端庫來獲取用於調用資源（如 Microsoft Graph）的訪問權杖。
* **伺服器中介軟體程式庫**：Web 應用程式會使用伺服器中介軟體程式庫進行使用者登入。 Web API 會使用伺服器中介軟體程式庫來驗證原生用戶端或其他伺服器所傳送的權杖。

## <a name="library-support"></a>程式庫支援

程式庫的支援類型有兩種︰

* **Microsoft 支援**：Microsoft 可提供這些程式庫的修正程式，並已完成這些程式庫的 SDL 審查評鑑。
* **相容**：微軟已在基本方案中測試了這些庫，並確認它們與 Microsoft 標識平臺終結點一起工作。 Microsoft 不為這些庫提供修補程式，也沒有對這些庫進行審核。 問題和功能要求應重新導向至程式庫的開放原始碼專案。

有關使用 Microsoft 標識平臺終結點的庫清單，請參閱以下部分。

## <a name="microsoft-supported-client-libraries"></a>Microsoft 支援的用戶端程式庫

使用用戶端身份驗證庫獲取用於調用受保護 Web API 的權杖。

| Platform | 程式庫 | 下載 | 原始程式碼 | 範例 | 參考資料 | 概念文件 | 藍圖 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ![JavaScript](media/sample-v2-code/logo_js.png) | MSAL.js  | [NPM](https://www.npmjs.com/package/msal) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/README.md) |  [單一頁面應用程式](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) | [參考](https://azuread.github.io/microsoft-authentication-library-for-js/docs/msal/) | [概念文檔](msal-overview.md)| [藍圖](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
|![Angular JS](media/sample-v2-code/logo_angular.png) | MSAL Angular JS | [NPM](https://www.npmjs.com/package/@azure/msal-angularjs) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md) |  |  | |
![Angular](media/sample-v2-code/logo_angular.png) | MSAL 角（預覽） | [NPM](https://www.npmjs.com/package/@azure/msal-angular) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | | | |
| ![.NET Framework](media/sample-v2-code/logo_NET.png) ![UWP](media/sample-v2-code/logo_windows.png) ![Xamarin](media/sample-v2-code/logo_xamarin.png) | MSAL.NET  |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [傳統型應用程式](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) | [MSAL.NET](https://docs.microsoft.com/dotnet/api/microsoft.identity.client?view=azure-dotnet-preview) |[概念文檔](msal-overview.md) | [藍圖](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap)
| ![Python](media/sample-v2-code/logo_python.png) | MSAL Python | [PyPI](https://pypi.org/project/msal) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [樣品](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) | [閱讀文檔](https://msal-python.rtfd.io/) | [維琪](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | [藍圖](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Roadmap)
| ![Java](media/sample-v2-code/logo_java.png) | MSAL Java | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-java) | [樣品](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) | [參考](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html) | [維琪](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | [藍圖](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki)
| iOS & macOS | MSAL iOS 和 macOS | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [iOS 應用程式](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc)， [macOS 應用程式](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) | [參考](https://azuread.github.io/microsoft-authentication-library-for-objc/index.html)  | [概念文檔](msal-overview.md) | |
|![Android/Java](media/sample-v2-code/logo_Android.png) | MSAL 安卓 | [中央存儲庫](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Android App](quickstart-v2-android.md) | [JavaDocs](https://javadoc.io/doc/com.microsoft.identity.client/msal) | [概念文檔](msal-overview.md) |[藍圖](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Roadmap)

## <a name="microsoft-supported-server-middleware-libraries"></a>Microsoft 支援的伺服器中介軟體程式庫

使用中介軟體庫説明保護 Web 應用程式和 Web API。 使用ASP.NET或 ASP.NET核心編寫的 Web 應用或 Web API 使用中介軟體庫。

| Platform | 程式庫 | 下載 | 原始程式碼 | 範例 | 參考資料
| --- | --- | --- | --- | --- | --- |
| ![.NET](media/sample-v2-code/logo_NET.png) ![.NET Core](media/sample-v2-code/logo_NETcore.png) | ASP.NET 安全性 |[NuGet](https://www.nuget.org/packages/Microsoft.AspNet.Mvc/) |[GitHub](https://github.com/aspnet/AspNetCore) |[MVC應用程式](quickstart-v2-aspnet-webapp.md) |[ASP.NET API 參考](https://docs.microsoft.com/dotnet/api/?view=aspnetcore-2.0) |
| ![.NET](media/sample-v2-code/logo_NET.png)| 適用於 .NET 的身分識別模型延伸模組| |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | [MVC應用程式](quickstart-v2-aspnet-webapp.md) |[參考](https://docs.microsoft.com/dotnet/api/overview/azure/activedirectory/client?view=azure-dotnet) |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | Azure AD Passport |[NPM](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Web 應用程式](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs) | |

## <a name="microsoft-supported-libraries-by-os--language"></a>微軟支援的作業系統/語言庫

在支援的作業系統與語言方面，映射如下：

|             | Windows    | Linux      | macOS      | iOS | Android    |
|-------------|------------|------------|------------|------------|------------|
| ![JavaScript](media/sample-v2-code/logo_js.png)  |  MSAL.js | MSAL.js | MSAL.js | MSAL.js |  MSAL.js |
| <img alt="C#" src="../../cognitive-services/speech-service/media/index/logo_csharp.svg" width="64px" height="64px" /> | ASP.NET，ASP.NET核心，MSAL.Net（.NET FW，核心，UWP）| ASP.NET核心，MSAL.Net（.NET 核心） | ASP.NET核心，MSAL.Net （MacOS）       | MSAL.Net（Xamarin.iOS） | MSAL.Net（Xamarin.安卓）|
| Swift <br> Objective-C |            |            | [適用於 iOS 和 macOS 的 MSAL](msal-overview.md) | [適用於 iOS 和 macOS 的 MSAL](msal-overview.md) |            |
| ![Java](media/sample-v2-code/logo_java.png) Java | msal4j | msal4j | msal4j | | MSAL 安卓 |
| ![Python](media/sample-v2-code/logo_python.png) Python | MSAL Python | MSAL Python | MSAL Python |
| ![節點.Js](media/sample-v2-code/logo_nodejs.png) Node.JS | 護照.節點 | 護照.節點 | 護照.節點 |

另請參閱[受支援的平臺和語言的方案](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)

## <a name="compatible-client-libraries"></a>相容的用戶端程式庫

| Platform | 程式庫名稱 | 測試的版本 | 原始程式碼 | 範例 |
|:---:|:---:|:---:|:---:|:---:|
|![JavaScript](media/sample-v2-code/logo_js.png)|[Hello.js](https://adodson.com/hello.js/) | 版本 1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
| ![Java](media/sample-v2-code/logo_java.png) | [Scribe Java](https://github.com/scribejava/scribejava) | [3.2.0 版](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| ![Java](media/sample-v2-code/logo_java.png) | [格魯開放ID連接庫](https://github.com/GluuFederation/oxAuth) | [版本 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | [格魯開放ID連接庫](https://github.com/GluuFederation/oxAuth) | |
| ![Python](media/sample-v2-code/logo_python.png) | [請求-奧阿特利布](https://github.com/requests/requests-oauthlib) | [版本 1.2.0](https://github.com/requests/requests-oauthlib/releases/tag/v1.2.0) | [請求-奧阿特利布](https://github.com/requests/requests-oauthlib) | |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | [openid 用戶端](https://github.com/panva/node-openid-client) | [版本 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | [openid 用戶端](https://github.com/panva/node-openid-client) | |
| ![PHP](media/sample-v2-code/logo_php.png) | [PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [1.4.2 版](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/) | |
| ![Ruby](media/sample-v2-code/logo_ruby.png) |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth： 1.3.1<br />omniauth-auth2： 1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)<br />[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |
| iOS， macOS， &安卓  | [回應本機應用程式 Auth](https://github.com/FormidableLabs/react-native-app-auth) | [4.2.0 版](https://github.com/FormidableLabs/react-native-app-auth/releases/tag/v4.2.0) | [回應本機應用程式 Auth](https://github.com/FormidableLabs/react-native-app-auth) | |

對於任何符合標準的庫，可以使用 Microsoft 標識平臺終結點。 瞭解從何處尋求支援非常重要：

* 如需程式庫程式碼中的問題和新功能要求，請連絡程式庫擁有者。
* 如需服務端通訊協定實作中的問題和新功能要求，請連絡 Microsoft。
* 為要在協定中看到的其他功能[提交功能請求](https://feedback.azure.com/forums/169401-azure-active-directory)。
* 如果發現 Microsoft 標識平臺終結點不符合 OAuth 2.0 或 OpenID Connect 1.0 的問題，[請創建支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。

## <a name="related-content"></a>相關內容

有關 Microsoft 標識平臺終結點的詳細資訊，請參閱[Microsoft 標識平臺概述][AAD-App-Model-V2-Overview]。

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[ClientLib-NET-Lib]: https://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
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
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
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
