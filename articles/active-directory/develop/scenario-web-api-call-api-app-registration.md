---
title: 註冊調用 Web API 的 Web API - 微軟身份平臺 |蔚藍
description: 瞭解如何構建調用下游 Web API（應用註冊）的 Web API。
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
ms.openlocfilehash: bafd71f34602535bb6193a8d8114a1182e4e8f40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701785"
---
# <a name="a-web-api-that-calls-web-apis-app-registration"></a>調用 Web API 的 Web API：應用註冊

調用下游 Web API 的 Web API 與受保護的 Web API 具有相同的註冊。 因此，您需要按照受保護的 Web API[中的說明：應用註冊](scenario-protected-web-api-app-registration.md)。

由於 Web 應用現在調用 Web API，因此它成為機密用戶端應用程式。 這就是為什麼需要額外的註冊資訊：應用需要與 Microsoft 標識平臺共用機密（用戶端憑據）。

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API 權限

Web 應用代表收到無記名權杖的使用者調用 API。 Web 應用需要請求委派的許可權。 有關詳細資訊，請參閱[添加訪問 Web API 的許可權](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [調用 Web API 的 Web API：代碼配置](scenario-web-api-call-api-app-configuration.md)
