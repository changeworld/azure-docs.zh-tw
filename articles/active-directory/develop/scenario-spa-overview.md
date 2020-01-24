---
title: JavaScript 單一頁面應用程式案例-Microsoft 身分識別平臺 |Azure
description: 瞭解如何使用 Microsoft 身分識別平臺來建立單一頁面應用程式（案例總覽）。
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: b430778bed811656b5c8aadc75ba3cf35917f737
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701870"
---
# <a name="scenario-single-page-application"></a>案例：單一頁面應用程式

瞭解您所需的一切，以建立單一頁面應用程式（SPA）。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>開始使用

您可以遵循 JavaScript SPA 快速入門來建立第一個應用程式：

> [!div class="nextstepaction"]
> [快速入門：單一頁面應用程式](./quickstart-v2-javascript.md)

## <a name="overview"></a>概觀

許多新式 web 應用程式都是以用戶端單一頁面應用程式的形式建立。 開發人員可以使用 JavaScript 或 SPA 架構（例如，角度、Vue 和回應 .js）來撰寫它們。 這些應用程式會在網頁瀏覽器上執行，並具有與傳統伺服器端 web 應用程式不同的驗證特性。 

Microsoft 身分識別平臺可讓單一頁面應用程式登入使用者並取得權杖，以使用[OAuth 2.0 隱含流程](./v2-oauth2-implicit-grant-flow.md)來存取後端服務或 web api。 隱含流程可讓應用程式取得識別碼權杖，以代表已驗證的使用者，也會存取呼叫受保護 Api 所需的權杖。

![單頁應用程式](./media/scenarios/spa-app.svg)

此驗證流程不包括使用跨平臺 JavaScript 架構的應用程式案例，例如 Electron 和回應原生。 它們需要進一步的功能來與原生平臺互動。

## <a name="specifics"></a>瞭解

若要為您的應用程式啟用此案例，您需要：

* 使用 Azure Active Directory （Azure AD）註冊應用程式。 此註冊牽涉到啟用隱含流程，並設定要傳回權杖的重新導向 URI。
* 具有已註冊應用程式屬性的應用程式設定，例如 [應用程式識別碼]。
* 使用 Microsoft 驗證程式庫（MSAL）來執行驗證流程以登入和取得權杖。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式註冊](scenario-spa-app-registration.md)
