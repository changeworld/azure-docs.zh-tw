---
title: 在 Azure Functions 中自訂 HTTP 端點
description: 瞭解如何在 Azure Functions 中自訂 HTTP 觸發程式端點
author: mattchenderson
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: mahender
ms.custom: mvc
ms.openlocfilehash: 440eb1f39284f8d99a8d6b9067b018c4a54fcd27
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87083016"
---
# <a name="customize-an-http-endpoint-in-azure-functions"></a>在 Azure Functions 中自訂 HTTP 端點

在本文中，您將瞭解 Azure Functions 如何讓您建立可高度擴充的 Api。 Azure Functions 隨附內建 HTTP 觸發程式和系結的集合，可讓您以各種語言輕鬆撰寫端點，包括 Node.js、c # 等。 在本文中，您將自訂 HTTP 觸發程式來處理 API 設計中的特定動作。 您也將藉由與 Azure Functions Proxy 整合，並設定模擬 Api，為您的 API 做好準備。 這些工作是在函式無伺服器計算環境的最上層完成，因此您不需要擔心調整資源-您可以只專注于您的 API 邏輯。

## <a name="prerequisites"></a>Prerequisites 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

產生的函數將用於本文的其餘部分。

## <a name="sign-in-to-azure"></a>登入 Azure

使用您的 Azure 帳戶登入 [Azure 入口網站](https://portal.azure.com) 。

## <a name="customize-your-http-function"></a>自訂 HTTP 函式

根據預設，您的 HTTP 觸發程式函數會設定為接受任何 HTTP 方法。 您也可以使用預設的 URL `http://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>` 。 在本節中，您會將函數修改為只回應 GET 要求 `/api/hello` 。 

1. 在 Azure 入口網站中瀏覽至您的函式。 在左側功能表中選取 [**整合**]，然後選取 [**觸發**程式] 下的 [ **HTTP (要求) ** 。

    :::image type="content" source="./media/functions-create-serverless-api/customizing-http.png" alt-text="自訂 HTTP 函式":::

1. 使用下表中指定的 HTTP 觸發程式設定。

    | 欄位 | 範例值 | 描述 |
    |---|---|---|
    | 路由範本 | /hello | 決定使用什麼路由來叫用此函式 |
    | 授權層級 | 匿名 | 選擇性：讓您的函式不需要 API 金鑰即可存取 |
    | 選取的 HTTP 方法 | GET | 只允許使用選取的 HTTP 方法來叫用此函式 |

    您未 `/api` 在路由範本中包含基底路徑前置詞，因為它是由全域設定所處理。

1. 選取 [儲存]****。

如需自訂 HTTP 函式的詳細資訊，請參閱 [AZURE FUNCTIONS HTTP](./functions-bindings-http-webhook.md)系結。

### <a name="test-your-api"></a>測試您的 API

接下來，測試您的函式，以查看它如何搭配新的 API 介面運作：
1. 在 [函數] 頁面的左側功能表中，選取 [程式 **代碼 + 測試** ]。

1. 從頂端功能表選取 [取得函式 **URL** ]，並複製 URL。 確認它現在使用該 `/api/hello` 路徑。
 
1. 將 URL 複製到新的瀏覽器索引標籤或您慣用的 REST 用戶端。 

   依預設，瀏覽器會使用 GET。
 
1. 將參數加入至 URL 中的查詢字串。 

   例如： `/api/hello/?name=John` 。
 
1. 按 Enter 以確認其正常運作。 您應該會看到回應 "*Hello John*"。

1. 您也可以嘗試以另一個 HTTP 方法呼叫端點，以確認函式不會執行。 若要這樣做，請使用 REST 用戶端，例如捲曲、Postman 或 Fiddler。

## <a name="proxies-overview"></a>Proxy 概觀

在下一節中，您將透過 proxy 呈現您的 API。 Azure Functions Proxy 可讓您將要求轉送至其他資源。 您可以像使用 HTTP 觸發程式一樣定義 HTTP 端點。 但是，您可以提供遠端執行的 URL，而不是在呼叫該端點時撰寫程式碼來執行。 這樣做可讓您將多個 API 來源組合成單一 API 介面，讓用戶端很容易取用，如果您想要將 API 建立為微服務，這會很有用。

Proxy 可以指向任何 HTTP 資源，例如︰
- Azure Functions 
- [Azure App Service](../app-service/overview.md) 中的 API 應用程式
- [Linux 上的 App Service](../app-service/containers/app-service-linux-intro.md) 中的 Docker 容器
- 其他任何裝載 API

若要深入了解 Proxy，請參閱[使用 Azure Functions Proxy]。

## <a name="create-your-first-proxy"></a>建立您的第一個 Proxy

在本節中，您會建立新的 proxy，作為整體 API 的前端。 

### <a name="setting-up-the-frontend-environment"></a>設定前端環境

重複[建立函式應用程式](./functions-create-first-azure-function.md#create-a-function-app)的步驟建立新的函式應用程式，您將在其中建立您的 Proxy。 這個新的應用程式 URL 可作為我們 API 的前端，而您先前編輯的函式應用程式會做為後端。

1. 在入口網站中瀏覽至新的前端函式應用程式。
1. 選取 [平台功能]****，然後選擇 [應用程式設定]****。
1. 向下滾動至 **應用程式設定**，其中儲存機碼/值組，並使用索引鍵建立新的設定 `HELLO_HOST` 。 將值設定為後端函式應用程式的主機，例如 `<YourBackendApp>.azurewebsites.net`。 此值是您稍早在測試 HTTP 函式時複製的 URL 的一部分。 稍後，您將在設定中參考這項設定。

    > [!NOTE] 
    > 建議使用應用程式設定作為主機設定，以避免 Proxy 依賴硬式編碼的環境。 使用應用程式設定表示您可以在不同環境之間移動 Proxy 設定，將會套用環境特定的應用程式設定。

1. 選取 [儲存]****。

### <a name="creating-a-proxy-on-the-frontend"></a>在前端建立 Proxy

1. 在入口網站中流覽回您的前端函數應用程式。

1. 在左側功能表中 **，選取 [** proxy]，然後選取 [ **新增**]。 

1. 在 [ **新 Proxy** ] 頁面上，使用下表中的設定，然後選取 [ **建立**]。

    | 欄位 | 範例值 | 描述 |
    |---|---|---|
    | 名稱 | HelloProxy | 僅用於管理的易記名稱 |
    | 路由範本 | /api/remotehello | 決定使用什麼路由來叫用此 Proxy |
    | 後端 URL | https://%HELLO_HOST%/api/hello | 指定端點，而其要求應該透過代理 |

    
    :::image type="content" source="./media/functions-create-serverless-api/creating-proxy.png" alt-text="自訂 HTTP 函式":::

    Azure Functions Proxy 不提供 `/api` 基底路徑前置詞，其必須包含在路由範本中。 此 `%HELLO_HOST%` 語法會參考您稍早建立的應用程式設定。 解析後的 URL 會指向您的原始函式。

1. 複製 proxy URL，然後在瀏覽器中測試，或使用您最愛的 HTTP 用戶端，以試用新的 proxy：
    - 若為匿名函數，請使用：   `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?name="Proxies"` 。
    - 若為具有授權的函式，請使用：   `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?code=YOURCODE&name="Proxies"` 。

## <a name="create-a-mock-api"></a>建立模擬 API

接下來，您將使用 proxy 來為您的解決方案建立模擬 API。 此 proxy 可讓用戶端開發進度，而不需要完全實作為後端。 在開發之後，您可以建立新的函式應用程式來支援此邏輯，並將您的 proxy 重新導向至該應用程式。

為了建立此模擬 API，我們將會建立新的 proxy，這次會使用 [App Service 編輯器](https://github.com/projectkudu/kudu/wiki/App-Service-Editor)。 若要開始，請在入口網站中瀏覽至您的函式應用程式。 選取 [ **平臺功能**]，然後在 [ **開發工具** ] 下找到 **App Service 編輯器**]。 App Service 編輯器會在新的索引標籤中開啟。

在左側導覽中，選取 `proxies.json`。 此檔案會儲存所有 proxy 的設定。 如果您使用其中一種函式 [部署方法](./functions-continuous-deployment.md)，您可以在原始檔控制中維護這個檔案。 若要深入了解此檔案，請參閱 [Proxy 進階組態](./functions-proxies.md#advanced-configuration)。

如果您已遵循到目前為止，您的 proxies.js看起來應該像這樣：

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

接下來，您將新增 mock API。 以下列程式碼取代檔案上的 proxies.js：

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

這段程式碼 `GetUserByName` 會在沒有屬性的情況下加入新的 proxy `backendUri` 。 它會使用回應覆寫修改 Proxy 的預設回應，而不是呼叫另一個資源。 要求和回應覆寫也可以搭配後端 URL 一起使用。 這項技術在 proxy 處理至舊版系統（您可能需要修改標頭、查詢參數等）時特別有用。 若要深入了解要求和回應覆寫，請參閱[在 Proxy 中修改要求和回應](./functions-proxies.md)。

使用瀏覽器或您最愛的 REST 用戶端呼叫 `<YourProxyApp>.azurewebsites.net/api/users/{username}` 端點，以測試您的模擬 API。 請務必以代表使用者名稱的字串值取代 _{username}_。

## <a name="next-steps"></a>接下來的步驟

在本文中，您已瞭解如何在 Azure Functions 上建立和自訂 API。 您也學到如何將多個 API (包括模擬) 組合成統一的 API 介面。 不論多麼複雜的 API 都可以使用這些技術來建置，而且都在 Azure Functions 提供的無伺服器計算模型上執行。

進一步開發您的 API 時，下列參考可能很有幫助︰

- [Azure Functions HTTP 繫結](./functions-bindings-http-webhook.md)
- [使用 Azure Functions Proxy]
- [定義 Azure Functions API (預覽)](./functions-openapi-definition.md)


[Create your first function]: ./functions-create-first-azure-function.md
[使用 Azure Functions Proxy]: ./functions-proxies.md
