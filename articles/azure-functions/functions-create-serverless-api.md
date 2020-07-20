---
title: 在 Azure Functions 中自訂 HTTP 端點
description: 瞭解如何在 Azure Functions 中自訂 HTTP 觸發程式端點
author: mattchenderson
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: mahender
ms.custom: mvc
ms.openlocfilehash: 5607a737fa4616d4eda3d174144c1717125f4181
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "83122763"
---
# <a name="customize-an-http-endpoint-in-azure-functions"></a>在 Azure Functions 中自訂 HTTP 端點

在本文中，您將瞭解 Azure Functions 如何讓您建立可高度擴充的 Api。 Azure Functions 隨附內建 HTTP 觸發程式和系結的集合，可讓您輕鬆地以各種語言（包括 Node.js、c # 等）撰寫端點。 在本文中，您將自訂 HTTP 觸發程式來處理 API 設計中的特定動作。 您也會將它與 Azure Functions Proxy 和設定模擬 Api 的整合，來準備增加您的 API。 這些工作是在無伺服器計算環境的最上層完成，因此您不必擔心調整資源，只需專注于您的 API 邏輯即可。

## <a name="prerequisites"></a>必要條件 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

產生的函數將用於本文的其餘部分。

## <a name="sign-in-to-azure"></a>登入 Azure

使用您的 Azure 帳戶登入 [Azure 入口網站](https://portal.azure.com) 。

## <a name="customize-your-http-function"></a>自訂 HTTP 函式

根據預設，您的 HTTP 觸發程式函式會設定為接受任何 HTTP 方法。 您也可以使用預設 URL `http://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>` 。 在本節中，您會修改函式，只回應使用的 GET 要求 `/api/hello` 。 

1. 在 Azure 入口網站中瀏覽至您的函式。 選取左側功能表中的 [**整合**]，然後選取 [**觸發**程式] 底下的 **[HTTP （需求）** ]。

    :::image type="content" source="./media/functions-create-serverless-api/customizing-http.png" alt-text="自訂 HTTP 函式":::

1. 使用下表中所指定的 HTTP 觸發程式設定。

    | 欄位 | 範例值 | 描述 |
    |---|---|---|
    | 路由範本 | /hello | 決定使用什麼路由來叫用此函式 |
    | 授權層級 | 匿名 | 選擇性：讓您的函式不需要 API 金鑰即可存取 |
    | 選取的 HTTP 方法 | GET | 只允許使用選取的 HTTP 方法來叫用此函式 |

    您未 `/api` 在路由範本中包含基底路徑前置詞，因為它是由全域設定所處理。

1. 選取 [儲存]。

如需自訂 HTTP 函數的詳細資訊，請參閱[AZURE FUNCTIONS HTTP](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook)系結。

### <a name="test-your-api"></a>測試您的 API

接下來，測試您的函式，以查看它如何與新的 API 介面搭配運作：
1. 在 [函數] 頁面上，從左側功能表中選取 [程式**代碼 + 測試**]。

1. 從頂端功能表中選取 [取得函式**URL** ]，然後複製 URL。 確認它現在使用 `/api/hello` 路徑。
 
1. 將 URL 複製到新的瀏覽器索引標籤或您慣用的 REST 用戶端。 

   瀏覽器預設會使用 GET。
 
1. 將參數新增至 URL 中的查詢字串。 

   例如： `/api/hello/?name=John` 。
 
1. 按 Enter 鍵以確認其運作正常。 您應該會看到回應 "*Hello John*"。

1. 您也可以嘗試使用另一個 HTTP 方法呼叫端點，以確認不會執行該函式。 若要這麼做，請使用 REST 用戶端，例如捲曲、Postman 或 Fiddler。

## <a name="proxies-overview"></a>Proxy 概觀

在下一節中，您將會透過 proxy 呈現您的 API。 Azure Functions Proxy 可讓您將要求轉送至其他資源。 您可以定義 HTTP 端點，就像使用 HTTP 觸發程式一樣。 不過，您可以提供遠端執行的 URL，而不是在呼叫該端點時，撰寫要執行的程式碼。 這麼做可讓您將多個 API 來源組成單一 API 介面，這很容易讓用戶端取用，如果您想要將您的 API 建立為微服務，這會很有用。

Proxy 可以指向任何 HTTP 資源，例如︰
- Azure Functions 
- [Azure App Service](https://docs.microsoft.com/azure/app-service/overview) 中的 API 應用程式
- [Linux 上的 App Service](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro) 中的 Docker 容器
- 其他任何裝載 API

若要深入了解 Proxy，請參閱[使用 Azure Functions Proxy]。

## <a name="create-your-first-proxy"></a>建立您的第一個 Proxy

在本節中，您會建立新的 proxy，做為整體 API 的前端。 

### <a name="setting-up-the-frontend-environment"></a>設定前端環境

重複[建立函式應用程式](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function#create-a-function-app)的步驟建立新的函式應用程式，您將在其中建立您的 Proxy。 這個新應用程式的 URL 會作為我們 API 的前端，而您先前編輯的函式應用程式會作為後端。

1. 在入口網站中瀏覽至新的前端函式應用程式。
1. 選取 [平台功能]****，然後選擇 [應用程式設定]****。
1. 向下卷到 [**應用程式設定**]，其中儲存索引鍵/值組，並使用索引鍵建立新的設定 `HELLO_HOST` 。 將值設定為後端函式應用程式的主機，例如 `<YourBackendApp>.azurewebsites.net`。 這個值是您稍早在測試 HTTP 函數時複製的 URL 的一部分。 稍後，您將在設定中參考這項設定。

    > [!NOTE] 
    > 建議使用應用程式設定作為主機設定，以避免 Proxy 依賴硬式編碼的環境。 使用應用程式設定表示您可以在不同環境之間移動 Proxy 設定，將會套用環境特定的應用程式設定。

1. 選取 [儲存]。

### <a name="creating-a-proxy-on-the-frontend"></a>在前端建立 Proxy

1. 在入口網站中流覽回到您的前端函式應用程式。

1. 在左側功能表中 **，選取 [** proxy]，然後選取 [**新增**]。 

1. 在 [**新增 Proxy** ] 頁面上，使用下表中的設定，然後選取 [**建立**]。

    | 欄位 | 範例值 | 描述 |
    |---|---|---|
    | Name | HelloProxy | 僅用於管理的易記名稱 |
    | 路由範本 | /api/remotehello | 決定使用什麼路由來叫用此 Proxy |
    | 後端 URL | https://%HELLO_HOST%/api/hello | 指定端點，而其要求應該透過代理 |

    
    :::image type="content" source="./media/functions-create-serverless-api/creating-proxy.png" alt-text="建立 Proxy":::

    Azure Functions Proxy 不提供 `/api` 基底路徑前置詞，其必須包含在路由範本中。 `%HELLO_HOST%`語法會參考您稍早建立的應用程式設定。 解析後的 URL 會指向您的原始函式。

1. 複製 proxy URL，並在瀏覽器中或使用您最愛的 HTTP 用戶端進行測試，以試用新的 proxy：
    - 若為匿名函式，請使用： `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?name="Proxies"` 。
    - 若為具有授權的函式，請使用： `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?code=YOURCODE&name="Proxies"` 。

## <a name="create-a-mock-api"></a>建立模擬 API

接下來，您將使用 proxy 來為您的解決方案建立 mock API。 此 proxy 可讓用戶端開發進行，而不需要完全實作為後端。 稍後在開發期間，您可以建立新的函式應用程式，以支援此邏輯，並將您的 proxy 重新導向至它。

為了建立此模擬 API，我們將建立新的 proxy，這次使用[App Service 編輯器](https://github.com/projectkudu/kudu/wiki/App-Service-Editor)。 若要開始，請在入口網站中瀏覽至您的函式應用程式。 選取 [**平臺功能**]，然後在 [**開發工具**] 下尋找**App Service 編輯器**]。 App Service 編輯器會在新的索引標籤中開啟。

在左側導覽中，選取 `proxies.json`。 這個檔案會儲存所有 proxy 的設定。 如果您使用其中一個函式[部署方法](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment)，您可以在原始檔控制中維護這個檔案。 若要深入了解此檔案，請參閱 [Proxy 進階組態](https://docs.microsoft.com/azure/azure-functions/functions-proxies#advanced-configuration)。

如果您到目前為止已遵循，則您的 proxies.js應該如下所示：

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/remotehello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        }
    }
}
```

接下來，您將新增 mock API。 使用下列程式碼取代檔案上的 proxies.js：

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/remotehello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        },
        "GetUserByName" : {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/users/{username}"
            },
            "responseOverrides": {
                "response.statusCode": "200",
                "response.headers.Content-Type" : "application/json",
                "response.body": {
                    "name": "{username}",
                    "description": "Awesome developer and master of serverless APIs",
                    "skills": [
                        "Serverless",
                        "APIs",
                        "Azure",
                        "Cloud"
                    ]
                }
            }
        }
    }
}
```

此程式碼 `GetUserByName` 會新增不含屬性的新 proxy `backendUri` 。 它會使用回應覆寫修改 Proxy 的預設回應，而不是呼叫另一個資源。 要求和回應覆寫也可以搭配後端 URL 一起使用。 當 proxy 處理舊版系統時，這項技術特別有用，因為您可能需要修改標頭、查詢參數等等。 若要深入了解要求和回應覆寫，請參閱[在 Proxy 中修改要求和回應](https://docs.microsoft.com/azure/azure-functions/functions-proxies)。

使用瀏覽器或您最愛的 REST 用戶端呼叫 `<YourProxyApp>.azurewebsites.net/api/users/{username}` 端點，以測試您的模擬 API。 請務必以代表使用者名稱的字串值取代 _{username}_。

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何在 Azure Functions 上建立和自訂 API。 您也學到如何將多個 API (包括模擬) 組合成統一的 API 介面。 不論多麼複雜的 API 都可以使用這些技術來建置，而且都在 Azure Functions 提供的無伺服器計算模型上執行。

進一步開發您的 API 時，下列參考可能很有幫助︰

- [Azure Functions HTTP 繫結](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook)
- [使用 Azure Functions Proxy]
- [定義 Azure Functions API (預覽)](https://docs.microsoft.com/azure/azure-functions/functions-api-definition-getting-started)


[Create your first function]: https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function
[使用 Azure Functions Proxy]: https://docs.microsoft.com/azure/azure-functions/functions-proxies
