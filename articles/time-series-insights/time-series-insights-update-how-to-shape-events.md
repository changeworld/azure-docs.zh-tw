---
title: 圖形事件-Azure 時間序列深入解析 |Microsoft Docs
description: 瞭解最佳做法，以及如何在 Azure 時間深入解析預覽版中塑造事件以進行查詢。
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/24/2020
ms.custom: seodec18
ms.openlocfilehash: fd2c58b07f3be5d5fa6d99d0c8c64906b81e7de4
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86036979"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>使用 Azure 時間序列深入解析為事件塑形

本文定義最佳做法來塑造您的 JSON 承載，以便在 Azure 時間序列深入解析中進行內嵌，並將預覽查詢的效率最大化。

## <a name="best-practices"></a>最佳作法

最好仔細考慮如何將事件傳送至您的時間序列深入解析預覽環境。 

一般最佳作法包括：

* 盡可能有效率地透過網路傳送資料。
* 以可協助您針對您的案例更適當地彙總資料的方式存放資料。

為了獲得最佳查詢效能，請遵守下列經驗法則：

* 請勿傳送不必要的屬性。 時間序列深入解析依使用量預覽帳單。 最好只儲存和處理您要查詢的資料。
* 為統計資料使用執行個體欄位。 這種作法有助於避免透過網路傳送靜態資料。 實例欄位是時間序列模型的元件，其運作方式就像是時間序列深入解析服務中正式推出的參考資料。 若要深入瞭解實例欄位，請參閱[時間序列模型](./concepts-model-overview.md)。
* 在兩個或更多事件之間共用維度屬性。 此實務可協助您更有效率地透過網路傳送資料。
* 請勿使用深層陣列巢狀結構。 時間序列深入解析 Preview 最多支援兩個包含物件的嵌套陣列層級。 時間序列深入解析會將訊息中的陣列壓平合併為具有屬性值組的多個事件。
* 如果所有或大部分的事件只存在少數的量值，最好將這些量值當作相同物件內的個別屬性來傳送。 將它們分開傳送可減少事件數目，而且可能會改善查詢效能，因為需要處理的事件較少。

## <a name="column-flattening"></a>資料行簡維

在內嵌期間，包含嵌套物件的承載將會壓平合併，讓資料行名稱是具有 delineator 的單一值。

* 例如，下列的嵌套 JSON：

   ```JSON
   "data": {
        "flow": 1.0172575712203979,
   },
   ```

   會變成： `data_flow` 壓平合併時。

> [!IMPORTANT]
> * Azure 時間序列深入解析 Preview 會針對資料 `_` 行略圖使用底線（）。
> * 請注意，與使用句號（）相反的公開上市差異 `.` 。

更複雜的案例如下所示。

#### <a name="example-1"></a>範例 1：

下列案例有兩個（或更多）裝置，可傳送測量（信號）：*流動率*、*引擎石油壓力*、*溫度*和*濕度*。

有一個傳送的單一 Azure IoT 中樞訊息，其中外部陣列包含通用維度值的共用區段（請注意訊息中包含的兩個裝置專案）。

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

**優點**

* 範例 JSON 具有外部陣列，其使用[時間序列實例](./concepts-model-overview.md#time-series-model-instances)資料來提高訊息的效率。 即使時間序列實例裝置中繼資料不太可能變更，它通常會提供有用的資料分析屬性。

* JSON 會將兩個或多個訊息（每個裝置一個）合併成一段時間後的頻寬儲存。

* 每個裝置的個別數列資料點會合並成單一**數列**屬性，以減少持續串流每個裝置更新的需求。

> [!TIP]
> 若要減少傳送資料和讓遙測更有效率所需的訊息數目，請考慮將通用維度值和時間序列實例中繼資料批次處理成單一 JSON 承載。

#### <a name="time-series-instance"></a>時間序列執行個體 

讓我們進一步瞭解如何使用[時間序列實例](./concepts-model-overview.md#time-series-model-instances)，以更理想的方式塑造您的 JSON。 

> [!NOTE]
> 以下的[時間序列識別碼](./time-series-insights-update-how-to-id.md)為*deviceIds*。

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

| deviceId  | 類型 | L1 | L2 | timestamp | series_Flow 速率 rate ft3/秒 | series_Engine 石油壓力 psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SIMULATOR | Battery System | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34.7 |
| `FXXX` | Default_Type | SIMULATOR |   Battery System |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | LINE_DATA    COMMON | SIMULATOR |    Battery System |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

> [!NOTE]
>  上表代表[時間序列預覽瀏覽器](./time-series-insights-update-explorer.md)中的查詢檢視。

**優點**

* 在上述範例中，靜態屬性會儲存在時間序列深入解析預覽中，以優化透過網路傳送的資料。
* 時間序列深入解析預覽資料會透過實例中所定義的時間序列識別碼，在查詢時聯結。
* 會使用兩層的嵌套。 此數位是時間序列深入解析 Preview 支援的最多。 請務必避免巢狀結構很深的陣列。
* 因為有一些量值，它們會在相同的物件中以不同的屬性傳送。 在範例中， **Series_Flow 速率 psi**， **series_Engine 石油壓力 psi**，而**series_Flow 速率 rate ft3/s**是唯一的資料行。

>[!IMPORTANT]
> 實例欄位不會與遙測一起儲存。 它們會與中繼資料一起儲存在時間序列模型中。

#### <a name="example-2"></a>範例 2：

請考慮下列 JSON：

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

在上述範例中，簡維 `data["flow"]` 屬性會顯示與屬性的命名衝突 `data_flow` 。

在此情況下，*最新*的屬性值會覆寫較舊的內容。 

> [!TIP]
> 如需更多協助，請洽詢時間序列深入解析小組！

> [!WARNING] 
> * 如果因為簡維或另一個機制而在相同（單數）事件裝載中出現重複屬性，則會儲存最新的 > 屬性值，而不會覆寫任何先前的值。
> * 合併事件的數列不會彼此覆寫。

## <a name="next-steps"></a>後續步驟

* 若要將這些指導方針納入實務，請閱讀[Azure 時間序列深入解析預覽查詢語法](./time-series-insights-query-data-csharp.md)。 您將深入瞭解適用于資料存取時間序列深入解析[預覽 REST API](https://docs.microsoft.com/rest/api/time-series-insights/preview)的查詢語法。

* 結合 JSON 最佳做法與[如何使用時間序列模型](./time-series-insights-update-how-to-tsm.md)。
