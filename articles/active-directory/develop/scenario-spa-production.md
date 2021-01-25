---
title: 將單一頁面應用程式移至生產環境
titleSuffix: Microsoft identity platform
description: '瞭解如何建立單一頁面應用程式 (移至生產環境) '
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 0c1e9e6da02478f5e4703676b74fc8247a4f619b
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98753531"
---
# <a name="single-page-application-move-to-production"></a>單一頁面應用程式：移至生產環境

既然您已瞭解如何取得權杖來呼叫 web Api，請瞭解如何移至生產環境。

## <a name="improve-your-app"></a>改善您的應用程式

[啟用記錄](msal-logging.md) ，讓您的應用程式生產環境就緒。

## <a name="test-your-integration"></a>測試您的整合

遵循 [Microsoft 身分識別平台整合檢查清單](identity-platform-integration-checklist.md)來測試您的整合。

## <a name="deploy-your-app"></a>部署您的應用程式

查看 [部署範例](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter3) ，以瞭解如何分別使用 Azure 儲存體和 Azure App 服務來部署 SPA 和 Web API 專案。 

## <a name="next-steps"></a>後續步驟

- 快速入門範例的深入探討，其中說明如何使用 **MSAL.js**： [JavaScript SPA 教學](./tutorial-v2-javascript-spa.md)課程來登入使用者，以及取得存取權杖以呼叫 **Microsoft Graph API** 的程式碼。

- 示範如何使用 **MSAL.js**： [SPA 與 ASP.NET 後端](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi)，為您自己的後端 web API 取得權杖 (ASP.NET Core) 的範例。

- 示範如何使用 **passport-azure-ad**： [Node.js web api (Azure AD](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2)來驗證後端 Web api ( # A0) 的存取權杖的範例。

- 此範例示範如何使用 **MSAL.js** ，在以 **Azure Active Directory B2C** (Azure AD B2C) ： [SPA 與 Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)註冊的應用程式中登入使用者。

- 此範例會示範如何使用 **passport-azure-ad** 來驗證向 **Azure Active Directory B2C** (Azure AD B2C) ： [Node.js Web API (](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)Azure AD B2C) 中註冊之應用程式的存取權杖。
