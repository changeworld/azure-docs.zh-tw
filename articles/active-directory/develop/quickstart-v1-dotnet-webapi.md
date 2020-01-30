---
title: 建置 Azure AD .NET Web API 來進行驗證和授權 | Microsoft Docs
description: 如何建置與 Azure AD 整合來進行驗證和授權的 .NET MVC Web API。
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5b080ce3869bcdbbde9724a33433e5f0659b37e4
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76703825"
---
# <a name="quickstart-build-a-net-web-api-that-integrates-with-azure-ad-for-authentication-and-authorization"></a>快速入門：建置與 Azure AD 整合來進行驗證和授權的 .NET Web API

[Microsoft 身分識別平台](v2-overview.md)是 Azure Active Directory (Azure AD) 開發人員平台的演化。 它可讓開發人員建置應用程式以登入所有 Microsoft 身分識別，並取得權杖以呼叫 Microsoft Graph 等 Microsoft API，或開發人員所建置的 API。

[Microsoft 驗證程式庫 (MSAL)](msal-overview.md) 可讓開發人員從 Microsoft 身分識別平台端點取得權杖，以存取受保護的 Web API。 Active Directory 驗證程式庫 (ADAL) 可與適用於開發人員的 Azure AD (v1.0) 端點整合；此時 MSAL 會與 Microsoft 身分識別平台 (v2.0) 端點整合。

對於新的 Web API，建議您使用 Microsoft 身分識別平台 (v2.0) 和 MSAL 來取得權杖及存取安全的 Web API：[快速入門：將「使用 Microsoft 登入」新增至 ASP.NET Web 應用程式](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2#calling-an-aspnet-core-web-api-from-a-wpf-application-using-azure-ad-v2)
