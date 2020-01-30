---
title: 取得權杖並呼叫 Microsoft Graph (.NET Core 主控台) (v1.0) | Azure
description: 建置與 Azure AD 整合的 .NET 精靈應用程式，並使用 OAuth 2.0 呼叫受 Azure AD 保護的 API
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: jmprieur
ms.reviewer: ryanwi
ms.custom: aaddev
ms.openlocfilehash: bdcf0632110df23cbe91040f41535f51fb06ba44
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76703791"
---
# <a name="quickstart-acquire-token-and-call-microsoft-graph-using-console-apps-identity-v10"></a>快速入門：取得權杖並使用主控台應用程式的身分識別 (v1.0) 呼叫 Microsoft Graph

[Microsoft 身分識別平台](v2-overview.md)是 Azure Active Directory (Azure AD) 開發人員平台的演化。 它可讓開發人員建置應用程式以登入所有 Microsoft 身分識別，並取得權杖以呼叫 Microsoft Graph 等 Microsoft API，或開發人員所建置的 API。

[Microsoft 驗證程式庫 (MSAL)](msal-overview.md) 可讓開發人員從 Microsoft 身分識別平台端點取得權杖，以存取受保護的 Web API。 Active Directory 驗證程式庫 (ADAL) 可與適用於開發人員的 Azure AD (v1.0) 端點整合；此時 MSAL 會與 Microsoft 身分識別平台 (v2.0) 端點整合。

## <a name="next-steps"></a>後續步驟

對於新的 .NET 精靈應用程式，建議您使用 Microsoft 身分識別平台 (v2.0) 和 MSAL 來取得權杖及存取安全的 Web API：[快速入門：使用應用程式的身分識別來取得權杖並從主控台應用程式呼叫 Microsoft Graph API](quickstart-v2-netcore-daemon.md)。
