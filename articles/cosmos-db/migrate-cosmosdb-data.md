---
title: 將數百 TB 的資料遷移至 Azure Cosmos DB
description: 本文檔介紹了如何將 100 TB 的資料移轉到 Cosmos DB
author: bharathsreenivas
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: bharathb
ms.openlocfilehash: 69b400eb7838c986ac6f275da58c7457179ebea6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72880209"
---
# <a name="migrate-hundreds-of-terabytes-of-data-into-azure-cosmos-db"></a>將數百 TB 的資料遷移至 Azure Cosmos DB 

Azure Cosmos DB 可以儲存 TB 級的資料。 您可以執行大規模資料移轉來將生產工作負載移動到 Azure Cosmos DB。 本文說明將大規模資料移至 Azure Cosmos DB 所涉及的挑戰，並介紹有助於因應這些挑戰以及將資料遷移至 Azure Cosmos DB 的工具。 在此案例研究中，客戶使用 Cosmos DB SQL API。  

在將整個工作負荷遷移到 Azure Cosmos DB 之前，可以遷移資料子集以驗證分區金鑰選擇、查詢性能和資料建模等某些方面。 驗證概念驗證後，可以將整個工作負荷移動到 Azure Cosmos DB。  

## <a name="tools-for-data-migration"></a>資料移轉工具 

Azure Cosmos DB 遷移策略目前因 API 選擇和資料大小而異。 要遷移較小的資料集 （用於驗證資料建模、查詢性能、分區金鑰選擇等），可以選擇[資料移轉工具](import-data.md)或[Azure 資料工廠的 Azure Cosmos 資料庫連接器](../data-factory/connector-azure-cosmos-db.md)。 如果您熟悉 Spark，還可以選擇使用[Azure Cosmos DB Spark 連接器](spark-connector.md)遷移資料。

## <a name="challenges-for-large-scale-migrations"></a>大規模遷移的挑戰 

用於將資料移轉到 Azure Cosmos DB 的現有工具存在一些在大型擴展中尤為明顯的限制：

 * **有限的橫向擴展功能**：為了儘快將 TB 的資料移轉到 Azure Cosmos DB，並有效地消耗整個預配輸送量，遷移用戶端應該能夠無限期地擴展。  

* **缺乏進度跟蹤和檢查點**：在遷移大型資料集時跟蹤遷移進度並進行檢查非常重要。 否則，遷移期間發生的任何錯誤都將停止遷移，您必須從頭開始該過程。 當 99% 的遷移過程已完成時，重新開機整個遷移過程將不富有成效的工作。  

* **缺少無效信件佇列**：在大型資料集中，在某些情況下，部分來源資料可能有問題。 此外，用戶端或網路可能存在暫時性問題。 這些情況中的任何一種情況都不應導致整個遷移失敗。 儘管大多數遷移工具具有強大的重試功能，可防止間歇性問題，但並不總是足夠。 例如，如果小於 0.01% 的來源資料文檔的大小大於 2 MB，則會導致文檔寫入在 Azure Cosmos DB 中失敗。 理想情況下，遷移工具將這些"失敗"文檔保存到另一個無效信件佇列非常有用，該佇列可以在遷移後處理。 

許多這些限制都針對 Azure 資料工廠、Azure 資料移轉服務等工具進行修復。 

## <a name="custom-tool-with-bulk-executor-library"></a>具有批量執行器庫的自訂工具 

上一節中描述的挑戰可以通過使用自訂工具來解決，該工具可以輕鬆地跨多個實例橫向擴展，並且具有對瞬態故障的彈性。 此外，自訂工具可以在各個檢查點暫停和恢復遷移。 Azure Cosmos DB 已經提供了包含其中一些功能[的批量執行器庫](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-overview)。 例如，大宗執行器庫已具有處理瞬態錯誤的功能，並且可以橫向擴展單個節點中的執行緒，以消耗每個節點約 500 K R。 批量執行器庫還將源資料集分區為微批次處理，這些批次處理作為檢查點的形式獨立運行。  

自訂工具使用批量執行器庫，並支援跨多個用戶端橫向擴展，並在引入過程中跟蹤錯誤。 要使用此工具，來源資料應分區到 Azure 資料湖存儲 （ADLS） 中的不同檔，以便不同的遷移工作人員可以選取每個檔並將其引入 Azure Cosmos DB。 自訂工具使用單獨的集合，該集合存儲有關 ADLS 中每個原始檔案的遷移進度的中繼資料，並跟蹤與其關聯的任何錯誤。  

下圖描述了使用此自訂工具的遷移過程。 該工具在一組虛擬機器上運行，每個虛擬機器查詢 Azure Cosmos DB 中的跟蹤集合以獲取其中一個來源資料分區的租約。 完成此操作後，該工具將讀取來源資料分區，並使用大容量執行器庫引入 Azure Cosmos DB。 接下來，將更新跟蹤集合以記錄資料引入的進度和遇到的任何錯誤。 處理資料分區後，該工具將嘗試查詢下一個可用源分區。 它繼續處理下一個源分區，直到遷移所有資料。 該工具的原始程式碼[可在此處](https://github.com/Azure-Samples/azure-cosmosdb-bulkingestion)獲取。  

 
![遷移工具設置](./media/migrate-cosmosdb-data/migrationsetup.png)
 

 

跟蹤集合包含文檔，如以下示例所示。 您將在來源資料中為每個分區看到一個這樣的文檔。  每個文檔都包含來源資料分區的中繼資料，如其位置、遷移狀態和錯誤（如果有）：  

```json
{ 
  "owner": "25812@bulkimporttest07", 
  "jsonStoreEntityImportResponse": { 
    "numberOfDocumentsReceived": 446688, 
    "isError": false, 
    "totalRequestUnitsConsumed": 3950252.2800000003, 
    "errorInfo": [], 
    "totalTimeTakenInSeconds": 188, 
    "numberOfDocumentsImported": 446688 
  }, 
  "storeType": "AZURE_BLOB", 
  "name": "sourceDataPartition", 
  "location": "sourceDataPartitionLocation", 
  "id": "sourceDataPartitionId", 
  "isInProgress": false, 
  "operation": "unpartitioned-writes", 
  "createDate": { 
    "seconds": 1561667225, 
    "nanos": 146000000 
  }, 
  "completeDate": { 
    "seconds": 1561667515, 
    "nanos": 180000000 
  }, 
  "isComplete": true 
} 
```
 

## <a name="prerequisites-for-data-migration"></a>資料移轉的先決條件 

在開始資料移轉之前，需要考慮以下幾個先決條件：  

#### <a name="estimate-the-data-size"></a>估計資料大小：  

來源資料大小可能不完全映射到 Azure Cosmos DB 中的資料大小。 可以插入源中的一些示例文檔，以檢查其資料大小。 根據示例文檔大小，可以估計遷移後 Azure Cosmos DB 中的總數據大小。 

例如，如果在 Azure Cosmos DB 中遷移後每個文檔大約 1 KB，並且源資料集中約有 600 億個文檔，則意味著 Azure Cosmos DB 中估計的大小將接近 60 TB。 

 

#### <a name="pre-create-containers-with-enough-rus"></a>預創建具有足夠 R 的容器： 

儘管 Azure Cosmos DB 會自動擴展存儲，但不建議從最小的容器大小開始。 較小的容器的輸送量可用性較低，這意味著遷移需要更長的時間才能完成。 相反，創建具有最終資料大小的容器（如上一步中估計的那樣）並確保遷移工作負載完全消耗預配輸送量非常有用。  

在上一步中。 由於資料大小估計約為 60 TB，因此需要至少 2.4 M R 的容器來容納整個資料集。  

 

#### <a name="estimate-the-migration-speed"></a>估計遷移速度： 

假設遷移工作負荷可以消耗整個預配輸送量，則整個預配將提供遷移速度的估計。 繼續前面的示例，將 1 KB 的文檔寫入 Azure Cosmos DB SQL API 帳戶需要 5 個 R。  240萬個魯馬，將允許每秒傳輸480，000份檔（或480MB/s）。 這意味著 60 TB 的完整遷移將需要 125，000 秒或大約 34 小時。  

如果希望在一天內完成遷移，則應將預配輸送量增加到 500 萬個 R。" 

 

#### <a name="turn-off-the-indexing"></a>關閉索引：  

由於遷移應儘快完成，因此建議儘量減少為每個引入的文檔創建索引的時間和 R。  Azure Cosmos DB 會自動索引所有屬性，因此有必要將索引最小化到選定的幾個術語，或將其完全關閉以進行遷移。 您可以通過將索引模式更改為 none 來關閉容器的索引策略，如下所示：  

 
```
  { 
        "indexingMode": "none" 
  } 
```
 

遷移完成後，可以更新索引。  

## <a name="migration-process"></a>移轉程序 

完成先決條件後，可以使用以下步驟遷移資料：  

1. 首先將資料從源導入 Azure Blob 存儲。 為了提高遷移速度，跨不同的源分區並行化是很有説明的。 在開始遷移之前，源資料集應分區為大小約為 200 MB 的檔。   

2. 批量執行器庫可以擴展，以在單個用戶端 VM 中使用 500，000 個 R。 由於可用輸送量為 500 萬個 RU，因此應在 Azure Cosmos 資料庫所在的同一區域預配 10 Ubuntu 16.04 VM （Standard_D32_v3）。 您應該使用遷移工具及其設置檔準備這些 VM。  

3. 在其中一個用戶端虛擬機器上運行佇列步驟。 此步驟創建跟蹤集合，該集合掃描 ADLS 容器，並為每個源資料集的分區檔創建進度跟蹤文檔。  

4. 接下來，在所有用戶端 VM 上運行導入步驟。 每個用戶端都可以對源分區擁有擁有權，並將其資料引入 Azure Cosmos DB。 完成並更新其狀態後，用戶端就可以查詢跟蹤集合中的下一個可用源分區。  

5. 此過程將一直持續到引入整個源分區集。 處理所有源分區後，應在同一跟蹤集合上的錯誤更正模式下重新運行該工具。 此步驟需要標識應由於錯誤而重新處理的源分區。  

6. 其中一些錯誤可能是由於來源資料中的文檔不正確造成的。 這些應被識別和修復。 接下來，應重新運行失敗分區上的導入步驟以重新使用它們。 

遷移完成後，可以驗證 Azure Cosmos DB 中的文檔計數是否與源資料庫中的文檔計數相同。 在此示例中，Azure Cosmos DB 中的總大小為 65 TB。 遷移後，可以有選擇地打開索引，並且可以將 R 並降低到工作負載操作所需的級別。

## <a name="contact-the-azure-cosmos-db-team"></a>聯繫 Azure 宇宙資料庫團隊
儘管您可以按照本指南成功將大型資料集遷移到 Azure Cosmos DB，但對於大規模遷移，建議您聯繫 Azure Cosmos DB 產品團隊以驗證資料建模和常規體系結構審查。 根據您的資料集和工作負載，產品團隊還可以建議可能適用于您的其他性能和成本優化。 要聯繫 Azure Cosmos DB 團隊以獲得大規模遷移的説明，可以在"一般通報"問題類型和"大型 （TB+） 遷移"問題子類型下打開支援票證，如下所示。

![遷移支援主題](./media/migrate-cosmosdb-data/supporttopic.png)


## <a name="next-steps"></a>後續步驟

* 通過嘗試在[.NET](bulk-executor-dot-net.md)和[JAVA](bulk-executor-java.md)中使用批量執行者庫的應用程式範例，瞭解更多資訊。 
* 批量執行器庫集成到 Cosmos DB Spark 連接器中，有關詳細資訊，請參閱[Azure Cosmos DB Spark 連接器](spark-connector.md)一文。  
* 通過在"一般諮詢"問題類型和"大型 （TB+） 遷移"問題子類型下打開支援票證，與 Azure Cosmos DB 產品團隊聯繫，以獲取有關大規模遷移的其他説明。 
