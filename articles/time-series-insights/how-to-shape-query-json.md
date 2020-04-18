---
title: 用於塑造 JSON 的最佳實踐 - Azure 時間序列見解查詢 |微軟文件
description: 瞭解如何通過塑造 JSON 提高 Azure 時間序列見解查詢效率。
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 04/17/2020
ms.custom: seodec18
ms.openlocfilehash: 63a708f80ad18309269e37c354b047c304a260d3
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641301"
---
# <a name="shape-json-to-maximize-query-performance"></a>元件 JSON 以最大限度地提高查詢性能

本文提供有關如何塑造 JSON 以最大限度地提高 Azure 時間序列見解查詢效率的指導。

## <a name="video"></a>影片

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>瞭解塑造 JSON 以滿足存儲需求的最佳做法。</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>最佳作法

考慮如何將事件發送到時間序列見解。 也就是說,你總是:

1. 盡可能有效率地透過網路傳送資料。
1. 確保以一種方式存儲數據,以便執行適合您的方案的聚合。
1. 確保您沒有達到時序見解的最大屬性限制:
   - S1 環境為 600 個屬性 (資料行)。
   - S2 環境為 800 個屬性 (資料行)。

> [!TIP]
> 在 Azure 時間序列的預覽中檢視[限制並規劃](time-series-insights-update-plan.md)。

以下指南有助於確保最佳的查詢效能:

1. 不要使用動態屬性(如標記 ID)作為屬性名稱。 此使用有助於達到最大屬性限制。
1. 請勿傳送不必要的屬性。 如果不需要查詢屬性,最好不要發送它。 這樣,可以避免存儲限制。
1. 使用[參考數據](time-series-insights-add-reference-data-set.md)避免通過網路發送靜態數據。
1. 在多個事件之間共用維度屬性,以便更有效地通過網路發送數據。
1. 請勿使用深層陣列巢狀結構。 時間序列見解最多支援包含物件的嵌套陣列的兩個級別。 時間序列見解將消息中的陣列到具有屬性值對的多個事件中。
1. 如果所有或大部分的事件只存在少數的量值，最好將這些量值當作相同物件內的個別屬性來傳送。 單獨發送它們會減少事件數,並可能提高查詢性能,因為需要處理的事件更少。 當有多個度量值時,將它們作為值發送到單個屬性中會最大程度地降低達到最大屬性限制的可能性。

## <a name="example-overview"></a>範例概述

以下兩個示例演示如何發送事件以突出顯示前面的建議。 在每個範例之後,您可以查看建議的應用方式。

這些範例是根據多部裝置傳送度量或訊號的案例而來。 測量或信號可以是流速、發動機機油壓力、溫度和濕度。 在第一個範例中，有幾個度量會跨所有裝置。 第二個範例具有許多設備,每個設備都發送許多唯一的測量值。

## <a name="scenario-one-only-a-few-measurements-exist"></a>方案一:僅存在幾個度量值

> [!TIP]
> 我們建議您將每個測量或信號作為單獨的屬性或列發送。

在下面的示例中,有一個 Azure IoT 中心消息,其中外部陣列包含公共維度值的共用部分。 外部陣列使用參考資料來提高訊息的效率。 參考數據包含的設備元數據不會隨每個事件發生更改,但它為數據分析提供了有用的屬性。 對通用維度值進行批處理並採用參考數據,可節省通過導線發送的位元組,從而提高消息的效率。

請考慮使用[IT 裝置訊息物件](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.message?view=azure-dotnet)傳送到時序到 Azure 雲時傳送到時序的時序見解 GA 環境的以下 JSON 有效負載:


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

* 具有金鑰屬性**裝置 Id**的參考資料表 :

   | deviceId | messageId | deviceLocation |
   | --- | --- | --- |
   | FXXX | LINE\_DATA | EU |
   | FYYY | LINE\_DATA | US |

* 拼合後的時間序列的解事件表:

   | deviceId | messageId | deviceLocation | timestamp | series.Flow Rate ft3/s | series.Engine Oil Pressure psi |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
   | FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
   | FYYY | LINE\_DATA | US | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

> [!NOTE]
> - **deviceId** 資料行是車隊中各種裝置的資料行標頭。 使**deviceId**值具有自己的屬性名稱,將設備總數限制為 595(對於 S1 環境)或 795(對於 S2 環境),與其他五列一起。
> - 避免了不必要的屬性(例如,make 和模型資訊)。 由於將來不會查詢這些屬性,因此消除這些屬性可實現更好的網路和存儲效率。
> - 使用參考資料來減少透過網路傳輸的位元組數目。 使用金鑰屬性**裝置 Id**加入這兩個屬性 訊息**Id** **和裝置位置**。 此數據在入口時間與遙測數據聯接,然後存儲在時間序列見解中以進行查詢。
> - 使用兩層嵌套,這是時間序列見解支援的最大嵌套量。 請務必避免巢狀結構很深的陣列。
> - 度量值作為單獨的屬性發送到同一物件中,因為很少有度量值。 在本例中，**series.Flow Rate psi** 和 **series.Engine Oil Pressure ft3/s** 是唯一的資料行。

## <a name="scenario-two-several-measures-exist"></a>方案二:存在幾種度量值

> [!TIP]
> 我們建議您將度量作為"類型"、"單位"和"值"組發送。

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

* 具有關鍵屬性**裝置 Id**和系列的參考資料表 **。**

   | deviceId | series.tagId | messageId | deviceLocation | type | unit |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINE\_DATA | EU | 流動率 | ft3/s |
   | FXXX | oilPressure | LINE\_DATA | EU | 引擎機油壓力 | psi |
   | FYYY | pumpRate | LINE\_DATA | US | 流動率 | ft3/s |
   | FYYY | oilPressure | LINE\_DATA | US | 引擎機油壓力 | psi |

* 拼合後的時間序列的解事件表:

   | deviceId | series.tagId | messageId | deviceLocation | type | unit | timestamp | series.value |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINE\_DATA | EU | 流動率 | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 | 
   | FXXX | oilPressure | LINE\_DATA | EU | 引擎機油壓力 | psi | 2018-01-17T01:17:00Z | 34.7 |
   | FXXX | pumpRate | LINE\_DATA | EU | 流動率 | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 | 
   | FXXX | oilPressure | LINE\_DATA | EU | 引擎機油壓力 | psi | 2018-01-17T01:17:00Z | 49.2 |
   | FYYY | pumpRate | LINE\_DATA | US | 流動率 | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | FYYY | oilPressure | LINE\_DATA | US | 引擎機油壓力 | psi | 2018-01-17T01:18:00Z | 22.2 |

> [!NOTE]
> - 列**裝置 Id**和**series.tagId**充當佇列中各種設備和標記的列標題。 使用每個作為自己的屬性將查詢限制為 594(對於 S1 環境)或 794(對於 S2 環境)的總設備與其他六列。
> - 由於第一個示例中引用的原因,避免了不必要的屬性。
> - 引用數據用於透過引入**設備Id**來減少透過網路傳輸的位元組數,該裝置Id用於唯一的消息**Id**和對**設備位置**的對。 複合鍵**系列.tagId**用於**唯一的類型**和**單位**對。 複合鍵允許裝置**Id**與**series.tagId**對用於參考四個值:**訊息 Id、裝置位置、類型**和**單位**。 此數據在入口時間與遙測數據聯接。 然後,它存儲在時間序列見解中以進行查詢。
> - 由於第一個示例中引用的原因,使用了兩層嵌套。

### <a name="for-both-scenarios"></a>針對這兩個案例

對於具有大量可能值的屬性,最好在單個列中作為不同的值發送,而不是為每個值創建新列。 在前兩個範例中：

  - 在第一個示例中,幾個屬性具有多個值,因此使每個屬性都適合。
  - 在第二個示例中,度量值未指定為單個屬性。 相反,它們是公共系列屬性下的值或度量值陣組。 將傳送新的鍵**tagId,** 這將在拼合表中建立新的欄**系列。** 使用引用資料創建新的屬性**類型**和**單位**,以便不達到屬性限制。

## <a name="next-steps"></a>後續步驟

- 閱讀有關向雲發送[IoT 中心設備消息](../iot-hub/iot-hub-devguide-messages-construct.md)的資訊。

- 閱讀[Azure 時間序列見解查詢語法](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-syntax),瞭解有關時序見解數據訪問 REST API 的查詢語法的更多資訊。

- [瞭解如何塑造事件](./time-series-insights-send-events.md)。
