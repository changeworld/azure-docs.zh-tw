---
title: 使用 Azure PowerShell 設定 Azure IoT 中樞的訊息路由
description: 使用 Azure PowerShell 設定 Azure IoT 中樞的訊息路由。 根據訊息中的屬性，路由至儲存體帳戶或服務匯流排佇列。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 92c30287e836ccadda915dab70a4d40e9c4e851a
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89073398"
---
# <a name="tutorial-use-azure-powershell-to-configure-iot-hub-message-routing"></a>教學課程：使用 Azure PowerShell 來設定 IoT 中樞訊息路由

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>下載指令碼 (選擇性)

在本教學課程的第二個部分中，您會下載並執行 Visual Studio 應用程式以將訊息傳送至 IoT 中樞。 該下載中有一個資料夾，內含 Azure Resource Manager 範本和參數檔案，以及 Azure CLI 和 PowerShell 指令碼。 

如果您想要檢視已完成的指令碼，請下載 [Azure IoT C# 範例](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)。 將 master.zip 檔案解壓縮。 Azure CLI 指令碼位於 /iot-hub/Tutorials/Routing/SimulatedDevice/resources/ 中，名稱是 **iothub_routing_psh.ps1**。

## <a name="create-your-resources"></a>建立資源

一開始先使用 PowerShell 建立資源。

### <a name="use-powershell-to-create-your-base-resources"></a>使用 PowerShell 來建立基底資源

將下列指令碼複製並貼到 Cloud Shell，然後按 Enter 鍵。 系統便會一次執行一行指令碼。 這是指令碼的第一個區段，會建立本教學課程的基底資源，包括儲存體帳戶、IoT 中樞、服務匯流排命名空間和服務匯流排佇列。 在進行本教學課程的過程中，請複製指令碼的每個區塊並將其貼到 Cloud Shell 中來加以執行。

有數個資源名稱必須是全域唯一的，例如 IoT 中樞名稱和儲存體帳戶名稱。 為求簡化，那些資源的名稱會加上稱為 randomValue  的隨機英數字元值。 randomValue 會在指令碼頂端產生一次，並於需要時加到整個指令碼的資源名稱後面。 如果您不想使用隨機值，則可將其設定為空字串，或設定為特定值。 

> [!IMPORTANT]
> 初始指令碼中所設定的變數會供路由指令碼使用，因此，請讓所有指令碼在相同的 Cloud Shell 工作階段中執行。 如果您開啟新的工作階段來執行用於設定路由的指令碼，將會有數個變數遺漏值。 
>

```azurepowershell-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing queries.
$subscriptionID = (Get-AzContext).Subscription.Id

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves the first 6 digits of a random value.
$randomValue = "$(Get-Random)".Substring(0,6)

# Set the values for the resource names that don't have to be globally unique.
$location = "West US"
$resourceGroup = "ContosoResources"
$iotHubConsumerGroup = "ContosoConsumers"
$containerName = "contosoresults"

# Create the resource group to be used 
#   for all resources for this tutorial.
New-AzResourceGroup -Name $resourceGroup -Location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
$iotHubName = "ContosoTestHub" + $randomValue
Write-Host "IoT hub name is " $iotHubName

# Create the IoT hub.
New-AzIotHub -ResourceGroupName $resourceGroup `
    -Name $iotHubName `
    -SkuName "S1" `
    -Location $location `
    -Units 1 

# Add a consumer group to the IoT hub for the 'events' endpoint.
Add-AzIotHubEventHubConsumerGroup -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EventHubConsumerGroupName $iotHubConsumerGroup `
  -EventHubEndpointName "events"

# The storage account name must be globally unique, so add a random value to the end.
$storageAccountName = "contosostorage" + $randomValue
Write-Host "storage account name is " $storageAccountName

# Create the storage account to be used as a routing destination.
# Save the context for the storage account 
#   to be used when creating a container.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
    -Name $storageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind Storage
# Retrieve the connection string from the context. 
$storageConnectionString = $storageAccount.Context.ConnectionString
Write-Host "storage connection string = " $storageConnectionString 

# Create the container in the storage account.
New-AzStorageContainer -Name $containerName `
    -Context $storageAccount.Context

# The Service Bus namespace must be globally unique,
#   so add a random value to the end.
$serviceBusNamespace = "ContosoSBNamespace" + $randomValue
Write-Host "Service Bus namespace is " $serviceBusNamespace

# Create the Service Bus namespace.
New-AzServiceBusNamespace -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name $serviceBusNamespace 

# The Service Bus queue name must be globally unique,
#  so add a random value to the end.
$serviceBusQueueName  = "ContosoSBQueue" + $randomValue
Write-Host "Service Bus queue name is " $serviceBusQueueName 

# Create the Service Bus queue to be used as a routing destination.
New-AzServiceBusQueue -ResourceGroupName $resourceGroup `
    -Namespace $serviceBusNamespace `
    -Name $serviceBusQueueName  `
    -EnablePartitioning $False 
```

### <a name="create-a-simulated-device"></a>建立模擬裝置

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

現在您已設定好基底資源，接下來請設定訊息路由。

## <a name="set-up-message-routing"></a>設定訊息路由

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

若要建立路由端點，請使用 [Add-AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint)。 若要建立端點的訊息路由，請使用 [Add-AzIotHubRoute](/powershell/module/az.iothub/Add-AzIoTHubRoute)。

### <a name="route-to-a-storage-account"></a>路由傳送至儲存體帳戶 

先設定儲存體帳戶的端點，然後建立訊息路由。

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

這些是指令碼所使用的變數，必須在您的 Cloud Shell 工作階段內設定：

**resourceGroup**：此欄位會出現兩次，請都設定為您的資源群組。

**名稱**：此欄位是要套用路由的 IoT 中樞名稱。

**endpointName**：此欄位是用來識別端點的名稱。 

**endpointType**：此欄位是端點的類型。 此值必須設定為 `azurestoragecontainer`、`eventhub`、`servicebusqueue` 或 `servicebustopic`。 針對您在此的目的，將其設定為 `azurestoragecontainer`。

**subscriptionID**：此欄位會設定為您 Azure 帳戶的訂用帳戶識別碼。

**storageConnectionString**：此值擷取自上一個指令碼中所設定的儲存體帳戶。 路由作業會使用此值來存取儲存體帳戶。

**containerName**：此欄位是儲存體帳戶中作為資料寫入目的地容器的名稱。

**Encoding**：請將此欄位設定為 `AVRO` 或 `JSON`。 這會指定預存資料的格式。 預設值為 AVRO。

**routeName**：此欄位是所要設定路由的名稱。 

**condition**：此欄位是用來篩選此端點所收到訊息的查詢。 路由傳送至儲存體的訊息會有 `level="storage"` 的查詢條件。

**enabled**：此欄位預設為 `true`，表示應於建立訊息路由後加以啟用。

將此指令碼複製並貼到 Cloud Shell 視窗。

```powershell
##### ROUTING FOR STORAGE #####

$endpointName = "ContosoStorageEndpoint"
$endpointType = "azurestoragecontainer"
$routeName = "ContosoStorageRoute"
$condition = 'level="storage"'
```

下一個步驟是建立儲存體帳戶的路由端點。 您也會指定要用來儲存結果的容器。 在建立儲存體帳戶時就會建立容器。

```powershell
# Create the routing endpoint for storage.
# Specify 'AVRO' or 'JSON' for the encoding of the data.
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $storageConnectionString  `
  -ContainerName $containerName `
  -Encoding AVRO
```

接下來，建立儲存體端點的訊息路由。 訊息路由會指定要將符合查詢規格的訊息傳送至何處。

```powershell
# Create the route for the storage endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="route-to-a-service-bus-queue"></a>路由傳送至服務匯流排佇列

現在來設定服務匯流排佇列的路由。 若要擷取服務匯流排佇列的連接字串，您必須建立已定義正確權限的授權規則。 下列指令碼會針對稱為 `sbauthrule` 的服務匯流排佇列建立授權規則，並將權限設定為 `Listen Manage Send`。 此授權規則設定好之後，便可用來擷取佇列的連接字串。

```powershell
##### ROUTING FOR SERVICE BUS QUEUE #####

# Create the authorization rule for the Service Bus queue.
New-AzServiceBusAuthorizationRule `
  -ResourceGroupName $resourceGroup `
  -NamespaceName $serviceBusNamespace `
  -Queue $serviceBusQueueName `
  -Name "sbauthrule" `
  -Rights @("Manage","Listen","Send")
```

現在，請使用授權規則來擷取服務匯流排佇列的金鑰。 稍後指令碼將會使用此授權規則來擷取連接字串。

```powershell
$sbqkey = Get-AzServiceBusKey `
    -ResourceGroupName $resourceGroup `
    -NamespaceName $serviceBusNamespace `
    -Queue $servicebusQueueName `
    -Name "sbauthrule"
```

現在，請設定服務匯流排佇列的路由端點和訊息路由。 這些是指令碼所使用的變數，必須在您的 Cloud Shell 工作階段內設定：

**endpointName**：此欄位是用來識別端點的名稱。 

**endpointType**：此欄位是端點的類型。 此值必須設定為 `azurestoragecontainer`、`eventhub`、`servicebusqueue` 或 `servicebustopic`。 針對您在此的目的，將其設定為 `servicebusqueue`。

**routeName**：此欄位是所要設定路由的名稱。 

**condition**：此欄位是用來篩選此端點所收到訊息的查詢。 路由傳送至服務匯流排佇列的訊息會有 `level="critical"` 的查詢條件。

以下是服務匯流排佇列訊息路由的 Azure PowerShell。

```powershell
$endpointName = "ContosoSBQueueEndpoint"
$endpointType = "servicebusqueue"
$routeName = "ContosoSBQueueRoute"
$condition = 'level="critical"'

# Add the routing endpoint, using the connection string property from the key.
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $sbqkey.PrimaryConnectionString

# Set up the message route for the Service Bus queue endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="view-message-routing-in-the-portal"></a>在入口網站中檢視訊息路由

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>後續步驟

現在您已設定好所有資源，也設定好訊息路由，接下來請前進到下一個教學課程，以了解如何將訊息傳送至 IoT 中樞，並看看訊息如何路由傳送至不同目的地。 

> [!div class="nextstepaction"]
> [第 2 部份 - 檢視訊息路由結果](tutorial-routing-view-message-routing-results.md)
