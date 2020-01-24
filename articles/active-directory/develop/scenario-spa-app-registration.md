---
title: 註冊單一頁面應用程式-Microsoft 身分識別平臺 |Azure
description: 瞭解如何建立單一頁面應用程式（應用程式註冊）
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
ms.custom: aaddev
ms.openlocfilehash: 5b18b10748e0587920c6965f1d235376da928469
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701887"
---
# <a name="single-page-application-app-registration"></a>單一頁面應用程式：應用程式註冊

本頁面說明單一頁面應用程式（SPA）的應用程式註冊細節。

遵循下列步驟，向[Microsoft 身分識別平臺註冊新的應用程式](quickstart-register-app.md)，並為您的應用程式選取支援的帳戶。 SPA 案例可支援貴組織中的帳戶或任何組織和個人 Microsoft 帳戶的驗證。

接下來，請瞭解適用于單一頁面應用程式之應用程式註冊的特定層面。

## <a name="register-a-redirect-uri"></a>註冊重新導向 URI

隱含流程會將重新導向中的權杖傳送至在網頁瀏覽器上執行的單一頁面應用程式。 因此，請務必註冊應用程式可以接收權杖的重新導向 URI。 確定重新導向 URI 完全符合您應用程式的 URI。

在[Azure 入口網站](https://go.microsoft.com/fwlink/?linkid=2083908)中，移至您已註冊的應用程式。 在應用程式的 [**驗證**] 頁面上，選取**Web**平臺。 在 [重新**導向 uri** ] 欄位中，輸入您應用程式的重新導向 uri 值。

## <a name="enable-the-implicit-flow"></a>啟用隱含流程

在同一個 [**驗證**] 頁面的 [ **Advanced settings**] 底下，您也必須啟用 **[隱含授**與]。 如果您的應用程式只會登入使用者並取得識別碼權杖，就足以選取 [**識別碼權杖**] 核取方塊。

如果您的應用程式也需要取得存取權杖來呼叫 Api，請務必同時選取 [**存取權杖**] 核取方塊。 如需詳細資訊，請參閱[識別碼權杖](./id-tokens.md)和[存取權杖](./access-tokens.md)。

## <a name="api-permissions"></a>API 權限

單頁應用程式可以代表已登入的使用者呼叫 Api。 他們需要要求委派的許可權。 如需詳細資訊，請參閱[新增存取 Web api 的許可權](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式的程式碼設定](scenario-spa-app-configuration.md)
