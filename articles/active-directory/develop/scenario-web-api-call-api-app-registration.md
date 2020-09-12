---
title: 註冊可呼叫 web Api 的 web API-Microsoft 身分識別平臺 |蔚藍
description: 瞭解如何建立 web API，以呼叫 (應用程式註冊) 的下游 web Api。
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
ms.openlocfilehash: c0b009d6d53a117aa11d0629fb649b2dd55559af
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2020
ms.locfileid: "89438189"
---
# <a name="a-web-api-that-calls-web-apis-app-registration"></a>呼叫 web Api 的 web API：應用程式註冊

呼叫下游 web Api 的 web API 與受保護的 web API 具有相同的註冊。 因此，您必須依照 [受保護的 WEB API：應用程式註冊](scenario-protected-web-api-app-registration.md)中的指示進行。

由於 web 應用程式現在會呼叫 web Api，因此它會成為機密用戶端應用程式。 這就是為什麼需要額外的註冊資訊：應用程式需要與 Microsoft 身分識別平臺) 共用 (用戶端認證的秘密。

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API 權限

Web apps 會代表已收到持有人權杖的使用者呼叫 Api。 Web 應用程式需要要求委派的許可權。 如需詳細資訊，請參閱 [新增存取 WEB API 的許可權](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)。

## <a name="next-steps"></a>接下來的步驟

> [!div class="nextstepaction"]
> [呼叫 web Api 的 web API：程式碼設定](scenario-web-api-call-api-app-configuration.md)
