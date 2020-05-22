---
title: 使用 Azure Functions 將 API 新增至 Azure 靜態 Web Apps
description: 使用 Azure Functions 將無伺服器 API 新增至靜態 Web 應用程式，以開始使用 Azure 靜態 Web Apps。
services: static-web-apps
author: manekinekko
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: wachegha
ms.openlocfilehash: a6aee5c8049e03a43c547f419f6c6646617e651c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596147"
---
# <a name="add-an-api-to-azure-static-web-apps-preview-with-azure-functions"></a>使用 Azure Functions 將 API 新增至 Azure 靜態 Web Apps 預覽版

您可以透過與 Azure Functions 的整合，將無伺服器 API 新增至 Azure 靜態 Web Apps。 本文示範如何將 API 新增至 Azure 靜態 Web Apps 網站，並在其中進行部署。

如需有關如何保護 API 路由的詳細資訊，請參閱[路由指南](routes.md)。

## <a name="prerequisites"></a>Prerequisites

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free)。
- [Visual Studio Code](https://code.visualstudio.com/)
- 適用於 Visual Studio Code 的 [Azure Functions 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
- [Live Server Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) 擴充功能。

## <a name="create-a-git-repository"></a>建立 Git 存放庫 

下列步驟示範如何建立新的存放庫，並將檔案複製到您的電腦。

1. 瀏覽至 https://github.com/staticwebdev/vanilla-basic/generate 以建立新的存放庫。
1. 在 [存放庫名稱] 方塊中，輸入 **my-vanilla-api**。
1. 按一下 [從範本建立存放庫]。

   :::image type="content" source="media/add-api/create-repository.png" alt-text="從 vanilla-basic 建立新的存放庫":::

建立專案之後，您可以使用 Visual Studio Code 來複製 Git 存放庫。

1. 按 **F1** 鍵，在命令選擇區中開啟命令。
1. 將 URL 貼到 _Git：複製_  提示字元，然後按 **Enter** 鍵。

   :::image type="content" source="media/add-api/vscode-git-0.png" alt-text="使用 Visual Studio Code 複製 GitHub 專案":::

   :::image type="content" source="media/add-api/github-clone-url.png" alt-text="使用 Visual Studio Code 複製 GitHub 專案":::


## <a name="create-your-local-project"></a>建立本機專案

在這一節中，您會使用 Visual Studio Code 來建立本機 Azure Functions 專案。 稍後，您會將函式應用程式發佈至 Azure。

1. 在 _my-vanilla-api_ 專案中，建立名為 **api** 的子資料夾。

   > [!NOTE]
   > 您可以為此資料夾指定任何名稱。 此範例會使用 `api`。 

2. 按 **F1** 鍵以開啟命令選擇區
3. 輸入 **Azure Functions：建立新專案...**
4. 按 **Enter** 鍵
5. 選擇 [瀏覽]
6. 選取 **api** 資料夾做為專案工作區的目錄
7. 選擇 [選取]

   :::image type="content" source="media/add-api/create-azure-functions-vscode-1.png" alt-text="使用 Visual Studio Code 來建立新的 Azure Functions":::

8. 依照提示提供下列資訊：

    - _為您的函式專案選取語言_：選擇 **JavaScript**
    - _選取您的專案第一個函式的範本_：選擇 **HTTP 觸發程序**
    - _提供函式名稱_：輸入 **GetMessage**
    - _授權層級_選擇 [匿名]，讓任何人都能呼叫您的函式端點。
        - 若要了解授權層級，請參閱[授權金鑰](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)。

9. Visual Studio Code 會使用這項資訊產生具有 HTTP 觸發程序的 Azure Functions 專案。
    - 您可以在 Visual Studio Code 的總管視窗中檢視本機專案檔。
    - 若要深入了解所建立的檔案，請參閱[產生的專案檔](https://docs.microsoft.com/azure/azure-functions/functions-develop-vs-code#generated-project-files)。

10. 您的應用程式現在應該有與此範例類似的專案結構。

    ```files
    ├── api
    │   ├── GetMessage
    │   │   ├── function.json
    │   │   ├── index.js
    │   │   └── sample.dat
    │   ├── host.json
    │   ├── local.settings.json
    │   ├── package.json
    │   └── proxies.json
    ├── index.html
    ├── readme.md
    └── styles.css
    ```

11. 接下來，使用下列程式碼，更新 _api/GetMessage/index.js_ 下的 `GetMessage` 函式。

    ```JavaScript
    module.exports = async function (context, req) {
      context.res = {
        body: { 
          text: "Hello from the API" 
        }
      };
    };
    ```

12. 使用下列設定來更新 `api/GetMessage/function.json` 底下的 `GetMessage` 組態。

    ```json
    {
      "bindings": [
        {
          "authLevel": "anonymous",
          "type": "httpTrigger",
          "direction": "in",
          "name": "req",
          "methods": [
            "get"
          ],
          "route": "message"
        },
        {
          "type": "http",
          "direction": "out",
          "name": "res"
        }
      ]
    }
    ```
    
使用上述設定，API 端點為：

- 已使用 HTTP 要求觸發對函式的呼叫
- 適用於所有要求，不論驗證狀態為何
- 透過 _/api/message_ 路由公開

## <a name="run-the-function-locally"></a>在本機執行函式

Visual Studio Code 可與 [Azure Functions Core Tools](https://docs.microsoft.com/azure/azure-functions/functions-run-local) 整合，讓您能夠先在本機開發電腦上執行此專案，再發佈至 Azure。

1. 按 **F5** 鍵來執行函式，以啟動函式應用程式，而 Core Tools 輸出會顯示在 [終端機] 面板中。

2. 如果您尚未安裝 Azure Functions Core Tools，請在提示字元中選取 [安裝]。

    安裝 Core Tools 後，您的應用程式將會在 [終端機] 面板中啟動。 在輸出中，您可以查看在本機執行之 HTTP 觸發函式的 URL 端點。

    :::image type="content" source="media/add-api/create-azure-functions-vscode-2.png" alt-text="使用 Visual Studio Code 來建立新的 Azure Functions":::

3. 執行 Core Tools 時，瀏覽至下列 URL 以執行 `GET` 要求。

   <http://localhost:7071/api/message>

   此時會傳回回應，其內容在瀏覽器中會顯示如下：

   :::image type="content" source="media/add-api/create-azure-functions-vscode-3.png" alt-text="使用 Visual Studio Code 來建立新的 Azure Functions":::

在確認函式正確執行之後，您就可以從 JavaScript 應用程式呼叫 API。

### <a name="call-the-api-from-the-application"></a>從應用程式呼叫 API

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]


#### <a name="update-files-to-access-the-api"></a>更新檔案以存取 API

1. 接下來，使用下列程式碼更新 _index.html_ 檔案的內容，以從 API 函式擷取文字並將其顯示在螢幕上：

   ```html
   <!DOCTYPE html>
   <html lang="en">

   <head>
     <meta charset="UTF-8">
     <meta name="viewport" content="width=device-width, initial-scale=1.0">
     <link rel="stylesheet" href="styles.css">
     <title>Vanilla JavaScript App</title>
   </head>

   <body>
     <main>
       <h1>Vanilla JavaScript App</h1>
       <p>Loading message from the API: <b id="name">...</b></p>
     </main>

     <script>
       (async function() {
         let { text } = await( await fetch(`/api/message`)).json();
         document.querySelector('#name').textContent = text;
       }())
     </script>
   </body>

   </html>
   ```

   在 Core Tools 執行時，請使用 [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) Visual Studio Code 擴充功能來提供 _index.html_ 檔案，並在瀏覽器中將其開啟。 

2. 按 **F1** 鍵，然後選擇 [Live Server：**開啟即時伺服器]** ，以啟動即時伺服器。

   :::image type="content" source="media/add-api/create-azure-functions-vscode-4.png" alt-text="使用 Visual Studio Code 來建立新的 Azure Functions":::

   > [!NOTE]
   > 您可以使用其他 HTTP 伺服器或 Proxy 來提供 `index.html` 檔案。 將無法從 `file:///` 存取 `index.html`。

### <a name="commit-and-push-your-changes-to-github"></a>認可及推送您對 GitHub 的變更

使用 Visual Studio Code 來認可您對遠端 Git 存放庫的變更並加以推送。

1. 按 **F1** 鍵以開啟命令選擇區
1. 輸入 **Git：全部認可**
1. 新增認可訊息
1. 輸入 **Git：推送**

## <a name="create-a-static-web-app"></a>建立靜態 Web 應用程式

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-1.png" alt-text="在 Azure 入口網站上建立靜態應用程式 - 畫面 1":::

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)
1. 按一下 [建立資源]
1. 搜尋 [靜態 Web Apps]
1. 按一下 [靜態 Web Apps (預覽)]
1. 按一下 [建立] 
1. 選取您的 _Azure 訂用帳戶_
1. 選取或建立新的 [資源群組]
1. 將應用程式命名為 **my-vanilla-api**。
1. 選取最靠近您的 [區域]
1. 選取 [免費] _SKU_
1. 按一下 [使用 GitHub 登入] 按鈕，並向 GitHub 進行驗證
1. 選取您慣用的 [組織]
1. 從 [存放庫] 下拉式選單中，選取 **my-vanilla-api**
1. 從 [分支] 下拉式選單中，選取 [主要]
1. 按 [下一步：組建 >] 按鈕，以編輯組建組態

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-2.png" alt-text="在 Azure 入口網站上建立靜態應用程式 - 畫面 2":::

接下來，新增下列組建詳細資料。

1. 針對 [應用程式位置]，輸入 **./** 。

1. 在 [API 位置] 方塊中，輸入 **api**。

   這是上一個步驟中所建立 API 群組的名稱。
   
1. 清除 [應用程式成品位置] 的預設值，並將此方塊保留空白。

1. 按一下 [檢閱 + 建立]。

| 設定 | 描述             | 必要 |
| -------- | ----------------------- |
|  應用程式位置 | 靜態應用程式原始程式碼的位置 | 是 |
|  API 位置 | API 後端的位置。 這會指向 Azure Functions 應用程式專案的根資料夾 | 否 |
|  應用程式成品位置 | 組建輸出檔案夾的位置。 有些前端 JavaScript 架構的組建步驟會將生產檔案放在資料夾中。 此設定會指向組建輸出檔案夾。 | 否 |

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-3.png" alt-text="在 Azure 入口網站上建立靜態應用程式 - 畫面 3":::

1. 按一下 [建立] 
1. 等待部署完成 (這可能需要幾分鐘的時間)
1. 巡覽到 `https://github.com/<YOUR_GITHUB_ACCOUNT>/my-vanilla-api/actions?query=workflow%3A"Azure+Pages+CI%2FCD"`
1. 確保組建成功

:::image type="content" source="media/add-api/github-workflow-0.png" alt-text="GitHub 工作流程":::

已部署的 API 將於 `https://<STATIC_APP_NAME>.azurestaticapps.net/api/<FUNCTION_OR_ROUTE_NAME>` 提供。

:::image type="content" source="media/add-api/github-workflow-1.png" alt-text="GitHub 工作流程":::

您也可以從 Azure 入口網站找到應用程式 URL：

:::image type="content" source="media/add-api/static-app-url-from-portal.png" alt-text="從 Azure 入口網站存取靜態應用程式 URL":::

或者，您可以在 `https://<STATIC_APP_NAME>.azurestaticapps.net` 直接存取您的 Azure 靜態 Web 應用程式，並在瀏覽器中檢查結果。

## <a name="clean-up-resources"></a>清除資源

如果您不想要保留此應用程式以供進一步使用，可以使用下列步驟來刪除 Azure 靜態 Web 應用程式及其相關資源。

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)
1. 在頂端的搜尋列中，輸入**資源群組**
1. 按一下 [資源群組] 
1. 選取 **myResourceGroup**
1. 在 [myResourceGroup] 頁面上，確定所列出的資源是您想要刪除的項目。
1. 選取 [刪除]
1. 在文字方塊中輸入 **myResourceGroup**
1. 選取 [刪除] 。


## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [進行應用程式設定](./application-settings.md)
