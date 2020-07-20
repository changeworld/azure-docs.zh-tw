---
title: 事件架構-Azure Event Grid IoT Edge |Microsoft Docs
description: IoT Edge 上事件方格中的事件架構。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: ea36c40f2038d016afb0c45944a98d4d90df6240
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171562"
---
# <a name="event-schemas"></a>事件結構描述

事件方格模組會接受並傳遞 JSON 格式的事件。 事件方格目前支援三種架構：

* **EventGridSchema**
* **CustomSchema**
* **CloudEventSchema**

您可以設定在主題建立期間，發行者必須符合的架構。 如果未指定，則預設為**EventGridSchema**。 不符合預期架構的事件將會遭到拒絕。

訂閱者也可以設定要傳遞事件的架構。 如果未指定，預設值為主題的架構。
目前的訂閱者傳遞架構必須符合其主題的輸入架構。 

## <a name="eventgrid-schema"></a>EventGrid 架構

EventGrid 架構是由發佈實體必須符合的一組必要屬性所組成。 每個發行者都必須填入最上層欄位。

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

### <a name="eventgrid-schema-properties"></a>EventGrid 架構屬性

所有事件都具有下列最上層資料：

| 屬性 | 類型 | 必要 | 描述 |
| -------- | ---- | ----------- |-----------
| 主題 | 字串 | No | 應符合其發行的主題。 如果未指定，事件方格會將其發佈所在的主題名稱填入其中。 |
| subject | 字串 | 是 | 發行者定義事件主旨的路徑。 |
| eventType | 字串 | 是 | 此事件來源的事件種類，例如 Microsoft.storage.blobcreated。 |
| eventTime | 字串 | 是 | 事件產生的時間，以提供者之 UTC 時間為準。 |
| 識別碼 | 字串 | No | 事件的唯一識別碼。 |
| data | 物件 (object) | 否 | 用來捕獲發佈實體特定的事件資料。 |
| dataVersion | 字串 | 是 | 資料物件的結構描述版本。 發行者會定義結構描述版本。 |
| metadataVersion | 字串 | No | 事件中繼資料的結構描述版本。 「事件方格」會定義最上層屬性的結構描述。 「事件方格」提供此值。 |

### <a name="example--eventgrid-schema-event"></a>範例-EventGrid 架構事件

```json
[
  {
    "id": "1807",
    "eventType": "recordInserted",
    "subject": "myapp/vehicles/motorcycles",
    "eventTime": "2017-08-10T21:03:07+00:00",
    "data": {
      "make": "Ducati",
      "model": "Monster"
    },
    "dataVersion": "1.0"
  }
]
```

## <a name="customevent-schema"></a>CustomEvent 架構

在自訂架構中，不會強制執行任何強制屬性，如 EventGrid 架構。 發行實體可以完全控制事件架構。 它提供最大的彈性，並可讓您擁有已有事件型系統的情況，而且想要重複使用現有的事件及/或不想要系結至特定的架構。

### <a name="custom-schema-properties"></a>自訂架構屬性

沒有必要的屬性。 這取決於發佈實體來判斷裝載。

### <a name="example--custom-schema-event"></a>範例-自訂架構事件

```json
[
  {
    "eventdata": {
      "make": "Ducati",
      "model": "Monster"
    }
  }
]
```

## <a name="cloudevent-schema"></a>CloudEvent 結構描述

除了上述架構，事件方格原本就支援[CLOUDEVENTS JSON 架構](https://github.com/cloudevents/spec/blob/master/json-format.md)中的事件。 CloudEvents 是用來說明事件資料的開放式規格。 它藉由提供用於發佈和取用事件的一般事件架構，來簡化互通性。 這是[由 cncf](https://www.cncf.io/)的一部分，且目前可用的版本為 1.0-rc1。

### <a name="cloudevent-schema-properties"></a>CloudEvent 架構屬性

請參閱必要信封屬性上的[CloudEvents 規格](https://github.com/cloudevents/spec/blob/master/json-format.md#3-envelope)。

### <a name="example--cloud-event"></a>範例-雲端事件
```json
[{
       "id": "1807",
       "type": "recordInserted",
       "source": "myapp/vehicles/motorcycles",
       "time": "2017-08-10T21:03:07+00:00",
       "datacontenttype": "application/json",
       "data": {
            "make": "Ducati",
            "model": "Monster"
        },
        "dataVersion": "1.0",
        "specVersion": "1.0-rc1"
}]
```
