---
title: 建立可呼叫 web Api 的 web API-Microsoft 身分識別平臺 |蔚藍
description: 瞭解如何建立 web API 來呼叫下游 web Api (總覽) 。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 4dcf377797709b56b4db735dabf4d48cfae4fc06
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91257158"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>案例：呼叫 web Api 的 web API

瞭解如何建立可呼叫 web Api 的 web API 的須知事項。

## <a name="prerequisites"></a>Prerequisites

在此案例中，受保護的 web API 會呼叫其他 web Api，並以 [案例為基礎：受保護的 WEB api](scenario-protected-web-api-overview.md)。

## <a name="overview"></a>概觀

- Web、桌面、行動或單頁應用程式用戶端 (不會在隨附的圖表中呈現) 呼叫受保護的 web API，並在其「授權」 HTTP 標頭中提供 JSON Web 權杖 (JWT) 持有人權杖。
- 受保護的 web API 會驗證權杖，並使用 Microsoft 驗證程式庫 (MSAL) `AcquireTokenOnBehalfOf` 方法，從 Azure Active Directory (Azure AD) 要求另一個權杖，讓受保護的 WEB api 可以代表使用者呼叫第二個 WEB api 或下游 WEB api。
- 受保護的 web API 也可以 `AcquireTokenSilent` 在稍後呼叫，以代表相同的使用者要求其他下游 api 的權杖。 `AcquireTokenSilent` 視需要重新整理權杖。

![呼叫 web API 的 web API 圖表](media/scenarios/web-api.svg)

## <a name="specifics"></a>特性

與 API 許可權相關的應用程式註冊部分是傳統的。 應用程式設定牽涉到使用 OAuth 2.0 代理者流程，以針對下游 API 的權杖交換 JWT 持有人權杖。 此權杖會新增至權杖快取，在 web API 的控制器中可供使用，並可讓您以無訊息方式取得權杖來呼叫下游 Api。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式註冊](scenario-web-api-call-api-app-registration.md)
