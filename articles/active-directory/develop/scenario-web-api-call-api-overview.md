---
title: 建立呼叫 web Api 的 Web API-Microsoft 身分識別平臺 |Azure
description: 瞭解如何建立呼叫下游 web Api 的 Web API （總覽）。
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 467ff2f789cc83bc5651d831838da0b5c922c839
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701734"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>案例：呼叫 web Api 的 Web API

瞭解建立呼叫 web Api 的 Web API 需要知道的事項。

## <a name="prerequisites"></a>必要條件

這種情況下，受保護的 Web API 會呼叫 web Api，建置於「保護 Web API」案例之上。 若要深入瞭解此基本案例，請參閱[案例：受保護的 Web API](scenario-protected-web-api-overview.md)。

## <a name="overview"></a>概觀

- Web、桌面、行動或單頁應用程式用戶端（未在隨附的圖表中表示）會呼叫受保護的 Web API，並在其「授權」 HTTP 標頭中提供 JSON Web Token （JWT）持有人權杖。
- 受保護的 Web API 會驗證權杖，並使用 Microsoft 驗證程式庫（MSAL） `AcquireTokenOnBehalfOf` 方法向 Azure Active Directory （Azure AD）要求另一個權杖，讓受保護的 Web API 可以代表使用者呼叫第二個 Web API 或下游 Web API。
- 受保護的 Web API 也可以在稍後呼叫 `AcquireTokenSilent`來代表相同的使用者要求其他下游 Api 的權杖。 `AcquireTokenSilent` 視需要重新整理權杖。

![呼叫 Web API 之 Web API 的圖表](media/scenarios/web-api.svg)

## <a name="specifics"></a>瞭解

與 API 許可權相關的應用程式註冊部分為傳統。 應用程式設定牽涉到使用 OAuth 2.0 代理者流程，針對下游 API 的權杖交換 JWT 持有人權杖。 此權杖會新增至權杖快取，在其中可在 Web API 的控制器中使用，然後可以無訊息地取得權杖以呼叫下游 Api。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式註冊](scenario-web-api-call-api-app-registration.md)
