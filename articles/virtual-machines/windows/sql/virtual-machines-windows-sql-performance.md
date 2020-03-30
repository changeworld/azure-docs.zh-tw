---
title: Azure 中的 SQL Server 效能指導方針 | Microsoft Docs
description: 提供將 Microsoft Azure VM 中 SQL Server 效能最佳化的指導方針。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/18/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 880f1c601cf4132fdec9e5d25b1bf1f2ff175ab7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650532"
---
# <a name="performance-guidelines-for-sql-server-in-azure-virtual-machines"></a>Azure 虛擬機器中的 SQL Server 效能指導方針

## <a name="overview"></a>總覽

本文提供在「Microsoft Azure 虛擬機器」中最佳化 SQL Server 效能的指引。 在 Azure 虛擬機器中執行 SQL Server 時，我們建議您繼續使用相同的資料庫效能微調選項，這些選項適用於內部部署伺服器環境中的 SQL Server。 不過，公用雲端中關聯式資料庫的效能優劣取決於許多因素，例如虛擬機器的大小和資料磁碟的組態。

[在 Azure 入口網站中佈建的 SQL Server 映像](quickstart-sql-vm-create-portal.md)會遵循存一般的儲存體組態最佳做法 (如需關於如何設定儲存體的詳細資訊，請參閱 [SQL Server VM 的儲存體組態](virtual-machines-windows-sql-server-storage-configuration.md))。 佈建之後，請考慮套用本文所討論的其他最佳化作業。 依據您的工作負載做選擇，並透過測試進行確認。

> [!TIP]
> 您通常必須在最佳化成本與最佳化效能之間做出取捨。 本文的主題為如何讓 Azure VM 上的 SQL Server 達到最佳 ** 效能。 如果您的工作負載需求較低，可能就不需要採用下列每一項最佳化條件。 評估以下建議時，請考量您的效能需求、成本和工作負載模式。

## <a name="quick-check-list"></a>快速檢查清單

下列快速檢查清單能協助您讓 Azure 虛擬機器上的 SQL Server 達到最佳效能：

| 區域 | 最佳化 |
| --- | --- |
| [VM 大小](#vm-size-guidance) | - 使用具有 4 個或更多 vCPU 的 VM 大小，如[E4S_v3](../../ev3-esv3-series.md)或更高版本，或[DS12_v2](../../dv2-dsv2-series-memory.md)或更高。<br/><br/> - [Es、Eas、Ds 和 Das 系列](../../sizes-general.md)提供 OLTP 工作負載性能所需的最佳記憶體與 vCPU 比。 <br/><br/> - [M 系列](../../m-series.md)提供任務關鍵型性能所需的最高記憶體與 vCPU 比，是資料倉儲工作負載的理想選擇。 <br/><br/> - 通過遵循[應用程式性能要求檢查表](../premium-storage-performance.md#application-performance-requirements-checklist)，在尖峰時間收集目標工作負載的[IOPS、](../premium-storage-performance.md#iops)[輸送量](../premium-storage-performance.md#throughput)和[延遲](../premium-storage-performance.md#latency)要求，然後選擇可擴展到工作負載性能要求的[VM 大小](../sizes-general.md)。|
| [儲存空間](#storage-guidance) | - 有關使用 TPC-E 和TPC_C基準的 Azure VM 上的 SQL Server 性能的詳細測試，請參閱博客["優化 OLTP 性能](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794)"。 <br/><br/> - 使用[高級 SSD](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794)獲得最佳性價比優勢。 為資料檔案配置[只 Read 唯一緩存](../premium-storage-performance.md#disk-caching)，而日誌檔沒有緩存。 <br/><br/> - 如果工作負載需要小於 1 ms 的存儲延遲，請使用[超磁片](../disks-types.md#ultra-disk)。 <br/><br/> - 在選擇磁片類型之前[，通過監視應用程式](../premium-storage-performance.md#application-performance-requirements-checklist)來收集 SQL Server 資料、日誌和 Temp DB 檔的存儲延遲要求。 如果需要<1ms 存儲延遲，請使用 Ultra 磁片，否則請使用高級 SSD。 如果僅對日誌檔要求低延遲，而不需要資料檔案，則僅在所需的 IOPS 和輸送量級別[預配 Ultra Disk。](../disks-enable-ultra-ssd.md) <br/><br/> -  [建議將高級檔共用](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md)作為 SQL Server 容錯移轉叢集實例的共用存儲。 高級檔共用不支援緩存，並且與高級 SSD 磁片相比，其性能有限。 選擇高級 SSD 管理的磁片，而不是獨立 SQL 實例的高級檔共用;但是利用高級檔共用進行容錯移轉叢集實例共用存儲，以便進行維護並靈活擴展。 <br/><br/> - 標準存儲僅用於開發和測試目的或備份檔案，不應用於生產工作負載。 <br/><br/> - 將[儲存體帳戶](../../../storage/common/storage-create-storage-account.md)和 SQL Server VM 置於同一個區域。<br/><br/> - 禁用存儲帳戶上的 Azure[異地冗余存儲](../../../storage/common/storage-redundancy.md)（異地複製）。  |
| [磁片](#disks-guidance) | - 使用至少 2 個[高級 SSD 磁片](../disks-types.md#premium-ssd)（日誌檔為 1 個磁片，資料檔案使用 1 個）。 <br/><br/> - 對於需要<1 ms IO 延遲的工作負載，為 M 系列啟用寫入加速器，並考慮為 Es 和 DS 系列使用超 SSD 磁片。 <br/><br/> - 在託管資料檔案的磁片上啟用[唯讀緩存](../premium-storage-performance.md#disk-caching)。<br/><br/> -[在為 SQL Server 資料、日誌和 TempDB 檔配置存儲時，](virtual-machines-windows-sql-server-storage-configuration.md)添加比工作負載所需的額外 20% 的高級 IOPS/輸送量容量 <br/><br/> - 避免使用作業系統或暫存磁碟作為資料儲存體或進行記錄。<br/><br/> - 不要啟用裝載記錄檔案的磁碟上的 [快取] 功能。  **重要提示**：更改 Azure VM 磁片的緩存設置時停止 SQL Server 服務。<br/><br/> - 分條多個 Azure 資料磁片，以獲得更高的存儲輸送量。<br/><br/> - 以文件上記載的配置大小格式化。 <br/><br/> - 將 TempDB 放在本地`D:\`SSD 磁碟機上，用於任務關鍵型 SQL Server 工作負載（在選擇正確的 VM 大小後）。 如果從 Azure 門戶或 Azure 快速啟動範本創建 VM，並將[Temp DB 放在本地磁片上](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583)，則不需要任何進一步操作;因此，無需執行任何操作。對於所有其他情況，請按照博客中[的步驟使用 SSD 存儲 TempDB](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/)以防止重新開機後失敗。 如果本地磁碟機的容量不足以達到臨時資料庫的大小，則將 Temp DB 放在具有[唯讀緩存](../premium-storage-performance.md#disk-caching)的優質 SSD 磁片上[剝離](../premium-storage-performance.md)的存儲池上。 |
| [I/O](#io-guidance) |- 啟用 [資料庫頁面壓縮] 功能　。<br/><br/> - 針對資料檔案，啟用 [立即檔案初始化] 功能。<br/><br/> - 限制資料庫的 [自動成長] 功能。<br/><br/> - 停用資料庫的 [自動壓縮] 功能。<br/><br/> - 將所有的資料庫 (包括系統資料庫) 移到資料磁碟。<br/><br/> - 將 SQL Server 的錯誤記錄檔和追蹤檔案目錄移至資料磁碟。<br/><br/> - 配置預設備份和資料庫檔案位置。<br/><br/> - [在記憶體中啟用鎖定的頁面](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows?view=sql-server-2017)。<br/><br/> - 套用 SQL Server 效能修正程式。 |
| [特定功能](#feature-specific-guidance) | - 直接備份至 Blob 儲存體。<br/><br/>- 對大於 12 TB 的資料庫使用[檔快照備份](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure)。 <br/><br/>- 使用多個臨時資料庫檔，每個核心1個檔，最多8個檔。<br/><br/>- 將作業系統的最大伺服器記憶體設置為 90% 或最多 50 GB。 <br/><br/>- 啟用軟 NUMA。 |

如需有關「如何」** 和「為何」** 進行這些最佳化的詳細資訊，請檢閱下列各節提供的詳細資料與指導方針。

## <a name="vm-size-guidance"></a>VM 大小指引

首先收集工作負載在高峰時間對工作負載的 cpu、記憶體和存儲輸送量要求。 [邏輯磁片]磁片讀取/秒和[邏輯磁片]磁片寫入/Sec效能計數器可用於收集讀寫 IOPS 要求，並且 [邏輯磁片]磁片位元組/秒計數器可用於收集資料、日誌和臨時資料庫檔的[存儲輸送量要求](../premium-storage-performance.md#disk-caching)。 定義 IOPS 和峰值輸送量要求後，評估 VM 大小提供該容量。 例如，如果工作負載在高峰時需要 20 K 讀取 IOPS 和 10K 寫入 IOPS，則可以選擇具有 2 個 P30 磁片的E16s_v3（最多 32 K 緩存和 25600 個未緩存 IOPS）或M16_s（緩存最多 20 K 和 10K 未緩存 IOPS）。 確保瞭解工作負載的輸送量和 IOPS 要求，因為 VM 對 IOPS 和輸送量具有不同的規模限制。<br/><br/>[DSv_3](../../dv3-dsv3-series.md)和[Es_v3系列](../../ev3-esv3-series.md)由英特爾哈斯韋爾或 Broadwell 處理器託管在通用硬體上。 [M 系列](../../m-series.md)為最大的 SQL Server 工作負載提供最高的 vCPU 計數和記憶體，並託管在具有 Skylake 處理器系列的記憶體優化硬體上。 這些 VM 系列支援高級存儲，建議使用主機級讀取緩存獲得最佳性能。 Es_v3和M系列也提供[受限的核心尺寸](../../windows/constrained-vcpu.md)，這為計算較低且存儲容量要求高的工作負載節省了資金。 

## <a name="storage-guidance"></a>儲存體指引

有關使用 TPC-E 和TPC_C基準的 Azure VM 上的 SQL Server 性能的詳細測試，請參閱博客["優化 OLTP 性能](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794)"。 

對於所有生產工作負荷，建議使用高級 SSD 的 Azure Blob 緩存。 

> [!WARNING]
> 標準 HDD 具有與 SSD 不同的延遲和頻寬，因此僅建議用於開發/測試工作負載。 生產工作負載應該使用進階 SSD。

此外，我們建議您在存放 SQL Server 虛擬機器的同一個資料中心內建立 Azure 儲存體帳戶，以減少傳輸延遲的狀況。 建立儲存體帳戶時，請停用 [異地複寫] 功能，因為跨多個磁碟時無法保證寫入順序一致。 請考慮另一個方法，在兩個 Azure 資料中心之間設定 SQL Server 災害復原技術。 如需詳細資訊，請參閱 [SQL Server 在 Azure 虛擬機器中的高可用性和災害復原](virtual-machines-windows-sql-high-availability-dr.md)。

## <a name="disks-guidance"></a>磁碟指引

Azure VM 上有三種主要的磁碟類型︰

* **OS 磁碟**：建立「Azure 虛擬機器」時，平台會至少將一個磁碟 (標示為 **C** 磁碟機) 連接至 VM 作為您的作業系統磁碟。 此磁碟是以分頁 Blob 的形式儲存於儲存體的 VHD。
* **暫存磁碟**：「Azure 虛擬機器」包含另一個稱為暫存磁碟的磁碟 (標示為 **D**: 磁碟機)。 此磁碟位於可用於塗銷空間的節點上。
* **資料磁碟**：您也可以將其他磁碟連接至虛擬機器作為資料磁碟，這些磁碟將會以分頁 Blob 形式儲存於儲存體中。

下列各節說明使用這些不同磁碟的建議。

### <a name="operating-system-disk"></a>作業系統磁碟

作業系統磁碟是指可開機且裝載為執行中作業系統版本的 VHD，且會標示為 **C** 磁碟機。

作業系統磁碟上的預設快取原則為 [讀取/寫入]****。 對於需要高效能的應用程式，我們建議您使用資料磁碟取代作業系統磁碟。 請參閱下面的＜資料磁碟＞一節。

### <a name="temporary-disk"></a>暫存磁碟

標記為**D**磁碟機的臨時存儲磁碟機不會持久化到 Azure Blob 存儲。 請勿將使用者資料庫檔案或使用者交易記錄檔儲存在 **D**: 磁碟機。

將 TempDB 放在本地 SSD`D:\`磁碟機上，用於任務關鍵型 SQL Server 工作負載（在選擇正確的 VM 大小後）。 如果從 Azure 門戶或 Azure 快速啟動範本創建 VM，並將[Temp DB 放在本地磁片上](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583)，則不需要任何進一步操作;因此，不需要任何進一步操作。對於所有其他情況，請按照博客中[的步驟使用 SSD 存儲 TempDB](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/)以防止重新開機後失敗。 如果本地磁碟機的容量不足以達到臨時資料庫的大小，則將 Temp DB 放在具有[唯讀緩存](../premium-storage-performance.md#disk-caching)的優質 SSD 磁片上[剝離](../premium-storage-performance.md)的存儲池上。

對於支援高級 SSD 的 VM，您還可以將 TempDB 存儲在支援啟用讀取緩存的優質 SSD 的磁片上。


### <a name="data-disks"></a>資料磁碟

* **對資料和日誌檔使用高級 SSD 磁片**：如果您不使用磁片條帶化，請使用兩個高級 SSD 磁片，其中一個磁片包含日誌檔，另一個包含資料。 每個高級 SSD 都提供許多 IOPS 和頻寬 （MB/s），具體取決於其大小，如本文中所述，[選擇磁片類型](../disks-types.md)。 如果您使用磁碟等量化技術 (例如儲存空間)，您將會有兩個集區 (一個用於記錄檔，另一個用於資料檔案)，而達到最佳效能。 但是，如果您計畫使用 SQL Server 容錯移轉叢集實例 （FCI），則必須配置一個池，或者改用[高級檔共用](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md)。

   > [!TIP]
   > - 如需不同磁碟和工作負載組態的測試結果，請參閱下列部落格文章：[Azure VM 上的 SQL Server 適用的儲存體組態指導方針](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/)。
   > - 需要 ~ 50,000 IOPS 的 SQL Server 若要達到任務關鍵性效能，請考慮以 Ultra SSD 取代 10 個 -P30 磁碟。 有關詳細資訊，請參閱以下博客文章：[使用超 SSD 的任務關鍵性能](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/)。

   > [!NOTE]
   > 當您在入口網站中佈建 SQL Server VM 時，您可以選擇編輯儲存體組態。 根據您的設定，Azure 會設定一或多個磁碟。 多個磁碟會利用串接合併成單一儲存體集區。 資料和記錄檔皆在此設定中。 如需詳細資訊，請參閱 [SQL Server VM 的儲存體組態](virtual-machines-windows-sql-server-storage-configuration.md)。

* **磁片條帶**：為了更高的輸送量，您可以添加其他資料磁片並使用磁片條帶。 為了判斷資料磁碟的數目，您需要分析記錄檔以及資料和 TempDB 檔案所需的 IOPS 和頻寬數目。 請注意，不同的 VM 大小在支援的 IOPS 和頻寬數目上有不同的限制，請參閱每個 [VM 大小](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)的 IOPS 相關表格。 請使用下列指引：

  * 若為 Windows 8/Windows Server 2012 以上版本，請遵循下列指導方針使用[儲存空間](https://technet.microsoft.com/library/hh831739.aspx)：

      1. 將 OLTP 工作負載的交錯（條帶大小）設置為 64 KB（65，536 位元組），將資料倉儲工作負載的插回（65，536 位元組）設置為 256 KB（262，144 位元組），以避免分區不對齊導致性能影響。 必須使用 PowerShell 來設定。
      2. 設定資料行數目 = 實體磁碟數量。 設定 8 個以上的磁碟 (不是伺服器管理員 UI) 時，使用 PowerShell。 

    例如，下列 PowerShell 會建立新的儲存體集區，其間隔大小為 64 KB，且資料行數目為 2︰

    ```powershell
    $PoolCount = Get-PhysicalDisk -CanPool $True
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * 對於 Windows 2008 R2 之前的版本，可以使用動態磁碟 (OS 分割的磁碟區)，且等量磁碟區的大小一律為 64 KB。 此選項從 Windows 8/Windows 伺服器 2012 開始棄用。 如需相關資訊，請參閱 [虛擬磁碟服務正轉換為 Windows 存放管理 API](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx)中的支援聲明。

  * 如果您要搭配使用[儲存空間直接存取 (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) 與 [SQL Server 容錯移轉叢集執行個體](virtual-machines-windows-portal-sql-create-failover-cluster.md)，您必須設定單一集區。 儘管在該單個池上可以創建不同的卷，但它們都將具有相同的特徵，例如相同的緩存策略。

  * 請根據您預期的負載量，決定與您的儲存體集區相關聯的磁碟數量。 請注意，各 VM 大小所允許連接的資料磁碟數量皆不同。 有關詳細資訊，請參閱[虛擬機器的大小](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

  * 如果您不是使用進階 SSD (開發/測試案例)，建議您新增您 [VM 大小](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)所支援的最大數目資料磁碟，並使用磁碟等量分割。

* **快取原則**：請注意，下列快取原則皆是根據您的儲存體組態來建議。

  * 如果您針對資料和記錄檔使用不同磁碟，請在裝載資料檔和 TempDB 資料檔案的資料磁碟上啟用讀取快取。 這會帶來明顯的效能優勢。 請勿在裝載記錄檔的磁碟上啟用快取，這樣會導致效能小幅降低。

  * 如果您要在單一儲存體集區中使用磁碟等量化，大部分的工作負載將會從讀取快取中獲益。 如果您為記錄檔和資料檔案使用個別的儲存體集區，請僅在資料檔的儲存體集區上啟用讀取快取。 針對某些大量寫入的工作負載，沒有快取可能會達到更好的效能。 這只能透過測試來判斷。

  * 上述建議適用於進階 SSD。 如果您並非使用進階 SSD，請勿在任何資料磁碟上啟用任何快取功能。

  * 如需有關設定磁碟快取功能的指示，請參閱下列文章。 對於傳統 (ASM) 部署模型，請參閱：[Set-azureosdisk](https://msdn.microsoft.com/library/azure/jj152847) 和 [Set-azuredatadisk](https://msdn.microsoft.com/library/azure/jj152851.aspx)。 有關 Azure 資源管理器部署模型，請參閱：[設置-AzOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk)和[集-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdatadisk)。

     > [!WARNING]
     > 變更 Azure VM 磁碟的快取設定時，請停止 SQL Server 服務，以避免任何發生資料庫損毀的可能性。

* **NTFS 分配單位大小**：格式化資料磁片時，建議對資料和日誌檔以及 TempDB 使用 64 KB 的分配單位大小。 如果 TempDB 放置在臨時磁片上 （D：*磁碟機） 利用此磁碟機獲得的性能超過了對 64K 分配單位尺寸的需求。 

* **磁碟管理最佳做法**︰移除資料磁碟或變更其快取類型時，請於變更期間停止 SQL Server 服務。 當 OS 磁碟上的快取設定發生變更時，Azure 會停止 VM、變更快取類型，然後重新啟動 VM。 當資料磁碟的快取設定發生變更時，系統不會停止 VM，但在變更期間會從 VM 卸離資料磁碟，然後再重新連接。

  > [!WARNING]
  > 如果在這些作業期間無法停止 SQL Server 服務，可能就會造成資料庫損毀。


## <a name="io-guidance"></a>I/O 指引

* 進階 SSD 在您以平行方式處理應用程式和要求時可以達到最佳效能。 進階 SSD 是專為 IO 佇列深度大於 1 的案例所設計，所以您會發現單一執行緒的序列要求 (即使其為儲存密集型) 的效能只有微幅提升，或沒有提升。 舉例來說，這可能會影響效能分析工具 (如 SQLIO) 的單一執行緒測試結果。

* 請考慮使用 [資料庫頁面壓縮](https://msdn.microsoft.com/library/cc280449.aspx) 功能，其有助於改善 I/O 密集型工作負載的效能。 不過，資料壓縮可能會增加資料庫伺服器的 CPU 使用量。

* 請考慮啟用 [立即檔案初始化] 功能，以減少配置初始檔案所需的時間。 為了利用立即檔案初始化功能，您必須授與 SQL Server (MSSQLSERVER) 服務帳戶的 SE_MANAGE_VOLUME_NAME，並將其新增至**執行磁碟區維護工作**安全性原則。 如果使用 Azure 的 SQL Server 平台映像，則不會將預設服務帳戶 (NT Service\MSSQLSERVER) 新增至**執行磁碟區維護工作**安全性原則。 也就是說，SQL Server Azure 平台映像未啟用 [立即檔案初始化] 功能。 將 SQL Server 服務帳戶新增至**執行磁碟區維護工作**安全性原則之後，請重新啟動 SQL Server 服務。 使用此功能時，可能有安全性考量。 如需詳細資訊，請參閱 [資料庫檔案初始化](https://msdn.microsoft.com/library/ms175935.aspx)。

* **** 只是發生非預期成長的應變方案。 請勿每天使用「自動成長」功能，管理資料和記錄成長。 若已使用「自動成長」功能，請透過 大小參數預先放大檔案。

* 請確定已停用「自動壓縮」 **** ，以避免不必要的額外負荷，而對效能造成負面影響。

* 將所有的資料庫 (包括系統資料庫) 移到資料磁碟。 如需詳細資訊，請參閱 [移動系統資料庫](https://msdn.microsoft.com/library/ms345408.aspx)。

* 將 SQL Server 的錯誤記錄檔和追蹤檔案目錄移至資料磁碟。 在 SQL Server 組態管理員中，以滑鼠右鍵按一下您的 SQL Server 執行個體並選取內容，即可完成。 可以在 **"啟動參數**"選項卡中更改錯誤日誌和追蹤檔案設置。轉儲目錄在 **"高級"** 選項卡中指定。以下螢幕截圖顯示了查找錯誤日誌啟動參數的位置。

    ![SQL ErrorLog 螢幕擷取畫面](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

* 設定預設備份和資料庫檔案位置。 請使用本文中的建議，並在 [伺服器屬性] 視窗中進行變更。 如需指示，請參閱 [檢視或變更資料及記錄檔的預設位置 (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx)。 下列螢幕擷取畫面示範進行這些變更的位置。

    ![SQL 資料記錄和備份檔案](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)
* 起用鎖定的頁面，以減少 IO 和任何分頁活動。 如需詳細資訊，請參閱 [啟用鎖定記憶體分頁選項 (Windows)](https://msdn.microsoft.com/library/ms190730.aspx)。

* 如果您執行的是 SQL Server 2012，請安裝 Service Pack 1 累計更新 10。 此更新包含一個修正程式，可修正在 SQL Server 2012 中執行 select into 暫存資料表陳述式時，I/O 效能不佳的狀況。 如需相關資訊，請參閱此 [知識庫文章](https://support.microsoft.com/kb/2958012)。

* 將資料檔案傳輸至 Azure，或從 Azure 往外傳輸時，請考慮先壓縮所有資料檔案。

## <a name="feature-specific-guidance"></a>特定功能的指引

有些部署作業可能會使用更進階的組態技術，提供額外的效能優點。 下列清單特別強調了一些可協助您達到更佳效能的 SQL Server 功能：

### <a name="back-up-to-azure-storage"></a>備份到 Azure 存儲
對 Azure 虛擬機器中所執行的 SQL Server 執行備份時，可以使用 [SQL Server 備份至 URL](https://msdn.microsoft.com/library/dn435916.aspx)。 此功能從 SQL Server 2012 SP1 CU2 開始提供，建議在備份至連接的資料磁碟時使用。 在備份至 Azure 儲存體 (或從中還原) 時，請依照 [SQL Server 備份至 URL 的最佳作法和疑難排解，以及從儲存在 Azure 儲存體中的備份還原](https://msdn.microsoft.com/library/jj919149.aspx)中提供的建議執行。 您也可以使用 [Azure 虛擬機器中的 SQL Server 自動備份](virtual-machines-windows-sql-automated-backup.md)，自動執行這些備份作業。

對於 SQL Server 2012 之前的版本，您可以使用 [將 SQL Server 備份至 Azure 工具](https://www.microsoft.com/download/details.aspx?id=40740)。 這項工具可以透過多個備份等量磁碟區目標協助您提高備份的輸送量。

### <a name="sql-server-data-files-in-azure"></a>Azure 中的 SQL 伺服器資料檔案

從 SQL Server 2014 開始提供 [Azure 中的 SQL Server 資料檔案](https://msdn.microsoft.com/library/dn385720.aspx)這項新功能。 在 Azure 中執行具有資料檔案的 SQL Server 的效能與使用 Azure 資料磁碟的效能特性相當。

### <a name="failover-cluster-instance-and-storage-spaces"></a>容錯移轉叢集實例和存儲空間

如果使用存儲空間，請在 **"確認"** 頁上向群集添加節點時，請清除標記為"**將所有符合條件的存儲添加到群集"核取方塊**。 

![取消選中符合條件的存儲](media/virtual-machines-windows-sql-performance/uncheck-eligible-cluster-storage.png)

如果您使用「儲存空間」但未取消選取 [新增適合的儲存裝置到叢集]****，Windows 就會在進行叢集程序時將虛擬機器中斷連結。 因此，它們將不會顯示在「磁碟管理員」或「總管」中，直到您使用 PowerShell 將儲存空間從叢集中移除後再重新連接為止。 儲存空間可將多個磁碟分組為存放集區。 如需詳細資訊，請參閱[儲存空間](/windows-server/storage/storage-spaces/overview)。

## <a name="next-steps"></a>後續步驟

如需儲存體和效能的詳細資訊，請參閱 [Azure VM 上的 SQL Server 適用的儲存體組態指導方針](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/)

如需安全性的最佳作法，請參閱 [Azure 虛擬機器中的 SQL Server 安全性考量](virtual-machines-windows-sql-security.md)。

請檢閱 [Azure 虛擬機器上的 SQL Server 概觀](virtual-machines-windows-sql-server-iaas-overview.md)中其他「SQL Server 虛擬機器」的相關文章。 如果您有 SQL Server 虛擬機器的相關問題，請參閱[常見問題集](virtual-machines-windows-sql-server-iaas-faq.md)。
