---
title: Azure 串流分析診斷紀錄資料錯誤
description: 本文介紹了使用 Azure 流分析時可能出現的不同輸入和輸出數據錯誤。
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 5457308d577b95201fa31bfad0a6634a7a79eda3
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398123"
---
# <a name="azure-stream-analytics-data-errors"></a>Azure 串流分析資料錯誤

數據錯誤是處理數據時發生的錯誤。  這些錯誤最常發生在數據去序列化、序列化和寫入操作期間。  當發生數據錯誤時,流分析會將詳細資訊和示例事件寫入診斷日誌。  在某些情況下,此資訊的摘要也通過門戶通知提供。

本文概述了輸入和輸出數據錯誤的不同錯誤類型、原因和診斷日誌詳細資訊。

## <a name="diagnostic-log-schema"></a>診斷記錄結構描述

請參閱[使用診斷日誌對 Azure 流分析進行故障排除](stream-analytics-job-diagnostic-logs.md#diagnostics-logs-schema),以查看診斷日誌的架構。 以下 JSON 是診斷日誌中數據錯誤的 **「屬性」** 欄位的範例值。

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

### <a name="inputdeserializererrorinvalidcompressiontype"></a>輸入反序列化器錯誤.無效壓縮類型

* 原因:所選的輸入壓縮類型與數據不匹配。
* 提供門戶通知:是
* 診斷紀錄等級:警告
* 影響:具有任何反序列化錯誤(包括無效壓縮類型)的消息將從輸入中刪除。
* 記錄詳細資料
   * 輸入消息標識碼。 對於事件中心,標識符是分區 Id、偏移和序列號。

**錯誤訊息**

```json
"BriefMessage": "Unable to decompress events from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please ensure compression setting fits the data being processed."
```

### <a name="inputdeserializererrorinvalidheader"></a>輸入反序列化器錯誤.不合法的標題

* 原因:輸入數據的標頭無效。 例如,CSV 具有具有重複名稱的列。
* 提供門戶通知:是
* 診斷紀錄等級:警告
* 影響:具有任何反序列化錯誤(包括無效標頭)的消息將從輸入中刪除。
* 記錄詳細資料
   * 輸入消息標識碼。 
   * 實際有效負載高達幾千位元組。

**錯誤訊息**

```json
"BriefMessage": "Invalid CSV Header for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please make sure there are no duplicate field names."
```

### <a name="inputdeserializererrormissingcolumns"></a>輸入序列化器錯誤.缺少欄位

* 原因:使用"創建表"或通過時間點 BY 定義的輸入列不存在。
* 提供門戶通知:是
* 診斷紀錄等級:警告
* 影響:缺少列的事件將從輸入中刪除。
* 記錄詳細資料
   * 輸入消息標識碼。 
   * 缺少的列的名稱。 
   * 實際有效負載高達幾千位元組。

**錯誤訊息**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Missing fields specified in query or in create table. Fields expected:ColumnA Fields found:ColumnB"
```

### <a name="inputdeserializererrortypeconversionerror"></a>輸入反序列化器錯誤.類型轉換錯誤

* 原因:無法將輸入轉換為 CREATE TABLE 語句中指定的類型。
* 提供門戶通知:是
* 診斷紀錄等級:警告
* 影響:具有類型轉換錯誤的事件從輸入中刪除。
* 記錄詳細資料
   * 輸入消息標識碼。 
   * 列的名稱和預期類型。

**錯誤訊息**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource '''https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Unable to convert column: dateColumn to expected type."
```

### <a name="inputdeserializererrorinvaliddata"></a>輸入序列化器錯誤.無效資料

* 原因:輸入數據格式不正確。 例如,輸入無效的 JSON。
* 提供門戶通知:是
* 診斷紀錄等級:警告
* 影響:遇到無效數據錯誤后消息中的所有事件都從輸入中刪除。
* 記錄詳細資料
   * 輸入消息標識碼。 
   * 實際有效負載高達幾千位元組。

**錯誤訊息**

```json
"BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

```json
"Message": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

### <a name="invalidinputtimestamp"></a>不合法輸入時間

* 原因:時間 STAMP BY 運算式值無法轉換為日期時間。
* 提供門戶通知:是
* 診斷紀錄等級:警告
* 影響:具有無效輸入時間戳的事件從輸入中刪除。
* 記錄詳細資料
   * 輸入消息標識碼。 
   * 錯誤訊息。 
   * 實際有效負載高達幾千位元組。

**錯誤訊息**

```json
"BriefMessage": "Unable to get timestamp for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' due to error 'Cannot convert string to datetime'"
```

### <a name="invalidinputtimestampkey"></a>不合法輸入時間戳鍵

* 原因:時間戳列的時間戳值為 NULL。
* 提供門戶通知:是
* 診斷紀錄等級:警告
* 影響:使用無效輸入時間戳鍵的事件從輸入中刪除。
* 記錄詳細資料
   * 實際有效負載高達幾千位元組。

**錯誤訊息**

```json
"BriefMessage": "Unable to get value of TIMESTAMP BY OVER COLUMN"
```

### <a name="lateinputevent"></a>後輸入事件

* 原因:申請時間和到達時間之間的差異大於延遲到達容差視窗。
* 提供門戶通知:否
* 診斷紀錄等級:資訊
* 影響:根據作業配置的事件排序部分中的"處理其他事件"設置處理後期輸入事件。 有關詳細資訊,請參閱[時間處理策略](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)。
* 記錄詳細資料
   * 申請時間和到達時間。 
   * 實際有效負載高達幾千位元組。

**錯誤訊息**

```json
"BriefMessage": "Input event with application timestamp '2019-01-01' and arrival time '2019-01-02' was sent later than configured tolerance."
```

### <a name="earlyinputevent"></a>早期輸入事件

* 原因:應用程式時間和到達時間之間的差異大於 5 分鐘。
* 提供門戶通知:否
* 診斷紀錄等級:資訊
* 影響:根據作業配置的事件排序部分中的"處理其他事件"設置處理早期輸入事件。 有關詳細資訊,請參閱[時間處理策略](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)。
* 記錄詳細資料
   * 申請時間和到達時間。 
   * 實際有效負載高達幾千位元組。

**錯誤訊息**

```json
"BriefMessage": "Input event arrival time '2019-01-01' is earlier than input event application timestamp '2019-01-02' by more than 5 minutes."
```

### <a name="outoforderevent"></a>訂單外事件

* 原因:根據定義的順序外容差視窗,事件被視為順序不一致。
* 提供門戶通知:否
* 診斷紀錄等級:資訊
* 影響:根據作業配置的事件排序部分中的"處理其他事件"設置處理順序錯誤事件。 有關詳細資訊,請參閱[時間處理策略](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)。
* 記錄詳細資料
   * 實際有效負載高達幾千位元組。

**錯誤訊息**

```json
"Message": "Out of order event(s) received."
```

## <a name="output-data-errors"></a>輸出資料錯誤

### <a name="outputdataconversionerrorrequiredcolumnmissing"></a>輸出資料轉換錯誤.必要的欄位

* 原因:輸出所需的列不存在。 例如,定義為 Azure 表分區鍵的列不存在。
* 提供門戶通知:是
* 診斷紀錄等級:警告
* 影響:所有輸出數據轉換錯誤(包括缺少所需列)都根據[輸出數據策略](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)設置進行處理。
* 記錄詳細資料
   * 列的名稱和記錄標識碼或記錄的一部分。

**錯誤訊息**

```json
"Message": "The output record does not contain primary key property: [deviceId] Ensure the query output contains the column [deviceId] with a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorcolumnnameinvalid"></a>輸出資料轉換錯誤.欄位無效

* 原因:列值與輸出不一致。 例如,列名稱不是有效的 Azure 表列。
* 提供門戶通知:是
* 診斷紀錄等級:警告
* 影響:所有輸出數據轉換錯誤(包括無效的列名稱)都根據[輸出數據策略](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)設置進行處理。
* 記錄詳細資料
   * 列的名稱和記錄標識碼或記錄的一部分。

**錯誤訊息**

```json
"Message": "Invalid property name #deviceIdValue. Please refer MSDN for Azure table property naming convention."
```

### <a name="outputdataconversionerrortypeconversionerror"></a>輸出資料轉換錯誤.類型轉換錯誤

* 原因:列無法轉換為輸出中的有效類型。 例如,列的值與 SQL 表中定義的約束或類型不相容。
* 提供門戶通知:是
* 診斷紀錄等級:警告
* 影響:所有輸出數據轉換錯誤(包括類型轉換錯誤)都根據[輸出數據策略](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)設置進行處理。
* 記錄詳細資料
   * 資料行的名稱。
   * 記錄標識碼或記錄的一部分。

**錯誤訊息**

```json
"Message": "The column [id] value null or its type is invalid. Ensure to provide a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorrecordexceededsizelimit"></a>輸出資料轉換錯誤.記錄超出大小限制

* 原因:消息的值大於支援的輸出大小。 例如,事件中心輸出的記錄大於 1 MB。
* 提供門戶通知:是
* 診斷紀錄等級:警告
* 影響:所有輸出數據轉換錯誤(包括超出大小限制的記錄)都根據[輸出數據策略](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)設置進行處理。
* 記錄詳細資料
   * 記錄標識碼或記錄的一部分。

**錯誤訊息**

```json
"BriefMessage": "Single output event exceeds the maximum message size limit allowed (262144 bytes) by Event Hub."
```

### <a name="outputdataconversionerrorduplicatekey"></a>輸出資料轉換錯誤.重複金鑰

* 原因:記錄已包含與系統列同名的列。 例如,當 ID 列位於其他列時,CosmosDB 輸出的列名為 ID。
* 提供門戶通知:是
* 診斷紀錄等級:警告
* 影響:所有輸出數據轉換錯誤(包括重複的密鑰)都根據[輸出數據策略](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)設置進行處理。
* 記錄詳細資料
   * 資料行的名稱。
   * 記錄標識碼或記錄的一部分。

```json
"BriefMessage": "Column 'devicePartitionKey' is being mapped to multiple columns."
```

## <a name="next-steps"></a>後續步驟

* [使用析診斷記錄對 Azure 串流分進行疑難排解](stream-analytics-job-diagnostic-logs.md)

* [了解串流分析工作監視功能，以及如何監視查詢](stream-analytics-monitoring.md)
