---
title: 在 Azure Functions 中自訂 HTTP 端點
description: 瞭解如何在 Azure Functions 中自訂 HTTP 觸發程式端點
author: mattchenderson
ms.topic: conceptual
ms.date: 05/04/2017
ms.author: mahender
ms.custom: mvc
ms.openlocfilehash: 61b930eec1385b8c4054f9c202547a82e61e55e7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75769263"
---
# <a name="customize-an-http-endpoint-in-azure-functions"></a>在 Azure Functions 中自訂 HTTP 端點

在本文中，您將瞭解 Azure Functions 如何讓您建立可高度擴充的 Api。 Azure Functions 隨附內建的 HTTP 觸發程式和系結的集合，可讓您輕鬆地以各種語言撰寫端點，包括 node.js、c # 等。 在本文中，您將自訂 HTTP 觸發程式來處理 API 設計中的特定動作。 您也會準備整合 API 與 Azure Functions Proxy，並設定模擬 API，以擴充您的 API。 這一切都在 Functions 無伺服器計算環境之上完成，因此，您不必擔心調整資源 - 只需要專注於您的 API 邏輯。

## <a name="prerequisites"></a>先決條件 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

產生的函數將用於本文的其餘部分。

### <a name="sign-in-to-azure"></a>登入 Azure

開啟 Azure 入口網站。 若要這麼做，請[https://portal.azure.com](https://portal.azure.com)使用您的 Azure 帳戶登入。

## <a name="customize-your-http-function"></a>自訂 HTTP 函式

根據預設，HTTP 觸發函式會設定為接受任何 HTTP 方法。 另外還有 `http://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>` 格式的預設 URL。 如果您遵循快速入門，則 `<funcname>` 可能會像 "HttpTriggerJS1" 一樣。 在本節中，您會將函式修改為只回應 `/api/hello` 路由的 GET 要求。 

1. 在 Azure 入口網站中瀏覽至您的函式。 在左側導覽列中，選取 [整合]****。

    ![自訂 HTTP 函式](./media/functions-create-serverless-api/customizing-http.png)

1. 使用表格中指定的 HTTP 觸發程序設定。

    | 欄位 | 範例值 | 說明 |
    |---|---|---|
    | 允許的 HTTP 方法 | 選取的方法 | 決定哪些 HTTP 方法可用來叫用此函式 |
    | 選取的 HTTP 方法 | GET | 只允許使用選取的 HTTP 方法來叫用此函式 |
    | 路由範本 | /hello | 決定使用什麼路由來叫用此函式 |
    | 授權層級 | 匿名 | 選擇性：讓您的函式不需要 API 金鑰即可存取 |

    > [!NOTE] 
    > 請注意，您並未在路由範本中包含 `/api` 基底路徑前置詞，因為這是由全域設定來處理。

1. 按一下 [檔案]  。

您可以在 [Azure Functions HTTP 繫結](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook)中深入了解 HTTP 函式的自訂。

### <a name="test-your-api"></a>測試您的 API

接下來，測試您的函式，確定可以搭配新的 API 介面一起使用。
1. 在左側導覽中按一下函式的名稱，返回開發頁面。
1. 按一下 [取得函式 URL]**** 並複製 URL。 您應該會看到它現在使用 `/api/hello` 路由。
1. 將 URL 複製到新的瀏覽器索引標籤或您慣用的 REST 用戶端。 根據預設，瀏覽器會使用 GET。
1. 在您的 URL 的查詢字串中新增參數，例如 `/api/hello/?name=John`
1. 按 Enter 鍵以確認它可以運作。 您應該會看到 "*Hello John*" 回應
1. 您也可以嘗試使用另一個 HTTP 方法呼叫端點，以確認不會執行此函式。 為此，您必須使用 REST 用戶端，例如 cURL、Postman 或 Fiddler。

## <a name="proxies-overview"></a>Proxy 概觀

在下一節，您將透過 Proxy 公開您的 API。 Azure Functions Proxy 可讓您將要求轉送至其他資源。 定義 HTTP 端點就像定義 HTTP 觸發程序一樣，但並不撰寫呼叫該端點時要執行的程式碼，而是提供遠端實作的 URL。 這可讓您將多個 API 來源組成單一 API 介面，讓用戶端輕鬆取用。 如果您想要將 API 建置成微服務，這特別有用。

Proxy 可以指向任何 HTTP 資源，例如︰
- Azure Functions 
- [Azure App Service](https://docs.microsoft.com/azure/app-service/overview) 中的 API 應用程式
- [Linux 上的 App Service](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro) 中的 Docker 容器
- 其他任何裝載 API

若要深入了解 Proxy，請參閱[使用 Azure Functions Proxy]。

## <a name="create-your-first-proxy"></a>建立您的第一個 Proxy

在本節中，您將建立新的 Proxy 作為整個 API 的前端。 

### <a name="setting-up-the-frontend-environment"></a>設定前端環境

重複[建立函式應用程式](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function#create-a-function-app)的步驟建立新的函式應用程式，您將在其中建立您的 Proxy。 這個新的應用程式 URL 將作為我們 API 的前端，而您先前編輯的函式應用程式將作為後端。

1. 在入口網站中瀏覽至新的前端函式應用程式。
1. 選取 [平台功能]****，然後選擇 [應用程式設定]****。
1. 向下捲動至儲存機碼/值組的 [應用程式設定]****，然後使用機碼 "HELLO_HOST" 來建立新的設定。 將值設定為後端函式應用程式的主機，例如 `<YourBackendApp>.azurewebsites.net`。 這是您先前測試 HTTP 函式時複製之 URL 的一部分。 稍後，您將在設定中參考這項設定。

    > [!NOTE] 
    > 建議使用應用程式設定作為主機設定，以避免 Proxy 依賴硬式編碼的環境。 使用應用程式設定表示您可以在不同環境之間移動 Proxy 設定，將會套用環境特定的應用程式設定。

1. 按一下 [檔案]  。

### <a name="creating-a-proxy-on-the-frontend"></a>在前端建立 Proxy

1. 在入口網站中瀏覽回到前端函式應用程式。
1. 在左側導覽中，按一下 [Proxy] 旁邊的加號 [+]。
    ![建立 Proxy](./media/functions-create-serverless-api/creating-proxy.png)
1. 使用表格中指定的 Proxy 設定。 

    | 欄位 | 範例值 | 描述 |
    |---|---|---|
    | 名稱 | HelloProxy | 僅用於管理的易記名稱 |
    | 路由範本 | /api/remotehello | 決定使用什麼路由來叫用此 Proxy |
    | 後端 URL | https://%HELLO_HOST%/api/hello | 指定端點，而其要求應該透過代理 |
    
1. 請注意，Proxy 不提供 `/api` 基底路徑前置詞，這必須加入路由範本中。
1. `%HELLO_HOST%` 語法會參考您稍早建立的應用程式設定。 解析後的 URL 會指向您的原始函式。
1. 按一下頁面底部的 [新增]  。
1. 您可以複製 Proxy URL，然後在瀏覽器或您最愛的 HTTP 用戶端測試，以試驗新的 Proxy。
    1. 針對匿名函式使用：
        1. `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?name="Proxies"`
    1. 針對具有授權的函式使用：
        1. `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?code=YOURCODE&name="Proxies"`

## <a name="create-a-mock-api"></a>建立模擬 API

接下來，您將使用 Proxy 為您的方案建立模擬 API。 這樣可以開始進行用戶端開發，而不需完全實作後端。 在開發過程中，稍後您可以建立新的函式應用程式來支援此邏輯，並將您的 Proxy 重新導向此應用程式。

為了建立這個模擬 API，我們將建立新的 proxy，這次會使用 [App Service 編輯器](https://github.com/projectkudu/kudu/wiki/App-Service-Editor)。 若要開始，請在入口網站中瀏覽至您的函式應用程式。 選取 [平台功能]****，然後在 [開發工具]**** 底下尋找 [App Service 編輯器]****。 按一下此選項會在新的索引標籤中開啟 App Service 編輯器。

在左側導覽中，選取 `proxies.json`。 此檔案儲存您的所有 Proxy 的設定。 如果您使用其中一個 [Functions 部署方法](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment)，則這是您在原始檔控制中維護的檔案。 若要深入了解此檔案，請參閱 [Proxy 進階組態](https://docs.microsoft.com/azure/azure-functions/functions-proxies#advanced-configuration)。

如果您到目前為止一直遵循指示操作，則 proxies.json 應該如下所示︰

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

接下來，您將新增模擬 API。 用下列內容取代 proxies.json 檔案︰

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

這會新增 Proxy "GetUserByName"，但不含 backendUri 屬性。 它會使用回應覆寫修改 Proxy 的預設回應，而不是呼叫另一個資源。 要求和回應覆寫也可以搭配後端 URL 一起使用。 當 proxy 處理舊版系統時（您可能需要修改標頭、查詢參數等），這會特別有用。若要深入瞭解要求和回應覆寫，請參閱[在 proxy 中修改要求和回應](https://docs.microsoft.com/azure/azure-functions/functions-proxies)。

使用瀏覽器或您最愛的 REST 用戶端呼叫 `<YourProxyApp>.azurewebsites.net/api/users/{username}` 端點，以測試您的模擬 API。 請務必以代表使用者名稱的字串值取代 _{username}_。

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何在 Azure Functions 上建立和自訂 API。 您也學到如何將多個 API (包括模擬) 組合成統一的 API 介面。 不論多麼複雜的 API 都可以使用這些技術來建置，而且都在 Azure Functions 提供的無伺服器計算模型上執行。

進一步開發您的 API 時，下列參考可能很有幫助︰

- [Azure Functions HTTP 繫結](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook)
- [使用 Azure Functions Proxy]
- [定義 Azure Functions API (預覽)](https://docs.microsoft.com/azure/azure-functions/functions-api-definition-getting-started)


[Create your first function]: https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function
[使用 Azure Functions Proxy]: https://docs.microsoft.com/azure/azure-functions/functions-proxies
