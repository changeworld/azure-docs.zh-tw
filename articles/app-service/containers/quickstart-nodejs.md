---
title: 快速入門：建立 Linux Node.js 應用程式
description: 藉由將您的第一個 Node.js 應用程式部署至 App Service 中的 Linux 容器，在 Azure App Service 上開始使用 Linux 應用程式。
author: msangapu-msft
ms.author: msangapu
ms.date: 08/12/2019
ms.topic: quickstart
ms.devlang: javascript
ms.openlocfilehash: 52466bac083f78002a8208ba52ca7d1b951c4064
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82801479"
---
# <a name="create-a-nodejs-app-in-azure"></a>在 Azure 中建立 Node.js 應用程式

Azure App Service 提供可高度擴充、自我修復的 Web 裝載服務。 本快速入門會顯示如何將 Node.js 應用程式部署至 Azure App Service。

## <a name="prerequisites"></a>Prerequisites

如果您沒有 Azure 帳戶，[現在免費註冊 Azure](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) 即可獲得價值 200 美元的 Azure 點數，讓您可以試用各種服務組合。

您需要安裝 [Visual Studio Code](https://code.visualstudio.com/) 及 [Node.js 和 npm](https://nodejs.org/en/download) (Node.js 套件管理員)。

您也必須安裝 [Azure App Service 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)，才能在 Azure 平台即服務 (PaaS) 上建立、管理和部署 Linux Web Apps。

### <a name="sign-in"></a>登入

安裝擴充功能之後，請登入您的 Azure 帳戶。 在活動列中，選取 Azure 標誌以顯示 [AZURE APP SERVICE]  總管。 選取 [登入 Azure...]  並遵循指示進行。

![登入 Azure](./media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>疑難排解

如果您看到 **「找不到名稱為 [訂用帳戶識別碼] 的訂用帳戶**」錯誤，可能是因為您位於 Proxy 後方，所以無法連線到 Azure API。 請使用 `export`，並以您終端機中的Proxy 資訊來設定 `HTTP_PROXY` 和 `HTTPS_PROXY` 環境變數。

```sh
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

如果設定環境變數無法更正問題，請選取下方的 [我遇到問題]  按鈕來聯繫我們。

### <a name="prerequisite-check"></a>先決條件檢查

在繼續之前，請確定您已安裝並設定所有必要條件。

在 VS Code 中，您應該會在狀態列中看到您的 Azure 電子郵件地址，並在 [AZURE APP SERVICE]  總管畫面中看到您的訂用帳戶。

> [!div class="nextstepaction"]
> [我遇到問題](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=getting-started)

## <a name="create-your-nodejs-application"></a>建立您的 Node.js 應用程式

接下來，建立可部署至雲端的 Node.js 應用程式。 本快速入門會使用應用程式產生器，以 Scaffold 方式快速地從終端機產生應用程式。

> [!TIP]
> 如果您已完成 [ode.js 教學課程](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial)，您可以直接跳到[部署至 Azure](#deploy-to-azure)。

### <a name="scaffold-a-new-application-with-the-express-generator"></a>使用 Express 產生器以 Scaffold 方式產生新的應用程式

[Express](https://www.expressjs.com) 是用來建立和執行 Node.js 應用程式的熱門架構。 您可以透過 [Express 產生器](https://expressjs.com/en/starter/generator.html)工具，以 Scaffold 方式產生 (建立) 新的 Express 應用程式。 Express 產生器會以 npm 模組的形式提供，並可使用 npm 命令列工具 `npx` 直接執行 (不需安裝)。

```bash
npx express-generator myExpressApp --view pug --git
```

`--view pug --git` 參數會指示產生器使用 [pug](https://pugjs.org/api/getting-started.html) 範本引擎 (先前稱為 `jade`)，並建立 `.gitignore` 檔案。

若要安裝應用程式的所有相依性，請移至新的資料夾並執行 `npm install`。

```bash
cd myExpressApp
npm install
```

### <a name="run-the-application"></a>執行應用程式

接下來，請確定應用程式已執行。 在終端機中，藉由使用 `npm start` 命令啟動應用程式來啟動伺服器。

```bash
npm start
```

現在，開啟您的瀏覽器並流覽至 `http://localhost:3000`，您應該會在這裡看到如下的內容：

![執行 Express 應用程式](./media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [我遇到問題](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-to-azure"></a>部署至 Azure

在本節中，您會使用 VS Code 和 Azure App Service 擴充功能來部署 Node.js 應用程式。 本快速入門會使用最基本的部署模型，也就是您的應用程式會壓縮並部署至 Linux 上的 Azure Web 應用程式。

### <a name="deploy-using-azure-app-service"></a>使用 Azure App Service 部署

首先，在 VS Code 中開啟您的應用程式資料夾。

```bash
code .
```

在 [AZURE APP SERVICE]  總管中，選取藍色的向上箭頭圖示，即可將您的應用程式部署至 Azure。

![部署到 Web 應用程式](./media/quickstart-nodejs/deploy.png)

> [!TIP]
> 您也可以從 [命令選擇區]  \(CTRL + SHIFT + P\) 進行部署，只要輸入 [部署到 Web 應用程式] 並執行 **Azure App Service:Deploy to Web App** 命令即可。

1. 選擇您目前已開啟的 `myExpressApp` 目錄。

1. 選擇 [建立新的 Web 應用程式]  ，其預設會部署至 Linux 上的 App Service。

1. 輸入 Web 應用程式的全域唯一名稱，然後按 ENTER。 應用程式名稱的有效字元為 'a-z'、'0-9' 和 '-'。

1. 選擇您的 **Node.js 版本** (建議使用 LTS)。

    通知通道會顯示要為應用程式建立的 Azure 資源。

1. 出現要您更新設定的提示時，請選取 [是]  ，如此才能在目標伺服器上執行 `npm install`。 接著會開始部署您的應用程式。

    ![已設定的部署](./media/quickstart-nodejs/server-build.png)

1. 當部署開始時，系統會提示您更新工作區，好讓之後的部署能自動鎖定相同的 App Service Web 應用程式。 請選擇 [是]  以確保您的變更會部署到正確的應用程式。

    ![已設定的部署](./media/quickstart-nodejs/save-configuration.png)

> [!TIP]
> 請確定您的應用程式正在 PORT 環境變數所提供的連接埠：`process.env.PORT` 上進行接聽。

### <a name="browse-the-app-in-azure"></a>在 Azure 中瀏覽應用程式

完成部署後，請選取提示中的 [流覽網站]  ，以檢視您剛部署的 Web 應用程式。

### <a name="troubleshooting"></a>疑難排解

如果您看到 **「您無權檢視此目錄或頁面。」** 的誤，則應用程式可能無法正確啟動。 請前往下一節，藉由檢視記錄輸出來尋找並修正錯誤。 如果您無法修正該錯誤，請選取下方的 [我遇到問題]  按鈕來聯繫我們。 為您服務是我們的榮幸！

> [!div class="nextstepaction"]
> [我遇到問題](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="update-the-app"></a>更新應用程式

若要部署此應用程式的變更，您可以使用相同程序並選擇現有的應用程式，而不是建立新的應用程式。

## <a name="viewing-logs"></a>檢視記錄

在本節中，您將了解如何從執行中的 App Service 應用程式檢視 (或使用 "tail" 取得) 記錄。 對應用程式中 `console.log` 進行的任何呼叫都會顯示在 Visual Studio Code 的 [輸出] 視窗中。

請在 [AZURE APP SERVICE]  總管畫面中找到應用程式，並以滑鼠右鍵按一下應用程式，然後選擇 [檢視串流記錄]  。

VS Code 的輸出視窗會隨即開啟，並且連線至記錄串流。

![檢視串流記錄](./media/quickstart-nodejs/view-logs.png)

![啟用記錄並重新啟動](./media/quickstart-nodejs/enable-restart.png)

幾秒鐘之後，您會看到指出您已連線至記錄串流服務的訊息。 重新整理頁面數次以查看更多活動。

<pre>
2019-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
2019-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
2019-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
2019-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
</pre>

> [!div class="nextstepaction"]
> [我遇到問題](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>後續步驟

恭喜，您已成功完成本快速入門！

接下來，請參閱其他 Azure 擴充功能。

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Docker 工具](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Azure CLI 工具](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) \(英文\)

或藉由安裝[適用於 Azure 的 Node Pack](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) 擴充功能來取得這所有項目。
