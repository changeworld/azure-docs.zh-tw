---
title: 以轉送混合式連線作為 Azure 事件方格事件的事件處理常式
description: 說明如何使用 Azure 轉送作為「Azure 事件方格」事件的事件處理常式。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 3807e2d125d652b8f5ed7c9dec1b972d69f699f6
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91270197"
---
# <a name="relay-hybrid-connection-as-an-event-handler-for-azure-event-grid-events"></a>以轉送混合式連線作為 Azure 事件方格事件的事件處理常式
事件處理常式是傳送事件的位置。 處理常式會採取一些進一步的動作來處理事件。 有幾項 Azure 服務已自動設定為會處理事件，**Azure 轉送**是其中之一。 

使用 **Azure 轉送**的混合式連線將事件傳送至應用程式，這些應用程式都位於企業網路中，而且沒有可公開存取的端點。

## <a name="tutorials"></a>教學課程
如需使用 Azure 轉送混合式連線作為事件處理常式的範例，請參閱下列教學課程。 

|Title  |描述  |
|---------|---------|
| [教學課程：將事件傳送至混合式連線](custom-event-to-hybrid-connection.md) | 將自訂事件傳送至現有的混合式連線，以透過接聽應用程式進行處理。 |

## <a name="rest-example-for-put"></a>REST 範例 (用於 PUT)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "HybridConnection",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Relay/namespaces/<RELAY NAMESPACE NAME>/hybridconnections/<HYBRID CONNECTION NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

> [!NOTE]
> 不支援將事件傳遞至 **另一個租** 使用者中的 Azure 轉送混合式連線。 

## <a name="next-steps"></a>後續步驟
如需支援的事件處理常式清單，請參閱[事件處理常式](event-handlers.md)一文。 