---
title: 以儲存體佇列作為 Azure 事件方格事件的事件處理常式
description: 說明如何使用 Azure 儲存體佇列作為「Azure 事件方格」事件的事件處理常式。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: f62f2b5bc01518af29bd1deb17a38e9fe105a4ed
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.contentlocale: zh-TW
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800561"
---
# <a name="storage-queue-as-an-event-handler-for-azure-event-grid-events"></a>以儲存體佇列作為 Azure 事件方格事件的事件處理常式
事件處理常式是傳送事件的位置。 處理常式會採取一些進一步的動作來處理事件。 有幾項 Azure 服務已自動設定為會處理事件，**Azure 佇列儲存體**是其中之一。 

使用**佇列儲存體**來接收需要提取的事件。 若執行中的流程太過冗長導致回應時間過久，您可以使用佇列儲存體。 藉由傳送事件至佇列儲存體，應用程式即可按照自己的排程提取並處理事件。

## <a name="tutorials"></a>教學課程
如需使用佇列儲存體作為事件處理常式的範例，請參閱下列教學課程。 

|Title  |描述  |
|---------|---------|
| [快速入門：使用 Azure CLI 和事件方格將自訂事件路由至 Azure 佇列儲存體](custom-event-to-queue-storage.md) | 描述如何將自訂事件傳送至佇列儲存體。 |

## <a name="rest-examples-for-put"></a>REST 範例 (用於 PUT)

### <a name="storage-queue-as-the-event-handler"></a>儲存體佇列作為事件處理常式

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "StorageQueue",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>",
                "queueName": "<QUEUE NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="storage-queue-as-the-event-handler---delivery-with-managed-identity"></a>儲存體佇列作為事件處理常式 - 使用受控識別進行傳遞

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
                "endpointType": "StorageQueue",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>",
                    "queueName": "<QUEUE NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="storage-queue-as-a-deadletter-destination"></a>儲存體佇列作為無效信件目的地

```json
{
    "name": "",
    "properties": 
    {
        "destination": 
        {
            "endpointType": "StorageQueue",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<DESTINATION STORAGE>",
                "queueName": "queue1"
            }
        },
        "eventDeliverySchema": "EventGridSchema",
        "deadLetterDestination": 
        {
            "endpointType": "StorageBlob",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<DEADLETTER STORAGE>",
                "blobContainerName": "test"
            }
        }
    }
}
```

### <a name="storage-queue-as-a-deadletter-destination---managed-identity"></a>儲存體佇列作為無效信件目的地 - 受控身分識別

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "StorageQueue",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<DESTINATION STORAGE>",
                "queueName": "queue1"
            }
        },
        "eventDeliverySchema": "EventGridSchema",
        "deadLetterWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "deadLetterDestination": 
            {
                "endpointType": "StorageBlob",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<DEADLETTER STORAGE>",
                    "blobContainerName": "test"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>後續步驟
如需支援的事件處理常式清單，請參閱[事件處理常式](event-handlers.md)一文。 
