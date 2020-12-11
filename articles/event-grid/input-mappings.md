---
title: 將自訂欄位對應到 Azure 事件格線結構描述
description: 本文說明如何在您的事件資料不符合事件方格架構的情況下，將您的自訂架構轉換為 Azure 事件方格架構。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 34381782c9337631b0aa04e47eb5897a8071139a
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109193"
---
# <a name="map-custom-fields-to-event-grid-schema"></a>將自訂欄位對應到事件格線結構描述

如果您的事件資料不符合預期的[事件方格結構描述](event-schema.md)，您仍然可以使用事件方格來將事件路由傳送給訂閱者。 本文說明如何將結構描述對應到事件格線結構描述。

## <a name="original-event-schema"></a>原始的事件結構描述

讓我們假設您的應用程式會以下列格式傳送事件：

```json
[
  {
    "myEventTypeField":"Created",
    "resource":"Users/example/Messages/1000",
    "resourceData":{"someDataField1":"SomeDataFieldValue"}
  }
]
```

儘管該格式不符合必要的結構描述，但事件格線可讓您將欄位對應到結構描述。 或者，您可以在原始結構描述中接收值。

## <a name="create-custom-topic-with-mapped-fields"></a>使用對應的欄位建立自訂主題

建立自訂主題時，指定如何將欄位從您的原始事件對應到事件格線結構描述。 您可用來自訂對應的值有三個：

* **輸入結構描述** 值會指定結構描述的類型。 可用的選項為 CloudEvents 結構描述、自訂事件結構描述或事件方格結構描述。 預設值為事件方格結構描述。 在您的結構描述與事件方格結構描述之間建立自訂對應時，請使用自訂事件結構描述。 當事件採用 CloudEvents 格式時，請使用 CloudEvents 架構。

* **對應預設值** 屬性會針對事件方格結構描述中的欄位指定預設值。 您可以設定 `subject`、`eventtype` 和 `dataversion` 的預設值。 一般而言，當您的自訂結構描述不包含對應到那三個欄位其中一個的欄位時，您會使用此參數。 例如，您可以指定一律將該資料版本設定為 **1.0**。

* **對應欄位** 值會將欄位從您的結構描述對應到事件方格結構描述。 您會以空格分隔的索引鍵/值組來指定值。 針對索引鍵名稱，使用事件格線欄位的名稱。 針對值，使用欄位的名稱。 您可以針對 `id`、`topic`、`eventtime`、`subject`、`eventtype` 和 `dataversion` 使用索引鍵名稱。

若要使用 Azure CLI 建立自訂主題，請使用：

```azurecli-interactive
az eventgrid topic create \
  -n demotopic \
  -l eastus2 \
  -g myResourceGroup \
  --input-schema customeventschema \
  --input-mapping-fields eventType=myEventTypeField \
  --input-mapping-default-values subject=DefaultSubject dataVersion=1.0
```

對於 PowerShell，請使用：

```azurepowershell-interactive
New-AzEventGridTopic `
  -ResourceGroupName myResourceGroup `
  -Name demotopic `
  -Location eastus2 `
  -InputSchema CustomEventSchema `
  -InputMappingField @{eventType="myEventTypeField"} `
  -InputMappingDefaultValue @{subject="DefaultSubject"; dataVersion="1.0" }
```

## <a name="subscribe-to-event-grid-topic"></a>訂閱事件格線主題

訂閱自訂主題時，需指定您想要用來接收事件的結構描述。 您可指定 CloudEvents 結構描述、自訂事件結構描述或事件方格結構描述。 預設值為事件方格結構描述。

下列範例會訂閱事件方格主題，並使用事件方格結構描述。 對於 Azure CLI，請使用：

```azurecli-interactive
topicid=$(az eventgrid topic show --name demoTopic -g myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub1 \
  --event-delivery-schema eventgridschema \
  --endpoint <endpoint_URL>
```

下一個範例會使用事件的輸入結構描述：

```azurecli-interactive
az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub2 \
  --event-delivery-schema custominputschema \
  --endpoint <endpoint_URL>
```

下列範例會訂閱事件方格主題，並使用事件方格結構描述。 對於 PowerShell，請使用：

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName myResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub1 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema EventGridSchema
```

下一個範例會使用事件的輸入結構描述：

```azurepowershell-interactive
New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub2 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema CustomInputSchema
```

## <a name="publish-event-to-topic"></a>將事件發行到主題

您現在已經準備好將事件傳送到自訂主題，並查看對應的結果。 下列指令碼會在[範例結構描述](#original-event-schema)中公佈事件：

對於 Azure CLI，請使用：

```azurecli-interactive
endpoint=$(az eventgrid topic show --name demotopic -g myResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name demotopic -g myResourceGroup --query "key1" --output tsv)

event='[ { "myEventTypeField":"Created", "resource":"Users/example/Messages/1000", "resourceData":{"someDataField1":"SomeDataFieldValue"} } ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
```

對於 PowerShell，請使用：

```azurepowershell-interactive
$endpoint = (Get-AzEventGridTopic -ResourceGroupName myResourceGroup -Name demotopic).Endpoint
$keys = Get-AzEventGridTopicKey -ResourceGroupName myResourceGroup -Name demotopic

$htbody = @{
    myEventTypeField="Created"
    resource="Users/example/Messages/1000"
    resourceData= @{
        someDataField1="SomeDataFieldValue"
    }
}

$body = "["+(ConvertTo-Json $htbody)+"]"
Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

現在，請查看您的 Webhook 端點。 這兩個訂用帳戶在不同的結構描述中傳遞了事件。

第一個訂用帳戶使用了事件格線結構描述。 已傳遞事件的格式如下：

```json
{
  "id": "aa5b8e2a-1235-4032-be8f-5223395b9eae",
  "eventTime": "2018-11-07T23:59:14.7997564Z",
  "eventType": "Created",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.EventGrid/topics/demotopic",
  "subject": "DefaultSubject",
  "data": {
    "myEventTypeField": "Created",
    "resource": "Users/example/Messages/1000",
    "resourceData": {
      "someDataField1": "SomeDataFieldValue"
    }
  }
}
```

這些欄位包含自訂主題的對應。 **myEventTypeField** 會對應到 **EventType**。 使用了 **DataVersion** 和 **Subject** 的預設值。 **Data** 物件包含原始的事件結構描述欄位。

第二個訂用帳戶使用了輸入事件結構描述。 已傳遞事件的格式如下：

```json
{
  "myEventTypeField": "Created",
  "resource": "Users/example/Messages/1000",
  "resourceData": {
    "someDataField1": "SomeDataFieldValue"
  }
}
```

請注意已傳遞原始欄位。

## <a name="next-steps"></a>後續步驟

* 如需事件傳遞和重試的相關資訊，請參閱[事件格線訊息傳遞與重試](delivery-and-retry.md)。
* 如需 Event Grid 的簡介，請參閱[關於 Event Grid](overview.md)。
* 若要快速地開始使用 Event Grid，請參閱[使用 Azure Event Grid 建立和路由傳送自訂事件](custom-event-quickstart.md)。
