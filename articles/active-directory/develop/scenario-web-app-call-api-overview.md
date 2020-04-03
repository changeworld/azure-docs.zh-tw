---
title: 構建一個調用 Web API 的 Web 應用程式 - 微軟身份平臺 |蔚藍
description: 瞭解如何建構 Web API 的 Web 應用(概述)
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
ms.openlocfilehash: 14f513306b3f0bc0c06a4143e5174c3ecddaef62
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617173"
---
# <a name="scenario-a-web-app-that-calls-web-apis"></a>專案:呼叫 Web API 的 Web 應用

瞭解如何構建將使用者登錄到 Microsoft 標識平臺的 Web 應用,然後代表登入使用者調用 Web API。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

此方案假定您已經經歷了以下方案:

> [!div class="nextstepaction"]
> [在使用者中簽名的 Web 應用](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>概觀

向 Web 應用程式加入驗證,以便它可以代表登入使用者登入使用者登入並調用 Web API。

![呼叫 Web API 的 Web 應用程式](./media/scenario-webapp/web-app.svg)

呼叫 Web API 的 Web 應用是機密用戶端應用程式。
這就是為什麼他們向 Azure 活動目錄 (Azure AD) 註冊機密(應用程式密碼或證書)。 此機密在調用 Azure AD 以獲取權杖期間傳入。

## <a name="specifics"></a>詳細資料

> [!NOTE]
> 向 Web 應用添加登錄是為了保護 Web 應用本身。 這種保護是通過使用*中間件*庫實現的,而不是使用 Microsoft 身份驗證庫 (MSAL)。 前面的方案,[在使用者中簽名的 Web 應用](scenario-web-app-sign-user-overview.md)涵蓋了該主題。
>
> 此方案介紹如何從 Web 應用調用 Web API。 您必須取得這些 Web API 的存取權杖。 您可以使用 MSAL 函式庫來取得這些權杖。

此專案的開發涉及以下特定工作:

- 在[應用程式註冊](scenario-web-app-call-api-app-registration.md)期間,必須提供要與 Azure AD 共用的回覆 URI、機密或證書。 如果將應用部署到多個位置,則將為每個位置提供此資訊。
- [應用程式設定](scenario-web-app-call-api-app-configuration.md)必須提供在應用程式註冊期間與 Azure AD 共用的用戶端認證。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [呼叫 Web API 的 Web 應用:應用程式註冊](scenario-web-app-call-api-app-registration.md)
