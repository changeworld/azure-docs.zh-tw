---
title: Web 應用程式的 IoT 中樞資料即時資料視覺效果
description: 使用 Web 應用程式，將收集自感應器並傳送至 IoT 中樞的溫度和溼度資料視覺化。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 05/31/2019
ms.author: robinsh
ms.custom:
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: c6452d1c5c9792e8d021838635686e8621629ff2
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92146681"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>使用 Web 應用程式，將來自 Azure IoT 中樞的即時感應器資料視覺化

![端對端圖表](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>您學到什麼

在本教學課程中，您會了解如何在本機電腦上執行 node.js Web 應用程式，將 IoT 中樞收到的即時感應器資料視覺化。 在本機執行 Web 應用程式之後，即可選擇性地遵循下列步驟在 Azure App Service 中裝載 Web 應用程式。 如果您想嘗試使用 Power BI 將 IoT 中樞的資料視覺化，請參閱[使用 Power BI 將 Azure IoT 中樞的即時感應器資料視覺化](iot-hub-live-data-visualization-in-power-bi.md)。

## <a name="what-you-do"></a>您要做什麼

* 將取用者群組新增至 IoT 中樞，以供 Web 應用程式讀取感應器資料
* 從 GitHub 下載 Web 應用程式程式碼
* 檢查 Web 應用程式程式碼
* 設定環境變數以保存 Web 應用程式所需的 IoT 中樞成品
* 在開發電腦上執行 Web 應用程式
* 開啟網頁，以查看 IoT 中樞的即時溫度和溼度資料
* (選擇性) 使用 Azure CLI 將 Web 應用程式裝載在 Azure App Service 中

## <a name="what-you-need"></a>您需要什麼

* 完成 [Raspberry Pi 線上模擬器](iot-hub-raspberry-pi-web-simulator-get-started.md)教學課程，或其中一個裝置教學課程；例如，[Raspberry Pi with node.js](iot-hub-raspberry-pi-kit-node-get-started.md)。 其中涵蓋下列需求：

  * 作用中的 Azure 訂用帳戶
  * 您訂用帳戶之下的 IoT 中樞
  * 將訊息傳送到 IoT 中樞的用戶端應用程式

* [下載 Git](https://www.git-scm.com/downloads)

* 本文步驟假設您使用 Windows 開發電腦；不過您也可以透過慣用的 Shell，輕鬆在 Linux 系統上執行這些步驟。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

執行下列命令，將適用於 Azure CLI 的 Microsoft Azure IoT 擴充功能新增至您的 Cloud Shell 執行個體。 IoT 擴充功能可將 IoT 中樞、IoT Edge 和 IoT 裝置佈建服務的特定命令新增至 Azure CLI。

```azurecli-interactive
az extension add --name azure-iot
```

## <a name="add-a-consumer-group-to-your-iot-hub"></a>將取用者群組新增至 IoT 中樞

[取用者群組](../event-hubs/event-hubs-features.md#event-consumers)可提供事件資料流的獨立檢視，讓應用程式和 Azure 服務能夠獨立取用來自相同事件中樞端點的資料。 在本節中，您會將取用者群組新增至 IoT 中樞的內建端點，以供 Web 應用程式讀取資料。

執行下列命令，將取用者群組新增至 IoT 中樞的內建端點：

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

記下所選的名稱，以在本教學課程稍後使用。

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>取得 IoT 中樞的服務連接字串

IoT 中樞在建立時即含有數個預設存取原則。 其中一個原則是**服務**原則，其提供可讓服務讀取和寫入 IoT 中樞端點的足夠權限。 執行下列命令，以取得符合服務原則的 IoT 中樞連接字串：

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

連接字串應該如下所示：

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

記下服務連接字串，以在本教學課程中稍後使用。

## <a name="download-the-web-app-from-github"></a>從 GitHub 下載 Web 應用程式

開啟命令視窗並輸入下列命令，即可從 GitHub 下載範例，然後切換至範例目錄：

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>檢查 Web 應用程式程式碼

從 web-apps-node-iot-hub-data-visualization 目錄，使用慣用的編輯器開啟 Web 應用程式。 下列為在 VS Code 中檢視的檔案結構：

![Web 應用程式檔案結構](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

請花點時間檢查下列檔案：

* **Server.js** 是用來初始化 Web 通訊端和事件中樞包裝函式類別的服務端指令碼。 這個指令碼可提供對事件中樞包裝函式類別的回呼，該類別即可使用此回呼將傳入訊息廣播至 Web 通訊端。

* **Event-hub-reader** 是服務端指令碼，其會使用所指的連接字串和取用者群組來連接 IoT 中樞的內建端點。 這個指令碼會從傳入訊息的中繼資料擷取 DeviceId 和 EnqueuedTimeUtc，然後使用 node.js 註冊的回呼方法來轉送訊息。

* **Chart-device-data.js** 是用戶端指令碼，其會接聽 Web 通訊端、追蹤每個 DeviceId，並儲存每個裝置最後 50 個點的傳入資料， 然後將選取的裝置資料繫結至圖表物件。

* **Index.html** 可處理網頁的 UI 版面配置，並參考用戶端邏輯的必要指令碼。

## <a name="configure-environment-variables-for-the-web-app"></a>針對 Web 應用程式設定環境變數

為讀取 IoT 中樞的資料，Web 應用程式需要使用 IoT 中樞的連接字串，以及用於讀取的取用者群組名稱。 Web 應用程式會從程序環境的 server.js 下列幾行取得這些字串：

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

使用下列命令，在命令視窗中設定環境變數。 將預留位置值取代為 IoT 中樞服務連接字串，以及先前建立的取用者群組名稱。 請勿將字串加上引號。

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>執行 Web 應用程式

1. 請確認裝置正在執行並傳送資料。

2. 在命令視窗中執行下列程式碼行，以下載並安裝參考的套件，然後啟動網站：

   ```cmd
   npm install
   npm start
   ```

3. 主控台應該會顯示輸出，其指出 Web 應用程式已成功連線到 IoT 中樞並於連接埠 3000 上接聽：

   ![Web 應用程式已在主控台中啟動](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>開啟網頁查看 IoT 中樞的資料

使用瀏覽器開啟 `http://localhost:3000`。

在 [選取裝置] 清單中，選取裝置，即可查看執行中的繪圖，其顯示裝置傳送給 IoT 中樞的最後 50 個溫度和濕度資料點。

![顯示即時溫度和溼度的 Web 應用程式 (web app) 頁面](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

您也應該會在主控台中看到輸出，其顯示 Web 應用程式正在向瀏覽器用戶端廣播的訊息：  

![主控台上的 Web 應用程式廣播輸出](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>在 App Service 中裝載 Web 應用程式

[Azure App Service 中的 Web Apps 功能](../app-service/overview.md)提供用來裝載 Web 應用程式的平台即服務 (PAAS)。 當將 Web 應用程式裝載於 Azure App Service 中時可獲得強大的 Azure 功能獲益，例如額外的安全性、負載平衡、可擴縮性，以及 Azure 和合作夥伴的 DevOps 解決方案 (例如持續部署、套件管理等)。 Azure App Service 支援以多種熱門語言開發並部署在 Windows 或 Linux 基礎結構上的 Web 應用程式。

在本節中，您會使用 Azure CLI 命令在 App Service 中佈建 Web 應用程式，並將程式碼部署至其中。 [az webapp](/cli/azure/webapp?view=azure-cli-latest) 文件包含所使用命令的詳細資料。 開始之前，請確認已完成[將資源群組新增至 IoT 中樞](#add-a-consumer-group-to-your-iot-hub)、[取得 IoT 中樞的服務連接字串](#get-a-service-connection-string-for-your-iot-hub)，以及[從 GitHub 下載 Web 應用程式](#download-the-web-app-from-github)的步驟。

1. [App Service 方案](../app-service/overview-hosting-plans.md)會定義一組計算資源以供裝載於 App Service 中的應用程式執行。 在本教學課程中，我們會使用開發人員/免費層來裝載 Web 應用程式。 使用免費層時， Web 應用程式會和其他 App Service 應用程式一起在共用的 Windows 資源上執行，包括其他客戶的應用程式。 Azure 也提供可在 Linux 計算資源上部署 Web 應用程式的 App Service 方案。 如果已有想要使用的 App Service 方案，則可略過此步驟。

   若要使用 Windows 免費層來建立 App Service 方案，請執行下列命令。 使用 IoT 中樞所在的相同資源群組。 服務方案可包含大小寫字母、數字與連字號。

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. 現在，請在 App Service 方案中佈建 Web 應用程式。 您可使用 `--deployment-local-git` 參數，在本機電腦上透過 Git 存放庫來上傳和部署 Web 應用程式程式碼。 Web 應用程式名稱必須是全域唯一名稱，並可包含大小寫字母、數字與連字號。 請務必為 `--runtime` 參數指定 Node 版本 10.6 或更新版本 (依據正在使用的 Node.js 執行階段版本而定)。 您可使用 `az webapp list-runtimes` 命令來取得支援的執行階段清單。

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --runtime "node|10.6" --deployment-local-git
   ```

3. 現在，請為環境變數新增應用程式設定，以指定 IoT 中樞連接字串和事件中樞取用者群組。 您可在 `-settings` 參數中以空格分隔個別設定。 使用 IoT 中樞服務連接字串，以及先前在本教學課程中建立的取用者群組。 請勿將值加上引號。

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString="<your IoT hub connection string>"
   ```

4. 啟用 Web 應用程式的 Web 通訊端通訊協定，並將 Web 應用程式設定為只接收 HTTPS 要求 (系統會將 HTTP 要求重新導向至 HTTPS)。

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. 若要將程式碼部署至 App Service，請利用[使用者層級部署認證](../app-service/deploy-configure-credentials.md)。 使用者層級部署認證可用於 Web 應用程式的 Git 本機和 FTP 部署，其與 Azure 認證不同。 一旦設定使用者層級部署認證之後，該認證在 Azure 帳戶中每個訂用帳戶的所有 App Service 應用程式均有效。 如果先前已設定使用者層級部署認證，則可直接使用。

   如果先前尚未設定使用者層級部署認證，或不記得密碼，請執行下列命令。 部署使用者名稱在 Azure 中必須是唯一名稱，且不得包含 ‘@’ 符號才能用於本機 Git 推送。 收到系統提示時，請輸入新密碼並確認。 密碼長度必須至少為 8 個字元，包含下列三個元素其中兩個：字母、數字及符號。

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. 取得用來將程式碼推送至 App Service 的 Git URL。

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. 將遠端新增至複本，以在 App Service 中參考 Web 應用程式的 Git 存放庫。 若是 \<Git clone URL\> ，請使用上一個步驟中所傳回的 URL。 在命令視窗中執行下列命令。

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. 若要將程式碼部署至 App Service，請在命令視窗中輸入下列命令。 如果系統提示提供認證，請輸入在步驟 5 中建立的使用者層級部署認證。 請確認推送至 App Service 遠端的主分支。

    ```cmd
    git push webapp master:master
    ```

9. 命令視窗會更新部署進度。 如果部署成功，則會看到類似下列幾行輸出：

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  master -> master
    ```

10. 執行下列命令查詢 Web 應用程式的狀態，並確認其正在執行：

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. 在瀏覽器中，瀏覽至 `https://<your web app name>.azurewebsites.net` 。 此網頁類似於在本機執行 Web 應用程式時所看到的網頁。 假設裝置正在執行並傳送資料，則應該會看到正在執行的繪圖，其顯示裝置傳送的最近 50 個溫度和濕度讀數。

## <a name="troubleshooting"></a>疑難排解

如果遇到本範例的任何問題，請嘗試下列各節中的步驟。 如果仍然遇到問題，請在本主題的底部傳送意見反應給我們。

### <a name="client-issues"></a>用戶端問題

* 如果裝置未出現在清單中，或未繪製任何圖形，請確認裝置程式碼正在裝置上執行。

* 在瀏覽器中開啟開發人員工具 (許多瀏覽器均可使用 F12 鍵開啟)，然後尋找主控台。 尋找其中是否有列印任何警告或錯誤。

* 您可對 /js/chat-device-data.js 中的用戶端指令碼進行偵錯。

### <a name="local-website-issues"></a>本機網站問題

* 在啟動 Node 以取得主控台輸出的視窗中，仔細檢查輸出。

* 對伺服器程式碼進行偵錯，特別是 server.js 和 /scripts/event-hub-reader.js。

### <a name="azure-app-service-issues"></a>Azure App Service 問題

* 在 Azure 入口網站中，移至 Web 應用程式。 在左窗格的 [監視] 下，選取 [App Service 記錄]。 開啟 [應用程式記錄 (檔案系統)]，並將 [層級] 設為 [錯誤]，然後選取 [儲存]。 接著，開啟 [監視] 下的 [記錄資料流]。

* 從 Azure 入口網站的 Web 應用程式中，選取 [開發工具] 下的 [主控台]，然後使用 `node -v` 和 `npm -v` 來驗證 Node 和 npm 版本。

* 如果出現找不到套件的錯誤，您可能未按照順序執行步驟。 當部署網站 (使用 `git push`) 時，App Service 會根據目前已設定的 Node 版本來執行 `npm install`。 如果之後變更設定，則必須對程式碼進行細微變更，然後再推送一次。

## <a name="next-steps"></a>後續步驟

您已成功使用 Web 應用程式 (web app) 將 IoT 中樞的即時感應器資料視覺化。

如需將 Azure IoT 中樞資料視覺化的其他方法，請參閱[使用 Power BI 將來自 IoT 中樞的即時感應器資料視覺化](iot-hub-live-data-visualization-in-power-bi.md)。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]