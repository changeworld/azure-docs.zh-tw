---
title: 將資料從內部部署 Hadoop 叢集遷移至 Azure 儲存體
description: 瞭解如何使用 Azure Data Factory 將資料從內部部署 Hadoop 叢集遷移至 Azure 儲存體。
services: data-factory
ms.author: yexu
author: dearandyxu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 8/30/2019
ms.openlocfilehash: 3e691244c4c03635eb87a7905eff6756da5c04f9
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92638120"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-hadoop-cluster-to-azure-storage"></a>使用 Azure Data Factory 將資料從內部部署 Hadoop 叢集遷移至 Azure 儲存體 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory 提供高效能、穩固且符合成本效益的機制，可將大規模的資料從內部部署 HDFS 遷移至 Azure Blob 儲存體或 Azure Data Lake Storage Gen2。 

Data Factory 提供兩種將資料從內部部署 HDFS 遷移至 Azure 的基本方法。 您可以根據您的案例選取方法。 

- **Data Factory DistCp 模式** (建議的) ：在 Data Factory 中，您可以使用 [DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) (分散式複製) ，將檔案依原樣複製到 Azure Blob 儲存體 (包括 [分段複製](./copy-activity-performance.md#staged-copy)) 或 Azure Data Lake 存放區 Gen2。 使用 Data Factory 與 DistCp 整合，以利用現有的強大叢集來達到最佳的複製輸送量。 您也可以從 Data Factory 獲得彈性排程和統一監視體驗的優點。 根據您的 Data Factory 設定，複製活動會自動建立 DistCp 命令、將資料提交至您的 Hadoop 叢集，然後監視複製狀態。 建議您 Data Factory DistCp 模式，將資料從內部部署 Hadoop 叢集遷移至 Azure。
- **Data Factory 原生整合執行時間模式** ： DistCp 不是所有案例中的選項。 例如，在 Azure 虛擬網路環境中，DistCp 工具不支援搭配 Azure 儲存體虛擬網路端點的 Azure ExpressRoute 私人對等互連。 此外，在某些情況下，您不會想要使用現有的 Hadoop 叢集做為遷移資料的引擎，因此您不會在叢集上放置大量負載，而這可能會影響現有 ETL 作業的效能。 相反地，您可以使用 Data Factory 整合執行時間的原生功能，作為將資料從內部部署 HDFS 複製到 Azure 的引擎。

本文提供這兩種方法的下列資訊：
> [!div class="checklist"]
> * 效能 
> * 複本恢復功能
> * 網路安全性
> * 高階解決方案架構 
> * 實作最佳做法  

## <a name="performance"></a>效能

在 Data Factory DistCp 模式中，輸送量與您獨立使用 DistCp 工具的方式相同。 Data Factory DistCp 模式會將現有 Hadoop 叢集的容量最大化。 您可以使用 DistCp 進行大型內部叢集或內部叢集複製。 

DistCp 使用 MapReduce 來影響其散發、錯誤處理和復原，以及報告。 它會將檔案和目錄的清單展開為工作對應的輸入。 每個工作都會複製來源清單中指定的檔案分割。 您可以使用與 DistCp 整合的 Data Factory 來建立管線，以充分利用您的網路頻寬、儲存體 IOPS 和頻寬，以將您環境的資料移動輸送量最大化。  

Data Factory 原生整合執行時間模式也允許不同層級的平行處理原則。 您可以使用平行處理原則來充分利用您的網路頻寬、儲存體 IOPS 和頻寬，以將資料移動輸送量最大化：

- 單一複製活動可以利用可擴充的計算資源。 使用自我裝載整合執行時間，您可以手動擴大電腦或向外延展至多部電腦， ([最多四個節點](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)) 。 單一複製活動會在所有節點上分割其檔案集。 
- 單一複製活動會使用多個執行緒來讀取和寫入資料存放區。 
- Data Factory 控制流程可以平行啟動多個複製活動。 例如，您可以使用 [For each 迴圈](./control-flow-for-each-activity.md)。 

如需詳細資訊，請參閱 [複製活動效能指南](./copy-activity-performance.md)。

## <a name="resilience"></a>恢復功能

在 Data Factory DistCp 模式中，您可以使用不同的 DistCp 命令列 (參數，例如， `-i` `-update` 當來源檔案和目的地檔案的) 大小因不同的復原層級而有所不同時，會忽略失敗或寫入資料。

在 Data Factory 原生整合執行時間模式中，在單一複製活動執行中，Data Factory 具有內建的重試機制。 它可以處理資料存放區或基礎網路中特定層級的暫時性失敗。 

從內部部署 HDFS 進行二進位複製到 Blob 儲存體，以及從內部部署 HDFS 複製到 Data Lake Store Gen2 時，Data Factory 會自動執行檢查點到很大的範圍。 如果複製活動執行失敗或超時，則在後續的重試 (確定重試計數為 > 1) ，複本會從最後一個失敗點繼續，而不是從開頭開始。

## <a name="network-security"></a>網路安全性 

根據預設，Data Factory 會使用透過 HTTPS 通訊協定的加密連線，將資料從內部部署 HDFS 傳送到 Blob 儲存體或 Azure Data Lake Storage Gen2。 HTTPS 會提供傳輸中的資料加密，並可防止竊聽和中間人攻擊。 

或者，如果您不想要透過公用網際網路傳輸資料，為了提高安全性，您可以透過 ExpressRoute 透過私人對等互連連結來傳輸資料。 

## <a name="solution-architecture"></a>解決方案架構

此映射描述如何透過公用網際網路來遷移資料：

![此圖顯示透過公用網路來遷移資料的解決方案架構](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-public-network.png)

- 在此架構中，會透過公用網際網路上的 HTTPS 來安全地傳輸資料。 
- 建議您在公用網路環境中使用 Data Factory DistCp 模式。 您可以利用功能強大的現有叢集來達到最佳的複製輸送量。 您也可以從 Data Factory 獲得彈性排程和統一監視體驗的優點。
- 針對此架構，您必須在公司防火牆後方的 Windows 電腦上安裝 Data Factory 的自我裝載整合執行時間，以將 DistCp 命令提交至 Hadoop 叢集並監視複製狀態。 因為電腦不是將資料移動 (的引擎，因此僅) 的控制用途，因此機器的容量不會影響資料移動的輸送量。
- 支援 DistCp 命令中的現有參數。

此映射描述透過私人連結來遷移資料： 

![此圖顯示透過私人網路遷移資料的解決方案架構](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-private-network.png)

- 在此架構中，資料會透過 Azure ExpressRoute 透過私用對等互連連結來進行遷移。 資料永遠不會經過公用網際網路。
- DistCp 工具不支援搭配 Azure 儲存體虛擬網路端點的 ExpressRoute 私人對等互連。 我們建議您透過整合執行時間使用 Data Factory 的原生功能來遷移資料。
- 針對此架構，您必須在 Azure 虛擬網路中的 Windows VM 上安裝 Data Factory 的自我裝載整合執行時間。 您可以手動將 VM 擴大，或向外延展至多個 Vm，以充分利用您的網路和儲存體 IOPS 或頻寬。
- 針對每個) 已安裝 Data Factory 自我裝載整合執行時間的 Azure VM (開始建議的設定，會 Standard_D32s_v3 32 vCPU 和 128 GB 的記憶體。 您可以在資料移轉期間監視 VM 的 CPU 和記憶體使用量，以查看您是否需要擴大 VM 以獲得較佳的效能，或縮小 VM 以降低成本。
- 您也可以使用單一自我裝載整合執行時間，將最多四個 VM 節點相關聯，以進行相應放大。 針對自我裝載整合執行時間執行的單一複製作業會自動分割檔案集，並利用所有 VM 節點以平行方式複製檔案。 為了達到高可用性，我們建議您從兩個 VM 節點開始，以避免在資料移轉期間發生單點失敗的情況。
- 當您使用此架構時，您可以使用初始快照集資料移轉和差異資料移轉。

## <a name="implementation-best-practices"></a>實作最佳做法

建議您在執行資料移轉時遵循這些最佳做法。

### <a name="authentication-and-credential-management"></a>驗證及認證管理 

- 若要驗證 HDFS，您可以使用 [Windows (Kerberos) 或匿名](./connector-hdfs.md#linked-service-properties)。 
- 支援多種驗證類型以連接到 Azure Blob 儲存體。  我們強烈建議使用 [適用于 Azure 資源的受控](./connector-azure-blob-storage.md#managed-identity)識別。 受控識別是以 Azure Active Directory (Azure AD) 中自動管理的 Data Factory 識別為基礎，可讓您設定管線，而不需要在連結服務定義中提供認證。 或者，您可以使用 [服務主體](./connector-azure-blob-storage.md#service-principal-authentication)、 [共用存取](./connector-azure-blob-storage.md#shared-access-signature-authentication)簽章或 [儲存體帳戶金鑰](./connector-azure-blob-storage.md#account-key-authentication)來驗證 Blob 儲存體。 
- 也支援多個驗證類型以連接到 Data Lake Storage Gen2。  我們強烈建議使用 [適用于 Azure 資源的受控](./connector-azure-data-lake-storage.md#managed-identity)識別，但您也可以使用 [服務主體](./connector-azure-data-lake-storage.md#service-principal-authentication) 或 [儲存體帳戶金鑰](./connector-azure-data-lake-storage.md#account-key-authentication)。 
- 當您未使用適用于 Azure 資源的受控識別時，強烈建議您將 [認證儲存在 Azure Key Vault 中](./store-credentials-in-key-vault.md) ，讓您可以更輕鬆地集中管理及輪替金鑰，而不需要修改 Data Factory 連結的服務。 這也是 [CI/CD 的最佳作法](./continuous-integration-deployment.md#best-practices-for-cicd)。 

### <a name="initial-snapshot-data-migration"></a>初始快照集資料移轉 

在 Data Factory DistCp 模式中，您可以建立一個複製活動來提交 DistCp 命令，並使用不同的參數來控制初始資料移轉行為。 

在 Data Factory 原生整合執行時間模式中，我們建議資料分割，特別是當您遷移超過 10 TB 的資料時。 若要分割資料，請使用 HDFS 上的資料夾名稱。 然後，每個 Data Factory 複製作業可以一次複製一個資料夾磁碟分割。 您可以同時執行多個 Data Factory 複製作業，以取得更佳的輸送量。

如果有任何複製作業因為網路或資料存放區暫時性問題而失敗，您可以重新執行失敗的複製作業，以從 HDFS 重載該特定分割區。 其他載入其他分割區的複製工作不受影響。

### <a name="delta-data-migration"></a>差異資料移轉 

在 Data Factory DistCp 模式中，您可以使用 DistCp 命令列參數 `-update` ，在來源檔案和目的地檔案大小不同時寫入資料，以進行差異資料移轉。

在 Data Factory 原生整合模式中，從 HDFS 識別新的或變更的檔案最有效的方式，就是使用時間分割的命名慣例。 當 HDFS 中的資料已使用檔案或資料夾名稱中的時間配量資訊進行時間分割時 (例如， */yyyy/mm/dd/file.csv* ) 時，您的管線可以輕鬆地識別要以累加方式複製的檔案和資料夾。

或者，如果您在 HDFS 中的資料沒有時間分割，Data Factory 可以使用其 **LastModifiedDate** 值來識別新的或變更的檔案。 Data Factory 會掃描 HDFS 中的所有檔案，並只複製最後修改時間戳記大於設定值的新檔案和更新檔案。 

如果您在 HDFS 中有大量檔案，則初始檔案掃描可能需要很長的時間，不論有多少個檔案符合篩選準則。 在此案例中，我們建議您先使用用於初始快照集遷移的相同資料分割來分割資料。 然後，檔案掃描可能會以平行方式進行。

### <a name="estimate-price"></a>預估價格 

請考慮下列管線，以將資料從 HDFS 遷移至 Azure Blob 儲存體： 

![顯示定價管線的圖表](media/data-migration-guidance-hdfs-to-azure-storage/pricing-pipeline.png)

讓我們假設下列資訊： 

- 資料量總計為 1 PB。
- 您可以使用 Data Factory 的原生整合執行時間模式來遷移資料。
- 1 PB 分為1000個分割區，每個複本都會移動一個資料分割。
- 每個複製活動都會使用一個與四部機器相關聯的自我裝載整合執行時間進行設定，而這會達到 500-MBps 的輸送量。
- ForEach concurrency 設定為 **4** ，而匯總輸送量為 2 GBps。
- 總共需要146小時才能完成遷移。

以下是根據我們的假設的預估價格： 

![顯示價格計算的表格](media/data-migration-guidance-hdfs-to-azure-storage/pricing-table.png)

> [!NOTE]
> 這是假設的定價範例。 實際定價取決於環境中的實際輸送量。
> 未包含已安裝自我裝載整合執行時間的 Azure Windows VM (的價格) 。

### <a name="additional-references"></a>其他參考資料

- [HDFS 連接器](./connector-hdfs.md)
- [Azure Blob 儲存體連接器](./connector-azure-blob-storage.md)
- [Azure Data Lake Storage Gen2 連接器](./connector-azure-data-lake-storage.md)
- [複製活動效能調整指南](./copy-activity-performance.md) (機器翻譯)
- [建立和設定自我裝載整合執行階段](./create-self-hosted-integration-runtime.md)
- [自我裝載整合執行時間高可用性和擴充性](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)
- [資料移動安全性考量](./data-movement-security-considerations.md) (機器翻譯)
- [在 Azure Key Vault 中儲存認證](./store-credentials-in-key-vault.md) (機器翻譯)
- [根據時間分割的檔案名，以累加方式複製檔案](./tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)
- [根據 LastModifiedDate 複製新增和變更的檔案](./tutorial-incremental-copy-lastmodified-copy-data-tool.md) (機器翻譯)
- [Data Factory 定價頁面](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>後續步驟

- [使用 Azure Data Factory 從多個容器複製檔案](solution-template-copy-files-multiple-containers.md)