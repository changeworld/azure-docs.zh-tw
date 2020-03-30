---
title: 公共和機密用戶端應用 （MSAL） |蔚藍
titleSuffix: Microsoft identity platform
description: 在 Microsoft 身份驗證庫 （MSAL） 中瞭解公共用戶端和機密用戶端應用程式。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/25/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: d59819c0ab614b0f6cc102c7ebe8c760fb851599
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084130"
---
# <a name="public-client-and-confidential-client-applications"></a>公共用戶端和機密用戶端應用程式
Microsoft 身份驗證庫 （MSAL） 定義了兩種類型的用戶端：公共用戶端和機密用戶端。 這兩種用戶端類型因能夠使用授權伺服器進行安全身份驗證並維護其用戶端憑據的機密性而與眾不同。 相反，Azure AD 身份驗證庫 （ADAL） 使用所謂的*身份驗證上下文*（即與 Azure AD 的連接）。

- **機密用戶端應用程式**是在伺服器上運行的應用（Web 應用、Web API 應用，甚至服務/守護應用程式）。 它們被認為難以訪問，因此能夠保留應用程式機密。 機密用戶端可以保存配置時間機密。 用戶端的每個實例都有不同的配置（包括用戶端 ID 和用戶端金鑰）。 最終使用者很難提取這些值。 Web 應用是最常見的機密用戶端。 用戶端 ID 通過 Web 瀏覽器公開，但金鑰僅在後通道中傳遞，並且從不直接公開。

    機密用戶端應用： <BR>
    ![Web](media/msal-client-applications/web-app.png)![應用](media/msal-client-applications/web-api.png)![Web API 守護進程/服務](media/msal-client-applications/daemon-service.png)

- **公共用戶端應用程式**是在設備或桌上型電腦或 Web 瀏覽器中運行的應用程式。 不信任它們安全地保存應用程式機密，因此它們僅代表使用者訪問 Web API。 （它們僅支援公共用戶端流。公共用戶端不能保存配置時間機密，因此它們沒有用戶端機密。

    公共用戶端應用： <BR>
    ![桌面應用程式](media/msal-client-applications/desktop-app.png)![無瀏覽器](media/msal-client-applications/browserless-app.png)![API 移動應用](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> 在 MSAL.js 中，公共和機密用戶端應用沒有分離。  MSAL.js 表示用戶端應用為基於使用者代理的應用，公共用戶端在使用者代理（如 Web 瀏覽器）中執行用戶端代碼。 這些用戶端不存儲機密，因為瀏覽器上下文是可公開訪問的。

## <a name="comparing-the-client-types"></a>比較用戶端類型
以下是公共用戶端和機密用戶端應用之間的一些相似之處和差異：

- 這兩種應用都維護使用者權杖緩存，並可以靜默方式獲取權杖（當令牌已在權杖緩存中時）。 機密用戶端應用還具有針對應用本身的權杖的應用權杖緩存。
- 這兩種類型的應用都管理使用者帳戶，並可以從使用者權杖緩存獲取帳戶、從其識別碼獲取帳戶或刪除帳戶。
- 公共用戶端應用有四種獲取權杖的方法（四個身份驗證流）。 機密用戶端應用有三種獲取權杖的方法（以及一種計算標識提供程式授權終結點的 URL 的方法）。 有關詳細資訊，請參閱[獲取權杖](msal-acquire-cache-tokens.md)。

如果您使用過 ADAL，您可能會注意到，與 ADAL 的身份驗證上下文不同，在 MSAL 中，用戶端 ID（也稱為*應用程式 ID*或*應用 ID）* 在應用程式構造時傳遞一次。 當應用獲取權杖時，無需再次傳遞它。 對於公共和機密用戶端應用也是如此。 機密用戶端應用的建構函式也傳遞用戶端憑據：他們與標識提供程式共用的機密。

## <a name="next-steps"></a>後續步驟
了解：
- [用戶端應用程式配置選項](msal-client-application-configuration.md)
- [使用MSAL.NET具現化用戶端應用程式](msal-net-initializing-client-applications.md)
- [使用 MSAL.js 具現化用戶端應用程式](msal-js-initializing-client-applications.md)
