---
title: 遷移到 Microsoft 身份驗證庫 （MSAL）
titleSuffix: Microsoft identity platform
description: 瞭解 Microsoft 身份驗證庫 （MSAL） 和 Azure AD 身份驗證庫 （ADAL） 之間的差異以及如何遷移到 MSAL。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/27/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 3af18eb09fd9906a0caaebda0b786795400467f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164928"
---
# <a name="migrate-applications-to-microsoft-authentication-library-msal"></a>將應用程式遷移到 Microsoft 身份驗證庫 （MSAL）

Microsoft 身份驗證庫 （MSAL） 和 Azure AD 身份驗證庫 （ADAL） 都用於對 Azure AD 實體進行身份驗證，並從 Azure AD 請求權杖。 到目前為止，大多數開發人員都已使用 Azure AD 驗證程式庫 (ADAL) 要求權杖，進而使用開發人員適用的 Azure AD 平台 (v1.0) 驗證 Azure AD 身分識別 (公司和學校帳戶)。 使用 MSAL：

- 在使用 Microsoft 標識平臺終結點時，可以通過 Azure AD B2C 對更廣泛的 Microsoft 標識集（Azure AD 標識和 Microsoft 帳戶以及社交和本地帳戶）進行身份驗證。
- 您的使用者將獲得最佳的單點登錄體驗。
- 您的應用程式可以啟用增量同意，並且支援條件訪問更容易。
- 您從創新中獲益。

**MSAL現在是推薦的auth庫與微軟身份平臺一起使用**。 ADAL 上不會實現任何新功能。 這些努力的重點是改進MSAL。

以下文章描述了 MSAL 和 ADAL 庫之間的差異，並説明您遷移到 MSAL：
- [遷移至 MSAL.NET](msal-net-migration.md)
- [遷移至 MSAL.js](msal-compare-msal-js-and-adal-js.md)
- [遷移到 MSAL.Android](migrate-android-adal-msal.md)
- [遷移到 MSAL.iOS / macOS](migrate-objc-adal-msal.md)
- [遷移到 MSAL Python](migrate-python-adal-msal.md)
- [遷移到適用於 Java 的 MSAL](migrate-adal-msal-java.md)
- [使用訊息代理程式將 Xamarin 應用程式遷移到 MSAL.NET](msal-net-migration-ios-broker.md)
