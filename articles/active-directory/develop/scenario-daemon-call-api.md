---
title: 從守護程序應用調用 Web API - 微軟身份平臺 |蔚藍
description: 瞭解如何建構 Web API 的守護程式應用程式
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
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885441"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>呼叫 Web API 的守護程式應用程式 - 從應用呼叫 Web API

.NET 守護程序應用可以調用 Web API。 .NET 守護程序應用還可以調用多個預先批准的 Web API。

## <a name="calling-a-web-api-from-a-daemon-application"></a>從守護程式應用程式呼叫 Web API

以下是如何使用權碼呼叫 API:

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

## <a name="calling-several-apis"></a>呼叫多個 API

對於守護程序應用,您調用的 Web API 需要預先批准。 守護程序應用沒有增量同意。 (沒有使用者交互。租戶管理員需要提前為應用程式和所有 API 許可權提供同意。 如果要呼叫多個 API,則需要為每個資源取得一個權杖,每次呼`AcquireTokenForClient`叫 。 MSAL 將使用應用程式令牌緩存來避免不必要的服務調用。

## <a name="next-steps"></a>後續步驟

# <a name="net"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [守護程式應用程式 - 移動到生產](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [守護程式應用程式 - 移動到生產](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [守護程式應用程式 - 移動到生產](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=java)

---
