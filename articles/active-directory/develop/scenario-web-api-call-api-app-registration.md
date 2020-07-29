---
title: 註冊會呼叫 web Api 的 Web API-Microsoft 身分識別平臺 |Azure
description: 瞭解如何建立呼叫下游 web Api 的 Web API （應用程式註冊）。
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
ms.openlocfilehash: 048f7d41bd9d106121859e6b1fc013258067af9c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80885118"
---
# <a name="a-web-api-that-calls-web-apis-app-registration"></a>呼叫 web Api 的 Web API：應用程式註冊

呼叫下游 web Api 的 Web API 與受保護的 Web API 具有相同的註冊。 因此，您必須依照[受保護 Web API：應用程式註冊](scenario-protected-web-api-app-registration.md)中的指示進行。

由於 web 應用程式現在會呼叫 web Api，因此它會成為機密用戶端應用程式。 這就是為什麼需要額外的註冊資訊：應用程式需要與 Microsoft 身分識別平臺共用秘密（用戶端認證）。

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API 權限

Web apps 會代表收到持有人權杖的使用者呼叫 Api。 Web apps 需要要求委派的許可權。 如需詳細資訊，請參閱[新增存取 Web API 的許可權](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [呼叫 web Api 的 Web API：程式碼設定](scenario-web-api-call-api-app-configuration.md)
