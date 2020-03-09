---
title: Azure Active Directory v1.0 的程式碼範例 |Microsoft Docs
description: 提供 Azure Active Directory (v1.0 端點) 程式碼範例的索引 (依案例組織)。
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.subservice: azuread-dev
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 9b4479436077dbad8cf2fb4dc40954194c138981
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78377692"
---
# <a name="azure-active-directory-code-samples-v10-endpoint"></a>Azure Active Directory 程式碼範例 (v1.0 端點)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

您可以使用 Microsoft Azure Active Directory (Azure AD)，將驗證和授權加入 Web 應用程式及 Web API。

本節提供可讓您深入了解 Azure AD v1.0 端點的範例連結。 這些範例會說明操作方式，並提供可在應用程式中使用的程式碼片段。 在程式碼範例頁面上，您可以找到詳細的讀我主題，其有助您了解需求並進行安裝與設定。 而程式碼加上的註解可協助您了解重要區段。

> [!NOTE]
> 如果您想要了解 Azure AD V2 程式碼範例，請參閱[依據案例的 v2.0 程式碼範例](../develop/sample-v2-code.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)。

若要了解每個範例類型的基本案例，請參閱 [Azure AD 的驗證案例](v1-authentication-scenarios.md)。

您也可以參與我們在 GitHub 上的範例。 若要了解做法，請參閱 [Microsoft Azure Active Directory 範例與文件](https://github.com/Azure-Samples?page=3&query=active-directory)。

## <a name="single-page-applications"></a>單頁應用程式

這個範例示範如何撰寫使用 Azure AD 保護的單頁應用程式。

 平台 | 呼叫它自己的 API | 呼叫其他 Web API
 -------- |  --------------------- | ------------------ 
![此圖顯示 JavaScript 標誌](media/sample-v2-code/logo-js.png) | [javascript-singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
![此圖顯示角度 JS 標誌](media/sample-v2-code/logo-angular.png) | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | [angularjs-singlepageapp-cors](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi)

## <a name="web-applications"></a>Web 應用程式

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Web 應用程式以使用者身分識別登入使用者、呼叫 Microsoft Graph 或 Web API 的 Web 應用程式

下列範例會說明簽署使用者的 Web 應用程式。 在這些應用程式之中，有些也會代表已登入的使用者呼叫 Microsoft Graph 或您自己的 Web API。

 平台 | 僅登入使用者 | 呼叫 Microsoft Graph | 呼叫其他 ASP.NET 或 ASP.NET Core 2.0 Web API
 -------- | ------------------- | --------------------- | -------------------------
![此圖顯示 ASP.NET 標誌](media/sample-v2-code/logo-netcore.png)</p>ASP.NET Core 2.0 | [dotnet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webapp-webapi-multitenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) </p>(AAD Graph) | [dotnet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
![此圖顯示 ASP.NET 標誌](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 |  </p> [webapp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) </p> [dotnet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | [dotnet-webapp-multitenant-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect)</p> (AAD Graph) |
![此圖顯示 Python 標誌](media/sample-v2-code/logo-python.png) | | [python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
![此圖顯示 JAVA 記錄檔](media/sample-v2-code/logo-java.png)  | | [java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
![此圖顯示 PHP 標誌](media/sample-v2-code/logo-php.png) | | [php-graphapi-web](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>示範角色型存取控制 (授權) 的 Web 應用程式

下列範例會示範如何實作角色型存取控制 (RBAC)。 RBAC 可用來限制只能將 Web 應用程式特定功能的權限，提供給特定使用者。 系統會根據使用者屬於 **Azure AD 群組**還是具有指定應用程式**角色**來對使用者授權。

平台 | 範例 |
 -------- | ------------------- |
![此圖顯示 ASP.NET 標誌](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | [dotnet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) </p>  [dotnet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | 使用 Azure AD **角色**進行授權的 .NET 4.5 MVC Web 應用程式

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>呼叫 Microsoft Graph 或 Web API 的桌上型與行動裝置公開用戶端應用程式

下列範例說明以使用者名稱存取 Microsoft Graph 或 Web API 的公用用戶端應用程式（deskto/pmobile 應用程式）。 視裝置及平台而定，應用程式可以使用不同的方式 (流程/授與) 將使用者登入：

- 以互動方式
- 無訊息模式（在 Windows 上使用整合式 Windows 驗證，或使用者名稱/密碼）
- 將互動式登入委派給另一個裝置（裝置上使用的裝置程式碼流程不提供 web 控制項）

用戶端應用程式 | 平台 | 流程/授與 | 呼叫 Microsoft Graph | 呼叫 ASP.NET 或 ASP.NET Core 2.x Web API
------------------ | -------- | ---------- | -------------------- | -------------------------
桌上型 (WPF)           | ![此圖顯示 .NET/C#標誌](media/sample-v2-code/logo-net.png)  | 互動式 | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) 的一部分 | [Dotnet-native-desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [dotnet-native-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore/)</p> [dotnet-webapi-manual-jwt-validation](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
行動裝置 (UWP)            | 。![此圖顯示 .NET/C#/UWP](media/sample-v2-code/logo-windows.png)   | 互動式 | [dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) </p> 這個範例會使用 [WAM](/windows/uwp/security/web-account-manager)，而非 [ADAL.NET](https://aka.ms/adalnet)|  [dotnet-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (使用 ADAL.NET 呼叫單一租用戶 Web API 的 UWP 應用程式) </p> [dotnet-webapi-multitenant-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (使用 ADAL.NET 呼叫多租用戶 Web API 的 UWP 應用程式)|
行動裝置 (Android、iOS、UWP)   | ![此圖顯示 .NET/C# （Xamarin）](media/sample-v2-code/logo-xamarin.png) | 互動式 | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
行動裝置 (Android)           | ![此圖顯示 Android 標誌](media/sample-v2-code/logo-android.png) | 互動式 |   [android](https://github.com/Azure-Samples/active-directory-android) |
行動裝置 (iOS)           | ![此圖顯示 iOS/目標 C 或 Swift](media/sample-v2-code/logo-ios.png) | 互動式 |   [nativeClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
桌上型 (主控台)          | ![此圖顯示 .NET/C#標誌](media/sample-v2-code/logo-net.png) | 使用者名稱/密碼 </p>  整合式 Windows 驗證 | | [dotnet-native-headless](https://github.com/azure-samples/active-directory-dotnet-native-headless)
桌上型 (主控台)          | ![此圖顯示 JAVA 標誌](media/sample-v2-code/logo-java.png) | 使用者名稱/密碼 | | [java-native-headless](https://github.com/Azure-Samples/active-directory-java-native-headless)
桌上型 (主控台)           | ![此圖顯示 .NET Core/C#標誌](media/sample-v2-code/logo-netcore.png) | 裝置程式碼流程 | | [dotnet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Daemon 應用程式（以應用程式的身分識別存取 web Api）

下列範例的桌上型或 Web 應用程式會以應用程式識別碼 (無使用者) 來存取 Microsoft Graph 或 Web API。

用戶端應用程式 | 平台 | 流程/授與 | 呼叫 ASP.NET 或 ASP.NET Core 2.0 Web API
------------------ | -------- | ---------- | -------------------- 
精靈應用程式 (主控台)          | ![此圖顯示 .NET 標誌](media/sample-v2-code/logo-netframework.png) | 使用應用程式祕密或憑證的用戶端認證 | [dotnet-daemon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [dotnet-daemon-certificate-credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
精靈應用程式 (主控台)         | ![此圖顯示 .NET 標誌](media/sample-v2-code/logo-netcore.png) | 使用憑證的用戶端認證| [dotnetcore-daemon-certificate-credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
ASP.NET Web 應用程式  | ![此圖顯示 .NET 標誌](media/sample-v2-code/logo-netframework.png) | 用戶端認證 | [dotnet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>Web API

### <a name="web-api-protected-by-azure-active-directory"></a>受 Azure Active Directory 保護的 Web API

下列範例示範如何透過 Azure AD 保護 node.js web API。

在本文前面幾節，您也可以找到其他範例說明會**呼叫** ASP.NET 或 ASP.NET Core **Web API** 的用戶端應用程式。 本節不會再次提及這些範例，但是您會在上方或下方資料表的最後一個資料行中發現它們

| 平台 | 範例 |
|--------|-------------------|
| ![此圖顯示 node.js 標誌](media/sample-v2-code/logo-nodejs.png)  | [node-webapi](https://github.com/Azure-Samples/active-directory-node-webapi) |

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>呼叫 Microsoft Graph 或其他 Web API 的 Web API

下列範例顯示呼叫其他 Web API 的 Web API。 第二個範例顯示如何處理條件式存取。

| 平台 |  呼叫 Microsoft Graph | 呼叫其他 ASP.NET 或 ASP.NET Core 2.0 Web API |
| -------- |  --------------------- | ------------------------- |
| ![此圖顯示 ASP.NET 標誌](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |

## <a name="other-microsoft-graph-samples"></a>其他 Microsoft Graph 範例

如需示範 Microsoft Graph API 不同使用模式的範例和教學課程 (包括使用 Azure AD 驗證)，請參閱 [Microsoft Graph 社群範例和教學課程](https://github.com/microsoftgraph/msgraph-community-samples)。

## <a name="see-also"></a>另請參閱

- [Azure Active Directory 開發人員指南](v1-overview.md)
- [Azure Active Directory 驗證程式庫](active-directory-authentication-libraries.md)
- [Microsoft Graph API 概念和參考](https://docs.microsoft.com/graph/use-the-api)
