---
title: 註冊調用 Web API 的 Web API - 微軟身份平臺 |蔚藍
description: 瞭解如何建構調用下游 Web API(應用註冊)的 Web API。
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
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885118"
---
# <a name="a-web-api-that-calls-web-apis-app-registration"></a>呼叫 Web API 的 Web API:應用程式註冊

調用下游 Web API 的 Web API 與受保護的 Web API 具有相同的註冊。 因此,您需要按照受保護的 Web API[中的說明:應用程式註冊](scenario-protected-web-api-app-registration.md)。

由於 Web 應用現在調用 Web API,因此它成為機密用戶端應用程式。 這就是為什麼需要額外的註冊資訊:應用需要與 Microsoft 標識平台共用機密(用戶端認證)。

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API 權限

Web 應用代表收到無記名令牌的用戶調用 API。 Web 應用需要請求委派的許可權。 有關詳細資訊,請參閱[新增存取 Web API 的權限](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [呼叫 Web API 的 Web API:代碼設定](scenario-web-api-call-api-app-configuration.md)
