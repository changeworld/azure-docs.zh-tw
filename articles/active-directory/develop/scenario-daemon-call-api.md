---
title: 從 daemon 應用程式呼叫 Web API-Microsoft 身分識別平臺 |Azure
description: 瞭解如何建立可呼叫 web Api 的 daemon 應用程式
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5a234e0b5d3a88b722257aa80249f4d80182d12f
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702227"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>呼叫 web Api 的 Daemon 應用程式-從應用程式呼叫 Web API

Daemon 應用程式可以從 .NET daemon 應用程式呼叫 Web API，或呼叫數個預先核准的 web Api。

## <a name="calling-a-web-api-daemon-application"></a>呼叫 Web API daemon 應用程式

以下說明如何使用權杖來呼叫 API：

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

# <a name="javatabjava"></a>[Java](#tab/java)

```Java
HttpURLConnection conn = (HttpURLConnection) url.openConnection();

// Set the appropriate header fields in the request header.
conn.setRequestProperty("Authorization", "Bearer " + accessToken);
conn.setRequestProperty("Accept", "application/json");

String response = HttpClientHelper.getResponseStringFromConn(conn);

int responseCode = conn.getResponseCode();
if(responseCode != HttpURLConnection.HTTP_OK) {
    throw new IOException(response);
}

JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
```

---

## <a name="calling-several-apis"></a>呼叫數個 Api

對於 daemon 應用程式，您呼叫的 web Api 必須經過預先核准。 Daemon 應用程式不會有任何累加式同意（不需要使用者互動）。 租使用者系統管理員必須預先同意應用程式和所有 API 許可權。 如果您想要呼叫數個 Api，每次呼叫 `AcquireTokenForClient`時，您都必須取得每個資源的權杖。 MSAL 會使用應用程式權杖快取來避免不必要的服務呼叫。

## <a name="next-steps"></a>後續步驟

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Daemon 應用程式-移至生產環境](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=dotnet)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Daemon 應用程式-移至生產環境](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=python)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Daemon 應用程式-移至生產環境](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=java)

---
