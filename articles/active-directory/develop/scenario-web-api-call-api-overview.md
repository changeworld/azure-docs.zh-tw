---
title: 構建一個調用 Web API 的 Web API - 微軟身份平臺 |蔚藍
description: 瞭解如何構建調用下游 Web API（概述）的 Web API。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701734"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>方案：調用 Web API 的 Web API

瞭解構建調用 Web API 的 Web API 所需的知識。

## <a name="prerequisites"></a>Prerequisites

此方案（受保護的 Web API 呼叫 Web API）構建在"保護 Web API"方案之上。 要瞭解有關此基本方案的更多資訊，請參閱[方案：受保護的 Web API](scenario-protected-web-api-overview.md)。

## <a name="overview"></a>總覽

- Web、桌面、移動或單頁應用程式用戶端（未在隨附的關係圖中表示）調用受保護的 Web API，並在其"授權"HTTP 標頭中提供 JSON Web 權杖 （JWT） 承載權杖。
- 受保護的 Web API 驗證權杖並使用 Microsoft 身份驗證庫 （MSAL）`AcquireTokenOnBehalfOf`方法從 Azure 活動目錄 （Azure AD） 請求另一個權杖，以便受保護的 Web API 可以代表使用者調用第二個 Web API 或下游 Web API。
- 受保護的 Web API 也可以`AcquireTokenSilent`稍後調用代表同一使用者請求其他下游 API 的權杖。 `AcquireTokenSilent`在需要時刷新權杖。

![調用 Web API 的 Web API 圖](media/scenarios/web-api.svg)

## <a name="specifics"></a>細節

與 API 許可權相關的應用註冊部分是經典內容。 應用配置涉及使用 OAuth 2.0 代表流將 JWT 承載權杖與下游 API 的權杖交換。 此權杖將添加到權杖緩存中，在 Web API 的控制器中可用，然後它可以以靜默方式獲取權杖以調用下游 API。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用註冊](scenario-web-api-call-api-app-registration.md)
