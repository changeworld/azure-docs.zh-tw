---
title: 教學課程-使用 Azure CLI 為 Azure IoT 中樞設定訊息路由
description: 教學課程-使用 Azure CLI 和 Azure 入口網站設定 Azure IoT 中樞的訊息路由
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/12/2019
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
- devx-track-azurecli
ms.openlocfilehash: d7a10aac82286616273f7283bc3fba74f2c58f35
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625210"
---
# <a name="tutorial-use-the-azure-cli-and-azure-portal-to-configure-iot-hub-message-routing"></a>教學課程：使用 Azure CLI 和 Azure 入口網站設定 IoT 中樞訊息路由

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="use-the-azure-cli-to-create-the-base-resources"></a>使用 Azure CLI 來建立基底資源

本教學課程使用 Azure CLI 來建立基底資源，然後使用 [Azure 入口網站](https://portal.azure.com)來示範如何設定訊息路由及設定虛擬裝置以便測試。

將下列指令碼複製並貼到 Cloud Shell，然後按 Enter 鍵。 系統便會一次執行一行指令碼。 這會建立本教學課程的基底資源，包括儲存體帳戶、IoT 中樞、服務匯流排命名空間和服務匯流排佇列。

有數個資源名稱必須是全域唯一的，例如 IoT 中樞名稱和儲存體帳戶名稱。 為求簡化，那些資源的名稱會加上稱為 randomValue  的隨機英數字元值。 randomValue 會在指令碼頂端產生一次，並於需要時加到整個指令碼的資源名稱後面。 如果您不想使用隨機值，則可將其設定為空字串，或設定為特定值。

> [!TIP]
> 關於偵錯的提示：此指令碼會使用接續符號 (反斜線 `\`) 讓指令碼更容易讀取。 如果您有執行指令碼的問題，請確定 Cloud Shell 執行的是 `bash`，而且反斜線後面沒有空格。
>

```azurecli-interactive
# This retrieves the subscription id of the account 
#   in which you're logged in.
# This field is used to set up the routing queries.
subscriptionID=$(az account show --query id)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# Set the values for the resource names that 
#   don't have to be globally unique.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
iotHubName=ContosoTestHub$randomValue 
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# Add a consumer group to the IoT hub for the 'events' endpoint.
az iot hub consumer-group create --hub-name $iotHubName \
    --name $iotHubConsumerGroup

# The storage account name must be globally unique, 
#   so add a random value to the end.
storageAccountName=contosostorage$randomValue
echo "Storage account name = " $storageAccountName

# Create the storage account to be used as a routing destination.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Get the primary storage account key. 
#    You need this to create the container.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"') 

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the container in the storage account. 
az storage container create --name $containerName \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# The Service Bus namespace must be globally unique, 
#   so add a random value to the end.
sbNamespace=ContosoSBNamespace$randomValue
echo "Service Bus namespace = " $sbNamespace

# Create the Service Bus namespace.
az servicebus namespace create --resource-group $resourceGroup \
    --name $sbNamespace \
    --location $location

# The Service Bus queue name must be globally unique, 
#   so add a random value to the end.
sbQueueName=ContosoSBQueue$randomValue
echo "Service Bus queue name = " $sbQueueName

# Create the Service Bus queue to be used as a routing destination.
az servicebus queue create --name $sbQueueName \
    --namespace-name $sbNamespace \
    --resource-group $resourceGroup

```

您現在已設定好基底資源，即可在 [Azure 入口網站](https://portal.azure.com)中設定訊息路由。

## <a name="set-up-message-routing"></a>設定訊息路由

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

### <a name="route-to-a-storage-account"></a>路由傳送至儲存體帳戶

現在來設定儲存體帳戶的路由。 移至 [訊息路由] 窗格，然後新增路由。 在新增路由時，定義新的路由端點。 設定好此路由之後，**level** 屬性設定為 **storage** 的訊息會自動寫入儲存體帳戶。 

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 [資源群組]  ，然後選取您的資源群組。 本教學課程使用 **ContosoResources**。

2. 選取資源清單下方的 IoT 中樞。 本教學課程使用 **ContosoTestHub**。

3. 選取 [訊息路由]  。 在 [訊息路由]  窗格中，選取 [+新增]  。 在 [ **新增路由** ] 窗格中，選取 [端點] 欄位旁的 [+**新增端點** ] 以顯示支援的端點，如下圖所示：

   ![開始新增路由的端點](./media/tutorial-routing/message-routing-add-a-route-with-storage-endpoint-ver2.png)

4. 選取 [儲存體] 。 您會看到 [新增儲存體端點]  窗格。

   ![新增端點](./media/tutorial-routing/message-routing-add-storage-endpoint-ver2.png)

5. 輸入端點的名稱。 本教學課程使用 **ContosoStorageEndpoint**。

6. 選取 [挑選容器]  。 這會帶您前往儲存體帳戶清單。 選取您在準備步驟中設定的帳戶。 本教學課程使用 **contosostorage**。 它會顯示該儲存體帳戶中的容器清單。 **選取** 您在準備步驟中設定的容器。 本教學課程使用 **contosoresults**。 您會回到 [新增儲存體端點]  窗格，查看您所選取的項目。

7. 將編碼設定為 AVRO 或 JSON。 基於本教學課程的目的，其他欄位可使用預設值。 如果選取的區域不支援 JSON 編碼，則此欄位會呈現灰色。

   > [!NOTE]
   > 您可以使用 **Blob 檔案名稱格式** 來設定 Blob 名稱的格式。 預設值為 `{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}`。 格式必須包含 {iothub}、{partition}、{YYYY}、{MM}、{DD}、{HH} 和 {mm} (順序不拘)。
   >
   > 例如，如果中樞名稱是 ContosoTestHub，且日期/時間是 2018 年 10 月 30 日上午 10:56，請使用預設 Blob 檔案名稱格式，Blob 名稱看起來像這樣：`ContosoTestHub/0/2018/10/30/10/56`。
   > 
   > Blob 會以 Avro 格式寫入。
   >

8. 選取 [建立]  來建立儲存體端點，並將它新增至路由。 您會返回 [新增路由]  窗格。

9. 現在，請完成其餘路由查詢資訊。 此查詢會指定準則，來規範如何將訊息傳送至您剛才新增為端點的儲存體容器。 填寫畫面上的欄位。

   **Name**：輸入路由查詢的名稱。 本教學課程使用 **ContosoStorageRoute**。

   **端點**：這會顯示您剛才設定的端點。

   **資料來源**：從下拉式清單選取 [裝置遙測訊息]  。

   **啟用路由**：確定此欄位設為 `enabled`。
   
   **路由查詢**：輸入 `level="storage"` 作為查詢字串。

   ![建立儲存體帳戶的路由查詢](./media/tutorial-routing/message-routing-finish-route-storage-ep.png)  

   選取 [儲存]  。 完成時會返回 [訊息路由] 窗格，您可以在其中看到儲存體的新路由查詢。 關閉 [路由] 窗格即會返回 [資源群組] 頁面。

### <a name="route-to-a-service-bus-queue"></a>路由傳送至服務匯流排佇列

現在來設定服務匯流排佇列的路由。 移至 [訊息路由] 窗格，然後新增路由。 在新增路由時，定義新的路由端點。 設定好此路由之後，**level** 屬性設定為 **critical** 的訊息會寫入至服務匯流排佇列中，然後觸發邏輯應用程式，並以電子郵件傳送資訊。

1. 在 [資源群組] 頁面上，選取您的 IoT 中樞，然後選取 [訊息路由]  。

2. 在 [訊息路由]  窗格中，選取 [+新增]  。

3. 在 [新增路由]  窗格中，選取 [端點] 欄位旁的 [+新增]  。 選取 [服務匯流排佇列]  。 您會看到 [新增服務匯流排端點]  窗格。

   ![新增服務匯流排端點](./media/tutorial-routing/message-routing-add-sbqueue-ep.png)

4. 填寫欄位：

   **端點名稱**：輸入端點的名稱。 本教學課程使用 **ContosoSBQueueEndpoint**。
   
   **服務匯流排命名空間**：使用下拉式清單來選取您在準備步驟中設定的服務匯流排命名空間。 本教學課程使用 **ContosoSBNamespace**。

   **服務匯流排佇列**：使用下拉式清單來選取服務匯流排佇列。 本教學課程使用 **contososbqueue**。

5. 選取 [建立]  以新增服務匯流排佇列端點。 您會返回 [新增路由]  窗格。

6. 現在，請完成其餘路由查詢資訊。 此查詢會指定準則，來規範如何將訊息傳送至您剛才新增為端點的服務匯流排佇列。 填寫畫面上的欄位。 

   **Name**：輸入路由查詢的名稱。 本教學課程使用 **ContosoSBQueueRoute**。 

   **端點**：這會顯示您剛才設定的端點。

   **資料來源**：從下拉式清單選取 [裝置遙測訊息]  。

   **路由查詢**：輸入 `level="critical"` 作為查詢字串。 

   ![建立服務匯流排佇列的路由查詢](./media/tutorial-routing/message-routing-finish-route-sbq-ep.png)

7. 選取 [儲存]  。 返回 [路由] 窗格中時，您會看到兩個新的路由顯示於此處。

   ![您剛設定的路由](./media/tutorial-routing/message-routing-show-both-routes.png)

8. 您可以選取 [自訂端點]  索引標籤，以查看您所設定的自訂端點。

   ![您剛設定的自訂端點](./media/tutorial-routing/message-routing-show-custom-endpoints.png)

9. 關閉 [訊息路由] 窗格即會返回 [資源群組] 窗格。

## <a name="create-a-simulated-device"></a>建立模擬裝置

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="next-steps"></a>後續步驟

現在您已設定好所有資源，也設定好訊息路由，接下來請前進到下一個教學課程，以了解如何將訊息傳送至 IoT 中樞，並看看訊息如何路由傳送至不同目的地。 

> [!div class="nextstepaction"]
> [第 2 部份 - 檢視訊息路由結果](tutorial-routing-view-message-routing-results.md)