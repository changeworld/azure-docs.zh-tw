---
title: 以事件中樞作為 Azure 事件方格事件的事件處理常式
description: 說明如何使用事件中樞作為「Azure 事件方格」事件的事件處理常式。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 5e6a84c1737c6b8a575f47576aeb1d3d9efae6eb
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91322558"
---
# <a name="event-hub-as-an-event-handler-for-azure-event-grid-events"></a>以事件中樞作為 Azure 事件方格事件的事件處理常式
事件處理常式是傳送事件的位置。 處理常式會採取相關動作來處理事件。 有幾項 Azure 服務已自動設定為會處理事件，**Azure 事件中樞**是其中之一。 

從事件方格解決方案收到事件的速度比處理事件的速度更快時，請使用**事件中樞**。 事件位於事件中樞內之後，您的應用程式就可以依自己的排程處理事件中樞的事件。 您可以調整事件流程來處理傳入事件。

## <a name="tutorials"></a>教學課程
請參閱下列範例： 

|Title  |描述  |
|---------|---------|
| [快速入門：使用 Azure CLI 將自訂事件路由至 Azure 事件中樞](custom-event-to-eventhub.md) | 將自訂事件傳送至事件中樞交給應用程式處理。 |
| [Resource Manager 範本：建立事件方格自訂主題，並將事件傳送至事件中樞](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| 建立自訂主題訂用帳戶的 Resource Manager 範本。 它會將事件傳送到 Azure 事件中樞。 |

## <a name="message-properties"></a>訊息屬性
如果您使用 **事件中樞** 做為事件方格中事件的事件處理常式，這些是您在訊息標頭中收到的屬性： 

| 屬性名稱 | 描述 |
| ------------- | ----------- | 
| aeg-subscription-name | 事件訂閱名稱。 |
| aeg-delivery-count | <p>對事件進行的嘗試次數。</p> <p>範例：「1」</p> |
| aeg-event-type | <p>事件類型。</p><p> 範例："Microsoft.Storage.blobCreated"</p> | 
| aeg-metadata-version | <p>事件的中繼資料版本。</p> <p>範例："1"。</p><p> 針對**事件方格事件結構描述**，此屬性代表中繼資料版本；而針對**雲端事件結構描述**，其代表**規格版本**。 </p>|
| aeg-data-version | <p>事件的資料版本。</p><p>範例："1"。</p><p>針對**事件方格事件結構描述**，此屬性代表資料版本；而針對**雲端事件結構描述**，則不會套用。</p> |
| aeg-output-event-id | 事件方格事件的識別碼。 |

## <a name="rest-examples-for-put"></a>REST 範例 (用於 PUT)


### <a name="event-hub"></a>事件中樞

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "EventHub",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="event-hub---delivery-with-managed-identity"></a>事件中樞 - 使用受控識別進行傳遞

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
                "endpointType": "EventHub",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

> [!NOTE]
> 不支援將事件傳遞至 **另一個租** 使用者中的 Azure 事件中樞。 

## <a name="next-steps"></a>後續步驟
如需支援的事件處理常式清單，請參閱[事件處理常式](event-handlers.md)一文。 
