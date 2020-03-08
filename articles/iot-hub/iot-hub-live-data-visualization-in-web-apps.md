---
title: Web 應用程式中 IoT 中樞資料的即時資料視覺效果
description: 使用 web 應用程式，將從感應器收集並傳送至 Iot 中樞的溫度和濕度資料視覺化。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 05/31/2019
ms.author: robinsh
ms.openlocfilehash: 138e077f7b47fa9f38a4710db95eb7208cef78e3
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78675325"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>在 web 應用程式中將 Azure IoT 中樞的即時感應器資料視覺化

![端對端圖表](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>您學到什麼

在本教學課程中，您將瞭解如何使用在本機電腦上執行的 node.js web 應用程式，將您的 IoT 中樞所接收的即時感應器資料視覺化。 在本機執行 web 應用程式之後，您可以選擇性地遵循在 Azure App Service 中裝載 web 應用程式的步驟。 如果您想嘗試使用 Power BI 將 IoT 中樞的資料視覺化，請參閱[使用 Power BI 將 Azure IoT 中樞的即時感應器資料視覺化](iot-hub-live-data-visualization-in-power-bi.md)。

## <a name="what-you-do"></a>您要做什麼

* 將取用者群組新增至 web 應用程式將用來讀取感應器資料的 IoT 中樞
* 從 GitHub 下載 web 應用程式程式碼
* 檢查 web 應用程式程式碼
* 設定環境變數以保存 web 應用程式所需的 IoT 中樞成品
* 在您的開發電腦上執行 web 應用程式
* 開啟網頁以查看 IoT 中樞的即時溫度和濕度資料
* 選擇性使用 Azure CLI 在 Azure App Service 中裝載您的 web 應用程式

## <a name="what-you-need"></a>必要條件

* 完成[Raspberry Pi 線上](iot-hub-raspberry-pi-web-simulator-get-started.md)模擬器教學課程或其中一個裝置教學課程;例如，[使用 Node.js Raspberry Pi](iot-hub-raspberry-pi-kit-node-get-started.md)。 其中涵蓋下列需求：

  * 作用中的 Azure 訂用帳戶
  * 您訂用帳戶之下的 IoT 中樞
  * 將訊息傳送到 IoT 中樞的用戶端應用程式

* [下載 Git](https://www.git-scm.com/downloads)

* 本文中的步驟假設是 Windows 開發電腦;不過，您可以在您慣用的 shell 中，輕鬆地在 Linux 系統上執行這些步驟。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

執行下列命令，將適用於 Azure CLI 的 Microsoft Azure IoT 擴充功能新增至您的 Cloud Shell 執行個體。 IoT 擴充功能可將 IoT 中樞、IoT Edge 和 IoT 裝置佈建服務的特定命令新增至 Azure CLI。

```azurecli-interactive
az extension add --name azure-iot
```

## <a name="add-a-consumer-group-to-your-iot-hub"></a>將取用者群組新增至 IoT 中樞

取用[者群組](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers)會將獨立的視圖提供給事件資料流程，讓應用程式和 Azure 服務獨立取用來自相同事件中樞端點的資料。 在本節中，您會將取用者群組新增至 IoT 中樞的內建端點，以供 web 應用程式用來讀取資料。

執行下列命令，將取用者群組新增至 IoT 中樞的內建端點：

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

記下您選擇的名稱，稍後在本教學課程中需要用到它。

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>取得 IoT 中樞的服務連接字串

IoT 中樞會以數個預設存取原則來建立。 這類原則之一是**服務**原則，它會提供足夠的許可權讓服務讀取和寫入 IoT 中樞的端點。 執行下列命令，以取得符合服務原則之 IoT 中樞的連接字串：

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

連接字串看起來應該如下所示：

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

記下服務連接字串，稍後在本教學課程中需要用到它。

## <a name="download-the-web-app-from-github"></a>從 GitHub 下載 web 應用程式

開啟命令視窗，並輸入下列命令，從 GitHub 下載範例，並變更為範例目錄：

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>檢查 web 應用程式程式碼

從 web 應用程式---------------------------web 應用程式 以下顯示在 VS Code 中查看的檔案結構：

![Web 應用程式檔結構](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

請花點時間檢查下列檔案：

* **Server .js**是用來初始化 web 通訊端和事件中樞包裝函式類別的服務端腳本。 它會提供事件中樞包裝函式類別的回呼，供類別用來將傳入訊息廣播至 web 通訊端。

* **Event-hub-reader**是服務端腳本，會使用指定的連接字串和取用者群組，連接到 IoT 中樞的內建端點。 它會從傳入訊息上的中繼資料中解壓縮 DeviceId 和 EnqueuedTimeUtc，然後使用由 node.js 註冊的回呼方法轉送訊息。

* **Chart-device-data**是一種用戶端腳本，它會接聽 web 通訊端、追蹤每個 DeviceId，並針對每個裝置儲存最後50點的傳入資料。 然後，它會將選取的裝置資料系結至圖表物件。

* **Index. html**會處理網頁的 UI 版面配置，並參考用戶端邏輯的必要腳本。

## <a name="configure-environment-variables-for-the-web-app"></a>設定 web 應用程式的環境變數

若要從您的 IoT 中樞讀取資料，web 應用程式需要您的 IoT 中樞的連接字串，以及它應該要讀取的取用者組名。 它會從程式環境中的下列幾行取得這些字串：

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

使用下列命令，在命令視窗中設定環境變數。 將預留位置值取代為您的 IoT 中樞的服務連接字串，以及您先前建立的取用者組名。 不要以引號括住字串。

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>執行 Web 應用程式

1. 請確定您的裝置正在執行並傳送資料。

2. 在命令視窗中，執行下列幾行以下載並安裝參考的套件，並啟動網站：

   ```cmd
   npm install
   npm start
   ```

3. 您應該會在主控台中看到輸出，指出 web 應用程式已成功連線到您的 IoT 中樞並接聽埠3000：

   ![在主控台上啟動 Web 應用程式](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>開啟網頁以查看 IoT 中樞的資料

開啟瀏覽器以 `http://localhost:3000`。

在 [**選取裝置**] 清單中，選取您的裝置，以查看裝置傳送至 IoT 中樞的最後50溫度和濕度資料點的執行中繪圖。

![顯示即時溫度和溼度的 Web 應用程式 (web app) 頁面](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

您也應該會在主控台中看到輸出，其中顯示您的 web 應用程式正在廣播至瀏覽器用戶端的訊息：  

![主控台上的 Web 應用程式廣播輸出](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>在 App Service 中裝載 web 應用程式

[Azure App Service 的 Web Apps 功能](https://docs.microsoft.com/azure/app-service/overview)提供平臺即服務（PAAS）來裝載 Web 應用程式。 Azure App Service 中裝載的 Web 應用程式可受益于強大的 Azure 功能，例如額外的安全性、負載平衡和擴充性，以及 Azure 和合作夥伴 DevOps 解決方案（例如持續部署、套件管理等）。 Azure App Service 支援以許多熱門語言開發並部署在 Windows 或 Linux 基礎結構上的 web 應用程式。

在本節中，您會在 App Service 中布建 web 應用程式，並使用 Azure CLI 命令將程式碼部署到其中。 您可以在[az webapp](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest)檔中找到所使用命令的詳細資料。 開始之前，請確定您已完成將[資源群組新增至 iot 中樞](#add-a-consumer-group-to-your-iot-hub)的步驟、[取得 iot 中樞的服務連接字串](#get-a-service-connection-string-for-your-iot-hub)，以及[從 GitHub 下載 web 應用程式](#download-the-web-app-from-github)。

1. [App Service 計畫](https://docs.microsoft.com/azure/app-service/overview-hosting-plans)會針對裝載于 App Service 以執行的應用程式定義一組計算資源。 在本教學課程中，我們會使用開發人員/免費層來裝載 web 應用程式。 使用免費層時，您的 web 應用程式會在與其他 App Service 應用程式共用的 Windows 資源上執行，包括其他客戶的應用程式。 Azure 也提供 App Service 方案，在 Linux 計算資源上部署 web 應用程式。 如果您已經有想要使用的 App Service 計畫，可以略過此步驟。

   若要使用 Windows 免費層來建立 App Service 計畫，請執行下列命令。 使用 IoT 中樞所在的相同資源群組。 您的服務方案名稱可以包含大寫和小寫字母、數位和連字號。

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. 現在，在您的 App Service 方案中布建 web 應用程式。 `--deployment-local-git` 參數可讓您從本機電腦上的 Git 儲存機制上傳和部署 web 應用程式程式碼。 您的 web 應用程式名稱必須是全域唯一的，而且可以包含大小寫字母、數位和連字號。 請務必針對 `--runtime` 參數指定節點版本10.6 或更新版本，視您使用的 node.js 執行階段版本而定。 您可以使用 `az webapp list-runtimes` 命令來取得支援的執行時間清單。

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --runtime "node|10.6" --deployment-local-git
   ```

3. 現在，針對指定 IoT 中樞連接字串和事件中樞取用者群組的環境變數，新增應用程式設定。 個別設定是在 `-settings` 參數中以空格分隔。 使用您的 IoT 中樞的服務連接字串，以及您先前在本教學課程中建立的取用者群組。 請勿將值加上引號。

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString=<your IoT hub connection string>
   ```

4. 啟用 web 應用程式的 Web 通訊端通訊協定，並將 web 應用程式設定為只接收 HTTPS 要求（HTTP 要求會重新導向至 HTTPS）。

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. 若要將程式碼部署至 App Service，您將會使用您[的使用者層級部署認證](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials)。 您的使用者層級部署認證與您的 Azure 認證不同，可用於 Git 本機和 FTP 部署至 web 應用程式。 一旦設定之後，它們就會在您 Azure 帳戶中所有訂用帳戶的所有 App Service 應用程式中有效。 如果您先前已設定使用者層級的部署認證，則可以使用它們。

   如果您先前尚未設定使用者層級的部署認證，或您不記得密碼，請執行下列命令。 您的部署使用者名稱在 Azure 中必須是唯一的，而且不能包含本機 Git 推送的 ' @ ' 符號。 當系統提示您時，請輸入並確認您的新密碼。 密碼長度必須至少為 8 個字元，包含下列三個元素其中兩個：字母、數字及符號。

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. 取得用來將您的程式碼推送至 App Service 的 Git URL。

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. 將遠端新增至您的複本，以在 App Service 中參考 web 應用程式的 Git 存放庫。 針對 \<的 Git 複製 URL\>，請使用上一個步驟中所傳回的 URL。 在命令視窗中執行下列命令。

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. 若要將程式碼部署至 App Service，請在命令視窗中輸入下列命令。 如果系統提示您提供認證，請輸入您在步驟5中建立的使用者層級部署認證。 請確定您已推送至 App Service 遠端的主要分支。

    ```cmd
    git push webapp master:master
    ```

9. 部署的進度將會在您的命令視窗中更新。 成功的部署將以類似下列輸出的行結尾：

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  master -> master
    ```

10. 執行下列命令來查詢 web 應用程式的狀態，並確定它正在執行：

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. 在瀏覽器中，瀏覽至 `https://<your web app name>.azurewebsites.net` 。 一個網頁，類似于您在本機執行 web 應用程式時所看到的網頁。 假設您的裝置正在執行並傳送資料，您應該會看到裝置所傳送之50最近溫度和濕度讀數的執行中繪圖。

## <a name="troubleshooting"></a>疑難排解

如果您遇到此範例的任何問題，請嘗試下列各節中的步驟。 如果您仍然遇到問題，請在本主題的底部傳送意見反應給我們。

### <a name="client-issues"></a>用戶端問題

* 如果裝置未出現在清單中，或未繪製任何圖形，請確定裝置程式碼正在裝置上執行。

* 在瀏覽器中，開啟開發人員工具（在許多瀏覽器中，F12 鍵會將它開啟），然後尋找主控台。 尋找在該處列印的任何警告或錯誤。

* 您可以在/js/chat-device-data.js. 中，對用戶端腳本進行調試

### <a name="local-website-issues"></a>本機網站問題

* 在您為主控台輸出啟動節點的視窗中，監看輸出。

* 進行伺服器程式碼的偵錯工具，特別是 node.js 和/scripts/event-hub-reader.js。

### <a name="azure-app-service-issues"></a>Azure App Service 問題

* 在 Azure 入口網站中，移至您的 web 應用程式。 在左窗格中的 [**監視**] 底下，選取 [ **App Service 記錄**]。 將 **[應用程式記錄（檔案系統）** ] 開啟，將 [**層級**] 設定為 [錯誤]，然後選取 [**儲存**]。 然後開啟**記錄資料流程**（在 [**監視**中] 底下）。

* 從您在 Azure 入口網站的 web 應用程式的 [**開發工具**] 底下，選取 [**主控台**]，然後使用 `node -v` 和 `npm -v`來驗證 node 和 npm 版本。

* 如果您看到有關找不到套件的錯誤，您可能已不按照循序執行步驟。 當部署網站（使用 `git push`）時，app service 會 `npm install`執行，這會根據目前已設定的節點版本來執行。 如果稍後設定中的變更，您必須對程式碼進行無意義的變更，然後再推送一次。

## <a name="next-steps"></a>後續步驟

您已成功使用 Web 應用程式 (web app) 將 IoT 中樞的即時感應器資料視覺化。

如需從 Azure IoT 中樞將資料視覺化的另一種方式，請參閱[使用 Power BI 將來自 IoT 中樞的即時感應器資料視覺化](iot-hub-live-data-visualization-in-power-bi.md)。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
