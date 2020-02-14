---
title: Azure 串流分析相容性層級
description: 了解如何為 Azure 串流分析作業設定相容性層級，並了解最新相容性層級中的重大變更
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 14b04f7d6068863c79c7060d29b58232be1f40cb
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201793"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Azure 串流分析作業的相容性層級

本文說明 Azure 串流分析中的相容性層級選項。 串流分析是受管理的服務，具有週期性功能更新和效能改進。 大部分服務的執行時間更新會自動提供給終端使用者。 

不過，服務中的某些新功能可能會造成重大變更，例如現有作業的行為變更，或變更資料在執行中作業中的使用方式。 保持相容性層級設定減少，您就可以讓現有的串流分析作業在沒有重大變更的情況下執行。 當您準備好進行最新的執行時間行為時，您可以藉由提高相容性層級來加入宣告。 

## <a name="choose-a-compatibility-level"></a>選擇相容性層級

相容性層級可控制串流分析作業的執行階段行為。 

Azure 串流分析目前支援三種相容性層級：

* 1.0-先前的行為
* 1.1-預設行為
* 1.2-最近改良功能的最新行為

原始的1.0 相容性層級是在幾年前的 Azure 串流分析正式運作期間引進。

當您建立新的串流分析作業時，最佳作法是使用最新的相容性層級來建立它。 開始您的工作設計，依賴最新的行為，以避免日後新增變更和複雜度。

## <a name="set-the-compatibility-level"></a>設定相容性層級

您可以在 Azure 入口網站中設定串流分析作業的相容性層級，或使用 [[建立作業 REST API 呼叫](/rest/api/streamanalytics/stream-analytics-job)]。

若要更新 Azure 入口網站中作業的相容性層級：

1. 使用[Azure 入口網站](https://portal.azure.com)找出您的串流分析作業。
2. 請先**停止**作業，再更新相容性層級。 如果您的作業處於執行中狀態，則無法更新相容性層級。
3. 在 [**設定**] 標題下，選取 [**相容性層級**]。
4. 選擇您想要的相容性層級值。
5. 選取頁面底部的 [**儲存**]。

![Azure 入口網站中的串流分析相容性層級](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

當您更新相容性層級時，T 編譯器會以對應至所選相容性層級的語法來驗證作業。

## <a name="compatibility-level-12"></a>相容性層級1。2

相容性層級1.2 引進下列主要變更：

###  <a name="amqp-messaging-protocol"></a>AMQP 訊息通訊協定

**1.2 層級**： Azure 串流分析使用[先進的訊息佇列通訊協定（AMQP）](../service-bus-messaging/service-bus-amqp-overview.md)訊息通訊協定來寫入服務匯流排的佇列和主題。 透過開放式標準通訊協定，AMQP 可讓您打造一個跨平台的混合式應用程式。

### <a name="geospatial-functions"></a>GeoSpatial 函式

**先前的層級：** Azure 串流分析使用地理計算。

**1.2 層級：** Azure 串流分析可讓您計算幾何投射的地理座標。 地理空間函數的簽章不會有任何變更。 不過，它們的語法稍有不同，允許比以前更精確的計算。

Azure 串流分析支援地理空間參考資料索引。 包含地理空間元素的參考資料可以編制索引，以進行更快速的聯結計算。

更新的地理空間函式會帶入知名文字（WKT）地理空間格式的完整表達。 您可以指定先前不支援 GeoJson 的其他地理空間元件。

如需詳細資訊，請參閱[Azure 串流分析-雲端和 IoT Edge 中的地理空間功能更新](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/)。

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>具有多個資料分割之輸入來源的平行查詢執行

**先前的層級：** Azure 串流分析查詢需要使用 PARTITION BY 子句來平行處理輸入來源分割區的查詢。

**1.2 層級：** 如果查詢邏輯可以跨輸入來源資料分割進行平行處理，Azure 串流分析會建立個別的查詢實例，並以平行方式執行計算。

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>與 CosmosDB 輸出的原生大量 API 整合

**先前的層級：** Upsert 行為是*插入或合併*。

**1.2 層級：** 與 CosmosDB 輸出的原生大量 API 整合可將輸送量最大化，並有效率地處理節流要求。 如需詳細資訊，請參閱[Azure 串流分析輸出至 Azure Cosmos DB 頁面](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-documentdb-output#improved-throughput-with-compatibility-level-12)。

Upsert 行為是*insert 或 replace*。

### <a name="datetimeoffset-when-writing-to-sql-output"></a>寫入 SQL 輸出時的 DateTimeOffset

**先前的層級：** [DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017)類型已調整為 UTC。

**1.2 層級：** DateTimeOffset 已不再調整。

### <a name="long-when-writing-to-sql-output"></a>寫入 SQL 輸出時很長

**先前的層級：** 已根據目標型別截斷值。

**1.2 層級：** 不符合目標型別的值會根據輸出錯誤原則進行處理。

### <a name="record-and-array-serialization-when-writing-to-sql-output"></a>寫入 SQL 輸出時的記錄和陣列序列化

**先前的層級：** 記錄是以「記錄」的形式寫入，而陣列則是以「陣列」的形式寫入。

**1.2 層級：** 記錄和陣列會以 JSON 格式序列化。

### <a name="strict-validation-of-prefix-of-functions"></a>函數前置詞的嚴格驗證

**先前的層級：** 函數前置詞沒有嚴格的驗證。

**1.2 層級：** Azure 串流分析具有嚴格的函數前置詞驗證。 將前置詞加入至內建函數會造成錯誤。 例如，不支援`myprefix.ABS(…)`。

將前置詞加入至內建匯總也會導致錯誤。 例如，不支援 `myprefix.SUM(…)`。

針對任何使用者定義函數使用前置詞 "system" 會導致錯誤。

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>不允許陣列和物件做為 Cosmos DB 輸出介面卡中的索引鍵屬性

**先前的層級：** 支援陣列和物件類型做為索引鍵屬性。

**1.2 層級：** 不再支援陣列和物件類型做為索引鍵屬性。

## <a name="compatibility-level-11"></a>相容性層級1。1

相容性層級 1.1 中引入了下列重大變更：

### <a name="service-bus-xml-format"></a>服務匯流排 XML 格式

**1.0 層級：** Azure 串流分析使用 DataContractSerializer，因此訊息內容包含 XML 標記。 例如，

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**1.1 層級：** 訊息內容包含直接沒有其他標記的資料流程。 例如： `{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>功能變數名稱的保存區分大小寫

**1.0 層級：** 當 Azure 串流分析引擎處理時，功能變數名稱已變更為小寫。

**1.1 層級：** 當 Azure 串流分析引擎處理功能變數名稱時，會保存該功能變數名稱的區分大小寫。

> [!NOTE]
> 使用 Edge 環境所裝載的串流分析作業尚無法使用保持大小寫區分的功能。 因此，如果您的作業裝載在 Edge 上，所有欄位名稱都會轉換為小寫。

### <a name="floatnandeserializationdisabled"></a>FloatNaNDeserializationDisabled

**1.0 層級：** CREATE TABLE 命令未以 NaN （非-數位）篩選事件。 例如，Infinity, -Infinity) 的事件，因其不符合這些數字的記載範圍。

**1.1 層級：** CREATE TABLE 可讓您指定強式架構。 串流分析引擎會驗證資料是否符合此結構描述。 使用此模型，命令可以篩選具有 NaN 值的事件。

### <a name="disable-automatic-upcast-for-datetime-strings-in-json"></a>停用 JSON 中 datetime 字串的自動向上轉換

**1.0 層級：** JSON 剖析器會自動將日期/時間/區域資訊的字串值，轉換成日期時間類型，然後將它轉換成 UTC。 此行為導致遺失時區資訊。

**1.1 層級：** 不會再自動將日期/時間/區域資訊的字串值向上轉換成 DateTime 類型。 如此一來，即可保留時區資訊。

## <a name="next-steps"></a>後續步驟

* [對 Azure 串流分析輸入進行疑難排解](stream-analytics-troubleshoot-input.md)
* [串流分析資源健康狀態](stream-analytics-resource-health.md)
