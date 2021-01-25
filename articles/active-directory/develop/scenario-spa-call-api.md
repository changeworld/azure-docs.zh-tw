---
title: 建立單一頁面應用程式呼叫 web API
titleSuffix: Microsoft identity platform
description: 瞭解如何建立可呼叫 web API 的單一頁面應用程式
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
ms.openlocfilehash: 4dd4a40233fc1c030581fd1ae2827061435a0ab3
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98753549"
---
# <a name="single-page-application-call-a-web-api"></a>單一頁面應用程式：呼叫 web API

建議您 `acquireTokenSilent` 先呼叫方法來取得或更新存取權杖，然後再呼叫 WEB API。 擁有權杖之後，您可以呼叫受保護的 web API。

## <a name="call-a-web-api"></a>呼叫 Web API

# <a name="javascript"></a>[JavaScript](#tab/javascript)

使用取得的存取權杖做為 HTTP 要求中的持有人，以呼叫任何 web API，例如 Microsoft Graph API。 例如：

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

MSAL 角度包裝函式會利用 HTTP 攔截器，以無訊息方式自動取得存取權杖，並將它們附加至 Api 的 HTTP 要求。 如需詳細資訊，請參閱 [取得權杖以呼叫 API](scenario-spa-acquire-token.md)。

---

## <a name="next-steps"></a>後續步驟

移至本案例的下一篇文章， [移至生產環境](scenario-spa-production.md)。