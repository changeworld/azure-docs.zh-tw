---
title: 從 daemon 應用程式呼叫 web API-Microsoft 身分識別平臺 |蔚藍
description: 瞭解如何建立可呼叫 web API 的 daemon 應用程式。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 576eaf2ad9350651e4400d980e6fedce236dfa57
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91257600"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>呼叫 web Api 的 Daemon 應用程式-從應用程式呼叫 web API

.NET daemon 應用程式可以呼叫 web API。 .NET daemon 應用程式也可以呼叫數個預先核准的 web Api。

## <a name="calling-a-web-api-from-a-daemon-application"></a>從 daemon 應用程式呼叫 web API

以下說明如何使用權杖來呼叫 API：

# <a name="net"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

# <a name="python"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

# <a name="java"></a>[Java](#tab/java)

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

針對背景程式應用程式，您呼叫的 web Api 需要預先核准。 背景程式應用程式不會有累加式同意。  (沒有使用者互動。 ) 租使用者系統管理員必須為應用程式和所有 API 許可權事先提供同意。 如果您想要呼叫數個 Api，則每次呼叫時都需要取得每個資源的權杖 `AcquireTokenForClient` 。 MSAL 會使用應用程式權杖快取來避免不必要的服務呼叫。

## <a name="next-steps"></a>後續步驟

# <a name="net"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Daemon 應用程式-移至生產環境](./scenario-daemon-production.md?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Daemon 應用程式-移至生產環境](./scenario-daemon-production.md?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Daemon 應用程式-移至生產環境](./scenario-daemon-production.md?tabs=java)

---
