---
title: Azure 串流分析資源記錄檔資料錯誤
description: 本文說明使用 Azure 串流分析時可能發生的不同輸入和輸出資料錯誤。
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.openlocfilehash: 48f178a74dea0403ff8926cf34fd64cdd9c6839f
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92071994"
---
# <a name="azure-stream-analytics-data-errors"></a>Azure 串流分析資料錯誤

資料錯誤是處理資料時所發生的錯誤。  這些錯誤通常會在資料還原序列化、序列化和寫入作業期間發生。  發生資料錯誤時，串流分析會將詳細的資訊和範例事件寫入資源記錄。 在您的作業中啟用診斷記錄，以取得這些額外的詳細資料。 在某些情況下，您也可以透過入口網站通知來提供此資訊的摘要。

本文概述輸入和輸出資料錯誤的不同錯誤類型、原因和資源記錄檔詳細資料。

## <a name="resource-logs-schema"></a>資源記錄架構

請參閱使用診斷記錄檔來查看資源記錄的架構，以針對 [Azure 串流分析進行疑難排解](stream-analytics-job-diagnostic-logs.md#resource-logs-schema) 。 下列 JSON 是資源記錄檔的 **屬性** 欄位的範例值，用於資料錯誤。

```json
{
    "Source": "InputTelemetryData",
    "Type": "DataError",
    "DataErrorType": "InputDeserializerError.InvalidData",
    "BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "Message": "Input Message Id: https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt Error: Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "ExampleEvents": "[\"1,2\\\\u000d\\\\u000a3,4\\\\u000d\\\\u000a5,6\"]",
    "FromTimestamp": "2019-03-22T22:34:18.5664937Z",
    "ToTimestamp": "2019-03-22T22:34:18.5965248Z",
    "EventCount": 1
}
```

## <a name="input-data-errors"></a>輸入資料錯誤

### <a name="inputdeserializererrorinvalidcompressiontype"></a>InputDeserializerError.InvalidCompressionType

* 原因：選取的輸入壓縮類型與資料不符。
* 提供的入口網站通知：是
* 資源記錄層級：警告
* 影響：含有任何還原序列化錯誤的訊息（包括不正確壓縮類型）會從輸入中卸載。
* 記錄詳細資料
   * 輸入訊息識別碼。 針對事件中樞，識別碼為 PartitionId、Offset 和序號。

**錯誤訊息**

```json
"BriefMessage": "Unable to decompress events from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please ensure compression setting fits the data being processed."
```

### <a name="inputdeserializererrorinvalidheader"></a>InputDeserializerError.InvalidHeader

* 原因：輸入資料的標頭無效。 例如，CSV 有名稱重複的資料行。
* 提供的入口網站通知：是
* 資源記錄層級：警告
* 影響：含有任何還原序列化錯誤的訊息（包括不正確標頭）會從輸入中卸載。
* 記錄詳細資料
   * 輸入訊息識別碼。 
   * 實際的承載，最多可達千位元組。

**錯誤訊息**

```json
"BriefMessage": "Invalid CSV Header for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please make sure there are no duplicate field names."
```

### <a name="inputdeserializererrormissingcolumns"></a>InputDeserializerError.MissingColumns

* 原因：使用 CREATE TABLE 或透過 TIMESTAMP BY 定義的輸入資料行不存在。
* 提供的入口網站通知：是
* 資源記錄層級：警告
* 影響：遺漏資料行的事件會從輸入中卸載。
* 記錄詳細資料
   * 輸入訊息識別碼。 
   * 遺漏的資料行名稱。 
   * 實際的承載，最多可達數 kb。

**錯誤訊息**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Missing fields specified in query or in create table. Fields expected:ColumnA Fields found:ColumnB"
```

### <a name="inputdeserializererrortypeconversionerror"></a>InputDeserializerError.TypeConversionError

* 原因：無法將輸入轉換為 CREATE TABLE 語句中指定的類型。
* 提供的入口網站通知：是
* 資源記錄層級：警告
* 影響：類型轉換錯誤的事件會從輸入中卸載。
* 記錄詳細資料
   * 輸入訊息識別碼。 
   * 資料行的名稱和預期的類型。

**錯誤訊息**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource '''https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Unable to convert column: dateColumn to expected type."
```

### <a name="inputdeserializererrorinvaliddata"></a>InputDeserializerError.InvalidData

* 原因：輸入資料的格式不正確。 例如，輸入不是有效的 JSON。
* 提供的入口網站通知：是
* 資源記錄層級：警告
* 影響：遇到無效資料錯誤之後，訊息中的所有事件都會從輸入中卸載。
* 記錄詳細資料
   * 輸入訊息識別碼。 
   * 實際的承載，最多可達千位元組。

**錯誤訊息**

```json
"BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

```json
"Message": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

### <a name="invalidinputtimestamp"></a>InvalidInputTimeStamp

* 原因： TIMESTAMP BY 運算式的值無法轉換成 datetime。
* 提供的入口網站通知：是
* 資源記錄層級：警告
* 影響：輸入中有無效輸入時間戳記的事件。
* 記錄詳細資料
   * 輸入訊息識別碼。 
   * 錯誤訊息。 
   * 實際的承載，最多可達千位元組。

**錯誤訊息**

```json
"BriefMessage": "Unable to get timestamp for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' due to error 'Cannot convert string to datetime'"
```

### <a name="invalidinputtimestampkey"></a>InvalidInputTimeStampKey

* 原因： TIMESTAMP BY OVER Timestampcolumn) 的值為 Null。
* 提供的入口網站通知：是
* 資源記錄層級：警告
* 影響：輸入的時間戳記索引鍵不正確事件會從輸入中卸載。
* 記錄詳細資料
   * 實際的承載，最多可達千位元組。

**錯誤訊息**

```json
"BriefMessage": "Unable to get value of TIMESTAMP BY OVER COLUMN"
```

### <a name="lateinputevent"></a>LateInputEvent

* 原因：應用程式時間與抵達時間之間的差異大於延遲抵達容錯時間範圍。
* 提供的入口網站通知：否
* 資源記錄層級：資訊
* 影響：在作業設定的事件順序區段中，根據「處理其他事件」設定來處理晚期輸入事件。 如需詳細資訊，請參閱 [時間處理原則](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)。
* 記錄詳細資料
   * 應用程式時間與抵達時間。 
   * 實際的承載，最多可達千位元組。

**錯誤訊息**

```json
"BriefMessage": "Input event with application timestamp '2019-01-01' and arrival time '2019-01-02' was sent later than configured tolerance."
```

### <a name="earlyinputevent"></a>EarlyInputEvent

* 原因：應用程式時間與抵達時間之間的差異大於5分鐘。
* 提供的入口網站通知：否
* 資源記錄層級：資訊
* 影響：提早輸入事件會根據作業設定的事件順序區段中的「處理其他事件」設定來處理。 如需詳細資訊，請參閱 [時間處理原則](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)。
* 記錄詳細資料
   * 應用程式時間與抵達時間。 
   * 實際的承載，最多可達千位元組。

**錯誤訊息**

```json
"BriefMessage": "Input event arrival time '2019-01-01' is earlier than input event application timestamp '2019-01-02' by more than 5 minutes."
```

### <a name="outoforderevent"></a>OutOfOrderEvent

* 原因：根據定義的順序容錯時間範圍，事件被視為不按照順序。
* 提供的入口網站通知：否
* 資源記錄層級：資訊
* 影響：系統會根據作業設定之事件排列區段中的「處理其他事件」設定來處理順序不對事件。 如需詳細資訊，請參閱 [時間處理原則](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)。
* 記錄詳細資料
   * 實際的承載，最多可達千位元組。

**錯誤訊息**

```json
"Message": "Out of order event(s) received."
```

## <a name="output-data-errors"></a>輸出資料錯誤

Azure 串流分析可以根據設定，識別輸出接收的輸出資料錯誤，或不使用 i/o 要求。 例如，  `PartitionKey` 當您使用 Azure 資料表輸出時，如果沒有 i/o 要求，就會遺失必要的資料行（例如）。 不過，SQL 輸出中的條件約束違規需要 i/o 要求。

有幾個資料錯誤只能在呼叫輸出接收器之後偵測到，這可能會使處理速度變慢。 若要解決此問題，請變更作業的設定或造成資料錯誤的查詢。

### <a name="outputdataconversionerrorrequiredcolumnmissing"></a>OutputDataConversionError.RequiredColumnMissing

* 原因：輸出所需的資料行不存在。 例如，定義為 Azure 資料表 PartitionKey 不的資料行存在。
* 提供的入口網站通知：是
* 資源記錄層級：警告
* 影響：系統會根據 [輸出資料原則](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) 設定來處理所有輸出資料轉換錯誤，包括遺漏必要的資料行。
* 記錄詳細資料
   * 資料行的名稱，以及記錄識別碼或記錄的一部分。

**錯誤訊息**

```json
"Message": "The output record does not contain primary key property: [deviceId] Ensure the query output contains the column [deviceId] with a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorcolumnnameinvalid"></a>OutputDataConversionError.ColumnNameInvalid

* 原因：資料行值不符合輸出。 例如，資料行名稱不是有效的 Azure 資料表資料行。
* 提供的入口網站通知：是
* 資源記錄層級：警告
* 影響：系統會根據 [輸出資料原則](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) 設定來處理所有輸出資料轉換錯誤，包括不正確資料行名稱。
* 記錄詳細資料
   * 資料行的名稱，以及記錄識別碼或記錄的一部分。

**錯誤訊息**

```json
"Message": "Invalid property name #deviceIdValue. Please refer MSDN for Azure table property naming convention."
```

### <a name="outputdataconversionerrortypeconversionerror"></a>OutputDataConversionError.TypeConversionError

* 原因：無法將資料行轉換成輸出中的有效類型。 例如，資料行的值與 SQL 資料表中定義的條件約束或類型不相容。
* 提供的入口網站通知：是
* 資源記錄層級：警告
* 影響：所有輸出資料轉換錯誤（包括類型轉換錯誤）都會根據 [輸出資料原則](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) 設定來處理。
* 記錄詳細資料
   * 資料行的名稱。
   * 記錄識別碼或記錄的一部分。

**錯誤訊息**

```json
"Message": "The column [id] value null or its type is invalid. Ensure to provide a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorrecordexceededsizelimit"></a>OutputDataConversionError.RecordExceededSizeLimit

* 原因：訊息的值大於支援的輸出大小。 例如，事件中樞輸出的記錄大於 1 MB。
* 提供的入口網站通知：是
* 資源記錄層級：警告
* 影響：所有輸出資料轉換錯誤（包括記錄超過大小限制）都會根據 [輸出資料原則](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) 設定來處理。
* 記錄詳細資料
   * 記錄識別碼或記錄的一部分。

**錯誤訊息**

```json
"BriefMessage": "Single output event exceeds the maximum message size limit allowed (262144 bytes) by Event Hub."
```

### <a name="outputdataconversionerrorduplicatekey"></a>OutputDataConversionError.DuplicateKey

* 原因：記錄已包含與系統資料行同名的資料行。 例如，當 ID 資料行指向不同的資料行時，CosmosDB 的輸出會有一個名為 ID 的資料行。
* 提供的入口網站通知：是
* 資源記錄層級：警告
* 影響：所有輸出資料轉換錯誤（包括重複的索引鍵）都會根據 [輸出資料原則](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) 設定來處理。
* 記錄詳細資料
   * 資料行的名稱。
   * 記錄識別碼或記錄的一部分。

```json
"BriefMessage": "Column 'devicePartitionKey' is being mapped to multiple columns."
```

## <a name="next-steps"></a>後續步驟

* [使用析診斷記錄對 Azure 串流分進行疑難排解](stream-analytics-job-diagnostic-logs.md)

* [了解串流分析工作監視功能，以及如何監視查詢](stream-analytics-monitoring.md)
