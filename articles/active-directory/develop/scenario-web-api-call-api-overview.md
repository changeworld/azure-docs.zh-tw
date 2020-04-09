---
title: 構建一個呼叫 Web API 的 Web API - 微軟身份平臺 |蔚藍
description: 瞭解如何建構調用下游 Web API(概述)的 Web API。
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
ms.openlocfilehash: 88a0177755fbd913bdaaf0ecf3e12c62dee294c1
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885067"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>專案:呼叫 Web API 的 Web API

瞭解建構數據 Web API 的 Web API 所需的知識。

## <a name="prerequisites"></a>Prerequisites

此方案(受保護的 Web API 呼叫 Web API)構建在「保護 Web API」方案之上。 要瞭解有關此基本方案的更多資訊,請參閱[方案:受保護的 Web API](scenario-protected-web-api-overview.md)。

## <a name="overview"></a>概觀

- Web、桌面、移動或單頁應用程式用戶端(未在隨附的關係圖中表示)調用受保護的 Web API,並在其"授權"HTTP 標頭中提供 JSON Web 權杖 (JWT) 承載權杖。
- 受保護的 Web API 驗證權杖並使用 Microsoft 身份驗證`AcquireTokenOnBehalfOf`庫 (MSAL) 方法從 Azure 活動目錄 (Azure AD) 請求另一個權杖,以便受保護的 Web API 可以代表使用者調用第二個 Web API 或下游 Web API。
- 受保護的 Web API 也可以`AcquireTokenSilent`稍後呼叫代表同一使用者請求其他下游 API 的權杖。 `AcquireTokenSilent`在需要時刷新權杖。

![呼叫 Web API 的 Web API 圖](media/scenarios/web-api.svg)

## <a name="specifics"></a>詳細資料

與 API 許可權相關的應用註冊部分是經典內容。 應用配置涉及使用 OAuth 2.0 代表流將 JWT 承載權杖與下游 API 的權杖交換。 此令牌將添加到權杖快取中,在 Web API 的控制器中可用,然後它可以以靜默方式獲取權杖以呼叫下遊 API。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式註冊](scenario-web-api-call-api-app-registration.md)
