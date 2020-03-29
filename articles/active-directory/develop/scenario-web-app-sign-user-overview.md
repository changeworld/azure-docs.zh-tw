---
title: 從 Web 應用登錄使用者 - 微軟身份平臺 |蔚藍
description: 瞭解如何構建在使用者中簽名的 Web 應用（概述）
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
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 403f589702fd7142f0515a3b6f19ee1b9bbb6420
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701547"
---
# <a name="scenario-web-app-that-signs-in-users"></a>方案：在使用者中簽名的 Web 應用

瞭解構建使用 Microsoft 標識平臺登錄使用者的 Web 應用所需的全部內容。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>開始使用

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

如果要創建第一個在使用者中簽名的可擕式（ASP.NET酷睿）Web 應用，請按照以下快速入門操作：

> [!div class="nextstepaction"]
> [快速入門：ASP.NET核心 Web 應用程式，在使用者中簽名](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

如果您想瞭解如何將登錄添加到現有ASP.NET Web 應用程式，請嘗試以下快速入門：

> [!div class="nextstepaction"]
> [快速入門：ASP.NET在使用者中簽名的 Web 應用](quickstart-v2-aspnet-webapp.md)

# <a name="java"></a>[JAVA](#tab/java)

如果您是 JAVA 開發人員，請嘗試以下快速入門：

> [!div class="nextstepaction"]
> [快速入門：將 Microsoft 的登錄添加到 JAVA Web 應用](quickstart-v2-java-webapp.md)

# <a name="python"></a>[Python](#tab/python)

如果使用 Python 進行開發，請嘗試以下快速入門：

> [!div class="nextstepaction"]
> [快速入門：將 Microsoft 登錄添加到 Python Web 應用](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>總覽

向 Web 應用添加身份驗證，以便它可以登錄使用者。 添加身份驗證使 Web 應用可以訪問有限的設定檔資訊，以便自訂使用者的體驗。 

Web 應用在 Web 瀏覽器中對使用者進行身份驗證。 在這種情況下，Web 應用指示使用者的瀏覽器將其登錄到 Azure 活動目錄 （Azure AD）。 Azure AD 通過使用者的瀏覽器返回登錄回應，該瀏覽器包含安全權杖中有關使用者的聲明。 登錄使用者利用開放 ID[連接](./v2-protocols-oidc.md)標準協定，通過使用中介軟體[庫](scenario-web-app-sign-user-app-configuration.md#libraries-for-protecting-web-apps)進行簡化。

![登入使用者的 Web 應用程式](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

作為第二階段，您可以使應用程式能夠代表登錄使用者調用 Web API。 下一階段是一個不同的方案，您將在調用 Web [API 的 Web 應用中](scenario-web-app-call-api-overview.md)找到。

> [!NOTE]
> 向 Web 應用添加登錄內容是保護 Web 應用並驗證使用者權杖，這是**中介軟體**庫所做的。 對於 .NET，此方案尚不需要 Microsoft 身份驗證庫 （MSAL），即獲取權杖來調用受保護的 API。 當 Web 應用需要調用 Web API 時，將在後續方案中引入身份驗證庫。

## <a name="specifics"></a>細節

- 在應用程式註冊期間，您需要提供一個或多個（如果將應用部署到多個位置）回復 URI。 在某些情況下（ASP.NET和ASP.NET核心），您需要啟用 ID 權杖。 最後，您需要設置一個登出 URI，以便應用程式對登出的使用者做出反應。
- 在應用程式的代碼中，您需要提供 Web 應用委派登錄的許可權。 您可能希望自訂權杖驗證（特別是在合作夥伴方案中）。
- Web 應用程式支援任何帳戶類型。 有關詳細資訊，請參閱[受支援的帳戶類型](v2-supported-account-types.md)。

## <a name="next-steps"></a>後續步驟

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [應用註冊](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [應用註冊](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnet)

# <a name="java"></a>[JAVA](#tab/java)

> [!div class="nextstepaction"]
> [應用註冊](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [應用註冊](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=python)

---
