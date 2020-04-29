---
title: 了解來自 Azure 串流分析的輸出
description: 本文說明 Azure 串流分析中所提供的資料輸出選項，包括適用於分析結果的 Power BI。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 4517f85fae278bd8bc15a9586d9dc0202e7dfe56
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80475222"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>了解來自 Azure 串流分析的輸出

本文說明適用于 Azure 串流分析作業的輸出類型。 輸出可讓您存放並儲存串流分析作業的結果。 藉由使用輸出資料，您可以對資料進行進一步的商務分析和資料倉儲。

當您設計串流分析查詢時，請使用[INTO 子句](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics)來參考輸出的名稱。 您可以針對每個作業使用單一輸出，或在查詢中提供多個 INTO 子句，以每個串流作業（如果您需要的話）來執行多個輸出。

若要建立、編輯和測試串流分析作業輸出，您可以使用[Azure 入口網站](stream-analytics-quick-create-portal.md#configure-job-output)、 [AZURE POWERSHELL](stream-analytics-quick-create-powershell.md#configure-output-to-the-job)、 [.net API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet)、 [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output)和[Visual Studio](stream-analytics-quick-create-vs.md)。

某些輸出類型支援資料[分割](#partitioning)。 [輸出批次大小](#output-batch-size)會依優化輸送量而有所不同。


## <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1

串流分析支援[Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md)。 Azure Data Lake Storage 是適用于大型資料分析工作負載的全企業超大規模資料庫存放庫。 您可以使用 Data Lake Storage 來儲存任何大小、類型和內嵌速度的資料，以進行操作和探索分析。 串流分析需要獲得授權才能存取 Data Lake Storage。

來自串流分析的 Azure Data Lake Storage 輸出目前無法在 Azure 中國的世紀和 Azure 德國（T 系統國際）區域中使用。

下表列出屬性名稱及其描述，以設定您的 Data Lake Storage Gen 1 輸出。   

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 | 在查詢中用來將查詢輸出導向 Data Lake Store 的易記名稱。 |
| 訂用帳戶 | 包含您 Azure Data Lake Storage 帳戶的訂閱。 |
| 帳戶名稱 | 您要傳送輸出之 Data Lake Store 帳戶的名稱。 您會看到您的訂用帳戶中可用 Data Lake Store 帳戶的下拉式清單。 |
| 路徑前置詞模式 | 用來在指定的 Data Lake Store 帳戶中寫入檔案的檔案路徑。 您可以指定一或多個 {date} 和 {time} 變數的實例：<br /><ul><li>範例 1：folder1/logs/{date}/{time}</li><li>範例 2：folder1/logs/{date}</li></ul><br />所建立資料夾結構的時間戳記會遵循 UTC 而非當地時間。<br /><br />如果檔案路徑模式不包含尾端斜線（/），則會將檔案路徑中的最後一個模式視為檔案名前置詞。 <br /><br />系統會在下列情況下建立新檔案：<ul><li>輸出結構描述中出現變更</li><li>作業的外部或內部重新開機</li></ul> |
| 日期格式 | 選擇性。 如果前置詞路徑中使用日期權杖，您可以選取組織檔案要用的日期格式。 範例：YYYY/MM/DD |
|時間格式 | 選擇性。 如果前置詞路徑中使用時間權杖，請指定組織檔案要用的時間格式。 目前唯一支援的值為 HH。 |
| 事件序列化格式 | 輸出資料的序列化格式。 支援 JSON、CSV 和 Avro。|
| 編碼 | 如果您使用 CSV 或 JSON 格式，則必須指定編碼。 UTF-8 是目前唯一支援的編碼格式。|
| 分隔符號 | 僅適用于 CSV 序列化。 串流分析可支援多種序列化 CSV 資料常用的分隔符號。 支援的值是逗號、分號、空格、索引標籤和分隔號。|
| 格式 | 僅適用于 JSON 序列化。 **分隔的行會**指定輸出的格式化方式是讓每個 JSON 物件都以新行分隔。 如果您選取 [**以行分隔**]，則會一次讀取一個物件的 JSON。 整個內容本身不是有效的 JSON。  **陣列**指定將輸出格式化為 JSON 物件的陣列。 只有在作業停止或串流分析已移動到下一個時間範圍時，才會關閉這個陣列。 一般來說，最好使用以行分隔的 JSON，因為它不需要任何特殊處理，而輸出檔案仍會寫入。|
| 驗證模式 | 您可以使用[受控識別](stream-analytics-managed-identities-adls.md)或使用者權杖來授權 Data Lake Storage 帳戶的存取權。 授與存取權之後，您可以藉由變更使用者帳戶密碼、刪除此作業的 Data Lake Storage 輸出，或刪除串流分析作業，來撤銷存取權。 |

## <a name="sql-database"></a>SQL Database

您可以使用[Azure SQL Database](https://azure.microsoft.com/services/sql-database/)做為本質上的關聯式資料，或是相依于關係資料庫中所裝載內容之應用程式的輸出。 串流分析作業會寫入 SQL Database 中的現有資料表。 資料表架構必須完全符合作業輸出中的欄位及其類型。 您也可以透過 SQL Database 輸出選項，將[Azure SQL 資料倉儲](https://azure.microsoft.com/documentation/services/sql-data-warehouse/)指定為輸出。 若要瞭解改善寫入輸送量的方式，請參閱[使用 Azure SQL Database 作為輸出的串流分析](stream-analytics-sql-output-perf.md)一文。

您也可以使用[Azure SQL Database 受控執行個體](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance)做為輸出。 您必須[在 Azure SQL Database 受控執行個體中設定公用端點](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)，然後在 Azure 串流分析中手動設定下列設定。 以附加資料庫執行 SQL Server 的 Azure 虛擬機器，也可以透過手動方式進行下列設定來支援。

下表列出用來建立 SQL Database 輸出的屬性名稱及其描述。

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 |此為易記名稱，用於在查詢中將查詢輸出指向這個資料庫。 |
| 資料庫 | 您要傳送輸出的目標資料庫名稱。 |
| 伺服器名稱 | SQL Database 伺服器名稱。 針對 Azure SQL Database 受控執行個體，必須指定埠3342。 例如， *sampleserver、3342、* 。 |
| 使用者名稱 | 具有資料庫之寫入權限的使用者名稱。 串流分析僅支援 SQL 驗證。 |
| 密碼 | 連線到資料庫的密碼。 |
| Table | 要在其中寫入輸出的資料表名稱。 資料表名稱會區分大小寫。 這個資料表的架構應該完全符合您的作業輸出所產生的欄位和其類型的數目。 |
|繼承資料分割配置| 繼承先前查詢步驟之資料分割配置的選項，可讓多個寫入器具有資料表的完全平行拓撲。 如需詳細資訊，請參閱 [Azure 串流分析輸出至 Azure SQL Database](stream-analytics-sql-output-perf.md)。|
|批次計數上限| 針對每個大量插入交易傳送的記錄數目建議的上限。|

## <a name="blob-storage-and-azure-data-lake-gen2"></a>Blob 儲存體和 Azure Data Lake Gen2

Data Lake Storage Gen2 讓 Azure 儲存體成為在 Azure 上打造企業 Data Lake 的基礎。 從一開始就設計為服務數 pb 的資訊，同時維持數百 gb 的輸送量，Data Lake Storage Gen2 可讓您輕鬆地管理大量資料。Data Lake Storage Gen2 的基本部分是將階層式命名空間新增至 Blob 儲存體。

Azure Blob 儲存體提供符合成本效益且可調整的解決方案，讓您在雲端中儲存大量非結構化資料。 如需 Blob 儲存體及其使用方式的簡介，請參閱[上傳、下載及列出具有 Azure 入口網站的 blob](../storage/blobs/storage-quickstart-blobs-portal.md)。

下表列出屬性名稱及其描述，以建立 blob 或 ADLS Gen2 輸出。

| 屬性名稱       | 描述                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| 輸出別名        | 此為易記名稱，用於在查詢中將查詢輸出指向這個 blob 儲存體。 |
| 儲存體帳戶     | 您要傳送輸出的目標儲存體帳戶名稱。               |
| 儲存體帳戶金鑰 | 與儲存體帳戶相關聯的密碼金鑰。                              |
| 儲存體容器   | 儲存在 Azure Blob 服務中之 blob 的邏輯群組。 當您將 blob 上傳至 Blob 服務時，您必須指定該 blob 的容器。 |
| 路徑模式 | 選擇性。 用來在指定容器中寫入 blob 的檔案路徑模式。 <br /><br /> 在路徑模式中，您可以選擇使用 date 和 time 變數的一個或多個實例來指定 blob 的寫入頻率： <br /> {date}、{time} <br /><br />您可以使用自訂 Blob 資料分割，指定事件資料中的一個自訂 {field} 名稱來分割 Blob。 欄位名稱是英數字元，可以包含空格、連字號和底線。 自訂欄位的限制包含下列各項： <ul><li>功能變數名稱不區分大小寫。 例如，服務無法區分資料行 "ID" 和資料行 "id"。</li><li>不允許使用嵌套欄位。 相反地，請使用工作查詢中的別名來「簡維」欄位。</li><li>運算式不能用來做為功能變數名稱。</li></ul> <br />這項功能允許在路徑中使用自訂日期/時間格式的指定名稱設定。 自訂日期和時間格式一次只能指定一項，兩側必須加上 {datetime:\<specifier>} 關鍵字。 規範的\<允許輸入> 是 YYYY、MM、M、dd、d、HH、H、MM、M、ss 或 s。 {Datetime：\<規範>} 關鍵字可以在路徑中多次使用，以形成自訂日期/時間設定。 <br /><br />範例： <ul><li>範例 1：cluster1/logs/{date}/{time}</li><li>範例 2：cluster1/logs/{date}</li><li>範例 3：cluster1/{client_id}/{date}/{time}</li><li>範例4： cluster1/{datetime： ss}/{myField}，其中查詢為： SELECT myField AS myField FROM Input;</li><li>範例 5：cluster1/year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}</ul><br />所建立資料夾結構的時間戳記會遵循 UTC 而非當地時間。<br /><br />檔案命名會使用下列慣例： <br /><br />{路徑前置詞模式}/schemaHashcode_Guid_Number.extension<br /><br />範例輸出檔案︰<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />如需這項功能的詳細資訊，請參閱[Azure 串流分析自訂 blob 輸出資料分割](stream-analytics-custom-path-patterns-blob-storage-output.md)。 |
| 日期格式 | 選擇性。 如果前置詞路徑中使用日期權杖，您可以選取組織檔案要用的日期格式。 範例：YYYY/MM/DD |
| 時間格式 | 選擇性。 如果前置詞路徑中使用時間權杖，請指定組織檔案要用的時間格式。 目前唯一支援的值為 HH。 |
| 事件序列化格式 | 輸出資料的序列化格式。 支援 JSON、CSV、Avro 和 Parquet。 |
|最少資料列（僅限 Parquet）|每個批次的最小資料列數目。 針對 Parquet，每個批次都會建立新的檔案。 目前的預設值為2000個數據列，而允許的上限為10000個數據列。|
|最長時間（僅限 Parquet）|每個批次的等候時間上限。 在這段時間之後，即使不符合最小資料列需求，也會將批次寫入輸出。 目前的預設值為1分鐘，而允許的上限為2小時。 如果您的 blob 輸出具有路徑模式頻率，則等候時間不得高於分割區時間範圍。|
| 編碼    | 如果您使用 CSV 或 JSON 格式，則必須指定編碼。 UTF-8 是目前唯一支援的編碼格式。 |
| 分隔符號   | 僅適用于 CSV 序列化。 串流分析可支援多種序列化 CSV 資料常用的分隔符號。 支援的值是逗號、分號、空格、索引標籤和分隔號。 |
| 格式      | 僅適用于 JSON 序列化。 **分隔的行會**指定輸出的格式化方式是讓每個 JSON 物件都以新行分隔。 如果您選取 [**以行分隔**]，則會一次讀取一個物件的 JSON。 整個內容本身不是有效的 JSON。 **陣列**指定將輸出格式化為 JSON 物件的陣列。 只有在作業停止或串流分析已移動到下一個時間範圍時，才會關閉這個陣列。 一般來說，最好使用以行分隔的 JSON，因為它不需要任何特殊處理，而輸出檔案仍會寫入。 |

當您使用 Blob 儲存體做為輸出時，在下列情況下，blob 中會建立新的檔案：

* 如果檔案超過允許的區塊數目上限 (目前為 50,000 個)。 您可能會達到允許的區塊數目上限，而不會達到允許的 blob 大小上限。 比方說，如果輸出速率很高，您可看到每個區塊有更多位元組，且檔案大小更大。 如果輸出速率很低，每個區塊都會具有較少的資料，且檔案大小會較小。
* 如果輸出中有架構變更，而輸出格式需要固定的架構（CSV 和 Avro）。
* 如果作業已重新啟動，無論是由使用者於外部停止並啟動它，或是於內部重新啟動以進行系統維護或錯誤復原。
* 如果查詢已完全分割，則會為每個輸出分割區建立新的檔案。
* 如果使用者刪除儲存體帳戶的檔案或容器，則為。
* 如果輸出是使用路徑前置詞模式分割的時間，則會在查詢移到下一個小時時使用新的 blob。
* 如果輸出是由自訂欄位分割，而且每個分割區索引鍵都有新的 blob （如果不存在的話）。
* 如果輸出是由資料分割索引鍵基數超過8000的自訂欄位分割，則會針對每個分割區索引鍵建立新的 blob。

## <a name="event-hubs"></a>事件中樞

[Azure 事件中樞](https://azure.microsoft.com/services/event-hubs/) 服務是具高延展性的發佈-訂閱事件擷取器。 它每秒可以收集數百萬個事件。 事件中樞做為輸出的其中一種用法，就是當串流分析作業的輸出變成另一個串流工作的輸入時。 如需訊息大小上限和批次大小優化的詳細資訊，請參閱[輸出批次大小](#output-batch-size)一節。

您需要幾個參數，以將事件中樞的資料流程設定為輸出。

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 | 一個易記名稱，用於在查詢中將查詢輸出指向這個事件中樞。 |
| 事件中樞命名空間 | 一組訊息實體的容器。 當您建立新的事件中樞時，也會建立事件中樞命名空間。 |
| 事件中樞名稱 | 事件中樞輸出的名稱。 |
| 事件中樞原則名稱 | 您可以在事件中樞的 [**設定**] 索引標籤上建立的共用存取原則。每個共用存取原則都會有名稱、您設定的許可權，以及存取金鑰。 |
| 事件中樞原則金鑰 | 用來驗證對事件中樞命名空間之存取權的共用存取金鑰。 |
| 資料分割索引鍵資料行 | 選擇性。 包含事件中樞輸出之分割區索引鍵的資料行。 |
| 事件序列化格式 | 輸出資料的序列化格式。 支援 JSON、CSV 和 Avro。 |
| 編碼 | 對於 CSV 和 JSON 而言，UTF-8 是目前唯一支援的編碼格式。 |
| 分隔符號 | 僅適用于 CSV 序列化。 串流分析可支援多種以 CSV 格式序列化資料常用的分隔符號。 支援的值是逗號、分號、空格、索引標籤和分隔號。 |
| 格式 | 僅適用于 JSON 序列化。 **分隔的行會**指定輸出的格式化方式是讓每個 JSON 物件都以新行分隔。 如果您選取 [**以行分隔**]，則會一次讀取一個物件的 JSON。 整個內容本身不是有效的 JSON。 **陣列**指定將輸出格式化為 JSON 物件的陣列。  |
| 屬性資料行 | 選擇性。 以逗號分隔的資料行，必須附加為外寄訊息的使用者屬性，而不是承載。 如需此功能的詳細資訊，請[查看輸出的自訂中繼資料屬性](#custom-metadata-properties-for-output)一節。 |

## <a name="power-bi"></a>Power BI

您可以使用[Power BI](https://powerbi.microsoft.com/)做為串流分析作業的輸出，以提供豐富的分析結果視覺效果體驗。 您可以使用這項功能來進行作業儀表板、產生報告，以及計量驅動的報告。

來自串流分析的 Power BI 輸出目前無法在 Azure 中國的世紀和 Azure 德國（T 系統國際）區域中使用。

下表列出屬性名稱及其描述，以設定您的 Power BI 輸出。

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 |提供可在查詢中用來將查詢輸出指向這個 Power BI 輸出的易記名稱。 |
| 群組工作區 |若要啟用與其他 Power BI 使用者共用資料，您可以選取 Power BI 帳戶內的群組，如果您不想要寫入群組，請選擇 [**我的工作區**]。 更新現有的群組需要更新 Power BI 驗證。 |
| 資料集名稱 |提供您想要 Power BI 輸出使用的資料集名稱。 |
| 資料表名稱 |提供 Power BI 輸出資料集的資料表名稱。 目前，串流分析作業的 Power BI 輸出在資料集內只能有一個資料表。 |
| 授權連線 | 您必須使用 Power BI 進行授權，以設定您的輸出設定。 一旦將此輸出存取權授與 Power BI 儀表板，您就可以藉由變更使用者帳戶密碼、刪除作業輸出或刪除串流分析作業來撤銷存取權。 | 

如需設定 Power BI 輸出和儀表板的逐步解說，請參閱[Azure 串流分析和 Power BI](stream-analytics-power-bi-dashboard.md)教學課程。

> [!NOTE]
> 請勿在 [Power BI] 儀表板中明確建立資料集和資料表。 當作業啟動時，會自動填入資料集和資料表，而作業會開始將輸出提取到 Power BI。 如果作業查詢不會產生任何結果，則不會建立資料集和資料表。 如果 Power BI 已經具有與此串流分析作業中提供之名稱相同的資料集和資料表，則會覆寫現有的資料。
>

### <a name="create-a-schema"></a>建立結構描述
Azure 串流分析會為使用者建立 Power BI 資料集和資料表架構（如果尚未存在的話）。 在其他情況下，則會以新的值更新資料表。 目前，資料集內只能存在一個資料表。 

Power BI 使用先進先出（FIFO）保留原則。 資料將會在資料表中收集，直到達到200000個數據列為止。

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>將資料類型從串流分析轉換成 Power BI
如果輸出結構描述變更，則 Azure 串流分析會在執行階段動態更新資料模型。 所有資料行名稱變更、資料行類型變更以及資料行新增或移除都會加以追蹤。

如果 Power BI 資料集和資料表不存在，則此資料表涵蓋從[串流分析資料類型](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)轉換為 Power BI[實體資料模型（EDM）類型](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model)的資料類型。

從串流分析 | 至 Power BI
-----|-----
BIGINT | Int64
nvarchar(max) | 字串
Datetime | Datetime
FLOAT | Double
記錄陣列 | 字串類型、常數值 "IRecord" 或 "IArray"

### <a name="update-the-schema"></a>更新結構描述
串流分析會根據輸出中的第一組事件來推斷資料模型結構描述。 之後，如有必要，資料模型架構會更新，以容納可能不符合原始架構的傳入事件。

避免`SELECT *`查詢防止跨資料列進行動態架構更新。 除了潛在的效能影響，它可能會導致結果所花費的時間不確定性。 選取需要顯示在 Power BI 儀表板上的確切欄位。 此外，資料值應該與所選的資料類型相符。


上一個/最新 | Int64 | 字串 | Datetime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | 字串 | 字串 | Double
Double | Double | 字串 | 字串 | Double
字串 | 字串 | 字串 | 字串 | 字串 
Datetime | 字串 | 字串 |  Datetime | 字串

## <a name="table-storage"></a>資料表儲存體

[Azure 表格儲存體](../storage/common/storage-introduction.md)提供高可用性且可大幅擴充的儲存體，讓應用程式可以自動調整以滿足使用者需求。 資料表儲存體是 Microsoft 的 NoSQL 索引鍵/屬性存放區，您可以將其用於架構上具有較少條件約束的結構化資料。 使用 Azure 資料表儲存資料時，資料可長期儲存而且調閱方便。

下表列出屬性名稱及其描述，以建立資料表輸出。

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 |此為易記名稱，用於在查詢中將查詢輸出指向這個資料表儲存體。 |
| 儲存體帳戶 |您要傳送輸出的目標儲存體帳戶名稱。 |
| 儲存體帳戶金鑰 |與儲存體帳戶相關聯的存取金鑰。 |
| 資料表名稱 |資料表的名稱。 如果資料表不存在，則會加以建立。 |
| 資料分割索引鍵 |包含資料分割索引鍵的輸出資料行名稱。 資料分割索引鍵是資料表內資料分割的唯一識別碼，可構成實體主鍵的第一個部分。 它是大小上限為 1 KB 的字串值。 |
| 列索引鍵 |包含資料列索引鍵的輸出資料行名稱。 資料列索引鍵是資料分割內實體的唯一識別碼。 它會構成實體主鍵的第二個部分。 資料列索引鍵是大小上限為 1 KB 的字串值。 |
| 批次大小 |批次作業的記錄數目。 預設值 (100)通常足以應付大部分的作業。 如需修改此設定的詳細資訊，請參閱[資料表批次作業規格](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table.tablebatchoperation)。 |

## <a name="service-bus-queues"></a>服務匯流排佇列

[服務匯流排佇列](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)提供 FIFO 訊息傳遞給一或多個競爭取用者。 一般來說，接收者會以它們新增至佇列的時態順序來接收和處理訊息。 每則訊息只會由一個訊息取用者接收和處理。

在[相容性層級 1.2](stream-analytics-compatibility-level.md)中，Azure 串流分析使用[先進的訊息佇列通訊協定（AMQP）](../service-bus-messaging/service-bus-amqp-overview.md)訊息通訊協定來寫入服務匯流排的佇列和主題。 透過開放式標準通訊協定，AMQP 可讓您打造一個跨平台的混合式應用程式。

下表列出屬性名稱及其描述，以建立佇列輸出。

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 |一個易記名稱，用於在查詢中將查詢輸出指向這個服務匯流排的佇列。 |
| 服務匯流排命名空間 |一組訊息實體的容器。 |
| Queue name |服務匯流排佇列的名稱。 |
| 佇列原則名稱 |當您建立佇列時，您也可以在佇列的 [**設定**] 索引標籤上建立共用存取原則。每個共用存取原則都會有名稱、您設定的許可權，以及存取金鑰。 |
| 佇列原則金鑰 |用來驗證服務匯流排命名空間之存取權的共用存取金鑰。 |
| 事件序列化格式 |輸出資料的序列化格式。 支援 JSON、CSV 和 Avro。 |
| 編碼 |對於 CSV 和 JSON 而言，UTF-8 是目前唯一支援的編碼格式。 |
| 分隔符號 |僅適用于 CSV 序列化。 串流分析可支援多種以 CSV 格式序列化資料常用的分隔符號。 支援的值是逗號、分號、空格、索引標籤和分隔號。 |
| 格式 |僅適用于 JSON 類型。 **分隔的行會**指定輸出的格式化方式是讓每個 JSON 物件都以新行分隔。 如果您選取 [**以行分隔**]，則會一次讀取一個物件的 JSON。 整個內容本身不是有效的 JSON。 **陣列**指定將輸出格式化為 JSON 物件的陣列。 |
| 屬性資料行 | 選擇性。 以逗號分隔的資料行，必須附加為外寄訊息的使用者屬性，而不是承載。 如需此功能的詳細資訊，請[查看輸出的自訂中繼資料屬性](#custom-metadata-properties-for-output)一節。 |
| 系統屬性資料行 | 選擇性。 系統屬性的機碼值組，以及需要附加至外寄訊息而非承載的對應資料行名稱。 如需這項功能的詳細資訊，請[服務匯流排佇列和主題輸出的系統屬性](#system-properties-for-service-bus-queue-and-topic-outputs)一節。  |

資料分割數目是[根據服務匯流排的 SKU 和大小](../service-bus-messaging/service-bus-partitioning.md)而定。 分割區索引鍵是每個分割區的唯一整數值。

## <a name="service-bus-topics"></a>服務匯流排主題
服務匯流排佇列提供從傳送者到接收者的一對一通訊方法。 [服務匯流排主題](https://msdn.microsoft.com/library/azure/hh367516.aspx)提供一對多的通訊形式。

下表列出屬性名稱及其描述，以建立服務匯流排主題輸出。

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 |此為易記名稱，用於在查詢中將查詢輸出指向這個服務匯流排主題。 |
| 服務匯流排命名空間 |一組訊息實體的容器。 建立新的事件中樞時，也會建立服務匯流排命名空間。 |
| 主題名稱 |主題為訊息實體，類似於事件中樞和佇列。 其設計目的是要從裝置和服務收集事件資料流程。 建立主題時，也會指定特定名稱。 除非已建立訂用帳戶，否則傳送至主題的訊息將無法使用，因此請確定該主題底下有一或多個訂閱。 |
| 主題原則名稱 |當您建立服務匯流排主題時，您也可以在主題的 [**設定**] 索引標籤上建立共用存取原則。每個共用存取原則都會有名稱、您設定的許可權，以及存取金鑰。 |
| 主題原則金鑰 |用來驗證服務匯流排命名空間之存取權的共用存取金鑰。 |
| 事件序列化格式 |輸出資料的序列化格式。 支援 JSON、CSV 和 Avro。 |
| 編碼 |如果您使用 CSV 或 JSON 格式，則必須指定編碼。 UTF-8 是目前唯一支援的編碼格式。 |
| 分隔符號 |僅適用于 CSV 序列化。 串流分析可支援多種以 CSV 格式序列化資料常用的分隔符號。 支援的值是逗號、分號、空格、索引標籤和分隔號。 |
| 屬性資料行 | 選擇性。 以逗號分隔的資料行，必須附加為外寄訊息的使用者屬性，而不是承載。 如需此功能的詳細資訊，請[查看輸出的自訂中繼資料屬性](#custom-metadata-properties-for-output)一節。 |
| 系統屬性資料行 | 選擇性。 系統屬性的機碼值組，以及需要附加至外寄訊息而非承載的對應資料行名稱。 如需這項功能的詳細資訊，請[服務匯流排佇列和主題輸出的系統屬性](#system-properties-for-service-bus-queue-and-topic-outputs)一節。 |

資料分割數目是[根據服務匯流排的 SKU 和大小](../service-bus-messaging/service-bus-partitioning.md)而定。 資料分割索引鍵是每個分割區的唯一整數值。

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/)是全域散發的資料庫服務，在全球各地提供無限的彈性調整、豐富的查詢，以及不限架構之資料模型的自動編制索引。 若要瞭解串流分析的 Azure Cosmos DB 容器選項，請參閱[使用 Azure Cosmos DB 作為輸出的串流分析](stream-analytics-documentdb-output.md)一文。

來自串流分析的 Azure Cosmos DB 輸出目前無法在 Azure 中國的世紀和 Azure 德國（T 系統國際）區域中使用。

> [!Note]
> 目前，Azure 串流分析僅支援使用 SQL API 連線到 Azure Cosmos DB。
> 尚不支援其他 Azure Cosmos DB API。 如果您將 Azure Stream Analytics 指向使用其他 API 建立的 Azure Cosmos DB 帳戶，可能無法正確儲存資料。

下表描述用來建立 Azure Cosmos DB 輸出的屬性。

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 | 在您的串流分析查詢中參照此輸出時所用的別名。 |
| 接收 | Azure Cosmos DB。 |
| 匯入選項 | 選擇**您的訂用帳戶中的 [Cosmos DB** ]，或**手動提供 Cosmos DB 設定**。
| 帳戶識別碼 | Azure Cosmos DB 帳戶的名稱或端點 URI。 |
| 帳戶金鑰 | Azure Cosmos DB 帳戶的共用存取金鑰。 |
| 資料庫 | Azure Cosmos DB 資料庫名稱。 |
| 容器名稱 | 要使用的容器名稱，必須存在於 Cosmos DB 中。 範例：  <br /><ul><li> _MyContainer_：名為 "MyContainer" 的容器必須存在。</li>|
| 文件識別碼 |選擇性。 輸出事件中的功能變數名稱，用來指定做為插入或更新作業基礎的主要金鑰。

## <a name="azure-functions"></a>Azure Functions
Azure Functions 是無伺服器計算服務，您可以用來視需要執行程式碼，而不需要明確布建或管理基礎結構。 它可讓您執行由 Azure 或合作夥伴服務中發生之事件所觸發的程式碼。 Azure Functions 回應觸發程式的這項能力，會使其成為 Azure 串流分析的自然輸出。 這個輸出介面卡可讓使用者將串流分析連接到 Azure Functions，並執行腳本或程式碼來回應各種事件。

來自串流分析的 Azure Functions 輸出目前無法在 Azure 中國的世紀和 Azure 德國（T 系統國際）區域中使用。

Azure 串流分析會透過 HTTP 觸發程序叫用 Azure Functions。 Azure Functions 輸出介面卡提供下列可設定的屬性：

| 屬性名稱 | 描述 |
| --- | --- |
| 函式應用程式 |Azure Functions 應用程式的名稱。 |
| 函式 |Azure Functions 應用程式中的函式名稱。 |
| Key |如果您想要使用來自另一個訂用帳戶的 Azure 函式，您可以藉由提供金鑰來存取您的函數來執行此動作。 |
| 批次大小上限 |屬性，可讓您設定傳送至 Azure 函式的每個輸出批次大小上限。 輸入是以位元組為單位。 根據預設，此值為262144個位元組（256 KB）。 |
| 批次計數上限  |屬性，可讓您指定每個批次中傳送至 Azure Functions 的最大事件數目。 預設值是 100。 |

Azure 串流分析預期來自函式應用程式的 HTTP 狀態200，以取得已成功處理的批次。

當 Azure 串流分析從 Azure 函式收到413（「HTTP 要求實體太大」）例外狀況時，它會減少傳送到 Azure Functions 的批次大小。 在您的 Azure 函式程式碼中，使用此例外狀況可確保 Azure 串流分析不會傳送過大的批次。 此外，請確定函式中所使用的批次計數和大小上限值，與串流分析入口網站中輸入的值一致。

> [!NOTE]
> 在測試連接期間，串流分析會將空的批次傳送至 Azure Functions，以測試兩者之間的連接是否有效。 請確定您的函式應用程式會處理空的批次要求，以確保測試連接通過。

此外，在某個時間範圍內沒有任何事件登陸的情況下，不會產生任何輸出。 因此，不會呼叫**computeResult**函數。 此行為與內建的視窗型彙總函式一致。

## <a name="custom-metadata-properties-for-output"></a>輸出的自訂中繼資料屬性 

您可以將查詢資料行當做使用者屬性附加至外寄訊息。 這些資料行不會進入裝載。 這些屬性會以輸出訊息上的字典形式呈現。 [索引*鍵*] 是資料行名稱，而 [*值*] 是屬性字典中的資料行值。 除了 [記錄] 和 [陣列] 以外，支援所有串流分析資料類型。  

支援的輸出： 
* 服務匯流排佇列 
* 服務匯流排主題 
* 事件中樞 

在下列範例中，我們會將兩個`DeviceId`欄位`DeviceStatus`和加入至中繼資料。 
* 查詢： `select *, DeviceId, DeviceStatus from iotHubInput`
* 輸出設定：`DeviceId,DeviceStatus`

![屬性資料行](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

下列螢幕擷取畫面顯示在 EventHub 中透過[服務匯流排 Explorer](https://github.com/paolosalvatori/ServiceBusExplorer)檢查的輸出訊息屬性。

![事件自訂屬性](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="system-properties-for-service-bus-queue-and-topic-outputs"></a>服務匯流排佇列和主題輸出的系統屬性 
您可以將查詢資料行當做[系統屬性](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties)附加至傳出的服務匯流排佇列或主題訊息。 這些資料行不會進入裝載，而是會以查詢資料行值填入對應的 BrokeredMessage[系統屬性](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties)。
支援這些系統屬性- `MessageId, ContentType, Label, PartitionKey, ReplyTo, SessionId, CorrelationId, To, ForcePersistence, TimeToLive, ScheduledEnqueueTimeUtc`。
這些資料行的字串值會剖析為對應的系統屬性數值型別，而任何剖析錯誤都會被視為資料錯誤。
此欄位是以 JSON 物件格式提供。 此格式的詳細資料如下所示-
* 以大括弧{}括住。
* 以索引鍵/值組寫入。
* 索引鍵和值必須是字串。
* Key 是系統屬性名稱，而值則是查詢資料行名稱。
* 索引鍵和值是以冒號分隔。
* 每個索引鍵/值組都是以逗號分隔。

這會顯示如何使用這個屬性–

* 查詢： `select *, column1, column2 INTO queueOutput FROM iotHubInput`
* 系統屬性資料行：`{ "MessageId": "column1", "PartitionKey": "column2"}`

這會`MessageId`在服務匯流排佇列訊息`column1`上設定，其值為，而 PartitionKey `column2`是以的值設定。

## <a name="partitioning"></a>資料分割

下表摘要說明分割支援，和每個輸出類型的輸出寫入器數目：

| 輸出類型 | 支援分割 | 資料分割索引鍵  | 輸出寫入器數目 |
| --- | --- | --- | --- |
| Azure Data Lake Store | 是 | 在路徑前置詞模式中使用 {date} 和 {time} 權杖。 選擇日期格式，例如 YYYY/MM/DD、DD/MM/YYYY 或 MM DD-YYYY。 HH 用於時間格式。 | 遵循[完整可平行化查詢](stream-analytics-scale-jobs.md)的輸入資料分割。 |
| Azure SQL Database | 是，必須啟用。 | 以查詢中的 PARTITION BY 子句為基礎。 | 啟用 [繼承資料分割] 選項時，會遵循輸入資料分割來進行[完整可並行的查詢](stream-analytics-scale-jobs.md)。 若要深入瞭解當您將資料載入 Azure SQL Database 時，如何達到更佳的寫入輸送量效能，請參閱[Azure 串流分析輸出至 Azure SQL Database](stream-analytics-sql-output-perf.md)。 |
| Azure Blob 儲存體 | 是 | 在路徑模式中，使用來自事件欄位的 {date} 和 {time} 權杖。 選擇日期格式，例如 YYYY/MM/DD、DD/MM/YYYY 或 MM DD-YYYY。 HH 用於時間格式。 您可依照單一自訂事件屬性 {fieldname} 或 {datetime:\<specifier>} 分割 Blob 輸出。 | 遵循[完整可平行化查詢](stream-analytics-scale-jobs.md)的輸入資料分割。 |
| Azure 事件中心 | 是 | 是 | 根據分割區對齊方式而有所不同。<br /> 當事件中樞輸出的資料分割索引鍵與上游（上一個）查詢步驟同樣地對齊時，寫入器的數目與事件中樞輸出中的磁碟分割數目相同。 每個寫入器都會使用[EventHubSender 類別](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet)，將事件傳送到特定的分割區。 <br /> 當事件中樞輸出的分割區索引鍵未與上游（上一個）查詢步驟對齊時，寫入器的數目與先前步驟中的磁碟分割數目相同。 每個寫入器都會使用**EventHubClient**中的[SendBatchAsync 類別](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet)，將事件傳送至所有輸出資料分割。 |
| Power BI | 否 | 無 | 不適用。 |
| Azure 資料表儲存體 | 是 | 任何輸出資料行。  | 遵循[完整平行化查詢](stream-analytics-scale-jobs.md)的輸入資料分割。 |
| Azure 服務匯流排主題 | 是 | 自動選擇。 分割區數目是根據[服務匯流排 SKU 和大小](../service-bus-messaging/service-bus-partitioning.md)。 資料分割索引鍵是每個分割區的唯一整數值。| 與輸出主題中的分割區數目相同。  |
| Azure 服務匯流排佇列 | 是 | 自動選擇。 分割區數目是根據[服務匯流排 SKU 和大小](../service-bus-messaging/service-bus-partitioning.md)。 資料分割索引鍵是每個分割區的唯一整數值。| 與輸出佇列中的分割區數目相同。 |
| Azure Cosmos DB | 是 | 以查詢中的 PARTITION BY 子句為基礎。 | 遵循[完整平行化查詢](stream-analytics-scale-jobs.md)的輸入資料分割。 |
| Azure Functions | 是 | 以查詢中的 PARTITION BY 子句為基礎。 | 遵循[完整平行化查詢](stream-analytics-scale-jobs.md)的輸入資料分割。 |

您也可以使用`INTO <partition count>`查詢中的（請參閱）子句[INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)來控制輸出寫入器的數目，這有助於達到所需的作業拓撲。 如果您的輸出配接器尚未分割，在某個輸入分割區中缺少資料的情況下，將會導致最多為延遲傳入時間長度的延遲。 在這種情況下，輸出會合並至單一寫入器，這可能會造成管線中的瓶頸。 若要深入瞭解延遲抵達原則，請參閱[Azure 串流分析事件順序考慮](stream-analytics-out-of-order-and-late-events.md)。

## <a name="output-batch-size"></a>輸出批次大小
Azure 串流分析使用可變大小的批次來處理事件和寫入輸出。 串流分析引擎通常不會一次寫入一則訊息，而且會使用批次來提高效率。 當傳入和傳出事件的速率很高時，串流分析會使用較大的批次。 當輸出速率較低時，它會使用較小的批次來降低延遲。

下表說明輸出批次處理的一些考慮：

| 輸出類型 |    訊息大小上限 | 批次大小最佳化 |
| :--- | :--- | :--- |
| Azure Data Lake Store | 請參閱[Data Lake Storage 限制](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-store-limits)。 | 每次寫入作業最多可使用 4 MB。 |
| Azure SQL Database | 可使用最大批次計數進行設定。 10000根據預設，每個單一大量插入的最大和100個數據列。<br />請參閱[AZURE SQL 限制](../sql-database/sql-database-resource-limits.md)。 |  每個批次一開始會大量插入，並具有最大的批次計數。 Batch 會根據 SQL 的可重試錯誤，以一半（直到最小批次計數）分割。 |
| Azure Blob 儲存體 | 請參閱[Azure 儲存體限制](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)。 | Blob 區塊大小上限為 4 MB。<br />Blob bock 計數上限為50000。 |
| Azure 事件中心    | 256 KB 或每則訊息 1 MB。 <br />請參閱[事件中樞限制](../event-hubs/event-hubs-quotas.md)。 |    當輸入/輸出資料分割未對齊時，每個事件都會`EventData`在中個別封裝，並以最大訊息大小的批次傳送。 如果使用[自訂中繼資料屬性](#custom-metadata-properties-for-output)，也會發生這種情況。 <br /><br />  當輸入/輸出分割對齊時，會將多個事件封裝到`EventData`單一實例中，最多可達訊息大小上限，並已傳送。    |
| Power BI | 請參閱[Power BI REST API 限制](https://msdn.microsoft.com/library/dn950053.aspx)。 |
| Azure 資料表儲存體 | 請參閱[Azure 儲存體限制](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)。 | 預設為每個單一交易100個實體。 您可以視需要將它設定為較小的值。 |
| Azure 服務匯流排佇列    | 標準層的每個訊息 256 KB，進階層為1MB。<br /> 請參閱[服務匯流排限制](../service-bus-messaging/service-bus-quotas.md)。 | 針對每個訊息使用單一事件。 |
| Azure 服務匯流排主題 | 標準層的每個訊息 256 KB，進階層為1MB。<br /> 請參閱[服務匯流排限制](../service-bus-messaging/service-bus-quotas.md)。 | 針對每個訊息使用單一事件。 |
| Azure Cosmos DB    | 請參閱[Azure Cosmos DB 限制](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cosmos-db-limits)。 | 批次大小和寫入頻率會根據 Azure Cosmos DB 回應動態調整。 <br /> 串流分析沒有預先決定的限制。 |
| Azure Functions    | | 預設批次大小為262144個位元組（256 KB）。 <br /> 每個批次的預設事件計數為100。 <br /> 批次大小可以設定，並可以在串流分析的[輸出選項](#azure-functions)中增加或減少。

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
