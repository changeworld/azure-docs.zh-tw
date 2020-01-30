---
title: 將登入使用者的 web 應用程式移至生產環境-Microsoft 身分識別平臺 |Azure
description: 瞭解如何建立可登入使用者（移至生產環境）的 web 應用程式
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6c486e59f32afd09a9934ae2298172ccb4ee2414
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768111"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>登入使用者的 Web 應用程式：移至生產環境

既然您已經知道如何取得權杖來呼叫 web Api，請瞭解如何將它移到生產環境。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>後續步驟

### <a name="same-site"></a>相同網站

請確定您瞭解新版本 Chrome 瀏覽器的可能問題

> [!div class="nextstepaction"]
> [如何處理 Chrome 瀏覽器中的 SameSite cookie 變更](howto-handle-samesite-cookie-changes-chrome-browser.md)

### <a name="scenario-for-calling-web-apis"></a>呼叫 web Api 的案例

在您的 web 應用程式登入使用者之後，它可以代表登入的使用者呼叫 web Api。 從 web 應用程式呼叫 web Api 是下列案例的物件：

> [!div class="nextstepaction"]
> [呼叫 Web API 的 Web 應用程式](scenario-web-app-call-api-overview.md)

## <a name="deep-dive-aspnet-core-web-app-tutorial"></a>深入探討： ASP.NET Core web 應用程式教學課程

深入瞭解使用此 ASP.NET Core 教學課程來登入使用者的其他方式： 

> [!div class="nextstepaction"]
> [讓您的 web 應用程式能夠使用 Microsoft 身分識別平臺來登入使用者並呼叫 Api，讓開發人員](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 

這個漸進式教學課程包含適用于 web 應用程式的生產就緒程式碼，包括如何在中新增帳戶登入：

- 您的組織
- 多個組織
- 公司或學校帳戶，或個人 Microsoft 帳戶
- [Azure AD B2C](https://aka.ms/aadb2c)
- 國家雲端

## <a name="sample-code-java-web-app"></a>範例程式碼： JAVA web 應用程式

從 GitHub 上的此範例深入瞭解 JAVA web 應用程式： 

> [!div class="nextstepaction"]
> [使用 Microsoft 身分識別平臺登入使用者並呼叫 Microsoft Graph 的 JAVA Web 應用程式](https://github.com/Azure-Samples/ms-identity-java-webapp)
