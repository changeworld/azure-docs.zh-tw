---
title: 塑造 JSON Azure 時間序列深入解析查詢的最佳作法 |Microsoft Docs
description: 瞭解如何藉由成形 JSON 來改善您的 Azure 時間序列深入解析查詢效率。
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.service: time-series-insights
ms.topic: article
ms.date: 10/01/2020
ms.custom: seodec18
ms.openlocfilehash: b5723433cca20c934d4cb5b129d77c1c6d65feef
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91617106"
---
# <a name="shape-json-to-maximize-query-performance-in-your-gen1-environment"></a>塑造 JSON 以將 Gen1 環境中的查詢效能最大化

> [!CAUTION]
> 這是 Gen1 文章。

本文提供如何塑造 JSON 以最大化 Azure 時間序列深入解析查詢效率的指引。

## <a name="video"></a>影片

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>瞭解塑造 JSON 以符合您儲存需求的最佳做法。</br>

> [!VIDEO <https://www.youtube.com/embed/b2BD5hwbg5I>]

## <a name="best-practices"></a>最佳作法

思考如何將事件傳送至 Azure 時間序列深入解析。 亦即，您一律：

1. 盡可能有效率地透過網路傳送資料。
1. 確定您的資料會以某種方式儲存，如此您就可以執行適合您案例的匯總。
1. 請確定您未達到 Azure 時間序列深入解析的最大屬性限制：
   - S1 環境為 600 個屬性 (資料行)。
   - S2 環境為 800 個屬性 (資料行)。

下列指導方針可協助確保最佳的查詢效能：

1. 請勿使用動態屬性（例如標記識別項）作為屬性名稱。 這種使用有助於達到最大屬性限制。
1. 請勿傳送不必要的屬性。 如果查詢屬性不是必要的，最好不要傳送。 如此一來，您就可以避免儲存空間限制。
1. 使用 [參考資料](time-series-insights-add-reference-data-set.md) ，以避免透過網路傳送靜態資料。
1. 在多個事件之間共用維度屬性，以更有效率地透過網路傳送資料。
1. 請勿使用深層陣列巢狀結構。 Azure 時間序列深入解析最多支援兩個包含物件的嵌套陣列層級。 Azure 時間序列深入解析將訊息中的陣列壓平合併為具有屬性值組的多個事件。
1. 如果所有或大部分的事件只存在少數的量值，最好將這些量值當作相同物件內的個別屬性來傳送。 另外傳送它們可減少事件數目，而且可能會改善查詢效能，因為需要處理的事件較少。 當有數個量值時，將它們當作單一屬性中的值來傳送，可將達到最大屬性限制的可能性降至最低。

## <a name="example-overview"></a>範例總覽

下列兩個範例示範如何傳送事件，以醒目提示先前的建議。 在每個範例之後，您可以複習建議的套用方式。

這些範例是根據多部裝置傳送度量或訊號的案例而來。 測量或信號可以是流動率、引擎石油壓力、溫度和濕度。 在第一個範例中，有幾個度量會跨所有裝置。 第二個範例有許多裝置，而每個裝置都會傳送許多獨特的度量。

## <a name="scenario-one-only-a-few-measurements-exist"></a>案例1：只有一些度量存在

> [!TIP]
> 建議您將每個量測或信號當作個別的屬性或資料行來傳送。

在下列範例中，有一個單一 Azure IoT 中樞訊息，其中外部陣列包含通用維度值的共用區段。 外部陣列使用參考資料來提高訊息的效率。 參考資料報含不會隨每個事件變更的裝置中繼資料，但它會提供有用的資料分析屬性。 批次處理一般維度值和採用參考資料，可節省透過網路傳送的位元組，讓訊息更有效率。

在傳送至 Azure 雲端時，請考慮使用已序列化為 JSON 的 [IoT 裝置訊息物件](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.message?view=azure-dotnet) ，將下列 JSON 承載傳送至您的 Azure 時間序列深入解析 GA 環境：

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

- 具有索引鍵屬性 **deviceId**的參考資料表：

   | deviceId | messageId | deviceLocation |
   | --- | --- | --- |
   | FXXX | LINE\_DATA | EU |
   | FYYY | LINE\_DATA | 美國 |

- 在簡維之後 Azure 時間序列深入解析事件資料表：

   | deviceId | messageId | deviceLocation | timestamp | series.Flow Rate ft3/s | series.Engine Oil Pressure psi |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
   | FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
   | FYYY | LINE\_DATA | 美國 | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

> [!NOTE]

> - **deviceId** 資料行是車隊中各種裝置的資料行標頭。 將 **deviceId** 值設為它自己的屬性名稱，會將 S1 環境的裝置總數限制為 595 () 或 (S2 環境的795，) 其他五個數據行。
> - 您可以避免不必要的屬性 (例如，make 和 model 資訊) 。 由於未來將不會查詢屬性，因此將其排除會讓網路和儲存效率更佳。
> - 使用參考資料來減少透過網路傳輸的位元組數目。 這兩個屬性 **messageId** 和 **deviceLocation** 是使用索引鍵屬性 **deviceId**聯結。 這項資料會在輸入時與遙測資料聯結，然後儲存在 Azure 時間序列深入解析以供查詢。
> - 使用兩層的嵌套，也就是 Azure 時間序列深入解析所支援的最大嵌套數量。 請務必避免巢狀結構很深的陣列。
> - 量值會作為相同物件內的個別屬性來傳送，因為有幾個量值。 在本例中，**series.Flow Rate psi** 和 **series.Engine Oil Pressure ft3/s** 是唯一的資料行。

## <a name="scenario-two-several-measures-exist"></a>案例2：有數個量值存在

> [!TIP]
> 建議您以「類型」、「單位」和「值」元組的形式傳送度量。

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

- 具有索引鍵屬性 **deviceId** 和 series 的參考資料表 **tagId**：

   | deviceId | series.tagId | messageId | deviceLocation | 類型 | unit |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINE\_DATA | EU | 流動率 | ft3/s |
   | FXXX | oilPressure | LINE\_DATA | EU | 引擎機油壓力 | psi |
   | FYYY | pumpRate | LINE\_DATA | 美國 | 流動率 | ft3/s |
   | FYYY | oilPressure | LINE\_DATA | 美國 | 引擎機油壓力 | psi |

- 在簡維之後 Azure 時間序列深入解析事件資料表：

   | deviceId | series.tagId | messageId | deviceLocation | 類型 | unit | timestamp | series.value |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINE\_DATA | EU | 流動率 | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 |
   | FXXX | oilPressure | LINE\_DATA | EU | 引擎機油壓力 | psi | 2018-01-17T01:17:00Z | 34.7 |
   | FXXX | pumpRate | LINE\_DATA | EU | 流動率 | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 |
   | FXXX | oilPressure | LINE\_DATA | EU | 引擎機油壓力 | psi | 2018-01-17T01:17:00Z | 49.2 |
   | FYYY | pumpRate | LINE\_DATA | 美國 | 流動率 | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | FYYY | oilPressure | LINE\_DATA | 美國 | 引擎機油壓力 | psi | 2018-01-17T01:18:00Z | 22.2 |

> [!NOTE]

> - TagId 的資料 **行可** 作為車隊中各種裝置和標記的 **資料** 行標頭。 使用每個做為其本身的屬性會將 S1 環境的查詢限制為 594 () 或 S2 環境的 794 (，) 與其他六個數據行的裝置總計。
> - 基於第一個範例中所述的原因，可避免不必要的屬性。
> - 參考資料是用來減少透過網路傳輸的位元組數，方法是引進 **deviceId**，此值會用於唯一對 **messageId** 和 **deviceLocation**的配對。 複合索引鍵 **系列. tagId** 用於 **類型** 和 **單位**的唯一配對。 複合索引鍵允許使用  **deviceId** 和 **tagId 組** 來參考四個值： **messageId、deviceLocation、type** 和 **unit**。 這項資料會在輸入時與遙測資料聯結。 然後，它會儲存在 Azure 時間序列深入解析以供查詢。
> - 基於第一個範例中所述的原因，會使用兩層的嵌套。

### <a name="for-both-scenarios"></a>針對這兩個案例

對於具有大量可能值的屬性，最好在單一資料行內以相異值形式傳送，而不是為每個值建立新的資料行。 在前兩個範例中：

- 在第一個範例中，有幾個屬性有數個值，因此適合讓每個屬性都是個別的屬性。
- 在第二個範例中，不會將量值指定為個別的屬性。 相反地，它們是通用數列屬性下值或量值的陣列。 新的索引鍵 **tagId** 會傳送，這會在簡維資料表中建立新的 **資料行 tagId** 。 新的屬性 **類型** 和 **單位** 是使用參考資料所建立，因此不會達到屬性限制。

## <a name="next-steps"></a>後續步驟

- 深入瞭解如何 [將 IoT 中樞裝置訊息傳送至雲端](../iot-hub/iot-hub-devguide-messages-construct.md)。

- 若要深入瞭解 Azure 時間序列深入解析資料存取 REST API 的查詢語法，請參閱 [Azure 時間序列深入解析查詢語法](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-syntax) 。

- 瞭解 [如何塑造事件](./time-series-insights-send-events.md)。
