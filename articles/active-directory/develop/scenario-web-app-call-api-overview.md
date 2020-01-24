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
ms.openlocfilehash: 9e123195205bb0eb88f0edd4e2dff2e0da9d84ce
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701649"
---
# <a name="scenario-web-app-that-calls-web-apis"></a>案例：呼叫 web Api 的 Web 應用程式

瞭解如何建立 web 應用程式，以在 Microsoft 身分識別平臺上登入使用者，然後代表已登入的使用者呼叫 web Api。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

此案例 supposes 您已經歷過下列案例：

> [!div class="nextstepaction"]
> [登入使用者的 Web 應用程式](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>概觀

您會將驗證新增至您的 Web 應用程式，如此可讓使用者登入，並代表登入的使用者呼叫 Web API。

![呼叫 Web API 的 Web 應用程式](./media/scenario-webapp/web-app.svg)

呼叫 web Api 的 Web Apps：

- 是機密用戶端應用程式。
- 這就是他們使用 Azure AD 來註冊密碼（應用程式密碼或憑證）的原因。 此密碼會在呼叫 Azure AD 時傳入，以取得權杖

## <a name="specifics"></a>瞭解

> [!NOTE]
> 將登入新增至 Web 應用程式並不會使用 MSAL 程式庫，因為這是關於保護 Web 應用程式。 保護程式庫的方式是透過名為中介軟體的程式庫來達成。 這是先前案例登[入 Web 應用程式之使用者](scenario-web-app-sign-user-overview.md)的物件
>
> 從 Web 應用程式呼叫 web Api 時，您將需要取得這些 web Api 的存取權杖。 您可以使用 MSAL 程式庫來取得這些權杖。

在此案例中，開發人員的端對端體驗具有下列各方面的特定層面：

- 在[應用程式註冊](scenario-web-app-call-api-app-registration.md)期間，您必須提供一或多個（如果您將應用程式部署至數個位置）回復 uri、密碼或憑證需要與 Azure AD 共用。
- [應用程式](scenario-web-app-call-api-app-configuration.md)設定必須提供用戶端認證，以便在應用程式註冊期間與 Azure AD 共用

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式註冊](scenario-web-app-call-api-app-registration.md)
