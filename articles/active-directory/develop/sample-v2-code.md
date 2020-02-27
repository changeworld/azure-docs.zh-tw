---
title: Microsoft 身分識別平臺的程式碼範例 |Microsoft Docs
description: 提供可用的 Microsoft 身分識別平臺（v2.0 端點）程式碼範例的索引（依案例組織）。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: f24412aa7e53c52f0fcbf0c266b57db077d41eb1
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "77612038"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Microsoft 身分識別平臺程式碼範例（v2.0 端點）

您可以使用 Microsoft 身分識別平台：

- 將驗證和授權新增至您的 Web 應用程式與 Web API。
- 要求存取權杖以存取受保護的 Web API。

本文簡要說明並提供 Microsoft 身分識別平臺端點的範例連結。 這些範例會示範其完成方式，並提供可在應用程式中使用的程式碼片段。 在 [程式碼範例] 頁面上，您可以找到詳細的讀我檔案主題，協助您進行需求、安裝和設定。 程式碼中的批註可協助您瞭解重要區段。

> [!NOTE]
> 如果您對 v1.0 範例感興趣，請參閱[Azure AD 程式碼範例（v1.0 端點）](../azuread-dev/sample-v1-code.md)。

若要瞭解每個範例類型的基本案例，請參閱[Microsoft 身分識別平臺端點的應用程式類型](v2-app-types.md)。

您也可以在 GitHub 上參與範例。 若要了解做法，請參閱 [Microsoft Azure Active Directory 範例與文件](https://github.com/Azure-Samples?page=3&query=active-directory)。

## <a name="single-page-applications"></a>單頁應用程式

這些範例示範如何撰寫使用 Microsoft 身分識別平臺保護的單一頁面應用程式。 這些範例會使用 MSAL 的其中一種類別。

| 平台 | 描述 | 連結 |
| -------- | --------------------- | -------- |
| ![此影像顯示 JavaScript 標誌](media/sample-v2-code/logo_js.png) [javascript （msal）](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | 呼叫 Microsoft Graph |[javascript-graphapi-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) (英文) |
| ![此影像顯示 JavaScript 標誌](media/sample-v2-code/logo_js.png) [javascript （msal）](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | 呼叫 B2C |[b2c-javascript-msal-active-directory-javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![此影像顯示 JavaScript 標誌](media/sample-v2-code/logo_js.png) [javascript （msal）](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | 呼叫自己的 Web API |[javascript-active-directory-javascript-singlepageapp-dotnet-webapi-v2-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |
| ![此影像顯示](media/sample-v2-code/logo_angular.png) JavaScript 的角度 JS 標誌[（MSAL AngularJS）](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs)| 呼叫 Microsoft Graph  | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/MsalAngularjsDemoApp)
| ![此影像顯示](media/sample-v2-code/logo_angular.png) JavaScript 的角度標誌[（MSAL 角度）](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| 呼叫 Microsoft Graph  | [javascript-active-directory-javascript-singlepageapp-dotnet-webapi-v2-角度](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) |

## <a name="web-applications"></a>Web 應用程式

下列範例會說明登入使用者的 Web 應用程式。 部分範例也會示範呼叫 Microsoft Graph 或使用使用者身分識別呼叫您自有 Web API 的應用程式。

| 平台 | 僅登入使用者 | 登入使用者與呼叫 Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![此圖顯示 ASP.NET Core 標誌](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2。2 | [ASP.NET Core WebApp 登入使用者教學課程](https://aka.ms/aspnetcore-webapp-sign-in) | [ASP.NET Core Web 應用程式](https://aka.ms/aspnetcore-webapp-call-msgraph)中的相同範例會呼叫 Microsoft Graph 階段 |
| ![此圖顯示 ASP.NET 標誌](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [ASP.NET 快速入門](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2) (英文)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) (英文) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![此圖顯示 JAVA 標誌](media/sample-v2-code/logo_java.png)  |                   | [ms-身分識別-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![此圖顯示 Python 標誌](media/sample-v2-code/logo_python.png)  |                   | [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![此圖顯示 node.js 標誌](media/sample-v2-code/logo_nodejs.png)  |                   | [Node.js 快速入門](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs) |
| ![此圖顯示 Ruby 標誌](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>桌上型與行動裝置公開用戶端應用程式

下列範例顯示存取 Microsoft Graph API 的公用用戶端應用程式（桌面或行動應用程式），或您自己 Web API 的使用者名稱。 所有這些用戶端應用程式都使用 Microsoft 驗證程式庫（MSAL）。

| 用戶端應用程式 | 平台 | 流程/授與 | 呼叫 Microsoft Graph | 呼叫 ASP.NET Core 2.0 Web API |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| 桌上型 (WPF)      | ![此圖顯示 .NET/C#標誌](media/sample-v2-code/logo_NET.png) | [互動](msal-authentication-flows.md#interactive)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) (英文) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) (英文) |
| 桌上型 (主控台)   | ![此圖顯示 .NET/C# （桌面）標誌](media/sample-v2-code/logo_NET.png) | [整合式 Windows 驗證](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| 桌上型 (主控台)   | ![此圖顯示 JAVA 標誌](media/sample-v2-code/logo_java.png) | [整合式 Windows 驗證](msal-authentication-flows.md#integrated-windows-authentication) |[ms-身分識別-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| 桌上型 (主控台)   | ![此圖顯示 .NET/C# （桌面）標誌](media/sample-v2-code/logo_NETcore.png) | [使用者名稱/密碼](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| 具有 WAM 的桌面（主控台）  | ![此圖顯示 .NET/C# （桌面）標誌](media/sample-v2-code/logo_NETcore.png) | [與 WAM 互動](msal-authentication-flows.md#interactive) |[dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  |
| 桌上型 (主控台)   | ![此圖顯示 JAVA 標誌](media/sample-v2-code/logo_java.png) | [使用者名稱/密碼](msal-authentication-flows.md#usernamepassword) |[ms-身分識別-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| 桌上型 (主控台)   | ![此圖顯示 Python 標誌](media/sample-v2-code/logo_python.png) | [使用者名稱/密碼](msal-authentication-flows.md#usernamepassword) |[毫秒-身分識別-python-桌面](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
| 行動裝置 (Android、iOS、UWP)   | ![此圖顯示 .NET/C# （Xamarin）標誌](media/sample-v2-code/logo_xamarin.png) | [互動](msal-authentication-flows.md#interactive) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) (英文) |  |
| 行動裝置 (iOS)       | ![此圖顯示 iOS/目標-C 或 Swift](media/sample-v2-code/logo_iOS.png) | [互動](msal-authentication-flows.md#interactive) |[ios-swift-objc-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) (英文) |  |
| Desktop （macOS）       | macOS | [互動](msal-authentication-flows.md#interactive) |[macOS-swift-objc-native-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| Mobile （Android-JAVA）   | ![此圖顯示 Android 標誌](media/sample-v2-code/logo_Android.png) | [互動](msal-authentication-flows.md#interactive) |  [android-JAVA](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| 行動裝置（Android-Kotlin）   | ![此圖顯示 Android 標誌](media/sample-v2-code/logo_Android.png) | [互動](msal-authentication-flows.md#interactive) |  [android-Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>精靈應用程式

下列範例說明如何使用其自有的身分識別 (無使用者) 存取 Microsoft Graph API 的應用程式。

| 用戶端應用程式 | 平台 | 流程/授與 | 呼叫 Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| 主控台 | ![此圖顯示 .NET Core 標誌](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [用戶端認證](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Web 應用程式 | ![此圖顯示 ASP.NET 標誌](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [用戶端認證](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) (英文) |
| 主控台 | ![此圖顯示 JAVA 標誌](media/sample-v2-code/logo_java.png) | [用戶端認證](msal-authentication-flows.md#client-credentials) | [ms-身分識別-java-daemon](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| 主控台 | ![此圖顯示 Python 標誌](media/sample-v2-code/logo_python.png) | [用戶端認證](msal-authentication-flows.md#client-credentials) | [ms-identity-python-daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>無周邊應用程式

下列範例示範在裝置 (沒有網頁瀏覽器) 上執行的公開用戶端應用程式。 應用程式可以是命令列工具、在 Linux 或 Mac 上執行的應用程式，或 IoT 應用程式。 此範例會以在另一個裝置（例如行動電話）上以互動方式登入的使用者名稱，來提供應用程式存取 Microsoft Graph API 的功能。 此用戶端應用程式會使用 Microsoft 驗證程式庫（MSAL）。

| 用戶端應用程式 | 平台 | 流程/授與 | 呼叫 Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| 桌上型 (主控台)   | ![此圖顯示 .NET/C# （桌面）標誌](media/sample-v2-code/logo_NETcore.png) | [裝置程式碼流程](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| 桌上型 (主控台)   | ![此圖顯示 JAVA 標誌](media/sample-v2-code/logo_java.png) | [裝置程式碼流程](msal-authentication-flows.md#device-code) |[ms-身分識別-java-devicecodeflow](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| 桌上型 (主控台)   | ![此圖顯示 Python 標誌](media/sample-v2-code/logo_python.png) | [裝置程式碼流程](msal-authentication-flows.md#device-code) |[毫秒-身分識別-python-devicecodeflow](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="web-apis"></a>Web API

下列範例示範如何使用 Microsoft 身分識別平臺端點來保護 Web API，以及如何從 Web API 呼叫下游 API。

| 平台 | 範例 |
| -------- | ------------------- |
| ![此圖顯示 ASP.NET Core 標誌](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2。2 | Dotnet 的 ASP.NET Core Web API （服務） [-原生-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![此圖顯示 ASP.NET 標誌](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | [Ms-identity-webapi-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof)的 Web API （服務） |
| ![此圖顯示 JAVA 標誌](media/sample-v2-code/logo_java.png) | Ms-身分識別的 Web API （服務）- [java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi) |

## <a name="azure-functions-as-web-apis"></a>Azure Functions 作為 web Api

下列範例示範如何使用 HttpTrigger 來保護 Azure 函式，以及如何使用 Microsoft 身分識別平臺端點來公開 Web API，以及如何從 Web API 呼叫下游 API。

| 平台 | 範例 |
| -------- | ------------------- |
| ![此圖顯示 ASP.NET Core 標誌](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2。2 | ASP.NET Core Web API （服務） dotnet 的 Azure 函[式-原生-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)  |
| ![此圖顯示 node.js 標誌](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | NodeJS 和 passport 的 Web API （服務） [-azure-ad](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) |
| ![此圖顯示 Python 標誌](media/sample-v2-code/logo_python.png)</p>Python | [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions)的 Web API （服務） |
| ![此圖顯示 node.js 標誌](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | NodeJS 和 passport 的 Web API （服務） [-使用代表的 azure ad](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) |

## <a name="other-microsoft-graph-samples"></a>其他 Microsoft Graph 範例

若要了解示範 Microsoft Graph API 不同使用模式 (包括使用 Azure AD 驗證) 的[範例](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) (英文) 和教學課程，請參閱 [Microsoft Graph 社群範例和教學課程](https://github.com/microsoftgraph/msgraph-community-samples) (英文).

## <a name="see-also"></a>另請參閱

- [Azure Active Directory （v1.0）開發人員指南](../azuread-dev/v1-overview.md)
- [Azure AD 圖形 API 的概念和參考](https://msdn.microsoft.com/library/azure/hh974476.aspx)
- [Azure AD Graph API 協助程式程式庫](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
