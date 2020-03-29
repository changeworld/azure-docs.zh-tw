---
title: 將資料從 Amazon S3 遷移到 Azure 儲存體
description: 使用 Azure 資料工廠將資料從 Amazon S3 遷移到 Azure 存儲。
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
ms.openlocfilehash: 6f2db91a35573bc2cbdd0df2cb1ac09914cc956b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122639"
---
# <a name="use-azure-data-factory-to-migrate-data-from-amazon-s3-to-azure-storage"></a>使用 Azure 資料工廠將資料從 Amazon S3 遷移到 Azure 存儲 

Azure 資料工廠提供了一種性能強、可靠且經濟高效的機制，用於將資料從 Amazon S3 大規模遷移到 Azure Blob 存儲或 Azure 資料湖存儲 Gen2。  本文為數據工程師和開發人員提供了以下資訊： 

> [!div class="checklist"]
> * 效能 
> * 複製彈性
> * 網路安全性
> * 高級解決方案架構 
> * 實施最佳實踐  

## <a name="performance"></a>效能

ADF 提供一種無伺服器架構，允許在不同級別上並行化，允許開發人員構建管道，以充分利用您的網路頻寬以及存儲 IOPS 和頻寬，從而最大限度地提高環境的資料移動輸送量。 

客戶已成功將由數億個檔組成的資料 PB 從 Amazon S3 遷移到 Azure Blob 存儲，持續輸送量為 2 GBp 及以上。 

![效能](media/data-migration-guidance-s3-to-azure-storage/performance.png)

上圖說明瞭如何通過不同級別的並行性實現出色的資料移動速度：
 
- 單個複製活動可以利用可擴展的計算資源：在使用 Azure 集成運行時時，您可以以無伺服器方式為每個複製活動指定[多達 256 個 DIA;](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units)使用自託管集成運行時時，可以手動擴展電腦或擴展到多台電腦（[最多 4 個節點](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)），單個複製活動將跨所有節點對其檔集進行分區。 
- 單個複製活動使用多個執行緒從資料存儲讀取和寫入。 
- ADF 控制流可以並行啟動多個複製活動，例如使用[For each 迴圈](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)。 

## <a name="resilience"></a>復原能力

在單個複製活動運行中，ADF 具有內置的重試機制，因此可以處理資料存儲或基礎網路中的一定級別的瞬態故障。 

當執行從 S3 到 Blob 的二進位複製以及從 S3 複製到 ADLS Gen2 時，ADF 會自動執行檢查點檢查。  如果複製活動運行失敗或超時，則在後續重試時，副本將從最後一個失敗點恢復，而不是從開頭開始。 

## <a name="network-security"></a>網路安全性 

預設情況下，ADF 通過 HTTPS 協定使用加密連接將資料從 Amazon S3 傳輸到 Azure Blob 存儲或 Azure 資料存儲第 2 代。  HTTPS 提供傳輸過程中的資料加密，並防止竊聽和中間人攻擊。 

或者，如果您不希望通過公共 Internet 傳輸資料，則可以通過 AWS 直接連接和 Azure 快速路由之間的專用對等互連鏈路傳輸資料，從而實現更高的安全性。  有關如何實現此目的，請參閱下面的解決方案體系結構。 

## <a name="solution-architecture"></a>方案架構

通過公共 Internet 遷移資料：

![解決方案-架構-公共網路](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-public-network.png)

- 在此體系結構中，資料通過公共 Internet 使用 HTTPS 安全地傳輸。 
- 源 Amazon S3 以及目標 Azure Blob 存儲或 Azure 資料存儲源第 2 代都配置為允許來自所有網路 IP 位址的流量。  請參閱下面的第二個體系結構，瞭解如何將網路訪問限制為特定的 IP 範圍。 
- 您可以輕鬆以無伺服器方式擴展馬力，以充分利用網路和存儲頻寬，從而獲得環境的最佳輸送量。 
- 使用此體系結構可以實現初始快照遷移和增量資料移轉。 

通過專用連結遷移資料： 

![解決方案-架構-私人網路絡](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-private-network.png)

- 在此體系結構中，資料移轉通過 AWS 直接連接和 Azure 快速路由之間的專用對等互連鏈路完成，這樣資料就不會通過公共 Internet 遍歷。  它要求使用 AWS VPC 和 Azure 虛擬網路。 
- 您需要在 Azure 虛擬網路中的 Windows VM 上安裝 ADF 自託管集成運行時，以實現此體系結構。  您可以手動擴展自託管的 IR VM，或擴展到多個 VM（最多 4 個節點），以充分利用網路和存儲 IOPS/頻寬。 
- 如果可以通過 HTTPS 傳輸資料，但您希望將源 S3 的網路訪問鎖定到特定的 IP 範圍，則可以通過刪除 AWS VPC 並將專用鏈路替換為 HTTPS 來採用此體系結構的變體。  您需要在 Azure VM 上保留 Azure 虛擬和自託管 IR，以便具有用於白名單的靜態公開可路由 IP。 
- 使用此體系結構可以實現初始快照資料移轉和增量資料移轉。 

## <a name="implementation-best-practices"></a>實施最佳實踐 

### <a name="authentication-and-credential-management"></a>身份驗證和憑據管理 

- 要對 Amazon S3 帳戶進行身份驗證，您必須使用[IAM 帳戶的訪問金鑰](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service#linked-service-properties)。 
- 支援多種身份驗證類型以連接到 Azure Blob 存儲。  強烈建議[使用 Azure 資源的託管標識](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity)：構建在 Azure AD 中自動託管的 ADF 標識之上，它允許您配置管道，而無需在連結服務定義中提供憑據。  或者，可以使用[服務主體](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication)、[共用訪問簽名](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)或[存儲帳戶金鑰](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication)對 Azure Blob 存儲進行身份驗證。 
- 還支援多種身份驗證類型以連接到 Azure 資料湖存儲 Gen2。  強烈建議[使用 Azure 資源的託管標識](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity)，儘管也可以使用[服務主體](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication)或[存儲帳戶金鑰](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication)。 
- 如果不對 Azure 資源使用託管標識，強烈建議[在 Azure 金鑰保存庫中存儲憑據](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)，以便更輕鬆地集中管理和旋轉金鑰，而無需修改 ADF 連結服務。  這也是[CI/CD 的最佳做法](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd)之一。 

### <a name="initial-snapshot-data-migration"></a>初始快照資料移轉 

建議進行資料分區，尤其是在遷移超過 100 TB 的資料時。  要對資料進行分區，請利用"首碼"設置按名稱篩選 Amazon S3 中的資料夾和檔，然後每個 ADF 複製作業可以一次複製一個分區。  您可以同時運行多個 ADF 複製作業，以便提高輸送量。 

如果任何複製作業由於網路或資料存儲暫時性問題而失敗，則可以重新運行失敗的複製作業，以便再次從 AWS S3 重新載入該特定分區。  載入其他分區的所有其他複製作業將不會受到影響。 

### <a name="delta-data-migration"></a>增量資料移轉 

識別 AWS S3 中新檔或更改檔的最性能方法是使用時間分區命名約定 - 當 AWS S3 中的資料已用檔或資料夾名稱中的時間片資訊（例如/yyyyy/mm/dd/file.csv）進行分區時，您的管道可以輕鬆地識別要增量複製的檔/資料夾。 

或者，如果 AWS S3 中的資料未進行時間分區，ADF 可以按其上次修改日期識別新檔或更改的檔。   其工作方式是 ADF 將從 AWS S3 掃描所有檔，並僅複製上次修改的時間戳記大於特定值的新檔和更新檔。  請注意，如果 S3 中包含大量檔，則無論有多少檔與篩選器條件匹配，初始檔掃描都可能需要很長時間。  在這種情況下，建議您首先對資料進行分區，使用相同的"首碼"設置進行初始快照遷移，以便檔掃描可以並行進行。  

### <a name="for-scenarios-that-require-self-hosted-integration-runtime-on-azure-vm"></a>對於需要在 Azure VM 上自承載的集成運行時的方案 

無論您是通過專用鏈路遷移資料，還是希望在 Amazon S3 防火牆上允許特定的 IP 範圍，都需要在 Azure Windows VM 上安裝自託管的集成運行時。 

- 每個 Azure VM 的推薦配置Standard_D32s_v3 32 vCPU 和 128 GB 記憶體。  您可以在資料移轉期間繼續監視 IR VM 的 CPU 和記憶體利用率，以查看是否需要進一步擴展 VM 以提高性能或縮減 VM 以節省成本。 
- 還可以通過將最多 4 個 VM 節點與單個自承載的 IR 關聯出來進行橫向擴展。  針對自託管 IR 運行的單個複製作業將自動對檔集進行分區，並利用所有 VM 節點並行複製檔。  為獲得高可用性，建議從 2 個 VM 節點開始，以避免資料移轉期間的單點故障。 

### <a name="rate-limiting"></a>速率限制 

最佳做法是使用具有代表性的示例資料集執行性能 POC，以便確定適當的分區大小。 

從單個分區和單個複製活動開始，設置預設 DIU。  逐漸增加 DIU 設置，直到達到網路的頻寬限制或資料存儲的 IOPS/頻寬限制，或者已達到單個複製活動允許的最大 256 DIU。 

接下來，逐步增加併發複製活動的數量，直到達到環境的限制。 

當您遇到 ADF 複製活動報告的限制錯誤時，請減少 ADF 中的併發或 DIU 設置，或者考慮增加網路和資料存儲的頻寬/IOPS 限制。  

### <a name="estimating-price"></a>估計價格 

> [!NOTE]
> 這是一個假設的定價示例。  您的實際定價取決於環境中的實際輸送量。

請考慮為將資料從 S3 遷移到 Azure Blob 存儲而構建的以下管道： 

![定價-管道](media/data-migration-guidance-s3-to-azure-storage/pricing-pipeline.png)

讓我們假設以下內容： 

- 資料總容量為 2 PB 
- 使用第一個解決方案體系結構通過 HTTPS 遷移資料 
- 2 PB 分為 1 K 分區，每個副本移動一個分區 
- 每個副本都配置了 DIU=256，並實現了 1 GBps 輸送量 
- 對於每個併發設置為 2，聚合輸送量為 2 GBps 
- 總共需要 292 小時才能完成遷移 

以下是基於上述假設的估計價格： 

![定價表](media/data-migration-guidance-s3-to-azure-storage/pricing-table.png)

### <a name="additional-references"></a>其他參考 
- [亞馬遜簡單存儲服務連接器](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service)
- [Azure Blob 儲存體連接器](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Azure Data Lake Storage Gen2 連接器](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [複製活動性能調優指南](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [創建和配置自託管集成運行時](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [自託管集成運行時 HA 和可擴充性](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [資料移動安全性考量](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [在 Azure Key Vault 中儲存認證](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [根據時間分區的檔案名以增量方式複製檔](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [根據上次修改日期複製新檔和更改的檔](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [ADF 定價頁面](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="template"></a>[範本]

下面是從遷移 PB 的資料的[範本](solution-template-migration-s3-azure.md)，這些資料由數億個檔組成，這些檔從 Amazon S3 遷移到 Azure 資料湖存儲 Gen2。

## <a name="next-steps"></a>後續步驟

- [使用 Azure Data Factory 從多個容器複製檔案](solution-template-copy-files-multiple-containers.md)
