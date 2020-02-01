---
title: 在 Azure 串流分析中剖析 JSON 和 AVRO
description: 本文說明如何對複雜資料類型 (如 陣列、JSON、CSV 格式的資料) 進行作業。
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.topic: conceptual
ms.date: 01/29/2020
ms.openlocfilehash: ac06521df38bdc91ca717d888c73cd541576014d
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905461"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>在 Azure 串流分析中剖析 JSON 和 Avro 資料

Azure 串流分析支援以 CSV、JSON 和 Avro 資料格式處理事件。 JSON 和 Avro 資料都可以結構化，並包含一些複雜類型，例如，嵌套物件（記錄）和陣列。 

>[!NOTE]
>事件中樞 Capture 所建立的 AVRO 檔案會使用特定格式，要求您使用*自訂*還原序列化程式功能。 如需詳細資訊，請參閱[使用 .net 自訂還原序列化程式以任何格式讀取輸入](https://docs.microsoft.com/azure/stream-analytics/custom-deserializer-examples)。



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

### <a name="access-nested-fields-in-known-schema"></a>存取已知架構中的嵌套欄位
使用點標記法（.）可輕鬆地直接從查詢存取嵌套的欄位。 例如，此查詢會選取上述 JSON 資料中 Location 屬性下的緯度和經度座標。 點標記法可以用來導覽多個層級，如下所示。

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

|DeviceID|Lat|長|溫度|版本|
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

|DeviceID|Lat|長|
|-|-|-|
|12345|47|122|


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>當屬性名稱為變數時存取嵌套欄位

如果屬性名稱是變數，請使用[GetRecordPropertyValue](https://docs.microsoft.com/stream-analytics-query/getrecordpropertyvalue-azure-stream-analytics)函數。 這可讓您建立動態查詢，而不需要硬式編碼屬性名稱。

例如，假設範例資料流程需要與包含每個裝置感應器閾值的**參考資料聯結**。 以下顯示這類參考資料的程式碼片段。

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

這裡的目標是將我們的範例資料集從文章頂端加入該參考資料，並將每個感應器量值的一個事件輸出到高於其閾值。 這表示，如果多個感應器高於其各自的臨界值，上述單一事件就會產生多個輸出事件，感謝您的聯結。 若要在沒有聯結的情況下達到類似結果，請參閱下一節。

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

**GetRecordPropertyValue**會在*SensorReadings*中選取屬性，其名稱符合來自參考資料的屬性名稱。 然後會解壓縮*SensorReadings*中的相關值。

結果如下：

|DeviceID|SensorName|AlertMessage|
|-|-|-|
|12345|溼度|警示：感應器高於閾值|

### <a name="convert-record-fields-into-separate-events"></a>將記錄欄位轉換成不同的事件

若要將資料列欄位轉換成個別的事件，請搭配使用 [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) 運算子和 [GetRecordProperties](https://docs.microsoft.com/stream-analytics-query/getrecordproperties-azure-stream-analytics) 函式。

使用原始範例資料時，可以使用下列查詢，將屬性解壓縮至不同的事件。

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
|12345|SensorMetadata|[物件物件]|

您可以使用搭配，將這些事件路由傳送至[不同的目的地](https://docs.microsoft.com/stream-analytics-query/with-azure-stream-analytics)：

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

## <a name="array-data-types"></a>陣列資料類型

陣列資料類型是已排序的值集合。 以下詳述陣列值的一些典型作業。 這些範例使用函式 [GetArrayElement](https://docs.microsoft.com/stream-analytics-query/getarrayelement-azure-stream-analytics)、[GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics)、[GetArrayLength](https://docs.microsoft.com/stream-analytics-query/getarraylength-azure-stream-analytics)，以及 [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) 運算子。

以下是單一事件的範例。 `CustomSensor03` 和 `SensorMetadata` 都是**陣列**類型：

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

### <a name="working-with-a-specific-array-element"></a>使用特定陣列元素

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

### <a name="convert-array-elements-into-separate-events"></a>將陣列元素轉換成不同的事件

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

如果解壓縮的欄位需要出現在資料行中，除了[聯結](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics)作業以外，您還可以使用[WITH](https://docs.microsoft.com/stream-analytics-query/with-azure-stream-analytics)語法來資料集。 該聯結需要的[時間界限](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics#BKMK_DateDiff)條件會阻止重複：

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

|deviceId|Lat|長|smVersion|smManufacturer|
|-|-|-|-|-|
|12345|47|122|1.2.45|ABC|

## <a name="see-also"></a>另請參閱
[Azure 串流分析中的資料類型](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics) \(英文\)
