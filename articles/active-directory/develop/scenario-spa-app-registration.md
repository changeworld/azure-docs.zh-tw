---
title: 註冊單頁應用 - 微軟身份平臺 |蔚藍
description: 瞭解如何構建單頁應用程式（應用註冊）
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701887"
---
# <a name="single-page-application-app-registration"></a>單頁應用程式：應用程式註冊

本頁介紹單頁應用程式 （SPA） 的應用註冊細節。

按照以下步驟向[Microsoft 標識平臺註冊新應用程式](quickstart-register-app.md)，然後為應用程式選擇受支援的帳戶。 SPA 方案可以支援對組織中的帳戶或任何組織和個人 Microsoft 帳戶進行身份驗證。

接下來，瞭解適用于單頁應用程式的應用程式註冊的具體方面。

## <a name="register-a-redirect-uri"></a>註冊重定向 URI

隱式流將重定向中的權杖發送到在 Web 瀏覽器上運行的單頁應用程式。 因此，註冊重定向 URI 非常重要，應用程式可以在其中接收權杖。 確保重定向 URI 與應用程式的 URI 完全符合。

在[Azure 門戶](https://go.microsoft.com/fwlink/?linkid=2083908)中，轉到已註冊的應用程式。 在應用程式的**身份驗證**頁上，選擇**Web**平臺。 在**重定向 URI**欄位中輸入應用程式的重定向 URI 的值。

## <a name="enable-the-implicit-flow"></a>啟用隱式流

在同一**身份驗證**頁上，在 **"高級設置"** 下，還必須啟用**隱式授予**。 如果應用程式僅登錄使用者並獲取 ID 權杖，則選中**ID 權杖**核取方塊就足夠了。

如果應用程式還需要獲取訪問權杖才能調用 API，請確保也選中 **"訪問權杖**"核取方塊。 有關詳細資訊，請參閱[ID 權杖](./id-tokens.md)和[訪問權杖](./access-tokens.md)。

## <a name="api-permissions"></a>API 權限

單頁應用程式可以代表登錄使用者調用 API。 他們需要請求委派的許可權。 有關詳細資訊，請參閱[添加訪問 Web API 的許可權](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用的代碼配置](scenario-spa-app-configuration.md)
