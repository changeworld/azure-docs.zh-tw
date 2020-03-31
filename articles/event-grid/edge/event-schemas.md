---
title: 事件架構 = Azure 事件網格 IoT 邊緣 |微軟文檔
description: IoT 邊緣事件網格中的事件架構。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ba261aeedf6574f69d3c05f8fd005c912dcc59d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73242458"
---
# <a name="event-schemas"></a>事件結構描述

事件網格模組以 JSON 格式接受並提供事件。 事件網格當前支援三個架構：

* **事件網格架構**
* **自訂架構**
* **雲事件架構**

您可以配置發行者在主題創建期間必須滿足的架構。 如果未指定，則預設為**事件網格架構**。 不符合預期架構的事件將被拒絕。

訂閱者還可以配置他們希望傳遞事件的架構。 如果未指定，則預設為主題的架構。
當前，訂閱者交付架構必須與其主題的輸入架構匹配。 

## <a name="eventgrid-schema"></a>事件網格架構

EventGrid 架構由一組發佈實體必須遵循的必需屬性組成。 每個發行者必須填充頂級欄位。

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

### <a name="eventgrid-schema-properties"></a>事件網格架構屬性

所有事件都有以下頂級資料：

| 屬性 | 類型 | 必要 | 描述 |
| -------- | ---- | ----------- |-----------
| 主題 | 字串 | 否 | 應與發佈它的主題匹配。 事件網格使用發佈主題（如果未指定）的名稱填充它。 |
| subject | 字串 | 是 | 發行者定義事件主體的路徑。 |
| eventType | 字串 | 是 | 此事件源的事件種類，例如 Blob 創建。 |
| eventTime | 字串 | 是 | 事件產生的時間，以提供者之 UTC 時間為準。 |
| ID | 字串 | 否 | 事件的唯一識別碼。 |
| data | 物件 (object) | 否 | 用於捕獲特定于發佈實體的事件資料。 |
| dataVersion | 字串 | 是 | 資料物件的結構描述版本。 發行者會定義結構描述版本。 |
| metadataVersion | 字串 | 否 | 事件中繼資料的結構描述版本。 Event Grid 會定義最上層屬性的結構描述。 Event Grid 提供此值。 |

### <a name="example--eventgrid-schema-event"></a>示例 = 事件網格架構事件

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

## <a name="customevent-schema"></a>自訂事件架構

在自訂架構中，沒有強制屬性像 EventGrid 架構那樣強制執行。 發佈實體可以完全控制事件架構。 它提供了最大的靈活性，並啟用了已建立基於事件的系統並希望重用現有事件和/或不希望綁定到特定架構的方案。

### <a name="custom-schema-properties"></a>自訂架構屬性

沒有強制屬性。 由發佈實體確定有效負載。

### <a name="example--custom-schema-event"></a>示例 = 自訂架構事件

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

除了上述架構外，事件網格本機支援[雲事件 JSON 架構](https://github.com/cloudevents/spec/blob/master/json-format.md)中的事件。 CloudEvents 是用來說明事件資料的開放式規格。 它通過提供用於發佈和使用事件的通用事件架構來簡化互通性。 它是[CNCF](https://www.cncf.io/)的一部分，當前可用的版本為 1.0-rc1。

### <a name="cloudevent-schema-properties"></a>雲事件架構屬性

請參閱強制信封屬性上的[雲事件規範](https://github.com/cloudevents/spec/blob/master/json-format.md#3-envelope)。

### <a name="example--cloud-event"></a>示例 = 雲事件
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
