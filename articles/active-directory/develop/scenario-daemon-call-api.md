---
title: 從 daemon 應用程式呼叫 Web API-Microsoft 身分識別平臺 |Azure
description: 瞭解如何建立可呼叫 web Api 的 daemon 應用程式
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
ms.openlocfilehash: e440628526dada7655cc71f63fc9fff006cc5ef5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80885441"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>呼叫 web Api 的 Daemon 應用程式-從應用程式呼叫 Web API

.NET daemon 應用程式可以呼叫 Web API。 .NET daemon 應用程式也可以呼叫數個預先核准的 web Api。

## <a name="calling-a-web-api-from-a-daemon-application"></a>從 daemon 應用程式呼叫 Web API

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

對於 daemon 應用程式，您呼叫的 web Api 必須經過預先核准。 沒有 daemon 應用程式的增量同意。 （沒有使用者互動）。租使用者系統管理員必須事先為應用程式和所有 API 許可權提供同意。 如果您想要呼叫數個 Api，每次呼叫`AcquireTokenForClient`時，您都需要取得每個資源的權杖。 MSAL 會使用應用程式權杖快取來避免不必要的服務呼叫。

## <a name="next-steps"></a>後續步驟

# <a name="net"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Daemon 應用程式-移至生產環境](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Daemon 應用程式-移至生產環境](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Daemon 應用程式-移至生產環境](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=java)

---
