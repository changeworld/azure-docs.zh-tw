---
title: Azure 資料資源管理器與 Azure 資料工廠集成
description: 在本主題中，將 Azure 資料資源管理器與 Azure 資料工廠集成，以使用複製、查找和命令活動
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tomersh26
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: bb08cf4db45a378b35a8245eadd56a2ab3e48bab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293618"
---
# <a name="integrate-azure-data-explorer-with-azure-data-factory"></a>將 Azure 資料資源管理器與 Azure 資料工廠集成

[Azure 資料工廠](/azure/data-factory/)（ADF） 是一種基於雲的資料整合服務，允許您集成不同的資料存儲並執行對資料的活動。 ADF 允許您創建資料驅動的工作流，以便協調和自動化資料移動和資料轉換。 Azure 資料資源管理器是 Azure 資料工廠[中受支援的資料存儲](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)之一。 

## <a name="azure-data-factory-activities-for-azure-data-explorer"></a>Azure 資料工廠活動，用於 Azure 資料資源管理器

Azure 資料資源管理器使用者可以使用與 Azure 資料工廠的各種集成：

### <a name="copy-activity"></a>複製活動
 
Azure 資料工廠複製活動用於在資料存儲之間傳輸資料。 Azure 資料資源管理器支援為源，其中資料從 Azure 資料資源管理器複製到任何受支援的資料存儲，以及從任何受支援的資料存儲複製到 Azure 資料資源管理器的接收器。 有關詳細資訊，請參閱使用[Azure 資料工廠將資料複製到 Azure 資料資源管理器或從 Azure 資料資源管理器](/azure/data-factory/connector-azure-data-explorer)中複製資料。 有關詳細演練，請參閱[將 Azure 資料工廠載入資料到 Azure 資料資源管理器](data-factory-load-data.md)。
Azure 資料資源管理器受 Azure IR（集成運行時）的支援，在 Azure 中複製資料時使用，在自託管 IR 中使用，用於將資料從/複製到本地的資料存儲或具有存取控制的網路中（如 Azure 虛擬網路）。 有關詳細資訊，請參閱要使用的[IR](/azure/data-factory/concepts-integration-runtime#determining-which-ir-to-use)
 
> [!TIP]
> 使用複製活動並創建**連結服務**或**資料集**時，請選擇資料存儲**Azure 資料資源管理器 （Kusto），** 而不是舊資料存儲**Kusto**。  

### <a name="lookup-activity"></a>查找活動
 
查找活動用於在 Azure 資料資源管理器上執行查詢。 查詢的結果將作為查找活動的輸出返回，並且可用於管道中的下一個活動，如[ADF 查找文檔中](/azure/data-factory/control-flow-lookup-activity#use-the-lookup-activity-result-in-a-subsequent-activity)所述。  
除了 5，000 行和 2 MB 的回應大小限制外，該活動還有 1 小時的查詢超時限制。

### <a name="command-activity"></a>命令活動

命令活動允許執行 Azure 資料資源管理器[控制項命令](/azure/kusto/concepts/#control-commands)。 與查詢不同，控制項命令可能會修改資料或中繼資料。 某些控制命令旨在將資料引入 Azure 資料資源管理器，使用 或`.ingest``.set-or-append`） 等命令或使用 命令（如`.export`） 將資料複製到外部資料存儲。
有關命令活動的詳細演練，請參閱[使用 Azure 資料工廠命令活動運行 Azure 資料資源管理器控制項命令](data-factory-command-activity.md)。  有時，使用控制命令複製資料可以比複製活動更快、更便宜。 要確定何時使用命令活動與複製活動，請參閱[在複製資料時在"複製"和"命令"活動之間進行選擇](#select-between-copy-and-azure-data-explorer-command-activities-when-copy-data)。

### <a name="copy-in-bulk-from-a-database-template"></a>從資料庫範本批量複製

[通過使用 Azure 資料工廠範本批量從資料庫複製到 Azure 資料資源管理器](data-factory-template.md)是預定義的 Azure 資料工廠管道。 該範本用於為每個資料庫或每個表創建多個管道，以加快資料複製。 

### <a name="mapping-data-flows"></a>對應資料流程 

[Azure 資料工廠映射資料流程](/azure/data-factory/concepts-data-flow-overview)是視覺化設計的資料轉換，允許資料工程師無需編寫代碼即可開發圖形資料轉換邏輯。 要創建資料流程並將資料引入 Azure 資料資源管理器，請使用以下方法：

1. 創建[映射資料流程](/azure/data-factory/data-flow-create)。
1. [將資料匯出到 Azure Blob](/azure/data-factory/data-flow-sink)。 
1. 定義[事件網格](/azure/data-explorer/ingest-data-event-grid)或[ADF 複製活動](/azure/data-explorer/data-factory-load-data)以將資料引入 Azure 資料資源管理器。

## <a name="select-between-copy-and-azure-data-explorer-command-activities-when-copy-data"></a>複製資料時在"複製"和"Azure 資料資源管理器"命令活動之間進行選擇 

本節將説明您選擇適合資料複製需求的正確活動。

將資料從 Azure 資料資源管理器複製到 Azure 資料資源管理器時，Azure 資料工廠中有兩個可用選項：
* 複製活動。
* Azure 資料資源管理器命令活動，它執行在 Azure 資料資源管理器中傳輸資料的控制項命令之一。 

### <a name="copy-data-from-azure-data-explorer"></a>從 Azure 資料資源管理器複製資料
  
可以使用複製活動或[`.export`](/azure/kusto/management/data-export/)命令從 Azure 資料資源管理器複製資料。 該`.export`命令執行查詢，然後匯出查詢的結果。 

有關複製活動和`.export`命令的比較，請參閱下表，以便從 Azure 資料資源管理器複製資料。

| | 複製活動 | .匯出命令 |
|---|---|---|
| **流描述** | ADF 在 Kusto 上執行查詢，處理結果，並將其發送到目標資料存儲。 <br>（**ADX > ADF >接收器資料存儲**） | ADF 向`.export`Azure 資料資源管理器發送控制命令，該資源管理器執行該命令，並將資料直接發送到目標資料存儲。 <br>**（ADX >接收器資料存儲**） |
| **支援的目標資料存儲** | 各種[受支援的資料存儲](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLSv2，Azure Blob，SQL 資料庫 |
| **效能** | 集中 | <ul><li>分散式（預設），同時從多個節點匯出資料</li><li>更快，COGS 效率高。</li></ul> |
| **伺服器限制** | 可以擴展/禁用[查詢限制](/azure/kusto/concepts/querylimits)。 預設情況下，ADF 查詢包含： <ul><li>大小限制為 500，000 條記錄或 64 MB。</li><li>時間限制為 10 分鐘。</li><li>`noTruncation`設置為 false。</li></ul> | 預設情況下，擴展或禁用查詢限制： <ul><li>大小限制已禁用。</li><li>伺服器超時延長到 1 小時。</li><li>`MaxMemoryConsumptionPerIterator`並`MaxMemoryConsumptionPerQueryPerNode`擴展到最大值（5 GB，總實體記憶體/2）。</li></ul>

> [!TIP]
> 如果複製目標是`.export`命令支援的資料存儲之一，並且"複製活動"功能對您的需求都至關重要，請選擇該`.export`命令。

### <a name="copying-data-to-azure-data-explorer"></a>將資料複製到 Azure 資料資源管理器

可以使用複製活動或引入命令（如`.set``.replace)``.ingest``.set-or-append``.set-or-replace`[從查詢（、、、](/azure/kusto/management/data-ingestion/ingest-from-query)和[從存儲（）引入](/azure/kusto/management/data-ingestion/ingest-from-storage)來將資料複製到 Azure 資料資源管理器。 

有關複製活動的比較以及將資料複製到 Azure 資料資源管理器的引入命令的比較，請參閱下表。

| | 複製活動 | 從查詢內嵌<br> `.set-or-append` / `.set-or-replace` / `.set` / `.replace` | 從儲存體內嵌 <br> `.ingest` |
|---|---|---|---|
| **流描述** | ADF 從來源資料存儲獲取資料，將其轉換為表格格式，並執行所需的架構映射更改。 然後，ADF 將資料上載到 Azure Blob，將其拆分為塊，然後將 blob 下載到 ADX 表中。 <br> （**來源資料存儲> ADF > AX > Azure blob**） | 這些命令可以執行查詢或`.show`命令，並將查詢的結果引入到表中 **（ADX > ADX**）。 | 此命令通過從一個或多個雲存儲專案"提取"資料來將資料引入表中。 |
| **支援的來源資料存儲** |  [多種選項](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLS 第 2 代、Azure Blob、SQL（使用sql_request外掛程式）、Cosmos（使用cosmosdb_sql_request外掛程式）和任何其他提供 HTTP 或 Python API 的資料存儲。 | 檔案系統、Azure Blob 存儲、ADLS 第 1 代、ADLS 第 2 代 |
| **效能** | 引入是排隊和管理的，通過提供負載平衡、重試和錯誤處理，確保小尺寸引入並確保高可用性。 | <ul><li>這些命令不是為輸入大容量資料而設計的。</li><li>工作如預期，更便宜。 但對於生產方案以及流量速率和資料大小較大時，請使用 Copy 活動。</li></ul> |
| **伺服器限制** | <ul><li>無大小限制。</li><li>最大超時限制：每個引入的 blob 1 小時。 |<ul><li>查詢部件上只有大小限制，可以通過指定 來跳過該限制`noTruncation=true`。</li><li>最大超時限制：1 小時。</li></ul> | <ul><li>無大小限制。</li><li>最大超時限制：1 小時。</li></ul>|

> [!TIP]
> * 將資料從 ADF 複製到 Azure 資料資源管理器時`ingest from query`，請使用這些命令。  
> * 對於大型資料集（>1GB），請使用複製活動。  

## <a name="required-permissions"></a>所需的權限

下表列出了與 Azure 資料工廠集成中各個步驟所需的許可權。

| 步驟 | 作業 | 最低權限等級 | 注意 |
|---|---|---|---|
| **創建連結服務** | 資料庫導航 | *資料庫檢視器* <br>應授權使用 ADF 登錄的使用者讀取資料庫中繼資料。 | 使用者可以手動提供資料庫名稱。 |
| | [測試連接] | *資料庫監視器*或*表引入器* <br>應授權服務主體執行資料庫級`.show`命令或表級引入。 | <ul><li>TestConnect 驗證與群集的連接，而不是與資料庫的連接。 即使資料庫不存在，它也能成功。</li><li>表管理員許可權不足。</li></ul>|
| **建立資料集** | 表導航 | *資料庫監視器* <br>必須授權使用 ADF 登錄的使用者執行資料庫級`.show`命令。 | 使用者可以手動提供表名稱。|
| **創建資料集**或**複製活動** | 預覽資料 | *資料庫檢視器* <br>必須授權服務主體讀取資料庫中繼資料。 | | 
|   | 導入架構 | *資料庫檢視器* <br>必須授權服務主體讀取資料庫中繼資料。 | 當 ADX 是表格到表格副本的來源時，ADF 將自動導入架構，即使使用者未顯式導入架構也是如此。 |
| **ADX 作為接收器** | 創建按名稱列映射 | *資料庫監視器* <br>必須授權服務主體執行資料庫級`.show`命令。 | <ul><li>所有必需操作都將使用*表引入器*。</li><li> 某些可選操作可能會失敗。</li></ul> |
|   | <ul><li>在表上創建 CSV 映射</li><li>刪除映射</li></ul>| *表引入器*或*資料庫管理員* <br>必須授權服務主體對表進行更改。 | |
|   | 擷取資料 | *表引入器*或*資料庫管理員* <br>必須授權服務主體對表進行更改。 | | 
| **ADX 作為源** | 執行查詢 | *資料庫檢視器* <br>必須授權服務主體讀取資料庫中繼資料。 | |
| **庫托命令** | | 根據每個命令的權限等級。 |

## <a name="performance"></a>效能 

如果 Azure 資料資源管理器是源，並且使用包含查詢的查找、複製或命令活動，請參閱[查詢](/azure/kusto/query/best-practices)性能資訊和[ADF 文檔以瞭解複製活動](/azure/data-factory/copy-activity-performance)。
  
本節介紹 Azure 資料資源管理器是接收器的複製活動的使用。 Azure 資料資源管理器接收器的估計輸送量為 11-13 MBps。 下表詳細介紹了影響 Azure 資料資源管理器接收器性能的參數。

| 參數 | 注意 |
|---|---|
| **元件地理接近** | 將所有元件放在同一區域中：<ul><li>源和接收器資料存儲。</li><li>ADF 集成運行時。</li><li>您的 ADX 群集。</li></ul>確保至少集成運行時與 ADX 群集位於同一區域。 |
| **DIA 數** | ADF 每 4 個 DIA 1 個 VM。 <br>僅當源是具有多個檔的檔案為儲存基礎時，增加 DIA 才會有所説明。 然後，每個 VM 將並行處理不同的檔。 因此，複製單個大型檔的延遲將比複製多個較小的檔更高。|
|**ADX 群集的數量和 SKU** | 大量 ADX 節點將增強引入處理時間。|
| **並行** | 要從資料庫中複製大量資料，請對資料進行分區，然後使用 ForEach 迴圈並行複製每個分區，或使用[從資料庫到 Azure 資料資源管理器範本的批量複製](data-factory-template.md)。 注意：**Settings** > 複製活動中**的並行性設置程度**與 ADX 無關。 |
| **資料處理複雜性** | 延遲因源檔案格式、列映射和壓縮而異。|
| **運行集成運行時的 VM** | <ul><li>對於 Azure 副本，無法更改 ADF VM 和電腦 SKU。</li><li> 對於 Azure 複製的"打開"，確定託管自託管 IR 的 VM 足夠強大。</li></ul>|

## <a name="tips-and-common-pitfalls"></a>提示和常見陷阱

### <a name="monitor-activity-progress"></a>監控活動進度

* 監視活動進度時，*資料寫入*屬性可能比*資料讀取*屬性大得多，因為*資料讀取*是根據二進位檔案大小計算的，而*寫入的資料*是在資料解序列化並解壓縮後根據記憶體中大小計算的。

* 監視活動進度時，可以看到資料已寫入 Azure 資料資源管理器接收器。 查詢 Azure 資料資源管理器表時，可以看到資料尚未到達。 這是因為複製到 Azure 資料資源管理器時有兩個階段。 
    * 第一階段讀取來源資料，將其拆分為 900 MB 塊，並將每個區塊上載到 Azure Blob。 ADF 活動進度視圖可以看到第一階段。 
    * 第二階段在所有資料上載到 Azure Blob 後開始。 Azure 資料資源管理器引擎節點下載 blob 並將資料引入接收器表。 然後，資料在 Azure 資料資源管理器表中查看。

### <a name="failure-to-ingest-csv-files-due-to-improper-escaping"></a>由於無法引入 CSV 檔，導致無法進行洩漏

Azure 資料資源管理器希望 CSV 檔與[RFC 4180](https://www.ietf.org/rfc/rfc4180.txt)對齊。
它期望：
* 包含需要轉義的字元（如"和新的行）的欄位應以 **"字元"** 開頭和結尾，而不帶空格。 所有 **"** 欄位中*的所有*字元都使用雙**字元****（""）** 轉義。 例如，"_你好，""世界"是_一個有效的CSV檔，其中一個記錄具有一個單一的列或欄位，內容為_Hello，"世界"。_
* 檔中的所有記錄必須具有相同的列和欄位數。

Azure 資料工廠允許反斜線（轉義）字元。 如果使用 Azure 資料工廠生成具有反斜線字元的 CSV 檔，則將檔引入 Azure 資料資源管理器將失敗。

#### <a name="example"></a>範例

以下文本值：你好，"世界"<br/>
ABC DEF<br/>
"ABC_D"EF<br/>
"ABC DEF<br/>

應出現在適當的 CSV 檔中，如下所示："你好，"世界"<br/>
"ABC DEF"<br/>
""ABC DEF"<br/>
""ABC\D""EF"<br/>

通過使用預設逸出字元（反斜線），以下 CSV 將無法使用 Azure 資料資源管理器："你好，\"世界"\"<br/>
"ABC DEF"<br/>
"ABC\"DEF"<br/>
\""ABC+D\"EF"<br/>

### <a name="nested-json-objects"></a>嵌套 JSON 物件

將 JSON 檔案複製到 Azure 資料資源管理器時，請注意：
* 不支援陣列。
* 如果 JSON 結構包含物件資料類型，Azure 資料工廠將拼平物件的子項，並嘗試將每個子項映射到 Azure 資料資源管理器表中的不同列。 如果希望將整個物件項映射到 Azure 資料資源管理器中的單個列：
    * 將整個 JSON 行引入 Azure 資料資源管理器中的單個動態列。
    * 使用 Azure 資料工廠的 JSON 編輯器手動編輯管道定義。 在**映射中**
       * 刪除為每個子項創建的多個映射，並添加將物件類型對應到表列的單個映射。
       * 在關閉的方括弧後，添加逗號，後跟：<br/>
       `"mapComplexValuesToString": true`.

### <a name="specify-additionalproperties-when-copying-to-azure-data-explorer"></a>複製到 Azure 資料資源管理器時指定其他屬性

> [!NOTE]
> 此功能當前可通過手動編輯 JSON 有效負載提供。 

在複製活動的"接收器"部分下添加一行，如下所示：

```json
"sink": {
    "type": "AzureDataExplorerSink",
    "additionalProperties": "{\"tags\":\"[\\\"drop-by:account_FiscalYearID_2020\\\"]\"}"
},
```

值的溢出可能比較棘手。 使用以下程式碼片段作為參考：

```csharp
static void Main(string[] args)
{
       Dictionary<string, string> additionalProperties = new Dictionary<string, string>();
       additionalProperties.Add("ignoreFirstRecord", "false");
       additionalProperties.Add("csvMappingReference", "Table1_mapping_1");
       IEnumerable<string> ingestIfNotExists = new List<string> { "Part0001" };
       additionalProperties.Add("ingestIfNotExists", JsonConvert.SerializeObject(ingestIfNotExists));
       IEnumerable<string> tags = new List<string> { "ingest-by:Part0001", "ingest-by:IngestedByTest" };
       additionalProperties.Add("tags", JsonConvert.SerializeObject(tags));
       var additionalPropertiesForPayload = JsonConvert.SerializeObject(additionalProperties);
       Console.WriteLine(additionalPropertiesForPayload);
       Console.ReadLine();
}
```

列印值：

```json
{"ignoreFirstRecord":"false","csvMappingReference":"Table1_mapping_1","ingestIfNotExists":"[\"Part0001\"]","tags":"[\"ingest-by:Part0001\",\"ingest-by:IngestedByTest\"]"}
```

## <a name="next-steps"></a>後續步驟

* 瞭解如何使用[Azure 資料工廠將資料複製到 Azure 資料資源管理器](data-factory-load-data.md)。
* 瞭解如何使用[Azure 資料工廠範本將批量複製到 Azure 資料資源管理器](data-factory-template.md)。
* 瞭解如何使用[Azure 資料工廠命令活動來運行 Azure 資料資源管理器控制項命令](data-factory-command-activity.md)。
* 瞭解[用於資料查詢的 Azure 資料資源管理器查詢](/azure/data-explorer/web-query-data)。



