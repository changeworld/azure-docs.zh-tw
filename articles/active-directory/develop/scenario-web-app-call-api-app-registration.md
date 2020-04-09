---
title: 註冊調用 Web API 的 Web 應用 - 微軟身份平臺 |蔚藍
description: 瞭解如何註冊呼叫 Web API 的 Web 應用
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
ms.openlocfilehash: 8cb7d86bd419563363779c499962c81f0c59e3b6
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881871"
---
# <a name="a-web-app-that-calls-web-apis-app-registration"></a>呼叫 Web API 的 Web 應用:應用程式註冊

調用 Web API 的 Web 應用與在使用者中簽名的 Web 應用具有相同的註冊。 因此,請按照[在使用者中簽名的 Web 應用中的說明進行操作:應用註冊](scenario-web-app-sign-user-app-registration.md)。

但是,由於 Web 應用現在也調用 Web API,因此它成為機密用戶端應用程式。 這就是為什麼需要一些額外的註冊。 應用程式必須與 Microsoft 識別平台共用用戶端認證或*機密*。

[!INCLUDE [Registration of client secrets](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API 權限

Web 應用代表登錄使用者調用 API。 為此,他們必須請求*委派的許可權*。 有關詳細資訊,請參閱[新增存取 Web API 的權限](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [呼叫 Web API 的 Web 應用:程式碼設定](scenario-web-app-call-api-app-configuration.md)
