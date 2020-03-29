---
title: 從守護程式應用調用 Web API - 微軟身份平臺 |蔚藍
description: 瞭解如何構建調用 Web API 的守護進程應用
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
ms.openlocfilehash: 338b638d6b33bcbbb5cf377643a96c71b0d314bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76775200"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>調用 Web API 的守護程式應用 - 從應用調用 Web API

.NET 守護進程應用可以調用 Web API。 .NET 守護進程應用還可以調用多個預先批准的 Web API。

## <a name="calling-a-web-api-from-a-daemon-application"></a>從守護進程應用程式調用 Web API

以下是如何使用權杖調用 API：

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

# <a name="java"></a>[JAVA](#tab/java)

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

## <a name="calling-several-apis"></a>調用多個 API

對於守護進程應用，您調用的 Web API 需要預先批准。 守護進程應用沒有增量同意。 （沒有使用者交互。租戶管理員需要提前為應用程式和所有 API 許可權提供同意。 如果要調用多個 API，則需要為每個資源獲取一個權杖，每次調用`AcquireTokenForClient`。 MSAL 將使用應用程式權杖緩存來避免不必要的服務調用。

## <a name="next-steps"></a>後續步驟

# <a name="net"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [守護進程應用 - 移動到生產](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [守護進程應用 - 移動到生產](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=python)

# <a name="java"></a>[JAVA](#tab/java)

> [!div class="nextstepaction"]
> [守護進程應用 - 移動到生產](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=java)

---
