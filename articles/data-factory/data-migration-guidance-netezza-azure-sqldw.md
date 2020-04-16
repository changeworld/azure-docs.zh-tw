---
title: 將資料從本地 Netezza 伺服器移至 Azure
description: 使用 Azure 資料工廠將數據從本地 Netezza 伺服器遷移到 Azure。
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416447"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-netezza-server-to-azure"></a>使用 Azure 資料工廠將資料從本地 Netezza 伺服器移到 Azure 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure 資料工廠提供了一種性能強、可靠且經濟高效的機制,用於將數據從本地 Netezza 伺服器大規模遷移到 Azure 儲存帳戶或 Azure SQL 資料倉庫資料庫。 

本文為資料工程師和開發人員提供以下資訊:

> [!div class="checklist"]
> * 效能 
> * 複製彈性
> * 網路安全性
> * 進階解決方案架構 
> * 實施最佳實務  

## <a name="performance"></a>效能

Azure 資料工廠提供了一個無伺服器體系結構,允許在不同級別進行並行化。 如果您是開發人員,這意味著您可以構建管道以充分利用網路和資料庫頻寬,以最大化環境的數據移動輸送量。

![效能圖](media/data-migration-guidance-netezza-azure-sqldw/performance.png)

前面的關係圖可以解釋為:

- 單個複製活動可以利用可擴展的計算資源。 使用 Azure 整合執行時時,可以以無伺服器方式為每個複製活動指定[多達 256 個 DII。](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) 使用自託管整合時(自託管 IR),您可以手動擴展電腦或擴展到多台電腦([最多四個節點](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)),單個複製活動將分區分佈到所有節點上。 

- 單個複製活動使用多個線程從數據儲存讀取和寫入。 

- Azure 資料工廠控制流可以並行啟動多個複製活動。 例如,它可以使用[For Each 循環](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)啟動它們。 

有關詳細資訊,請參閱[複製活動性能和可伸縮性指南](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)。

## <a name="resilience"></a>復原能力

在單個複製活動運行中,Azure 數據工廠具有內建重試機制,它能夠處理數據存儲或基礎網路中的一定級別的瞬態故障。

使用 Azure 資料工廠複製活動時,在源和接收器數據存儲之間複製數據時,有兩種方法可以處理不相容的行。 您可以中止複製活動並失敗,或者通過跳過不相容的數據行繼續複製其餘資料。 此外,要瞭解故障原因,可以在 Azure Blob 儲存或 Azure 資料湖存儲中記錄不相容的行,修復數據源上的數據,然後重試複製活動。

## <a name="network-security"></a>網路安全性 

預設情況下,Azure 數據工廠透過使用透過超文字傳輸協定安全 (HTTPS) 的加密連線將資料從本地 Netezza 伺服器傳輸到 Azure 儲存帳戶或 Azure SQL 資料倉儲資料庫。 HTTPS 提供傳輸過程中的數據加密,並防止竊聽和中間人攻擊。

或者,如果您不希望通過公共 Internet 傳輸數據,則可以通過 Azure 快速路由通過專用對等互連鏈路傳輸數據,從而幫助實現更高的安全性。 

下一節將討論如何實現更高的安全性。

## <a name="solution-architecture"></a>方案架構

本節討論遷移數據的兩種方法。

### <a name="migrate-data-over-the-public-internet"></a>透過公共網路移轉資料

![透過公共網路移轉資料](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-public-network.png)

前面的關係圖可以解釋為:

- 在此體系結構中,您可以通過在公共 Internet 上使用 HTTPS 安全地傳輸數據。

- 要實現此體系結構,需要在公司防火牆後面的 Windows 電腦上安裝 Azure 數據工廠整合運行時(自託管)。 確保此整合式執行時可以直接存取 Netezza 伺服器。 要充分利用網路和資料儲存頻寬來複製資料,您可以手動擴展電腦或擴展到多台電腦。

- 通過使用此體系結構,可以遷移初始快照數據和增量數據。

### <a name="migrate-data-over-a-private-network"></a>透過專用網路移轉資料 

![透過專用網路移轉資料](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-private-network.png)

前面的關係圖可以解釋為:

- 在此體系結構中,通過 Azure 快速路由通過專用對等互連鏈路遷移數據,並且數據永遠不會通過公共 Internet 遍歷。 

- 要實現此體系結構,需要在 Azure 虛擬網路中的 Windows 虛擬機器 (VM) 上安裝 Azure 資料工廠整合執行時(自託管)。 要充分利用網路和資料儲存頻寬來複製數據,可以手動擴展 VM 或擴展到多個 VM。

- 通過使用此體系結構,可以遷移初始快照數據和增量數據。

## <a name="implement-best-practices"></a>實施最佳實務 

### <a name="manage-authentication-and-credentials"></a>管理身份驗證和認證 

- 要對 Netezza 進行認證,可以透過連線字串 使用[ODBC 認證](https://docs.microsoft.com/azure/data-factory/connector-netezza#linked-service-properties)。 

- 要對 Azure Blob 儲存進行身份驗證, 

   - 我們強烈建議對[Azure 資源使用託管識別](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity)。 託管識別基於 Azure 活動目錄 (Azure AD) 中自動託管的 Azure 資料工廠識別為基礎,允許您配置管道,而無需在連結服務定義中提供認證。  

   - 或者,可以使用[服務主體](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication)、[共享存取簽名](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)或[儲存帳戶金鑰](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication)對 Azure Blob 儲存進行身份驗證。 

- 要對 Azure 數據存儲第 2 代進行身份驗證, 

   - 我們強烈建議對[Azure 資源使用託管識別](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity)。
   
   - 您還可以使用[服務主體](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication)或[儲存帳戶金鑰](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication)。 

- 要對 Azure SQL 數據倉庫進行身份驗證,

   - 我們強烈建議對[Azure 資源使用託管識別](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#managed-identity)。
   
   - 您還可以使用[服務主體](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#service-principal-authentication)或 SQL[認證](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#sql-authentication)。

- 如果不對 Azure 資源使用託管標識,我們強烈建議[將憑據存儲在 Azure 密鑰保管庫中](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault),以便更輕鬆地集中管理和旋轉密鑰,而無需修改 Azure 數據工廠鏈接的服務。 這也是[CI/CD 的最佳做法](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd)之一。 

### <a name="migrate-initial-snapshot-data"></a>移至初始快照資料 

對於小表(即卷小於 100 GB 的表,或者可在兩小時內遷移到 Azure 的表),可以使每個副本作業載入每個表的數據。 為了獲得更高的輸送量,可以運行多個 Azure 數據工廠複製作業以同時載入單獨的表。 

在每個複製作業中,要按分區運行並行查詢和複製數據,還可以通過將[`parallelCopies`屬性設置](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#parallel-copy)與以下數據分區選項之一一使用來達到一定程度的並行性:

- 為了説明提高效率,我們鼓勵您從數據切片開始。  確保設置中`parallelCopies`的值小於 Netezza 伺服器上表中的數據切片分區總數。  

- 如果每個數據切片分區的體積仍然很大(例如,10 GB 或更高),我們建議您切換到動態範圍分區。 此選項使您能夠更靈活地按分區列、上限和下限定義分區數和每個分區的卷。

對於較大的表(即卷為 100 GB 或更高或*兩小時內無法*遷移到 Azure 的表),我們建議您按自定義查詢對數據進行分區,然後使每個複製作業一次複製一個分區。 為了更好的輸送量,可以同時運行多個 Azure 數據工廠複製作業。 對於按自定義查詢載入一個分區的每個複製作業目標,可以通過數據切片或動態範圍啟用並行性來提高輸送量。 

如果任何複製作業由於網路或數據存儲暫時性問題而失敗,則可以重新運行失敗的複製作業以從表中重新載入該特定分區。 載入其他分區的其他複製作業不受影響。

將資料載入到 Azure SQL 資料倉儲資料庫時,我們建議您在複製作業中啟用 PolyBase,將 Azure Blob 儲存作為暫存。

### <a name="migrate-delta-data"></a>移移增量資料 

要標識表中的新行或更新的行,請使用架構中的時間戳列或增量鍵。 然後,您可以將最新值存儲為外部表中的高水位線,然後在下次載入數據時使用它篩選增量數據。 

每個表可以使用不同的水印列來標識其新行或更新行。 我們建議您創建外部控制表。 在表中,每行表示 Netezza 伺服器上的一個表,其特定的水印列名稱和高水印值。 

### <a name="configure-a-self-hosted-integration-runtime"></a>設定自託管整合時

如果要將數據從 Netezza 伺服器遷移到 Azure,無論伺服器是在公司防火牆後面的本地還是虛擬網路環境中,都需要在 Windows 電腦或 VM 上安裝自承載的 IR,而 VM 是用於行動資料的引擎。 安裝自託管 IR 時,我們建議採用以下方法:

- 對於每個 Windows 電腦或 VM,從 32 vCPU 和 128 GB 記憶體的配置開始。 在資料遷移期間,您可以繼續監視 IR 電腦的 CPU 和記憶體使用方式,以查看是否需要進一步擴展電腦以獲得更好的性能,還是縮小機器規模以節省成本。

- 還可以通過將最多四個節點與單個自承載的 IR 關聯來橫向擴展。 針對自託管 IR 運行的單個複製作業會自動應用所有 VM 節點並行複製數據。 為獲得高可用性,請從四個 VM 節點開始,以避免數據遷移期間出現單點故障。

### <a name="limit-your-partitions"></a>限制分割區

最佳做法是使用具有代表性的範例資料集進行性能概念驗證 (POC),以便確定每個複製活動的適當分區大小。 我們建議您在兩小時內將每個分區載入到 Azure。  

要複製表,請從單個自承載的 IR 計算機的單個複製活動開始。 根據表中的數據`parallelCopies`切片分區數逐步增加設置。 根據複製作業產生的輸送量,查看是否可以在兩小時內將整個表載入到 Azure。 

如果在兩小時內無法載入 Azure,並且未完全使用自承載的 IR 節點和資料儲存的容量,則逐漸增加併發複製活動的數量,直到達到網路限制或數據儲存的頻寬限制。 

繼續監視自承載的 IR 電腦上的 CPU 和記憶體使用方式,並在看到 CPU 和記憶體已完全使用時準備好將電腦放大或擴展到多台電腦。 

遇到 Azure 資料工廠複製活動報告的限制錯誤時,請減少 Azure 資料`parallelCopies`工廠中的併發 或設置,或者考慮增加網路和資料儲存的頻寬或每秒 I/O 操作 (IOPS) 限制。 


### <a name="estimate-your-pricing"></a>估計您的定價 

請考慮以下導管,該導管建構用於將資料從本地 Netezza 伺服器移至 Azure SQL 資料倉儲資料庫:

![定價管道](media/data-migration-guidance-netezza-azure-sqldw/pricing-pipeline.png)

假設以下語句為 true: 

- 數據總體積為 50 TB。 

- 我們使用第一解決方案體系結構(Netezza 伺服器位於本地,防火牆後面)遷移數據。

- 50 TB 卷分為 500 個分區,每個複製活動移動一個分區。

- 每個複製活動都配置了一個針對四台計算機的自託管 IR,並實現了每秒 20 兆位元組 (MBps) 的輸送量。 (在複製活動中,`parallelCopies`設置為 4,並且從表中載入資料的每個線程都實現了 5-MBps 輸送量。

- ForEach 併發設置為 3,聚合輸送量為 60 MBps。

- 總共需要 243 小時才能完成遷移。

根據上述假設,以下是估計價格: 

![定價表](media/data-migration-guidance-netezza-azure-sqldw/pricing-table.png)

> [!NOTE]
> 上表所示的定價是假設的。 您的實際定價取決於環境中的實際輸送量。 不包括 Windows 計算機的價格(安裝了自承載的 IR)。 

### <a name="additional-references"></a>其他參考

有關詳細資訊,請參閱以下文章和指南:

- [使用 Azure 資料工廠將資料從本地關係資料倉儲資料庫移到 Azure](https://azure.microsoft.com/resources/data-migration-from-on-premise-relational-data-warehouse-to-azure-data-lake-using-azure-data-factory/)
- [內內紮連接器](https://docs.microsoft.com/azure/data-factory/connector-netezza)
- [ODBC 連接器](https://docs.microsoft.com/azure/data-factory/connector-odbc)
- [Azure Blob 儲存連線器](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Azure Data Lake Storage Gen2 連接器](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Azure SQL 資料主目錄連接器](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse)
- [複製活動效能調優指南](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [建立和設定自我裝載整合執行階段](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [自託管整合時 HA 和可擴充性](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [資料移動安全性考量](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [在 Azure Key Vault 中儲存認證](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [從一個表增量複製資料](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-portal)
- [從多個表增量複製資料](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-multiple-tables-portal)
- [Azure 資料工廠定價頁](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>後續步驟

- [使用 Azure 資料工廠從多個容器複製檔案](solution-template-copy-files-multiple-containers.md)
