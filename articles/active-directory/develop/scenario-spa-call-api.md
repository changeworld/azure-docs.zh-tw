---
title: 建立單一頁面應用程式，以呼叫 Web API-Microsoft 身分識別平臺 |Azure
description: 瞭解如何建立單一頁面應用程式，以呼叫 Web API
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
ms.openlocfilehash: f3cafb4d2df4d1944f583a99ca2014f3841827d0
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701853"
---
# <a name="single-page-application-call-a-web-api"></a>單一頁面應用程式：呼叫 Web API

我們建議您在呼叫 Web API 之前，先呼叫 `acquireTokenSilent` 方法來取得或更新存取權杖。 有了權杖之後，您就可以呼叫受保護的 Web API。

## <a name="call-a-web-api"></a>呼叫 Web API

### <a name="javascript"></a>JavaScript

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

### <a name="angular"></a>Angular

MSAL 角度包裝函式會利用 HTTP 攔截器，以無訊息方式自動取得存取權杖，並將它們附加至 Api 的 HTTP 要求。 如需詳細資訊，請參閱[取得權杖以呼叫 API](scenario-spa-acquire-token.md)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [移至生產環境](scenario-spa-production.md)
