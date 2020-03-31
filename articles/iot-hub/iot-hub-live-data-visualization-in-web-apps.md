---
title: Web 應用中 IoT 中心資料的即時資料視覺化
description: 使用 Web 應用程式視覺化從感應器收集併發送到 Iot 中心的溫度和濕度資料。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 05/31/2019
ms.author: robinsh
ms.openlocfilehash: 138e077f7b47fa9f38a4710db95eb7208cef78e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78675325"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>在 Web 應用程式中視覺化來自 Azure IoT 中心的即時感應器資料

![端對端圖表](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>您學到什麼

在本教程中，您將瞭解如何使用本地電腦上運行的 node.js Web 應用來視覺化 IoT 中心接收的即時感應器資料。 在本地運行 Web 應用後，可以選擇按照步驟在 Azure 應用服務中託管 Web 應用。 如果您想嘗試使用 Power BI 將 IoT 中樞的資料視覺化，請參閱[使用 Power BI 將 Azure IoT 中樞的即時感應器資料視覺化](iot-hub-live-data-visualization-in-power-bi.md)。

## <a name="what-you-do"></a>您要做什麼

* 將消費者組添加到 IoT 中心，Web 應用程式將使用該組讀取感應器資料
* 從 GitHub 下載 Web 應用代碼
* 檢查 Web 應用代碼
* 配置環境變數以保存 Web 應用所需的 IoT 中心專案
* 在開發電腦上運行 Web 應用
* 打開網頁，查看 IoT 中心的即時溫度和濕度資料
* （可選）使用 Azure CLI 在 Azure 應用服務中託管 Web 應用

## <a name="what-you-need"></a>您需要什麼

* 完成[樹莓派線上模擬器](iot-hub-raspberry-pi-web-simulator-get-started.md)教程或設備教程之一;例如，[樹莓派與node.js。](iot-hub-raspberry-pi-kit-node-get-started.md) 這些要求包括以下要求：

  * 作用中的 Azure 訂用帳戶
  * 您訂用帳戶之下的 IoT 中樞
  * 將訊息傳送到 IoT 中樞的用戶端應用程式

* [下載 Git](https://www.git-scm.com/downloads)

* 本文中的步驟假定 Windows 開發電腦;因此，將採用 Windows 開發電腦。但是，您可以輕鬆地在首選 shell 中的 Linux 系統上執行這些步驟。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

執行下列命令，將適用於 Azure CLI 的 Microsoft Azure IoT 擴充功能新增至您的 Cloud Shell 執行個體。 IoT 擴充功能可將 IoT 中樞、IoT Edge 和 IoT 裝置佈建服務的特定命令新增至 Azure CLI。

```azurecli-interactive
az extension add --name azure-iot
```

## <a name="add-a-consumer-group-to-your-iot-hub"></a>將取用者群組新增至 IoT 中樞

[消費者組](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers)在事件流中提供獨立視圖，使應用和 Azure 服務能夠獨立使用來自同一事件中心終結點的資料。 在本節中，您將消費者組添加到 IoT 中心的內置終結點，Web 應用將使用該終結點從該終結點讀取資料。

運行以下命令將消費者組添加到 IoT 中心的內置終結點：

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

記下您選擇的名稱，本教程稍後將需要它。

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>獲取 IoT 中心的服務連接字串

使用多個預設訪問策略創建 IoT 中心。 其中一個策略是**服務**策略，它為服務提供足夠的許可權來讀取和寫入 IoT 中心端點。 運行以下命令以獲取符合服務策略的 IoT 中心的連接字串：

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

連接字串應類似于以下內容：

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

記下服務連接字串，本教程稍後將需要它。

## <a name="download-the-web-app-from-github"></a>從 GitHub 下載 Web 應用

打開命令視窗，並輸入以下命令從 GitHub 下載示例並更改為示例目錄：

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>檢查 Web 應用代碼

從 Web 應用節點-iot-hub-資料視覺化目錄中，在您喜愛的編輯器中打開 Web 應用。 下面顯示了 VS 代碼中查看的檔結構：

![Web 應用檔結構](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

花點時間檢查以下檔：

* **Server.js**是一個服務端腳本，用於初始化 Web 通訊端和事件中心包裝類。 它提供了對事件中心包裝器類的回檔，該類用於將傳入消息廣播到 Web 通訊端。

* **事件中心-閱讀器.js**是一個服務端腳本，使用指定的連接字串和消費人組連接到 IoT 中心的內置終結點。 它從傳入消息上的中繼資料中提取 DeviceId 和 EnqueuedTimeUtc，然後使用伺服器.js 註冊的回檔方法中繼消息。

* **圖表設備資料.js**是一個用戶端腳本，用於偵聽 Web 通訊端，跟蹤每個 DeviceId，並存儲每個設備最後 50 個點的傳入資料。 然後，它將所選設備資料繫結到圖表物件。

* **Index.html**處理網頁的 UI 佈局，並引用用戶端邏輯所需的腳本。

## <a name="configure-environment-variables-for-the-web-app"></a>為 Web 應用配置環境變數

要從 IoT 中心讀取資料，Web 應用需要 IoT 中心的連接字串及其應通讀的消費群體的名稱。 它在 server.js 中的以下行中從進程環境中獲取這些字串：

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

使用以下命令在命令視窗中設置環境變數。 將預留位置值替換為 IoT 中心的服務連接字串以及以前創建的消費者組的名稱。 不要引用字串。

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>執行 Web 應用程式

1. 確保設備正在運行併發送資料。

2. 在命令視窗中，運行以下行以下載並安裝引用的包並啟動網站：

   ```cmd
   npm install
   npm start
   ```

3. 您應該在主控台中看到指示 Web 應用已成功連接到 IoT 中心並在埠 3000 上偵聽的輸出：

   ![在主控台上啟動 Web 應用](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>打開網頁以查看來自 IoT 中心的資料

開啟瀏覽器並前往 `http://localhost:3000`。

在 **"選擇設備**清單"中，選擇設備以查看設備發送到 IoT 中心的最後 50 個溫度和濕度資料點的運行圖。

![顯示即時溫度和溼度的 Web 應用程式 (web app) 頁面](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

您還應在主控台中看到顯示 Web 應用正在廣播到瀏覽器用戶端的消息的輸出：  

![主控台上的 Web 應用廣播輸出](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>在應用服務中託管 Web 應用

[Azure 應用服務的 Web 應用功能](https://docs.microsoft.com/azure/app-service/overview)提供了一個平臺即服務 （PAAS），用於託管 Web 應用程式。 Azure 應用服務中託管的 Web 應用程式可以受益于強大的 Azure 功能，如附加安全性、負載平衡和可伸縮性，以及 Azure 和合作夥伴 DevOps 解決方案（如連續部署、包管理等）。 Azure 應用服務支援以許多常用語言開發並在 Windows 或 Linux 基礎結構上部署的 Web 應用程式。

在本節中，您可以在應用服務中預配 Web 應用，並使用 Azure CLI 命令將代碼部署到該應用。 您可以找到[az Webapp](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest)文檔中使用的命令的詳細資訊。 在開始之前，請確保已完成將[資源組添加到 IoT 中心](#add-a-consumer-group-to-your-iot-hub)、[獲取 IoT 中心的服務連接字串](#get-a-service-connection-string-for-your-iot-hub)以及[從 GitHub 下載 Web 應用](#download-the-web-app-from-github)的步驟。

1. [應用服務方案](https://docs.microsoft.com/azure/app-service/overview-hosting-plans)為應用服務中託管的應用定義一組計算資源以運行。 在本教程中，我們使用開發人員/免費層來託管 Web 應用。 使用免費套餐，Web 應用將與其他應用服務應用（包括其他客戶的應用）共用 Windows 資源運行。 Azure 還提供應用服務方案，用於在 Linux 計算資源上部署 Web 應用。 如果您已有要使用的應用服務方案，則可以跳過此步驟。

   要使用 Windows 免費層創建應用服務方案，請運行以下命令。 使用 IoT 中心位於的同一資源組。 服務方案名稱可以包含大寫字母和小寫字母、數位和連字號。

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. 現在，在應用服務方案中預配 Web 應用。 該`--deployment-local-git`參數允許從本地電腦上的 Git 存儲庫上載和部署 Web 應用代碼。 Web 應用名稱必須全域唯一，並可以包含大寫字母和小寫字母、數位和連字號。 請務必為`--runtime`參數指定 Node 版本 10.6 或更高版本，具體取決於您正在使用的 Node.js 運行時的版本。 可以使用 命令`az webapp list-runtimes`獲取受支援的運行時清單。

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --runtime "node|10.6" --deployment-local-git
   ```

3. 現在添加指定 IoT 中心連接字串和事件中心消費者組的環境變數的應用程式設定。 單個設置在參數中分隔的空間`-settings`。 使用 IoT 中心的服務連接字串以及您在本教程中以前創建的消費者組。 不要引用這些值。

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString=<your IoT hub connection string>
   ```

4. 為 Web 應用啟用 Web 通訊端協定，並將 Web 應用設置為僅接收 HTTPS 請求（HTTP 要求重定向到 HTTPS）。

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. 要將代碼部署到應用服務，將使用[使用者級別的部署憑據](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials)。 使用者級別的部署憑據與 Azure 憑據不同，用於將 Git 本地和 FTP 部署到 Web 應用。 設置後，它們對 Azure 帳戶中所有訂閱中的所有應用服務應用都有效。 如果以前設置了使用者級部署憑據，則可以使用這些憑據。

   如果您以前未設置使用者級部署憑據或不記得密碼，請運行以下命令。 部署使用者名在 Azure 中必須是唯一的，並且不能包含本地 Git 推送的"*"符號。 系統提示您時，輸入並確認您的新密碼。 密碼長度必須至少為 8 個字元，包含下列三個元素其中兩個：字母、數字及符號。

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. 獲取用於將代碼推送到應用服務的 Git URL。

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. 向克隆添加一個遙控器，該遙控器引用應用服務中的 Web 應用的 Git 存儲庫。 對於\<Git\>克隆 URL，請使用上一步驟中返回的 URL。 在命令視窗中運行以下命令。

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. 要將代碼部署到應用服務，請在命令視窗中輸入以下命令。 如果系統提示您輸入憑據，請輸入在步驟 5 中創建的使用者級部署憑據。 請確保推送到應用服務遠端的主分支。

    ```cmd
    git push webapp master:master
    ```

9. 部署的進度將在命令視窗中更新。 成功部署將以類似于以下輸出的行結束：

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  master -> master
    ```

10. 運行以下命令以查詢 Web 應用的狀態並確保其運行：

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. 在瀏覽器中，瀏覽至 `https://<your web app name>.azurewebsites.net` 。 顯示的網頁類似于您運行本地 Web 應用時看到的網頁。 假設您的設備正在運行併發送資料，您應該會看到設備發送的 50 個最新溫度和濕度讀數的運行圖。

## <a name="troubleshooting"></a>疑難排解

如果遇到此示例的任何問題，請嘗試以下各節中的步驟。 如果您仍有問題，請在本主題底部向我們發送回饋。

### <a name="client-issues"></a>客戶問題

* 如果設備未顯示在清單中，或者未繪製圖形，請確保設備代碼在設備上運行。

* 在瀏覽器中，打開開發人員工具（在許多瀏覽器中 F12 金鑰將打開它），然後找到主控台。 查找列印的任何警告或錯誤。

* 您可以在 /js/聊天設備資料.js 中調試用戶端腳本。

### <a name="local-website-issues"></a>本地網站問題

* 在啟動節點的視窗中觀看主控台輸出的輸出。

* 調試伺服器代碼，特別是伺服器.js 和 /腳本/事件-中心-讀取器.js。

### <a name="azure-app-service-issues"></a>Azure 應用服務問題

* 在 Azure 門戶中，轉到 Web 應用。 在左側窗格中的 **"監視"** 下，選擇**應用服務日誌**。 將**應用程式日誌記錄（檔案系統）** 打開，將**級別**設置為錯誤，然後選擇 **"保存**"。 然後打開**日誌流**（在**監視**下）。

* 在 Azure 門戶中的 Web 應用中，**在"開發工具**"下選擇**主控台**，並使用`node -v``npm -v`和 驗證節點和 npm 版本。

* 如果您看到有關找不到包的錯誤，則可能已按順序運行這些步驟。 部署網站（使用`git push`） 時，應用服務將`npm install`運行 ，該服務基於它配置的當前版本的節點運行。 如果稍後在配置中更改，則需要對代碼進行毫無意義的更改，然後再次推送。

## <a name="next-steps"></a>後續步驟

您已成功使用 Web 應用程式 (web app) 將 IoT 中樞的即時感應器資料視覺化。

有關從 Azure IoT 中心視覺化資料的另一種方法，請參閱[使用 Power BI 視覺化來自 IoT 中心的即時感應器資料](iot-hub-live-data-visualization-in-power-bi.md)。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
