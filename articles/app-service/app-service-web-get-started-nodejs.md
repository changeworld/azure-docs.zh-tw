---
title: 快速入門：建立 Node.js Web 應用程式
description: 在短短幾分鐘內將您的第一個 Node.js Hello World 部署至 Azure App Service。 您可以使用 Visual Studio Code 進行部署，這是部署至 App Service 的眾多方式之一。
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 03/04/2020
ms.custom: mvc, devcenter, seodec18
ms.openlocfilehash: 4f08068e0920eb8a9e93fbbf91cb352bb8860fe3
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2020
ms.locfileid: "84905913"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>在 Azure 中建立 Node.js Web 應用程式 

使用 Visual Studio Code 在本機建立 Node.js/Express 應用程式，然後將應用程式部署至雲端，以開始使用 Azure App Service。 由於您是使用免費的 App Service 層，因此不需支付任何費用，就能完成本快速入門。

## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension)。
- [Node.js 和 npm](https://nodejs.org)。 執行命令 `node --version`，確認已安裝 Node.js。
- [Visual Studio Code](https://code.visualstudio.com/) \(英文\)。
- [適用於 Visual Studio Code 的 Azure App Service 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)。

## <a name="clone-and-run-a-local-nodejs-application"></a>複製並建立本機 Node.js 應用程式

1. 在您的本機電腦上，開啟終端機並複製範例存放庫：

    ```bash
    git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
    ```

1. 瀏覽至新的應用程式資料夾：

    ```bash
    cd nodejs-docs-hello-world
    ```

1. 啟動應用程式以在本機進行測試：

    ```bash
    npm start
    ```
    
1. 開啟瀏覽器並巡覽至 `http://localhost:1337`。 瀏覽器應該會顯示「Hello World!」。

1. 在終端機中按 **Ctrl**+**C** 以停止伺服器。

> [!div class="nextstepaction"]
> [我遇到問題](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-app-to-azure"></a>將應用程式部署至 Azure

在本節中，您會使用 VS Code 和 Azure App Service 擴充功能將 Node.js 應用程式部署至 Azure。

1. 在終端機中，確定您位於 *nodejs-docs-hello-world* 資料夾中，然後使用下列命令啟動 Visual Studio Code：

    ```bash
    code .
    ```

1. 在 VS Code 活動列中，選取 Azure 標誌以顯示 [AZURE APP SERVICE]**** 總管。 選取 [登入 Azure...]**** 並遵循指示進行。 (如果遇到錯誤，請參閱 [Azure 登入疑難排解](#troubleshooting-azure-sign-in)。)登入之後，總管應會顯示您的 Azure 訂用帳戶名稱。

    ![登入 Azure](containers/media/quickstart-nodejs/sign-in.png)

1. 在 VS Code 的 [AZURE APP SERVICE]**** 總管中，選取藍色的向上箭頭圖示，即可將您的應用程式部署至 Azure。 (您也可以從 [命令選擇區]**** \(**Ctrl**+**Shift**+**P**\) 叫用相同命令，只要輸入「部署到 Web 應用程式」並選擇 [Azure App Service:**** 部署到 Web 應用程式])。

    ![部署到 Web 應用程式](containers/media/quickstart-nodejs/deploy.png)
        
1. 選擇 *nodejs-docs-hello-world* 資料夾。

1. 根據您要部署的作業系統，選擇一個建立選項：

    - Linux：選擇 [建立新的 Web 應用程式]****
    - Windows：選擇 [建立新的 Web 應用程式]...****[進階]

1. 輸入 Web 應用程式的全域唯一名稱，然後按 **Enter**。 該名稱在所有 Azure 中必須是唯一的，而且只能使用英數位元 ('A-z'、'a-z' 和 '0-9') 和連字號 ('-')。

1. 如果以 Linux 為，請在出現提示時選取 Node.js 版本。 建議使用 **LTS** 版本。

1. 如果以 Windows 為目標，請遵循其他提示：
    1. 選取 [建立新的資源群組]****，然後輸入資源群組的名稱，例如 `AppServiceQS-rg`。
    1. 針對作業系統選取 [Windows]****。
    1. 選取 [建立新的 App Service 方案]****，然後輸入方案的名稱 (例如 `AppServiceQS-plan`)，然後針對定價層選取 [F1 免費]****。
    1. 在出現 Application Insights 提示時，選擇 [暫時略過]****。
    1. 選擇您想要存取且接近您或接近資源的區域。

1. 在您回應所有提示之後，VS Code 會顯示要為其通知快顯中應用程式建立的 Azure 資源。

    在部署至 Linux 時，請於系統提示您要更新組態以在目標 Linux 伺服器上執行 `npm install` 時，選取 [是]****。

    ![提示您要在目標 Linux 伺服器上更新組態](containers/media/quickstart-nodejs/server-build.png)

1. 請在出現 [一律將工作區 "nodejs-docs-hello-world" 部署至 \應用程式名稱\]**** 提示時，選取 [是]****。 選取 [是]**** 會指示 VS Code 自動讓後續部署使用相同的 App Service Web 應用程式作為目標。

1. 如果是部署至 Linux，請在部署完成時，選取提示中的 [瀏覽網站]****，以檢視您剛才部署的 Web 應用程式。 瀏覽器應該會顯示「Hello World!」

1. 如果是部署至 Windows，您必須先設定 Web 應用程式的 Node.js 版本號碼：

    1. 在 VS Code 中展開新 App Service 的節點、以滑鼠右鍵按一下 [應用程式設定]****，然後選取 [新增設定...]****：

        ![新增應用程式設定命令](containers/media/quickstart-nodejs/add-setting.png)

    1. 輸入設定金鑰的 `WEBSITE_NODE_DEFAULT_VERSION`。
    1. 輸入設定值的 `10.15.2`。
    1. 以滑鼠右鍵按一 應用程式服務的節點，並選取 [重新啟動]****

        ![重新啟動應用程式服務命令](containers/media/quickstart-nodejs/restart.png)

    1. 以滑鼠右鍵再按一下應用程式服務的節點，然後選取 [瀏覽網站]****。

> [!div class="nextstepaction"]
> [我遇到問題](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="troubleshooting-azure-sign-in"></a>Azure 登入疑難排解

如果您在登入 Azure 時看到 **「找不到名稱為 [訂用帳戶識別碼] 的訂用帳戶」** 錯誤，可能是因為您位於 Proxy 後方，所以無法連線到 Azure API。 請使用 `export`，並以您終端機中的Proxy 資訊來設定 `HTTP_PROXY` 和 `HTTPS_PROXY` 環境變數。

```bash
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

如果設定環境變數無法更正問題，請選取上方的 [我遇到問題]**** 按鈕來聯繫我們。

### <a name="update-the-app"></a>更新應用程式

若要在 VS Code 中編輯以部署此應用程式的變更，您可以使用與先前相同程序並選擇現有的應用程式，而不是建立新的應用程式。

## <a name="viewing-logs"></a>檢視記錄

您可以直接在 VS Code 輸出視窗中，從應用程式中檢視記錄輸出 (`console.log`的呼叫)。

1. 在 **AZURE APP SERVICE** 總管中，以滑鼠右鍵按一下應用程式，然後選擇 [開始串流記錄]****。

    ![啟動串流記錄](containers/media/quickstart-nodejs/view-logs.png)

1. 出現提示時，請選擇啟用記錄，然後重新啟動應用程式。 重新啟動應用程式之後，VS Code 的輸出視窗會隨即開啟，並且連線至記錄串流。 

    ![啟用記錄並重新啟動](containers/media/quickstart-nodejs/enable-restart.png)

1. 幾秒鐘之後，輸出視窗中會顯示您已連線至記錄串流服務的訊息。 您可以在瀏覽器中重新整理頁面，以產生更多的輸出活動。

    <pre>
    Connecting to log stream...
    2020-03-04T19:29:44  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours.
    Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).    
    </pre>

> [!div class="nextstepaction"]
> [我遇到問題](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>後續步驟

恭喜，您已成功完成本快速入門！

> [!div class="nextstepaction"]
> [教學課程：使用 MongoDB 的 Node.js 應用程式](app-service-web-tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [設定 Node.js 應用程式](configure-language-nodejs.md)

查看其他 Azure 擴充功能。

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Docker 工具](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Azure CLI 工具](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) \(英文\)

或藉由安裝[適用於 Azure 的 Node Pack](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) 擴充功能來取得這所有項目。

