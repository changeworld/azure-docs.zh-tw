---
title: 將數百 TB 的資料遷移至 Azure Cosmos DB
description: 本檔說明如何將數百 tb 的資料移轉至 Cosmos DB
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/23/2019
ms.openlocfilehash: 02fd0a4c7d931f439ab85af8d90de323105e21f2
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096694"
---
# <a name="migrate-hundreds-of-terabytes-of-data-into-azure-cosmos-db"></a>將數百 TB 的資料遷移至 Azure Cosmos DB 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB 可以儲存 TB 級的資料。 您可以執行大規模資料移轉來將生產工作負載移動到 Azure Cosmos DB。 本文說明將大規模資料移至 Azure Cosmos DB 所涉及的挑戰，並介紹有助於因應這些挑戰以及將資料遷移至 Azure Cosmos DB 的工具。 在此案例研究中，客戶使用 Cosmos DB SQL API。  

將整個工作負載遷移至 Azure Cosmos DB 之前，您可以遷移資料子集來驗證一些層面，例如分割區索引鍵選擇、查詢效能和資料模型化。 驗證概念證明之後，您可以將整個工作負載移至 Azure Cosmos DB。  

## <a name="tools-for-data-migration"></a>資料移轉工具 

Azure Cosmos DB 的遷移策略目前會根據 API 選擇和資料大小而有所不同。 若要遷移較小的資料集–以驗證資料模型、查詢效能、資料分割索引鍵選擇等，您可以選擇 [資料移轉工具](import-data.md) 或 [Azure Data Factory 的 Azure Cosmos DB 連接器](../data-factory/connector-azure-cosmos-db.md)。 如果您熟悉 Spark，也可以選擇使用 [Azure Cosmos DB spark 連接器](spark-connector.md) 來遷移資料。

## <a name="challenges-for-large-scale-migrations"></a>大規模遷移的挑戰 

用來將資料移轉至 Azure Cosmos DB 的現有工具有一些限制，在大型規模中特別明顯：

 * **有限的 scale out 功能** ：為了盡可能快速地將數 tb 的資料移轉至 Azure Cosmos DB，並有效取用整個布建的輸送量，遷移用戶端應該能夠無限期地相應放大。  

* **缺少進度追蹤和檢查** 點：請務必追蹤遷移進度，並且在遷移大型資料集時有檢查點。 否則，在遷移期間發生的任何錯誤都會停止遷移，而您必須從頭開始處理。 當99% 的時間已完成時，重新開機整個遷移程式並不具生產力。  

* 缺少寄不出 **的信件佇列** ：在大型資料集中，某些情況下可能會有來源資料的部分問題。 此外，用戶端或網路可能會有暫時性的問題。 這兩種情況都不應該讓整個遷移失敗。 雖然大部分的遷移工具都具有可抵禦間歇性問題的強大重試功能，但並不一定足夠。 例如，如果源資料檔案的小於0.01% 大小超過 2 MB，則會導致檔寫入在 Azure Cosmos DB 失敗。 在理想的情況下，遷移工具會將這些「失敗」的檔保存到另一個無效信件佇列，以在遷移後進行處理。 

這些限制中有許多是針對 Azure Data factory、Azure 資料移轉服務等工具進行修正。 

## <a name="custom-tool-with-bulk-executor-library"></a>使用大量執行程式程式庫的自訂工具 

上一節所述的挑戰，可透過使用可在多個實例之間輕鬆擴充的自訂工具來解決，並可在暫時性失敗時復原。 此外，自訂工具可以暫停和繼續在不同的檢查點上進行遷移。 Azure Cosmos DB 已經提供包含其中一些功能的 [大量執行](./bulk-executor-overview.md) 程式程式庫。 例如，大量執行程式程式庫已經有處理暫時性錯誤的功能，而且可以在單一節點中相應放大執行緒，以針對每個節點耗用大約 500 K 個 ru。 大量執行程式程式庫也會將源資料集分割成以一種檢查點形式獨立運作的微批次。  

自訂工具會使用大量執行程式程式庫，並支援跨多個用戶端相應放大，並在內嵌進程期間追蹤錯誤。 若要使用此工具，來源資料應該分割成 Azure Data Lake Storage (ADLS) 中的不同檔案，讓不同的遷移工作者可以挑選每個檔案，並將其內嵌到 Azure Cosmos DB。 自訂工具會使用個別的集合，該集合會儲存 ADLS 中每個個別原始程式檔之遷移進度的相關中繼資料，並追蹤任何與其相關聯的錯誤。  

下圖說明使用此自訂工具的遷移程式。 此工具會在一組虛擬機器上執行，而且每個虛擬機器都會查詢 Azure Cosmos DB 中的追蹤集合，以取得其中一個來源資料分割的租用。 完成這項操作之後，工具會讀取來源資料分割區，並使用大量執行程式程式庫內嵌至 Azure Cosmos DB。 接下來，會更新追蹤集合，以記錄資料內嵌的進度，以及任何遇到的錯誤。 處理資料分割之後，工具會嘗試查詢下一個可用的來源資料分割。 它會繼續處理下一個來源分割區，直到所有資料都遷移為止。 此工具的原始程式碼可在 [這裡](https://github.com/Azure-Samples/azure-cosmosdb-bulkingestion)取得。  

 
:::image type="content" source="./media/migrate-cosmosdb-data/migrationsetup.png" alt-text="遷移工具設定" border="false":::
 

 

追蹤集合包含如下列範例所示的檔。 您將會在來源資料的每個資料分割中看到這種檔。  每份檔都包含來源資料分割的中繼資料，例如其位置、遷移狀態和錯誤 (如果有任何) ：  

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

開始資料移轉之前，需要考慮幾個必要條件：  

#### <a name="estimate-the-data-size"></a>預估資料大小：  

來源資料大小可能不會完全對應至 Azure Cosmos DB 中的資料大小。 您可以插入來自來源的一些範例檔，以在 Azure Cosmos DB 中檢查其資料大小。 視範例檔案大小而定，可預估 Azure Cosmos DB 後置遷移中的資料大小總計。 

例如，如果在 Azure Cosmos DB 中遷移之後的每一份檔大約是 1 KB，且源資料集中有大約60000000000的檔，這表示 Azure Cosmos DB 的預估大小會接近 60 TB。 

 

#### <a name="pre-create-containers-with-enough-rus"></a>預先建立具有足夠 ru 的容器： 

雖然 Azure Cosmos DB 會自動相應放大儲存體，但不建議從最小的容器大小開始。 較小的容器具有較低的輸送量可用性，這表示遷移作業需要更長的時間才能完成。 相反地，若要建立具有最終資料大小 (的容器，) 上一個步驟中的估計值，並確保遷移工作負載完全耗用布建的輸送量。  

在上一個步驟中。 由於資料大小預估大約為 60 TB，因此至少需要 2.4 M ru 的容器才能容納整個資料集。  

 

#### <a name="estimate-the-migration-speed"></a>估計遷移速度： 

假設遷移工作負載可以耗用整個布建的輸送量，則在整個布建的會提供遷移速度的估計。 繼續上述範例，需要5個 ru 才能將 1 KB 檔寫入 Azure Cosmos DB SQL API 帳戶。  2400000 ru 允許每秒傳輸480000份檔 (或 480 MB/s) 。 這表示，60 TB 的完整遷移將需要125000秒或大約34個小時。  

如果您想要在一天內完成遷移，您應該將布建的輸送量增加到 5000000 ru。 

 

#### <a name="turn-off-the-indexing"></a>關閉索引編制：  

因為應該儘快完成遷移，建議您將花費在為每個檔內嵌建立索引的時間和 ru 降至最低。  Azure Cosmos DB 會自動編制所有屬性的索引，建議您盡可能將索引編制為所選的幾個詞彙，或在遷移過程中完全關閉。 您可以藉由將 indexingMode 變更為「無」來關閉容器的編制索引原則，如下所示：  

 
```
  { 
        "indexingMode": "none" 
  } 
```
 

完成遷移之後，您可以更新索引。  

## <a name="migration-process"></a>移轉程序 

完成必要條件之後，您可以使用下列步驟來遷移資料：  

1. 首先，將資料從來源匯入至 Azure Blob 儲存體。 為了提高遷移速度，在不同的來源分割區之間進行平行處理會很有説明。 開始遷移之前，應該將源資料集分割成大小大約為 200 MB 的檔案。   

2. 大量執行程式程式庫可以擴大，以在單一用戶端 VM 中使用 500000 ru。 由於可用的輸送量為 5000000 ru，因此 (Standard_D32_v3) 的10個 Ubuntu 16.04 Vm 應布建在 Azure Cosmos 資料庫所在的相同區域中。 您應使用遷移工具和其設定檔來準備這些 Vm。  

3. 在其中一個用戶端虛擬機器上執行佇列步驟。 此步驟會建立追蹤集合，以掃描 ADLS 容器，並為每個源資料集的資料分割檔案建立進度追蹤檔。  

4. 接下來，在所有用戶端 Vm 上執行匯入步驟。 每個用戶端都可以取得來源分割區的擁有權，並將其資料內嵌至 Azure Cosmos DB。 一旦完成，而且其狀態在追蹤集合中更新後，用戶端就可以查詢追蹤集合中下一個可用的來源資料分割。  

5. 此程式會繼續進行，直到整個來源分割集內嵌為止。 處理所有的來源資料分割之後，應該在相同追蹤集合上的錯誤修正模式上重新執行此工具。 需要執行此步驟，才能找出因錯誤而應重新處理的來源資料分割。  

6. 其中有些錯誤可能是由於來源資料中的檔不正確所致。 這些應加以識別和修正。 接下來，您應該在失敗的磁碟分割上重新執行匯入步驟，以 reingest 它們。 

完成遷移之後，您可以驗證 Azure Cosmos DB 中的檔計數是否與源資料庫中的檔計數相同。 在此範例中，Azure Cosmos DB 中的總大小已轉換為 65 tb。 遷移後，您可以選擇性地開啟索引，而且可以將 ru 降至工作負載作業所需的層級。

## <a name="contact-the-azure-cosmos-db-team"></a>聯絡 Azure Cosmos DB 小組
雖然您可以遵循本指南來成功地將大型資料集遷移至 Azure Cosmos DB，但針對大規模遷移，建議您與 Azure Cosmos DB 產品小組聯繫，以驗證資料模型和一般的架構審查。 根據您的資料集和工作負載，產品小組也可以建議適用于您的其他效能和成本優化。 若要聯絡 Azure Cosmos DB 小組以取得大規模遷移的協助，您可以在「一般諮詢」問題類型和「大型 (TB +) 遷移」問題子類別下開啟支援票證，如下所示。

:::image type="content" source="./media/migrate-cosmosdb-data/supporttopic.png" alt-text="遷移工具設定":::


## <a name="next-steps"></a>下一步

* 試用使用 [.net](bulk-executor-dot-net.md) 和 [JAVA](bulk-executor-java.md)大量執行程式程式庫的範例應用程式，以深入瞭解。 
* 大量執行程式程式庫已整合到 Cosmos DB Spark 連接器中，若要深入瞭解，請參閱 [Azure Cosmos DB Spark 連接器](spark-connector.md) 文章。  
* 若要取得大規模遷移的額外說明，請在「一般諮詢」問題類型和「大型 (TB +) 遷移」問題子類別中開啟支援票證，以與 Azure Cosmos DB 產品小組聯繫。