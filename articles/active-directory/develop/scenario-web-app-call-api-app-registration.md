---
title: 註冊 web 應用程式以呼叫 web Api-Microsoft 身分識別平臺 |蔚藍
description: 瞭解如何註冊 web 應用程式以呼叫 web Api
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f94a3da96243e30faa90277ce86efec037f54672
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89436456"
---
# <a name="a-web-app-that-calls-web-apis-app-registration"></a>呼叫 web Api 的 web 應用程式：應用程式註冊

呼叫 web Api 的 web 應用程式，與登入使用者的 web 應用程式具有相同的註冊。 因此，請依照 [登入使用者的 web 應用程式中的指示進行：應用程式註冊](scenario-web-app-sign-user-app-registration.md)。

不過，由於 web 應用程式現在也會呼叫 web Api，因此它會成為機密用戶端應用程式。 這就是為什麼需要進行額外的註冊。 應用程式必須與 Microsoft 身分識別平臺共用用戶端認證或 *秘密*。

[!INCLUDE [Registration of client secrets](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API 權限

Web apps 會代表已登入的使用者呼叫 Api。 若要這樣做，他們必須要求 *委派的許可權*。 如需詳細資訊，請參閱 [新增存取 WEB API 的許可權](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [呼叫 Web API 的 Web 應用程式：程式碼設定](scenario-web-app-call-api-app-configuration.md)
