---
title: 塑造 JSON Azure 時間序列深入解析查詢的最佳做法 |Microsoft Docs
description: 瞭解如何藉由塑造 JSON 來改善您的 Azure 時間序列深入解析查詢效率。
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 04/17/2020
ms.custom: seodec18
ms.openlocfilehash: 63a708f80ad18309269e37c354b047c304a260d3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81641301"
---
# <a name="shape-json-to-maximize-query-performance"></a>塑造 JSON 以最大化查詢效能

本文提供如何塑造 JSON 以將 Azure 時間序列深入解析查詢效率最大化的指引。

## <a name="video"></a>影片

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>瞭解塑造 JSON 以符合您的儲存體需求的最佳做法。</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>最佳作法

考慮如何將事件傳送至時間序列深入解析。 亦即，您一律會：

1. 盡可能有效率地透過網路傳送資料。
1. 請確定您的資料是以一種方式儲存，讓您可以執行適用于您案例的匯總。
1. 請確定您未達到時間序列深入解析的最大屬性限制：
   - S1 環境為 600 個屬性 (資料行)。
   - S2 環境為 800 個屬性 (資料行)。

> [!TIP]
> 查看 Azure 時間序列深入解析 Preview 中的[限制和計畫](time-series-insights-update-plan.md)。

下列指導方針有助於確保最佳的查詢效能：

1. 請勿使用動態屬性（例如標記識別項）做為屬性名稱。 這會使用來達到最大屬性限制。
1. 請勿傳送不必要的屬性。 如果查詢屬性不是必要的，最好不要傳送它。 如此一來，您就可以避免儲存限制。
1. 使用[參考資料](time-series-insights-add-reference-data-set.md)，以避免透過網路傳送靜態資料。
1. 在多個事件之間共用維度屬性，以更有效率地透過網路傳送資料。
1. 請勿使用深層陣列巢狀結構。 時間序列深入解析支援最多兩個包含物件的嵌套陣列層級。 時間序列深入解析將訊息中的陣列壓平合併為具有屬性值組的多個事件。
1. 如果所有或大部分的事件只存在少數的量值，最好將這些量值當作相同物件內的個別屬性來傳送。 將它們分開傳送可減少事件數目，而且可能會改善查詢效能，因為需要處理的事件較少。 有數個量值時，將它們當做單一屬性中的值來傳送，會使達到最大屬性限制的可能性降到最低。

## <a name="example-overview"></a>範例總覽

下列兩個範例示範如何傳送事件以反白顯示先前的建議。 在每個範例之後，您可以檢查建議的套用方式。

這些範例是根據多部裝置傳送度量或訊號的案例而來。 測量或信號可以是流動率、引擎石油壓力、溫度和濕度。 在第一個範例中，有幾個度量會跨所有裝置。 第二個範例有許多裝置，而每個裝置都會傳送許多獨特的測量。

## <a name="scenario-one-only-a-few-measurements-exist"></a>案例一：只有幾個度量存在

> [!TIP]
> 我們建議您將每個量測或信號當做個別的屬性或資料行來傳送。

在下列範例中，有一個 Azure IoT 中樞訊息，其中外部陣列包含通用維度值的共用區段。 外部陣列使用參考資料來提高訊息的效率。 參考資料報含每個事件都不會變更的裝置中繼資料，但它會提供有用的資料分析屬性。 批次處理一般維度值和採用參考資料會節省透過網路傳送的位元組，讓訊息更有效率。

請考慮將下列 JSON 承載傳送至您的時間序列深入解析 GA 環境，其使用在傳送至 Azure 雲端時序列化為 JSON 的[IoT 裝置訊息物件](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.message?view=azure-dotnet)：


```JSON
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "Flow Rate ft3/s": 1.0172575712203979,
                "Engine Oil Pressure psi ": 34.7
            },
            {
                "Flow Rate ft3/s": 2.445906400680542,
                "Engine Oil Pressure psi ": 49.2
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "Flow Rate ft3/s": 0.58015072345733643,
                "Engine Oil Pressure psi ": 22.2
            }
        ]
    }
]
```

* 具有 key 屬性**deviceId**的參考資料表：

   | deviceId | messageId | deviceLocation |
   | --- | --- | --- |
   | FXXX | LINE\_DATA | EU |
   | FYYY | LINE\_DATA | 美國 |

* 在簡維之後時間序列深入解析事件資料表：

   | deviceId | messageId | deviceLocation | timestamp | series.Flow Rate ft3/s | series.Engine Oil Pressure psi |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
   | FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
   | FYYY | LINE\_DATA | 美國 | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

> [!NOTE]
> - **deviceId** 資料行是車隊中各種裝置的資料行標頭。 將**deviceId**值設為其本身的屬性名稱，會將裝置的總計限制為595（適用于 S1 環境）或795（適用于 S2 環境），以及其他五個數據行。
> - 避免不必要的屬性（例如，make 和 model 資訊）。 因為未來不會查詢這些屬性，所以排除它們可提高網路和儲存體的效率。
> - 使用參考資料來減少透過網路傳輸的位元組數目。 使用索引鍵屬性**deviceId**聯結兩個屬性**messageId**和**deviceLocation** 。 這項資料會在輸入時與遙測資料聯結，然後儲存在時間序列深入解析以進行查詢。
> - 會使用兩層的嵌套，這是時間序列深入解析所支援的最大嵌套數量。 請務必避免巢狀結構很深的陣列。
> - 量值會當做相同物件內的個別屬性來傳送，因為有幾個量值。 在本例中，**series.Flow Rate psi** 和 **series.Engine Oil Pressure ft3/s** 是唯一的資料行。

## <a name="scenario-two-several-measures-exist"></a>案例二：有數個量值存在

> [!TIP]
> 我們建議您將度量當做「類型」、「單位」和「值」元組來傳送。

範例 JSON 承載：

```JSON
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "tagId": "pumpRate",
                "value": 1.0172575712203979
            },
            {
                "tagId": "oilPressure",
                "value": 49.2
            },
            {
                "tagId": "pumpRate",
                "value": 2.445906400680542
            },
            {
                "tagId": "oilPressure",
                "value": 34.7
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "tagId": "pumpRate",
                "value": 0.58015072345733643
            },
            {
                "tagId": "oilPressure",
                "value": 22.2
            }
        ]
    }
]
```

* 具有索引鍵屬性**deviceId**和**tagId**的參考資料表：

   | deviceId | series.tagId | messageId | deviceLocation | 類型 | unit |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINE\_DATA | EU | 流動率 | ft3/s |
   | FXXX | oilPressure | LINE\_DATA | EU | 引擎機油壓力 | psi |
   | FYYY | pumpRate | LINE\_DATA | 美國 | 流動率 | ft3/s |
   | FYYY | oilPressure | LINE\_DATA | 美國 | 引擎機油壓力 | psi |

* 在簡維之後時間序列深入解析事件資料表：

   | deviceId | series.tagId | messageId | deviceLocation | 類型 | unit | timestamp | series.value |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINE\_DATA | EU | 流動率 | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 | 
   | FXXX | oilPressure | LINE\_DATA | EU | 引擎機油壓力 | psi | 2018-01-17T01:17:00Z | 34.7 |
   | FXXX | pumpRate | LINE\_DATA | EU | 流動率 | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 | 
   | FXXX | oilPressure | LINE\_DATA | EU | 引擎機油壓力 | psi | 2018-01-17T01:17:00Z | 49.2 |
   | FYYY | pumpRate | LINE\_DATA | 美國 | 流動率 | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | FYYY | oilPressure | LINE\_DATA | 美國 | 引擎機油壓力 | psi | 2018-01-17T01:18:00Z | 22.2 |

> [!NOTE]
> - 資料行**deviceId**和**tagId**可做為車隊中各種裝置和標記的欄標題。 使用每個做為其本身的屬性，會將查詢限制為594（適用于 S1 環境）或794（適用于 S2 環境）與其他六個數據行的裝置總數。
> - 基於第一個範例中提及的原因，已避免不必要的屬性。
> - 參考資料是用來減少透過網路傳輸的位元組數，方法是引進**deviceId**，這會用於唯一的**messageId**和**deviceLocation**。 複合索引鍵**序列. tagId**是用於**類型**和**單位**的唯一配對。 複合索引鍵可讓**deviceId**和**tagId 組**用來參考四個值： **messageId、deviceLocation、type**和**unit**。 此資料會在輸入時與遙測資料聯結。 然後，它會儲存在時間序列深入解析以進行查詢。
> - 針對第一個範例中所述的原因，會使用兩層的嵌套。

### <a name="for-both-scenarios"></a>針對這兩個案例

對於具有大量可能值的屬性，最好以單一資料行中的相異值來傳送，而不是針對每個值建立新的資料行。 在前兩個範例中：

  - 在第一個範例中，有幾個屬性有數個值，因此最好讓每一個屬性都是不同的。
  - 在第二個範例中，不會將量值指定為個別屬性。 相反地，它們是在通用數列屬性下的值或量值陣列。 會傳送新的索引鍵**tagId** ，這會在簡維資料表中建立新的**資料行數列 tagId** 。 新的屬性**類型**和**單位**是使用參考資料所建立，因此不會達到屬性限制。

## <a name="next-steps"></a>後續步驟

- 深入瞭解將[IoT 中樞裝置訊息傳送至雲端](../iot-hub/iot-hub-devguide-messages-construct.md)。

- 若要深入瞭解時間序列深入解析資料存取 REST API 的查詢語法，請參閱[Azure 時間序列深入解析查詢語法](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-syntax)。

- 瞭解[如何塑造事件](./time-series-insights-send-events.md)。
