---
title: 將資料作為輸入串流處理至 Azure 串流分析中
description: 了解如何設定 Azure 串流分析中的資料連線。 輸入包括來自事件的資料流，以及參考資料。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 388f43fec9242f6a4b448483d9486aa4413d2612
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254465"
---
# <a name="stream-data-as-input-into-stream-analytics"></a>將資料作為輸入串流處理至串流分析中

串流分析與 Azure 資料流做了絕佳的整合，並透過三種資源將其作為輸入：

- [Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT 中心](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob 儲存體](https://azure.microsoft.com/services/storage/blobs/) 

這些輸入來源可存在於與串流分析作業相同的 Azure 訂用帳戶中，也可存在於不同的訂用帳戶。

### <a name="compression"></a>壓縮

串流分析支援跨所有資料流輸入來源的壓縮。 支援的壓縮類型包括：無壓縮、GZip 和 Deflate 壓縮。 支援壓縮無法用於參考資料。 如果輸入格式為已壓縮的 Avro 資料，資料將以透明的方式處理。 您不需要使用 Avro 序列化來指定壓縮類型。 

## <a name="create-edit-or-test-inputs"></a>建立、編輯或測試輸入

您可以使用 Azure[門戶](stream-analytics-quick-create-portal.md)、[視覺化工作室](stream-analytics-quick-create-vs.md)和[視覺化工作室代碼](quick-create-vs-code.md)在流作業上添加和查看或編輯現有輸入。 還可以從 Azure 門戶、[視覺化工作室](stream-analytics-vs-tools-local-run.md)和[視覺化工作室代碼](visual-studio-code-local-run.md)中的示例資料測試輸入連接和[測試查詢](stream-analytics-manage-job.md#test-your-query)。 編寫查詢時，在 FROM 子句中列出輸入。 您可以從入口網站的 [查詢]**** 頁面取得可用輸入的清單。 如果您想要使用多個輸入，則可 `JOIN` 它們或撰寫多個 `SELECT` 查詢。


## <a name="stream-data-from-event-hubs"></a>來自事件中樞的串流資料

Azure 事件中樞提供高延展性的發佈-訂閱事件擷取器。 事件中心每秒可以收集數百萬個事件，以便您可以處理和分析連接的設備和應用程式產生的大量資料。 事件中樞和串流分析搭配在一起，可提供即時分析所需的端對端解決方案。 事件中樞可讓您即時將事件饋送至 Azure，而串流分析作業則可即時處理這些事件。 例如，您可以將網頁點擊、感應器讀數或線上記錄事件傳送至事件中樞。 然後，您可以建立串流分析作業，將事件中樞當作輸入資料流來即時篩選、彙總和相互關聯。

`EventEnqueuedUtcTime` 是事件在事件中樞的抵達時間戳記，也是事件從事件中樞到達串流分析的預設時間戳記。 若要使用事件裝載中的時間戳記，將資料當作資料流處理，您必須使用 [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) 關鍵字。

### <a name="event-hubs-consumer-groups"></a>事件中心消費者組

您應該將每一個串流分析事件中樞輸入設定為有自己的取用者群組。 當作業包含自我聯結或有多個輸入時，某些輸入可能由下游的多個讀取器所讀取。 這種情況會影響單一取用者群組中的讀取器數目。 若要避免超出每個分割區的每個取用者群組最多有 5 個讀取器的事件中樞限制，最好為每個串流分析作業指定取用者群組。 標準層事件中心也有 20 個消費者組的限制。 如需詳細資訊，請參閱[對 Azure 串流分析輸入進行疑難排解](stream-analytics-troubleshoot-input.md)。

### <a name="create-an-input-from-event-hubs"></a>從事件中心創建輸入

下表解釋了 Azure 門戶中 **"新建輸入**"頁中用於從事件中心資料流資料輸入的每個屬性：

| 屬性 | 描述 |
| --- | --- |
| **輸入別名** |在作業查詢中用來參考這個輸入的易記名稱。 |
| **訂閱** | 選擇事件中樞資源所在的訂用帳戶。 | 
| **事件中心命名空間** | 事件中樞命名空間是一組訊息實體的容器。 在建立新的事件中樞時，也會建立此命名空間。 |
| **事件中樞名稱** | 作為輸入的事件中樞名稱。 |
| **事件中樞原則名稱** | 支援存取事件中樞的共用存取原則。 每一個共用存取原則都會有名稱、權限 (由您設定) 和存取金鑰。 此選項會自動填入，除非您選取手動提供事件中樞設定的選項。|
| **事件中樞取用者群組** (建議使用) | 強烈建議您為每一個串流分析作業使用不同的取用者群組。 此字串可識別要用來從事件中樞擷取資料的取用者群組。 若未指定取用者群組，串流分析作業會使用 $Default 取用者群組。  |
| **分區鍵** | 如果輸入由屬性分區，則可以添加此屬性的名稱。 分區鍵是可選的，如果包含此屬性上的分區 BY 或 GROUP BY 子句，則用於提高查詢的性能。 |
| **事件序列化格式** | 傳入資料流程的序列化格式（JSON、CSV、Avro[或其他（原型、XML、專有...）。](custom-deserializer.md)  確認 JSON 格式與規格一致，並且不包含以 0 開頭的十進位數字。 |
| **編碼** | UTF-8 是目前唯一支援的編碼格式。 |
| **事件壓縮類型** | 用來讀取內送資料流的壓縮類型，例如無 (預設值)、GZip 或 Deflate。 |

如果您的資料來自事件中樞資料流輸入，您將可在串流分析查詢中存取下列中繼資料欄位：

| 屬性 | 描述 |
| --- | --- |
| **EventProcessedUtcTime** |資料流分析處理事件的日期與時間。 |
| **EventEnqueuedUtcTime** |事件中樞收到事件的日期與時間。 |
| **分區 Id** |輸入配接器以零起始的資料分割識別碼。 |

例如，您可以使用這些欄位來撰寫類似下列範例的查詢：

```sql
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
```

> [!NOTE]
> 將事件中樞作為 IoT 中樞路由的端點時，您可以使用 [GetMetadataPropertyValue 函式](https://docs.microsoft.com/stream-analytics-query/getmetadatapropertyvalue)存取 IoT 中樞中繼資料。
> 

## <a name="stream-data-from-iot-hub"></a>來自 IoT 中樞的串流資料

Azure IoT 中心是一種高度可擴展的發佈-訂閱事件引入器，針對 IoT 方案進行了優化。

在串流分析中，來自 IoT 中樞之事件的預設時間戳記是事件抵達 IoT 中樞的時間戳記，也就是 `EventEnqueuedUtcTime`。 若要使用事件裝載中的時間戳記，將資料當作資料流處理，您必須使用 [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) 關鍵字。

### <a name="iot-hub-consumer-groups"></a>Iot 中心消費者組

您應將每個串流分析 IoT 中樞輸入設定為有其本身的取用者群組。 當作業包含自我聯結或有多個輸入時，某些輸入可能由下游的多個讀取器所讀取。 這種情況會影響單一取用者群組中的讀取器數目。 若要避免超出每個分割區的每個取用者群組最多有 5 個讀取器的 Azure IoT 中樞限制，最好為每個串流分析作業指定取用者群組。

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>將 IoT 中樞設定為資料流輸入

下表說明在 Azure 入口網站中將 IoT 中樞設定為資料流輸入時，[新的輸入]**** 頁面中的每個屬性。

| 屬性 | 描述 |
| --- | --- |
| **輸入別名** | 在作業查詢中用來參考這個輸入的易記名稱。|
| **訂閱** | 選擇 IoT 中樞資源所在的訂用帳戶。 | 
| **IoT 中樞** | 要作為輸入的 IoT 中樞的名稱。 |
| **端點** | IoT 中樞的端點。|
| **共用訪問策略名稱** | 支援存取 IoT 中樞的共用存取原則。 每一個共用存取原則都會有名稱、權限 (由您設定) 和存取金鑰。 |
| **共用訪問策略金鑰** | 用來授與 IoT 中樞存取權的共用存取金鑰。  此選項會自動填入，除非您選取手動提供 IoT 中樞設定的選項。 |
| **消費者組** | 強烈建議讓每個串流分析作業使用不同的取用者群組。 用來從 IoT 中樞擷取資料的取用者群組。 串流分析會使用 $Default 取用者群組，除非您另有指定。  |
| **分區鍵** | 如果輸入由屬性分區，則可以添加此屬性的名稱。 分區鍵是可選的，如果包含此屬性上的分區 BY 或 GROUP BY 子句，則用於提高查詢的性能。 |
| **事件序列化格式** | 傳入資料流程的序列化格式（JSON、CSV、Avro[或其他（原型、XML、專有...）。](custom-deserializer.md)  確認 JSON 格式與規格一致，並且不包含以 0 開頭的十進位數字。 |
| **編碼** | UTF-8 是目前唯一支援的編碼格式。 |
| **事件壓縮類型** | 用來讀取內送資料流的壓縮類型，例如無 (預設值)、GZip 或 Deflate。 |


當您使用來自 IoT 中樞的串流資料時，您將可在串流分析查詢中存取下列中繼資料欄位：

| 屬性 | 描述 |
| --- | --- |
| **EventProcessedUtcTime** | 處理事件的日期與時間。 |
| **EventEnqueuedUtcTime** | IoT 中心收到事件的日期與時間。 |
| **分區 Id** | 輸入配接器以零起始的資料分割識別碼。 |
| **IoTHub.MessageId** | IoT 中樞內用於雙向通訊相互關聯的識別碼。 |
| **IoTHub.CorrelationId** | IoT 中樞內用於訊息回應和回饋的識別碼。 |
| **IoTHub.ConnectionDeviceId** | 用來傳送此訊息的驗證識別碼。 IoT 中樞會將服務繫結訊息標上此值。 |
| **IoTHub.ConnectionDeviceGenerationId** | 用來傳送此訊息之已驗證裝置的世代識別碼。 IoT 中樞會將服務繫結訊息標上此值。 |
| **IoTHub.EnqueuedTime** | IoT 中樞收到訊息的時間。 |


## <a name="stream-data-from-blob-storage"></a>來自 Blob 儲存體的串流資料
在要於雲端中儲存大量非結構化資料的情節中，Azure Blob 儲存體提供具有成本效益且可擴充的解決方案。 我們通常會將 Blob 儲存體 中的資料視為待用資料，但串流分析可將 Blob 資料視為資料流來加以處理。 

記錄處理是透過串流分析使用 Blob 儲存體輸入時的常用案例。 在此案例中，從系統擷取遙測資料檔案之後，必須加以剖析和處理，才能得到有意義的資料。

在串流分析中，Blob 儲存體事件的預設時間戳記是上次修改 blob 的時間戳記，也就是 `BlobLastModifiedUtcTime`。 如果在 13：00 時將 Blob 上載到存儲帳戶，並且 Azure 流分析作業在 13：01 開始使用選項 *"現在*"，則由於 Blob 的修改時間超出作業運行期，因此不會拾取該 Blob。

如果在 13：00 時將 Blob 上載到存儲帳戶容器，並且 Azure 流分析作業在 13：00 或更早時使用*自訂時間*啟動，則當 Blob 修改的時間位於作業運行期間時，將選取該 Blob。

如果在 13：00 開始使用 *"立即"，* 並且 Blob 在 13：01 時上載到存儲帳戶容器，則 Azure 流分析將選取 Blob。

若要使用事件裝載中的時間戳記，將資料當作資料流處理，您必須使用 [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) 關鍵字。 串流分析作業在 Blob 檔案可用時，會每秒從 Azure Blob 儲存體輸入中提取資料。 如果 Blob 檔案無法使用，則會執行時間延遲上限為 90 秒的指數輪詢。

CSV 格式的輸入需要以標頭資料列來定義資料集的欄位，且所有標頭資料列欄位都必須是唯一的。

> [!NOTE]
> 串流分析不支援將內容加入現有的 blob 檔案。 串流分析只會檢視每個檔案一次，在作業讀取資料之後，不會處理檔案中發生的任何變更。 最佳做法是一次上傳 blob 檔案的所有資料，然後將其他較新的事件新增到不同的新 blob 檔案。

一次上傳大量 Blob 可能會導致流分析在極少數情況下跳過讀取幾個 blob。 建議將 Blob 上載至少 2 秒到 Blob 存儲。 如果此選項不可行，則可以使用事件中心資料流大量事件。 

### <a name="configure-blob-storage-as-a-stream-input"></a>將 Blob 儲存體設定為資料流輸入 

下表說明在 Azure 入口網站中將 Blob 儲存體設定為資料流輸入時，[新的輸入]**** 頁面中的每個屬性。

| 屬性 | 描述 |
| --- | --- |
| **輸入別名** | 在作業查詢中用來參考這個輸入的易記名稱。 |
| **訂閱** | 選擇 IoT 中樞資源所在的訂用帳戶。 | 
| **存儲帳戶** | Blob 檔案所在的儲存體帳戶名稱。 |
| **儲存體帳戶金鑰** | 與儲存體帳戶相關聯的密碼金鑰。 此選項會自動填入，除非您選取手動提供 Blob 儲存體設定的選項。 |
| **容器** | Blob 輸入的容器。 容器提供邏輯分組給儲存在 Microsoft Azure Blob 服務中的 blob。 將 blob 上傳至 Azure Blob 儲存體服務時，您必須指定該 blob 的容器。 您可以選擇**使用現有的**容器，或選擇**新建**以使用新建立的容器。|
| **路徑模式** (選用) | 用來在指定的容器中找出 blob 的檔案路徑。 如果要從容器的根目錄讀取 Blob，請不要設置路徑模式。 在該路徑內，您可以指定下列三個變數的一個或多個執行個體：`{date}`、`{time}` 或 `{partition}`<br/><br/>範例 1：`cluster1/logs/{date}/{time}/{partition}`<br/><br/>範例 2：`cluster1/logs/{date}`<br/><br/>`*` 字元不是路徑前置詞允許的值。 僅允許有效的 <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">Azure blob 字元</a>。 不包括容器名稱或檔案名。 |
| **日期格式**（可選） | 在路徑中使用日期變數時，用來組織檔案的日期格式。 範例： `YYYY/MM/DD` |
| **時間格式** (選用) |  在路徑中使用時間變數時，用來組織檔案的時間格式。 目前唯一支援的值為 `HH` (表示小時)。 |
| **分區鍵** | 如果輸入由屬性分區，則可以添加此屬性的名稱。 分區鍵是可選的，如果包含此屬性上的分區 BY 或 GROUP BY 子句，則用於提高查詢的性能。 |
| **事件序列化格式** | 傳入資料流程的序列化格式（JSON、CSV、Avro[或其他（原型、XML、專有...）。](custom-deserializer.md)  確認 JSON 格式與規格一致，並且不包含以 0 開頭的十進位數字。 |
| **編碼** | 對於 CSV 和 JSON 而言，UTF-8 是目前唯一支援的編碼格式。 |
| **壓縮** | 用來讀取內送資料流的壓縮類型，例如無 (預設值)、GZip 或 Deflate。 |

當您的資料來自 Blob 儲存體來源時，您可以在串流分析查詢中存取下列中繼資料欄位：

| 屬性 | 描述 |
| --- | --- |
| **BlobName** |事件來源的輸入 Blob 名稱。 |
| **EventProcessedUtcTime** |資料流分析處理事件的日期與時間。 |
| **BlobLastModifiedUtcTime** |上次修改 Blob 的時間與日期。 |
| **分區 Id** |輸入配接器以零起始的資料分割識別碼。 |

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
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
