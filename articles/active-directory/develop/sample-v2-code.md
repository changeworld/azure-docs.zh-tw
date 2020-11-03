---
title: Microsoft 身分識別平台的程式碼範例
description: 提供可用的 Microsoft 身分識別平台 (v2.0 端點) 程式碼範例的索引 (依案例分類)。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: sample
ms.workload: identity
ms.date: 10/21/2020
ms.author: marsma
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 49bba8054065123d7409f26b61d1dfc2580ef720
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636046"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Microsoft 身分識別平台程式碼範例 (v2.0 端點)

您可以使用 Microsoft 身分識別平台：

- 將驗證和授權新增至您的 Web 應用程式與 Web API。
- 要求存取權杖以存取受保護的 Web API。

本文簡短描述 Microsoft 身分識別平台端點，並提供範例連結。 這些範例會說明操作方式，並提供可在應用程式中使用的程式碼片段。 在程式碼範例頁面上，您可以找到詳細的讀我主題，協助您了解需求並進行安裝及設定。 程式碼內有註解可幫助您了解重要區段。

若要了解每個範例類型的基本案例，請參閱 [Microsoft 身分識別平台端點的應用程式類型](v2-app-types.md)。

您也可以在 GitHub 上參與範例。 若要了解做法，請參閱 [Microsoft Azure Active Directory 範例與文件](https://github.com/Azure-Samples?page=3&query=active-directory)。

## <a name="single-page-applications"></a>單頁應用程式

這些範例示範如何撰寫以 Microsoft 身分識別平台保護的單頁應用程式。 這些範例會使用其中一種 MSAL.js。

| 平台 | 描述 | 連結 |
| -------- | --------------------- | -------- |
| ![此圖顯示 JavaScript 標誌](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | SPA 呼叫 Microsoft Graph |[javascript-graphapi-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) (英文) |
| ![此圖顯示 JavaScript 標誌](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | SPA 使用 Auth Code Flow 搭配 PKCE 呼叫 Microsoft Graph |[javascript-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2) |
| ![此圖顯示 JavaScript 標誌](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | SPA 呼叫 B2C |[b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![此圖顯示 JavaScript 標誌](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | SPA 使用驗證碼流程與 PKCE 來呼叫 B2C |[b2c-javascript-spa](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa) |
| ![此圖顯示 Angular 標誌](media/sample-v2-code/logo_angular.png) [Angular (MSAL-Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| SPA 呼叫 Microsoft Graph  | [active-directory-javascript-singlepageapp-angular](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) |
| ![此圖顯示 Angular 標誌](media/sample-v2-code/logo_angular.png) [Angular (MSAL-Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| SPA 呼叫自訂 Web API | [ms-identity-javascript-angular-spa-aspnetcore-webapi](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi) |
| ![此圖顯示 Angular 標誌](media/sample-v2-code/logo_angular.png) [Angular (MSAL-Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | SPA 呼叫 B2C |[active-directory-b2c-javascript-angular-spa](https://github.com/Azure-Samples/active-directory-b2c-javascript-angular-spa) |
| ![此圖顯示 React 標誌](media/sample-v2-code/logo_react.png) [React (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core)| SPA 呼叫自訂 Web API，然後再呼叫 Microsoft Graph  | [ms-identity-javascript-react-spa-dotnetcore-webapi-obo](https://github.com/Azure-Samples/ms-identity-javascript-react-spa-dotnetcore-webapi-obo) |
| ![此圖顯示 JavaScript 標誌](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | SPA 呼叫自訂 Web API，然後再呼叫 Microsoft Graph  | [ms-identity-javascript-tutorial-chapter4-obo](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/4-AdvancedGrants/4-1-call-api-graph) |
| ![此圖顯示 Angular 標誌](media/sample-v2-code/logo_angular.png) [Angular (MSAL-Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | SPA 呼叫具有應用程式角色和安全性群組的自訂 Web API |[ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups) |

## <a name="web-applications"></a>Web 應用程式

下列範例會說明登入使用者的 Web 應用程式。 部分範例也會示範呼叫 Microsoft Graph 或使用使用者身分識別呼叫您自有 Web API 的應用程式。

| 平台 | 僅登入使用者 | 登入使用者與呼叫 Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![此圖顯示 ASP.NET Core 標誌](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [ASP.NET Core WebApp 登入使用者教學課程](https://aka.ms/aspnetcore-webapp-sign-in) | [ASP.NET Core Web 應用程式呼叫 Microsoft Graph](https://aka.ms/aspnetcore-webapp-call-msgraph) 階段中的相同範例 |
| ![此圖顯示 ASP.NET 標誌](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [ASP.NET 快速入門](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2) (英文)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) (英文) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![此圖顯示 Java 標誌](media/sample-v2-code/logo_java.png)  |                   | [ms-identity-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![此圖顯示 Python 標誌](media/sample-v2-code/logo_python.png)  | [ms-identity-python-flask-webapp-authentication](https://github.com/Azure-Samples/ms-identity-python-flask-webapp-authentication) | [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![此圖顯示 Ruby 標誌](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>桌上型與行動裝置公開用戶端應用程式

下列範例的公開用戶端應用程式 (桌上型或行動裝置應用程式) 會以使用者名稱存取 Microsoft Graph API 或您自己的 Web API。 除了 *桌上型 (主控台) 搭配 WAM* 範例以外，這些用戶端應用程式全都會使用 Microsoft 驗證程式庫 (MSAL)。

| 用戶端應用程式 | 平台 | 流程/授與 | 呼叫 Microsoft Graph | 呼叫 ASP.NET Core Web API |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| 桌上型 (WPF)      | ![此圖顯示 .NET/C# 標誌](media/sample-v2-code/logo_NET.png) | [授權碼](msal-authentication-flows.md#authorization-code)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) (英文) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) (英文) |
| 桌上型 (主控台)   | ![此圖顯示 .NET/C# (桌上型) 標誌](media/sample-v2-code/logo_NET.png) | [整合式 Windows 驗證](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| 桌上型 (主控台)   | ![此圖顯示 Java 標誌](media/sample-v2-code/logo_java.png) | [整合式 Windows 驗證](msal-authentication-flows.md#integrated-windows-authentication) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| 桌上型 (主控台)   | ![此圖顯示 .NET/C# (桌上型) 標誌](media/sample-v2-code/logo_NETcore.png) | [使用者名稱/密碼](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| 桌上型 (主控台) 搭配 WAM  | ![此圖顯示 .NET/C# (桌上型) 標誌](media/sample-v2-code/logo_NETcore.png) | 與 [Web 帳戶管理員](/windows/uwp/security/web-account-manager) (WAM) 互動 |[dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  |
| 桌上型 (主控台)   | ![此圖顯示 Java 標誌](media/sample-v2-code/logo_java.png) | [使用者名稱/密碼](msal-authentication-flows.md#usernamepassword) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| 桌上型 (主控台)   | ![此圖顯示 Python 標誌](media/sample-v2-code/logo_python.png) | [使用者名稱/密碼](msal-authentication-flows.md#usernamepassword) |[ms-identity-python-desktop](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
| 行動裝置 (Android、iOS、UWP)   | ![此圖顯示 .NET/C# (Xamarin) 標誌](media/sample-v2-code/logo_xamarin.png) | [授權碼](msal-authentication-flows.md#authorization-code) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) (英文) |  |
| 行動裝置 (iOS)       | ![此圖顯示 iOS/Objective-C 或 Swift](media/sample-v2-code/logo_iOS.png) | [授權碼](msal-authentication-flows.md#authorization-code) |[ios-swift-objc-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) (英文) |  |
| 桌上型 (macOS)       | macOS | [授權碼](msal-authentication-flows.md#authorization-code) |[macOS-swift-objc-native-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| 行動裝置 (Android-Java)   | ![此圖顯示 Android 標誌](media/sample-v2-code/logo_Android.png) | [授權碼](msal-authentication-flows.md#authorization-code) |  [android-Java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| 行動裝置 (Android-Kotlin)   | ![此圖顯示 Android 標誌](media/sample-v2-code/logo_Android.png) | [授權碼](msal-authentication-flows.md#authorization-code) |  [android-Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>精靈應用程式

下列範例說明如何使用其自有的身分識別 (無使用者) 存取 Microsoft Graph API 的應用程式。

| 用戶端應用程式 | 平台 | 流程/授與 | 呼叫 Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| 主控台 | ![此圖顯示 .NET Core 標誌](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [用戶端認證](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Web 應用程式 | ![此圖顯示 ASP.NET 標誌](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [用戶端認證](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) (英文) |
| 主控台 | ![此圖顯示 Java 標誌](media/sample-v2-code/logo_java.png) | [用戶端認證](msal-authentication-flows.md#client-credentials) | [ms-identity-java-daemon](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| 主控台 | ![此圖顯示 Python 標誌](media/sample-v2-code/logo_python.png) | [用戶端認證](msal-authentication-flows.md#client-credentials) | [ms-identity-python-daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>無周邊應用程式

下列範例示範在裝置 (沒有網頁瀏覽器) 上執行的公開用戶端應用程式。 此應用程式可以是命令列工具，或是在 Linux 或 Mac 上執行的應用程式，或者是 IoT 應用程式。 範例應用程式會以使用者 (可以互動式方式登入另一部裝置，例如行動電話) 的名稱存取 Microsoft Graph API。 此用戶端應用程式會使用 MicroSoft 驗證程式庫 (MSAL)。

| 用戶端應用程式 | 平台 | 流程/授與 | 呼叫 Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| 桌上型 (主控台)   | ![此圖顯示 .NET/C# (桌上型) 標誌](media/sample-v2-code/logo_NETcore.png) | [裝置碼流程](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| 桌上型 (主控台)   | ![此圖顯示 Java 標誌](media/sample-v2-code/logo_java.png) | [裝置碼流程](msal-authentication-flows.md#device-code) |[ms-identity-java-devicecodeflow](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| 桌上型 (主控台)   | ![此圖顯示 Python 標誌](media/sample-v2-code/logo_python.png) | [裝置碼流程](msal-authentication-flows.md#device-code) |[ms-identity-python-devicecodeflow](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="multi-tenant-saas-applications"></a>多租用戶 SaaS 應用程式

下列範例說明如何設定您的應用程式，以接受來自任何 Azure Active Directory (Azure AD) 租用戶的登入。 將應用程式設定為 *多租用戶* 表示您可以提供 **軟體即服務** (SaaS) 應用程式給許多組織，讓其使用者能夠在同意使用其帳戶之後，登入您的應用程式。

| 平台 | 描述 | 連結 |
| -------- | --------------------- | -------- |
| ![此圖顯示 JavaScript 標誌](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | 多租用戶 SPA 呼叫圖形 API |[ms-identity-javascript-angular-spa-aspnet-webapi-multitenant](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter1) |
| ![此圖顯示 Angular 標誌](media/sample-v2-code/logo_angular.png) [Angular (MSAL-Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | 多租用戶 SPA 呼叫多租用戶自訂 Web API |[ms-identity-javascript-angular-spa-aspnet-webapi-multitenant](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter2) |
| ![此圖顯示 ASP.NET Core 標誌](media/sample-v2-code/logo_NETcore.png) [.NET Core (MSAL.NET)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | ASP.NET Core MVC Web 應用程式呼叫 Graph API |[active-directory-aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-3-Multi-Tenant) |
| ![此圖顯示 ASP.NET Core 標誌](media/sample-v2-code/logo_NETcore.png) [.NET Core (MSAL.NET)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | ASP.NET Core MVC Web 應用程式呼叫 ASP.NET Core Web API |[active-directory-aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-3-AnyOrg) |

## <a name="web-apis"></a>Web API

下列範例顯示如何使用 Microsoft 身分識別平台端點保護 Web API，以及如何從 Web API 呼叫下游的 API。

| 平台 | 範例 |
| -------- | ------------------- |
| ![此圖顯示 ASP.NET Core 標誌](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph) 的 ASP.NET Core Web API (服務)  |
| ![此圖顯示 ASP.NET 標誌](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | [ms-identity-aspnet-webapi-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) 的 Web API (服務) |
| ![此圖顯示 Java 標誌](media/sample-v2-code/logo_java.png) | [ms-identity-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi) 的 Web API (服務) |
| ![此圖顯示 Node.js 標誌](media/sample-v2-code/logo_nodejs.png) | [active-directory-javascript-nodejs-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2) 的 Web API (服務) |
| ![此圖顯示 Node.js 標誌](media/sample-v2-code/logo_nodejs.png) | [active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) 的 B2C Web API (服務) |

## <a name="azure-functions-as-web-apis"></a>Azure Functions 即 Web API

下列範例顯示如何使用 HttpTrigger 保護 Azure 函式，並使用 Microsoft 身分識別平台端點公開 Web API，以及如何從 Web API 呼叫下游的 API。

| 平台 | 範例 |
| -------- | ------------------- |
| ![此圖顯示 ASP.NET Core 標誌](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [dotnet-native-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions) 的 Azure 函式 ASP.NET Core Web API (服務)  |
| ![此圖顯示 Python 標誌](media/sample-v2-code/logo_python.png)</p>Python | [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) 的 Web API (服務) |
| ![此圖顯示 Node.js 標誌](media/sample-v2-code/logo_nodejs.png)</p>Node.js | [Node.js 和 passport-azure-ad](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) 的 Web API (服務) |
| ![此圖顯示 Node.js 標誌](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | [NodeJS 和代替下列項目使用 passport-azure-ad](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) 的 Web API (服務) |

## <a name="other-microsoft-graph-samples"></a>其他 Microsoft Graph 範例

若要了解示範 Microsoft Graph API 不同使用模式 (包括使用 Azure AD 驗證) 的[範例](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) (英文) 和教學課程，請參閱 [Microsoft Graph 社群範例和教學課程](https://github.com/microsoftgraph/msgraph-community-samples) (英文).

## <a name="see-also"></a>另請參閱

[Microsoft Graph API 的概念和參考](/graph/use-the-api?context=graph%2fapi%2fbeta&view=graph-rest-beta)
