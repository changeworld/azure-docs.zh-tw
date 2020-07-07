---
title: 將資料從內部部署 Netezza 伺服器遷移至 Azure
description: 使用 Azure Data Factory，將資料從內部部署 Netezza 伺服器遷移至 Azure。
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
ms.openlocfilehash: a0263880262da95f4d26ee8388da464e9a59efca
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81416447"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-netezza-server-to-azure"></a>使用 Azure Data Factory 將資料從內部部署 Netezza 伺服器遷移至 Azure 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory 提供高效能、強大且符合成本效益的機制，將內部部署 Netezza 伺服器的資料大規模遷移至您的 Azure 儲存體帳戶或 Azure SQL 資料倉儲資料庫。 

本文為資料工程師和開發人員提供下列資訊：

> [!div class="checklist"]
> * 效能 
> * 複本恢復功能
> * 網路安全性
> * 高階解決方案架構 
> * 實作最佳做法  

## <a name="performance"></a>效能

Azure Data Factory 提供無伺服器架構，允許各種層級的平行處理。 如果您是開發人員，這表示您可以建立管線來完全使用網路和資料庫頻寬，以最大化環境的資料移動輸送量。

![效能圖表](media/data-migration-guidance-netezza-azure-sqldw/performance.png)

上述圖表可以解讀如下：

- 單一複製活動可以利用可調整的計算資源。 當您使用 Azure Integration Runtime 時，您可以用無伺服器的方式為每個複製活動指定[最多 256 diu](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) 。 使用自我裝載整合執行時間（自我裝載 IR）時，您可以手動相應增加機器或相應放大至多部電腦（[最多四個節點](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)），而單一複製活動會將其分割區分散到所有節點。 

- 單一複製活動會使用多個執行緒來讀取和寫入資料存放區。 

- Azure Data Factory 控制流程可以平行啟動多個複製活動。 例如，它可以使用[For each 迴圈](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)來啟動它們。 

如需詳細資訊，請參閱[複製活動效能和擴充性指南](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)。

## <a name="resilience"></a>恢復功能

在單一複製活動執行中，Azure Data Factory 具有內建的重試機制，它可讓它處理資料存放區或基礎網路中特定層級的暫時性失敗。

使用 Azure Data Factory 複製活動時，當您在來源和接收資料存放區之間複製資料時，有兩種方式可處理不相容的資料列。 您可以中止並使複製活動失敗，或略過不相容的資料列來繼續複製資料的其餘部分。 此外，若要瞭解失敗的原因，您可以在 Azure Blob 儲存體或 Azure Data Lake 存放區中記錄不相容的資料列、修正資料來源上的資料，然後重試複製活動。

## <a name="network-security"></a>網路安全性 

根據預設，Azure Data Factory 會使用透過超文字安全傳輸通訊協定（HTTPS）的加密連線，將資料從內部部署 Netezza 伺服器傳送至 Azure 儲存體帳戶或 Azure SQL 資料倉儲資料庫。 HTTPS 會提供傳輸中的資料加密，並可防止竊聽和中間人攻擊。

或者，如果您不想透過公用網際網路傳送資料，您可以透過 Azure Express Route 透過私人對等互連連結傳輸資料，以協助達到更高的安全性。 

下一節將討論如何達成更高的安全性。

## <a name="solution-architecture"></a>方案架構

本節討論兩種遷移資料的方式。

### <a name="migrate-data-over-the-public-internet"></a>透過公用網際網路遷移資料

![透過公用網際網路遷移資料](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-public-network.png)

上述圖表可以解讀如下：

- 在此架構中，您可以使用 HTTPS 透過公用網際網路來安全地傳輸資料。

- 若要達成此架構，您必須在公司防火牆後方的 Windows 電腦上安裝 Azure Data Factory integration runtime （自我裝載）。 請確定此整合執行時間可以直接存取 Netezza 伺服器。 若要完全使用您的網路和資料存放區頻寬來複製資料，您可以手動相應增加電腦或相應放大至多部電腦。

- 藉由使用此架構，您可以遷移初始快照集資料和差異資料。

### <a name="migrate-data-over-a-private-network"></a>透過私人網路遷移資料 

![透過私人網路遷移資料](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-private-network.png)

上述圖表可以解讀如下：

- 在此架構中，您會透過 Azure Express Route，透過私用對等互連連結來遷移資料，而資料永遠不會通過公用網際網路。 

- 若要達成此架構，您必須在 Azure 虛擬網路內的 Windows 虛擬機器（VM）上安裝 Azure Data Factory integration runtime （自我裝載）。 若要完全使用您的網路和資料存放區頻寬來複製資料，您可以手動相應增加 VM 或相應放大至多個 Vm。

- 藉由使用此架構，您可以遷移初始快照集資料和差異資料。

## <a name="implement-best-practices"></a>實行最佳做法 

### <a name="manage-authentication-and-credentials"></a>管理驗證和認證 

- 若要向 Netezza 進行驗證，您可以透過[連接字串使用 ODBC 驗證](https://docs.microsoft.com/azure/data-factory/connector-netezza#linked-service-properties)。 

- 若要向 Azure Blob 儲存體進行驗證： 

   - 我們強烈建議使用[適用于 Azure 資源的受控](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity)識別。 受控識別建置於自動受管理的 Azure Data Factory 身分識別 Azure Active Directory （Azure AD）中，可讓您設定管線，而不需要在連結服務定義中提供認證。  

   - 或者，您可以使用[服務主體](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication)、[共用存取](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)簽章或[儲存體帳戶金鑰](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication)，向 Azure Blob 儲存體進行驗證。 

- 若要驗證 Azure Data Lake Storage Gen2： 

   - 我們強烈建議使用[適用于 Azure 資源的受控](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity)識別。
   
   - 您也可以使用[服務主體](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication)或[儲存體帳戶金鑰](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication)。 

- 若要驗證 Azure SQL 資料倉儲：

   - 我們強烈建議使用[適用于 Azure 資源的受控](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#managed-identity)識別。
   
   - 您也可以使用[服務主體](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#service-principal-authentication)或[SQL 驗證](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#sql-authentication)。

- 當您未使用 Azure 資源的受控識別時，強烈建議您將[認證儲存在 Azure Key Vault 中](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)，以便在不需要修改 Azure Data Factory 連結服務的情況下，更輕鬆地集中管理和旋轉金鑰。 這也是其中一個 [CI/CD 的最佳做法](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd)。 

### <a name="migrate-initial-snapshot-data"></a>遷移初始快照集資料 

針對小型資料表（也就是，磁片區小於 100 GB 或可在兩個小時內遷移至 Azure 的資料表），您可以讓每個複製作業在每個資料表中載入資料。 如需更大的輸送量，您可以執行多個 Azure Data Factory 複製作業，以同時載入個別的資料表。 

在每個複製作業中，若要依分割區執行平行查詢和複製資料，您也可以使用[ `parallelCopies` 屬性設定](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#parallel-copy)搭配下列其中一個資料分割選項，達到某種程度的平行處理原則：

- 為了協助達成更高的效率，我們鼓勵您從資料配量開始。  請確定設定中的值 `parallelCopies` 小於您在 Netezza 伺服器上資料表中的資料配量分割區總數。  

- 如果每個資料配量分割區的數量仍然很大（例如 10 GB 或更大），建議您切換到動態範圍分割區。 此選項可讓您更有彈性地依資料分割資料行、上限和下限來定義資料分割的數目和每個分割區的磁片區數量。

對於較大的資料表（亦即，磁片區為 100 GB 或以上，或在兩個小時內*無法*遷移至 Azure 的資料表），我們建議您依自訂查詢來分割資料，然後讓每個複製作業一次複製一個分割區。 為獲得更好的輸送量，您可以同時執行多個 Azure Data Factory 複製作業。 針對透過自訂查詢載入一個資料分割的每個複製作業目標，您可以透過資料配量或動態範圍啟用平行處理原則來增加輸送量。 

如果因為網路或資料存放區暫時性問題而導致任何複製工作失敗，您可以重新執行失敗的複製作業，從資料表重載該特定的分割區。 其他載入其他分割區的複製作業不會受到影響。

當您將資料載入 Azure SQL 資料倉儲資料庫時，建議您在複製作業中使用 Azure Blob 儲存體作為預備環境來啟用 PolyBase。

### <a name="migrate-delta-data"></a>遷移差異資料 

若要從您的資料表中識別新的或更新的資料列，請在架構內使用 timestamp 資料行或遞增索引鍵。 接著，您可以將最新的值儲存為外部資料表中的高水位線，然後在下一次載入資料時，用它來篩選差異資料。 

每個資料表都可以使用不同的浮水印資料行來識別其新的或更新的資料列。 我們建議您建立外部控制資料表。 在資料表中，每個資料列都代表 Netezza 伺服器上的一個資料表，以及其特定的浮水印資料行名稱和高水位線值。 

### <a name="configure-a-self-hosted-integration-runtime"></a>設定自我裝載整合執行時間

如果您要將資料從 Netezza 伺服器遷移至 Azure，不論伺服器是在公司防火牆後方或虛擬網路環境中，您都必須在 Windows 機器或 VM 上安裝自我裝載的 IR，這是用來移動資料的引擎。 當您安裝自我裝載 IR 時，我們建議採用下列方法：

- 針對每個 Windows 機器或 VM，啟動 32 vCPU 和 128 GB 記憶體的設定。 在資料移轉期間，您可以持續監視 IR 機器的 CPU 和記憶體使用量，以瞭解您是否需要進一步相應增加機器以提升效能，或相應減少電腦以節省成本。

- 您也可以將最多四個節點與單一的自我裝載 IR 產生關聯，以相應放大。 針對自我裝載 IR 執行的單一複製作業會自動套用所有 VM 節點，以平行方式複製資料。 如需高可用性，請從四個 VM 節點開始，以避免在資料移轉期間發生單一失敗點。

### <a name="limit-your-partitions"></a>限制您的磁碟分割

最佳做法是使用具代表性的範例資料集來進行效能證明（POC），讓您可以針對每個複製活動判斷適當的分割區大小。 我們建議您在兩個小時內，將每個分割區載入至 Azure。  

若要複製資料表，請從具有單一自我裝載 IR 機器的單一複製活動開始。 `parallelCopies`根據資料表中的資料配量分割區數目，逐漸增加設定。 根據複製作業所產生的輸送量，查看是否可在兩個小時內將整個資料表載入至 Azure。 

如果在兩個小時內無法將其載入至 Azure，而且自我裝載 IR 節點和資料存放區的容量未完全使用，則會逐漸增加並行複製活動的數目，直到達到您的網路限制或資料存放區的頻寬限制為止。 

在自我裝載的 IR 機器上持續監視 CPU 和記憶體使用量，並準備好在您看到 CPU 和記憶體完全使用時，相應增加機器或相應放大至多部電腦。 

當您遇到節流錯誤（如 Azure Data Factory 複製活動所回報）時，請減少 Azure Data Factory 中的並行或 `parallelCopies` 設定，或考慮增加網路和資料存放區的每秒 i/o 作業數（IOPS）限制。 


### <a name="estimate-your-pricing"></a>預估您的定價 

請考慮下列管線，其結構是用來將資料從內部部署 Netezza 伺服器遷移至 Azure SQL 資料倉儲資料庫：

![定價管線](media/data-migration-guidance-netezza-azure-sqldw/pricing-pipeline.png)

假設下列語句為 true： 

- 總數據量為 50 tb。 

- 我們正使用第一種解決方案來遷移資料（Netezza 伺服器位於防火牆後方的內部部署）。

- 50-TB 磁片區會分割成500個磁碟分割，且每個複製活動會移動一個分割區。

- 每個複製活動都會針對四部機器設定一個自我裝載 IR，並達到每秒 20 mb 的輸送量（MBps）。 （在複製活動內， `parallelCopies` 會設定為4，而每個從資料表載入資料的執行緒都會達到 5 MBps 的輸送量）。

- ForEach concurrency 已設定為3，而匯總輸送量為 60 MBps。

- 總共需要243小時才能完成遷移。

根據上述假設，以下是預估的價格： 

![定價表](media/data-migration-guidance-netezza-azure-sqldw/pricing-table.png)

> [!NOTE]
> 上表所示的價格是假設的。 實際定價取決於環境中的實際輸送量。 不包含 Windows 機器的價格（已安裝自我裝載 IR）。 

### <a name="additional-references"></a>其他參考

如需詳細資訊，請參閱下列文章和指南：

- [使用 Azure Data Factory 將內部部署關聯式資料倉儲資料庫中的資料移轉至 Azure](https://azure.microsoft.com/resources/data-migration-from-on-premise-relational-data-warehouse-to-azure-data-lake-using-azure-data-factory/)
- [Netezza 連接器](https://docs.microsoft.com/azure/data-factory/connector-netezza)
- [ODBC 連接器](https://docs.microsoft.com/azure/data-factory/connector-odbc)
- [Azure Blob 儲存體連接器](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Azure Data Lake Storage Gen2 連接器](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Azure SQL 資料倉儲連接器](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse)
- [複製活動效能調整指南](https://docs.microsoft.com/azure/data-factory/copy-activity-performance) (機器翻譯)
- [建立和設定自我裝載整合執行階段](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [自我裝載整合執行階段 HA 及可擴縮性](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability) (機器翻譯)
- [資料移動安全性考量](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations) (機器翻譯)
- [在 Azure Key Vault 中儲存認證](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) (機器翻譯)
- [以累加方式從一個資料表複製資料](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-portal)
- [以累加方式從多個資料表複製資料](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-multiple-tables-portal)
- [Azure Data Factory 定價頁面](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>後續步驟

- [使用 Azure Data Factory 從多個容器複製檔案](solution-template-copy-files-multiple-containers.md)
