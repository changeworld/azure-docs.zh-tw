---
title: 將單一頁面應用程式移至生產環境-Microsoft 身分識別平臺 |Azure
description: 瞭解如何建立單一頁面應用程式（移至生產環境）
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
ms.openlocfilehash: 777f3de8f2872e378fe30cc50ee0a5eb3823a625
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2020
ms.locfileid: "82900318"
---
# <a name="single-page-application-move-to-production"></a>單一頁面應用程式：移至生產環境

現在您已瞭解如何取得權杖以呼叫 web Api，請瞭解如何進入生產環境。

## <a name="improve-your-app"></a>改善您的應用程式

[啟用記錄功能](msal-logging.md)，讓您的應用程式生產環境就緒。

## <a name="test-your-integration"></a>測試您的整合

遵循 [Microsoft 身分識別平台整合檢查清單](identity-platform-integration-checklist.md)來測試您的整合。

## <a name="next-steps"></a>後續步驟

深入瞭解快速入門範例，其中說明如何使用 MSAL 來登入使用者，以及取得存取權杖以呼叫 Microsoft Graph API 的程式碼：

> [!div class="nextstepaction"]
> [JavaScript SPA 教學課程](./tutorial-v2-javascript-spa.md)

示範如何使用 MSAL 取得您自己後端 Web API 權杖的範例：

> [!div class="nextstepaction"]
> [具有 ASP.NET 後端的 SPA](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi)

範例示範如何使用 MSAL，在向 Azure Active Directory B2C （Azure AD B2C）註冊的應用程式中登入使用者：

> [!div class="nextstepaction"]
> [具有 Azure AD B2C 的 SPA](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)
