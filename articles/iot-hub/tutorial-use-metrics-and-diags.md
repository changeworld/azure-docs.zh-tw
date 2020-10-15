---
title: 搭配 Azure IoT 中樞來設定及使用計量和診斷記錄
description: 了解如何搭配 Azure IoT 中樞來設定及使用計量和診斷記錄。 這會提供要分析的資料，以協助診斷您中樞可能發生的問題。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 3/13/2019
ms.author: robinsh
ms.custom:
- mvc
- mqtt
- devx-track-azurecli
- devx-track-csharp
ms.openlocfilehash: b24d9e1cbbcf875d7b4bde3981b28d8999ba8d47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89019125"
---
# <a name="tutorial-set-up-and-use-metrics-and-diagnostic-logs-with-an-iot-hub"></a>教學課程：搭配 IoT 中樞來設定及使用計量和診斷記錄

如果您有在生產環境中執行 IoT 中樞解決方案，建議您設定某些計量並啟用診斷記錄。 之後若發生問題，您就可以查看資料來協助您診斷問題，並加快修正速度。 在本文中，您會了解如何啟用診斷記錄，以及如何檢查記錄來找出錯誤。 您也會設定某些要監看的計量，以及會在計量達到特定界限時引發的警示。 例如，您可以讓系統在所傳送的遙測訊息數目超過特定界限時，或是在所使用的訊息數目接近 IoT 中樞每日允許的訊息配額時，傳送電子郵件給您。 

以加油站使用案例為例，站內的加油機為 IoT 裝置，會與 IoT 中樞通訊。 信用卡驗證通過後，最終的交易便會寫入到資料存放區。 如果 IoT 裝置不再與中樞連線並傳送訊息，而您又無法了解發生了什麼事，要修復問題就會變得更加困難。

本教學課程使用從 [IoT 中樞路由](tutorial-routing.md)取得的 Azure 範例，以將訊息傳送至 IoT 中樞。

在本教學課程中，您會執行下列工作：

> [!div class="checklist"]
> * 使用 Azure CLI 建立 IoT 中樞、模擬裝置和儲存體帳戶。  
> * 啟用診斷記錄。
> * 啟用計量。
> * 設定這些計量的警示。 
> * 下載並執行某個應用程式，以模擬 IoT 裝置傳送訊息給中樞。 
> * 執行應用程式，直到開始引發警示。 
> * 檢視計量結果，並檢查診斷記錄。 

## <a name="prerequisites"></a>Prerequisites

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 安裝 [Visual Studio](https://www.visualstudio.com/)。 

- 能夠接收郵件的電子郵件帳戶。

- 請確定您的防火牆已開啟連接埠 8883。 本教學課程中的裝置範例會使用 MQTT 通訊協定，其會透過連接埠 8883 進行通訊。 某些公司和教育網路環境可能會封鎖此連接埠。 如需此問題的詳細資訊和解決方法，請參閱[連線至 IoT 中樞 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-resources"></a>設定資源

在此教學課程中，您需要 IoT 中樞、儲存體帳戶和模擬的 IoT 裝置。 這些資源可使用 Azure CLI 或 Azure PowerShell 來建立。 請將所有資源建立在相同的資源群組和位置。 如此一來，您就可以在結束時，透過刪除資源群組的單一步驟來移除所有項目。

必要步驟如下。

1. 建立[資源群組](../azure-resource-manager/management/overview.md)。 

2. 建立 IoT 中樞。

3. 透過 Standard_LRS 複寫來建立標準 V1 儲存體帳戶。

4. 為傳送訊息至中樞的模擬裝置建立裝置身分識別。 儲存測試階段的金鑰。

### <a name="set-up-resources-using-azure-cli"></a>使用 Azure CLI 設定資源

複製此指令碼並貼到 Cloud Shell。 如果您已登入，它會一次執行一行指令碼。 新的資源會建立到資源群組 ContosoResources 中。

必須是全域唯一的變數會與 `$RANDOM` 串連。 執行指令碼並設定變數時，系統會產生隨機數字字串，並將其串連至固定字串的結尾，使其成為唯一。

```azurecli-interactive

# This is the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-iot

# Set the values for the resource names that don't have to be globally unique.
# The resources that have to have unique names are named in the script below
#   with a random number concatenated to the name so you can probably just
#   run this script, and it will work with no conflicts.
location=westus
resourceGroup=ContosoResources
iotDeviceName=Contoso-Test-Device 

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, so add a random number to the end.
iotHubName=ContosoTestHub$RANDOM
echo "IoT hub name = " $iotHubName

# Create the IoT hub in the Free tier.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku F1 --location $location

# The storage account name must be globally unique, so add a random number to the end.
storageAccountName=contosostoragemon$RANDOM
echo "Storage account name = " $storageAccountName

# Create the storage account.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName 

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

```

>[!NOTE]
>在建立裝置身分識別時，您可能會收到下列錯誤：無法針對 IoT 中樞 ContosoTestHub 的原則 iothubowner 找到其金鑰  。 若要修正這個錯誤，請更新 Azure CLI IoT 擴充功能，然後再次執行指令碼中的最後兩個命令。 
>
>以下是用來更新擴充功能的命令。 請在 Cloud Shell 執行個體中執行此命令。
>
>```cli
>az extension update --name azure-iot
>```

## <a name="enable-the-diagnostic-logs"></a>啟用診斷記錄 

當您建立新的 IoT 中樞時，[診斷記錄](../azure-monitor/platform/platform-logs-overview.md)預設會停用。 在本節中，請啟用中樞的診斷記錄。

1. 首先，如果您還未進入您在入口網站中的中樞，請按一下 [資源群組]  ，然後按一下資源群組 Contoso-Resources。 從所顯示的資源清單中選取中樞。 

2. 在 IoT 中樞刀鋒視窗中找到 [監視]  區段。 按一下 [診斷設定]  。 

   ![螢幕擷取畫面，顯示 IoT 中樞刀鋒視窗的診斷設定部分。](./media/tutorial-use-metrics-and-diags/01-diagnostic-settings.png)


3. 確定訂用帳戶和資源群組是否正確。 在 [資源類型]  底下，取消核取 [全選]  ，然後尋找並核取 [IoT 中樞]  。 (這麼做會讓 [全選]  旁邊再出出現核取記號，予以忽略即可)。在 [資源]  底下，選取中樞名稱。 畫面應該會如下圖所示： 

   ![螢幕擷取畫面，顯示 IoT 中樞刀鋒視窗的診斷設定部分。](./media/tutorial-use-metrics-and-diags/02-diagnostic-settings-start.png)

4. 現在，按一下 [開啟診斷]  。 隨即會顯示 [診斷設定] 窗格。 將診斷記錄設定的名稱指定為「diags-hub」。

5. 勾選 [封存至儲存體帳戶]  。 

   ![螢幕擷取畫面，顯示設定診斷來封存至儲存體帳戶。](./media/tutorial-use-metrics-and-diags/03-diagnostic-settings-storage.png)

    按一下 [設定]  以查看 [選取儲存體帳戶]  畫面，選取正確的帳戶 (contosostoragemon  )，然後按一下 [確定]  返回 [診斷設定] 窗格。 

   ![螢幕擷取畫面，顯示設定診斷記錄來封存至儲存體帳戶。](./media/tutorial-use-metrics-and-diags/04-diagnostic-settings-after-storage.png)

6. 在 [記錄]  底下，核取 [連線]  和 [裝置遙測]  ，並將每個遙測的 [保留期 (天)]  設定為 7 天。 診斷設定畫面現在應該會如下圖所示：

   ![螢幕擷取畫面，顯示最後的診斷記錄設定。](./media/tutorial-use-metrics-and-diags/05-diagnostic-settings-done.png)

7. 按一下 [儲存]  來儲存這些設定。 關閉 [診斷設定] 窗格。

之後，當您查看診斷記錄時，就能夠看到裝置的連接和中斷連線記錄。 

## <a name="set-up-metrics"></a>設定計量 

現在，請設定一些計量以監看傳送至中樞的訊息。 

1. 在 IoT 中樞的 [設定] 窗格中，按一下 [監視]  區段中的 [計量]  選項。

2. 在畫面頂端，按一下 [過去 24 小時 (自動)]  。 在出現的下拉式清單中，選取 [過去 4 小時]  作為 [時間範圍]  ，並將 [時間細微性]  設定為 [1 分鐘]  (當地時間)。 按一下 [套用]  來儲存這些設定。 

   ![螢幕擷取畫面，顯示計量時間設定。](./media/tutorial-use-metrics-and-diags/06-metrics-set-time-range.png)

3. 系統中預設會有一個計量項目。 讓資源群組保留預設值，並保留計量命名空間。 在 [計量]  下拉式清單中，選取 [已傳送的遙測訊息]  。 將 [彙總]  設定為 [總和]  。

   ![螢幕擷取畫面，顯示為已傳送的遙測訊息新增計量。](./media/tutorial-use-metrics-and-diags/07-metrics-telemetry-messages-sent.png)


4. 現在，按一下 [新增計量]  以在圖表中新增其他計量。 選取您的資源群組 (**ContosoTestHub**)。 在 [計量]  底下，選取 [已使用的訊息總數]  。 在 [彙總]  中選取 [平均]  。 

   現在，您的畫面中會顯示已最小化的 [已傳送的遙測訊息]  計量，以及用於 [已使用的訊息總數]  的新計量。

   ![螢幕擷取畫面，顯示為已傳送的遙測訊息新增計量。](./media/tutorial-use-metrics-and-diags/07-metrics-num-messages-used.png)

   按一下 [釘選到儀表板]  。 此選項會將計量釘選到 Azure 入口網站的儀表板，以便您可以再次存取。 如果未將其釘選到儀表板，則不會保留您的設定。

## <a name="set-up-alerts"></a>設定警示

移至入口網站中的中樞。 按一下 [資源群組]  ，選取 [ContosoResources]  ，然後選取 IoT 中樞 [ContosoTestHub]  。 

IoT 中樞尚未遷移至 [Azure 監視器中的計量](/azure/azure-monitor/platform/data-collection#metrics)；您必須使用[傳統警示](/azure/azure-monitor/platform/alerts-classic.overview)。

1. 在 [監視]  底下，按一下 [警示]  ；這會顯示主要的 [警示] 畫面。 

   ![螢幕擷取畫面，顯示如何尋找傳統警示。](./media/tutorial-use-metrics-and-diags/08-find-classic-alerts.png)

2. 若要從這裡移至傳統警示，請按一下 [檢視傳統警示]  。 

    ![螢幕擷取畫面，顯示傳統警示畫面。](./media/tutorial-use-metrics-and-diags/09-view-classic-alerts.png)

    填寫欄位： 

    訂用帳戶  ：讓此欄位設定為您目前的訂用帳戶。

    **來源**：將此欄位設定為 [計量]  。

    **資源群組**：將此欄位設定為您目前的資源群組 ContosoResources  。 

    **資源類型**：將此欄位設定為 IoT 中樞。 

    **資源**：選取您的 IoT 中樞 ContosoTestHub  。

3. 按一下 [新增計量警示 (傳統)]  來設定新警示。

    填寫欄位：

    **Name**：提供警示規則的名稱，例如 telemetry-messages  。

    **描述**：提供描述的警示，例如「傳送了 1000 則遙測訊息時發出警示」  。 

    **來源**：將此欄位設定為 [計量]  。

    [訂用帳戶]  、[資源群組]  和 [資源]  應該設定為您在 [檢視傳統警示]  畫面所選取的值。 

    將 [計量]  設定為 [已傳送的遙測訊息]  。

    ![螢幕擷取畫面，顯示為已傳送的遙測訊息設定傳統警示。](./media/tutorial-use-metrics-and-diags/10-alerts-add-rule-telemetry-top.png)

4. 在圖表之後，設定下列欄位：

   **條件**：設定為 [大於]  。

   **閾值**：設為 1000。

   **期間**：設定為 [過去 5 分鐘]  。

   **通知電子郵件收件者**：在此輸入您的電子郵件地址。 

   ![螢幕擷取畫面，顯示警示畫面下半部。](./media/tutorial-use-metrics-and-diags/11-alerts-add-rule-bottom.png)

   按一下 [確定]  以儲存警示。 

5. 現在，為 [已使用的訊息總數]  設定另一個警示。 如果您想要在所使用的訊息數目接近 IoT 中樞的配額時傳送警示，以便得知中樞很快就會開始拒絕訊息，此計量會很有用。

   在 [檢視傳統警示]  畫面上，按一下 [新增計量警示 (傳統)]  ，然後在 [新增規則]  窗格上填入這些欄位。

   **Name**：提供警示規則的名稱，例如 number-of-messages-used  。

   **描述**：提供描述的警示，例如「在接近配額時發出警示」  。

   **來源**：將此欄位設定為 [計量]  。

    [訂用帳戶]  、[資源群組]  和 [資源]  應該設定為您在 [檢視傳統警示]  畫面所選取的值。 

    將 [計量]  設定為 [已使用的訊息總數]  。

6. 在圖表底下，填寫下列欄位：

   **條件**：設定為 [大於]  。

   **閾值**：設為 1000。

   **期間**：將此欄位設定為 [過去 5 分鐘]  。 

   **通知電子郵件收件者**：在此輸入您的電子郵件地址。 

   按一下 [確定]  以儲存規則。 

5. 現在，您應該會看到 [傳統警示] 窗格中有兩個警示： 

   ![螢幕擷取畫面，顯示傳統警示畫面與其中的新警示規則。](./media/tutorial-use-metrics-and-diags/12-alerts-done.png)

6. 關閉 [警示] 窗格。 
    
    使用這些設定時，您會在已傳送的訊息數目大於 400 時，以及已使用的訊息總數超過 NUMBER 時收到警示。

## <a name="run-simulated-device-app"></a>執行模擬裝置應用程式

在前面的指令碼設定一節中，您已設定裝置來模擬使用 IoT 裝置。 在本節中，您會下載模擬裝置的 .NET 主控台應用程式，來將裝置到雲端的訊息傳送至 IoT 中樞。  

下載 [IoT 裝置模擬](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)的解決方案。 此連結會下載包含數個應用程式的存放庫；您需要的解決方案位於 iot-hub/Tutorials/Routing/。

按兩下解決方案檔案 (SimulatedDevice.sln)，從 Visual Studio 中開啟程式碼，然後開啟 Program.cs。 以 IoT 中樞的主機名稱取代 `{iot hub hostname}`。 IoT 中樞主機名稱的格式是 **{iot-hub-name}.azure-devices.net**。 在此教學課程中，中樞主機名稱是 **ContosoTestHub.azure-devices.net**。 接下來，以您稍早設定模擬裝置時儲存的裝置金鑰取代 `{device key}`。 

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>執行和測試 

在 Program.cs 中，將 `Task.Delay` 從 1000 變更為 10，以將傳送訊息的間隔時間從 1 秒減為 0.1 秒。 縮短此延遲會增加所傳送的訊息數目。

```csharp
await Task.Delay(10);
```

執行主控台應用程式。 請等待數分鐘 (10-15)。 在應用程式的主控台畫面上，您可以看到訊息會從模擬裝置傳送至中樞。

### <a name="see-the-metrics-in-the-portal"></a>在入口網站中查看計量

從 [儀表板] 開啟您的計量。 將時間值變更為 [過去 30 分鐘]  ，時間細微性則設定為 [1 分鐘]  。 此計量會在圖表上顯示已傳送的遙測訊息和已使用的訊息總數，圖表底部會顯示最新的數量。

   ![顯示計量的螢幕擷取畫面。](./media/tutorial-use-metrics-and-diags/13-metrics-populated.png)

### <a name="see-the-alerts"></a>查看警示

返回警示。 按一下 [資源群組]  ，選取 [ContosoResources]  ，然後選取中樞 [ContosoTestHub]  。 在針對中樞所顯示的 [屬性] 頁面中，選取 [警示]  ，然後選取 [檢視傳統警示]  。 

當已傳送的訊息數目超過限制時，您就會開始收到電子郵件警示。 若要查看是否還有任何作用中的警示，請移至您的中樞，並選取 [警示]  。 裡面會顯示作用中的警示，以及是否有任何警告。 

   ![螢幕擷取畫面，顯示已引發警示。](./media/tutorial-use-metrics-and-diags/14-alerts-firing.png)

按一下遙測訊息的警示。 該警示會顯示計量結果，以及含有結果的圖表。 此外，為了警告您已引發警示而傳送的電子郵件會如下圖所示：

   ![電子郵件的螢幕擷取畫面，顯示已引發警示。](./media/tutorial-use-metrics-and-diags/15-alert-email.png)

### <a name="see-the-diagnostic-logs"></a>查看診斷記錄

請將診斷記錄設定為要匯出至 Blob 儲存體。 移至您的資源群組，然後選取您的儲存體帳戶 contosostoragemon  。 選取 Blob，然後開啟容器 insights-logs-connections  。 向下切入，直到您到達目前的日期，然後選取最新的檔案。 

   ![螢幕擷取畫面，向下切入至儲存體容器以查看診斷記錄。](./media/tutorial-use-metrics-and-diags/16-diagnostics-logs-list.png)

按一下 [下載]  來下載記錄並加以開啟。 您會看到裝置傳送訊息至中樞時的連線與中斷連線記錄。 範例如下：

``` json
{ 
  "time": "2018-12-17T18:11:25Z", 
  "resourceId": 
    "/SUBSCRIPTIONS/your-subscription-id/RESOURCEGROUPS/CONTOSORESOURCES/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/CONTOSOTESTHUB", 
  "operationName": "deviceConnect", 
  "category": "Connections", 
  "level": "Information", 
  "properties": 
      {"deviceId":"Contoso-Test-Device",
       "protocol":"Mqtt",
       "authType":null,
       "maskedIpAddress":"73.162.215.XXX",
       "statusCode":null
       }, 
  "location": "westus"
}
{ 
   "time": "2018-12-17T18:19:25Z", 
   "resourceId": 
     "/SUBSCRIPTIONS/your-subscription-id/RESOURCEGROUPS/CONTOSORESOURCES/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/CONTOSOTESTHUB", 
    "operationName": "deviceDisconnect", 
    "category": "Connections", 
    "level": "Error", 
    "resultType": "404104", 
    "resultDescription": "DeviceConnectionClosedRemotely", 
    "properties": 
        {"deviceId":"Contoso-Test-Device",
         "protocol":"Mqtt",
         "authType":null,
         "maskedIpAddress":"73.162.215.XXX",
         "statusCode":"404"
         }, 
    "location": "westus"
}
```

## <a name="clean-up-resources"></a>清除資源 

若要將您已針對本教學課程所建立的所有資源移除，請刪除資源群組。 此動作會同時刪除群組內含的所有資源。 在本例中，此動作會移除 IoT 中樞、儲存體帳戶和資源群組本身。 如果您已將計量釘選到儀表板，則必須對每個計量右上角的三個點按一下，然後選取 [移除]  ，來手動移除這些計量。

若要移除資源群組，請使用 [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) 命令。

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何藉由執行下列工作，來使用計量和診斷記錄：

> [!div class="checklist"]
> * 使用 Azure CLI 建立 IoT 中樞、模擬裝置和儲存體帳戶。  
> * 啟用診斷記錄。 
> * 啟用計量。
> * 設定這些計量的警示。 
> * 下載並執行某個應用程式，以模擬 IoT 裝置傳送訊息給中樞。 
> * 執行應用程式，直到開始引發警示。 
> * 檢視計量結果，並檢查診斷記錄。 

前進至下一個教學課程，以了解如何管理 IoT 裝置的狀態。 

> [!div class="nextstepaction"]
> [從後端服務設定您的裝置](tutorial-device-twins.md)
