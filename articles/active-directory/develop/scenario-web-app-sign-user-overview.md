---
title: 從 Web 應用程式登入使用者-Microsoft 身分識別平臺 |蔚藍
description: '瞭解如何建立 web 應用程式，以登入使用者 (總覽) '
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 7cadb4784cbf90d283f64e12edc155d4430fab06
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91257058"
---
# <a name="scenario-web-app-that-signs-in-users"></a>案例：登入使用者的 Web 應用程式

瞭解您所需的一切，以建立使用 Microsoft 身分識別平臺來登入使用者的 web 應用程式。

## <a name="getting-started"></a>開始使用

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

如果您想要建立第一個可攜 (ASP.NET Core) web 應用程式登入使用者，請遵循本快速入門：

> [!div class="nextstepaction"]
> [快速入門： ASP.NET Core 登入使用者的 web 應用程式](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

如果您想要瞭解如何將登入新增至現有的 ASP.NET web 應用程式，請嘗試下列快速入門：

> [!div class="nextstepaction"]
> [快速入門： ASP.NET 登入使用者的 web 應用程式](quickstart-v2-aspnet-webapp.md)

# <a name="java"></a>[Java](#tab/java)

如果您是 JAVA 開發人員，請嘗試下列快速入門：

> [!div class="nextstepaction"]
> [快速入門：將「使用 Microsoft 登入」新增至 Java Web 應用程式](quickstart-v2-java-webapp.md)

# <a name="python"></a>[Python](#tab/python)

如果您使用 Python 進行開發，請嘗試下列快速入門：

> [!div class="nextstepaction"]
> [快速入門：將「使用 Microsoft 登入」新增至 Python Web 應用程式](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>概觀

您會將驗證新增至您的 web 應用程式，讓它可以登入使用者。 新增驗證可讓您的 web 應用程式存取有限的設定檔資訊，以便自訂使用者的體驗。

Web 應用程式會在網頁瀏覽器中驗證使用者。 在此案例中，web 應用程式會指示使用者的瀏覽器將其登入，以 Azure Active Directory (Azure AD) 。 Azure AD 會透過使用者的瀏覽器傳回登入回應，其中包含有關安全性權杖中使用者的宣告。 登入使用者會利用 [開放式識別碼 Connect](./v2-protocols-oidc.md) 標準通訊協定，並藉由使用中介軟體連結 [庫](scenario-web-app-sign-user-app-configuration.md#libraries-for-protecting-web-apps)來簡化。

![登入使用者的 Web 應用程式](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

作為第二個階段，您可以讓應用程式代表已登入使用者呼叫 web Api。 下一個階段是不同的案例，您會在 [web 應用程式中找到呼叫 Web api 的](scenario-web-app-call-api-overview.md)不同案例。

> [!NOTE]
> 將登入新增至 web 應用程式的方式，是關於保護 web 應用程式和驗證使用者權杖，這是  **中介軟體** 程式庫的用途。 在 .NET 的案例中，此案例還不需要 Microsoft 驗證程式庫 (MSAL) ，也就是取得權杖來呼叫受保護的 Api。 當 web 應用程式需要呼叫 web Api 時，會在後續案例中引進驗證程式庫。

## <a name="specifics"></a>特性

- 在應用程式註冊期間，如果您將應用程式部署到多個位置) 回復 Uri，則必須提供一或多個 (。 在某些情況下 (ASP.NET 和 ASP.NET Core) 時，您必須啟用識別碼權杖。 最後，您會想要設定登出 URI，讓您的應用程式回應登出的使用者。
- 在您應用程式的程式碼中，您必須提供 web 應用程式委派登入的授權單位。 在合作夥伴案例中，您可能會想要自訂權杖驗證 (特別是) 。
- Web 應用程式支援任何帳戶類型。 如需詳細資訊，請參閱 [支援的帳戶類型](v2-supported-account-types.md)。

## <a name="recommended-reading"></a>建議閱讀資料

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>後續步驟

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [應用程式註冊](./scenario-web-app-sign-user-app-registration.md?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [應用程式註冊](./scenario-web-app-sign-user-app-registration.md?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [應用程式註冊](./scenario-web-app-sign-user-app-registration.md?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [應用程式註冊](./scenario-web-app-sign-user-app-registration.md?tabs=python)

---