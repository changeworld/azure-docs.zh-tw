---
title: 在登出時從權杖快取中移除帳戶-Microsoft 身分識別平臺 |Azure
description: 瞭解如何在登出時從權杖快取移除帳戶
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5246100815fde569c55027a555464c44a240d4b3
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88119992"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>呼叫 web Api 的 web 應用程式：在全域登出時從權杖快取中移除帳戶

您已瞭解如何在登入使用者的 Web 應用程式中，將登入新增至您的 web 應用程式[：登入和登出](scenario-web-app-sign-user-sign-in.md)。

呼叫 web api 的 web 應用程式會有不同的登出。 當使用者登出您的應用程式或任何應用程式時，您必須從權杖快取中移除與該使用者相關聯的權杖。

## <a name="intercept-the-callback-after-single-sign-out"></a>在單一登出後攔截回呼

若要清除與已登出之帳戶相關聯的權杖快取專案，您的應用程式可以攔截 after `logout` 事件。 Web apps 會在權杖快取中儲存每個使用者的存取權杖。 藉由攔截回呼後的 `logout` ，您的 web 應用程式就可以從快取中移除使用者。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Microsoft 身分識別 Web 會負責為您執行登出。 如需詳細資訊，請參閱[Microsoft 原始碼](https://github.com/AzureAD/microsoft-identity-web/blob/c29f1a7950b940208440bebf0bcb524a7d6bee22/src/Microsoft.Identity.Web/WebAppExtensions/WebAppCallsWebApiAuthenticationBuilderExtensions.cs#L168-L176)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET 範例不會從全域登出的快取中移除帳戶。

# <a name="java"></a>[Java](#tab/java)

JAVA 範例不會從全域登出的快取中移除帳戶。

# <a name="python"></a>[Python](#tab/python)

Python 範例不會從全域登出的快取中移除帳戶。

---

## <a name="next-steps"></a>後續步驟

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [取得 web 應用程式的權杖](./scenario-web-app-call-api-acquire-token.md?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [取得 web 應用程式的權杖](./scenario-web-app-call-api-acquire-token.md?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [取得 web 應用程式的權杖](./scenario-web-app-call-api-acquire-token.md?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [取得 web 應用程式的權杖](./scenario-web-app-call-api-acquire-token.md?tabs=python)

---