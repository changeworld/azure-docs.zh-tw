---
title: 教學課程：使用 Logic Apps 傳送電子郵件
description: 了解如何從您的 App Service 應用程式叫用商務流程。 傳送電子郵件、推文和 Facebook 貼文、新增至郵寄清單，以及其他更多動作。
ms.topic: tutorial
ms.date: 04/08/2020
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 5aa563e55c64893d57522dd1154a64c7e90a1690
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91397431"
---
# <a name="tutorial-send-email-and-invoke-other-business-processes-from-app-service"></a>教學課程：傳送電子郵件並從 App Service 叫用其他商務流程

在本教學課程中，您將了解如何整合 App Service 應用程式與您的商務流程。 這常見於 Web 應用程式案例，例如：

- 傳送交易的確認電子郵件
- 將使用者新增至 Facebook 群組
- 連線到第三方系統，例如 SAP、Salesforce 等等。
- 交換標準 B2B 訊息

在本教學課程中，您會使用 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)，從 App Service 應用程式透過 Gmail 傳送電子郵件。 還有其他方法可以從 Web 應用程式傳送電子郵件，例如您的語言架構所提供的 SMTP 設定。 不過，Logic Apps 為您的 App Service 應用程式帶來更多功效，但不會增加程式碼的複雜度。 Logic Apps 針對大多數熱門的商務整合提供了簡單的設定介面，而您的應用程式可以隨時使用 HTTP 要求進行呼叫。

## <a name="prerequisite"></a>必要條件

將採用您所選語言架構的應用程式部署至 App Service。 若要遵循教學課程來部署範例應用程式，請參閱以下內容：

### <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

[教學課程：在 Azure 中搭配 SQL Database 來建置 ASP.NET 應用程式](app-service-web-tutorial-dotnet-sqldatabase.md)

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

[教學課程：在 Azure App Service 中建置 ASP.NET Core 和 SQL Database 應用程式](tutorial-dotnetcore-sqldb-app.md)

### <a name="nodejs"></a>[Node.js](#tab/node)

[教學課程：在 Azure 中建置 Node.js 和 MongoDB 應用程式](tutorial-nodejs-mongodb-app.md)

### <a name="php"></a>[PHP](#tab/php)

[教學課程：在 Azure 中建置 PHP 和 MySQL 應用程式](tutorial-php-mysql-app.md)

### <a name="python"></a>[Python](#tab/python)

[教學課程：在 Azure App Service 中透過 PostgreSQL 執行 Python (Django) Web 應用程式](tutorial-python-postgresql-app.md)

### <a name="ruby"></a>[Ruby](#tab/ruby)

[在 Linux 上的 Azure App Service 中建置 Ruby 和 Postgres 應用程式](tutorial-ruby-postgres-app.md)

---

## <a name="create-the-logic-app"></a>建立邏輯應用程式

1. 在 [Azure 入口網站](https://portal.azure.com)中，遵循[建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md#create-your-logic-app)中的指示，建立空的邏輯應用程式。 當您看到 [Logic Apps 設計工具]  時，請回到本教學課程。
1. 在 Logic Apps 設計工具的啟動顯示頁面中，選取 [從一般觸發程序開始]  之下的 [收到 HTTP 要求時]  。

    ![顯示 Logic Apps Designer 啟動顯示畫面已反白顯示 [收到 HTTP 要求時] 的螢幕擷取畫面。](./media/tutorial-send-email/receive-http-request.png)
1. 在 [收到 HTTP 要求時] 的對話方塊中，選取 [使用範例承載來產生結構描述]。

    ![顯示已選取 [收到 HTTP 要求時] 對話方塊和 [使用範例承載來產生結構描述] 選項的螢幕擷取畫面。 ](./media/tutorial-send-email/generate-schema-with-payload.png)

1. 將下列範例 JSON 複製到文字方塊中，然後選取 [完成]  。

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

    現在已針對您想要的要求資料產生結構描述。 實際上，您可以只擷取您的應用程式程式碼所產生的實際要求資料，讓 Azure 為您產生 JSON 結構描述。 
1. 在 Logic Apps 設計工具的頂端，選取 [儲存]  。 

    您現在可以看到 HTTP 要求觸發程序的 URL。 選取複製圖示進行複製，以供日後使用。

    ![反白顯示複製圖示以複製 HTTP 要求觸發程序 URL 的螢幕擷取畫面。](./media/tutorial-send-email/http-request-url.png)

    此 HTTP 要求定義可觸發您想要在此邏輯應用程式中執行的任何一切，也就是 Gmail 或任何其他項目。 稍後您將在 App Service 應用程式中叫用此 URL。 如需要求觸發程序的詳細資訊，請參閱 [HTTP 要求/回應參考](../connectors/connectors-native-reqres.md)。

1. 在設計工具的底部，按一下 [新增步驟]  ，在動作搜尋方塊中輸入 **Gmail**，然後尋找並選取 [傳送電子郵件 (V2)]  。
    
    > [!TIP]
    > 您可以搜尋其他類型的整合，例如 SendGrid、MailChimp、Microsoft 365 和 SalesForce。 如需詳細資訊，請參閱 [Logic Apps 文件](../logic-apps/index.yml)。

1. 在 [Gmail]  對話方塊中，選取 [登入]  並登入您想用來傳送電子郵件的 Gmail 帳戶。

    ![顯示 Gmail 對話方塊的螢幕擷取畫面，您使用此對話方塊來登入要從中傳送電子郵件的 Gmail 帳戶。](./media/tutorial-send-email/gmail-sign-in.png)

1. 登入後，按一下 [收件人]  文字方塊，動態內容對話方塊就會自動開啟。

1. 選取 [收到 HTTP 要求時]  動作旁邊的 [更多資訊]  。

    ![顯示 [收到 HTTP 要求時] 動作旁 [查看更多] 按鈕的螢幕擷取畫面。](./media/tutorial-send-email/expand-dynamic-content.png)

    您現在應會看到您稍早使用的範例 JSON 資料中的三個屬性。 在此步驟中，您會使用 HTTP 要求中的這些屬性來建構電子郵件。
1. 因為您要選取 [收件人]  欄位的值，請選擇 [電子郵件]  。 如有需要，請按一下 [新增動態內容]  以關閉動態內容對話方塊。

    ![反白顯示 [電子郵件] 選項和 [新增動態競爭] 選項的螢幕擷取畫面。](./media/tutorial-send-email/hide-dynamic-content.png)

1. 在 [新增參數]  下拉式清單中，選取 [主旨]  和 [本文]  。

1. 在 [主旨]  文字方塊中按一下，並以相同的方式選擇 [工作]  。 當游標仍在 [主旨]  方塊中，輸入 *created*。 

1. 按一下 [本文]  ，並以相同的方式選擇 [到期]  。 將游標移至 [到期]  左邊並輸入 *This work item is due on*。

    > [!TIP]
    > 如果您想要直接在電子郵件本文中編輯 HTML 內容，請選取 [Logic Apps 設計工具] 視窗頂端的 [程式碼檢視]  。 只要確定您保留動態內容程式碼 (例如 `@{triggerBody()?['due']}`)
    >
    > ![顯示程式碼檢視以直接在電子郵件內文中檢視 HTML 內容的螢幕擷取畫面。](./media/tutorial-send-email/edit-rich-html-email.png) 

1. 接下來，將非同步 HTTP 回應新增至 HTTP 觸發程序。 在 HTTP 觸發程序與 Gmail 動作之間，按一下 **+** 號，然後選取 [新增平行分支]  。

    ![反白顯示 + 符號 和 [新增平行分支] 選項的螢幕擷取畫面。](./media/tutorial-send-email/add-http-response.png)

1. 在搜尋方塊中，搜尋**回應**，然後選取 [回應] 動作。

    ![反白顯示搜尋列和回應動作的螢幕擷取畫面。](./media/tutorial-send-email/choose-response-action.png)

    根據預設，回應動作會傳送 HTTP 200。 這對本教學課程而言夠好。 如需詳細資訊，請參閱 [HTTP 要求/回應參考](../connectors/connectors-native-reqres.md)。

1. 在 Logic Apps 設計工具的頂端，再次選取 [儲存]  。 

## <a name="add-http-request-code-to-app"></a>將 HTTP 要求程式碼新增至應用程式

請確定您稍早已複製 HTTP 要求觸發程序的 URL。 因為其包含敏感性資訊，所以最好不要直接將其放入程式碼中。 透過 App Service，您可以使用應用程式設定，改為以環境變數的形式進行參考。 

在 [Cloud Shell](https://shell.azure.com) 中，使用下列命令建立應用程式設定 (取代 *\<app-name>* 、 *\<resource-group-name>* 和 *\<logic-app-url>* )：

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings LOGIC_APP_URL="<your-logic-app-url>"
```

在您的程式碼中，透過下列設定，使用您的語言架構可用的任何 HTTP 用戶端語言，對 URL 進行標準 HTTP Post：

- 要求本文包含您提供給邏輯應用程式的相同 JSON 格式：

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

- 此要求包含標題 `Content-Type: application/json`。 
- 若要達到最佳化效能，請盡可能以非同步方式傳送要求。

按一下底下慣用的語言/架構索引標籤，以查看範例。

### <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

在 ASP.NET 中，您可以使用 [System.Net.Http.HttpClient](/dotnet/api/system.net.http.httpclient) 類別傳送 HTTP Post。 例如：

```csharp
// requires using System.Net.Http;
var client = new HttpClient();
// requires using System.Text.Json;
var jsonData = JsonSerializer.Serialize(new
{
    email = "a-valid@emailaddress.com",
    due = "4/1/2020",
    task = "My new task!"
});

HttpResponseMessage result = await client.PostAsync(
    // requires using System.Configuration;
    ConfigurationManager.AppSettings["LOGIC_APP_URL"],
    new StringContent(jsonData, Encoding.UTF8, "application/json"));
    
var statusCode = result.StatusCode.ToString();
```

如果您要在[教學課程：在 Azure 中使用 SQL Database 建置 ASP.NET 應用程式](app-service-web-tutorial-dotnet-sqldatabase.md)的範例應用程式上測試此程式碼，可以在新增 `Todo` 項目之後，使用此程式碼在 [Create 動作](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/blob/master/DotNetAppSqlDb/Controllers/TodosController.cs#L52-L63)中傳送電子郵件確認。 若要使用上述的非同步程式碼，請將 Create 動作轉換為非同步。

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

在 ASP.NET Core 中，您可以使用 [System.Net.Http.HttpClient](/dotnet/api/system.net.http.httpclient) 類別傳送 HTTP Post。 例如：

```csharp
// requires using System.Net.Http;
var client = new HttpClient();
// requires using System.Text.Json;
var jsonData = JsonSerializer.Serialize(new
{
    email = "a-valid@emailaddress.com",
    due = "4/1/2020",
    task = "My new task!"
});

HttpResponseMessage result = await client.PostAsync(
    // Requires DI configuration to access app settings. See https://docs.microsoft.com/azure/app-service/configure-language-dotnetcore#access-environment-variables
    _configuration["LOGIC_APP_URL"],
    new StringContent(jsonData, Encoding.UTF8, "application/json"));
    
var statusCode = result.StatusCode.ToString();
```

> [!NOTE]
> 撰寫此程式碼的目的是為了簡化示範。 實際上，請勿將每個要求的 `HttpClient` 物件具現化。 請依照[使用 IHttpClientFactory 來實作復原性 HTTP 要求](/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)的指引操作。

如果您要在[教學課程：在 Azure App Service 中建置 ASP.NET Core 和 SQL Database 應用程式](tutorial-dotnetcore-sqldb-app.md)的範例應用程式上測試此程式碼，可以在新增 `Todo` 項目之後，使用此程式碼在 [Create 動作](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial/blob/master/Controllers/TodosController.cs#L56-L65)中傳送電子郵件確認。

### <a name="nodejs"></a>[Node.js](#tab/node)

在 Node.js 中，您可以使用 [axios](https://www.npmjs.com/package/axios) 之類的 npm 套件輕鬆地傳送 HTTP Post。 例如：

```javascript
// Requires npm install --save axios
const axios = require('axios');

var jsonData = {
        email: "a-valid@emailaddress.com",
        due: "4/1/2020",
        task: "My new task!"
};

(async function(data) {
    try {
        const response = await axios.post(process.env.LOGIC_APP_URL, jsonData);
        console.log(response.status);
    } catch (error) {
        console.log(error);
    }
})(jsonData);

```

如果您要在[教學課程：在 Azure 中建置 ASP.NET Core 和 MongoDB 應用程式](tutorial-nodejs-mongodb-app.md)的範例應用程式上測試此程式碼，可以在[順利儲存本文](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L24)之後，使用此程式碼在 [Create 動作](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L14-L27)中傳送電子郵件確認。

### <a name="php"></a>[PHP](#tab/php)

在 PHP 中，您可以使用 [Guzzle](http://docs.guzzlephp.org/en/stable/index.html) 輕鬆地傳送 HTTP Post。 例如：

```php
// Requires composer require guzzlehttp/guzzle:~6.0
use GuzzleHttp\Client;
...
$client = new Client();
$options = [
    'json' => [ 
        'email' => 'a-valid@emailaddress.com',
        'due' => '4/1/2020',
        'task' => "My new task!"
    ]
];

$promise = $client-> postAsync(getenv($LOGIC_APP_URL), $options)->then( 
    function ($response) {
        return $response->getStatusCode();
    }, function ($exception) {
        return $exception->getResponse();
    }
);

$response = $promise->wait();
// Requires Laravel to run Log::info(). Check the documentation of your preferred framework for logging instructions.
Log::info(print_r($response, TRUE));
```

如果您要在[教學課程：在 Azure 中建置 PHP 和 MySQL 應用程式](tutorial-php-mysql-app.md)的範例應用程式上測試此程式碼，可以使用此程式碼在 [Route::post 函式](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48)中 (就在 return 陳述式之前) 中傳送電子郵件確認。

### <a name="python"></a>[Python](#tab/python)

在 Python 中，您可以使用 [requests](https://pypi.org/project/requests/) 輕鬆地傳送 HTTP Post。 例如：

```python
# Requires pip install requests && pip freeze > requirements.txt
import requests
...
payload = {
    "email": "a-valid@emailaddress.com",
    "due": "4/1/2020",
    "task": "My new task!"
}
response = requests.post("https://prod-112.westeurope.logic.azure.com:443/workfl$
print(response.status_code)
```
<!-- ```python
# Requires pip install aiohttp && pip freeze > requirements.txt
import aiohttp
...
payload = {
        'email': 'a-valid@emailaddress.com',
        'due': '4/1/2020',
        'task': 'My new task!'
}
async with aiohttp.post('http://httpbin.org/post', data=json.dump(payload)) as resp:
    print(await resp.status())
``` -->

如果您要在[教學課程：在 Azure App Service 中使用 PostgreSQL 執行 Python (Django) Web 應用程式](tutorial-python-postgresql-app.md)的範例應用程式上測試此程式碼，可以使用此程式碼在 [Route::post 函式](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48)中 (就在 return 陳述式之前) 傳送電子郵件確認。

### <a name="ruby"></a>[Ruby](#tab/ruby)

在 Ruby 中，您可以使用 [JSONClient](https://www.rubydoc.info/gems/httpclient/JSONClient) 輕鬆地傳送 HTTP Post。 例如：

```ruby
clnt = JSONClient.new
body = { 
    'email' => 'a-valid@emailaddress.com',
    'due' => '4/1/2020',
    'task' => "My new task!"
}

connection = clnt.post_async(ENV['LOGIC_APP_URL'], body)
```

如果您要在[在 Linux 上的 Azure App Service 中建置 Ruby 和 Postgres 應用程式](tutorial-ruby-postgres-app.md)的範例應用程式上測試此程式碼，[當 @task.save 成功時](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L30)，使用此程式碼在 [Create](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L26-L38) 動作中傳送電子郵件確認。

---

## <a name="more-resources"></a>其他資源

[教學課程：在 Azure App Service 中裝載具有 CORS 支援的 RESTful API](app-service-web-tutorial-rest-api.md)  
[Logic Apps 的 HTTP 要求/回應參考](../connectors/connectors-native-reqres.md)  
[快速入門：使用 Azure Logic Apps 建立第一個工作流程 - Azure 入口網站](../logic-apps/quickstart-create-first-logic-app-workflow.md)