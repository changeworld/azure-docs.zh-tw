---
title: 構建一個調用 Web API 的 Web 應用程式 - 微軟身份平臺 |蔚藍
description: 瞭解如何構建調用 Web API 的 Web 應用（概述）
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
ms.custom: aaddev
ms.openlocfilehash: d121d6c198cb0d92cd098a40096e2f2300f65537
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76758984"
---
# <a name="scenario-a-web-app-that-calls-web-apis"></a>方案：調用 Web API 的 Web 應用

瞭解如何構建將使用者登錄到 Microsoft 標識平臺的 Web 應用，然後代表登錄使用者調用 Web API。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

此方案假定您已經經歷了以下方案：

> [!div class="nextstepaction"]
> [在使用者中簽名的 Web 應用](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>總覽

向 Web 應用添加身份驗證，以便它可以代表登錄使用者登錄使用者登錄並調用 Web API。

![呼叫 Web API 的 Web 應用程式](./media/scenario-webapp/web-app.svg)

調用 Web API 的 Web 應用是機密用戶端應用程式。
這就是為什麼他們向 Azure 活動目錄 （Azure AD） 註冊機密（應用程式密碼或證書）。 此機密在調用 Azure AD 以獲取權杖期間傳入。

## <a name="specifics"></a>細節

> [!NOTE]
> 向 Web 應用添加登錄是為了保護 Web 應用本身。 這種保護是通過使用*中介軟體*庫實現的，而不是使用 Microsoft 身份驗證庫 （MSAL）。 前面的方案，[在使用者中簽名的 Web 應用](scenario-web-app-sign-user-overview.md)涵蓋了該主題。
>
> 此方案介紹如何從 Web 應用調用 Web API。 您必須獲取這些 Web API 的訪問權杖。 要獲取這些權杖，請使用 MSAL 庫來獲取這些權杖。

此方案的開發涉及以下特定任務：

- 在[應用程式註冊](scenario-web-app-call-api-app-registration.md)期間，必須提供要與 Azure AD 共用的回復 URI、機密或證書。 如果將應用部署到多個位置，則將為每個位置提供此資訊。
- [應用程式佈建](scenario-web-app-call-api-app-configuration.md)必須提供在應用程式註冊期間與 Azure AD 共用的用戶端憑據。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [調用 Web API 的 Web 應用：應用註冊](scenario-web-app-call-api-app-registration.md)
