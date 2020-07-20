---
title: 使用 Azure Resource Manager 範本設定 Azure IoT 中樞的訊息路由
description: 使用 Azure Resource Manager 範本設定 Azure IoT 中樞的訊息路由
author: robinsh
manager: philmeagit st
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 8f245653a8b84944e1e8a3f48a49992f0065be58
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "74084397"
---
# <a name="tutorial-use-an-azure-resource-manager-template-to-configure-iot-hub-message-routing"></a>教學課程：使用 Azure Resource Manager 範本設定 IoT 中樞的訊息路由

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="message-routing"></a>訊息路由

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

## <a name="download-the-template-and-parameters-file"></a>下載範本和參數檔案

在本教學課程的第二個部分中，您會下載並執行 Visual Studio 應用程式以將訊息傳送至 IoT 中樞。 該下載中有一個資料夾，內含 Azure Resource Manager 範本和參數檔案，以及 Azure CLI 和 PowerShell 指令碼。

請繼續進行，並立即下載 [Azure IoT C# 範例](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)。 將 master.zip 檔案解壓縮。 Resource Manager 範本和參數檔案位於 /iot-hub/Tutorials/Routing/SimulatedDevice/resources/ 中，名稱分別是 **template_iothub.json** 和 **template_iothub_parameters.json**。

## <a name="create-your-resources"></a>建立資源

您將會使用 Azure Resource Manager (RM) 範本來建立所有資源。 Azure CLI 和 PowerShell 指令碼一次可以執行幾行。 一個步驟就能部署好 RM 範本。 本文會個別說明每個區段以便讓您了解。 接著會說明如何部署範本，並建立虛擬裝置以供測試。 範本部署好之後，您可以在入口網站中檢視訊息路由的組態。

有數個資源名稱必須是全域唯一的，例如 IoT 中樞名稱和儲存體帳戶名稱。 為了簡化資源的命名方式，那些資源的名稱會設定為附加以目前的日期/時間產生的隨機英數字元值。 

如果您查看範本，您就會看到針對這些資源所設定的變數，會在何處採用傳入的參數並對該參數串連 randomValue  。 

下列區段說明所使用的參數。

### <a name="parameters"></a>參數

這些參數大多具有預設值。 以 **_in** 結尾的參數會串連 randomValue  以便成為全域唯一的值。 

**randomValue**：這個值會從部署範本當下的日期/時間來產生。 此欄位不在參數檔案內，因為其會在範本當中產生。

**subscriptionId**：此欄位會為您設定為範本部署目的地的訂用帳戶。 此欄位不在參數檔案內，因為會由系統為您設定。

**IoTHubName_in**：此欄位是基底 IoT 中樞名稱，會串連 randomValue 以便成為全域唯一的值。

**location**：此欄位是您要作為部署目的地的 Azure 區域，例如「westus」。

**consumer_group**：此欄位是針對通過路由端點而來的訊息所設定的取用者群組。 其可用來篩選 Azure 串流分析中的結果。 例如，其中有可以取得所有項目的整個資料流，如果您有將 consumer_group 設定為 **Contoso** 的傳入資料，也可以將 Azure 串流分析資料流 (和 Power BI 報告) 設定為只顯示這些項目。 本教學課程的第 2 部分會使用到此欄位。

**sku_name**：此欄位是 IoT 中樞的規模。 此值必須為 S1 以上；免費層不允許多個端點，因此不適用於本教學課程。

**sku_units**：此欄位會與 **sku_name** 一同出現，其為可使用的 IoT 中樞單位數目。

**d2c_partitions**：此欄位是用於事件資料流的分割區數目。

**storageAccountName_in**：此欄位是所要建立儲存體帳戶的名稱。 訊息會路由傳送至這個儲存體帳戶中的容器。 此欄位會串連 randomValue 以便成為全域唯一的值。

**storageContainerName**：此欄位是路由傳送至儲存體帳戶的訊息儲存所在容器的名稱。

**storage_endpoint**：此欄位是訊息路由所使用儲存體帳戶端點的名稱。

**service_bus_namespace_in**：此欄位是所要建立服務匯流排命名空間的名稱。 此值會串連 randomValue 以便成為全域唯一的值。

**service_bus_queue_in**：此欄位是用於路由傳送訊息的服務匯流排佇列名稱。 此值會串連 randomValue 以便成為全域唯一的值。

**AuthRules_sb_queue**：此欄位是服務匯流排佇列的授權規則，可用來擷取佇列的連接字串。

### <a name="variables"></a>變數

這些值會在範本中用到，且大多衍生自參數。

**queueAuthorizationRuleResourceId**：此欄位是服務匯流排佇列授權規則的 ResourceId。 ResourceId 會再用來擷取佇列的連接字串。

**iotHubName**：此欄位是串連 randomValue 後的 IoT 中樞名稱。 

**storageAccountName**：此欄位是串連 randomValue 後的儲存體帳戶名稱。 

**service_bus_namespace**：此欄位是串連 randomValue 後的命名空間。

**service_bus_queue**：此欄位是串連 randomValue 後的服務匯流排佇列名稱。

**sbVersion**：所要使用服務匯流排 API 的版本。 在此例為「2017-04-01」。

### <a name="resources-storage-account-and-container"></a>資源：儲存體帳戶和容器

所建立的第一個資源是儲存體帳戶，以及要作為訊息路由傳送目的地的容器。 此容器是儲存體帳戶底下的資源。 其有儲存體帳戶的 `dependsOn` 子句，但必須先建立儲存體帳戶再建立容器。

此區段的樣貌如下：

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2018-07-01",
    "location": "[parameters('location')]",
    "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
    },
    "kind": "Storage",
    "properties": {},
    "resources": [
        {
        "type": "blobServices/containers",
        "apiVersion": "2018-07-01",
        "name": "[concat('default/', parameters('storageContainerName'))]",
        "properties": {
            "publicAccess": "None"
            } ,
        "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
            ]
        }
    ]
}
```

### <a name="resources-service-bus-namespace-and-queue"></a>資源：服務匯流排命名空間和佇列

所建立的第二個資源是服務匯流排命名空間，以及作為訊息路由傳送目的地的服務匯流排佇列。 SKU 會設定為標準。 API 版本則擷取自變數。 其在部署此區段時也會設定為啟動服務匯流排命名空間 (狀態：作用中)。 

```json
{
    "type": "Microsoft.ServiceBus/namespaces",
    "comments": "The Sku should be 'Standard' for this tutorial.",
    "sku": {
        "name": "Standard",
        "tier": "Standard"
    },
    "name": "[variables('service_bus_namespace')]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "properties": {
        "provisioningState": "Succeeded",
        "metricId": "[concat('a4295411-5eff-4f81-b77e-276ab1ccda12:', variables('service_bus_namespace'))]",
        "serviceBusEndpoint": "[concat('https://', variables('service_bus_namespace'),'.servicebus.windows.net:443/')]",
        "status": "Active"
    },
    "dependsOn": []
}
```

此區段會建立服務匯流排佇列。 這個部分的指令碼具有 `dependsOn` 子句，以確保會先建立命名空間再建立佇列。

```json
{
    "type": "Microsoft.ServiceBus/namespaces/queues",
    "name": "[concat(variables('service_bus_namespace'), '/', variables('service_bus_queue'))]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "scale": null,
    "properties": {},
    "dependsOn": [
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]"
    ]
}
```

### <a name="resources-iot-hub-and-message-routing"></a>資源：IoT 中樞和訊息路由

現在您已建立儲存體帳戶和服務匯流排佇列，接下來請建立 IoT 中樞以作為訊息的路由傳送目的地。 RM 範本會使用 `dependsOn` 子句，因此不會嘗試先建立中樞再建立服務匯流排資源和儲存體帳戶。 

以下是 IoT 中樞區段的第一個部分。 這個部分的範本會設定相依性，並使用屬性來開始。

```json
{
    "apiVersion": "2018-04-01",
    "type": "Microsoft.Devices/IotHubs",
    "name": "[variables('IoTHubName')]",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces/queues', variables('service_bus_namespace'), variables('service_bus_queue'))]"
    ],
    "properties": {
        "eventHubEndpoints": {}
            "events": {
                "retentionTimeInDays": 1,
                "partitionCount": "[parameters('d2c_partitions')]"
                }
            },
```

下一個區段則是 Iot 中樞的訊息路由組態區段。 首先是端點區段。 這個部分的範本會設定服務匯流排佇列和儲存體帳戶的路由端點，包括連接字串。

若要建立佇列的連接字串，您需要以內嵌方式擷取的 queueAuthorizationRulesResourcedId。 若要建立儲存體帳戶的連接字串，您必須先擷取主要儲存體金鑰，再依照連接字串的格式來加以使用。

端點組態也可用來將 Blob 格式設定為 `AVRO` 或 `JSON`。

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

 ```json
"routing": {
    "endpoints": {
        "serviceBusQueues": [
        {
            "connectionString": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]",
            "name": "[parameters('service_bus_queue_endpoint')]",
            "subscriptionId": "[parameters('subscriptionId')]", 
            "resourceGroup": "[resourceGroup().Name]"
        }
        ],
        "serviceBusTopics": [],
        "eventHubs": [],
        "storageContainers": [
            {
                "connectionString": 
                "[Concat('DefaultEndpointsProtocol=https;AccountName=',variables('storageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).keys[0].value)]",
                "containerName": "[parameters('storageContainerName')]",
                "fileNameFormat": "{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}",
                "batchFrequencyInSeconds": 100,
                "maxChunkSizeInBytes": 104857600,
                "encoding": "avro",
                "name": "[parameters('storage_endpoint')]",
                "subscriptionId": "[parameters('subscriptionId')]",
                "resourceGroup": "[resourceGroup().Name]"
            }
        ]
    },
```

下一個區段適用於路由傳送至端點的訊息。 每個端點會設定一個，所以服務匯流排佇列會有一個，儲存體帳戶容器會有一個。

請記住，要路由傳送至儲存體的訊息會有 `level="storage"` 的查詢條件，要路由傳送至服務匯流排佇列的訊息則會有 `level="critical"` 的查詢條件。

```json
"routes": [
    {
        "name": "contosoStorageRoute",
        "source": "DeviceMessages",
        "condition": "level=\"storage\"",
        "endpointNames": [
            "[parameters('storage_endpoint')]"
            ],
        "isEnabled": true
    },
    {
        "name": "contosoSBQueueRoute",
        "source": "DeviceMessages",
        "condition": "level=\"critical\"",
        "endpointNames": [
            "[parameters('service_bus_queue_endpoint')]"
            ],
        "isEnabled": true
    }
],
```

此 json 會顯示 IoT 中樞區段的其餘部分，內含該中樞的預設資訊和 SKU。

```json
            "fallbackRoute": {
                "name": "$fallback",
                "source": "DeviceMessages",
                "condition": "true",
                "endpointNames": [
                    "events"
                ],
                "isEnabled": true
            }
        },
        "storageEndpoints": {
            "$default": {
                "sasTtlAsIso8601": "PT1H",
                "connectionString": "",
                "containerName": ""
            }
        },
        "messagingEndpoints": {
            "fileNotifications": {
                "lockDurationAsIso8601": "PT1M",
                "ttlAsIso8601": "PT1H",
                "maxDeliveryCount": 10
            }
        },
        "enableFileUploadNotifications": false,
        "cloudToDevice": {
            "maxDeliveryCount": 10,
            "defaultTtlAsIso8601": "PT1H",
            "feedback": {
                "lockDurationAsIso8601": "PT1M",
                "ttlAsIso8601": "PT1H",
                "maxDeliveryCount": 10
            }
        }
    },
    "sku": {
        "name": "[parameters('sku_name')]",
        "capacity": "[parameters('sku_units')]"
    }
}
```

### <a name="resources-service-bus-queue-authorization-rules"></a>資源：服務匯流排佇列授權規則

服務匯流排佇列授權規則可用來擷取服務匯流排佇列的連接字串。 其使用 `dependsOn` 子句來確保不會比服務匯流排命名空間和服務匯流排佇列早建立。

```json
{
    "type": "Microsoft.ServiceBus/namespaces/queues/authorizationRules",
    "name": "[concat(variables('service_bus_namespace'), '/', variables('service_bus_queue'), '/', parameters('AuthRules_sb_queue'))]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "scale": null,
    "properties": {
        "rights": [
            "Send"
        ]
    },
    "dependsOn": [
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces/queues', variables('service_bus_namespace'), variables('service_bus_queue'))]"
    ]
},
```

### <a name="resources-consumer-group"></a>資源：取用者群組

在此區段中，您會針對要供本教學課程第二部分中的 Azure 串流分析使用的 IoT 中樞資料建立取用者群組。

```json
{
    "type": "Microsoft.Devices/IotHubs/eventHubEndpoints/ConsumerGroups",
    "name": "[concat(variables('iotHubName'), '/events/',parameters('consumer_group'))]",
    "apiVersion": "2018-04-01",
    "dependsOn": [
        "[concat('Microsoft.Devices/IotHubs/', variables('iotHubName'))]"
    ]
}
```

### <a name="resources-outputs"></a>資源：輸出

如果您想要將值傳回給部署指令碼以便顯示出來，請使用輸出區段。 這個部分的範本會傳回服務匯流排佇列的連接字串。 傳回值並非必要，在此納入是為了舉例說明如何將結果傳回給發出呼叫的指令碼。

```json
"outputs": {
    "sbq_connectionString": {
      "type": "string",
      "value": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]"
    }
  }
```

## <a name="deploy-the-rm-template"></a>部署 RM 範本

若要將範本部署至 Azure，請將範本和參數檔案上傳至 Azure Cloud Shell，然後執行指令碼來部署範本。 開啟 Azure Cloud Shell 並登入。 此範例會使用 PowerShell。

若要上傳檔案，請選取功能表列中的 [上傳/下載檔案]  圖示，然後選擇 [上傳]。

![Cloud Shell 功能表列，已醒目提示 [上傳/下載檔案]](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_files.png)

使用彈現的檔案總管，尋找您本機磁碟上的檔案並加以選取，然後選擇 [開啟]  。

檔案上傳之後，會有結果對話方塊顯示如下圖所示的內容。

![Cloud Shell 功能表列，已醒目提示 [上傳/下載檔案]](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_results.png)

檔案會上傳至 Cloud Shell 執行個體所使用的共用。 

執行指令碼來執行部署。 此指令碼的最後一行會擷取已設定要傳回的變數，也就是服務匯流排佇列的連接字串。

指令碼會設定並使用下列變數：

**$RGName** 是要作為範本部署目的地的資源群組名稱。 會先建立此欄位再部署範本。

**$location** 是要用於此範本的 Azure 位置，例如「westus」。

**deploymentname** 是指派給部署以便擷取所傳回變數值的名稱。

以下是 PowerShell 指令碼。 請複製此 PowerShell 指令碼並將其貼到 Cloud Shell 視窗，然後按 Enter 鍵來加以執行。

```powershell
$RGName="ContosoResources"
$location = "westus"
$deploymentname="contoso-routing"

# Remove the resource group if it already exists. 
#Remove-AzResourceGroup -name $RGName 
# Create the resource group.
New-AzResourceGroup -name $RGName -Location $location 

# Set a path to the parameter file. 
$parameterFile = "$HOME/template_iothub_parameters.json"
$templateFile = "$HOME/template_iothub.json"

# Deploy the template.
New-AzResourceGroupDeployment `
    -Name $deploymentname `
    -ResourceGroupName $RGName `
    -TemplateParameterFile $parameterFile `
    -TemplateFile $templateFile `
    -verbose

# Get the returning value of the connection string.
(Get-AzResourceGroupDeployment -ResourceGroupName $RGName -Name $deploymentname).Outputs.sbq_connectionString.value
```

如果您收到指令碼錯誤，則可以在本機編輯指令碼、將其重新上傳至 Cloud Shell，然後再次執行指令碼。 指令碼順利完成執行後，請繼續進行下一個步驟。

## <a name="create-simulated-device"></a>建立模擬裝置

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="view-message-routing-in-the-portal"></a>在入口網站中檢視訊息路由

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>後續步驟

現在您已設定好所有資源，也設定好訊息路由，接下來請前進到下一個教學課程，以了解如何處理及顯示所路由傳送訊息的相關資訊。

> [!div class="nextstepaction"]
> [第 2 部份 - 檢視訊息路由結果](tutorial-routing-view-message-routing-results.md)
