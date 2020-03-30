---
title: 了解來自 Azure 串流分析的輸出
description: 本文說明 Azure 串流分析中所提供的資料輸出選項，包括適用於分析結果的 Power BI。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: e0b4bcac8494f136dde21b03422e12b72cecb8f3
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366448"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>了解來自 Azure 串流分析的輸出

本文介紹了可用於 Azure 流分析作業的輸出類型。 輸出可讓您存放並儲存串流分析作業的結果。 通過使用輸出資料，您可以進一步進行業務分析和資料倉儲。

在設計流分析查詢時，請使用[INTO 子句](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics)引用輸出的名稱。 通過在查詢中提供多個 INTO 子句，可以每個作業使用單個輸出，或者每個流式處理作業（如果需要）使用多個輸出。

要創建、編輯和測試流分析作業輸出，可以使用[Azure 門戶](stream-analytics-quick-create-portal.md#configure-job-output)[、Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job) [、.NET API、REST](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet) [API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output)和[視覺化工作室](stream-analytics-quick-create-vs.md)。

某些輸出類型支援[分區](#partitioning)。 [輸出批次大小](#output-batch-size)因優化輸送量而異。


## <a name="azure-data-lake-storage-gen-1"></a>Azure 資料存儲第 1 代

流分析支援[Azure 資料存儲第 1 代](../data-lake-store/data-lake-store-overview.md)。 Azure 資料湖存儲是一個企業範圍的超大規模存儲庫，用於大資料分析工作負載。 您可以使用"資料湖存儲"存儲任何大小、類型和引入速度的資料，以便進行操作和探索性分析。 需要授權流分析才能訪問資料湖存儲。

流分析的 Azure 資料湖存儲輸出當前不在 Azure 中國 21Vianet 和 Azure 德國 （T-Systems 國際）區域中提供。

下表列出了屬性名稱及其說明，以配置資料湖存儲第 1 代輸出。   

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 | 用於查詢的易記名稱，用於將查詢輸出定向到資料湖存儲。 |
| 訂用帳戶 | 包含 Azure 資料湖存儲帳戶的訂閱。 |
| 帳戶名稱 | 發送輸出的資料湖存儲帳戶的名稱。 您會收到訂閱中可用的資料湖應用商店帳戶的下拉清單。 |
| 路徑前置詞模式 | 用於在指定的 Data Lake 應用商店帳戶中寫入檔的檔路徑。 您可以指定 [date] 和 [時間] 變數的一個或多個實例：<br /><ul><li>範例 1：folder1/logs/{date}/{time}</li><li>範例 2：folder1/logs/{date}</li></ul><br />創建資料夾結構的時間戳記遵循 UTC 而不是本地時間。<br /><br />如果檔路徑模式不包含尾隨斜杠 （/），則檔路徑中的最後一個模式將被視為檔案名首碼。 <br /><br />系統會在下列情況下建立新檔案：<ul><li>輸出結構描述中出現變更</li><li>作業的外部或內部重新開機</li></ul> |
| 日期格式 | 選擇性。 如果前置詞路徑中使用日期權杖，您可以選取組織檔案要用的日期格式。 範例：YYYY/MM/DD |
|時間格式 | 選擇性。 如果前置詞路徑中使用時間權杖，請指定組織檔案要用的時間格式。 目前唯一支援的值為 HH。 |
| 事件序列化格式 | 輸出資料的序列化格式。 支援 JSON、CSV 和 Avro。|
| 編碼 | 如果使用 CSV 或 JSON 格式，則必須指定編碼。 UTF-8 是目前唯一支援的編碼格式。|
| 分隔符號 | 僅適用于 CSV 序列化。 串流分析可支援多種序列化 CSV 資料常用的分隔符號。 支援的值是逗號、分號、空格、索引標籤和分隔號。|
| [格式] | 僅適用于 JSON 序列化。 **線分隔**指定通過將每個 JSON 物件由新行分隔來格式化輸出。 如果選擇 **"行分隔**"，則一次讀取一個物件的 JSON。 整個內容本身將不是有效的 JSON。  **陣列**指定輸出格式化為 JSON 物件的陣列。 只有在作業停止或串流分析已移動到下一個時間範圍時，才會關閉這個陣列。 通常，最好使用線路分隔的 JSON，因為它不需要在輸出檔案仍在寫入時進行任何特殊處理。|
| 驗證模式 | 您可以使用[託管標識](stream-analytics-managed-identities-adls.md)或使用者權杖授權訪問資料湖存儲帳戶。 授予存取權限後，您可以通過更改使用者帳戶密碼、刪除此作業的"資料湖存儲"輸出或刪除流分析作業來撤銷存取權限。 |

## <a name="sql-database"></a>SQL Database

可以將[Azure SQL 資料庫](https://azure.microsoft.com/services/sql-database/)用作本質上具有關系性的資料或依賴于在關係資料庫中託管的內容的應用程式的輸出。 流分析作業寫入 SQL 資料庫中的現有表。 表架構必須與作業輸出中的欄位及其類型完全符合。 還可以通過 SQL 資料庫輸出選項指定[Azure SQL 資料倉儲](https://azure.microsoft.com/documentation/services/sql-data-warehouse/)作為輸出。 要瞭解如何提高寫入輸送量，請參閱使用 Azure [SQL 資料庫作為輸出文章的流分析](stream-analytics-sql-output-perf.md)。

您還可以使用 Azure [SQL 資料庫託管實例](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance)作為輸出。 您必須在[Azure SQL 資料庫託管實例中配置公共終結點](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)，然後在 Azure 流分析中手動設定以下設置。 手動設定以下設置還支援運行 SQL Server 並附加資料庫的 Azure 虛擬機器。

下表列出了創建 SQL 資料庫輸出的屬性名稱及其說明。

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 |此為易記名稱，用於在查詢中將查詢輸出指向這個資料庫。 |
| 資料庫 | 發送輸出的資料庫的名稱。 |
| 伺服器名稱 | SQL Database 伺服器名稱。 對於 Azure SQL 資料庫託管實例，需要指定埠 3342。 例如，*示例伺服器.public.database.windows.net，3342* |
| 使用者名稱 | 對資料庫具有寫入存取權限的使用者名。 流分析僅支援 SQL 身份驗證。 |
| 密碼 | 連線到資料庫的密碼。 |
| Table | 要在其中寫入輸出的資料表名稱。 表名稱區分大小寫。 此表的架構應與作業輸出生成的欄位數及其類型完全符合。 |
|繼承資料分割配置| 用於繼承上一個查詢步驟的分區方案的選項，用於將具有多個寫入器的完全並行拓撲啟用到表。 如需詳細資訊，請參閱 [Azure 串流分析輸出至 Azure SQL Database](stream-analytics-sql-output-perf.md)。|
|批次計數上限| 建議對隨每個批量插入事務發送的記錄數的上限。|

## <a name="blob-storage-and-azure-data-lake-gen2"></a>Blob 存儲和 Azure 資料湖第 2 代

Data Lake Storage Gen2 讓 Azure 儲存體成為在 Azure 上打造企業 Data Lake 的基礎。 Data Lake Storage Gen2 從開始設計到服務多個 PB 的資訊，同時維持數百百萬位元組的輸送量，使您能夠輕鬆管理大量資料。資料存儲庫 Gen2 的一個基本部分是向 Blob 存儲添加階層命名空間。

Azure Blob 存儲提供了一個經濟高效且可擴展的解決方案，用於在雲中存儲大量非結構化資料。 有關 Blob 存儲及其使用方式的簡介，請參閱[使用 Azure 門戶上載、下載和列出 Blob。](../storage/blobs/storage-quickstart-blobs-portal.md)

下表列出了創建 blob 或 ADLS Gen2 輸出的屬性名稱及其說明。

| 屬性名稱       | 描述                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| 輸出別名        | 此為易記名稱，用於在查詢中將查詢輸出指向這個 blob 儲存體。 |
| 儲存體帳戶     | 發送輸出的存儲帳戶的名稱。               |
| 儲存體帳戶金鑰 | 與儲存體帳戶相關聯的密碼金鑰。                              |
| 存儲容器   | 存儲在 Azure Blob 服務中的 Blob 的邏輯分組。 當您將 blob 上傳至 Blob 服務時，您必須指定該 blob 的容器。 |
| 路徑模式 | 選擇性。 用於在指定的容器中寫入 blob 的檔路徑模式。 <br /><br /> 在路徑模式中，您可以選擇使用日期和時間變數的一個或多個實例來指定寫入 blob 的頻率： <br /> {date}、{time} <br /><br />您可以使用自訂 Blob 資料分割，指定事件資料中的一個自訂 {field} 名稱來分割 Blob。 欄位名稱是英數字元，可以包含空格、連字號和底線。 自訂欄位的限制包含下列各項： <ul><li>欄位名稱不區分大小寫。 例如，服務無法區分列"ID"和列"ID"。</li><li>不允許嵌套欄位。 相反，請使用作業查詢中的別名來"平展"欄位。</li><li>運算式不能用作欄位名稱。</li></ul> <br />這項功能允許在路徑中使用自訂日期/時間格式的指定名稱設定。 自訂日期和時間格式一次只能指定一項，兩側必須加上 {datetime:\<specifier>} 關鍵字。 指定>允許\<輸入的有 yyy、MM、M、dd、d、HH、H、mm、m、ss 或 s。 [日期時間：\<指定>] 關鍵字可以在路徑中多次使用，以形成自訂日期/時間配置。 <br /><br />範例： <ul><li>範例 1：cluster1/logs/{date}/{time}</li><li>範例 2：cluster1/logs/{date}</li><li>範例 3：cluster1/{client_id}/{date}/{time}</li><li>示例 4：群集 1/[日期時間：ss]/[myField]，其中查詢為：選擇資料.myField 作為 myField 從輸入;</li><li>範例 5：cluster1/year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}</ul><br />創建資料夾結構的時間戳記遵循 UTC 而不是本地時間。<br /><br />檔命名使用以下約定： <br /><br />{路徑前置詞模式}/schemaHashcode_Guid_Number.extension<br /><br />範例輸出檔案︰<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />有關此功能的詳細資訊，請參閱 Azure[流分析自訂 blob 輸出分區](stream-analytics-custom-path-patterns-blob-storage-output.md)。 |
| 日期格式 | 選擇性。 如果前置詞路徑中使用日期權杖，您可以選取組織檔案要用的日期格式。 範例：YYYY/MM/DD |
| 時間格式 | 選擇性。 如果前置詞路徑中使用時間權杖，請指定組織檔案要用的時間格式。 目前唯一支援的值為 HH。 |
| 事件序列化格式 | 輸出資料的序列化格式。 支援 JSON、CSV、Avro 和 Parquet。 |
|最小行（僅限鑲木地板）|每個批次的最小行數。 對於 Parquet，每個批次處理都將創建一個新檔。 當前預設值為 2，000 行，允許的最大預設值為 10，000 行。|
|最長時間（僅限鑲木地板）|每個批次的最大等待時間。 在此時間之後，批次處理將寫入輸出，即使未滿足最小行要求也是如此。 當前預設值為 1 分鐘，允許的最大值為 2 小時。 如果 Blob 輸出具有路徑模式頻率，則等待時間不能高於分區時間範圍。|
| 編碼    | 如果使用 CSV 或 JSON 格式，則必須指定編碼。 UTF-8 是目前唯一支援的編碼格式。 |
| 分隔符號   | 僅適用于 CSV 序列化。 串流分析可支援多種序列化 CSV 資料常用的分隔符號。 支援的值是逗號、分號、空格、索引標籤和分隔號。 |
| [格式]      | 僅適用于 JSON 序列化。 **線分隔**指定通過將每個 JSON 物件由新行分隔來格式化輸出。 如果選擇 **"行分隔**"，則一次讀取一個物件的 JSON。 整個內容本身將不是有效的 JSON。 **陣列**指定輸出格式化為 JSON 物件的陣列。 只有在作業停止或串流分析已移動到下一個時間範圍時，才會關閉這個陣列。 通常，最好使用線路分隔的 JSON，因為它不需要在輸出檔案仍在寫入時進行任何特殊處理。 |

使用 Blob 存儲作為輸出時，在 Blob 中會創建一個新檔，在以下情況下：

* 如果檔案超過允許的區塊數目上限 (目前為 50,000 個)。 在未達到允許的最大 blob 大小的情況下，可能達到允許的最大塊數。 比方說，如果輸出速率很高，您可看到每個區塊有更多位元組，且檔案大小更大。 如果輸出速率很低，每個區塊都會具有較少的資料，且檔案大小會較小。
* 如果輸出中有架構更改，並且輸出格式需要固定架構（CSV 和 Avro）。
* 如果作業已重新啟動，無論是由使用者於外部停止並啟動它，或是於內部重新啟動以進行系統維護或錯誤復原。
* 如果查詢已完全分區，並且為每個輸出分區創建了一個新檔。
* 如果使用者刪除存儲帳戶的檔或容器。
* 如果使用路徑首碼模式對輸出進行時間分區，則當查詢移動到下一小時時，將使用新的 Blob。
* 如果輸出由自訂欄位分區，並且如果不存在，則每個分區鍵將創建新的 Blob。
* 如果輸出由分區金鑰基數超過 8，000 的自訂欄位進行分區，並且每個分區鍵創建一個新的 Blob。

## <a name="event-hubs"></a>事件中樞

[Azure 事件中樞](https://azure.microsoft.com/services/event-hubs/) 服務是具高延展性的發佈-訂閱事件擷取器。 它每秒可以收集數百萬個事件。 當流分析作業的輸出成為另一個流作業的輸入時，使用事件中心作為輸出。 有關最大消息大小和批次大小優化的資訊，請參閱[輸出批次大小](#output-batch-size)部分。

您需要一些參數來將來自事件中心的資料流程配置為輸出。

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 | 用於查詢的易記名稱，用於將查詢輸出定向到此事件中心。 |
| 事件中樞命名空間 | 一組消息傳遞實體的容器。 創建新的事件中心時，還會創建事件中心命名空間。 |
| 事件中樞名稱 | 事件中心輸出的名稱。 |
| 事件中樞原則名稱 | 共用訪問策略，您可以在事件中心的 **"配置"** 選項卡上創建。每個共用訪問策略都有一個名稱、您設置的許可權和訪問金鑰。 |
| 事件中樞原則金鑰 | 用於對事件中心命名空間的訪問進行身份驗證的共用訪問金鑰。 |
| 分區鍵列 | 選擇性。 包含事件中心輸出的分區鍵的列。 |
| 事件序列化格式 | 輸出資料的序列化格式。 支援 JSON、CSV 和 Avro。 |
| 編碼 | 對於 CSV 和 JSON 而言，UTF-8 是目前唯一支援的編碼格式。 |
| 分隔符號 | 僅適用于 CSV 序列化。 串流分析可支援多種以 CSV 格式序列化資料常用的分隔符號。 支援的值是逗號、分號、空格、索引標籤和分隔號。 |
| [格式] | 僅適用于 JSON 序列化。 **線分隔**指定通過將每個 JSON 物件由新行分隔來格式化輸出。 如果選擇 **"行分隔**"，則一次讀取一個物件的 JSON。 整個內容本身將不是有效的 JSON。 **陣列**指定輸出格式化為 JSON 物件的陣列。  |
| 屬性列 | 選擇性。 需要作為傳出消息的使用者屬性而不是有效負載附加的 Comma 分隔列。 有關此功能的詳細資訊，在["自訂輸出中繼資料屬性"](#custom-metadata-properties-for-output)部分中。 |

## <a name="power-bi"></a>Power BI

您可以使用[Power BI](https://powerbi.microsoft.com/)作為流分析作業的輸出，以提供豐富的分析結果視覺化體驗。 您可以將此功能用於操作儀表板、報表生成和指標驅動報告。

流分析的 Power BI 輸出當前不在 Azure 中國 21Vianet 和 Azure 德國（T-Systems 國際）區域提供。

下表列出了屬性名稱及其說明，以配置 Power BI 輸出。

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 |提供用於查詢的易記名稱，將查詢輸出定向到此 Power BI 輸出。 |
| 群組工作區 |要啟用與其他 Power BI 使用者共用資料，可以在 Power BI 帳戶中選擇組，或者如果您不想寫入組，請選擇 **"我的工作區**"。 更新現有的群組需要更新 Power BI 驗證。 |
| 資料集名稱 |提供您希望 Power BI 輸出使用的資料集名稱。 |
| 資料表名稱 |提供 Power BI 輸出資料集的資料表名稱。 目前，串流分析作業的 Power BI 輸出在資料集內只能有一個資料表。 |
| 授權連線 | 您需要使用 Power BI 授權來配置輸出設置。 將此輸出存取權限授予 Power BI 儀表板後，可以通過更改使用者帳戶密碼、刪除作業輸出或刪除流分析作業來撤銷存取權限。 | 

有關配置 Power BI 輸出和儀表板的演練，請參閱[Azure 流分析和 Power BI](stream-analytics-power-bi-dashboard.md)教程。

> [!NOTE]
> 不要在 Power BI 儀表板中顯式創建資料集和表。 當作業啟動並且作業開始將輸出泵入 Power BI 時，資料集和表將自動填滿。 如果作業查詢未生成任何結果，則不會創建資料集和表。 如果 Power BI 已具有與此流分析作業中提供的資料集和表同名，則現有資料將被覆蓋。
>

### <a name="create-a-schema"></a>建立結構描述
Azure 流分析為使用者創建 Power BI 資料集和表架構（如果使用者不存在）。 在其他情況下，則會以新的值更新資料表。 目前，資料集中只能存在一個表。 

Power BI 使用先出 （FIFO） 保留原則。 資料將在表中收集，直到達到 200，000 行。

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>將資料類型從流分析轉換為 Power BI
如果輸出結構描述變更，則 Azure 串流分析會在執行階段動態更新資料模型。 所有資料行名稱變更、資料行類型變更以及資料行新增或移除都會加以追蹤。

此表介紹資料類型轉換，從[流分析資料類型](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)轉換為 Power BI[實體資料模型 （EDM） 類型](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model)（如果 Power BI 資料集和表不存在）。

從串流分析 | 至 Power BI
-----|-----
BIGINT | Int64
nvarchar(max) | String
Datetime | Datetime
FLOAT | Double
記錄陣列 | 字串類型、常量值"IRecord"或"IArray"

### <a name="update-the-schema"></a>更新結構描述
串流分析會根據輸出中的第一組事件來推斷資料模型結構描述。 稍後，如有必要，將更新資料模型架構，以適應可能不適合原始架構的傳入事件。

避免查詢`SELECT *`，以防止跨行的動態架構更新。 除了潛在的業績影響外，它還可能導致結果所需要時間的不確定性。 選擇需要在 Power BI 儀表板上顯示的確切欄位。 此外，資料值應該與所選的資料類型相符。


上一個/當前 | Int64 | String | Datetime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | String | String | Double
Double | Double | String | String | Double
String | String | String | String | String 
Datetime | String | String |  Datetime | String

## <a name="table-storage"></a>表格儲存體

[Azure 表存儲](../storage/common/storage-introduction.md)提供高可用性、大規模可擴展的存儲，以便應用程式可以自動擴展以滿足使用者需求。 表存儲是 Microsoft 的 NoSQL 金鑰/屬性存儲，可用於架構上約束較少的結構化資料。 使用 Azure 資料表儲存資料時，資料可長期儲存而且調閱方便。

下表列出了創建表輸出的屬性名稱及其說明。

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 |此為易記名稱，用於在查詢中將查詢輸出指向這個資料表儲存體。 |
| 儲存體帳戶 |發送輸出的存儲帳戶的名稱。 |
| 儲存體帳戶金鑰 |與儲存體帳戶相關聯的存取金鑰。 |
| 資料表名稱 |資料表的名稱。 如果表不存在，則將創建該表。 |
| 資料分割索引鍵 |包含分區鍵的輸出列的名稱。 分區鍵是表中構成實體主鍵第一部分的分區的唯一識別碼。 它是一個字串值，其大小可達 1 KB。 |
| 列索引鍵 |包含行鍵的輸出列的名稱。 行鍵是分區中實體的唯一識別碼。 它可構成實體主索引鍵的第二個部分。 行鍵是一個字串值，其大小可達 1 KB。 |
| 批次大小 |批次作業的記錄數目。 預設值 (100)通常足以應付大部分的作業。 有關修改此設置的更多詳細資訊，請參閱[表批次處理操作規範](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table._table_batch_operation)。 |

## <a name="service-bus-queues"></a>服務匯流排佇列

[服務匯流排佇列](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)向一個或多個競爭消費者提供 FIFO 消息傳遞。 通常，接收方按添加到佇列的時間順序接收和處理消息。 每條消息僅由一個消息消費者接收和處理。

在[相容性級別 1.2](stream-analytics-compatibility-level.md)中，Azure 流分析使用[高級訊息佇列協定 （AMQP）](../service-bus-messaging/service-bus-amqp-overview.md)消息傳遞協定寫入服務匯流排佇列和主題。 透過開放式標準通訊協定，AMQP 可讓您打造一個跨平台的混合式應用程式。

下表列出了創建佇列輸出的屬性名稱及其說明。

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 |用於查詢的易記名稱，用於將查詢輸出定向到此服務匯流排佇列。 |
| 服務匯流排命名空間 |一組消息傳遞實體的容器。 |
| Queue name |服務匯流排佇列的名稱。 |
| 佇列原則名稱 |創建佇列時，還可以在佇列的 **"配置"** 選項卡上創建共用訪問策略。每個共用訪問策略都有一個名稱、您設置的許可權和訪問金鑰。 |
| 佇列原則金鑰 |用來驗證服務匯流排命名空間之存取權的共用存取金鑰。 |
| 事件序列化格式 |輸出資料的序列化格式。 支援 JSON、CSV 和 Avro。 |
| 編碼 |對於 CSV 和 JSON 而言，UTF-8 是目前唯一支援的編碼格式。 |
| 分隔符號 |僅適用于 CSV 序列化。 串流分析可支援多種以 CSV 格式序列化資料常用的分隔符號。 支援的值是逗號、分號、空格、索引標籤和分隔號。 |
| [格式] |僅適用于 JSON 類型。 **線分隔**指定通過將每個 JSON 物件由新行分隔來格式化輸出。 如果選擇 **"行分隔**"，則一次讀取一個物件的 JSON。 整個內容本身將不是有效的 JSON。 **陣列**指定輸出格式化為 JSON 物件的陣列。 |
| 屬性列 | 選擇性。 需要作為傳出消息的使用者屬性而不是有效負載附加的 Comma 分隔列。 有關此功能的詳細資訊，在["自訂輸出中繼資料屬性"](#custom-metadata-properties-for-output)部分中。 |
| 系統屬性列 | 選擇性。 需要附加到傳出消息而不是有效負載的系統屬性和相應列名稱的鍵值對。 有關此功能的詳細資訊，位於[服務匯流排佇列和主題輸出的"系統"部分屬性中](#system-properties-for-service-bus-queue-and-topic-outputs)  |

分區數[基於服務匯流排 SKU 和大小](../service-bus-messaging/service-bus-partitioning.md)。 分割區索引鍵是每個分割區的唯一整數值。

## <a name="service-bus-topics"></a>服務匯流排主題
服務匯流排佇列提供從發送方到接收方的一對一通信方法。 [服務匯流排主題](https://msdn.microsoft.com/library/azure/hh367516.aspx)提供一對多形式的通信。

下表列出了創建服務匯流排主題輸出的屬性名稱及其說明。

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 |用於查詢的易記名稱，用於將查詢輸出定向到此服務匯流排主題。 |
| 服務匯流排命名空間 |一組消息傳遞實體的容器。 建立新的事件中樞時，也會建立服務匯流排命名空間。 |
| 主題名稱 |主題為訊息實體，類似於事件中樞和佇列。 它們旨在從設備和服務收集事件流。 創建主題時，還會為主題指定特定名稱。 除非創建了訂閱，否則發送到主題的消息不可用，因此請確保主題下有一個或多個訂閱。 |
| 主題原則名稱 |創建服務匯流排主題時，還可以在主題的 **"配置"** 選項卡上創建共用訪問策略。每個共用訪問策略都有一個名稱、您設置的許可權和訪問金鑰。 |
| 主題原則金鑰 |用來驗證服務匯流排命名空間之存取權的共用存取金鑰。 |
| 事件序列化格式 |輸出資料的序列化格式。 支援 JSON、CSV 和 Avro。 |
| 編碼 |如果使用 CSV 或 JSON 格式，則必須指定編碼。 UTF-8 是目前唯一支援的編碼格式。 |
| 分隔符號 |僅適用于 CSV 序列化。 串流分析可支援多種以 CSV 格式序列化資料常用的分隔符號。 支援的值是逗號、分號、空格、索引標籤和分隔號。 |
| 屬性列 | 選擇性。 需要作為傳出消息的使用者屬性而不是有效負載附加的 Comma 分隔列。 有關此功能的詳細資訊，在["自訂輸出中繼資料屬性"](#custom-metadata-properties-for-output)部分中。 |
| 系統屬性列 | 選擇性。 需要附加到傳出消息而不是有效負載的系統屬性和相應列名稱的鍵值對。 有關此功能的詳細資訊，位於[服務匯流排佇列和主題輸出的"系統"部分屬性中](#system-properties-for-service-bus-queue-and-topic-outputs) |

分區數[基於服務匯流排 SKU 和大小](../service-bus-messaging/service-bus-partitioning.md)。 分區鍵是每個分區的唯一整數值。

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/)是一種全球分佈的資料庫服務，在全球範圍內提供無限的彈性擴展、豐富的查詢和跨架構無關資料模型的自動索引。 要瞭解用於流分析的 Azure Cosmos DB 容器選項，請參閱[使用 Azure Cosmos DB 的流分析作為輸出](stream-analytics-documentdb-output.md)文章。

來自流分析的 Azure Cosmos DB 輸出當前不在 Azure 中國 21Vianet 和 Azure 德國（T-Systems 國際）區域中提供。

> [!Note]
> 此時，Azure 流分析僅支援使用 SQL API 連接到 Azure Cosmos DB。
> 尚不支援其他 Azure Cosmos DB API。 如果您將 Azure Stream Analytics 指向使用其他 API 建立的 Azure Cosmos DB 帳戶，可能無法正確儲存資料。

下表描述用來建立 Azure Cosmos DB 輸出的屬性。

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 | 在您的串流分析查詢中參照此輸出時所用的別名。 |
| 接收 | Azure 宇宙資料庫。 |
| 匯入選項 | 從**訂閱中選擇"選擇宇宙資料庫"** 或**手動提供宇宙資料庫設置**。
| 帳戶識別碼 | Azure Cosmos DB 帳戶的名稱或端點 URI。 |
| 帳戶金鑰 | Azure Cosmos DB 帳戶的共用存取金鑰。 |
| 資料庫 | Azure Cosmos DB 資料庫名稱。 |
| 容器名稱 | 要使用的容器名稱，必須存在於 Cosmos DB 中。 範例：  <br /><ul><li> _我的容器_：必須存在名為"我的容器"的容器。</li>|
| 文件識別碼 |選擇性。 用於指定插入或更新操作所基於的主鍵的輸出事件中的欄位的名稱。

## <a name="azure-functions"></a>Azure Functions
Azure 函數是一種無伺服器計算服務，可用於按需運行代碼，而無需顯式預配或管理基礎結構。 它允許您實現由 Azure 或合作夥伴服務中發生的事件觸發的代碼。 Azure 函數回應觸發器的這種功能使其成為 Azure 流分析的自然輸出。 此輸出配接器使使用者能夠將流分析連接到 Azure 函數，並運行腳本或程式碼片段以回應各種事件。

流分析的 Azure 函數輸出當前不在 Azure 中國 21Vianet 和 Azure 德國（T-Systems 國際）區域中提供。

Azure 串流分析會透過 HTTP 觸發程序叫用 Azure Functions。 Azure 函數輸出配接器具有以下可配置屬性：

| 屬性名稱 | 描述 |
| --- | --- |
| 函式應用程式 |Azure 函數應用的名稱。 |
| 函式 |Azure 函數應用中函數的名稱。 |
| Key |如果要使用其他訂閱中的 Azure 函數，可以通過提供訪問函數的金鑰來執行此操作。 |
| 批次大小上限 |一個屬性，用於設置發送到 Azure 函數的每個輸出批次處理的最大大小。 輸入是以位元組為單位。 預設情況下，此值為 262，144 位元組 （256 KB）。 |
| 批次計數上限  |一個屬性，用於指定發送到 Azure 函數的每個批次處理中事件的最大數。 預設值是 100。 |

Azure 流分析期望從函數應用中針對成功處理的批次提供 HTTP 狀態 200。

當 Azure 流分析收到 Azure 函數中的 413（"HTTP 請求實體太大"）異常時，它會減小它發送到 Azure 函數的批次處理的大小。 在 Azure 函式程式碼中，使用這個例外狀況可確保 Azure 串流分析不會傳送過大的批次。 此外，請確保函數中使用的最大批次處理計數和大小值與流分析門戶中輸入的值一致。

> [!NOTE]
> 在測試連接期間，流分析向 Azure 函數發送一個空批次處理，以測試兩者之間的連接是否有效。 確保"功能"應用處理空批次處理請求，以確保測試連接通過。

此外，在時間視窗中沒有事件著陸的情況下，不會生成輸出。 因此，不調用**computeResult**函數。 此行為與內建的視窗型彙總函式一致。

## <a name="custom-metadata-properties-for-output"></a>用於輸出的自訂中繼資料屬性 

您可以將查詢列作為使用者屬性附加到傳出郵件。 這些列不會進入有效負載。 這些屬性以輸出消息上的字典形式存在。 *鍵*是列名，*值*是屬性字典中的列值。 除記錄和陣列外，所有流分析資料類型都受支援。  

支援的輸出： 
* 服務匯流排佇列 
* 服務匯流排主題 
* 事件中樞 

在下面的示例中，我們將兩個欄位`DeviceId`和`DeviceStatus`中繼資料添加到中繼資料中。 
* 查詢： `select *, DeviceId, DeviceStatus from iotHubInput`
* 輸出配置：`DeviceId,DeviceStatus`

![屬性列](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

以下螢幕截圖顯示了通過[服務匯流排資源管理器](https://github.com/paolosalvatori/ServiceBusExplorer)在 EventHub 中檢查的輸出消息屬性。

![事件自訂屬性](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="system-properties-for-service-bus-queue-and-topic-outputs"></a>服務匯流排佇列和主題輸出的系統屬性 
您可以將查詢列作為[系統屬性](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties)附加到傳出服務匯流排佇列或主題消息。 這些列不會進入有效負載，而是使用查詢列值填充相應的 BrokeredMessage[系統屬性](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties)。
支援這些系統屬性 - `MessageId, ContentType, Label, PartitionKey, ReplyTo, SessionId, CorrelationId, To, ForcePersistence, TimeToLive, ScheduledEnqueueTimeUtc`。
這些列的字串值被解析為相應的系統屬性數值型別，任何分析失敗都被視為資料錯誤。
此欄位作為 JSON 物件格式提供。 有關此格式的詳細資訊如下 ：
* 被大括弧包圍{}。
* 以鍵/值對書寫。
* 鍵和值必須是字串。
* 鍵是系統屬性名稱，值是查詢列名稱。
* 鍵和值由冒號分隔。
* 每個鍵/值對用逗號分隔。

這顯示了如何使用此屬性 |

* 查詢： `select *, column1, column2 INTO queueOutput FROM iotHubInput`
* 系統屬性列：`{ "MessageId": "column1", "PartitionKey": "column2"}`

這將設置`MessageId`具有`column1`值的服務匯流排佇列消息，並且分區鍵設置為`column2`'值。

## <a name="partitioning"></a>資料分割

下表摘要說明分割支援，和每個輸出類型的輸出寫入器數目：

| 輸出類型 | 支援分割 | 資料分割索引鍵  | 輸出寫入器數目 |
| --- | --- | --- | --- |
| Azure Data Lake Store | 是 | 在路徑首碼模式中使用 [date] 和 [時間] 權杖。 選擇日期格式，如 YYYY/MM/DD、DD/MM/YYYY 或 MM-DD-YYYY。 HH 用於時間格式。 | 遵循[完整可平行化查詢](stream-analytics-scale-jobs.md)的輸入資料分割。 |
| Azure SQL Database | 是，需要啟用。 | 基於查詢中的分區 BY 子句。 | 啟用繼承分區選項時，遵循[完全可並行查詢](stream-analytics-scale-jobs.md)的輸入分區。 要瞭解有關在將資料載入到 Azure SQL 資料庫時實現更好的寫入輸送量性能的詳細資訊，請參閱[Azure 流分析輸出到 Azure SQL 資料庫](stream-analytics-sql-output-perf.md)。 |
| Azure Blob 儲存體 | 是 | 在路徑模式中使用事件欄位中的 {date} 和 {time} 權杖。 選擇日期格式，如 YYYY/MM/DD、DD/MM/YYYY 或 MM-DD-YYYY。 HH 用於時間格式。 您可依照單一自訂事件屬性 {fieldname} 或 {datetime:\<specifier>} 分割 Blob 輸出。 | 遵循[完整可平行化查詢](stream-analytics-scale-jobs.md)的輸入資料分割。 |
| Azure 事件中心 | 是 | 是 | 根據分割區對齊方式而有所不同。<br /> 當事件中心輸出的分區鍵與上游（上一個）查詢步驟等一致時，寫入器的數量與事件中心輸出中的分區數相同。 每個編寫器使用[EventHubSender 類](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet)將事件發送到特定分區。 <br /> 當事件中心輸出的分區鍵未與上游（上一個）查詢步驟對齊時，編寫器的數量與上一步中的分區數相同。 每個編寫器使用**EventHubClient**中的[SendBatchAsync 類](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet)向所有輸出分區發送事件。 |
| Power BI | 否 | None | 不適用。 |
| Azure 資料表儲存體 | 是 | 任何輸出資料行。  | 遵循[完整平行化查詢](stream-analytics-scale-jobs.md)的輸入資料分割。 |
| Azure 服務匯流排主題 | 是 | 自動選擇。 分割區數目是根據[服務匯流排 SKU 和大小](../service-bus-messaging/service-bus-partitioning.md)。 分區鍵是每個分區的唯一整數值。| 與輸出主題中的分割區數目相同。  |
| Azure 服務匯流排佇列 | 是 | 自動選擇。 分割區數目是根據[服務匯流排 SKU 和大小](../service-bus-messaging/service-bus-partitioning.md)。 分區鍵是每個分區的唯一整數值。| 與輸出佇列中的分割區數目相同。 |
| Azure Cosmos DB | 是 | 基於查詢中的分區 BY 子句。 | 遵循[完整平行化查詢](stream-analytics-scale-jobs.md)的輸入資料分割。 |
| Azure Functions | 是 | 基於查詢中的分區 BY 子句。 | 遵循[完整平行化查詢](stream-analytics-scale-jobs.md)的輸入資料分割。 |

還可以使用`INTO <partition count>`查詢中的 （請參見[INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)） 子句控制輸出編寫器的數量，這有助於實現所需的作業拓撲。 如果您的輸出配接器尚未分割，在某個輸入分割區中缺少資料的情況下，將會導致最多為延遲傳入時間長度的延遲。 在這種情況下，輸出將合併到單個寫入器，這可能會導致管道中的瓶頸。 要瞭解有關延遲到達策略的更多資訊，請參閱[Azure 流分析事件順序注意事項](stream-analytics-out-of-order-and-late-events.md)。

## <a name="output-batch-size"></a>輸出批次大小
Azure 流分析使用可變大小的批次處理來處理事件並寫入輸出。 通常，流分析引擎不會一次編寫一條消息，並且使用批次處理提高效率。 當傳入和傳出事件的速率都很高時，流分析使用較大的批次處理。 當輸出速率較低時，它會使用較小的批次來降低延遲。

下表解釋了輸出批次處理的一些注意事項：

| 輸出類型 | 訊息大小上限 | 批次大小最佳化 |
| :--- | :--- | :--- |
| Azure Data Lake Store | 請參閱[資料湖存儲限制](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-store-limits)。 | 每個寫入操作最多使用 4 MB。 |
| Azure SQL Database | 可使用最大批數進行配置。 預設情況下，每個批量插入的最大行和 100 個最小行。<br />請參閱[Azure SQL 限制](../sql-database/sql-database-resource-limits.md)。 |  每個批次最初批量插入，並具有最大批次計數。 批次處理根據 SQL 中的可重試錯誤分成兩半（直到最小批次處理計數）。 |
| Azure Blob 儲存體 | 請參閱[Azure 存儲限制](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)。 | 最大 blob 塊大小為 4 MB。<br />最大 blob bock 計數為 50，000。 |
| Azure 事件中心  | 每條消息 256 KB 或 1 MB。 <br />請參閱[事件中心限制](../event-hubs/event-hubs-quotas.md)。 |  當輸入/輸出分區未對齊時，每個事件將單獨打包，`EventData`併發送到一批最多到最大消息大小的中。 如果使用[自訂中繼資料屬性](#custom-metadata-properties-for-output)，也會發生這種情況。 <br /><br />  當輸入/輸出分區對齊時，多個事件將打包到單個`EventData`實例中，最多到最大消息大小併發送。 |
| Power BI | 請參閱[Power BI 休息 API 限制](https://msdn.microsoft.com/library/dn950053.aspx)。 |
| Azure 資料表儲存體 | 請參閱[Azure 存儲限制](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)。 | 預設值為每個事務 100 個實體。 您可以根據需要將其配置為較小的值。 |
| Azure 服務匯流排佇列   | 標準層每條消息 256 KB，高級層 1MB。<br /> 請參閱[服務匯流排限制](../service-bus-messaging/service-bus-quotas.md)。 | 每封郵件使用單個事件。 |
| Azure 服務匯流排主題 | 標準層每條消息 256 KB，高級層 1MB。<br /> 請參閱[服務匯流排限制](../service-bus-messaging/service-bus-quotas.md)。 | 每封郵件使用單個事件。 |
| Azure Cosmos DB   | 請參閱[Azure 宇宙資料庫限制](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cosmos-db-limits)。 | 批次處理大小和寫入頻率根據 Azure Cosmos DB 回應動態調整。 <br /> 流分析沒有預先確定的限制。 |
| Azure Functions   | | 預設批次處理大小為 262，144 位元組 （256 KB）。 <br /> 每個批次處理的預設事件計數為 100。 <br /> 批次大小可以設定，並可以在串流分析的[輸出選項](#azure-functions)中增加或減少。

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> 
> [快速入門：使用 Azure 入口網站建立串流分析作業](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
