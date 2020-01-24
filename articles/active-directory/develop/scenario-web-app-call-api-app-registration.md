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
ms.openlocfilehash: bcb4fa5eb5889698174b65eb576c9caf0c82f857
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701666"
---
# <a name="web-app-that-calls-web-apis---app-registration"></a>呼叫 web Api 的 web 應用程式-應用程式註冊

呼叫 web Api 的 Web 應用程式與登入使用者的 Web 應用程式具有相同的註冊。 因此，您需要遵循[Web 應用程式中登入使用者-應用程式註冊](scenario-web-app-sign-user-app-registration.md)的指示

不過，由於 Web 應用程式現在會呼叫 web Api，因此它會成為機密用戶端應用程式。 這就是為什麼需要額外的註冊：它需要與 Microsoft 身分識別平臺共用秘密（用戶端認證）。

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API 權限

Web 應用程式代表已登入的使用者呼叫 Api。 他們需要要求委派的許可權。 如需詳細資訊，請參閱[新增存取 Web api 的許可權](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式的程式碼設定](scenario-web-app-call-api-app-configuration.md)
