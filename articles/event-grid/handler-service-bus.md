---
title: 作為 Azure 事件方格事件之事件處理常式的服務匯流排佇列和主題
description: 說明如何使用作為 Azure 事件方格事件之事件處理常式的服務匯流排佇列和主題。
ms.topic: conceptual
ms.date: 09/03/2020
ms.openlocfilehash: 2b18009f8fb31f1a5f057c7395781f63f182847f
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "96024209"
---
# <a name="service-bus-queues-and-topics-as-event-handlers-for-azure-event-grid-events"></a>作為 Azure 事件方格事件之事件處理常式的服務匯流排佇列和主題
事件處理常式是傳送事件的位置。 處理常式會採取一些進一步的動作來處理事件。 有幾項 Azure 服務已自動設定為會處理事件，**Azure 服務匯流排** 是其中之一。 

您可以使用服務佇列或主題作為事件方格中事件的處理常式。 

## <a name="service-bus-queues"></a>服務匯流排佇列
您可以將事件方格中的事件直接路由至服務匯流排佇列，以用於企業應用程式中的緩衝處理或命令和控制項案例。

在 Azure 入口網站中建立事件訂用帳戶時，選取 [服務匯流排佇列] 作為端點類型，然後按一下 [選取端點] 以選擇服務匯流排佇列。

### <a name="using-cli-to-add-a-service-bus-queue-handler"></a>使用 CLI 新增服務匯流排佇列處理常式

針對 Azure CLI，下列範例會訂閱事件方格主題，並將其連線到服務匯流排佇列：

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

## <a name="service-bus-topics"></a>服務匯流排主題

您可以將事件方格中的事件直接路由至服務匯流排主題，以服務匯流排主題或命令和控制訊息案例來處理 Azure 系統事件。

在 Azure 入口網站中建立事件訂用帳時，選取 [服務匯流排主題] 作為端點類型，然後按一下 [選取和端點] 來選擇服務匯流排主題。

### <a name="using-cli-to-add-a-service-bus-topic-handler"></a>使用 CLI 新增服務匯流排主題處理常式

針對 Azure CLI，下列範例會訂閱事件方格主題，並將其連線到服務匯流排佇列：

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebustopic \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/topics/topic1
```

[!INCLUDE [event-grid-message-headers](../../includes/event-grid-message-headers.md)]

將事件傳送至服務匯流排佇列或主題作為代理訊息時，代理訊息的 `messageid` 會是內部系統識別碼。

訊息的內部系統識別碼會跨事件的日後再次傳遞進行維護，如此您就可以在服務匯流排實體上開啟 **重複偵測** ，以避免重複的傳遞。 建議您在服務匯流排實體上啟用重複偵測的持續時間，使其成為事件的存留時間 (TTL) 或最大重試持續時間 (以較長者為準)。

## <a name="rest-examples-for-put"></a>REST 範例 (用於 PUT)

### <a name="service-bus-queue"></a>服務匯流排佇列

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "ServiceBusQueue",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/queues/<SERVICE BUS QUEUE NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-queue---delivery-with-managed-identity"></a>服務匯流排佇列 - 使用受控識別進行傳遞

```json
{
    "properties": {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "ServiceBusQueue",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/queues/<SERVICE BUS QUEUE NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-topic"></a>服務匯流排主題

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "ServiceBusTopic",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/topics/<SERVICE BUS TOPIC NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-topic---delivery-with-managed-identity"></a>服務匯流排主題 - 使用受控識別進行傳遞

```json
{
    "properties": 
    {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "ServiceBusTopic",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/topics/<SERVICE BUS TOPIC NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>後續步驟
如需支援的事件處理常式清單，請參閱[事件處理常式](event-handlers.md)一文。 
