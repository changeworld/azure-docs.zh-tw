---
title: 註冊單頁應用 - 微軟身份平臺 |蔚藍
description: 瞭解如何建構單頁應用程式(應用程式註冊)
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
ms.openlocfilehash: 6f690a8b3436a45d434ccad2bbaa7d2a1b0b76aa
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882143"
---
# <a name="single-page-application-app-registration"></a>單頁應用程式:應用程式註冊

本頁介紹單頁應用程式 (SPA) 的應用註冊細節。

按照以下步驟向[Microsoft 標識平台註冊新應用程式](quickstart-register-app.md),然後為應用程式選擇受支援的帳戶。 SPA 方案可以支援對組織中的帳戶或任何組織和個人 Microsoft 帳戶進行身份驗證。

接下來,瞭解適用於單頁應用程式的應用程式註冊的具體方面。

## <a name="register-a-redirect-uri"></a>註冊重定向 URI

隱含流將重定向中的權杖發送到在 Web 瀏覽器上執行的單頁應用程式。 因此,註冊重定向URI非常重要,應用程式可以在其中接收權杖。 確保重定向URI與應用程式的URI完全匹配。

在[Azure 門戶](https://go.microsoft.com/fwlink/?linkid=2083908)中,轉到已註冊的應用程式。 在應用程式的**身份驗證**頁上,選擇**Web**平臺。 在**重定向URI**欄位中輸入應用程式的重定向URI的值。

## <a name="enable-the-implicit-flow"></a>開啟隱式流

在同一**認證**頁上,在 **「進階設定」** 下,還必須啟用**隱式授予**。 如果應用程式僅登錄使用者並獲取 ID 權杖,則選擇**ID 權杖**複選框就足夠了。

如果應用程式還需要獲取訪問權杖才能調用 API,請確保也選中 **「訪問權杖**」複選框。 關於詳細資訊,請參閱[ID 權碼](./id-tokens.md)與[存取權杖](./access-tokens.md)。

## <a name="api-permissions"></a>API 權限

單頁應用程式可以代表登錄使用者調用 API。 他們需要請求委派的許可權。 有關詳細資訊,請參閱[新增存取 Web API 的權限](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [套用的代碼設定](scenario-spa-app-configuration.md)
