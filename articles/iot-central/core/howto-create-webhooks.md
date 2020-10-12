---
title: 在 Azure IoT Central 中建立規則的 Webhook | Microsoft Docs
description: 在 Azure IoT Central 中建立 Webhook，以在規則引發時自動通知其他應用程式。
author: viv-liu
ms.author: viviali
ms.date: 04/03/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: b2ac4bbf1457144d23a91c4e83b554b3ee806119
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87337223"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>在 Azure IoT Central 中建立規則的 Webhook 動作

*本主題適用於建置員和系統管理員。*

Webhook 可讓您將 IoT Central 應用程式連線至其他應用程式和服務，以進行遠端監視和通知。 Webhook 會在 IoT Central 應用程式中觸發規則時，自動通知您所連線的其他應用程式和服務。 每當觸發規則時，您的 IoT Central 應用程式就會將 POST 要求傳送至其他應用程式的 HTTP 端點。 承載包含裝置詳細資料和規則引發程式詳細資料。

## <a name="set-up-the-webhook"></a>設定 webhook

在此範例中，您會連接到 RequestBin，以在使用 webhook 引發規則時收到通知。

1. 開啟 [RequestBin](https://requestbin.net/)。

1. 建立新的 RequestBin，並複製 **Bin URL**。

1. 建立 [遙測規則](tutorial-create-telemetry-rules.md)。 儲存規則，然後新增動作。

    ![Webhook 建立畫面](media/howto-create-webhooks/webhookcreate.png)

1. 選擇 Webhook 動作並提供顯示名稱，然後貼上 Bin URL 作為回呼 URL。

1. 儲存規則。

現在當規則引發時，您會看到新的要求出現在 RequestBin 中。

## <a name="payload"></a>Payload

觸發規則時，會對包含 json 承載的回呼 URL 發出 HTTP POST 要求，其中包含遙測、裝置、規則和應用程式詳細資料。 承載看起來可能如下所示：

```json
{
    "timestamp": "2020-04-06T00:20:15.06Z",
    "action": {
        "id": "<id>",
        "type": "WebhookAction",
        "rules": [
            "<rule_id>"
        ],
        "displayName": "Webhook 1",
        "url": "<callback_url>"
    },
    "application": {
        "id": "<application_id>",
        "displayName": "Contoso",
        "subdomain": "contoso",
        "host": "contoso.azureiotcentral.com"
    },
    "device": {
        "id": "<device_id>",
        "etag": "<etag>",
        "displayName": "MXChip IoT DevKit - 1yl6vvhax6c",
        "instanceOf": "<device_template_id>",
        "simulated": true,
        "provisioned": true,
        "approved": true,
        "cloudProperties": {
            "City": {
                "value": "Seattle"
            }
        },
        "properties": {
            "deviceinfo": {
                "firmwareVersion": {
                    "value": "1.0.0"
                }
            }
        },
        "telemetry": {
            "<interface_instance_name>": {
                "humidity": {
                    "value": 47.33228889360127
                }
            }
        }
    },
    "rule": {
        "id": "<rule_id>",
        "displayName": "Humidity monitor"
    }
}
```
如果規則在一段時間內監視匯總的遙測，承載將會包含不同的遙測區段。

```json
{
    "telemetry": {
        "<interface_instance_name>": {
            "Humidity": {
                "avg": 39.5
            }
        }
    }
}
```

## <a name="data-format-change-notice"></a>資料格式變更通知

如果您有一或多個 webhook 在 **2020 年4月 3**日之前建立並儲存，您將需要刪除 webhook 並建立新的 webhook。 這是因為較舊的 webhook 會使用未來將淘汰的舊版裝載格式。

### <a name="webhook-payload-format-deprecated-as-of-3-april-2020"></a>從2020年4月3日起，Webhook 承載 (格式已淘汰) 

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
        "displayName": "x - Store Analytics Checkout",
        "subdomain": "<subdomain>",
        "host": "<host>"
    }
}
```

## <a name="known-limitations"></a>已知限制

目前並無法以程式設計方式透過 API 來訂閱/取消訂閱這些 Webhook。

如果您有關于如何改進這項功能的構想，請將您的建議張貼到我們的 [使用者語音論壇](https://feedback.azure.com/forums/911455-azure-iot-central)。

## <a name="next-steps"></a>接下來的步驟

現在您已瞭解如何設定和使用 webhook，建議的下一個步驟是探索設定 [Azure 監視器動作群組](howto-use-action-groups.md)。
