---
title: 在 Azure 串流分析中剖析 JSON 和 AVRO
description: 本文說明如何對複雜資料類型 (如 陣列、JSON、CSV 格式的資料) 進行作業。
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.topic: conceptual
ms.date: 01/29/2020
ms.openlocfilehash: 73905483850a47a9d036bef1b9e1ee60d3484555
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484582"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>在 Azure 串流分析中剖析 JSON 和 Avro 資料

Azure 流分析支援以 CSV、JSON 和 Avro 資料格式處理事件。 JSON 和 Avro 資料都可以結構化，並包含一些複雜的類型，如嵌套物件（記錄）和陣列。 

>[!NOTE]
>事件中心捕獲創建的 AVRO 檔使用特定的格式，要求您使用*自訂反序列化功能*。 有關詳細資訊，請參閱使用[.NET 自訂反序列化器 以任何格式讀取輸入](https://docs.microsoft.com/azure/stream-analytics/custom-deserializer-examples)。



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

### <a name="access-nested-fields-in-known-schema"></a>訪問已知架構中的嵌套欄位
使用點標記法 （.） 直接從查詢輕鬆訪問嵌套欄位。 例如，此查詢選擇上述 JSON 資料中位置屬性下的緯度和經度座標。 點符號可用於導航多個級別，如下所示。

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

|裝置識別碼|Lat|long|溫度|版本|
|-|-|-|-|-|
|12345|47|122|80|1.2.45|


### <a name="select-all-properties"></a>選擇所有屬性
您可以使用 '*' 萬用字元選取巢狀資料列的所有屬性。 請考慮下列範例：

```SQL
SELECT
    DeviceID,
    Location.*
FROM input
```

結果如下：

|裝置識別碼|Lat|long|
|-|-|-|
|12345|47|122|


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>當屬性名稱是變數時訪問嵌套欄位

如果屬性名稱是變數，請使用[GetRecord屬性值](https://docs.microsoft.com/stream-analytics-query/getrecordpropertyvalue-azure-stream-analytics)函數。 這允許生成動態查詢，而無需硬編碼屬性名稱。

例如，假設示例資料流程需要與包含每個設備感應器閾值的**參考資料聯接**。 此類參考資料片段如下所示。

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

此處的目標是將示例資料集從文章頂部聯接到該參考資料，並為每個感應器度量值超過其閾值輸出一個事件。 這意味著，如果多個感應器超過各自的閾值，則由於聯接，上述單個事件可以生成多個輸出事件。 要在沒有聯接的情況下實現類似的結果，請參閱下面的部分。

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

**GetRecordPropertyValue**在*SensorReadings*中選擇屬性，該屬性的名稱與來自參考資料的屬性名稱匹配。 然後提取*感應器讀取中的*關聯值。

結果如下：

|裝置識別碼|感應器名稱|警報消息|
|-|-|-|
|12345|溼度|警報 ： 高於閾值的感應器|

### <a name="convert-record-fields-into-separate-events"></a>將記錄欄位轉換為單獨的事件

若要將資料列欄位轉換成個別的事件，請搭配使用 [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) 運算子和 [GetRecordProperties](https://docs.microsoft.com/stream-analytics-query/getrecordproperties-azure-stream-analytics) 函式。

使用原始示例資料，可以使用以下查詢將屬性提取到不同的事件。

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```

結果如下：

|裝置識別碼|感應器名稱|警報消息|
|-|-|-|
|12345|溫度|80|
|12345|溼度|70|
|12345|自訂感應器01|5|
|12345|自訂感應器02|99|
|12345|感應器中繼資料|[物件]|

使用[WITH](https://docs.microsoft.com/stream-analytics-query/with-azure-stream-analytics)，就可以將這些事件路由到不同的目標：

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

### <a name="parse-json-record-in-sql-reference-data"></a>在 SQL 參考資料中分析 JSON 記錄
在作業中使用 Azure SQL 資料庫作為參考資料時，可以有一個包含 JSON 格式資料的列。 範例如下所示。

|裝置識別碼|資料|
|-|-|
|12345|{"鍵"："值 1"}|
|54321|{"鍵"："值 2"}|

您可以通過編寫簡單的 JavaScript 使用者定義的函數來分析*資料*列中的 JSON 記錄。

```javascript
function parseJson(string) {
return JSON.parse(string);
}
```

然後，您可以在流分析查詢中創建一個步驟，如下所示，以訪問 JSON 記錄的欄位。

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

下面是單個事件的示例。 和`CustomSensor03``SensorMetadata`都是類型**陣列**：

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

|第一元素|
|-|
|12|

### <a name="select-array-length"></a>選擇陣列長度

```SQL
SELECT
    GetArrayLength(SensorReadings.CustomSensor03) AS arrayLength
FROM input
```

結果如下：

|arrayLength|
|-|
|3|

### <a name="convert-array-elements-into-separate-events"></a>將陣列元素轉換為單獨的事件

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

|deviceId|陣列索引|ArrayValue|
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

如果提取的欄位需要顯示在列中，除了[JOIN](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics)操作之外，還可以使用[WITH](https://docs.microsoft.com/stream-analytics-query/with-azure-stream-analytics)語法對資料集進行透視。 該聯接將需要一個[防止重複的時間邊界](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics#BKMK_DateDiff)條件：

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

|deviceId|Lat|long|smVersion|sm製造商|
|-|-|-|-|-|
|12345|47|122|1.2.45|ABC|

## <a name="see-also"></a>另請參閱
[Azure 串流分析中的資料類型](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics) \(英文\)
