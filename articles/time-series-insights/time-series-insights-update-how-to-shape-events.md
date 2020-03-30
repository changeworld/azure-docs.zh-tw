---
title: 形狀事件 - Azure 時間序列見解 |微軟文檔
description: 瞭解最佳實踐以及如何在 Azure 時間見解預覽中塑造要查詢的事件。
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/24/2020
ms.custom: seodec18
ms.openlocfilehash: 99a2f32c3f76d7fec475c9b299f7208b4db29cfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650918"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>使用 Azure 時間序列深入解析為事件塑形

本文定義了用於在 Azure 時間序列見解中引入 JSON 有效負載並最大限度地提高預覽查詢效率的最佳做法。

## <a name="best-practices"></a>最佳作法

最好仔細考慮如何向時序見解預覽環境發送事件。 

一般最佳做法包括：

* 盡可能有效率地透過網路傳送資料。
* 以可協助您針對您的案例更適當地彙總資料的方式存放資料。

為了獲得最佳的查詢性能，請遵循以下經驗法則：

* 請勿傳送不必要的屬性。 時間序列見解 按使用方式預覽帳單。 最好只存儲和處理要查詢的資料。
* 為統計資料使用執行個體欄位。 這種做法有助於避免通過網路發送靜態資料。 實例欄位是時序模型的一個元件，它的工作方式與時間序列見解服務中的參考資料類似，通常可用。 要瞭解有關實例欄位的更多內容，請閱讀[時間序列模型](./time-series-insights-update-tsm.md)。
* 在兩個或更多事件之間共用維度屬性。 此實務可協助您更有效率地透過網路傳送資料。
* 請勿使用深層陣列巢狀結構。 時間序列見解預覽支援最多包含物件的嵌套陣列的兩個級別。 時間序列深入解析會將訊息中的陣列壓平合併為具有屬性值組的多個事件。
* 如果所有或大部分的事件只存在少數的量值，最好將這些量值當作相同物件內的個別屬性來傳送。 單獨發送它們會減少事件數，並可能提高查詢性能，因為需要處理的事件更少。

## <a name="column-flattening"></a>列拼平

在引入期間，包含嵌套物件的有效負載將被拼合，以便列名稱是具有劃線器的單個值。

* 例如，以下嵌套 JSON：

   ```JSON
   "data": {
        "flow": 1.0172575712203979,
   },
   ```

   變為：`data_flow`拼合時。

> [!IMPORTANT]
> * Azure 時間序列見解預覽使用下劃`_`線 （ ） 進行清單示。
> * 請注意與使用期間 （`.`） 的"常規可用性"的區別。

更複雜的場景如下圖所示。

#### <a name="example-1"></a>範例 1：

以下方案有兩個（或更多）設備發送測量（信號）：*流速*、*發動機機油壓力*、*溫度*和*濕度*。

發送單個 Azure IoT 中心消息，其中外部陣列包含公共維度值的共用部分（請注意消息中包含的兩個設備條目）。

```JSON
[
  {
    "deviceId":"FXXX",
    "timestamp":"2018-01-17T01:17:00Z",
    "series":[
      {
        "Flow Rate ft3/s":1.0172575712203979,
        "Engine Oil Pressure psi ":34.7
      },
      {
        "Flow Rate ft3/s":2.445906400680542,
        "Engine Oil Pressure psi ":49.2
      }
    ]
  },
  {
    "deviceId":"FYYY",
    "timestamp":"2018-01-17T01:18:00Z",
    "series":[
      {
        "Flow Rate ft3/s":0.58015072345733643,
        "Engine Oil Pressure psi ":22.2
      }
    ]
  }
]
```

**外賣：**

* 示例 JSON 具有一個外部陣列，該陣列使用[時間序列實例](./time-series-insights-update-tsm.md#time-series-model-instances)資料來提高消息的效率。 即使時間序列實例設備中繼資料不太可能更改，它通常為數據分析提供有用的屬性。

* JSON 將兩個或多個消息（每個設備中的一條消息）合併到單個有效負載中，從而隨著時間的推移節省頻寬。

* 每個設備的單個系列資料點合併到單個**系列**屬性中，減少了對每台設備持續資料流更新的需求。

> [!TIP]
> 為了減少發送資料所需的消息數，提高遙測效率，請考慮將通用維度值和時間序列實例中繼資料批次處理到單個 JSON 負載中。

#### <a name="time-series-instance"></a>時間序列執行個體 

讓我們仔細看看如何使用[時間序列實例](./time-series-insights-update-tsm.md#time-series-model-instances)更優化地塑造 JSON。 

> [!NOTE]
> 下面的[時間序列 ID](./time-series-insights-update-how-to-id.md)是*裝置識別碼*。

```JSON
[
  {
    "timeSeriesId":[
      "FXXX"
    ],
    "typeId":"17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds":[
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description":null,
    "instanceFields":{
      "L1":"REVOLT SIMULATOR",
      "L2":"Battery System"
    }
  },
  {
    "timeSeriesId":[
      "FYYY"
    ],
    "typeId":"17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds":[
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description":null,
    "instanceFields":{
      "L1":"COMMON SIMULATOR",
      "L2":"Battery System"
    }
  }
]
```

時間序列深入解析預覽版會在查詢期間聯結資料表 (在壓平之後)。 該資料表包括額外的資料行，例如**類型**。

| deviceId  | 類型 | L1 | L2 | timestamp | series_Flow價格英尺3/s | series_Engine 油壓 psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SIMULATOR | Battery System | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34.7 |
| `FXXX` | Default_Type | SIMULATOR |   Battery System |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | LINE_DATA    COMMON | SIMULATOR |    Battery System |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

> [!NOTE]
>  前面的表表示[時間序列預覽資源管理器](./time-series-insights-update-explorer.md)中的查詢檢視。

**外賣：**

* 在前面的示例中，靜態屬性存儲在時間序列見解預覽中，以優化通過網路發送的資料。
* 時序見解 預覽資料通過實例中定義的時序 ID 在查詢時聯接。
* 使用兩層嵌套。 此數位是時間序列見解預覽版支援最多的數位。 請務必避免巢狀結構很深的陣列。
* 因為有一些量值，它們會在相同的物件中以不同的屬性傳送。 在此示例中 **，series_Flow速率 psi、series_Engine****油壓 psi**和**series_Flow速率 ft3/s**是唯一的列。

>[!IMPORTANT]
> 實例欄位不隨遙測一起存儲。 它們與時間序列模型中的中繼資料一起存儲。

#### <a name="example-2"></a>範例 2：

請考慮以下 JSON：

```JSON
{
  "deviceId": "FXXX",
  "timestamp": "2019-01-18T01:17:00Z",
  "data": {
        "flow": 1.0172575712203979,
    },
  "data_flow" : 1.76435072345733643
}
```

在上面的示例中，拼合`data["flow"]`屬性將呈現與屬性的`data_flow`命名衝突。

在這種情況下，*最新的*屬性值將覆蓋前面的屬性值。 

> [!TIP]
> 請聯繫時序洞察團隊以獲得更多説明！

> [!WARNING] 
> * 如果由於拼合或其他機制而在同一（單數）事件裝載中存在重複屬性，則存儲最新的>屬性值，從而過度寫入任何以前的值。
> * 一系列合併事件不會相互覆蓋。

## <a name="next-steps"></a>後續步驟

* 要將這些準則付諸實踐，請閱讀[Azure 時間序列見解預覽查詢語法](./time-series-insights-query-data-csharp.md)。 您將瞭解有關時間序列見解[預覽 REST API](https://docs.microsoft.com/rest/api/time-series-insights/preview)用於資料訪問的查詢語法的更多資訊。

* 將 JSON 最佳實踐與["如何時間序列模型"](./time-series-insights-update-how-to-tsm.md)相結合。
