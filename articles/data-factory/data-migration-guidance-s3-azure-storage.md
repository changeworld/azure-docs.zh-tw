---
title: 將資料從 Amazon S3 遷移到 Azure 儲存體
description: 使用 Azure Data Factory 將資料從 Amazon S3 遷移到 Azure 儲存體。
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
ms.openlocfilehash: 963a541835c5e45c5642f2d516da53fd165142b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91616919"
---
# <a name="use-azure-data-factory-to-migrate-data-from-amazon-s3-to-azure-storage"></a>使用 Azure Data Factory 將資料從 Amazon S3 遷移到 Azure 儲存體 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory 提供具效能、強固，且符合成本效益的機制，以將資料從 Amazon S3 大規模遷移至 Azure Blob 儲存體或 Azure Data Lake Storage Gen2。  本文為資料工程師和開發人員提供下列資訊： 

> [!div class="checklist"]
> * 效能 
> * 複本恢復功能
> * 網路安全性
> * 高階解決方案架構 
> * 實作最佳做法  

## <a name="performance"></a>效能

ADF 提供無伺服器的架構，其可供以不同層級進行平行處理，允許開發人員建置管線來充分利用網路頻寬以及儲存體 IOPS 和頻寬來最大化環境的資料移動輸送量。 

客戶已成功將由數以百萬計檔案組成的數 PB 資料從 Amazon S3 移動到 Azure Blob 儲存體，且將輸送量維持在 2 GBps 以上。 

![圖表顯示 A W S S3 存放區中的數個檔案分割區，其中有相關聯的複製動作來 Azure Blob 儲存體 D L S Gen2。](media/data-migration-guidance-s3-to-azure-storage/performance.png)

上圖說明如何透過不同層級的平行處理以達到絕佳資料移動速度：
 
- 單一複製活動可利用可調整的計算資源：使用 Azure Integration Runtime 時，您可以無伺服器的方式為每個複製活動指定[最多 256 個 DIU](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units)；使用自我裝載整合執行階段時，您可手動擴大機器或擴增至多部機器 ([最多 4 個節點](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability))，且單一複製活動會將其檔案集分割至所有節點。 
- 單一複製活動會使用多個執行緒來從資料存放區讀取和寫入至資料存放區。 
- ADF 控制流程可以平行方式啟動多個複製活動，例如使用 [For Each 迴圈](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)。 

## <a name="resilience"></a>恢復功能

在單一複製活動回合內，ADF 具備內建的重試機制，因此可處理資料存放區或基礎網路中特定層級的暫時性失敗。 

執行從 S3 到 Blob，以及從 S3 到 ADLS Gen2 的二進位複製時，ADF 會自動執行檢查點。  若複製活動回合失敗或逾時，則在進行下一個重試時，複製會從上一個失敗點繼續，而非從頭開始。 

## <a name="network-security"></a>網路安全性 

根據預設，ADF 會透過 HTTPS 通訊協定以使用加密連線將資料從 Amazon S3 傳輸到 Azure Blob 儲存體或 Azure Data Lake Storage Gen2。  HTTPS 會提供傳輸中的資料加密，並可防止竊聽和中間人攻擊。 

或者，若您不希望透過公用網際網路傳輸資料，則可透過 AWS Direct Connect 和 Azure Express Route 之間的私人對等互連連結來傳輸資料，以取得更高的安全性。  請參閱以下的解決方案架構來了解如何進行。 

## <a name="solution-architecture"></a>方案架構

透過公用網際網路遷移資料：

![圖顯示透過網際網路從 A W S S3 市集中的 Azure Integration Runtime，透過 D F Azure 中的從網際網路遷移至 Azure 儲存體。 執行時間有 Data Factory 的控制通道。](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-public-network.png)

- 在此架構中，資料會透過公用網際網路，使用 HTTPS 安全地進行傳輸。 
- 來源 Amazon S3 和目的地 Azure Blob 儲存體或 Azure Data Lake Storage Gen2 會設為允許來自所有網路 IP 位址的流量。  請參閱以下第二個架構來了解如何將網路存取限制在特定 IP 範圍。 
- 您可以無伺服器的方式輕鬆進行擴大，以完整利用網路和儲存體頻寬來為環境取得最佳的輸送量。 
- 初始快照集的移轉和差異資料移轉都可以使用此架構進行。 

透過私人連結遷移資料： 

![圖表顯示透過私人對等互連連線，透過 Azure 虛擬機器上的自我裝載整合執行時間，透過 Azure 虛擬機器上的自我裝載整合執行時間，將其遷移至 Azure 儲存體。 執行時間有 Data Factory 的控制通道。](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-private-network.png)

- 在此架構中，資料移轉是透過 AWS Direct Connect 和 Azure Express Route 之間的私人對等互連連結來執行，因此資料永遠都不會在公用網際網路上周遊。  其需要使用 AWS VPC 和 Azure 虛擬網路。 
- 您需要在 Azure 虛擬網路內的 Windows VM 上安裝 ADF 自我裝載整合執行階段，才能達成此架構。  您可手動擴大自我裝載 IR VM，或擴增至多部 VM (最多 4 個節點) 來完整利用網路和儲存體 IOPS/頻寬。 
- 如果可透過 HTTPS 傳輸資料，但是想要將對來源 S3 的網路存取鎖定在特定 IP 範圍，則可採用此架構的變化來移除 AWS VPC 並使用 HTTPS 取代私人連結。  您會想要將 Azure 虛擬和自我裝載的 IR 保留在 Azure VM 上，讓您可以有靜態可路由傳送的 IP 來進行篩選。 
- 初始快照集的資料移轉和差異資料移轉都可使用此架構進行。 

## <a name="implementation-best-practices"></a>實作最佳做法 

### <a name="authentication-and-credential-management"></a>驗證及認證管理 

- 若要向 Amazon S3 帳戶進行驗證，則必須使用 [IAM 帳戶的存取金鑰](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service#linked-service-properties)。 
- 支援多個驗證類型來連線到 Azure Blob 儲存體。  我們強烈建議使用 [Azure 資源受控識別](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity)：Azure 資源受控識別是建置在 Azure AD 中的自動受控 ADF 識別上，其可供在並未於連結服務定義中提供認證的情況下設定管線。  或者，您可使用[服務主體](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication)、[共用存取簽章](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)，或[儲存體帳戶金鑰](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication)來向 Azure Blob 儲存體進行驗證。 
- 也支援多個驗證類型來連線到 Azure Data Lake Storage Gen2。  我們強烈建議使用 [Azure 資源受控識別](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity)，雖然您也可使用[服務主體](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication)或[儲存體帳戶金鑰](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication)。 
- 當不使用 Azure 資源受控識別時，則強烈建議[將認證儲存在 Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) 中，其可供更輕鬆地集中管理和輪換金鑰，而無須修改 ADF 連結服務。  這也是其中一個 [CI/CD 的最佳做法](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd)。 

### <a name="initial-snapshot-data-migration"></a>初始快照集資料移轉 

特別是在遷移超過 100 TB 的資料時，建議使用資料分割。  若要分割資料，請利用「前置」設定來透過名稱篩選 Amazon S3 中的資料夾和檔案，接著每個 ADF 複製作業即可一次複製一個分割。  您可同時執行多個 ADF 複製作業，以達到更佳的輸送量。 

若因為網路或資料存放區暫時性的問題而導致複製作業失敗，則可重新執行失敗的複製作業來從 AWS S3 重新載入該特定分割。  所有其他正在載入其他分割的複製作業都不會受到影響。 

### <a name="delta-data-migration"></a>差異資料移轉 

識別 AWS S3 新增或變更檔案效能最高的方式是使用時間分割命名慣例 ‒ 當在 AWS S3 中資料已使用檔案或資料夾名稱中的時間配量資訊進行時間分割時 (例如 /yyyy/mm/dd/file.csv)，管線即可輕易地識別要以累加方式複製的檔案/資料夾。 

或者，若在 AWS S3 中的資料並未經過時間分割，則 ADF 可透過其 LastModifiedDate 來識別新增或變更的檔案。   其運作方式是 ADF 會掃描 AWS S3 的所有檔案，然後只複製其最後所修改時間戳記大於特定值的新增和更新檔案。  請注意，若在 S3 中有大量的檔案，則初始檔案掃描可能會花費相當長的時間，無論符合篩選條件的檔案數量為何。  在這種情況下，建議先對資料進行分割，針對初始快照集移轉使用相同的「前置」設定，讓檔案掃描可以平行方式進行。  

### <a name="for-scenarios-that-require-self-hosted-integration-runtime-on-azure-vm"></a>針對需要 Azure VM 上自我裝載整合執行階段的案例 

無論是透過私人連結遷移資料，還是想要在 Amazon S3 防火牆上允許特定的 IP 範圍，您都需要在 Azure Windows VM 上安裝自我裝載整合執行階段。 

- 針對每個 Azure VM 建議採取的初始設定是 Standard_D32s_v3，其中包含 32 個 vCPU 和 128 GB 的的記憶體。  您可在資料移轉期間持續監視 IR VM 的 CPU 和記憶體使用率，以了解是否需要進一步地擴大 VM 以改善效能，或縮小 VM 以節省成本。 
- 您也可以將單一自我裝載 IR 與最多 4 個 VM 節點建立關聯，以進行擴增。  針對自我裝載 IR 執行的單一複製作業將會自動分割檔案集，並利用所有 VM 節點來以平行方式複製檔案。  為取得高可用性，建議從 2 個 VM 節點開始，以避免資料移轉期間的單一失敗點。 

### <a name="rate-limiting"></a>速率限制 

最佳做法是使用代表性的範例資料集進行效能 POC，其可供判斷適當的分割大小。 

使用預設 DIU 設定，從單一分割和單一複製活動開始。  逐步增加 DIU 設定，直到到達網路的頻寬限制，或資料存放區的 IOPS/頻寬限制，或到達在單一複製活動上所允許的 256 個 DIU 上限。 

接下來，逐步增加同時複製活動的數量，直到達到環境的限制。 

當 ADF 複製活動報告節流錯誤時，請減少 ADF 中的並行或 DIU 設定，或考慮增加網路和資料存放區的頻寬/IOPS 限制。  

### <a name="estimating-price"></a>估計價格 

> [!NOTE]
> 這是假設的定價範例。  實際定價取決於環境中的實際輸送量。

考慮下列為將資料從 S3 遷移至 Azure Blob 儲存體所建構的管線： 

![下圖顯示用來遷移資料的管線，其中包含手動觸發程式流向查詢、流入 ForEach、流向包含複製到預存程式之每個資料分割的子管線。 在管線外部，預存程式會流向 Azure SQL D B，以流向查閱，以及要複製的 W S S3 流程，以流向 Blob 儲存體。](media/data-migration-guidance-s3-to-azure-storage/pricing-pipeline.png)

讓我們假設下列情況： 

- 總資料量為 2 PB 
- 使用第一個解決方案架構以透過 HTTPS 遷移資料 
- 2 PB 會分成一千個分割，且每個複製都會移動一個分割 
- 每個複製都已設為 DIU=256，並達到 1 GBps 的輸送量 
- ForEach 並行已設為 2，且彙總輸送量為 2 GBps 
- 總計需要耗費 292 個小時來完成移轉 

以下是根據上述假設所估計的價格： 

![資料表的螢幕擷取畫面會顯示預估價格。](media/data-migration-guidance-s3-to-azure-storage/pricing-table.png)

### <a name="additional-references"></a>其他參考 
- [Amazon Simple Storage Service 連接器](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service)
- [Azure Blob 儲存體連接器](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Azure Data Lake Storage Gen2 連接器](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [複製活動效能調整指南](https://docs.microsoft.com/azure/data-factory/copy-activity-performance) (機器翻譯)
- [建立和設定自我裝載整合執行階段](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime) (機器翻譯)
- [自我裝載整合執行階段 HA 及可擴縮性](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability) (機器翻譯)
- [資料移動安全性考量](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations) (機器翻譯)
- [在 Azure Key Vault 中儲存認證](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) (機器翻譯)
- [根據時間分割檔案名稱，以累加方式複製檔案](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool) (機器翻譯)
- [根據 LastModifiedDate 複製新增和變更的檔案](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool) (機器翻譯)
- [DF 定價頁面](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="template"></a>[範本]

以下是開始將由數以百萬計檔案所組成數 PB 資料從 Amazon S3 遷移至 Azure Data Lake Storage Gen2 的[範本](solution-template-migration-s3-azure.md)。

## <a name="next-steps"></a>後續步驟

- [使用 Azure Data Factory 從多個容器複製檔案](solution-template-copy-files-multiple-containers.md)
