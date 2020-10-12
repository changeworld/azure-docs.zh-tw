---
title: 將資料從內部部署 Netezza 伺服器遷移至 Azure
description: 使用 Azure Data Factory 將資料從內部部署 Netezza 伺服器遷移至 Azure。
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 9/03/2019
ms.openlocfilehash: 2197136b86d0bfbb2de79af6712c953339d46371
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89442832"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-netezza-server-to-azure"></a>使用 Azure Data Factory 將資料從內部部署 Netezza 伺服器遷移至 Azure 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory 提供高效能、穩固且符合成本效益的機制，可將大規模的資料從內部部署 Netezza 伺服器遷移至 Azure 儲存體帳戶，或 Azure Synapse Analytics (先前的 SQL 資料倉儲) 資料庫中。 

本文為資料工程師和開發人員提供下列資訊：

> [!div class="checklist"]
> * 效能 
> * 複本恢復功能
> * 網路安全性
> * 高階解決方案架構 
> * 實作最佳做法  

## <a name="performance"></a>效能

Azure Data Factory 提供了無伺服器架構，可允許各種層級的平行處理原則。 如果您是開發人員，這表示您可以建立管線，以充分使用網路和資料庫頻寬，以將您環境的資料移動輸送量最大化。

![效能圖表](media/data-migration-guidance-netezza-azure-sqldw/performance.png)

上圖可解讀如下：

- 單一複製活動可以利用可擴充的計算資源。 當您使用 Azure Integration Runtime 時， [最多](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) 可為每個複製活動指定以無伺服器方式為256的 diu。 使用自我裝載整合執行時間 (自我裝載的整合執行時間) ，您可以手動向上擴充電腦或向外延展至多部電腦 ([最多四個節點](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)) ，而單一複製活動會將其分割區分散到所有節點。 

- 單一複製活動會使用多個執行緒來讀取和寫入資料存放區。 

- Azure Data Factory 控制流程可以平行啟動多個複製活動。 例如，它可以使用 [For each 迴圈](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)來啟動它們。 

如需詳細資訊，請參閱 [複製活動效能和擴充性指南](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)。

## <a name="resilience"></a>恢復功能

在單一複製活動執行中，Azure Data Factory 有內建的重試機制，可讓它在資料存放區或基礎網路中處理特定層級的暫時性失敗。

使用 Azure Data Factory 複製活動時，當您在來源和接收資料存放區之間複製資料時，您有兩種方式可以處理不相容的資料列。 您可以中止並使複製活動失敗，或略過不相容的資料列，繼續複製其餘的資料。 此外，若要瞭解失敗的原因，您可以在 Azure Blob 儲存體或 Azure Data Lake 存放區中記錄不相容的資料列、修正資料來源上的資料，然後重試複製活動。

## <a name="network-security"></a>網路安全性 

根據預設，Azure Data Factory 會將資料從內部部署 Netezza 伺服器傳輸到 Azure 儲存體帳戶或 Azure Synapse Analytics 資料庫，方法是透過超文字傳輸通訊協定（安全 (HTTPS) ）來使用加密的連接。 HTTPS 會提供傳輸中的資料加密，並可防止竊聽和中間人攻擊。

或者，如果您不想要透過公用網際網路傳送資料，您可以透過 Azure Express Route 透過私人對等互連連結來傳輸資料，以達到更高的安全性。 

下一節將討論如何達到更高的安全性。

## <a name="solution-architecture"></a>方案架構

本節將討論兩種遷移資料的方式。

### <a name="migrate-data-over-the-public-internet"></a>透過公用網際網路遷移資料

![透過公用網際網路遷移資料](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-public-network.png)

上圖可解讀如下：

- 在此架構中，您會透過公用網際網路使用 HTTPS 安全地傳輸資料。

- 若要達成此架構，您必須在公司防火牆後方的 Windows 電腦上，安裝 Azure Data Factory 整合執行時間 (自我裝載) 。 請確定此 integration runtime 可以直接存取 Netezza 伺服器。 若要完整地使用您的網路和資料存放區頻寬來複製資料，您可以手動擴大電腦或向外延展至多部電腦。

- 藉由使用此架構，您可以同時遷移初始快照集資料和差異資料。

### <a name="migrate-data-over-a-private-network"></a>透過私人網路遷移資料 

![透過私人網路遷移資料](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-private-network.png)

上圖可解讀如下：

- 在此架構中，您會透過 Azure Express Route 以私人對等互連連結來遷移資料，而資料永遠不會通過公用網際網路。 

- 若要達成此架構，您需要在 Azure 虛擬網路中的 Windows 虛擬機器 (VM) 上，安裝 Azure Data Factory 整合執行時間 (自我裝載) 。 若要完全使用您的網路和資料存放區頻寬來複製資料，您可以手動將 VM 擴大，或向外延展至多個 Vm。

- 藉由使用此架構，您可以同時遷移初始快照集資料和差異資料。

## <a name="implement-best-practices"></a>實施最佳做法 

### <a name="manage-authentication-and-credentials"></a>管理驗證和認證 

- 若要驗證 Netezza，您可以透過 [連接字串使用 ODBC 驗證](https://docs.microsoft.com/azure/data-factory/connector-netezza#linked-service-properties)。 

- 若要向 Azure Blob 儲存體進行驗證： 

   - 我們強烈建議使用 [適用于 Azure 資源的受控](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity)識別。 受控識別是以 Azure Active Directory (Azure AD) 中自動管理的 Azure Data Factory 識別為基礎，可讓您設定管線，而不需要在連結服務定義中提供認證。  

   - 或者，您可以使用 [服務主體](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication)、 [共用存取](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)簽章或 [儲存體帳戶金鑰](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication)來驗證 Azure Blob 儲存體。 

- 若要驗證 Azure Data Lake Storage Gen2： 

   - 我們強烈建議使用 [適用于 Azure 資源的受控](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity)識別。
   
   - 您也可以使用 [服務主體](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) 或 [儲存體帳戶金鑰](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication)。 

- 若要驗證 Azure Synapse Analytics：

   - 我們強烈建議使用 [適用于 Azure 資源的受控](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#managed-identity)識別。
   
   - 您也可以使用 [服務主體](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#service-principal-authentication) 或 [SQL 驗證](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#sql-authentication)。

- 當您未使用適用于 Azure 資源的受控識別時，強烈建議您將 [認證儲存在 Azure Key Vault 中](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) ，讓您可以更輕鬆地集中管理及輪替金鑰，而不需要修改 Azure Data Factory 連結的服務。 這也是其中一個 [CI/CD 的最佳做法](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd)。 

### <a name="migrate-initial-snapshot-data"></a>遷移初始快照集資料 

若為小型資料表 (也就是，磁片區小於 100 GB 或可在兩個小時內遷移至 Azure 的資料表) ，您可以讓每個複製作業每個資料表載入資料。 若要取得更高的輸送量，您可以執行多個 Azure Data Factory 複製作業，同時載入不同的資料表。 

在每個複製作業中，若要依分割區執行平行查詢和複製資料，您也可以使用[ `parallelCopies` 屬性設定](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#parallel-copy)搭配下列其中一個資料分割選項，以達到某個層級的平行處理原則：

- 為了協助達成更高的效率，我們鼓勵您從資料配量著手。  請確定設定中的值 `parallelCopies` 小於您在 Netezza 伺服器上資料表中的資料配量分割區總數。  

- 如果每個資料配量分割區的磁片區仍是大型的 (例如 10 GB 或更大的) ，我們建議您切換至動態範圍的資料分割。 此選項可讓您以更大的彈性來定義分割區數目和每個分割區的數量（依分割區資料行、上限和下限）。

針對較大的資料表 (也就是，磁片區為 100 GB 或以上的資料表，或在兩個小時內 *無法* 遷移至 Azure) ，我們建議您依自訂查詢分割資料，然後將每個複製作業複製一個資料分割一次。 為了獲得更好的輸送量，您可以同時執行多個 Azure Data Factory 複製作業。 針對透過自訂查詢載入一個資料分割的每個複製作業目標，您可以透過資料配量或動態範圍啟用平行處理原則來增加輸送量。 

如果任何複製作業因為網路或資料存放區暫時性問題而失敗，您可以重新執行失敗的複製作業，以從資料表重載該特定的資料分割。 其他載入其他資料分割的複製作業不會受到影響。

當您將資料載入 Azure Synapse Analytics 資料庫時，建議您在使用 Azure Blob 儲存體作為預備環境的複製作業中啟用 PolyBase。

### <a name="migrate-delta-data"></a>遷移差異資料 

若要從資料表中識別新的或更新的資料列，請在架構中使用時間戳資料行或遞增索引鍵。 然後，您可以將最新的值儲存為外部資料表中的高水位線，然後用它來篩選下次載入資料時的差異資料。 

每個資料表都可以使用不同的浮水印資料行來識別其新的或更新的資料列。 建議您建立外部控制資料表。 在資料表中，每個資料列都代表 Netezza 伺服器上的一個資料表，以及其特定的浮水印資料行名稱和高水位線值。 

### <a name="configure-a-self-hosted-integration-runtime"></a>設定自我裝載整合執行時間

如果您要將資料從 Netezza 伺服器遷移至 Azure，不論伺服器是位於公司防火牆後方或虛擬網路環境中，您都需要在 Windows 電腦或 VM （也就是用來移動資料的引擎）上安裝自我裝載 IR。 當您安裝自我裝載 IR 時，建議您採用下列方法：

- 針對每部 Windows 機器或 VM，從 32 vCPU 和 128 GB 記憶體的設定開始。 您可以在資料移轉期間持續監視 IR 機器的 CPU 和記憶體使用量，以瞭解您是否需要進一步擴大電腦的效能，以提升效能，或縮小電腦以節省成本。

- 您也可以使用單一的自我裝載 IR，將最多四個節點相關聯，以進行相應放大。 針對自我裝載 IR 執行的單一複製作業會自動套用所有 VM 節點，以平行方式複製資料。 為了達到高可用性，請從四個 VM 節點開始，以避免在資料移轉期間發生單一失敗點。

### <a name="limit-your-partitions"></a>限制磁碟分割

最佳做法是以具代表性範例資料集的 (POC) 來進行效能證明，讓您可以判斷每個複製活動的適當分割區大小。 建議您在兩個小時內，將每個分割區載入至 Azure。  

若要複製資料表，請從單一的複製活動開始，並使用單一的自我裝載 IR 電腦。 `parallelCopies`根據資料表中的資料配量分割區數目，逐漸增加設定。 查看是否可在兩個小時內，根據複製作業所產生的輸送量，將整個資料表載入至 Azure。 

如果在兩個小時內無法將其載入至 Azure，且未完全使用自我裝載 IR 節點和資料存放區的容量，則會逐漸增加並行複製活動的數目，直到達到您的網路限制或資料存放區的頻寬限制為止。 

持續監視自我裝載 IR 機器上的 CPU 和記憶體使用量，並準備好在您看到 CPU 和記憶體完全使用時，擴大電腦或向外延展至多部電腦。 

當您遇到節流錯誤（如 Azure Data Factory 複製活動所報告）時，請減少 Azure Data Factory 中的平行存取或 `parallelCopies` 設定，或考慮增加網路和資料存放區的頻寬或 i/o 作業 (IOPS) 限制。 


### <a name="estimate-your-pricing"></a>預估您的定價 

請考慮下列管線，此管線的結構是用來將資料從內部部署 Netezza 伺服器遷移至 Azure Synapse Analytics 資料庫：

![定價管線](media/data-migration-guidance-netezza-azure-sqldw/pricing-pipeline.png)

假設下列陳述成立： 

- 資料量總計為 50 tb (TB) 。 

- 我們會使用第一個解決方案架構來遷移資料， (Netezza 伺服器位於防火牆後方的內部部署環境) 。

- 50-TB 磁片區會分割成500分割區，而且每個複製活動都會移動一個資料分割。

- 每個複製活動都會針對四部電腦設定一個自我裝載的 IR，並可達到每秒 20 mb 的輸送量 (MBps) 。 在複製活動內 (， `parallelCopies` 設定為4，而從資料表載入資料的每個執行緒都會達到 5 MBps 的輸送量。 ) 

- ForEach concurrency 設定為3，而匯總輸送量為 60 MBps。

- 總共需要243小時才能完成遷移。

根據上述假設，以下是預估價格： 

![定價表](media/data-migration-guidance-netezza-azure-sqldw/pricing-table.png)

> [!NOTE]
> 上表所示的定價為假設。 實際定價取決於環境中的實際輸送量。 未包含已安裝自我裝載 IR 的 Windows 機器 (的價格) 。 

### <a name="additional-references"></a>其他參考資料

如需詳細資訊，請參閱下列文章和指南：

- [使用 Azure Data Factory 將資料從內部部署的關聯式資料倉儲資料庫移轉至 Azure](https://azure.microsoft.com/resources/data-migration-from-on-premise-relational-data-warehouse-to-azure-data-lake-using-azure-data-factory/)
- [Netezza 連接器](https://docs.microsoft.com/azure/data-factory/connector-netezza)
- [ODBC 連接器](https://docs.microsoft.com/azure/data-factory/connector-odbc)
- [Azure Blob 儲存體連接器](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Azure Data Lake Storage Gen2 連接器](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Azure Synapse Analytics 連接器](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse)
- [複製活動效能調整指南](https://docs.microsoft.com/azure/data-factory/copy-activity-performance) (機器翻譯)
- [建立和設定自我裝載整合執行階段](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [自我裝載整合執行階段 HA 及可擴縮性](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability) (機器翻譯)
- [資料移動安全性考量](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations) (機器翻譯)
- [在 Azure Key Vault 中儲存認證](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) (機器翻譯)
- [從一個資料表以累加方式複製資料](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-portal)
- [從多個資料表以累加方式複製資料](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-multiple-tables-portal)
- [Azure Data Factory 定價頁面](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>後續步驟

- [使用 Azure Data Factory 從多個容器複製檔案](solution-template-copy-files-multiple-containers.md)
