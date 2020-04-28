---
title: 遷移至 Microsoft 驗證程式庫（MSAL）
titleSuffix: Microsoft identity platform
description: 瞭解 Microsoft 驗證程式庫（MSAL）與 Azure AD 驗證程式庫（ADAL）之間的差異，以及如何遷移至 MSAL。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "78164928"
---
# <a name="migrate-applications-to-microsoft-authentication-library-msal"></a>將應用程式遷移至 Microsoft 驗證程式庫（MSAL）

Microsoft 驗證程式庫（MSAL）和 Azure AD 驗證程式庫（ADAL）都是用來驗證 Azure AD 實體，並從 Azure AD 要求權杖。 到目前為止，大多數開發人員都已使用 Azure AD 驗證程式庫 (ADAL) 要求權杖，進而使用開發人員適用的 Azure AD 平台 (v1.0) 驗證 Azure AD 身分識別 (公司和學校帳戶)。 使用 MSAL：

- 您可以使用 Microsoft 身分識別平臺端點，驗證更廣泛的 Microsoft 身分識別集（Azure AD 身分識別和 Microsoft 帳戶，以及透過 Azure AD B2C 的社交和本機帳戶）。
- 您的使用者將獲得最佳的單一登入體驗。
- 您的應用程式可以啟用累加式同意，而且支援條件式存取比較簡單。
- 您可以從創新中獲益。

**MSAL 現在是建議用於 Microsoft 身分識別平臺的驗證程式庫**。 ADAL 不會執行任何新功能。 致力於改善 MSAL。

下列文章說明 MSAL 和 ADAL 程式庫之間的差異，並協助您遷移至 MSAL：
- [遷移至 MSAL.NET](msal-net-migration.md)
- [遷移至 MSAL.js](msal-compare-msal-js-and-adal-js.md)
- [遷移到 MSAL.Android](migrate-android-adal-msal.md)
- [遷移到 MSAL.iOS/macOS](migrate-objc-adal-msal.md)
- [遷移到 MSAL Python](migrate-python-adal-msal.md)
- [遷移到適用於 Java 的 MSAL](migrate-adal-msal-java.md)
- [使用訊息代理程式將 Xamarin 應用程式遷移到 MSAL.NET](msal-net-migration-ios-broker.md)
