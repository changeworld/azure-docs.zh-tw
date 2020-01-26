---
title: 建立呼叫 web Api 的 web 應用程式-Microsoft 身分識別平臺 |Azure
description: 瞭解如何建立呼叫 web Api 的 web 應用程式（總覽）
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
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2020
ms.locfileid: "76758984"
---
# <a name="scenario-a-web-app-that-calls-web-apis"></a>案例：呼叫 web Api 的 web 應用程式

瞭解如何建立 web 應用程式，以將使用者登入 Microsoft 身分識別平臺，然後代表已登入的使用者呼叫 web Api。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

此案例假設您已完成下列案例：

> [!div class="nextstepaction"]
> [登入使用者的 Web 應用程式](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>概觀

您會將驗證新增至 web 應用程式，讓它可以將使用者登入，並代表登入的使用者呼叫 Web API。

![呼叫 Web API 的 Web 應用程式](./media/scenario-webapp/web-app.svg)

呼叫 web Api 的 web 應用程式是機密用戶端應用程式。
這就是為什麼他們會使用 Azure Active Directory （Azure AD）來註冊密碼（應用程式密碼或憑證）。 此密碼會在呼叫 Azure AD 時傳入，以取得權杖。

## <a name="specifics"></a>瞭解

> [!NOTE]
> 將登入新增至 web 應用程式，是關於保護 web 應用程式本身。 這項保護是藉由使用*中介軟體*程式庫來達成，而不是 Microsoft 驗證程式庫（MSAL）。 上述案例是[登入使用者的 Web 應用程式](scenario-web-app-sign-user-overview.md)，涵蓋該主題。
>
> 此案例涵蓋如何從 web 應用程式呼叫 web Api。 您必須取得這些 web Api 的存取權杖。 若要取得這些權杖，您可以使用 MSAL 程式庫來取得這些 token。

此案例的開發牽涉到這些特定工作：

- 在[應用程式註冊](scenario-web-app-call-api-app-registration.md)期間，您必須提供要與 Azure AD 共用的回復 URI、密碼或憑證。 如果您將應用程式部署到多個位置，則會為每個位置提供這項資訊。
- [應用程式](scenario-web-app-call-api-app-configuration.md)設定必須提供在應用程式註冊期間與 Azure AD 共用的用戶端認證。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [呼叫 web Api 的 web 應用程式：應用程式註冊](scenario-web-app-call-api-app-registration.md)
