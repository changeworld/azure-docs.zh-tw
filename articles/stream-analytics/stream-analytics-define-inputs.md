---
title: 將資料作為輸入串流處理至 Azure 串流分析中
description: 了解如何設定 Azure 串流分析中的資料連線。 輸入包括來自事件的資料流，以及參考資料。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2020
ms.openlocfilehash: 649abf6d07a95c7f20f6416f7d3155f8d115782b
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93127564"
---
# <a name="stream-data-as-input-into-stream-analytics"></a>將資料作為輸入串流處理至串流分析中

串流分析與 Azure 資料流做了絕佳的整合，並透過三種資源將其作為輸入：

- [Azure 事件中樞](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT 中心](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob 儲存體](https://azure.microsoft.com/services/storage/blobs/) 
- [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) \(部分機器翻譯\) 

這些輸入來源可存在於與串流分析作業相同的 Azure 訂用帳戶中，也可存在於不同的訂用帳戶。

### <a name="compression"></a>壓縮

串流分析支援跨所有資料流輸入來源的壓縮。 支援的壓縮類型包括：無、GZip 及 Deflate 壓縮。 支援壓縮無法用於參考資料。 如果輸入格式為已壓縮的 Avro 資料，資料將以透明的方式處理。 您不需要使用 Avro 序列化來指定壓縮類型。 

## <a name="create-edit-or-test-inputs"></a>建立、編輯或測試輸入

您可使用 [Azure 入口網站](stream-analytics-quick-create-portal.md)、[Visual Studio](stream-analytics-quick-create-vs.md) 和 [Visual Studio Code](quick-create-visual-studio-code.md) 來新增及檢視或編輯串流作業上的現有輸入。 您也可以從 Azure 入口網站、[Visual Studio](stream-analytics-vs-tools-local-run.md) 和 [Visual Studio Code](visual-studio-code-local-run.md) 以透過範例資料來測試輸入連線並[測試查詢](stream-analytics-manage-job.md#test-your-query)。 當撰寫查詢時，您會在 FROM 子句中列出輸入。 您可以從入口網站的 [查詢] 頁面取得可用輸入的清單。 如果您想要使用多個輸入，則可 `JOIN` 它們或撰寫多個 `SELECT` 查詢。


## <a name="stream-data-from-event-hubs"></a>來自事件中樞的串流資料

Azure 事件中樞提供可高度擴充的發佈-訂閱事件投資人。 事件中樞每秒可收集數百萬個事件，因此其可供處理和分析連線的裝置和應用程式所產生大量資料。 事件中樞和串流分析搭配在一起，可提供即時分析所需的端對端解決方案。 事件中樞可讓您即時將事件饋送至 Azure，而串流分析作業則可即時處理這些事件。 例如，您可以將網頁點擊、感應器讀數或線上記錄事件傳送至事件中樞。 然後，您可以建立串流分析作業，將事件中樞當作輸入資料流來即時篩選、彙總和相互關聯。

`EventEnqueuedUtcTime` 是事件在事件中樞的抵達時間戳記，也是事件從事件中樞到達串流分析的預設時間戳記。 若要使用事件裝載中的時間戳記，將資料當作資料流處理，您必須使用 [TIMESTAMP BY](/stream-analytics-query/timestamp-by-azure-stream-analytics) 關鍵字。

### <a name="event-hubs-consumer-groups"></a>事件中樞取用者群組

您應該將每一個串流分析事件中樞輸入設定為有自己的取用者群組。 當作業包含自我聯結或有多個輸入時，某些輸入可能由下游的多個讀取器所讀取。 這種情況會影響單一取用者群組中的讀取器數目。 若要避免超出每個分割區的每個取用者群組最多有 5 個讀取器的事件中樞限制，最好為每個串流分析作業指定取用者群組。 另外也限制標準層事件中樞最多有 20 個取用者群組。 如需詳細資訊，請參閱[對 Azure 串流分析輸入進行疑難排解](stream-analytics-troubleshoot-input.md)。

### <a name="create-an-input-from-event-hubs"></a>從事件中樞建立輸入

下表說明 Azure 入口網站的 [新的輸入] 頁面中用來從事件中樞串流處理資料輸入的每個屬性：

| 屬性 | 描述 |
| --- | --- |
| **輸入別名** |在作業查詢中用來參考這個輸入的易記名稱。 |
| **訂用帳戶** | 選擇事件中樞資源所在的訂用帳戶。 | 
| **事件中樞命名空間** | 事件中樞命名空間是一組訊息實體的容器。 在建立新的事件中樞時，也會建立此命名空間。 |
| **事件中樞名稱** | 作為輸入的事件中樞名稱。 |
| **事件中樞原則名稱** | 支援存取事件中樞的共用存取原則。 每一個共用存取原則都會有名稱、權限 (由您設定) 和存取金鑰。 此選項會自動填入，除非您選取手動提供事件中樞設定的選項。|
| **事件中樞取用者群組** (建議使用) | 強烈建議您為每一個串流分析作業使用不同的取用者群組。 此字串可識別要用來從事件中樞擷取資料的取用者群組。 若未指定取用者群組，串流分析作業會使用 $Default 取用者群組。  |
| **分割區索引鍵** | 這是選擇性欄位，只有當您的作業設定為使用 [相容性層級](./stream-analytics-compatibility-level.md) 1.2 或更高版本時，才能使用此欄位。 如果您的輸入是依屬性進行分割，您可以在這裡加入這個屬性的名稱。 如果您的查詢包含這個屬性的 PARTITION BY 或 GROUP BY 子句，就會使用這項功能來改善查詢的效能。 如果此作業使用相容性層級1.2 或更高版本，此欄位預設為 "PartitionId"。 |
| **事件序列化格式** | 傳入資料流的序列化格式 (JSON、CSV、Avro 或[其他 (Protobuf、XML、專用...)](custom-deserializer.md))。  確認 JSON 格式與規格一致，並且不包含以 0 開頭的十進位數字。 |
| **編碼方式** | UTF-8 是目前唯一支援的編碼格式。 |
| **事件壓縮類型** | 用來讀取內送資料流的壓縮類型，例如無 (預設值)、GZip 或 Deflate。 |

如果您的資料來自事件中樞資料流輸入，您將可在串流分析查詢中存取下列中繼資料欄位：

| 屬性 | 描述 |
| --- | --- |
| **EventProcessedUtcTime** |資料流分析處理事件的日期與時間。 |
| **EventEnqueuedUtcTime** |事件中樞收到事件的日期與時間。 |
| **PartitionId** |輸入配接器以零起始的資料分割識別碼。 |

例如，您可以使用這些欄位來撰寫類似下列範例的查詢：

```sql
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
```

> [!NOTE]
> 將事件中樞作為 IoT 中樞路由的端點時，您可以使用 [GetMetadataPropertyValue 函式](/stream-analytics-query/getmetadatapropertyvalue)存取 IoT 中樞中繼資料。
> 

## <a name="stream-data-from-iot-hub"></a>來自 IoT 中樞的串流資料

Azure IoT 中樞是可高度調整的發佈/訂閱事件擷取器，已針對 IoT 案例最佳化。

在串流分析中，來自 IoT 中樞之事件的預設時間戳記是事件抵達 IoT 中樞的時間戳記，也就是 `EventEnqueuedUtcTime`。 若要使用事件裝載中的時間戳記，將資料當作資料流處理，您必須使用 [TIMESTAMP BY](/stream-analytics-query/timestamp-by-azure-stream-analytics) 關鍵字。

### <a name="iot-hub-consumer-groups"></a>IoT 中樞取用者群組

您應將每個串流分析 IoT 中樞輸入設定為有其本身的取用者群組。 當作業包含自我聯結或有多個輸入時，某些輸入可能由下游的多個讀取器所讀取。 這種情況會影響單一取用者群組中的讀取器數目。 若要避免超出每個分割區的每個取用者群組最多有 5 個讀取器的 Azure IoT 中樞限制，最好為每個串流分析作業指定取用者群組。

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>將 IoT 中樞設定為資料流輸入

下表說明在 Azure 入口網站中將 IoT 中樞設定為資料流輸入時，[新的輸入] 頁面中的每個屬性。

| 屬性 | 描述 |
| --- | --- |
| **輸入別名** | 在作業查詢中用來參考這個輸入的易記名稱。|
| **訂用帳戶** | 選擇 IoT 中樞資源所在的訂用帳戶。 | 
| **IoT 中心** | 要作為輸入的 IoT 中樞的名稱。 |
| **端點** | IoT 中樞的端點。|
| **共用存取原則名稱** | 支援存取 IoT 中樞的共用存取原則。 每一個共用存取原則都會有名稱、權限 (由您設定) 和存取金鑰。 |
| **共用存取原則金鑰** | 用來授與 IoT 中樞存取權的共用存取金鑰。  此選項會自動填入，除非您選取手動提供 IoT 中樞設定的選項。 |
| **取用者群組** | 強烈建議讓每個串流分析作業使用不同的取用者群組。 用來從 IoT 中樞擷取資料的取用者群組。 串流分析會使用 $Default 取用者群組，除非您另有指定。  |
| **分割區索引鍵** | 這是選擇性欄位，只有當您的作業設定為使用 [相容性層級](./stream-analytics-compatibility-level.md) 1.2 或更高版本時，才能使用此欄位。 如果您的輸入是依屬性進行分割，您可以在這裡加入這個屬性的名稱。 如果您的查詢包含這個屬性的 PARTITION BY 或 GROUP BY 子句，就會使用這項功能來改善查詢的效能。 如果此作業使用相容性層級1.2 或更高版本，此欄位預設為 "PartitionId"。 |
| **事件序列化格式** | 傳入資料流的序列化格式 (JSON、CSV、Avro 或[其他 (Protobuf、XML、專用...)](custom-deserializer.md))。  確認 JSON 格式與規格一致，並且不包含以 0 開頭的十進位數字。 |
| **編碼方式** | UTF-8 是目前唯一支援的編碼格式。 |
| **事件壓縮類型** | 用來讀取內送資料流的壓縮類型，例如無 (預設值)、GZip 或 Deflate。 |


當您使用來自 IoT 中樞的串流資料時，您將可在串流分析查詢中存取下列中繼資料欄位：

| 屬性 | 描述 |
| --- | --- |
| **EventProcessedUtcTime** | 處理事件的日期與時間。 |
| **EventEnqueuedUtcTime** | IoT 中心收到事件的日期與時間。 |
| **PartitionId** | 輸入配接器以零起始的資料分割識別碼。 |
| **IoTHub.MessageId** | IoT 中樞內用於雙向通訊相互關聯的識別碼。 |
| **IoTHub.CorrelationId** | IoT 中樞內用於訊息回應和回饋的識別碼。 |
| **IoTHub.ConnectionDeviceId** | 用來傳送此訊息的驗證識別碼。 IoT 中樞會將服務繫結訊息標上此值。 |
| **IoTHub.ConnectionDeviceGenerationId** | 用來傳送此訊息之已驗證裝置的世代識別碼。 IoT 中樞會將服務繫結訊息標上此值。 |
| **IoTHub.EnqueuedTime** | IoT 中樞收到訊息的時間。 |


## <a name="stream-data-from-blob-storage-or-data-lake-storage-gen2"></a>從 Blob 儲存體或 Data Lake Storage Gen2 串流資料
針對有大量非結構化資料儲存在雲端中的案例，Azure Blob 儲存體或 Azure Data Lake Storage Gen2 (ADLS Gen2) 提供符合成本效益且可調整的解決方案。 Blob 儲存體或 ADLS Gen2 中的資料通常會被視為待用資料;不過，串流分析可將此資料當作資料流程來處理。 

記錄處理是在串流分析中使用這類輸入的常用案例。 在此案例中，從系統擷取遙測資料檔案之後，必須加以剖析和處理，才能得到有意義的資料。

在串流分析中，Blob 儲存體或 ADLS Gen2 事件的預設時間戳記是上次修改的時間戳記，也就是 `BlobLastModifiedUtcTime` 。 如果 blob 已上傳至13:00 的儲存體帳戶，且 Azure 串流分析作業已使用 *現在* 的選項（在13:01 開始）開始，則不會在其修改的時間落在作業執行期間之外挑選。

如果在 13:00 將 blob 上傳至儲存體帳戶容器，然後在 13:00 (含) 以前使用 [自訂時間] 啟動 Azure 串流分析作業，則會選取該 blob，因為其修改時間落在作業執行期間以內。

如果在 13:00 使用 [現在] 來啟動 Azure 串流分析作業，然後在 13:01 將 blob 上傳至儲存體帳戶容器，則 Azure 串流分析會選取該 blob。 指派給每個 blob 的時間戳記只會依據 `BlobLastModifiedTime`。 blob 所在的資料夾與指派的時間戳記無關。 例如，如果有一個 blob *2019/10-01/00/b1.txt* ，其 `BlobLastModifiedTime` 為 2019-11-11，則指派給此 blob 的時間戳記為 2019-11-11。

若要使用事件裝載中的時間戳記，將資料當作資料流處理，您必須使用 [TIMESTAMP BY](/stream-analytics-query/stream-analytics-query-language-reference) 關鍵字。 如果 Blob 檔案可供使用，串流分析作業會從 Azure Blob 儲存體提取資料，或每秒 ADLS Gen2 輸入。 如果 Blob 檔案無法使用，則會執行時間延遲上限為 90 秒的指數輪詢。

CSV 格式的輸入需要以標頭資料列來定義資料集的欄位，且所有標頭資料列欄位都必須是唯一的。

> [!NOTE]
> 串流分析不支援將內容加入現有的 blob 檔案。 串流分析只會檢視每個檔案一次，在作業讀取資料之後，不會處理檔案中發生的任何變更。 最佳做法是一次上傳 blob 檔案的所有資料，然後將其他較新的事件新增到不同的新 blob 檔案。

在許多 blob 持續新增的情況下，串流分析會在新增 blob 時進行處理，在罕見的情況下可能會因為的資料細微性而略過某些 blob `BlobLastModifiedTime` 。 您可在上傳每個 blob 之間至少隔兩秒來減輕此問題。 如果此選項不可行，則可使用事件中樞來串流大量事件。

### <a name="configure-blob-storage-as-a-stream-input"></a>將 Blob 儲存體設定為資料流輸入 

下表說明在 Azure 入口網站中將 Blob 儲存體設定為資料流輸入時，[新的輸入] 頁面中的每個屬性。

| 屬性 | 描述 |
| --- | --- |
| **輸入別名** | 在作業查詢中用來參考這個輸入的易記名稱。 |
| **訂用帳戶** | 選擇儲存體資源所在的訂用帳戶。 | 
| **儲存體帳戶** | Blob 檔案所在的儲存體帳戶名稱。 |
| **儲存體帳戶金鑰** | 與儲存體帳戶相關聯的密碼金鑰。 此選項會自動填入，除非您選取手動提供設定的選項。 |
| **容器** | 容器提供 blob 的邏輯群組。 您可以選擇 **使用現有的** 容器，或選擇 **新建** 以使用新建立的容器。|
| **路徑模式** (選用) | 用來在指定的容器中找出 blob 的檔案路徑。 如果您想要從容器的根目錄讀取 blob，請勿設定路徑模式。 在該路徑內，您可以指定下列三個變數的一個或多個執行個體：`{date}`、`{time}` 或 `{partition}`<br/><br/>範例 1：`cluster1/logs/{date}/{time}/{partition}`<br/><br/>範例 2：`cluster1/logs/{date}`<br/><br/>`*` 字元不是路徑前置詞允許的值。 僅允許有效的 <a HREF="/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata">Azure blob 字元</a>。 請勿包含容器名稱或檔案名稱。 |
| **日期格式** (選用) | 在路徑中使用日期變數時，用來組織檔案的日期格式。 範例： `YYYY/MM/DD` <br/><br/> 當 blob 輸入在其路徑中有 `{date}` 或 `{time}` 時，則會以遞增的時間順序來查看資料夾。|
| **時間格式** (選用) |  在路徑中使用時間變數時，用來組織檔案的時間格式。 目前唯一支援的值為 `HH` (表示小時)。 |
| **分割區索引鍵** | 這是選擇性欄位，只有當您的作業設定為使用 [相容性層級](./stream-analytics-compatibility-level.md) 1.2 或更高版本時，才能使用此欄位。 如果您的輸入是依屬性進行分割，您可以在這裡加入這個屬性的名稱。 如果您的查詢包含這個屬性的 PARTITION BY 或 GROUP BY 子句，就會使用這項功能來改善查詢的效能。 如果此作業使用相容性層級1.2 或更高版本，此欄位預設為 "PartitionId"。 |
| **輸入磁碟分割的計數** | 只有當路徑模式中有 {partition} 時，此欄位才會出現。 這個屬性的值是整數 >= 1。 在 pathPattern 中出現 {partition} 的任何地方，將會使用介於0和這個欄位1值之間的數位-1。 |
| **事件序列化格式** | 傳入資料流的序列化格式 (JSON、CSV、Avro 或[其他 (Protobuf、XML、專用...)](custom-deserializer.md))。  確認 JSON 格式與規格一致，並且不包含以 0 開頭的十進位數字。 |
| **編碼方式** | 對於 CSV 和 JSON 而言，UTF-8 是目前唯一支援的編碼格式。 |
| **壓縮** | 用來讀取內送資料流的壓縮類型，例如無 (預設值)、GZip 或 Deflate。 |

當您的資料來自 Blob 儲存體來源時，您可以在串流分析查詢中存取下列中繼資料欄位：

| 屬性 | 描述 |
| --- | --- |
| **BlobName** |事件來源的輸入 Blob 名稱。 |
| **EventProcessedUtcTime** |資料流分析處理事件的日期與時間。 |
| **BlobLastModifiedUtcTime** |上次修改 Blob 的時間與日期。 |
| **PartitionId** |輸入配接器以零起始的資料分割識別碼。 |

例如，您可以使用這些欄位來撰寫類似下列範例的查詢：

```sql
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
```

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [快速入門：使用 Azure 入口網站建立串流分析作業](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/