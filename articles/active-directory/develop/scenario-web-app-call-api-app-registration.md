---
title: 註冊調用 Web API 的 Web 應用 - 微軟身份平臺 |蔚藍
description: 瞭解如何註冊調用 Web API 的 Web 應用
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759052"
---
# <a name="a-web-app-that-calls-web-apis-app-registration"></a>調用 Web API 的 Web 應用：應用註冊

調用 Web API 的 Web 應用與在使用者中簽名的 Web 應用具有相同的註冊。 因此，請按照[在使用者中簽名的 Web 應用中的說明進行操作：應用註冊](scenario-web-app-sign-user-app-registration.md)。

但是，由於 Web 應用現在也調用 Web API，因此它成為機密用戶端應用程式。 這就是為什麼需要一些額外的註冊。 應用程式必須與 Microsoft 標識平臺共用用戶端憑據或*機密*。

[!INCLUDE [Registration of client secrets](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API 權限

Web 應用代表登錄使用者調用 API。 為此，他們必須請求*委派的許可權*。 有關詳細資訊，請參閱[添加訪問 Web API 的許可權](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [調用 Web API 的 Web 應用：代碼配置](scenario-web-app-call-api-app-configuration.md)
