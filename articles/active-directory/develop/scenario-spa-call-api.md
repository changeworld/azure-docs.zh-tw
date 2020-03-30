---
title: 構建調用 Web API 的單頁應用 - 微軟身份平臺 |蔚藍
description: 瞭解如何構建調用 Web API 的單頁應用程式
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: ccece6c840033913ec6d96b446dcb98c4befb32f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159993"
---
# <a name="single-page-application-call-a-web-api"></a>單頁應用程式：調用 Web API

我們建議您在調用 Web `acquireTokenSilent` API 之前調用 方法以獲取或續訂訪問權杖。 獲得權杖後，可以調用受保護的 Web API。

## <a name="call-a-web-api"></a>呼叫 Web API

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

在 HTTP 要求中使用獲取的訪問權杖作為承載器調用任何 Web API，如 Microsoft 圖形 API。 例如：

```javascript
    var headers = new Headers();
    var bearer = "Bearer " + access_token;
    headers.append("Authorization", bearer);
    var options = {
         method: "GET",
         headers: headers
    };
    var graphEndpoint = "https://graph.microsoft.com/v1.0/me";

    fetch(graphEndpoint, options)
        .then(function (response) {
             //do something with response
        }
```

# <a name="angular"></a>[Angular](#tab/angular)

MSAL 角包裝器利用 HTTP 攔截器自動以靜默方式獲取訪問權杖，並將它們附加到 API 的 HTTP 要求。 有關詳細資訊，請參閱[獲取權杖以調用 API](scenario-spa-acquire-token.md)。

---

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [移動至生產環境](scenario-spa-production.md)
