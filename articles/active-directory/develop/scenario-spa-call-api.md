---
title: 建立單一頁面應用程式，以呼叫 Web API-Microsoft 身分識別平臺 |Azure
description: 瞭解如何建立單一頁面應用程式，以呼叫 Web API
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80882126"
---
# <a name="single-page-application-call-a-web-api"></a>單一頁面應用程式：呼叫 Web API

我們建議您在 `acquireTokenSilent` 呼叫 Web API 之前，先呼叫方法來取得或更新存取權杖。 有了權杖之後，您就可以呼叫受保護的 Web API。

## <a name="call-a-web-api"></a>呼叫 Web API

# <a name="javascript"></a>[JavaScript](#tab/javascript)

使用取得的存取權杖做為 HTTP 要求中的持有人，以呼叫任何 Web API，例如 Microsoft Graph API。 例如：

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

MSAL 角度包裝函式會利用 HTTP 攔截器，以無訊息方式自動取得存取權杖，並將它們附加至 Api 的 HTTP 要求。 如需詳細資訊，請參閱[取得權杖以呼叫 API](scenario-spa-acquire-token.md)。

---

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [移至生產環境](scenario-spa-production.md)
