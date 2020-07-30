---
title: 在 Azure 串流分析中剖析 JSON 和 AVRO
description: 本文說明如何對複雜資料類型 (如 陣列、JSON、CSV 格式的資料) 進行作業。
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.topic: conceptual
ms.date: 01/29/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: ff3ae9a787586a4d3f7c27353aca37326be32448
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2020
ms.locfileid: "87432538"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>在 Azure 串流分析中剖析 JSON 和 Avro 資料

Azure 串流分析支援處理資料格式為 CSV、JSON 和 Avro 的事件。 JSON 和 Avro 資料都可以結構化並包含某些複雜類型，例如巢狀物件 (資料列) 和陣列。 

>[!NOTE]
>由事件中樞擷取所建立的 AVRO 檔案是使用特定的格式，其會要求您使用「自訂還原序列化程式」功能。 如需詳細資訊，請參閱[使用 .NET 自訂還原序列化程式來讀取任何格式的輸入](https://docs.microsoft.com/azure/stream-analytics/custom-deserializer-examples) \(部分機器翻譯\)。
>
>串流分析 AVRO 還原序列化並不支援對應類型。 串流分析無法讀取事件中樞擷取 Blob，因為事件中樞擷取會使用對應。


## <a name="record-data-types"></a>資料列資料類型
當輸入資料流中是使用資料列資料類型時，此資料類型會用來代表 JSON 和 Avro。 這些範例示範讀取 JSON 格式輸入事件的範例感應器。 以下是單一事件的範例：

```json
{
    "DeviceId" : "12345",
    "Location" :
    {
        "Lat": 47,
        "Long": 122
    },
    "SensorReadings" :
    {
        "Temperature" : 80,
        "Humidity" : 70,
        "CustomSensor01" : 5,
        "CustomSensor02" : 99,
        "SensorMetadata" : 
        {
        "Manufacturer":"ABC",
        "Version":"1.2.45"
        }
    }
}
```

### <a name="access-nested-fields-in-known-schema"></a>存取已知結構描述中的巢狀欄位
使用點標記法 (.) 來直接從查詢中輕鬆存取巢狀欄位。 例如，此查詢會選取上述 JSON 資料中 Location 屬性下的緯度和經度座標。 點標記法可以用來瀏覽多個層級，如下所示。

```SQL
SELECT
    DeviceID,
    Location.Lat,
    Location.Long,
    SensorReadings.Temperature,
    SensorReadings.SensorMetadata.Version
FROM input
```

結果如下：

|DeviceID|Lat|long|溫度|版本|
|-|-|-|-|-|
|12345|47|122|80|1.2.45|


### <a name="select-all-properties"></a>選取所有屬性
您可以使用 '*' 萬用字元選取巢狀資料列的所有屬性。 請考慮下列範例：

```SQL
SELECT
    DeviceID,
    Location.*
FROM input
```

結果如下：

|DeviceID|Lat|long|
|-|-|-|
|12345|47|122|


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>在屬性名稱為變數時存取巢狀欄位

如果屬性名稱為變數，請使用 [GetRecordPropertyValue](https://docs.microsoft.com/stream-analytics-query/getrecordpropertyvalue-azure-stream-analytics) \(英文\) 函式。 這可讓您在無需對屬性名稱進行硬式編碼的情況下建置動態查詢。

例如，假設範例資料流需要與包含每個裝置感應器閾值的**參考資料聯結**。 下列顯示這類參考資料的程式碼片段。

```json
{
    "DeviceId" : "12345",
    "SensorName" : "Temperature",
    "Value" : 85
},
{
    "DeviceId" : "12345",
    "SensorName" : "Humidity",
    "Value" : 65
}
```

這裡的目標是要將文章頂端的範例資料集與該參考資料聯結，並針對每個量值超過其閾值的感應器輸出單一事件。 那表示如果有多個感應器皆高於其對應的閾值，透過聯結便能使上面的單一事件產生多個輸出事件。 若要在不使用聯結的情況下達成類似結果，請參閱下列小節。

```SQL
SELECT
    input.DeviceID,
    thresholds.SensorName,
    "Alert : Sensor above threshold" AS AlertMessage
FROM input      -- stream input
JOIN thresholds -- reference data input
ON
    input.DeviceId = thresholds.DeviceId
WHERE
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value
```

**GetRecordPropertyValue** 會選取 *SensorReadings* 中的屬性，其會以名稱比對來自參考資料的屬性名稱。 接著會擷取來自 *SensorReadings* 的相關聯值。

結果如下：

|DeviceID|SensorName|AlertMessage|
|-|-|-|
|12345|溼度|警示:感應器超過閾值|

### <a name="convert-record-fields-into-separate-events"></a>將記錄欄位轉換為個別事件

若要將資料列欄位轉換成個別的事件，請搭配使用 [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) 運算子和 [GetRecordProperties](https://docs.microsoft.com/stream-analytics-query/getrecordproperties-azure-stream-analytics) 函式。

使用原始範例資料時，下列查詢可用來將屬性擷取為不同的事件。

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```

結果如下：

|DeviceID|SensorName|AlertMessage|
|-|-|-|
|12345|溫度|80|
|12345|溼度|70|
|12345|CustomSensor01|5|
|12345|CustomSensor02|99|
|12345|SensorMetadata|[object Object]|

透過使用 [WITH](https://docs.microsoft.com/stream-analytics-query/with-azure-stream-analytics) \(英文\)，便可以將那些事件路由到不同的目的地：

```SQL
WITH Stage0 AS
(
    SELECT
        event.DeviceID,
        sensorReading.PropertyName,
        sensorReading.PropertyValue
    FROM input as event
    CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
)

SELECT DeviceID, PropertyValue AS Temperature INTO TemperatureOutput FROM Stage0 WHERE PropertyName = 'Temperature'
SELECT DeviceID, PropertyValue AS Humidity INTO HumidityOutput FROM Stage0 WHERE PropertyName = 'Humidity'
```

### <a name="parse-json-record-in-sql-reference-data"></a>剖析 SQL 參考資料中的 JSON 記錄
使用 Azure SQL Database 作為作業中的參考資料時，有可能會有包含 JSON 格式資料的資料行。 範例如下所示。

|DeviceID|資料|
|-|-|
|12345|{"key" : "value1"}|
|54321|{"key" : "value2"}|

您可以透過撰寫簡單的 JavaScript 使用者定義函式，來剖析 *Data* 資料行中的 JSON 記錄。

```javascript
function parseJson(string) {
return JSON.parse(string);
}
```

您接著可以在串流分析查詢中建立步驟來存取 JSON 記錄中的欄位，如下所示。

 ```SQL
 WITH parseJson as
 (
 SELECT DeviceID, udf.parseJson(sqlRefInput.Data) as metadata,
 FROM sqlRefInput
 )
 
 SELECT metadata.key
 INTO output
 FROM streamInput
 JOIN parseJson 
 ON streamInput.DeviceID = parseJson.DeviceID
```

## <a name="array-data-types"></a>陣列資料類型

陣列資料類型是已排序的值集合。 以下詳述陣列值的一些典型作業。 這些範例使用函式 [GetArrayElement](https://docs.microsoft.com/stream-analytics-query/getarrayelement-azure-stream-analytics)、[GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics)、[GetArrayLength](https://docs.microsoft.com/stream-analytics-query/getarraylength-azure-stream-analytics)，以及 [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) 運算子。

以下是單一事件的範例。 `CustomSensor03` 和 `SensorMetadata` 都是 **array** 類型：

```json
{
    "DeviceId" : "12345",
    "SensorReadings" :
    {
        "Temperature" : 80,
        "Humidity" : 70,
        "CustomSensor01" : 5,
        "CustomSensor02" : 99,
        "CustomSensor03": [12,-5,0]
     },
    "SensorMetadata":[
        {          
            "smKey":"Manufacturer",
            "smValue":"ABC"                
        },
        {
            "smKey":"Version",
            "smValue":"1.2.45"
        }
    ]
}
```

### <a name="working-with-a-specific-array-element"></a>使用特定的陣列元素

選取在指定索引的陣列元素 (選取第一個陣列元素)：

```SQL
SELECT
    GetArrayElement(SensorReadings.CustomSensor03, 0) AS firstElement
FROM input
```

結果如下：

|firstElement|
|-|
|12|

### <a name="select-array-length"></a>選取陣列長度

```SQL
SELECT
    GetArrayLength(SensorReadings.CustomSensor03) AS arrayLength
FROM input
```

結果如下：

|arrayLength|
|-|
|3|

### <a name="convert-array-elements-into-separate-events"></a>將陣列元素轉換為個別事件

以個別事件選取所有陣列元素。 [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) 運算子和 [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics) 內建函式一起擷取所有陣列元素且視為個別事件：

```SQL
SELECT
    DeviceId,
    CustomSensor03Record.ArrayIndex,
    CustomSensor03Record.ArrayValue
FROM input
CROSS APPLY GetArrayElements(SensorReadings.CustomSensor03) AS CustomSensor03Record

```

結果如下：

|deviceId|ArrayIndex|ArrayValue|
|-|-|-|
|12345|0|12|
|12345|1|-5|
|12345|2|0|

```SQL
SELECT   
    i.DeviceId, 
    SensorMetadataRecords.ArrayValue.smKey as smKey,
    SensorMetadataRecords.ArrayValue.smValue as smValue
FROM input i
CROSS APPLY GetArrayElements(SensorMetadata) AS SensorMetadataRecords
 ```
 
結果如下：

|deviceId|smKey|smValue|
|-|-|-|
|12345|製造商|ABC|
|12345|版本|1.2.45|

如果擷取的欄位必須出現在資料行中，您可以搭配 [JOIN](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics) \(英文\) 作業使用 [WITH](https://docs.microsoft.com/stream-analytics-query/with-azure-stream-analytics) \(英文\) 語法來對資料集進行樞紐分析。 該聯結將會需要能避免重複的[時間界限](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics#BKMK_DateDiff) \(英文\) 條件：

```SQL
WITH DynamicCTE AS (
    SELECT   
        i.DeviceId,
        SensorMetadataRecords.ArrayValue.smKey as smKey,
        SensorMetadataRecords.ArrayValue.smValue as smValue
    FROM input i
    CROSS APPLY GetArrayElements(SensorMetadata) AS SensorMetadataRecords 
)

SELECT
    i.DeviceId,
    i.Location.*,
    V.smValue AS 'smVersion',
    M.smValue AS 'smManufacturer'
FROM input i
LEFT JOIN DynamicCTE V ON V.smKey = 'Version' and V.DeviceId = i.DeviceId AND DATEDIFF(minute,i,V) BETWEEN 0 AND 0 
LEFT JOIN DynamicCTE M ON M.smKey = 'Manufacturer' and M.DeviceId = i.DeviceId AND DATEDIFF(minute,i,M) BETWEEN 0 AND 0
```

結果如下：

|deviceId|Lat|long|smVersion|smManufacturer|
|-|-|-|-|-|
|12345|47|122|1.2.45|ABC|

## <a name="see-also"></a>另請參閱
[Azure 串流分析中的資料類型](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics) \(英文\)
