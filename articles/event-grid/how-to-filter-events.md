---
title: 如何針對 Azure 事件格線篩選事件
description: 本文說明如何在建立 Event Grid 訂用帳戶時篩選事件（依事件種類、依主題、依運算子和資料等）。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 63a5cdbff79af52d9f96cf410a820c6cfc530066
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "79454018"
---
# <a name="filter-events-for-event-grid"></a>針對事件格線篩選事件

本文示範如何在建立事件格線訂用帳戶期間篩選事件。 若要了解適用於事件篩選的選項，請參閱[了解適用於事件格線訂用帳戶的事件篩選](event-filtering.md)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="filter-by-event-type"></a>依事件類型進行篩選

建立事件格線訂用帳戶時，您可以指定要將哪些[事件類型](event-schema.md)傳送至端點。 本節中的範例會為資源群組建立事件訂閱，但會限制傳送至 `Microsoft.Resources.ResourceWriteFailure` 和 `Microsoft.Resources.ResourceWriteSuccess` 的事件。 如果您在依事件類型篩選事件時需要更多彈性，請參閱依進階運算子和資料欄位進行篩選。

針對 PowerShell，請在建立訂閱時使用 `-IncludedEventType` 參數。

```powershell
$includedEventTypes = "Microsoft.Resources.ResourceWriteFailure", "Microsoft.Resources.ResourceWriteSuccess"

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Endpoint <endpoint-URL> `
  -IncludedEventType $includedEventTypes
```

針對 Azure CLI，請使用 `--included-event-types` 參數。 下列範例會在 Bash 殼層中使用 Azure CLI：

```azurecli
includedEventTypes="Microsoft.Resources.ResourceWriteFailure Microsoft.Resources.ResourceWriteSuccess"

az eventgrid event-subscription create \
  --name demoSubToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --included-event-types $includedEventTypes
```

針對 Resource Manager 範本，請使用 `includedEventTypes` 屬性。

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [
          "Microsoft.Resources.ResourceWriteFailure",
          "Microsoft.Resources.ResourceWriteSuccess"
        ]
      }
    }
  }
]
```

## <a name="filter-by-subject"></a>依主旨進行篩選

您可以依事件資料中的主旨來篩選事件。 您可以指定要符合主旨開頭或結尾的值。 如果您在依主旨篩選事件時需要更多彈性，請參閱依進階運算子和資料欄位進行篩選。

在下列 PowerShell 範例中，您會建立能依主旨開頭進行篩選的事件訂閱。 您會使用 `-SubjectBeginsWith` 參數來將事件限制為適用於特定資源的事件。 您會傳遞網路安全性群組的資源識別碼。

```powershell
$resourceId = (Get-AzResource -ResourceName demoSecurityGroup -ResourceGroupName myResourceGroup).ResourceId

New-AzEventGridSubscription `
  -Endpoint <endpoint-URL> `
  -EventSubscriptionName demoSubscriptionToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -SubjectBeginsWith $resourceId
```

下一個 PowerShell 範例會建立適用於 Blob 儲存體的訂閱。 它會將事件限制為具有以 `.jpg` 為結尾之主旨的事件。

```powershell
$storageId = (Get-AzStorageAccount -ResourceGroupName myResourceGroup -AccountName $storageName).Id

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToStorage `
  -Endpoint <endpoint-URL> `
  -ResourceId $storageId `
  -SubjectEndsWith ".jpg"
```

在下列 Azure CLI 範例中，您會建立能依主旨開頭進行篩選的事件訂閱。 您會使用 `--subject-begins-with` 參數來將事件限制為適用於特定資源的事件。 您會傳遞網路安全性群組的資源識別碼。

```azurecli
resourceId=$(az resource show --name demoSecurityGroup --resource-group myResourceGroup --resource-type Microsoft.Network/networkSecurityGroups --query id --output tsv)

az eventgrid event-subscription create \
  --name demoSubscriptionToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --subject-begins-with $resourceId
```

下一個 Azure CLI 範例會建立適用於 Blob 儲存體的訂閱。 它會將事件限制為具有以 `.jpg` 為結尾之主旨的事件。

```azurecli
storageid=$(az storage account show --name $storageName --resource-group myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name demoSubToStorage \
  --endpoint <endpoint-URL> \
  --subject-ends-with ".jpg"
```

在下列 Resource Manager 範本範例中，您會建立能依主旨開頭進行篩選的事件訂閱。 您會使用 `subjectBeginsWith` 屬性來將事件限制為適用於特定資源的事件。 您會傳遞網路安全性群組的資源識別碼。

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "[resourceId('Microsoft.Network/networkSecurityGroups','demoSecurityGroup')]",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

下一個 Resource Manager 範本範例會建立適用於 Blob 儲存體的訂閱。 它會將事件限制為具有以 `.jpg` 為結尾之主旨的事件。

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts/providers/eventSubscriptions",
    "name": "[concat(parameters('storageName'), '/Microsoft.EventGrid/', parameters('eventSubName'))]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectEndsWith": ".jpg",
        "subjectBeginsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

## <a name="filter-by-operators-and-data"></a>依運算子和資料進行篩選

若要以更有彈性的方式進行篩選，您可以使用運算子和資料屬性來篩選事件。

### <a name="subscribe-with-advanced-filters"></a>搭配進階篩選進行訂閱

若要了解可用來進行進階篩選的運算子和索引鍵，請參閱[進階篩選](event-filtering.md#advanced-filtering)。

這些範例會建立自訂主題。 它們會訂閱自訂主題，並依資料物件中的值進行篩選。 系統會將色彩屬性設為藍色、紅色或綠色的事件傳送到訂閱。

對於 Azure CLI，請使用：

```azurecli
topicName=<your-topic-name>
endpointURL=<endpoint-URL>

az group create -n gridResourceGroup -l eastus2
az eventgrid topic create --name $topicName -l eastus2 -g gridResourceGroup

topicid=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  -n demoAdvancedSub \
  --advanced-filter data.color stringin blue red green \
  --endpoint $endpointURL \
  --expiration-date "<yyyy-mm-dd>"
```

請留意到已針對訂閱設定[到期日](concepts.md#event-subscription-expiration)。

對於 PowerShell，請使用：

```powershell
$topicName = <your-topic-name>
$endpointURL = <endpoint-URL>

New-AzResourceGroup -Name gridResourceGroup -Location eastus2
New-AzEventGridTopic -ResourceGroupName gridResourceGroup -Location eastus2 -Name $topicName

$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Id

$expDate = '<mm/dd/yyyy hh:mm:ss>' | Get-Date
$AdvFilter1=@{operator="StringIn"; key="Data.color"; Values=@('blue', 'red', 'green')}

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint $endpointURL `
  -ExpirationDate $expDate `
  -AdvancedFilter @($AdvFilter1)
```

### <a name="test-filter"></a>測試篩選

若要測試篩選，請以將色彩欄位設為綠色的方式傳送事件。 因為綠色是篩選的其中一個值，所以會將事件傳遞到端點。

對於 Azure CLI，請使用：

```azurecli
topicEndpoint=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $topicName -g gridResourceGroup --query "key1" --output tsv)

event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "green"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

對於 PowerShell，請使用：

```powershell
$endpoint = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Endpoint
$keys = Get-AzEventGridTopicKey -ResourceGroupName gridResourceGroup -Name $topicName

$eventID = Get-Random 99999
$eventDate = Get-Date -Format s

$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="green"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

若要測試事件未傳送的案例，請以將色彩欄位設為黃色的方式傳送該事件。 由於訂閱中並沒有指定黃色的值，因此系統並不會將該事件傳送至您的訂用帳戶。

對於 Azure CLI，請使用：

```azurecli
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "yellow"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```
對於 PowerShell，請使用：

```powershell
$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="yellow"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

## <a name="next-steps"></a>後續步驟

* 如需關於監視事件傳遞的資訊，請參閱[監視 Event Grid 訊息傳遞](monitor-event-delivery.md)。
* 如需驗證金鑰的詳細資訊，請參閱 [Event Grid 安全性和驗證](security-authentication.md)。
* 若要了解 Event Grid 訂用帳戶的建立，請參閱 [Event Grid 訂用帳戶結構描述](subscription-creation-schema.md)。
