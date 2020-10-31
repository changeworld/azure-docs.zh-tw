---
title: Azure 串流分析相容性層級
description: 了解如何為 Azure 串流分析作業設定相容性層級，並了解最新相容性層級中的重大變更
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 87ee8a9c57fc456ba02f97bf56db25e4c91e9398
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93129808"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Azure 串流分析作業的相容性層級

本文說明 Azure 串流分析中的相容性層級選項。 串流分析是受控服務，具有一般功能更新和效能改善。 大部分服務的執行時間更新都會自動提供給終端使用者。 

不過，服務中的某些新功能可能會引進重大變更，例如現有工作的行為變更，或是在執行中的作業中使用資料的方式變更。 您可以保留相容性層級的設定，讓現有的串流分析作業執行，而不需要進行重大變更。 當您準備好使用最新的執行時間行為時，您可以藉由提高相容性層級來加入宣告。 

## <a name="choose-a-compatibility-level"></a>選擇相容性層級

相容性層級可控制串流分析作業的執行階段行為。 

Azure 串流分析目前支援三種相容性層級：

* 1.0-原始相容性層級是在 Azure 串流分析于幾年前的正式運作期間引進。
* 1.1-先前的行為
* 1.2-最近改進的最新行為

當您建立新的串流分析作業時，最佳作法是使用最新的相容性層級來建立它。 開始您的作業設計，依賴最新的行為，以避免在稍後新增變更和複雜度。

## <a name="set-the-compatibility-level"></a>設定相容性層級

您可以在 Azure 入口網站中設定串流分析作業的相容性層級，或使用 [建立作業 REST API 呼叫](./stream-analytics-quick-create-portal.md)來設定相容性層級。

若要更新 Azure 入口網站中作業的相容性層級：

1. 使用 [Azure 入口網站](https://portal.azure.com) 來尋找您的串流分析作業。
2. 更新相容性層級之前，請先 **停止** 作業。 如果您的作業處於執行中狀態，則無法更新相容性層級。
3. 在 [ **設定** ] 標題下，選取 [ **相容性層級** ]。
4. 選擇您想要的相容性層級值。
5. 選取頁面底部的 [ **儲存** ]。

![Azure 入口網站中的串流分析相容性層級](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

當您更新相容性層級時，T 編譯器會以對應至所選相容性層級的語法來驗證作業。

## <a name="compatibility-level-12"></a>相容性層級1。2

相容性層級1.2 中引進了下列重大變更：

###  <a name="amqp-messaging-protocol"></a>AMQP 訊息通訊協定

**1.2 層級** ： Azure 串流分析使用 [Advanced Message 佇列通訊協定 (AMQP)](../service-bus-messaging/service-bus-amqp-overview.md) 訊息通訊協定寫入至服務匯流排佇列和主題。 透過開放式標準通訊協定，AMQP 可讓您打造一個跨平台的混合式應用程式。

### <a name="geospatial-functions"></a>GeoSpatial 函式

**先前的層級：** Azure 串流分析使用地理位置計算。

**1.2 層級：** Azure 串流分析可讓您計算幾何投射的地理座標。 地理空間函式的簽章不會有任何變更。 不過，它們的語法稍有不同，因此可以比以往更精確地計算。

Azure 串流分析支援地理空間參考資料索引。 您可以為包含地理空間元素的參考資料編制索引，以加快聯結的計算速度。

更新的地理空間函式會將知名文字的完整表達， (WKT) 地理空間格式。 您可以指定 GeoJson 先前未支援的其他地理空間元件。

如需詳細資訊，請參閱 [Azure 串流分析中的地理空間功能更新–雲端和 IoT Edge](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/)。

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>具有多個資料分割之輸入來源的平行查詢執行

**先前的層級：** Azure 串流分析查詢需要使用 PARTITION BY 子句，以便跨輸入來源資料分割平行處理查詢處理。

**1.2 層級：** 如果查詢邏輯可以跨輸入來源分割區進行平行處理，Azure 串流分析會建立個別的查詢實例，並以平行方式執行計算。

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>原生大量 API 與 CosmosDB 輸出整合

**先前的層級：** Upsert 行為為 *insert 或 merge* 。

**1.2 層級：** 原生大量 API 與 CosmosDB 輸出整合可將輸送量最大化，並有效率地處理節流要求。 如需詳細資訊，請參閱 [Azure Cosmos DB 頁面的 Azure 串流分析輸出](./stream-analytics-documentdb-output.md#improved-throughput-with-compatibility-level-12)。

Upsert 行為為 *insert 或 replace* 。

### <a name="datetimeoffset-when-writing-to-sql-output"></a>寫入 SQL 輸出時的 DateTimeOffset

**先前的層級：** [DateTimeOffset](/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) 類型已調整為 UTC。

**1.2 層級：** DateTimeOffset 不再調整。

### <a name="long-when-writing-to-sql-output"></a>寫入 SQL 輸出時的長時間

**先前的層級：** 根據目標型別來截斷值。

**1.2 層級：** 不符合目標型別的值會根據輸出錯誤原則進行處理。

### <a name="record-and-array-serialization-when-writing-to-sql-output"></a>寫入 SQL 輸出時的記錄和陣列序列化

**先前的層級：** 記錄是以「記錄」的形式寫入，而陣列則撰寫為「陣列」。

**1.2 層級：** 記錄和陣列會以 JSON 格式序列化。

### <a name="strict-validation-of-prefix-of-functions"></a>函數前置詞的嚴格驗證

**先前的層級：** 函數首碼沒有嚴格的驗證。

**1.2 層級：** Azure 串流分析會嚴格驗證函式首碼。 將前置詞加入至內建函數會造成錯誤。 例如， `myprefix.ABS(…)` 不支援。

將前置詞加入至內建匯總也會導致錯誤。 例如， `myprefix.SUM(…)` 不支援。

針對任何使用者定義函數使用前置詞 "system" 會導致錯誤。

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>在 Cosmos DB 輸出介面卡中不允許陣列和物件做為索引鍵屬性

**先前的層級：** 支援陣列和物件類型做為索引鍵屬性。

**1.2 層級：** 不再支援陣列和物件類型作為索引鍵屬性。

## <a name="compatibility-level-11"></a>相容性層級1。1

相容性層級 1.1 中引入了下列重大變更：

### <a name="service-bus-xml-format"></a>服務匯流排 XML 格式

**1.0 層級：** Azure 串流分析使用 DataContractSerializer，因此訊息內容包含 XML 標記。 例如：

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**1.1 層級：** 訊息內容直接包含沒有其他標記的資料流程。 例如：`{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>欄位名稱保持大小寫區分

**1.0 層級：** 當 Azure 串流分析引擎處理時，功能變數名稱會變更為小寫。

**1.1 層級：** 由 Azure 串流分析引擎處理時，會保留區分大小寫的功能變數名稱。

> [!NOTE]
> 使用 Edge 環境所裝載的串流分析作業尚無法使用保持大小寫區分的功能。 因此，如果您的作業裝載在 Edge 上，所有欄位名稱都會轉換為小寫。

### <a name="floatnandeserializationdisabled"></a>FloatNaNDeserializationDisabled

**1.0 層級：** CREATE TABLE 命令未以 NaN (非數位來篩選事件。 例如，Infinity, -Infinity) 的事件，因其不符合這些數字的記載範圍。

**1.1 層級：** CREATE TABLE 可讓您指定強式架構。 串流分析引擎會驗證資料是否符合此結構描述。 使用此模型，命令可以篩選具有 NaN 值的事件。

### <a name="disable-automatic-conversion-of-datetime-strings-to-datetime-type-at-ingress-for-json"></a>停用將 datetime 字串自動轉換成 JSON 輸入的日期時間類型

**1.0 層級：** JSON 剖析器會在輸入時自動將日期/時間/區域資訊的字串值轉換成日期時間類型，如此一來，值就會立即失去其原始格式和時區資訊。 因為這是在輸入時完成的，即使該欄位未在查詢中使用，也會轉換成 UTC 日期時間。

**1.1 層級：** 不會自動將具有日期/時間/區域資訊的字串值轉換成 DATETIME 類型。 因此，會保留時區資訊和原始格式。 但是，如果在查詢中使用 NVARCHAR (MAX) 欄位做為 DATETIME 運算式的一部分 (DATEADD 函數，例如) ，它就會轉換成 DATETIME 類型來執行計算，並失去其原始形式。

## <a name="next-steps"></a>後續步驟

* [對 Azure 串流分析輸入進行疑難排解](stream-analytics-troubleshoot-input.md)
* [串流分析資源健康狀態](./stream-analytics-troubleshoot-query.md)