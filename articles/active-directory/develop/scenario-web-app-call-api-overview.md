---
title: 建立可呼叫 web Api 的 web 應用程式-Microsoft 身分識別平臺 |蔚藍
description: '瞭解如何建立 web 應用程式，以呼叫 web Api (總覽) '
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 174db339079de91a87cef9551bc79e6f03701605
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91257124"
---
# <a name="scenario-a-web-app-that-calls-web-apis"></a>案例：呼叫 web Api 的 web 應用程式

瞭解如何建立 web 應用程式，以將使用者登入 Microsoft 身分識別平臺，然後代表已登入的使用者呼叫 web Api。

## <a name="prerequisites"></a>Prerequisites

此案例假設您已完成 [案例：登入使用者的 Web 應用程式](scenario-web-app-sign-user-overview.md)。

## <a name="overview"></a>概觀

您會將驗證新增至您的 web 應用程式，讓它可以將使用者登入，並代表已登入的使用者呼叫 web API。

![呼叫 Web API 的 Web 應用程式](./media/scenario-webapp/web-app.svg)

呼叫 web Api 的 web 應用程式是機密用戶端應用程式。
這就是為什麼他們將秘密 (應用程式密碼或憑證) Azure Active Directory (Azure AD) 。 此密碼會在呼叫 Azure AD 期間傳入，以取得權杖。

## <a name="specifics"></a>特性

> [!NOTE]
> 將登入新增至 web 應用程式是關於保護 web 應用程式本身。 使用 *中介軟體* 程式庫，而不是 Microsoft 驗證程式庫 (MSAL) 來達成該保護。 上述案例是 [登入使用者的 Web 應用程式](scenario-web-app-sign-user-overview.md)，涵蓋該主題。
>
> 此案例涵蓋如何從 web 應用程式呼叫 web Api。 您必須取得這些 web Api 的存取權杖。 您可以使用 MSAL 程式庫來取得這些權杖。

此案例的開發包含下列特定工作：

- 在 [應用程式註冊](scenario-web-app-call-api-app-registration.md)期間，您必須提供要與 Azure AD 共用的回復 URI、秘密或憑證。 如果您將應用程式部署到多個位置，則會提供每個位置的回復 URI。
- [應用程式](scenario-web-app-call-api-app-configuration.md)設定必須提供在應用程式註冊期間與 Azure AD 共用的用戶端認證。

## <a name="recommended-reading"></a>建議閱讀資料

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [呼叫 web Api 的 web 應用程式：應用程式註冊](scenario-web-app-call-api-app-registration.md)
