---
title: 使用 Azure CLI 設定 Azure IoT 中樞的訊息路由
description: 使用 Azure CLI 設定 Azure IoT 中樞的訊息路由。 根據訊息中的屬性，路由至儲存體帳戶或服務匯流排佇列。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 056dac7977115f97892d8dbfde0710e00237804e
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78674351"
---
# <a name="tutorial-use-the-azure-cli-to-configure-iot-hub-message-routing"></a>教學課程：使用 Azure CLI 設定 IoT 中樞訊息路由

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>下載指令碼 (選擇性)

在本教學課程的第二個部分中，您會下載並執行 Visual Studio 應用程式以將訊息傳送至 IoT 中樞。 該下載中有一個資料夾，內含 Azure Resource Manager 範本和參數檔案，以及 Azure CLI 和 PowerShell 指令碼。

如果您想要檢視已完成的指令碼，請下載 [Azure IoT C# 範例](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)。 將 master.zip 檔案解壓縮。 Azure CLI 指令碼位於 /iot-hub/Tutorials/Routing/SimulatedDevice/resources/ 中，名稱是 **iothub_routing_cli.azcli**。

## <a name="use-the-azure-cli-to-create-your-resources"></a>使用 Azure CLI 來建立資源

將下列指令碼複製並貼到 Cloud Shell，然後按 Enter 鍵。 系統便會一次執行一行指令碼。 這是指令碼的第一個區段，會建立本教學課程的基底資源，包括儲存體帳戶、IoT 中樞、服務匯流排命名空間和服務匯流排佇列。 在進行本教學課程其餘部分的過程中，請複製指令碼的每個區塊並將其貼到 Cloud Shell 中來加以執行。

> [!TIP]
> 關於偵錯的提示：此指令碼會使用接續符號 (反斜線 `\`) 讓指令碼更容易讀取。 如果您有執行指令碼的問題，請確定 Cloud Shell 執行的是 `bash`，而且反斜線後面沒有空格。
> 

有數個資源名稱必須是全域唯一的，例如 IoT 中樞名稱和儲存體帳戶名稱。 為求簡化，那些資源的名稱會加上稱為 randomValue  的隨機英數字元值。 randomValue 會在指令碼頂端產生一次，並於需要時加到整個指令碼的資源名稱後面。 如果您不想使用隨機值，則可將其設定為空字串，或設定為特定值。 

> [!IMPORTANT]
> 初始指令碼中所設定的變數會供路由指令碼使用，因此，請讓所有指令碼在相同的 Cloud Shell 工作階段中執行。 如果您開啟新的工作階段來執行用於設定路由的指令碼，將會有數個變數遺漏值。
>

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account. 
# This field is used when setting up the routing queries.
subscriptionID=$(az account show --query id -o tsv)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-iot

# Set the values for the resource names that 
#   don't have to be globally unique.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults
iotDeviceName=Contoso-Test-Device

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

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName
```

現在您已設定好基底資源，接下來請設定訊息路由。

## <a name="set-up-message-routing"></a>設定訊息路由

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

若要建立路由端點，請使用 [az iot hub routing-endpoint create](/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest#az-iot-hub-routing-endpoint-create)。 若要建立端點的訊息路由，請使用 [az iot hub route create](/cli/azure/iot/hub/route?view=azure-cli-latest#az-iot-hub-route-create)。

### <a name="route-to-a-storage-account"></a>路由傳送至儲存體帳戶

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

先設定儲存體帳戶的端點，然後設定路由。 

這些是指令碼所使用的變數，必須在您的 Cloud Shell 工作階段內設定：

**storageConnectionString**：此值擷取自上一個指令碼中所設定的儲存體帳戶。 訊息路由作業會使用此值來存取儲存體帳戶。

  **resourceGroup**：資源群組會出現兩次，請都設定為您的資源群組。

**endpoint subscriptionID**：此欄位會設定為端點的 Azure subscriptionID。 

**endpointType**：此欄位是端點的類型。 此值必須設定為 `azurestoragecontainer`、`eventhub`、`servicebusqueue` 或 `servicebustopic`。 針對您在此的目的，將其設定為 `azurestoragecontainer`。

**iotHubName**：此欄位是將會執行路由的中樞名稱。

**containerName**：此欄位是儲存體帳戶中作為資料寫入目的地容器的名稱。

**encoding**：此欄位會是 `avro` 或 `json`。 這會指出預存資料的格式。

**routeName**：此欄位是所要設定路由的名稱。 

**endpointName**：此欄位是用來識別端點的名稱。 

**enabled**：此欄位預設為 `true`，表示應於建立訊息路由後加以啟用。

**condition**：此欄位是用來篩選此端點所收到訊息的查詢。 路由傳送至儲存體的訊息會有 `level="storage"` 的查詢條件。

將此指令碼複製並貼到 Cloud Shell 視窗，然後執行。

```azurecli
##### ROUTING FOR STORAGE ##### 

endpointName="ContosoStorageEndpoint"
endpointType="azurestoragecontainer"
routeName="ContosoStorageRoute"
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)
```

下一個步驟是建立儲存體帳戶的路由端點。 您也會指定要用來儲存結果的容器。 此容器已於先前建立儲存體帳戶時建立好。

```azurecli
# Create the routing endpoint for storage.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName \
  --resource-group $resourceGroup \
  --encoding avro
```

接下來，請建立儲存體端點的路由。 訊息路由會指定要將符合查詢規格的訊息傳送至何處。 

```azurecli
# Create the route for the storage endpoint.
az iot hub route create \
  --name $routeName \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName \
  --enabled \
  --condition $condition
```

### <a name="route-to-a-service-bus-queue"></a>路由傳送至服務匯流排佇列

現在來設定服務匯流排佇列的路由。 若要擷取服務匯流排佇列的連接字串，您必須建立已定義正確權限的授權規則。 下列指令碼會針對稱為 `sbauthrule` 的服務匯流排佇列建立授權規則，並將權限設定為 `Listen Manage Send`。 此授權規則定義好之後，便可用來擷取佇列的連接字串。

```azurecli
# Create the authorization rule for the Service Bus queue.
az servicebus queue authorization-rule create \
  --name "sbauthrule" \
  --namespace-name $sbNamespace \
  --queue-name $sbQueueName \
  --resource-group $resourceGroup \
  --rights Listen Manage Send \
  --subscription $subscriptionID
```

現在，請使用授權規則來擷取服務匯流排佇列的連接字串。

```azurecli
# Get the Service Bus queue connection string.
# The "-o tsv" ensures it is returned without the default double-quotes.
sbqConnectionString=$(az servicebus queue authorization-rule keys list \
  --name "sbauthrule" \
  --namespace-name $sbNamespace \
  --queue-name $sbQueueName \
  --resource-group $resourceGroup \
  --subscription $subscriptionID \
  --query primaryConnectionString -o tsv)

# Show the Service Bus queue connection string.
echo "service bus queue connection string = " $sbqConnectionString
```

現在，請設定服務匯流排佇列的路由端點和訊息路由。 這些是指令碼所使用的變數，必須在您的 Cloud Shell 工作階段內設定：

**endpointName**：此欄位是用來識別端點的名稱。 

**endpointType**：此欄位是端點的類型。 此值必須設定為 `azurestoragecontainer`、`eventhub`、`servicebusqueue` 或 `servicebustopic`。 針對您在此的目的，將其設定為 `servicebusqueue`。

**routeName**：此欄位是所要設定路由的名稱。 

**condition**：此欄位是用來篩選此端點所收到訊息的查詢。 路由傳送至服務匯流排佇列的訊息會有 `level="critical"` 的查詢條件。

以下是服務匯流排佇列的路由端點和訊息路由所適用的 Azure CLI。

```azurecli
endpointName="ContosoSBQueueEndpoint"
endpointType="ServiceBusQueue"
routeName="ContosoSBQueueRoute"
condition='level="critical"'

# Set up the routing endpoint for the Service Bus queue.
# This uses the Service Bus queue connection string.
az iot hub routing-endpoint create \
  --connection-string $sbqConnectionString \
  --endpoint-name $endpointName \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --resource-group $resourceGroup 

# Set up the message route for the Service Bus queue endpoint.
az iot hub route create --name $routeName \
  --hub-name $iotHubName \
  --source-type devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName \
  --enabled \
  --condition $condition
  ```

### <a name="view-message-routing-in-the-portal"></a>在入口網站中檢視訊息路由

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>後續步驟

現在您已設定好所有資源，也設定好訊息路由，接下來請前進到下一個教學課程，以了解如何將訊息傳送至 IoT 中樞，並看看訊息如何路由傳送至不同目的地。 

> [!div class="nextstepaction"]
> [第 2 部份 - 檢視訊息路由結果](tutorial-routing-view-message-routing-results.md)
