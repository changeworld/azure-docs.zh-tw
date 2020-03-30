---
title: Azure 流分析相容性級別
description: 了解如何為 Azure 串流分析作業設定相容性層級，並了解最新相容性層級中的重大變更
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 8f22b1ff97826dc318794aca58973b1276e74209
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087866"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Azure 串流分析作業的相容性層級

本文介紹 Azure 流分析中的相容性級別選項。 流分析是一個託管服務，具有週期性功能更新和性能改進。 服務的大部分運行時更新會自動提供給最終使用者。 

但是，服務中的一些新功能可能會引入重大更改，例如現有作業行為的更改，或運行作業中資料的使用方式的更改。 通過降低相容性級別設置，可以保持現有流分析作業運行，而無需進行重大更改。 準備好進行最新運行時行為後，可以通過提高相容性級別來加入宣告。 

## <a name="choose-a-compatibility-level"></a>選擇相容性級別

相容性層級可控制串流分析作業的執行階段行為。 

Azure 流分析當前支援三個相容性級別：

* 1.0 - 原始相容性級別，幾年前 Azure 流分析的一般可用性期間引入。
* 1.1 - 以前的行為
* 1.2 - 最新改進的最新行為

創建新的流分析作業時，最好使用最新的相容性級別創建它。 依靠最新行為開始您的工作設計，以避免以後增加的變化和複雜性。

## <a name="set-the-compatibility-level"></a>設定相容性層級

您可以在 Azure 門戶中或使用[創建作業 REST API 呼叫](/rest/api/streamanalytics/stream-analytics-job)設置流分析作業的相容性級別。

要更新 Azure 門戶中的作業的相容性級別，請執行以下操作：

1. 使用[Azure 門戶](https://portal.azure.com)查找流分析作業。
2. 在更新相容性級別之前**停止**作業。 如果您的作業處於執行中狀態，則無法更新相容性層級。
3. 在 **"配置**"標題下，選擇**相容性級別**。
4. 選擇所需的相容性級別值。
5. 選擇 **"在**頁面底部保存"。

![Azure 入口網站中的串流分析相容性層級](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

更新相容性級別時，T 編譯器使用對應于所選相容性級別的語法驗證作業。

## <a name="compatibility-level-12"></a>相容性級別 1.2

相容性級別 1.2 引入了以下主要更改：

###  <a name="amqp-messaging-protocol"></a>AMQP 消息傳遞協定

**1.2 級別**：Azure 流分析使用[高級訊息佇列協定 （AMQP）](../service-bus-messaging/service-bus-amqp-overview.md)消息傳遞協定寫入服務匯流排佇列和主題。 透過開放式標準通訊協定，AMQP 可讓您打造一個跨平台的混合式應用程式。

### <a name="geospatial-functions"></a>GeoSpatial 函式

**以前的級別：** Azure 流分析使用地理計算。

**1.2 級別：** Azure 流分析允許您計算幾何投影地理座標。 地理空間函數的簽名沒有變化。 但是，它們的語義略有不同，允許比以前更精確的計算。

Azure 流分析支援地理空間參考資料索引。 包含地理空間元素的參考資料可以編制索引，以加快聯接計算。

更新後的地理空間函數帶來了已知文本 （WKT） 地理空間格式的全部表現力。 您可以指定 GeoJson 以前不支援的其他地理空間元件。

有關詳細資訊，請參閱[Azure 流分析 - 雲和 IoT 邊緣 中地理空間要素的更新](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/)。

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>具有多個分區的輸入源的並行查詢執行

**以前的級別：** Azure 流分析查詢需要使用分區 BY 子句來並行化跨輸入源分區的查詢處理。

**1.2 級別：** 如果查詢邏輯可以在輸入源分區之間並行化，Azure 流分析將創建單獨的查詢實例並並行運行計算。

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>本機批量 API 集成與 CosmosDB 輸出

**以前的級別：** 向上端行為是*插入或合併*。

**1.2 級別：** 本機批量 API 與 CosmosDB 輸出集成可最大限度地提高輸送量並高效地處理限制請求。 有關詳細資訊，請參閱[Azure 流分析輸出到 Azure Cosmos DB 頁](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-documentdb-output#improved-throughput-with-compatibility-level-12)。

向上端行為是*插入或替換*。

### <a name="datetimeoffset-when-writing-to-sql-output"></a>寫入 SQL 輸出時的日期時間偏移

**以前的級別：**[日期時間偏移](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017)類型已調整為 UTC。

**1.2 級別：** 日期時間偏移不再調整。

### <a name="long-when-writing-to-sql-output"></a>寫入 SQL 輸出時長

**以前的級別：** 根據目標型別截斷值。

**1.2 級別：** 根據輸出錯誤策略處理不適合目標型別的值。

### <a name="record-and-array-serialization-when-writing-to-sql-output"></a>寫入 SQL 輸出時記錄和陣列序列化

**以前的級別：** 記錄被寫入為"記錄"，陣列被寫入為"Array"。

**1.2 級別：** 記錄和陣列以 JSON 格式序列化。

### <a name="strict-validation-of-prefix-of-functions"></a>嚴格驗證函數的首碼

**以前的級別：** 沒有對函數首碼進行嚴格的驗證。

**1.2 級別：** Azure 流分析具有對函數首碼的嚴格驗證。 向內建函數添加首碼會導致錯誤。 例如，`myprefix.ABS(…)`不受支援。

向內置聚合添加首碼也會導致錯誤。 例如，`myprefix.SUM(…)`不受支援。

對任何使用者定義的函數使用首碼"系統"會導致錯誤。

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>禁止將陣列和物件作為 Cosmos DB 輸出配接器中的關鍵屬性

**以前的級別：** 陣列和物件類型作為關鍵屬性受支援。

**1.2 級別：** 陣列和物件類型不再作為鍵屬性受支援。

## <a name="compatibility-level-11"></a>相容性級別 1.1

相容性層級 1.1 中引入了下列重大變更：

### <a name="service-bus-xml-format"></a>服務匯流排 XML 格式

**1.0 級別：** Azure 流分析使用資料合同序列化程式，因此消息內容包含 XML 標記。 例如：

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**1.1 級：** 消息內容直接包含流，沒有其他標記。 例如： `{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>欄位名稱保持大小寫區分

**1.0 級別：** 當 Azure 流分析引擎處理欄位名稱時，欄位名稱更改為小寫。

**1.1 級別：** 當 Azure 流分析引擎處理欄位名稱時，將保留對欄位名稱的區分大小寫。

> [!NOTE]
> 使用 Edge 環境所裝載的串流分析作業尚無法使用保持大小寫區分的功能。 因此，如果您的作業裝載在 Edge 上，所有欄位名稱都會轉換為小寫。

### <a name="floatnandeserializationdisabled"></a>FloatNaNDeserializationDisabled

**1.0 級別：** 創建表命令未使用 NaN（非數位）篩選事件。 例如，Infinity, -Infinity) 的事件，因其不符合這些數字的記載範圍。

**1.1 級：** 創建表允許您指定強架構。 串流分析引擎會驗證資料是否符合此結構描述。 使用此模型，命令可以篩選具有 NaN 值的事件。

### <a name="disable-automatic-upcast-for-datetime-strings-in-json"></a>禁用 JSON 中日期時間字串的自動上播

**1.0 級別：** JSON 解析器將自動將具有日期/時間/區域資訊的字串值向上轉換為 DateTime 類型，然後將其轉換為 UTC。 此行為導致失去時區資訊。

**1.1 級：** 沒有更多的字串值自動向上轉換，具有日期/時間/區域資訊到日期時間類型。 如此一來，即可保留時區資訊。

## <a name="next-steps"></a>後續步驟

* [對 Azure 串流分析輸入進行疑難排解](stream-analytics-troubleshoot-input.md)
* [流分析資源運行狀況](stream-analytics-resource-health.md)
