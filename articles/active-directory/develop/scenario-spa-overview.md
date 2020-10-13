---
title: JavaScript 單一頁面應用程式案例-Microsoft 身分識別平臺 |蔚藍
description: 瞭解如何使用 Microsoft 身分識別平臺來建立單一頁面應用程式 (案例總覽) 。
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: dc0aee2e6c1b06850ffd0385626955a9798e7aeb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91257311"
---
# <a name="scenario-single-page-application"></a>案例：單一頁面應用程式

瞭解建立單一頁面應用程式 (SPA) 所需的一切。

## <a name="getting-started"></a>開始使用

如果您還沒有這麼做，請完成 JavaScript SPA 快速入門以建立您的第一個應用程式：

[快速入門：單一頁面應用程式](./quickstart-v2-javascript.md)

## <a name="overview"></a>概觀

許多新式 web 應用程式都是以用戶端的單一頁面應用程式來建立。 開發人員使用 JavaScript 或 SPA 架構（例如角度、Vue 和回應）來撰寫它們。 這些應用程式會在網頁瀏覽器上執行，而且其驗證特性與傳統伺服器端 web 應用程式不同。

Microsoft 身分識別平臺提供 **兩個** 選項，可讓單一頁面應用程式登入使用者，並取得權杖來存取後端服務或 web api：

- [OAuth 2.0 授權碼流程 (與 PKCE) ](./v2-oauth2-auth-code-flow.md)。 授權碼流程可讓應用程式交換 **識別碼** 權杖的授權碼，以代表呼叫受保護 api 所需的已驗證使用者和 **存取** 權杖。 此外，它會傳回重新 **整理權杖，** 以代表使用者提供資源的長期存取權，而不需要與這些使用者互動。 這是 **建議** 的方法。

![單一頁面應用程式-驗證](./media/scenarios/spa-app-auth.svg)

- [OAuth 2.0 隱含流程](./v2-oauth2-implicit-grant-flow.md)。 隱含授與流程可讓應用程式取得 **識別碼** 和 **存取** 權杖。 不同于授權碼流程，隱含授與流程不會傳回重新整理 **權杖**。

![單一頁面應用程式-隱含](./media/scenarios/spa-app.svg)

此驗證流程不包含使用跨平臺 JavaScript 架構的應用程式案例，例如 Electron 和回應原生。 它們需要進一步的功能來與原生平臺互動。

## <a name="specifics"></a>特性

若要為您的應用程式啟用此案例，您需要：

* 使用 Azure Active Directory (Azure AD) 註冊應用程式。 隱含授與流程與授權碼流程之間的註冊步驟不同。
* 使用已註冊應用程式屬性的應用程式設定，例如應用程式識別碼。
* 使用適用于 JavaScript 的 Microsoft 驗證程式庫 ( # A0) 來進行登入和取得權杖的驗證流程。

## <a name="recommended-reading"></a>建議閱讀資料

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式註冊](scenario-spa-app-registration.md)
