---
title: 在 Azure IoT Central 中建立規則的 Webhook | Microsoft Docs
description: 在 Azure IoT Central 中建立 Webhook，以在規則引發時自動通知其他應用程式。
author: viv-liu
ms.author: viviali
ms.date: 12/02/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: db4e48a7bff9127810b051a9ab63bbe9d78cf6da
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271612"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>在 Azure IoT Central 中建立規則的 Webhook 動作

*本主題適用於建置員和系統管理員。*

Webhook 可讓您將 IoT Central 應用程式連線至其他應用程式和服務，以進行遠端監視和通知。 Webhook 會在 IoT Central 應用程式中觸發規則時，自動通知您所連線的其他應用程式和服務。 每當觸發規則時，您的 IoT Central 應用程式就會將 POST 要求傳送至其他應用程式的 HTTP 端點。 承載包含裝置詳細資料和規則引發程式詳細資料。

## <a name="set-up-the-webhook"></a>設定 webhook

在此範例中，您會連接到 RequestBin，以在使用 webhook 引發規則時收到通知。

1. 開啟 [RequestBin](https://requestbin.net/)。

1. 建立新的 RequestBin，並複製 **Bin URL**。

1. 建立[遙測規則](tutorial-create-telemetry-rules.md)。 儲存規則，然後新增動作。

    ![Webhook 建立畫面](media/howto-create-webhooks/webhookcreate.png)

1. 選擇 Webhook 動作並提供顯示名稱，然後貼上 Bin URL 作為回呼 URL。

1. 儲存規則。

現在當觸發規則時，您會在 RequestBin 中看到新的要求。

## <a name="payload"></a>Payload

觸發規則時，會向包含 json 承載的回呼 URL 發出 HTTP POST 要求，其中含有遙測、裝置、規則和應用程式詳細資料。 承載看起來可能如下所示：

```json
{
    "id": "<id>",
    "displayName": "Webhook 1",
    "timestamp": "2019-10-24T18:27:13.538Z",
    "rule": {
        "id": "<id>",
        "displayName": "High temp alert",
        "enabled": true
    },
    "device": {
        "id": "mx1",
        "displayName": "MXChip IoT DevKit - mx1",
        "instanceOf": "<device-template-id>",
        "simulated": true,
        "provisioned": true,
        "approved": true
    },
    "data": [{
        "@id": "<id>",
        "@type": ["Telemetry"],
        "name": "temperature",
        "displayName": "Temperature",
        "value": 66.27310467496761,
        "interfaceInstanceName": "sensors"
    }],
    "application": {
        "id": "<id>",
        "displayName": "x - Store Analytics Checkout---PnP",
        "subdomain": "<subdomain>",
        "host": "<host>"
    }
}
```

## <a name="known-limitations"></a>已知限制

目前並無法以程式設計方式透過 API 來訂閱/取消訂閱這些 Webhook。

如果您有關于如何改進這項功能的想法，請將您的建議張貼到我們的[User voice 論壇](https://feedback.azure.com/forums/911455-azure-iot-central)。

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何設定和使用 webhook，建議的下一個步驟是探索如何設定[Azure 監視器動作群組](howto-use-action-groups.md)。
