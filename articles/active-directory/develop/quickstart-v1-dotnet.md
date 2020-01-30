---
title: 從 .NET 桌面 (WPF) 讓使用者登入並呼叫 Microsoft Graph API | Azure
description: 深入了解如何建置 .NET Windows 桌面應用程式來與 Azure AD 整合進行登入，並使用 OAuth 2.0 呼叫受 Azure AD 保護的 API。
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: ed33574f-6fa3-402c-b030-fae76fba84e1
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: c35e44dff321c4a2f568855dacd827001a57f64a
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76703808"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-a-net-desktop-wpf-app"></a>快速入門：從 .NET 桌面 (WPF) 應用程式登入使用者並呼叫 Microsoft Graph API

[Microsoft 身分識別平台](v2-overview.md)是 Azure Active Directory (Azure AD) 開發人員平台的演化。 它可讓開發人員建置應用程式以登入所有 Microsoft 身分識別，並取得權杖以呼叫 Microsoft Graph 等 Microsoft API，或開發人員所建置的 API。

[Microsoft 驗證程式庫 (MSAL)](msal-overview.md) 可讓開發人員從 Microsoft 身分識別平台端點取得權杖，以存取受保護的 Web API。 Active Directory 驗證程式庫 (ADAL) 可與適用於開發人員的 Azure AD (v1.0) 端點整合；此時 MSAL 會與 Microsoft 身分識別平台 (v2.0) 端點整合。

對於新的桌面應用程式，建議您使用 Microsoft 身分識別平台 (v2.0) 和 MSAL 來取得權杖及存取安全的 Web API：[快速入門：取得權杖，並從 Windows 桌面應用程式呼叫 Microsoft Graph API](quickstart-v2-windows-desktop.md)
