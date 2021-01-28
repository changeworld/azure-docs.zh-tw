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
ms.openlocfilehash: 9656da8be086724482f129efab323e02b73e117e
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954950"
---
# <a name="single-page-application-move-to-production"></a>單一頁面應用程式：移至生產環境

現在您已瞭解如何取得權杖來呼叫 web Api，以下是將應用程式移至生產環境時要考慮的一些事項。

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="deploy-your-app"></a>部署您的應用程式

查看 [部署範例](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter3) ，以瞭解如何分別使用 Azure 儲存體和 Azure App 服務來部署 SPA 和 Web API 專案。 

## <a name="code-samples"></a>程式碼範例

這些程式碼範例示範單一頁面應用程式的數個重要作業。
- [SPA 與 ASP.NET 後端](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi)：如何使用 **MSAL.js** (ASP.NET Core) 取得您自己的後端 web API 權杖。

- [Node.js WEB api (Azure AD](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2)：如何使用 **passport-Azure-AD** 來驗證後端 Web api 的存取權杖 ( # A1) 。

- 使用 [Azure AD B2C 的 SPA](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)：如何使用 **MSAL.js** 在以 **Azure Active Directory B2C** (Azure AD B2C) 註冊的應用程式中登入使用者。

- [Node.js WEB API (Azure AD B2C)](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)：如何使用 **passport-Azure-AD** 來驗證以 **Azure Active Directory B2C** (Azure AD B2C) 註冊之應用程式的存取權杖。

## <a name="next-steps"></a>下一步

- [JAVASCRIPT SPA 教學](./tutorial-v2-javascript-spa.md)課程：深入探討如何使用 **MSAL.js** 來登入使用者，以及取得存取權杖以呼叫 **Microsoft Graph API** 。