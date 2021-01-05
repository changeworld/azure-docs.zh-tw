---
title: 將 Azure 事件方格用於 CloudEvents 結構描述中的事件
description: 說明如何在 Azure 事件方格中將 CloudEvents 結構描述用於事件。 此服務支援雲端事件的 JSON 實作中的事件。
ms.topic: conceptual
ms.date: 11/10/2020
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 823c1aa89d38a240af819a68706a02222dd93b9f
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97881577"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>搭配使用 CloudEvents v1.0 結構描述與事件方格
除了[預設事件結構描述](event-schema.md)以外，Azure 事件方格在本質上也支援 [CloudEvents v1.0 的 JSON 實作](https://github.com/cloudevents/spec/blob/v1.0/json-format.md)和 [HTTP 通訊協定繫結](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md)中的事件。 [CloudEvents](https://cloudevents.io/) 是用來說明事件資料的[開放式規格](https://github.com/cloudevents/spec/blob/v1.0/spec.md)。

CloudEvents 提供用以發佈和取用雲端型事件的常見事件結構描述，可簡化互通性。 此結構描述可支援統一的工具、路由和處理事件的標準方式，以及將外部事件結構描述還原序列化的通用方式。 透過通用結構描述，您將可更輕鬆地跨平台整合工作。

目前有數個[共同作業者](https://github.com/cloudevents/spec/blob/master/community/contributors.md) (包括 Microsoft) 正透過 [Cloud Native Computing Foundation](https://www.cncf.io/) 建置 CloudEvents。 目前可用的版本為 1.0。

本文說明如何透過事件方格使用 CloudEvents 結構描述。


## <a name="cloudevent-schema"></a>CloudEvent 結構描述

下列範例說明 CloudEvents 格式的 Azure Blob 儲存體事件：

``` JSON
{
    "specversion": "1.0",
    "type": "Microsoft.Storage.BlobCreated",  
    "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}",
    "id": "9aeb0fdf-c01e-0131-0922-9eb54906e209",
    "time": "2019-11-18T15:13:39.4589254Z",
    "subject": "blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "dataschema": "#",
    "data": {
        "api": "PutBlockList",
        "clientRequestId": "4c5dd7fb-2c48-4a27-bb30-5361b5de920a",
        "requestId": "9aeb0fdf-c01e-0131-0922-9eb549000000",
        "eTag": "0x8D76C39E4407333",
        "contentType": "image/png",
        "contentLength": 30699,
        "blobType": "BlockBlob",
        "url": "https://gridtesting.blob.core.windows.net/testcontainer/{new-file}",
        "sequencer": "000000000000000000000000000099240000000000c41c18",
        "storageDiagnostics": {
            "batchId": "681fe319-3006-00a8-0022-9e7cde000000"
        }
    }
}
```

如需 CloudEvents v1.0 中的可用欄位、其類型和定義的詳細說明，請參閱[這裡](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes)。

在 CloudEvents 結構描述中傳遞的事件標頭值與事件方格結構描述的該值相同，不同之處在於 `content-type`。 針對 CloudEvents 結構描述，該標頭值是 `"content-type":"application/cloudevents+json; charset=utf-8"`。 針對事件方格結構描述，該標頭值是 `"content-type":"application/json; charset=utf-8"`。

## <a name="configure-event-grid-for-cloudevents"></a>設定 CloudEvents 的事件方格

在 CloudEvents 架構中，您可以使用事件方格來輸入和輸出事件。 下表描述可能的轉換：

 事件方格資源 | 輸入結構描述       | 傳遞架構
|---------------------|-------------------|---------------------
| 系統主題       | 事件方格架構 | 事件方格架構或 CloudEvent 架構
| 使用者主題/網域 | 事件方格架構 | 事件方格架構
| 使用者主題/網域 | CloudEvent 架構 | CloudEvent 架構
| 使用者主題/網域 | 自訂架構     | 自訂架構或事件方格架構或 CloudEvent 架構
| PartnerTopics       | CloudEvent 架構 | CloudEvent 架構


針對所有事件架構，事件方格需要在發行至事件方格主題時，以及建立事件訂閱時進行驗證。

如需詳細資訊，請參閱 [Event Grid 安全性和驗證](security-authentication.md)。

### <a name="input-schema"></a>輸入結構描述

當您建立自訂主題時，您可以設定自訂主題的輸入結構描述。

對於 Azure CLI，請使用：

```azurecli-interactive
az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventschemav1_0
```

對於 PowerShell，請使用：

```azurepowershell-interactive
New-AzEventGridTopic `
  -ResourceGroupName gridResourceGroup `
  -Location westcentralus `
  -Name <topic_name> `
  -InputSchema CloudEventSchemaV1_0
```

### <a name="output-schema"></a>輸出結構描述

當您建立事件訂用帳戶時，您可以設定輸出結構描述。

對於 Azure CLI，請使用：

```azurecli-interactive
topicID=$(az eventgrid topic show --name <topic-name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --source-resource-id $topicID \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventschemav1_0
```

對於 PowerShell，請使用：
```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventSchemaV1_0
```

 目前，當事件是在 CloudEvents 結構描述中傳遞時，您無法針對 Azure Functions 應用程式使用事件方格觸發程序。 使用 HTTP 觸發程序。 如需實作 HTTP 觸發程序 (該觸發程序會接收 CloudEvents 結構描述中的事件) 的範例，請參閱[搭配使用 CloudEvents 與 Azure Functions](#azure-functions)。

 ## <a name="endpoint-validation-with-cloudevents-v10"></a>使用 CloudEvents v1.0 進行端點驗證

如果您已經熟悉事件方格，則可能會注意到事件方格的端點驗證交握可防止濫用。 CloudEvents v1.0 會使用 HTTP OPTIONS 方法，實作自己的[濫用保護語義](webhook-event-delivery.md)。 您可以在 [此處](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection)閱讀相關資訊。 使用 CloudEvents 結構描述進行輸出時，事件方格會使用 CloudEvents v1.0 濫用保護來取代事件方格驗證事件機制。

<a name="azure-functions"></a>

## <a name="use-with-azure-functions"></a>與 Azure Functions 搭配使用

[Azure Functions 事件方格繫結](../azure-functions/functions-bindings-event-grid.md)未提供 CloudEvents 的原生支援，因此會使用 HTTP 觸發的函式來讀取 CloudEvents 訊息。 如果使用 HTTP 觸發程序來讀取 CloudEvents，則必須撰寫程式碼來處理 Event Grid 觸發程序自動執行的作業：

* 將驗證回應傳送給[訂用帳戶驗證要求](../event-grid/webhook-event-delivery.md)。
* 為要求本文中包含的每個事件陣列元素分別叫用一次函式。

如需在本機叫用函式時或在 Azure 中執行函式時所使用的 URL 相關資訊，請參閱 [HTTP 觸發程序繫結參考文件](../azure-functions/functions-bindings-http-webhook.md)

HTTP 觸發程序的下列範例 C# 程式碼會模擬事件格線觸發程序的行為。  使用此範例示範以 CloudEvents 結構描述傳送的事件。

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run([HttpTrigger(AuthorizationLevel.Anonymous, "post", "options", Route = null)]HttpRequestMessage req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");
    if (req.Method == HttpMethod.Options)
    {
        // If the request is for subscription validation, send back the validation code
        
        var response = req.CreateResponse(HttpStatusCode.OK);
        response.Headers.Add("Webhook-Allowed-Origin", "eventgrid.azure.net");

        return response;
    }

    var requestmessage = await req.Content.ReadAsStringAsync();
    var message = JToken.Parse(requestmessage);

    // The request is not for subscription validation, so it's for an event.
    // CloudEvents schema delivers one event at a time.
    log.LogInformation($"Source: {message["source"]}");
    log.LogInformation($"Time: {message["eventTime"]}");
    log.LogInformation($"Event data: {message["data"].ToString()}");

    return req.CreateResponse(HttpStatusCode.OK);
}
```

HTTP 觸發程序的下列範例 JavaScript 程式碼會模擬事件格線觸發程序行為。 使用此範例示範以 CloudEvents 結構描述傳送的事件。

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    
    if (req.method == "OPTIONS") {
        // If the request is for subscription validation, send back the validation code
        
        context.log('Validate request received');
        context.res = {
            status: 200,
            headers: {
                'Webhook-Allowed-Origin': 'eventgrid.azure.net',
            },
         };
    }
    else
    {
        var message = req.body;
        
        // The request is not for subscription validation, so it's for an event.
        // CloudEvents schema delivers one event at a time.
        var event = JSON.parse(message);
        context.log('Source: ' + event.source);
        context.log('Time: ' + event.eventTime);
        context.log('Data: ' + JSON.stringify(event.data));
    }
 
    context.done();
};
```

## <a name="next-steps"></a>後續步驟

* 如需關於監視事件傳遞的資訊，請參閱[監視 Event Grid 訊息傳遞](monitor-event-delivery.md)。
* 建議您測試、評論和[參與](https://github.com/cloudevents/spec/blob/master/community/CONTRIBUTING.md) CloudEvents。
* 若要了解 Event Grid 訂用帳戶的建立，請參閱 [Event Grid 訂用帳戶結構描述](subscription-creation-schema.md)。
