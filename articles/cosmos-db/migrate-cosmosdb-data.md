---
title: 將數百 TB 的資料遷移至 Azure Cosmos DB
description: 本檔說明如何將數百 tb 的資料移轉到 Cosmos DB
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/23/2019
ms.openlocfilehash: 1e48b2ff6e469a5f792b64c20631e4bd64fb9fd7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85263539"
---
# <a name="migrate-hundreds-of-terabytes-of-data-into-azure-cosmos-db"></a>將數百 TB 的資料遷移至 Azure Cosmos DB 

Azure Cosmos DB 可以儲存 TB 級的資料。 您可以執行大規模資料移轉來將生產工作負載移動到 Azure Cosmos DB。 本文說明將大規模資料移至 Azure Cosmos DB 所涉及的挑戰，並介紹有助於因應這些挑戰以及將資料遷移至 Azure Cosmos DB 的工具。 在此案例研究中，客戶使用 Cosmos DB SQL API。  

將整個工作負載遷移至 Azure Cosmos DB 之前，您可以遷移資料的子集，以驗證一些層面，例如分割區索引鍵選擇、查詢效能和資料模型化。 驗證概念證明之後，您就可以將整個工作負載移到 Azure Cosmos DB。  

## <a name="tools-for-data-migration"></a>資料移轉工具 

Azure Cosmos DB 的遷移策略根據 API 選擇和資料大小而有所不同。 若要遷移較小的資料集（用於驗證資料模型化、查詢效能、資料分割索引鍵選擇等），您可以選擇[資料移轉工具](import-data.md)或[Azure Data Factory 的 Azure Cosmos DB 連接器](../data-factory/connector-azure-cosmos-db.md)。 如果您熟悉 Spark，也可以選擇使用[Azure Cosmos DB spark 連接器](spark-connector.md)來遷移資料。

## <a name="challenges-for-large-scale-migrations"></a>大規模遷移的挑戰 

用來將資料移轉至 Azure Cosmos DB 的現有工具，會有一些在大型規模方面特別明顯的限制：

 * **有限的相應放大功能**：若要儘快將數 tb 的資料移轉到 Azure Cosmos DB，並有效地取用整個布建的輸送量，則遷移用戶端應該能夠無限期擴充。  

* **缺少進度追蹤和檢查**點：請務必追蹤遷移進度，並在遷移大型資料集時擁有檢查點。 否則，在遷移期間發生的任何錯誤都會停止進行遷移，而且您必須從頭開始處理常式。 當99% 的時間已完成時，重新開機整個遷移程式將不會有生產力。  

* 缺少寄不出**的信件佇列**：在大型資料集內，在某些情況下，來源資料的某些部分可能會有問題。 此外，用戶端或網路可能發生暫時性問題。 這兩種情況都不應該導致整個遷移失敗。 雖然大部分的遷移工具都具有強大的重試功能，可防範斷斷續續的問題，但它並不一定夠好。 例如，如果小於0.01% 的源資料檔案大小大於 2 MB，則會導致檔寫入失敗，Azure Cosmos DB。 在理想的情況下，遷移工具會將這些「失敗」的檔保存到另一個寄不出的信件佇列中，這可在遷移後進行處理。 

其中有許多限制是針對 Azure Data factory、Azure 資料移轉服務等工具來修正。 

## <a name="custom-tool-with-bulk-executor-library"></a>使用大量執行程式程式庫的自訂工具 

上一節所述的挑戰，可以透過使用可輕鬆地在多個實例上相應放大的自訂工具來解決，而且能夠彈性地進行暫時性失敗。 此外，自訂工具可以在不同的檢查點暫停和繼續進行遷移。 Azure Cosmos DB 已經提供包含其中一些功能的[大量執行](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-overview)程式程式庫。 例如，大量執行程式程式庫已經有處理暫時性錯誤的功能，而且可以相應放大單一節點中的執行緒，以取用每個節點大約 500 K ru。 大量執行程式程式庫也會將源資料集分割成以一種檢查點形式獨立運作的微批次。  

自訂工具會使用大量執行程式程式庫，並支援在多個用戶端之間向外延展，並在內嵌過程中追蹤錯誤。 若要使用此工具，來源資料應該分割成 Azure Data Lake Storage （ADLS）中的不同檔案，讓不同的遷移工作者可以挑選每個檔案，並將它們內嵌到 Azure Cosmos DB 中。 自訂工具會使用個別的集合，它會針對 ADLS 中的每個個別來源檔案儲存有關遷移進度的中繼資料，並追蹤與它們相關聯的任何錯誤。  

下圖說明使用此自訂工具的遷移程式。 此工具會在一組虛擬機器上執行，而且每個虛擬機器都會查詢 Azure Cosmos DB 中的追蹤集合，以取得其中一個來源資料分割區的租用。 完成這項作業之後，工具會讀取來源資料分割區，並使用大量執行程式程式庫內嵌到 Azure Cosmos DB。 接下來，追蹤集合會更新，以記錄資料內嵌的進度和遇到的任何錯誤。 處理資料分割之後，此工具會嘗試查詢下一個可用的來源分割區。 它會繼續處理下一個來源分割區，直到所有資料都遷移為止。 此工具的原始程式碼可在[這裡](https://github.com/Azure-Samples/azure-cosmosdb-bulkingestion)取得。  

 
:::image type="content" source="./media/migrate-cosmosdb-data/migrationsetup.png" alt-text="遷移工具設定" border="false":::
 

 

追蹤集合包含檔，如下列範例所示。 在來源資料中，您會看到這類檔分別用於每個分割區。  每份檔都包含來源資料分割的中繼資料，例如其位置、遷移狀態和錯誤（如果有的話）：  

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
 

## <a name="prerequisites-for-data-migration"></a>資料移轉的必要條件 

開始資料移轉之前，有幾個必要條件需要考慮：  

#### <a name="estimate-the-data-size"></a>估計資料大小：  

來源資料大小可能不會與 Azure Cosmos DB 中的資料大小完全對應。 您可以插入來自來源的幾個範例檔，以檢查其在 Azure Cosmos DB 中的資料大小。 視範例檔案大小而定，可以預估 Azure Cosmos DB 後置遷移中的資料大小總計。 

例如，如果在 Azure Cosmos DB 遷移之後的每個檔大約為 1 KB，而且源資料集中有60000000000個檔，則表示 Azure Cosmos DB 中的估計大小會接近 60 TB。 

 

#### <a name="pre-create-containers-with-enough-rus"></a>預先建立具有足夠 ru 的容器： 

雖然 Azure Cosmos DB 會自動相應放大儲存體，但建議您從最小的容器大小開始。 較小的容器會有較低的輸送量可用性，這表示需要更長的時間才能完成遷移。 相反地，您可以建立具有最終資料大小的容器（如前一個步驟中所估計），並確定遷移工作負載已完全耗用布建的輸送量。  

在上一個步驟中。 由於資料大小預估大約是 60 TB，因此必須至少有 2.4 M ru 的容器，才能容納整個資料集。  

 

#### <a name="estimate-the-migration-speed"></a>估計遷移速度： 

假設遷移工作負載可能會耗用整個布建的輸送量，則在整個中布建的會提供遷移速度的估計。 繼續上一個範例，必須有 5 ru，才能將 1 KB 的檔寫入 Azure Cosmos DB SQL API 帳戶。  2400000 ru 允許每秒傳輸480000份檔（或 480 MB/s）。 這表示完整的 60 TB 遷移需要125000秒或大約34小時。  

如果您想要在一天內完成遷移，您應該將布建的輸送量增加到 5000000 ru。 

 

#### <a name="turn-off-the-indexing"></a>關閉索引編制：  

由於應該儘快完成遷移，因此建議您將每個檔內嵌建立索引所花費的時間和 ru 降到最低。  Azure Cosmos DB 會自動編制所有屬性的索引，您可以將索引編制成最少選取的幾個詞彙，或在進行遷移的過程中完全關閉。 您可以將 indexingMode 變更為「無」，以關閉容器的編制索引原則，如下所示：  

 
```
  { 
        "indexingMode": "none" 
  } 
```
 

完成遷移之後，您可以更新索引。  

## <a name="migration-process"></a>移轉程序 

完成必要條件之後，您可以使用下列步驟來遷移資料：  

1. 首先，將資料從來源匯入 Azure Blob 儲存體。 若要提高遷移的速度，在不同的來源資料分割之間進行平行處理會很有説明。 開始進行遷移之前，源資料集應分割成大小大約 200 MB 的檔案。   

2. 大量執行程式程式庫可以相應增加，以在單一用戶端 VM 中使用 500000 ru。 因為可用的輸送量為 5000000 ru，所以應在您的 Azure Cosmos 資料庫所在的相同區域中布建10個 Ubuntu 16.04 Vm （Standard_D32_v3）。 您應該使用遷移工具和其設定檔來準備這些 Vm。  

3. 在其中一個用戶端虛擬機器上執行佇列步驟。 此步驟會建立追蹤集合，這會掃描 ADLS 容器，並為每個源資料集的分割區檔案建立進度追蹤檔。  

4. 接下來，在所有用戶端 Vm 上執行 [匯入] 步驟。 每個用戶端都可以取得來源分割區的擁有權，並將其資料內嵌到 Azure Cosmos DB 中。 完成並在追蹤集合中更新其狀態之後，用戶端就可以查詢追蹤集合中的下一個可用來源資料分割。  

5. 此程式會繼續進行，直到整個來源資料分割集內嵌為止。 處理完所有的來源資料分割之後，應該在相同追蹤集合的錯誤更正模式上重新執行此工具。 您必須執行此步驟，才能識別因錯誤而應重新處理的來源資料分割。  

6. 其中一些錯誤可能是來源資料中的檔不正確所造成。 您應該識別並修正這些問題。 接下來，您應該在失敗的資料分割上重新執行匯入步驟來 reingest 它們。 

完成遷移之後，您可以驗證 Azure Cosmos DB 中的檔計數與源資料庫中的檔計數相同。 在此範例中，Azure Cosmos DB 中的總大小為 65 tb。 遷移後，可以選擇性地開啟索引，而 ru 可以降低為工作負載作業所需的層級。

## <a name="contact-the-azure-cosmos-db-team"></a>聯絡 Azure Cosmos DB 小組
雖然您可以遵循本指南來成功地將大型資料集遷移至 Azure Cosmos DB，但對於大規模的遷移，建議您 Azure Cosmos DB 的產品小組，以驗證資料模型化和一般架構審查。 根據您的資料集和工作負載，產品小組也可以建議適用于您的其他效能和成本優化。 若要與 Azure Cosmos DB 小組尋求大規模遷移的協助，您可以在「一般諮詢」問題類型和「大型（TB +）遷移」問題子類型底下開啟支援票證，如下所示。

:::image type="content" source="./media/migrate-cosmosdb-data/supporttopic.png" alt-text="遷移支援主題":::


## <a name="next-steps"></a>後續步驟

* 若要深入瞭解，請嘗試使用[.net](bulk-executor-dot-net.md)和[JAVA](bulk-executor-java.md)中的大量執行程式程式庫。 
* 大量執行程式程式庫已整合到 Cosmos DB Spark 連接器中，若要深入瞭解，請參閱[Azure Cosmos DB Spark 連接器](spark-connector.md)一文。  
* 請洽詢「一般諮詢」問題類型和「大型（TB +）遷移」問題子類型底下的支援票證，以取得 Azure Cosmos DB 產品小組，以取得大規模遷移的其他協助。 
