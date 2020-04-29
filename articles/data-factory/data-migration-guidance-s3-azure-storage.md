---
title: 將資料從 Amazon S3 遷移到 Azure 儲存體
description: 使用 Azure Data Factory，將資料從 Amazon S3 遷移至 Azure 儲存體。
services: data-factory
ms.author: yexu
author: dearandyxu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 8/04/2019
ms.openlocfilehash: 3f40ad7346219b48a38ade38b2a75ddf71940875
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416425"
---
# <a name="use-azure-data-factory-to-migrate-data-from-amazon-s3-to-azure-storage"></a>使用 Azure Data Factory 將資料從 Amazon S3 遷移至 Azure 儲存體 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory 提供高效能、穩固且符合成本效益的機制，將資料從 Amazon S3 大規模遷移至 Azure Blob 儲存體或 Azure Data Lake Storage Gen2。  本文提供資料工程師和開發人員的下列資訊： 

> [!div class="checklist"]
> * 效能 
> * 複製復原能力
> * 網路安全性
> * 高階解決方案架構 
> * 實施最佳做法  

## <a name="performance"></a>效能

ADF 提供無伺服器架構，允許不同層級的平行處理原則，讓開發人員能夠建立管線來充分利用您的網路頻寬，以及儲存 IOPS 和頻寬，以最大化您環境的資料移動輸送量。 

客戶已成功將數以百萬計的資料從 Amazon S3 遷移到 Azure Blob 儲存體，且持續的輸送量為 2 GBps 和更高。 

![效能](media/data-migration-guidance-s3-to-azure-storage/performance.png)

上圖說明如何透過不同的平行處理原則層級來達到絕佳的資料移動速度：
 
- 單一複製活動可以利用可調整的計算資源：使用 Azure Integration Runtime 時，您可以用無伺服器的方式為每個複製活動指定[最多 256 diu](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) ;使用自我裝載的 Integration Runtime 時，您可以手動相應增加機器或相應放大至多部電腦（[最多4個節點](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)），而單一複製活動會在所有節點上分割其檔案集。 
- 單一複製活動會使用多個執行緒讀取和寫入資料存放區。 
- ADF 控制流程可以平行啟動多個複製活動，例如，[針對每個迴圈](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)使用。 

## <a name="resilience"></a>復原能力

在單一複製活動執行中，ADF 具有內建的重試機制，因此它可以處理資料存放區或基礎網路中特定層級的暫時性失敗。 

進行從 S3 到 Blob 的二進位複製，以及從 S3 複製到 ADLS Gen2 時，ADF 會自動執行檢查點。  如果複製活動執行失敗或超時，則在後續的重試中，複製會從最後一個失敗點繼續，而不是從開頭開始。 

## <a name="network-security"></a>網路安全性 

根據預設，ADF 會使用透過 HTTPS 通訊協定的加密連接，將資料從 Amazon S3 傳輸至 Azure Blob 儲存體或 Azure Data Lake Storage Gen2。  HTTPS 提供傳輸中的資料加密，並防止竊聽和攔截式攻擊。 

或者，如果您不想透過公用網際網路傳送資料，您可以透過 AWS Direct Connect 與 Azure Express Route 之間的私用對等互連連結來傳輸資料，以達到更高的安全性。  請參閱下列解決方案架構，以瞭解如何達成此目的。 

## <a name="solution-architecture"></a>方案架構

透過公用網際網路遷移資料：

![解決方案-架構-公用-網路](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-public-network.png)

- 在此架構中，資料會使用 HTTPS over 公用網際網路安全地傳輸。 
- 來源 Amazon S3 和目的地 Azure Blob 儲存體或 Azure Data Lake Storage Gen2 都會設定為允許來自所有網路 IP 位址的流量。  請參閱下面的第二個架構，以瞭解如何限制對特定 IP 範圍的網路存取。 
- 您可以用無伺服器的方式輕鬆地相應增加功能，以充分利用您的網路和儲存體頻寬，讓您可以為您的環境取得最佳的輸送量。 
- 初始快照集遷移和差異資料移轉都可以使用此架構來達成。 

透過私人連結遷移資料： 

![解決方案-架構-私人-網路](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-private-network.png)

- 在此架構中，資料移轉是透過 AWS Direct Connect 與 Azure Express Route 之間的私用對等互連連結進行，因此資料永遠不會通過公用網際網路。  它需要使用 AWS VPC 和 Azure 虛擬網路。 
- 您需要在 Azure 虛擬網路內的 Windows VM 上安裝 ADF 自我裝載整合執行時間，以達成此架構。  您可以手動相應增加自我裝載的 IR Vm，或相應放大至多個 Vm （最多4個節點），以充分利用您的網路和儲存體 IOPS/頻寬。 
- 如果可接受透過 HTTPS 傳輸資料，但您想要鎖定對來源 S3 到特定 IP 範圍的網路存取，您可以藉由移除 AWS VPC 並以 HTTPS 取代私用連結，以採用此架構的變化。  您會想要在 Azure VM 上保留 Azure 虛擬和自我裝載的 IR，讓您可以有靜態可路由傳送的 IP 以供允許清單之用。 
- 初始快照集資料移轉和差異資料移轉都可以使用此架構來達成。 

## <a name="implementation-best-practices"></a>實施最佳做法 

### <a name="authentication-and-credential-management"></a>驗證和認證管理 

- 若要向 Amazon S3 帳戶進行驗證，您必須使用[IAM 帳戶的存取金鑰](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service#linked-service-properties)。 
- 支援多個驗證類型來連接到 Azure Blob 儲存體。  強烈建議使用[適用于 Azure 資源的受控](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity)識別：建置於自動受管理的 ADF 識別 Azure AD 中，它可讓您設定管線，而不需要在連結服務定義中提供認證。  或者，您可以使用[服務主體](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication)、[共用存取](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)簽章或[儲存體帳戶金鑰](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication)，向 Azure Blob 儲存體進行驗證。 
- 也支援多個驗證類型來連接到 Azure Data Lake Storage Gen2。  強烈建議使用[Azure 資源的受控](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity)識別，雖然也可以使用[服務主體](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication)或[儲存體帳戶金鑰](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication)。 
- 當您未使用 Azure 資源的受控識別時，強烈建議您將[認證儲存在 Azure Key Vault 中](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)，讓您更輕鬆地集中管理和旋轉金鑰，而不需要修改 ADF 連結的服務。  這也是[CI/CD](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd)的其中一個最佳作法。 

### <a name="initial-snapshot-data-migration"></a>初始快照集資料移轉 

當遷移超過 100 TB 的資料時，建議使用資料分割。  若要分割資料，請利用 [前置詞] 設定來依名稱篩選 Amazon S3 中的資料夾和檔案，然後每個 ADF 複製作業一次只能複製一個分割區。  您可以同時執行多個 ADF 複製工作，以獲得更好的輸送量。 

如果有任何複製作業因為網路或資料存放區暫時性問題而失敗，您可以重新執行失敗的複製作業，從 AWS S3 重新載入該特定的分割區。  載入其他分割區的所有其他複製作業都不會受到影響。 

### <a name="delta-data-migration"></a>差異資料移轉 

若要從 AWS S3 識別新的或變更的檔案，最有效的方式就是使用時間分割的命名慣例–當 AWS S3 中的資料已使用檔案或資料夾名稱中的時間配量資訊進行時間分割時（例如/yyyy/mm/dd/file.csv），您的管線就可以輕鬆地識別要以累加方式複製哪些檔案/資料夾。 

或者，如果您在 AWS S3 中的資料不是時間分割，ADF 可以透過其 LastModifiedDate 來識別新的或已變更的檔案。   其運作方式是 ADF 會掃描 AWS S3 的所有檔案，並只複製新的和更新的檔案，其上次修改時間戳記大於特定值。  請注意，如果您在 S3 中有大量檔案，則不論符合篩選準則的檔案數目為何，初始檔案掃描可能會花很長的時間。  在此情況下，建議您先將資料分割，並使用相同的「前置詞」設定來進行初始快照集遷移，讓檔案能夠以平行方式進行掃描。  

### <a name="for-scenarios-that-require-self-hosted-integration-runtime-on-azure-vm"></a>適用于在 Azure VM 上需要自我裝載整合執行時間的案例 

無論您是透過私人連結來遷移資料，或想要允許 Amazon S3 防火牆上的特定 IP 範圍，都必須在 Azure Windows VM 上安裝自我裝載整合執行時間。 

- 針對每個 Azure VM 開始使用的建議設定是以 32 vCPU 和 128 GB 記憶體 Standard_D32s_v3。  您可以在資料移轉期間持續監視 IR VM 的 CPU 和記憶體使用量，以瞭解您是否需要進一步相應增加 VM 以獲得更佳的效能，或相應減少 VM 以節省成本。 
- 您也可以將最多4個 VM 節點與單一的自我裝載 IR 產生關聯，以相應放大。  針對自我裝載 IR 執行的單一複製作業會自動分割盤案集，並利用所有 VM 節點來平行複製檔案。  為達高可用性，建議您從2個 VM 節點開始，以避免在資料移轉期間發生單一失敗點。 

### <a name="rate-limiting"></a>速率限制 

最佳做法是使用具代表性的範例資料集來進行效能 POC，讓您可以判斷適當的分割區大小。 

從單一分割區開始，並使用預設 DIU 設定來進行單一複製活動。  逐漸增加 DIU 設定，直到您達到網路的頻寬限制或資料存放區的 IOPS/頻寬限制，或已達到單一複製活動允許的最大 256 DIU。 

接下來，逐漸增加並行複製活動的數目，直到達到您環境的限制為止。 

當您遇到 ADF 複製活動所回報的節流錯誤時，請減少 ADF 中的並行或 DIU 設定，或考慮增加網路和資料存放區的頻寬/IOPS 限制。  

### <a name="estimating-price"></a>預估價格 

> [!NOTE]
> 這是假設性的定價範例。  您的實際定價取決於您環境中的實際輸送量。

請考慮下列管線，其結構是用來將資料從 S3 遷移至 Azure Blob 儲存體： 

![定價-管線](media/data-migration-guidance-s3-to-azure-storage/pricing-pipeline.png)

讓我們假設下列各項： 

- 總數據量為 2 PB 
- 使用第一個解決方案架構透過 HTTPS 遷移資料 
- 2 PB 會分成 1 K 分割區，而每個複本會移動一個資料分割 
- 每個複本都設定 DIU = 256，並達到 1 GBps 輸送量 
- ForEach concurrency 已設定為2，而匯總輸送量為 2 GBps 
- 總共需要292小時才能完成遷移 

以下是以上述假設為基礎的預估價格： 

![定價-資料表](media/data-migration-guidance-s3-to-azure-storage/pricing-table.png)

### <a name="additional-references"></a>其他參考資料 
- [Amazon Simple Storage Service connector](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service)
- [Azure Blob 儲存體連接器](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Azure Data Lake Storage Gen2 連接器](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [複製活動效能微調指南](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [建立和設定自我裝載 Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [自我裝載整合執行時間 HA 和擴充性](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [資料移動安全性考量](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [在 Azure Key Vault 中儲存認證](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [根據時間分割的檔案名，以累加方式複製檔案](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [根據 LastModifiedDate 複製新的和已變更的檔案](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [ADF 定價頁面](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="template"></a>[範本]

以下是一開始的[範本](solution-template-migration-s3-azure.md)，可將來自 Amazon S3 的數十萬個檔案中的數以百萬個檔案遷移至 Azure Data Lake Storage Gen2。

## <a name="next-steps"></a>後續步驟

- [使用 Azure Data Factory 從多個容器複製檔案](solution-template-copy-files-multiple-containers.md)
