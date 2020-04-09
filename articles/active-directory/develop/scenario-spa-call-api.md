---
title: 構建調用 Web API 的單頁應用 - 微軟身份平臺 |蔚藍
description: 瞭解如何建構 Web API 的單頁應用程式
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/06/2019
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: 5b70b109f43e80fc3ec68f52aef2dba6823033bb
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882126"
---
# <a name="single-page-application-call-a-web-api"></a>單頁應用程式:呼叫 Web API

我們建議您在調用`acquireTokenSilent`Web API 之前呼叫方法以獲取或續訂存取權杖。 獲得權杖後,可以調用受保護的 Web API。

## <a name="call-a-web-api"></a>呼叫 Web API

# <a name="javascript"></a>[JavaScript](#tab/javascript)

在 HTTP 請求中使用獲取的存取權杖作為承載器呼叫任何 Web API,如 Microsoft 圖形 API。 例如：

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

MSAL 角包裝器利用 HTTP 攔截器自動以靜默方式獲取訪問權杖,並將它們附加到 API 的 HTTP 請求。 有關詳細資訊,請參閱[獲取權杖以呼叫 API](scenario-spa-acquire-token.md)。

---

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [移動至生產環境](scenario-spa-production.md)
