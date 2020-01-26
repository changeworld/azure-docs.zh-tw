---
title: 註冊 web 應用程式以呼叫 web Api-Microsoft 身分識別平臺 |Azure
description: 瞭解如何註冊會呼叫 web Api 的 web 應用程式
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
ms.openlocfilehash: 5a57fcef3569734964bf6e8a41faa49800798f9b
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759052"
---
# <a name="a-web-app-that-calls-web-apis-app-registration"></a>呼叫 web Api 的 web 應用程式：應用程式註冊

呼叫 web Api 的 web 應用程式與登入使用者的 web 應用程式具有相同的註冊。 因此，請依照[登入使用者：應用程式註冊的 web 應用程式](scenario-web-app-sign-user-app-registration.md)中的指示進行。

不過，由於 web 應用程式現在也會呼叫 web Api，因此它會成為機密用戶端應用程式。 這就是為什麼需要額外註冊的原因。 應用程式必須與 Microsoft 身分識別平臺共用用戶端認證或*秘密*。

[!INCLUDE [Registration of client secrets](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API 權限

Web apps 代表已登入的使用者呼叫 Api。 若要這麼做，他們必須要求*委派的許可權*。 如需詳細資訊，請參閱[新增存取 Web api 的許可權](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [呼叫 web Api 的 web 應用程式：程式碼設定](scenario-web-app-call-api-app-configuration.md)
