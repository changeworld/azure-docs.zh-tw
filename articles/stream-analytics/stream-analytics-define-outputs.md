---
title: 了解來自 Azure 串流分析的輸出
description: 本文說明 Azure 串流分析中所提供的資料輸出選項，包括適用於分析結果的 Power BI。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/8/2020
ms.openlocfilehash: c4790585d089ab287260f74001a8aa3f1cb7e5f7
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647510"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>了解來自 Azure 串流分析的輸出

本文說明適用於 Azure 串流分析作業的輸出類型。 輸出可讓您存放並儲存串流分析作業的結果。 透過使用輸出資料，您可以對資料進行進一步的商務分析及資料倉儲處理。

在您設計串流分析查詢時，請使用 [INTO 子句](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics)來參考輸出的名稱。 您可以針對每個作業使用單一輸出，或視需要在查詢中提供多個 INTO 子句來針對每個串流作業使用多個輸出。

若要建立、編輯及測試串流分析作業輸出，您可以使用 [Azure 入口網站](stream-analytics-quick-create-portal.md#configure-job-output)、[Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job)、[.NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet)、[REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output)，以及 [Visual Studio](stream-analytics-quick-create-vs.md)。

某些輸出類型支援[資料分割](#partitioning)。 [輸出批次大小](#output-batch-size)會因最佳化輸送量而有所不同。


## <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1

串流分析支援 [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md)。 Azure Data Lake Storage 是容納巨量資料分析工作負載的企業級超大規模存放庫。 您可以使用 Data Lake Store 來存放任何大小、類型和擷取速度的資料，以便進行運作和探究分析。 串流分析需要經過授權，才能存取 Data Lake Storage。

Azure China 21Vianet 和 Azure 德國 (T-Systems International) 區域目前無法從串流分析產生 Azure Data Lake Storage 輸出。

下表是設定 Data Lake Storage Gen 1 輸出的屬性名稱及其描述的清單。   

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 | 此為易記名稱，用於在查詢中將查詢輸出指向 Data Lake Store。 |
| 訂用帳戶 | 包含 Azure Data Lake Storage 帳戶的訂用帳戶。 |
| 帳戶名稱 | 您傳送輸出的 Data Lake Store 帳戶名稱。 您會看到您訂用帳戶中可用 Data Lake Store 帳戶的下拉式清單。 |
| 路徑前置詞模式 | 用來在指定 Data Lake Store 帳戶中寫入檔案的檔案路徑。 您可以指定一或多個 {date} 和 {time} 變數的執行個體：<br /><ul><li>範例 1：folder1/logs/{date}/{time}</li><li>範例 2：folder1/logs/{date}</li></ul><br />所建立資料夾結構的時間戳記遵循 UTC 而非當地時間。<br /><br />如果檔案路徑模式不包含尾端斜線 (/)，則會將檔案路徑中的最後一個模式視為檔案名稱前置詞。 <br /><br />系統會在下列情況下建立新檔案：<ul><li>輸出結構描述中出現變更</li><li>從外部或內部重新啟動作業</li></ul> |
| 日期格式 | 選擇性。 如果前置詞路徑中使用日期權杖，您可以選取組織檔案要用的日期格式。 範例：YYYY/MM/DD |
|時間格式 | 選擇性。 如果前置詞路徑中使用時間權杖，請指定組織檔案要用的時間格式。 目前唯一支援的值為 HH。 |
| 事件序列化格式 | 輸出資料的序列化格式。 支援 JSON、CSV 和 Avro。|
| 編碼 | 如果您使用 CSV 或 JSON 格式，則必須指定編碼。 UTF-8 是目前唯一支援的編碼格式。|
| 分隔符號 | 僅適用於 CSV 序列化。 串流分析可支援多種序列化 CSV 資料常用的分隔符號。 支援的值是逗號、分號、空格、索引標籤和分隔號。|
| [格式] | 僅適用於 JSON 序列化。 **分隔的行**會指定輸出的格式化方式為利用新行分隔每個 JSON 物件。 如果您選取**分隔的行**，JSON 會一次讀取一個物件。 整個內容本身不是有效的 JSON。  **陣列**會指定輸出將會格式化為 JSON 物件的陣列。 只有在作業停止或串流分析已移動到下一個時間範圍時，才會關閉這個陣列。 一般情況下，最好使用分行的 JSON，因為其不需要任何特殊處理，同時仍會寫入輸出檔案。|
| 驗證模式 | 您可以使用[受控識別](stream-analytics-managed-identities-adls.md)或使用者權杖來授權 Data Lake Storage 帳戶的存取權。 授與存取權之後，您可以藉由變更使用者帳戶密碼、刪除此作業的 Data Lake Storage 輸出，或刪除串流分析作業，來撤銷存取權。 |

## <a name="sql-database"></a>SQL Database

如果資料的本質上具備關聯性，或者應用程式所需的內容需由關聯式資料庫提供時，您可以使用 [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) 作為資料輸出。 串流分析作業會寫入至 SQL Database 中的現有資料表。 資料表結構描述必須完全符合作業輸出中的欄位及其類型。 您也可以透過 SQL Database 輸出選項，將 [Azure SQL 資料倉儲](https://azure.microsoft.com/documentation/services/sql-data-warehouse/)指定為輸出。 若要深入了解如何改善寫入輸送量，請參閱[使用 Azure SQL Database 作為輸出的串流分析](stream-analytics-sql-output-perf.md)一文。

您也可以使用 [Azure SQL Database 受控執行個體](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance)作為輸出。 您必須[在 Azure SQL Database 受控執行個體中設定公用端點](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)，然後在 Azure 串流分析中手動設定下列設定。 以附加資料庫執行 SQL Server 的 Azure 虛擬機器，也可以透過手動進行下列設定來支援。

下表列出屬性名稱及其描述以建立 SQL Database 輸出。

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 |此為易記名稱，用於在查詢中將查詢輸出指向這個資料庫。 |
| 資料庫 | 您傳送輸出的目標資料庫名稱。 |
| 伺服器名稱 | SQL Database 伺服器名稱。 針對 Azure SQL Database 受控執行個體，必須指定連接埠 3342。 例如，「sampleserver.public.database.windows.net,3342」 |
| 使用者名稱 | 具有資料庫寫入存取權的使用者名稱。 串流分析只支援 SQL 驗證。 |
| 密碼 | 連線到資料庫的密碼。 |
| Table | 要在其中寫入輸出的資料表名稱。 表格名稱會區分大小寫。 這個資料表的結構描述應該完全符合您作業輸出所產生的欄位數目及其類型。 |
|繼承資料分割配置| 可讓您繼承先前查詢步驟的資料分割配置，以便啟用資料表多個寫入器的完全平行拓撲。 如需詳細資訊，請參閱 [Azure 串流分析輸出至 Azure SQL Database](stream-analytics-sql-output-perf.md)。|
|批次計數上限| 隨每筆大量插入交易傳送的建議記錄數上限。|

有兩張介面卡可讓您從 Azure 串流分析輸出到 Azure Synapse Analytics (先前稱為 SQL 資料倉儲)：SQL Database 和 Azure Synapse。 如果下列任一條件成立，建議您選擇 Azure Synapse Analytics 介面卡，而不是 SQL Database 介面卡：

* **輸送量**：如果您現在或未來的預期輸送量大於 10 MB/秒，請使用 Azure Synapse 輸出選項以獲得更好的效能。

* **輸入分割區**：如果您有八個以上的輸入分割區，請使用 Azure Synapse 輸出選項以獲得更好的相應放大。

## <a name="azure-synapse-analytics-preview"></a>Azure Synapse Analytics (預覽)

[Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics) (先前為 SQL 資料倉儲) 是一種無限制的分析服務，可將企業資料倉儲和巨量資料分析整合在一起。 

Azure 串流分析作業可以輸出至 Azure Synapse Analytics 中的 SQL 集區資料表，而且可以處理高達 200 MB/秒的輸送量速率。這可針對報告和儀表板管理等工作負載，支援最嚴苛的即時分析和忙碌路徑資料處理需求。  

SQL 集區資料表必須先存在，您才能將其當做輸出新增至您的串流分析作業。 資料表結構描述必須符合作業輸出中的欄位及其類型。 

若要使用 Azure Synapse 作為輸出，您必須確定已設定儲存體帳戶。 瀏覽到儲存體帳戶設定以設定儲存體帳戶。 僅允許支援資料表的儲存體帳戶類型：一般用途 V2 和一般用途 V1。 僅選取標準層。 不支援進階層。   

下表列出屬性名稱及其描述以建立 Azure Synapse Analytics 輸出。

|屬性名稱|描述|
|-|-|
|輸出別名 |此為易記名稱，用於在查詢中將查詢輸出指向這個資料庫。 |
|資料庫 |您要傳送輸出的 SQL 集區名稱。 |
|伺服器名稱 |Azure Synapse 伺服器名稱。  |
|使用者名稱 |具有資料庫寫入存取權的使用者名稱。 串流分析只支援 SQL 驗證。 |
|密碼 |連線到資料庫的密碼。 |
|Table  | 要在其中寫入輸出的資料表名稱。 表格名稱會區分大小寫。 這個資料表的結構描述應該完全符合您作業輸出所產生的欄位數目及其類型。|

## <a name="blob-storage-and-azure-data-lake-gen2"></a>Blob 儲存體和 Azure Data Lake Gen2

Data Lake Storage Gen2 讓 Azure 儲存體成為在 Azure 上打造企業 Data Lake 的基礎。 Data Lake Storage Gen2 從一開始就設計為服務數 PB 的資訊，同時可以維持數百 GB 的輸送量，可讓您輕鬆地管理大量資料。Data Lake Storage Gen2 的基本功能是將階層式命名空間新增至 Blob 儲存體。

若要將大量非結構化資料儲存於雲端，Azure Blob 儲存體提供具有成本效益且可擴充的解決方案。 如需 Blob 儲存體及其使用方式的簡介，請參閱[使用 Azure 入口網站上傳、下載及列出 Blob](../storage/blobs/storage-quickstart-blobs-portal.md)。

下表列出屬性名稱及其描述以建立 Blob 或 ADLS Gen2 輸出。

| 屬性名稱       | 描述                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| 輸出別名        | 此為易記名稱，用於在查詢中將查詢輸出指向這個 blob 儲存體。 |
| 儲存體帳戶     | 您傳送輸出的儲存體帳戶名稱。               |
| 儲存體帳戶金鑰 | 與儲存體帳戶相關聯的密碼金鑰。                              |
| 儲存體容器   | 針對儲存在 Azure Blob 服務中 Blob 的邏輯群組。 當您將 blob 上傳至 Blob 服務時，您必須指定該 blob 的容器。 |
| 路徑模式 | 選擇性。 用來在指定容器中寫入 Blob 的檔案路徑模式。 <br /><br /> 在路徑模式中，您可以選擇使用日期時間變數的一或多個執行個體，來指定 blob 的寫入頻率： <br /> {date}、{time} <br /><br />您可以使用自訂 Blob 資料分割，指定事件資料中的一個自訂 {field} 名稱來分割 Blob。 欄位名稱是英數字元，可以包含空格、連字號和底線。 自訂欄位的限制包含下列各項： <ul><li>欄位名稱不區分大小寫。 例如，服務無法分辨資料行 "ID" 和資料行 "id"。</li><li>不允許使用巢狀欄位。 改為在作業查詢中使用別名來「壓平」欄位。</li><li>運算式不能作為欄位名稱使用。</li></ul> <br />這項功能允許在路徑中使用自訂日期/時間格式的指定名稱設定。 自訂日期和時間格式一次只能指定一項，兩側必須加上 {datetime:\<specifier>} 關鍵字。 允許輸入的 \<specifier> 為 yyyy、MM、M、dd、d、HH、H、mm、m、ss 或 s。 {datetime:\<specifier>} 關鍵字可以在路徑中使用多次，以構成自訂日期/時間設定。 <br /><br />範例： <ul><li>範例 1：cluster1/logs/{date}/{time}</li><li>範例 2：cluster1/logs/{date}</li><li>範例 3：cluster1/{client_id}/{date}/{time}</li><li>範例 4：cluster1/{datetime:ss}/{myField}，其中查詢為：SELECT data.myField AS myField FROM Input;</li><li>範例 5：cluster1/year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}</ul><br />所建立資料夾結構的時間戳記遵循 UTC 而非當地時間。<br /><br />檔案命名會使用下列慣例： <br /><br />{路徑前置詞模式}/schemaHashcode_Guid_Number.extension<br /><br />範例輸出檔案︰<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />如需有關這項功能的詳細資訊，請參閱 [Azure 串流分析自訂 Blob 輸出資料分割](stream-analytics-custom-path-patterns-blob-storage-output.md)。 |
| 日期格式 | 選擇性。 如果前置詞路徑中使用日期權杖，您可以選取組織檔案要用的日期格式。 範例：YYYY/MM/DD |
| 時間格式 | 選擇性。 如果前置詞路徑中使用時間權杖，請指定組織檔案要用的時間格式。 目前唯一支援的值為 HH。 |
| 事件序列化格式 | 輸出資料的序列化格式。 支援 JSON、CSV、Avro 和 Parquet。 |
|資料列下限 (僅限 Parquet)|這是每一批次中的資料列數目下限。 針對 Parquet，每個批次都會建立新的檔案。 目前的預設值為 2,000 個資料列，允許的上限為 10,000 個資料列。|
|時間上限 (僅限 Parquet)|每個批次的等候時間上限。 在此時間後，即使未符合資料列下限需求，也會將批次寫入輸出。 目前的預設值為 1 分鐘，允許的上限為 2 小時。 如果您的 Blob 輸出具有路徑模式頻率，則等候時間不得高於分割區時間範圍。|
| 編碼    | 如果您使用 CSV 或 JSON 格式，則必須指定編碼。 UTF-8 是目前唯一支援的編碼格式。 |
| 分隔符號   | 僅適用於 CSV 序列化。 串流分析可支援多種序列化 CSV 資料常用的分隔符號。 支援的值是逗號、分號、空格、索引標籤和分隔號。 |
| [格式]      | 僅適用於 JSON 序列化。 **分隔的行**會指定輸出的格式化方式為利用新行分隔每個 JSON 物件。 如果您選取**分隔的行**，JSON 會一次讀取一個物件。 整個內容本身不是有效的 JSON。 **陣列**會指定輸出將會格式化為 JSON 物件的陣列。 只有在作業停止或串流分析已移動到下一個時間範圍時，才會關閉這個陣列。 一般情況下，最好使用分行的 JSON，因為其不需要任何特殊處理，同時仍會寫入輸出檔案。 |

當您使用 Blob 儲存體作為輸出時，在下列情況下 Blob 中會建立新檔案：

* 如果檔案超過允許的區塊數目上限 (目前為 50,000 個)。 您可以在未達到允許 Blob 大小上限的情況下，達到允許的區塊數目上限。 比方說，如果輸出速率很高，您可看到每個區塊有更多位元組，且檔案大小更大。 如果輸出速率很低，每個區塊都會具有較少的資料，且檔案大小會較小。
* 如果輸出中有結構描述變更，且輸出格式需要固定的結構描述 (CSV 與 Avro)。
* 如果作業已重新啟動，無論是由使用者於外部停止並啟動它，或是於內部重新啟動以進行系統維護或錯誤復原。
* 如果完全分割查詢，則系統會為每個輸出分割區建立新檔案。
* 如果使用者刪除儲存體帳戶的檔案或容器。
* 如果使用路徑前置詞模式將輸出進行時間分割，則會在查詢移至下一個小時的時候使用新的 Blob。
* 如果輸出是由自訂欄位進行分割，系統會針對每個分割區索引鍵建立新的 blob (若不存在的話)。
* 如果輸出是由自訂欄位進行分割，其中分割區索引鍵基數超過 8000，則系統可能會針對每個分割區索引鍵建立新的 Blob。

## <a name="event-hubs"></a>事件中樞

[Azure 事件中樞](https://azure.microsoft.com/services/event-hubs/) 服務是具高延展性的發佈-訂閱事件擷取器。 它每秒可以收集數百萬個事件。 當串流分析作業成為另一個串流作業的輸入時，就會使用事件中樞作為輸出。 如需訊息大小上限和批次大小最佳化的詳細資訊，請參閱[輸出批次大小](#output-batch-size)一節。

您需要幾個參數，以將事件中樞的資料串流設定為輸出。

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 | 此為易記名稱，用於在查詢中將查詢輸出指向這個事件中樞。 |
| 事件中樞命名空間 | 一組訊息實體的容器。 建立新的事件中樞時，也會建立事件中樞命名空間。 |
| 事件中樞名稱 | 事件中樞輸出的名稱。 |
| 事件中樞原則名稱 | 共用存取原則，您可以在事件中樞的 [設定] 索引標籤上建立。每一個共用存取原則都會有名稱、權限 (由您設定) 和存取金鑰。 |
| 事件中樞原則金鑰 | 用來驗證事件中樞命名空間存取權的共用存取金鑰。 |
| 分割區索引鍵資料行 | 選擇性。 資料行包含事件中樞輸出的分割區索引鍵。 |
| 事件序列化格式 | 輸出資料的序列化格式。 支援 JSON、CSV 和 Avro。 |
| 編碼 | 對於 CSV 和 JSON 而言，UTF-8 是目前唯一支援的編碼格式。 |
| 分隔符號 | 僅適用於 CSV 序列化。 串流分析可支援多種以 CSV 格式序列化資料常用的分隔符號。 支援的值是逗號、分號、空格、索引標籤和分隔號。 |
| [格式] | 僅適用於 JSON 序列化。 **分隔的行**會指定輸出的格式化方式為利用新行分隔每個 JSON 物件。 如果您選取**分隔的行**，JSON 會一次讀取一個物件。 整個內容本身不是有效的 JSON。 **陣列**會指定輸出將會格式化為 JSON 物件的陣列。  |
| 屬性資料行 | 選擇性。 以逗號分隔的資料行，必須附加為外寄郵件的使用者屬性，而不是承載。 如需這項功能的詳細資訊，請參閱[輸出的自訂中繼資料屬性](#custom-metadata-properties-for-output)一節。 |

## <a name="power-bi"></a>Power BI

您可以使用 [Power BI](https://powerbi.microsoft.com/) 作為串流分析作業的輸出，來為分析結果提供豐富的視覺體驗。 您可以針對可運作的儀表板、產生報告，以及計量驅動的報告使用這項功能。

Azure China 21Vianet 和 Azure 德國 (T-Systems International) 區域目前無法從串流分析產生 Power BI 輸出。

下表列出設定 Power BI 輸出的屬性名稱及其描述。

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 |提供易記名稱，用於在查詢中將查詢輸出指向這個 Power BI 輸出。 |
| 群組工作區 |若要與其他 Power BI 使用者共用資料，您可以選取 Power BI 帳戶內的群組，若您不希望寫入群組，請選擇 [我的工作區]。 更新現有的群組需要更新 Power BI 驗證。 |
| 資料集名稱 |提供您想要讓 Power BI 輸出使用的資料集名稱。 |
| 資料表名稱 |提供 Power BI 輸出資料集的資料表名稱。 目前，串流分析作業的 Power BI 輸出在資料集內只能有一個資料表。 |
| 授權連接 | 您必須對 Power BI 授權，才能進行輸出設定。 將這個輸出存取權授與 Power BI 儀表板之後，您可以藉由變更使用者帳戶密碼、刪除作業輸出，或刪除串流分析作業，來撤銷存取權。 | 

如需設定 Power BI 輸出和儀表板的逐步解說，請參閱 [Azure 串流分析與 Power BI](stream-analytics-power-bi-dashboard.md) 教學課程。

> [!NOTE]
> 請勿在 Power BI 儀表板中明確建立資料集和資料表。 當作業開始並將輸出提取至 Power BI 時，就會自動填入資料集和資料表。 如果作業查詢沒有產生任何結果，則不會建立資料集和資料表。 如果 Power BI 已經具有與串流分析作業中提供的名稱相同的資料集和資料表，則會覆寫現有的資料。
>

### <a name="create-a-schema"></a>建立結構描述
如果 Power BI 資料集和資料表結構描述尚不存在，則 Azure 串流分析會為使用者建立。 在其他情況下，則會以新的值更新資料表。 目前，資料集內只能有一個資料表存在。 

Power BI 是使用先進先出(FIFO) 保留原則。 將會在資料表中收集資料，直到達到 200,000 個資料列為止。

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>將資料類型從串流分析轉換至 Power BI
如果輸出結構描述變更，則 Azure 串流分析會在執行階段動態更新資料模型。 所有資料行名稱變更、資料行類型變更以及資料行新增或移除都會加以追蹤。

如果 Power BI 資料集和資料表不存在，此資料表包含從[串流分析資料類型](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)至 Power BI [實體資料模型 (EDM) 類型](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model)的資料類型轉換。

從串流分析 | 至 Power BI
-----|-----
BIGINT | Int64
nvarchar(max) | String
Datetime | Datetime
FLOAT | Double
記錄陣列 | 字串類型、常數值 "IRecord" 或 "IArray"

### <a name="update-the-schema"></a>更新結構描述
串流分析會根據輸出中的第一組事件來推斷資料模型結構描述。 之後會視需要更新資料模型結構描述，以容納原始結構描述放不下的連入事件。

避免 `SELECT *` 查詢，以防止跨越資料列的動態結構描述更新。 除了潛在的效能影響以外，可能導致結果所花費的時間不定。 選取必須顯示在 Power BI 儀表板上的確切欄位。 此外，資料值應該與所選的資料類型相符。


先前/目前 | Int64 | String | Datetime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | String | String | Double
Double | Double | String | String | Double
String | String | String | String | String 
Datetime | String | String |  Datetime | String

## <a name="table-storage"></a>表格儲存體

[Azure 資料表儲存體](../storage/common/storage-introduction.md) 提供高可用性且可大幅擴充的儲存體，可讓應用程式自動調整來滿足使用者需求。 資料表儲存體是 Microsoft 的 NoSQL 索引鍵/屬性存放區，您可以針對結構化資料使用，但結構描述的限制較少。 使用 Azure 資料表儲存資料時，資料可長期儲存而且調閱方便。

下表列出屬性名稱及其描述以建立表格輸出。

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 |此為易記名稱，用於在查詢中將查詢輸出指向這個資料表儲存體。 |
| 儲存體帳戶 |您傳送輸出的儲存體帳戶名稱。 |
| 儲存體帳戶金鑰 |與儲存體帳戶相關聯的存取金鑰。 |
| 資料表名稱 |資料表的名稱。 如果資料表不存在，則會建立資料表。 |
| 資料分割索引鍵 |包含分割區索引鍵的輸出資料行名稱。 在構成實體主索引鍵第一個部分的資料表內，分割區索引鍵是資料分割的唯一識別碼。 大小最高為 1 KB 的字串值。 |
| 列索引鍵 |其中包含資料列索引鍵的輸出資料行名稱。 資料列索引鍵是分割區內實體的唯一識別碼。 其可構成實體主索引鍵的第二個部分。 資料列索引鍵是大小可以高達 1 KB 的字串值。 |
| 批次大小 |批次作業的記錄數目。 預設值 (100)通常足以應付大部分的作業。 如需修改此設定的詳細資料，請參閱[資料表批次作業規格](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table.tablebatchoperation)。 |

## <a name="service-bus-queues"></a>服務匯流排佇列

如果有一或多個競爭取用者，[服務匯流排佇列](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)會採取 FIFO 訊息傳遞機制。 一般來說，接收者會以其新增至佇列的時態順序來接收和處理訊息。 每則訊息只會由一個訊息取用者接收和處理。

在[相容性層級 1.2](stream-analytics-compatibility-level.md) 中，Azure 串流分析使用[進階訊息佇列通訊協定 (AMQP)](../service-bus-messaging/service-bus-amqp-overview.md) 訊息通訊協定來寫入服務匯流排佇列和主題。 透過開放式標準通訊協定，AMQP 可讓您打造一個跨平台的混合式應用程式。

下表列出屬性名稱及其描述以建立佇列輸出。

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 |此為易記名稱，用於在查詢中將查詢輸出指向這個服務匯流排佇列。 |
| 服務匯流排命名空間 |一組訊息實體的容器。 |
| Queue name |服務匯流排佇列的名稱。 |
| 佇列原則名稱 |當您建立佇列時，您也可以在佇列的 [設定] 索引標籤上建立共用存取原則。每一個共用存取原則都會有名稱、權限 (由您設定) 和存取金鑰。 |
| 佇列原則金鑰 |用來驗證服務匯流排命名空間之存取權的共用存取金鑰。 |
| 事件序列化格式 |輸出資料的序列化格式。 支援 JSON、CSV 和 Avro。 |
| 編碼 |對於 CSV 和 JSON 而言，UTF-8 是目前唯一支援的編碼格式。 |
| 分隔符號 |僅適用於 CSV 序列化。 串流分析可支援多種以 CSV 格式序列化資料常用的分隔符號。 支援的值是逗號、分號、空格、索引標籤和分隔號。 |
| [格式] |僅適用於 JSON 類型。 **分隔的行**會指定輸出的格式化方式為利用新行分隔每個 JSON 物件。 如果您選取**分隔的行**，JSON 會一次讀取一個物件。 整個內容本身不是有效的 JSON。 **陣列**會指定輸出將會格式化為 JSON 物件的陣列。 |
| 屬性資料行 | 選擇性。 以逗號分隔的資料行，必須附加為外寄郵件的使用者屬性，而不是承載。 如需這項功能的詳細資訊，請參閱[輸出的自訂中繼資料屬性](#custom-metadata-properties-for-output)一節。 |
| 系統屬性資料行 | 選擇性。 系統屬性的索引鍵值組，以及必須附加至外寄郵件而非承載的對應資料行名稱。 如需這項功能的詳細資訊，請參閱[服務匯流排佇列和主題輸出的系統屬性](#system-properties-for-service-bus-queue-and-topic-outputs)一節  |

分割區數目是[根據服務匯流排 SKU 和大小](../service-bus-messaging/service-bus-partitioning.md)。 分割區索引鍵是每個分割區的唯一整數值。

## <a name="service-bus-topics"></a>服務匯流排主題
服務匯流排佇列提供從傳送者到接收者的一對一通訊方法。 [服務匯流排主題](https://msdn.microsoft.com/library/azure/hh367516.aspx)提供一對多的通訊形式。

下表列出屬性名稱及其描述以建立服務匯流排主題輸出。

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 |此為易記名稱，用於在查詢中將查詢輸出指向這個服務匯流排主題。 |
| 服務匯流排命名空間 |一組訊息實體的容器。 建立新的事件中樞時，也會建立服務匯流排命名空間。 |
| 主題名稱 |主題為訊息實體，類似於事件中樞和佇列。 其設計目的是要從裝置和服務收集事件串流。 建立主題時也會給予其特定名稱。 除非已建立訂用帳戶，否則無法使用傳送至主題的訊息，所以請確保該主題內有一或多個訂用帳戶。 |
| 主題原則名稱 |當您建立服務匯流排主題時，您也可以在主題的 [設定] 索引標籤上建立共用存取原則。每一個共用存取原則都會有名稱、權限 (由您設定) 和存取金鑰。 |
| 主題原則金鑰 |用來驗證服務匯流排命名空間之存取權的共用存取金鑰。 |
| 事件序列化格式 |輸出資料的序列化格式。 支援 JSON、CSV 和 Avro。 |
| 編碼 |如果您使用 CSV 或 JSON 格式，則必須指定編碼。 UTF-8 是目前唯一支援的編碼格式。 |
| 分隔符號 |僅適用於 CSV 序列化。 串流分析可支援多種以 CSV 格式序列化資料常用的分隔符號。 支援的值是逗號、分號、空格、索引標籤和分隔號。 |
| 屬性資料行 | 選擇性。 以逗號分隔的資料行，必須附加為外寄郵件的使用者屬性，而不是承載。 如需這項功能的詳細資訊，請參閱[輸出的自訂中繼資料屬性](#custom-metadata-properties-for-output)一節。 |
| 系統屬性資料行 | 選擇性。 系統屬性的索引鍵值組，以及必須附加至外寄郵件而非承載的對應資料行名稱。 如需這項功能的詳細資訊，請參閱[服務匯流排佇列和主題輸出的系統屬性](#system-properties-for-service-bus-queue-and-topic-outputs)一節 |

分割區數目是[根據服務匯流排 SKU 和大小](../service-bus-messaging/service-bus-partitioning.md)。 分割區索引鍵是每個分割區的唯一整數值。

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) 是全域散發的資料庫服務，在全球各地提供無限的彈性調整、透過無從驗證結構描述資料模型的豐富查詢和自動索引。 若要了解串流分析的 Azure Cosmos DB 容器選項，請參閱[以 Azure Cosmos DB 作為輸出的串流分析](stream-analytics-documentdb-output.md)一文。

Azure China 21Vianet 和 Azure 德國 (T-Systems International) 區域目前無法從串流分析產生 Azure Cosmos DB 輸出。

> [!Note]
> 此時 Azure 串流分析僅支援使用 SQL API 連線至 Azure Cosmos DB。
> 尚不支援其他 Azure Cosmos DB API。 如果您將 Azure Stream Analytics 指向使用其他 API 建立的 Azure Cosmos DB 帳戶，可能無法正確儲存資料。

下表描述用來建立 Azure Cosmos DB 輸出的屬性。

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 | 在您的串流分析查詢中參照此輸出時所用的別名。 |
| 接收 | Azure Cosmos DB。 |
| 匯入選項 | 選擇 [從您的訂用帳戶選取 Cosmos DB]，或 [手動提供 Cosmos DB 設定]。
| 帳戶識別碼 | Azure Cosmos DB 帳戶的名稱或端點 URI。 |
| 帳戶金鑰 | Azure Cosmos DB 帳戶的共用存取金鑰。 |
| 資料庫 | Azure Cosmos DB 資料庫名稱。 |
| 容器名稱 | 要使用的容器名稱，必須存在於 Cosmos DB 中。 範例：  <br /><ul><li> _MyContainer_：名為 "MyContainer" 的容器必須存在。</li>|
| 文件識別碼 |選擇性。 輸出事件中的欄位名稱會用來指定主索引鍵，此為插入或更新作業的依據。

## <a name="azure-functions"></a>Azure Functions
Azure Functions 是無伺服器計算服務，您可以用來依需求執行程式碼，無需明確佈建或管理基礎結構。 可讓您實作在 Azure 或合作夥伴服務中發生事件所觸發的程式碼。 Azure Functions 回應觸發程序的這個功能使其自然輸出 Azure 串流分析。 此輸出配接器可讓使用者將串流分析連接至 Azure Functions，然後執行指令碼或程式碼片段，以回應各種不同的事件。

Azure China 21Vianet 和 Azure 德國 (T-Systems International) 區域目前無法從串流分析產生 Azure Functions 輸出。

Azure 串流分析會透過 HTTP 觸發程序叫用 Azure Functions。 Azure Functions 輸出配接器可搭配下列可設定屬性使用：

| 屬性名稱 | 描述 |
| --- | --- |
| 函式應用程式 |Azure Functions 應用程式的名稱。 |
| 函式 |Azure Functions 應用程式中函式的名稱。 |
| Key |如果您想要使用另一個訂用帳戶中的 Azure Function，可以藉由提供存取函式的金鑰來達到這個目的。 |
| 批次大小上限 |屬性可讓您針對傳送到您 Azure Function 的每個輸出批次，設定大小上限。 輸入是以位元組為單位。 根據預設，此值是 262,144 個位元組 (256 KB)。 |
| 批次計數上限  |屬性可讓您在傳送至 Azure Functions 的每個批次中，指定事件數目上限。 預設值是 100。 |

Azure 串流分析針對已成功處理的批次，預期來自 Functions 應用程式是 HTTP 狀態 200。

當 Azure 串流分析從 Azure 函式收到 413 (http 要求實體太大) 例外狀況時，會縮減傳送至 Azure Functions 的批次大小。 在 Azure 函式程式碼中，使用這個例外狀況可確保 Azure 串流分析不會傳送過大的批次。 同時，請確認函式中使用的最大批次計數和大小值都符合串流分析入口網站中輸入的值。

> [!NOTE]
> 在測試連線期間，串流分析會將空的批次傳送至 Azure Functions，以測試兩者之間的連線是否運作。 請確定您的 Functions 應用程式會處理空的批次要求，以確保測試連線通過。

此外，如果在某個時間範圍內沒有登陸任何事件，則不會產生任何輸出。 如此一來，就不會呼叫 **computeResult** 函式。 此行為與內建的視窗型彙總函式一致。

## <a name="custom-metadata-properties-for-output"></a>輸出的自訂中繼資料屬性 

您可以將查詢資料行當做使用者屬性附加至外寄郵件。 這些資料行不會進入承載。 屬性會以輸出訊息上的字典形式呈現。 「索引鍵」是資料行名稱，「值」是屬性字典中的資料行值。 除了「記錄」和「陣列」以外，支援所有串流分析資料類型。  

支援的輸出： 
* 服務匯流排佇列 
* 服務匯流排主題 
* 事件中樞 

在下列範例中，我們會將兩個欄位 `DeviceId` 和 `DeviceStatus` 新增至中繼資料。 
* 查詢： `select *, DeviceId, DeviceStatus from iotHubInput`
* 輸出組態：`DeviceId,DeviceStatus`

![屬性資料行](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

下列螢幕擷取畫面顯示在 EventHub 中透過 [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) 檢查的輸出訊息屬性。

![事件自訂屬性](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="system-properties-for-service-bus-queue-and-topic-outputs"></a>服務匯流排佇列和主題輸出的系統屬性 
您可以將查詢資料行當做[系統屬性](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties)附加至外寄服務匯流排佇列或主題訊息。 這些資料行不會進入承載，而是以查詢資料行值填入的對應 BrokeredMessage [系統屬性](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties)。
支援這些系統屬性 - `MessageId, ContentType, Label, PartitionKey, ReplyTo, SessionId, CorrelationId, To, ForcePersistence, TimeToLive, ScheduledEnqueueTimeUtc`。
這些資料行的字串值會剖析為對應的系統屬性值類型，而任何剖析失敗都會被視為資料錯誤。
此欄位是以 JSON 物件格式提供。 此格式的詳細資料如下所示 -
* 以大括號 {} 括住。
* 以索引鍵/值組寫入。
* 索引鍵和值必須是字串。
* 索引鍵是系統屬性名稱，而值則是查詢資料行名稱。
* 索引鍵和值以冒號分隔。
* 每個索引鍵/值組都以逗號分隔。

這會顯示如何使用這個屬性 -

* 查詢： `select *, column1, column2 INTO queueOutput FROM iotHubInput`
* 系統屬性資料行：`{ "MessageId": "column1", "PartitionKey": "column2"}`

這會使用 `column1` 的值來設定服務匯流排佇列訊息上的 `MessageId`，以及使用 `column2` 的值來設定 PartitionKey。

## <a name="partitioning"></a>資料分割

下表摘要說明分割支援，和每個輸出類型的輸出寫入器數目：

| 輸出類型 | 支援分割 | 資料分割索引鍵  | 輸出寫入器數目 |
| --- | --- | --- | --- |
| Azure Data Lake Store | 是 | 在路徑前置詞模式中使用 {date} 和 {time} 權杖。 選擇日期格式，例如 YYYY/MM/DD、DD/MM/YYYY、MM-DD-YYYY。 HH 用於時間格式。 | 遵循[完整可平行化查詢](stream-analytics-scale-jobs.md)的輸入資料分割。 |
| Azure SQL Database | 是，必須啟用。 | 以查詢中的 PARTITION BY 子句為依據。 | 啟用 [繼承資料分割] 選項時，會遵循[完整可平行化查詢](stream-analytics-scale-jobs.md)的輸入資料分割。 若要深入了解如何在將資料載入 Azure SQL Database 時達到更佳寫入輸送量效能，請參閱 [Azure 串流分析輸出至 Azure SQL Database](stream-analytics-sql-output-perf.md)。 |
| Azure Blob 儲存體 | 是 | 使用來自路徑模式中事件欄位的 {date} 和 {time} 權杖。 選擇日期格式，例如 YYYY/MM/DD、DD/MM/YYYY、MM-DD-YYYY。 HH 用於時間格式。 您可依照單一自訂事件屬性 {fieldname} 或 {datetime:\<specifier>} 分割 Blob 輸出。 | 遵循[完整可平行化查詢](stream-analytics-scale-jobs.md)的輸入資料分割。 |
| Azure 事件中心 | 是 | 是 | 根據分割區對齊方式而有所不同。<br /> 當事件中樞輸出中的分割區索引鍵與上游 (先前的) 查詢步驟同等對齊時，寫入器的數目將會和事件中樞輸出中的分割區數目相同。 每個寫入器都會使用 [EventHubSender 類別](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet)來將事件傳送至特定的分割區。 <br /> 當事件中樞輸出中的分割區索引鍵沒有與上游 (先前的) 查詢步驟同等對齊時，寫入器的數目將會和先前步驟中的分割區數目相同。 每個寫入器會使用 **EventHubClient** 中的 [SendBatchAsync 類別](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet)，將事件傳送至所有輸出分割區。 |
| Power BI | 否 | None | 不適用。 |
| Azure 資料表儲存體 | 是 | 任何輸出資料行。  | 遵循[完整平行化查詢](stream-analytics-scale-jobs.md)的輸入資料分割。 |
| Azure 服務匯流排主題 | 是 | 自動選擇。 分割區數目是根據[服務匯流排 SKU 和大小](../service-bus-messaging/service-bus-partitioning.md)。 分割區索引鍵是每個分割區的唯一整數值。| 與輸出主題中的分割區數目相同。  |
| Azure 服務匯流排佇列 | 是 | 自動選擇。 分割區數目是根據[服務匯流排 SKU 和大小](../service-bus-messaging/service-bus-partitioning.md)。 分割區索引鍵是每個分割區的唯一整數值。| 與輸出佇列中的分割區數目相同。 |
| Azure Cosmos DB | 是 | 以查詢中的 PARTITION BY 子句為依據。 | 遵循[完整平行化查詢](stream-analytics-scale-jobs.md)的輸入資料分割。 |
| Azure Functions | 是 | 以查詢中的 PARTITION BY 子句為依據。 | 遵循[完整平行化查詢](stream-analytics-scale-jobs.md)的輸入資料分割。 |

您也可以使用您查詢中的 `INTO <partition count>` (請參閱 [INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)) 子句來控制輸出寫入器的數目，有助於達成所需的作業拓撲。 如果您的輸出配接器尚未分割，在某個輸入分割區中缺少資料的情況下，將會導致最多為延遲傳入時間長度的延遲。 在這種情況下，輸出會合併為單一寫入器，這可能會在您的管線中產生瓶頸。 若要深入了解延遲傳入原則，請參閱 [Azure 串流分析事件的順序考量](stream-analytics-out-of-order-and-late-events.md)。

## <a name="output-batch-size"></a>輸出批次大小
Azure 串流分析使用可變大小批次來處理事件及寫入至輸出。 串流分析引擎通常不會一次撰寫一個訊息，並會使用批次以提升效率。 當傳入和傳出事件的速率很高時，串流分析會使用較大的批次。 當輸出速率較低時，它會使用較小的批次來降低延遲。

下表說明輸出批次處理的部分考量：

| 輸出類型 |    訊息大小上限 | 批次大小最佳化 |
| :--- | :--- | :--- |
| Azure Data Lake Store | 請參閱 [Data Lake Storage 限制](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-store-limits)。 | 每個寫入作業最多使用 4 MB。 |
| Azure SQL Database | 可使用最大批次計數進行設定。 每個單一大量插入預設為上限 10,000 個資料列和下限 100 個資料列。<br />請參閱 [Azure SQL 限制](../sql-database/sql-database-resource-limits.md)。 |  每個批次一開始會以上限批次計數大量插入。 批次根據 SQL 的可重試錯誤分割為一半 (直到批次計數下限)。 |
| Azure Blob 儲存體 | 請參閱 [Azure 儲存體限制](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)。 | Blob 區塊大小上限為 4 MB。<br />Blob 區塊計數上限為 50000。 |
| Azure 事件中心    | 每個訊息 256 KB 或 1 MB。 <br />請參閱[事件中樞限制](../event-hubs/event-hubs-quotas.md)。 |    當輸入/輸出資料分割沒有對齊時，每個事件會個別封裝於 `EventData` 中，並在以訊息大小上限為限的情況下，以批次的方式傳送。 如果使用[自訂中繼資料屬性](#custom-metadata-properties-for-output)，也會發生這種情況。 <br /><br />  當輸入/輸出資料分割有對齊時，多個事件會封裝為單一 `EventData` 執行個體，以訊息大小上限為限並傳送。    |
| Power BI | 請參閱 [Power BI Rest API 限制](https://msdn.microsoft.com/library/dn950053.aspx)。 |
| Azure 資料表儲存體 | 請參閱 [Azure 儲存體限制](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)。 | 預設為每個單一交易 100 個實體。 您可以視需要將其設定為較小的值。 |
| Azure 服務匯流排佇列    | 標準層的每個訊息為 256 KB，進階層的每個訊息為 1MB。<br /> 請參閱[服務匯流排限制](../service-bus-messaging/service-bus-quotas.md)。 | 使用每個訊息單一事件。 |
| Azure 服務匯流排主題 | 標準層的每個訊息為 256 KB，進階層的每個訊息為 1MB。<br /> 請參閱[服務匯流排限制](../service-bus-messaging/service-bus-quotas.md)。 | 使用每個訊息單一事件。 |
| Azure Cosmos DB    | 請參閱 [Azure Cosmos DB 限制](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cosmos-db-limits)。 | 批次大小和寫入頻率會依 Azure Cosmos DB 回應進行動態調整。 <br /> 串流分析不會做任何預先決定的限制。 |
| Azure Functions    | | 預設批次大小為 262,144 個位元組 (256 KB)。 <br /> 每個批次的預設事件計數為 100。 <br /> 批次大小可以設定，並可以在串流分析的[輸出選項](#azure-functions)中增加或減少。

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
