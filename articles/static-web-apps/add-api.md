---
title: 使用 Azure Functions 將 API 新增至 Azure 靜態 Web Apps
description: 使用 Azure Functions 將無伺服器 API 新增至靜態 Web 應用程式，以開始使用 Azure 靜態 Web Apps。
services: static-web-apps
author: manekinekko
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/29/2020
ms.author: wachegha
ms.custom: devx-track-js
ms.openlocfilehash: 0e90c023fa74711246c2e6e69eb576695e86a457
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93128499"
---
# <a name="add-an-api-to-azure-static-web-apps-preview-with-azure-functions"></a>使用 Azure Functions 將 API 新增至 Azure 靜態 Web Apps 預覽版

您可以透過與 Azure Functions 的整合，將無伺服器 API 新增至 Azure 靜態 Web Apps。 本文示範如何將 API 新增至 Azure 靜態 Web Apps 網站，並在其中進行部署。

## <a name="prerequisites"></a>Prerequisites

- 具有有效訂用帳戶的 Azure 帳戶。
  - 如果您沒有帳戶，可以 [免費建立一個](https://azure.microsoft.com/free)帳戶。
- [Visual Studio Code](https://code.visualstudio.com/)
- 適用於 Visual Studio Code 的 [Azure Functions 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
- [Live Server Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) 擴充功能。
- [Node.js](https://nodejs.org/download/) 在本機執行 API 應用程式

## <a name="create-a-git-repository"></a>建立 Git 存放庫

下列步驟示範如何建立新的存放庫，並將檔案複製到您的電腦。

1. 請確定您已登入 GitHub，然後流覽至 https://github.com/staticwebdev/vanilla-basic/generate 以建立新的存放庫。
1. 在 [存放庫名稱] 方塊中，輸入 **my-vanilla-api** 。
1. 按一下 [從範本建立存放庫]。

   :::image type="content" source="media/add-api/create-repository.png" alt-text="從 vanilla-basic 建立新的存放庫":::

建立專案之後，請在瀏覽器中複製新存放庫的 URL。 您可以在 Visual Studio Code 中使用此 URL，以複製 Git 存放庫。

1. 按 **F1** 鍵，在命令選擇區中開啟命令。
1. 將 URL 貼到 _Git：複製_  提示字元，然後按 **Enter** 鍵。

   :::image type="content" source="media/add-api/vscode-git-0.png" alt-text="從 vanilla-basic 建立新的存放庫":::

    遵循提示來選取要複製專案的存放庫位置。

## <a name="create-the-api"></a>建立 API

接下來，您會建立 Azure Functions 專案作為應用程式的 API。 

1. 在 _my-vanilla-api_ 專案中，建立名為 **api** 的子資料夾。
1. 按 **F1** 鍵以開啟命令選擇區
1. 輸入 **Azure Functions：建立新專案...**
1. 按 **Enter** 鍵
1. 選擇 [瀏覽]
1. 選取 **api** 資料夾做為專案工作區的目錄
1. 選擇 [選取]

   :::image type="content" source="media/add-api/create-azure-functions-vscode-1.png" alt-text="從 vanilla-basic 建立新的存放庫":::

1. 依照提示提供下列資訊：

    - _選取語言_ ：選擇 **JavaScript**
    - _選取您的專案第一個函式的範本_ ：選擇 **HTTP 觸發程序**
    - _提供_ 函式名稱：輸入 **GetMessage**
    - _授權層級_ 選擇 [匿名]，讓任何人都能呼叫您的函式端點。
        - 若要了解授權層級，請參閱[授權金鑰](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)。

Visual Studio Code 會產生具有 HTTP 觸發函式的 Azure Functions 專案。

您的應用程式現在具有類似下列範例的專案結構。

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

接下來，您將變更函式， `GetMessage` 以將訊息傳回至前端。

1. `GetMessage`使用下列程式碼更新 _Api/GetMessage/index.js_ 下的函式。

    ```javascript
    module.exports = async function (context, req) {
      context.res = {
        body: {
          text: "Hello from the API"
        }
      };
    };
    ```

1. 使用下列設定來更新 `api/GetMessage/function.json` 底下的 `GetMessage` 組態。

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

- 對函式發出 HTTP 要求時觸發
- 適用於所有要求，不論驗證狀態為何
- 透過 _/api/message_ 路由公開

## <a name="run-the-api-locally"></a>在本機執行 API

Visual Studio Code 可與 [Azure Functions Core Tools](../azure-functions/functions-run-local.md) 整合，讓您能夠先在本機開發電腦上執行此專案，再發佈至 Azure。

> [!TIP]
> 在繼續之前，請確定您已安裝 [必要條件](#prerequisites) 區段中所列的所有資源。

1. 按 **F5** 以啟動函式應用程式來執行函式。

1. 如果您尚未安裝 Azure Functions Core Tools，請在提示字元中選取 [安裝]。

    Core Tools 會在 _終端_ 機面板中顯示正在執行之應用程式的輸出。 在輸出中，您可以查看在本機執行之 HTTP 觸發函式的 URL 端點。

    :::image type="content" source="media/add-api/create-azure-functions-vscode-2.png" alt-text="從 vanilla-basic 建立新的存放庫":::

1. 當核心工具執行時，流覽至下列 URL，以確認 API 是否正確執行： `http://localhost:7071/api/message` 。

   瀏覽器中的回應看起來應該類似下列範例：

   :::image type="content" source="media/add-api/create-azure-functions-vscode-3.png" alt-text="從 vanilla-basic 建立新的存放庫":::

1. 按 **Shift + F5** 以停止調試會話。

### <a name="call-the-api-from-the-application"></a>從應用程式呼叫 API

當部署至 Azure 時，對 API 的要求會自動路由傳送至函式應用程式，以取得傳送至路由的要求 `api` 。 在本機工作時，您必須將應用程式設定設定為對本機 API 的 proxy 要求。

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]

#### <a name="update-html-files-to-access-the-api"></a>更新 HTML 檔案以存取 API

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
       <p>Loading content from the API: <b id="name">...</b></p>
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

1. 按 **F5** 以啟動 API 專案。

1. 按 **F1** 鍵，然後選擇 [Live Server： **開啟即時伺服器]** ，以啟動即時伺服器。

    您現在應該會在網頁中看到 API 訊息。

   :::image type="content" source="media/add-api/create-azure-functions-vscode-4.png" alt-text="從 vanilla-basic 建立新的存放庫":::

   > [!NOTE]
   > 您可以使用其他 HTTP 伺服器或 Proxy 來提供 `index.html` 檔案。 將無法從 `file:///` 存取 `index.html`。

1. 按 **Shift + F5** 以停止 API 專案。

### <a name="commit-and-push-your-changes-to-github"></a>認可及推送您對 GitHub 的變更

使用 Visual Studio Code 來認可您對遠端 Git 存放庫的變更並加以推送。

1. 按 **F1** 鍵以開啟命令選擇區
1. 輸入 **Git：全部認可**
1. 新增認可訊息，然後按 **enter**
1. 按下 **F1**
1. 在 **Git： push** 中輸入，然後按 **enter** 鍵

## <a name="create-a-static-web-app"></a>建立靜態 Web 應用程式

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)
1. 按一下 [建立資源]
1. 搜尋 **靜態 Web 應用程式**
1. 按一下 [ **靜態 Web 應用程式 (預覽])**
1. 按一下 [建立] 

接下來，新增應用程式特定的設定。

1. 選取您的 _Azure 訂用帳戶_
1. 選取或建立新的 [資源群組]
1. 將應用程式命名為 **my-vanilla-api** 。
1. 選取最靠近您的 [區域]
1. 選取 [免費] _SKU_
1. 按一下 [使用 GitHub 登入] 按鈕，並向 GitHub 進行驗證
1. 選取您慣用的 [組織]
1. 從 [存放庫] 下拉式選單中，選取 **my-vanilla-api**
1. 從 [分支] 下拉式選單中，選取 [主要]
1. 按 [下一步：組建 >] 按鈕，以編輯組建組態

接下來，新增下列組建詳細資料。

1. 輸入 **/** _應用程式位置_ 。
1. 在 [API 位置] 方塊中，輸入 **api** 。
1. 清除 [應用程式成品位置] 的預設值，並將此方塊保留空白。
1. 按一下 [檢閱 + 建立]。
1. 按一下 [建立] 按鈕

    一旦您按一下 [ _建立_ ] 按鈕，Azure 就會執行兩項作業。 首先，會建立基礎雲端服務以支援應用程式。 接下來，背景進程會開始建立及部署應用程式。

1. 按一下 [ **移至資源** ] 按鈕，以帶您前往 web 應用程式的 _[總覽_ ] 頁面。

    當應用程式是在背景中建立時，您可以按一下包含連結的橫幅來查看組建狀態。

    :::image type="content" source="media/add-api/github-action-flag.png" alt-text="從 vanilla-basic 建立新的存放庫":::

1. 部署完成後，ou 可以按一下 [ _總覽_ ] 頁面上顯示的 _URL_ 連結，以流覽至 web 應用程式。

    :::image type="content" source="media/add-api/static-app-url-from-portal.png" alt-text="從 vanilla-basic 建立新的存放庫":::

## <a name="clean-up-resources"></a>清除資源

如果您不想要保留此應用程式以供進一步使用，可以使用下列步驟來刪除 Azure 靜態 Web 應用程式及其相關資源。

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)
1. 在頂端的搜尋列中，輸入 **資源群組**
1. 按一下 [資源群組]
1. 選取 **myResourceGroup**
1. 在 [myResourceGroup] 頁面上，確定所列出的資源是您想要刪除的項目。
1. 選取 [刪除]
1. 在文字方塊中輸入 **myResourceGroup**
1. 選取 [刪除] 。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [設定應用程式設定](./application-settings.md)
