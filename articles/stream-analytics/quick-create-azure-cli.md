---
title: 快速入門 - 使用 Azure CLI 建立 Azure 串流分析作業
description: 此快速入門說明如何使用 Azure CLI 建立 Azure 串流分析作業。
services: stream-analytics
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 07/01/2020
ms.openlocfilehash: 48ef9cc48f48cec92ae57774e5d89e300ee43385
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96485346"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-using-the-azure-cli"></a>快速入門：使用 Azure CLI 建立 Azure 串流分析作業

在本快速入門中，您將使用 Azure CLI 來定義串流分析作業，以篩選出溫度讀數大於 27 的即時感應器訊息。 串流分析作業會從 IoT 中樞讀取資料、轉換資料，以及將資料寫回 Blob 儲存體中的容器。 本快速入門中使用的輸入資料是由 Raspberry Pi 線上模擬器產生。

## <a name="before-you-begin"></a>開始之前

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 建立資源群組。 所有 Azure 資源都必須部署至資源群組。 資源群組可讓您組織和管理相關的 Azure 資源。

   針對此快速入門，請使用下列 [az group create](/cli/azure/group#az-group-create) 命令，在 eastus 位置中建立一個名為 streamanalyticsrg 的資源群組：

   ```azurecli
   az group create --name streamanalyticsrg --location eastus
   ```

## <a name="prepare-the-input-data"></a>準備輸入資料

在您定義串流分析作業之前，請先準備用於作業輸入的資料。

下列 Azure CLI 程式碼區塊是準備作業所需輸入資料的命令。 檢閱區段以了解程式碼。

1. 使用 [az iot hub create](../iot-hub/iot-hub-create-using-cli.md#create-an-iot-hub) 命令建立 IoT 中樞。 此範例會建立名為 **MyASAIoTHub** 的 IoT 中樞。 因為 IoT 中樞名稱是唯一的，所以您需要提出您自己的 IoT 中樞名稱。 將 SKU 設定為 F1 可使用免費層 (如果適用於您的訂用帳戶)。 否則，請選擇下一個最低層。

    ```azurecli
    az iot hub create --name "MyASAIoTHub" --resource-group streamanalyticsrg --sku S1
    ```

    建立 IoT 中樞後，使用 [az iot hub show-connection-string](/cli/azure/iot/hub) 命令來取得 IoT 中樞連接字串。 複製整個連接字串並加以儲存，以便在將 IoT 中樞當作輸入新增至 Stream Analytics 作業時使用。

    ```azurecli
    az iot hub show-connection-string --hub-name "MyASAIoTHub"
    ```

2. 使用 [az iothub device-identity create](../iot-hub/quickstart-send-telemetry-c.md#register-a-device) 命令，將裝置新增到 IoT 中樞。 此範例會建立名為 **MyASAIoTDevice** 的裝置。

    ```azurecli
    az iot hub device-identity create --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice"
    ```

3. 使用 [az iot hub device-identity show-connection-string](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-show-connection-string) 命令取得裝置連接字串。 複製整個連接字串並加以儲存，以便在建立 Raspberry Pi 模擬器時使用。

    ```azurecli
    az iot hub device-identity show-connection-string --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice" --output table
    ```

    **輸出範例：**

    ```output
    HostName=MyASAIoTHub.azure-devices.net;DeviceId=MyASAIoTDevice;SharedAccessKey=a2mnUsg52+NIgYudxYYUNXI67r0JmNubmfVafojG8=
    ```

## <a name="create-a-blob-storage-account"></a>建立 Blob 儲存體帳戶

下列 Azure CLI 程式碼區塊會建立用於作業輸出的 Blob 儲存體帳戶。 檢閱區段以了解程式碼。

1. 使用 [az storage account create](/cli/azure/storage/account) 命令建立一般用途的儲存體帳戶。 一般用途的儲存體帳戶可以用於所有四個服務：Blob、檔案、資料表和佇列。

   請記得以您自己的值取代角括號中的預留位置值：

   ```azurecli
   az storage account create \
       --name <storage-account> \
       --resource-group streamanalyticsrg \
       --location eastus \
       --sku Standard_ZRS \
       --encryption-services blob
   ```

2. 執行 [az storage account keys list](/cli/azure/storage/account/keys) 命令，以取得儲存體帳戶的金鑰。 儲存此金鑰以在下一個步驟中使用。

   ```azurecli
   az storage account keys list -g streamanalyticsrg -n <storage-account>
   ```

3. 使用 [az storage container create](/cli/azure/storage/container) 命令，建立用於儲存 Blob 的容器。 您可以使用儲存體帳戶金鑰來授權作業以建立容器。 如需有關藉由 Azure CLI 授權資料作業的詳細資訊，請參閱 [藉由 Azure CLI 來授權 Blob 或佇列資料的存取權](../storage/blobs/authorize-data-operations-cli.md)。

   ```azurecli
   az storage container create \
       --account-name <storage-account> \
       --name sample-container \
       --account-key <key>
       --auth-mode key
   ```

## <a name="create-a-stream-analytics-job"></a>建立串流分析作業

下列 Azure CLI 程式碼區塊會建立串流分析作業。 請檢閱各區段來了解程式碼

1. 使用 [az stream-analytics job create](/cli/azure/ext/stream-analytics/stream-analytics/job#ext-stream-analytics-az-stream-analytics-job-create) 命令來建立串流分析作業。

```azurecli
az stream-analytics job create \
    --resource-group streamanalyticsrg 
    --name streamanalyticsjob \
    --location eastus \
    --output-error-policy "Drop" \
    --events-outoforder-policy "Drop" \
    --events-outoforder-max-delay 5 \
    --events-late-arrival-max-delay 16 \     
    --data-locale "en-US"
```

## <a name="configure-input-to-the-job"></a>設定作業的輸入

使用 [az stream-analytics input](/cli/azure/ext/stream-analytics/stream-analytics/input#ext-stream-analytics-az-stream-analytics-input-create) Cmdlet，將輸入新增至您的作業。 此 Cmdlet 會採用作業名稱、作業輸入名稱、資源群組名稱和作業輸入定義來作為參數。 作業輸入定義是 JSON 檔案，其中包含設定作業輸入所需的屬性。 在此範例中，您將建立 IoT 中樞來作為輸入。

在本機電腦上，建立名為 `datasource.json` 的檔案，並於其中新增下列 JSON 資料。 務必以您在上一節中儲存的 IoT 中樞裝置連接字串的 `SharedAccessKey` 部分，取代 `sharedAccessPolicyKey` 的值。

```json
{
    "type": "Microsoft.Devices/IotHubs",
    "properties": {
        "iotHubNamespace": "iothub",
        "sharedAccessPolicyName": "iothubowner",
        "sharedAccessPolicyKey": "sharedAccessPolicyKey=",
        "consumerGroupName": "sdkconsumergroup",
        "endpoint": "messages/events"
    }
}
```

在本機電腦上，建立名為 `serialization.json` 的檔案，並於其中新增下列 JSON 資料。

```json
{
     "type": "Json",
     "properties": {
         "encoding": "UTF8"
     }
}
```

接下來，執行 `az stream-analytics input create` Cmdlet。 請務必將 `datasource` 變數的值取代為您已儲存作業輸入定義 JSON 檔案的路徑，並將 `serialization` 變數的值取代為儲存序列化 JSON 檔案的路徑。

```azurecli
az stream-analytics input create \
    --resource-group streamanalyticsrg 
    --job-name streamanalyticsjob \
    --name asaiotinput \
    --type Stream \
    --datasource datasource.json \
    --serialization serialization.json
```

## <a name="configure-output-to-the-job"></a>設定作業的輸出

使用 [az stream-analytics output create](/cli/azure/ext/stream-analytics/stream-analytics/output#ext-stream-analytics-az-stream-analytics-output-create) Cmdlet，將輸出新增至您的作業。 此 Cmdlet 會採用作業名稱、作業輸出名稱、資源群組名稱和作業輸出定義來作為參數。 作業輸出定義是 JSON 檔案，其中包含設定作業輸出所需的屬性。 這個範例會使用 blob 儲存體作為輸出。

在本機電腦上，建立名為 `datasink.json` 的檔案，並於其中新增下列 JSON 資料。 務必要將 `accountKey` 的值取代為儲存體帳戶的存取金鑰 (也就是 $storageAccountKey 值內所儲存的值)。

```json
{
    "type": "Microsoft.Storage/Blob",
    "properties": {
        "storageAccounts": [
            {
                "accountName": "<storage-account>",
                "accountKey": "accountKey=="
            }
        ],
        "container": "state",
        "pathPattern": "{date}/{time}",
        "dateFormat": "yyyy/MM/dd",
        "timeFormat": "HH"
    }
}
```

接下來，執行 `az stream-analytics output` Cmdlet。 請務必將 `datasource` 變數的值取代為您已儲存作業輸出定義 JSON 檔案的路徑，並將 `serialization` 變數的值取代為儲存序列化 JSON 檔案的路徑。

```azurecli
az stream-analytics output create \
    --resource-group streamanalyticsrg \
    --job-name streamanalyticsjob \
    --name asabloboutput \
    --datasource datasink.json \
    --serialization serialization.json
```

## <a name="define-the-transformation-query"></a>定義轉換查詢

使用 [az stream-analytics transformation create](/cli/azure/ext/stream-analytics/stream-analytics/transformation#ext-stream-analytics-az-stream-analytics-transformation-create) Cmdlet 來新增轉換作業。 此 Cmdlet 會採用作業名稱、作業轉換名稱、資源群組名稱和作業轉換定義來作為參數。 

執行 `az stream-analytics transformation create` Cmdlet。

```azurecli
az stream-analytics transformation create \
    --resource-group streamanalyticsrg \
    --job-name streamanalyticsjob \
    --name Transformation \
    --streaming-units "6" \
    --transformation-query "SELECT * INTO asabloboutput FROM asaiotinput HAVING Temperature > 27"
```
## <a name="run-the-iot-simulator"></a>執行 IoT 模擬器

1. 開啟 [Raspberry Pi Azure IoT 線上模擬器](https://azure-samples.github.io/raspberry-pi-web-simulator/)。

2. 以您在上一節中儲存的整個 Azure IoT 中樞裝置連接字串取代行 15 中的預留位置。

3. 按一下 **[執行]** 。 下列輸出會顯示傳送至 IoT 中樞的感應器資料和訊息。

    ![Raspberry Pi Azure IoT 線上模擬器](./media/stream-analytics-quick-create-powershell/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>啟動串流分析工作並查看輸出

使用 [az stream-analytics job start](/cli/azure/ext/stream-analytics/stream-analytics/job#ext-stream-analytics-az-stream-analytics-job-start) Cmdlet 來啟動作業。 此 Cmdlet 會採用作業名稱、資源群組名稱、輸出啟動模式和啟動時間來作為參數。 `OutputStartMode` 可接受 `JobStartTime`、`CustomTime` 或 `LastOutputEventTime`。

執行下列 Cmdlet 後，如果作業啟動，它會在輸出中傳回 `True`。 在儲存體容器中，建立的輸出資料夾包含已轉換的資料。

```azurecli
az stream-analytics job start \
    --resource-group streamanalyticsrg \
    --name streamanalyticsjob \
    --output-start-mode JobStartTime
```

## <a name="clean-up-resources"></a>清除資源

若不再需要，可刪除資源群組、串流作業和所有相關資源。 刪除作業可避免因為作業使用串流單位而產生費用。 如果您計劃在未來使用該作業，您可以跳過刪除動作，且目前停止作業。 如果您不要繼續使用此作業，請執行下列 Cmdlet，以刪除本快速入門所建立的所有資源：

```azurecli
az group delete \
    --name streamanalyticsrg \
    --no-wait
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已使用 Azure CLI 部署了簡單的串流分析作業。 您也可以使用 [Azure 入口網站](stream-analytics-quick-create-portal.md)和 [Visual Studio](stream-analytics-quick-create-vs.md) 部署串流分析作業。

若要了解如何設定其他輸入來源及執行即時偵測，請前往下列文章：

> [!div class="nextstepaction"]
> [使用 Azure 串流分析進行即時詐騙偵測](stream-analytics-real-time-fraud-detection.md)