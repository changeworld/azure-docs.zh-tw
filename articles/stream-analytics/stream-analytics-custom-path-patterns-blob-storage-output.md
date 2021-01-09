---
title: Azure 串流分析自訂 Blob 輸出資料分割
description: 本文說明 Azure 串流分析作業的 Blob 儲存體輸出所適用的自訂日期時間路徑模式與自訂欄位或屬性功能。
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/15/2020
ms.custom: seodec18
ms.openlocfilehash: cb9d8edd24dcc8809f2b207a4db80653b0e140e4
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98014031"
---
# <a name="azure-stream-analytics-custom-blob-output-partitioning"></a>Azure 串流分析自訂 Blob 輸出資料分割

Azure 串流分析支援使用自訂欄位或屬性以及自訂日期時間路徑模式的自訂 Blob 輸出資料分割。 

## <a name="custom-field-or-attributes"></a>自訂欄位或屬性

自訂欄位或輸入屬性透過允許更充分掌控輸出，來改進下游資料處理和報告工作流程。

### <a name="partition-key-options"></a>分割區索引鍵選項

用來分割輸入資料的分割區索引鍵或資料行名稱可能包含任何可接受的 [blob 名稱](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)字元。 除非搭配別名使用，否則無法將嵌套欄位當做分割區索引鍵使用，但您可以使用某些字元來建立檔案的階層。 例如，您可以使用下列查詢來建立結合兩個其他資料行資料的資料行，以建立唯一的資料分割索引鍵。

```sql
SELECT name, id, CONCAT(name, "/", id) AS nameid
```

分割區索引鍵必須是 NVARCHAR (MAX) 、BIGINT、FLOAT 或 BIT (1.2 相容性層級或更高的) 。 不支援 DateTime、Array 和記錄類型，但如果將它們轉換成字串，就可以用來做為資料分割索引鍵。 如需詳細資訊，請參閱 [Azure 串流分析資料類型](/stream-analytics-query/data-types-azure-stream-analytics)。

### <a name="example"></a>範例

假設作業會從連接到視訊遊戲服務的即時使用者工作階段取得輸入資料，其中所擷取的資料包含資料行 **client_id** 以識別工作階段。 若要依 **client_id** 對資料進行分割，請在建立作業時將 [Blob 路徑模式] 欄位設定為在 Blob 輸出屬性中包含分割區權杖 **{client_id}** 。 由於具有各種 **client_id** 值的資料流經串流分析作業，因此輸出資料將根據每個文件夾的單一 **client_id** 值儲存到不同的資料夾中。

![路徑模式與用戶端識別碼](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-path-pattern-client-id.png)

同樣地，如果作業輸入是來自數百萬個感應器的感應器資料，其中每個感應器具有 **sensor_id**，則路徑模式將是 **{sensor_id}** 以將每個感應器資料分割至不同的資料夾。  


使用 REST API，用於該要求的 JSON 檔案的輸出區段可能看起來如下所示：  

![REST API 輸出](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-rest-output.png)

作業開始執行後，用戶端容器可能如下所示：  

![用戶端容器](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-clients-container.png)

每個資料夾可能包含多個 Blob，其中每個 Blob 包含一或多個記錄。 在上述範例中，資料夾中有一個標示為 "06000000" 且具有下列內容的單一 blob：

![Blob 內容](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-contents.png)

請注意，Blob 中的每一筆記錄都有一個與資料夾名稱相符的 **client_id** 資料行，因為用來對輸出路徑中的輸出進行分割的資料行為 **client_id**。

### <a name="limitations"></a>限制

1. 路徑模式 Blob 輸出屬性中只允許一個自訂分割區索引鍵。 下列所有路徑模式均有效：

   * cluster1/{date}/{aFieldInMyData}  
   * cluster1/{time}/{aFieldInMyData}  
   * cluster1/{aFieldInMyData}  
   * cluster1/{date}/{time}/{aFieldInMyData} 
   
2. 分割區索引鍵不區分大小寫，因此 "John" 和 "john" 之類的分割區索引鍵是相等的。 此外，運算式不能作為分割區索引鍵。 例如， **{columnA + columnB}** 無法運作。  

3. 當輸入資料流由分割區索引鍵基數低於 8000 的記錄組成時，記錄將附加至現有的 Blob，並且僅在必要時建立新的 Blob。 如果基數超過 8000，則不保證會寫入至現有的 Blob，並且不會為具有相同分割區索引鍵的任意數目的記錄建立新的 Blob。

4. 如果 blob 輸出 [設定為不可變](../storage/blobs/storage-blob-immutable-storage.md)，則串流分析會在每次傳送資料時建立新的 blob。

## <a name="custom-datetime-path-patterns"></a>自訂日期時間路徑模式

自訂日期時間路徑模式可讓您指定與 Hive 串流慣例一致的輸出格式，讓 Azure 串流分析得以將資料傳送至 Azure HDInsight 和 Azure Databricks 以進行下游處理。 自訂日期時間路徑模式可以使用您 Blob 輸出 [路徑前置詞] 欄位中的 `datetime` 關鍵字，輕易地實作。 例如： `{datetime:yyyy}` 。

### <a name="supported-tokens"></a>支援的權杖

下列格式規範權杖可以單獨使用，或者合併使用以達成自訂日期時間格式：

|格式規範   |描述   |範例時間 2018-01-02T10:06:08 的結果|
|----------|-----------|------------|
|{datetime:yyyy}|四位數的年份|2018|
|{datetime:MM}|從 01 到 12 的月份|01|
|{datetime:M}|從 1 到 12 的月份|1|
|{datetime:dd}|從 01 到 31 的日期|02|
|{datetime:d}|從 1 到 31 的日期|2|
|{datetime:HH}|使用 24 小時格式的小時，從 00 到 23|10|
|{datetime:mm}|從 00 到 60 的分鐘數|06|
|{datetime:m}|從 0 到 60 的分鐘數|6|
|{datetime:ss}|從 00 到 60 的秒數|08|

如果您不希望使用自訂日期時間模式，可以將 {date} 和/或 {time} 權杖新增至 [路徑前置詞]，以產生具有內建日期時間格式的下拉式清單。

![串流分析舊日期時間格式](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-old-date-time-formats.png)

### <a name="extensibility-and-restrictions"></a>擴充性及限制

您可以在路徑模式中使用任意數量的權杖、`{datetime:<specifier>}`，直到達到 [路徑前置詞] 的字元限制為止。 若是單一權杖內的格式規範無法合併，應是其超過已由日期和時間下拉式清單列出的合併。 

針對 `logs/MM/dd` 的路徑分割：

|有效運算式   |無效運算式   |
|----------|-----------|
|`logs/{datetime:MM}/{datetime:dd}`|`logs/{datetime:MM/dd}`|

您可能會在 [路徑前置詞] 中使用相同的格式規範多次。 每次都必須重複權杖。

### <a name="hive-streaming-conventions"></a>Hive 串流慣例

Blob 儲存體的自訂路徑模式可與 Hive 串流慣例一起使用，該慣例預期資料夾以資料夾名稱中的 `column=` 標示。

例如： `year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}/hour={datetime:HH}` 。

自訂輸出可以免除改變資料表及手動將分割新增至 Azure 串流分析與 Hive 之間連接埠資料的麻煩。 相反地，許多資料夾可以使用以下方式自動新增：

```SQL
MSCK REPAIR TABLE while hive.exec.dynamic.partition true
```

### <a name="example"></a>範例

根據 [Azure 串流分析 Azure 入口網站](stream-analytics-quick-create-portal.md) 快速入門手冊，建立儲存體帳戶、資源群組、串流分析作業，以及輸入來源。 使用在快速入門指南中使用的相同範例資料，也可以在 [GitHub](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json) 上取得。

以下列組態建立 Blob 輸出接收：

![串流分析會建立 Blob 輸出接收](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-create-output-sink.png)

完整路徑模式如下所示：


`year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}`


當您啟動作業時，以路徑模式為基礎的資料夾結構會在 Blob 容器中建立。 您可以向下切入至日層級。

![具有自訂路徑模式的串流分析 Blob 輸出](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-output-folder-structure.png)

## <a name="next-steps"></a>後續步驟

* [了解來自 Azure 串流分析的輸出](stream-analytics-define-outputs.md)
