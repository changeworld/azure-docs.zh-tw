---
title: 深入瞭解 MSAL |蔚藍
titleSuffix: Microsoft identity platform
description: Microsoft 驗證程式庫 (MSAL) 可讓應用程式開發人員取得權杖，以便呼叫受保護的 web Api。 這些 web Api 可以是 Microsoft Graph、其他 Microsoft Api、協力廠商 web Api 或您自己的 web API。 MSAL 支援多種應用程式架構和平台。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 8fbcacf4e3c26f117be4ab5b64b35f670435e6b6
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92092744"
---
# <a name="overview-of-microsoft-authentication-library-msal"></a>Microsoft 驗證程式庫 (MSAL) 概觀
Microsoft 驗證程式庫 (MSAL) 可讓開發人員從 Microsoft 身分識別平臺端點取得 [權杖](developer-glossary.md#security-token) ，以便驗證使用者並存取安全的 web api。 您可以使用它來提供 Microsoft Graph、其他 Microsoft Api、協力廠商 web Api 或您自己的 web API 的安全存取。 MSAL 支援許多不同的應用程式架構和平臺，包括 .NET、JavaScript、JAVA、Python、Android 和 iOS。

MSAL 可讓您透過多種平台通用的 API，以不同的方式取得權杖。 使用 MSAL 有下列優點：

* 無須根據您應用程式中的通訊協定直接使用 OAuth 程式庫或程式碼。
* 可代表使用者或代表應用程式 (適用於平台時) 取得權杖。
* 可維護權杖快取，並且在權杖即將到期時為您重新整理權杖。 您不需要自行處理權杖到期的問題。
* 可協助您指定您要讓應用程式登入的標的 (您的組織、數個組織、工作、學校和 Microsoft 個人帳戶、Azure AD B2C 的社交身分識別、主權雲端和國家雲端中的使用者)。
* 可協助您從組態檔設定應用程式。
* 可協助您藉由公開可操作的例外狀況、記錄和遙測，對您的應用程式進行疑難排解。

## <a name="application-types-and-scenarios"></a>應用程式類型和案例
使用 MSAL 時，您可以從多個應用程式類型取得權杖：Web 應用程式、Web API、單頁應用程式 (JavaScript)、行動和原生應用程式，以及精靈和伺服器端應用程式。

MSAL 可用於許多應用程式案例，包括：

* [ (JavaScript) 的單一頁面應用程式 ](scenario-spa-overview.md)
* [登入使用者的 Web 應用程式](scenario-web-app-sign-user-overview.md)
* [登入使用者並代表使用者呼叫 Web API 的 Web 應用程式](scenario-web-app-call-api-overview.md)
* [保護 Web API，使其僅可供已驗證的使用者存取](scenario-protected-web-api-overview.md)
* [代表登入的使用者呼叫另一個下游 Web API 的 Web API](scenario-web-api-call-api-overview.md)
* [代表已登入使用者呼叫 web API 的桌面應用程式](scenario-desktop-overview.md)
* [代表以互動方式登入之使用者呼叫 WEB API 的行動應用程式](scenario-mobile-overview.md)。
* [代表本身呼叫 Web API 的桌面/服務精靈應用程式](scenario-daemon-overview.md)

## <a name="languages-and-frameworks"></a>語言和架構

| 程式庫 | 支援的平台和架構|
| --- | --- |
| [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)| .NET Framework、.NET Core、Xamarin Android、Xamarin iOS、通用 Windows 平台|
| [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)| JavaScript/TypeScript 架構，例如 AngularJS、Ember.js、Durandal.js|
| [適用於 Android 的 MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-android)|Android|
| [適用於 iOS 和 macOS 的 MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|iOS 和 macOS|
| [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java)|Windows、macOS、Linux|
| [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)|Windows、macOS、Linux|

## <a name="differences-between-adal-and-msal"></a>ADAL 與 MSAL 的差異

Active Directory 驗證程式庫 (ADAL) 可與適用於開發人員的 Azure AD (v1.0) 端點整合；此時 MSAL 會與 Microsoft 身分識別平台 (v2.0) 端點整合。 V1.0 端點支援工作帳戶，但不支援個人帳戶。 v2.0 端點可將 Microsoft 個人帳戶和工作帳戶統一成單一驗證系統。 此外，您也可以透過 MSAL 取得 Azure AD B2C 的驗證。

如需更具體的資訊，請參閱[從 ADAL.NET 移轉至 MSAL.NET](msal-net-migration.md) 和[從 ADAL.js 移轉至 MSAL.js](msal-compare-msal-js-and-adal-js.md)。
