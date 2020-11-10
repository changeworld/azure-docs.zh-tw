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
ms.custom: aaddev, identityplatformtop40, devx-track-js
ms.openlocfilehash: 17acb2bc5e96a136f31371c0be912c2c758c0f76
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94443852"
---
# <a name="scenario-single-page-application"></a>案例：單頁應用程式

瞭解建立單一頁面應用程式 (SPA) 所需的一切。

## <a name="getting-started"></a>開始使用

如果您還沒有這麼做，請完成 JavaScript SPA 快速入門以建立您的第一個應用程式：

[快速入門：單一頁面應用程式](./quickstart-v2-javascript.md)

## <a name="overview"></a>概觀

許多新式 web 應用程式都是以用戶端的單一頁面應用程式來建立。 開發人員可以使用 JavaScript 或 SPA 架構 (例如 Angular、Vue 和 React) 來撰寫這些應用程式。 這些應用程式會在網頁瀏覽器上執行，且具有與傳統伺服器端 Web 應用程式不同的驗證特性。

Microsoft 身分識別平臺提供 **兩個** 選項，可讓單一頁面應用程式登入使用者，並取得權杖來存取後端服務或 web api：

- [OAuth 2.0 授權碼流程 (使用 PKCE)](./v2-oauth2-auth-code-flow.md)。 授權碼流程可讓應用程式針對代表已驗證使用者的 **識別碼** 權杖，以及呼叫受保護 API 所需的 **存取** 權杖，交換授權碼。 此外，其會傳回 **重新整理** 權杖，以代表使用者提供長期的資源存取權，而不需要與使用者互動。 這是 **建議** 的方法。

![Single-page applications-auth](./media/scenarios/spa-app-auth.svg)

- [OAuth 2.0 隱含流程](./v2-oauth2-implicit-grant-flow.md)。 隱含授與流程可讓應用程式取得 **識別碼** 及 **存取** 權杖。 不同於授權碼流程，隱含授與流程不會傳回 **重新整理權杖** 。

![Single-page applications-implicit](./media/scenarios/spa-app.svg)

此驗證流程不包含使用跨平台 JavaScript 架構 (例如 Electron 和 React-Native) 的應用程式案例。 它們需要進一步的功能來與原生平臺互動。

## <a name="specifics"></a>特性

若要為您的應用程式啟用此案例，您需要：

* 使用 Azure Active Directory (Azure AD) 註冊應用程式。 隱含授與流程與授權碼流程之間的註冊步驟不同。
* 使用已註冊應用程式屬性的應用程式設定，例如應用程式識別碼。
* 使用適用于 JavaScript 的 Microsoft 驗證程式庫 ( # A0) 來進行登入和取得權杖的驗證流程。

## <a name="recommended-reading"></a>建議閱讀資料

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>後續步驟

請移至此案例的 [應用程式註冊](scenario-spa-app-registration.md)中的下一篇文章。
