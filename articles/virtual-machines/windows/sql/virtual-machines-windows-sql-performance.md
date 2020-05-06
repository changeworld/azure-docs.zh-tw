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
ms.openlocfilehash: 6ff6051f302c624438be8ef88416df2e4d429d71
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82559698"
---
# <a name="performance-guidelines-for-sql-server-in-azure-virtual-machines"></a>Azure 虛擬機器中的 SQL Server 效能指導方針

## <a name="overview"></a>概觀

本文提供在「Microsoft Azure 虛擬機器」中最佳化 SQL Server 效能的指引。 在 Azure 虛擬機器中執行 SQL Server 時，我們建議您繼續使用相同的資料庫效能微調選項，這些選項適用於內部部署伺服器環境中的 SQL Server。 不過，公用雲端中關聯式資料庫的效能優劣取決於許多因素，例如虛擬機器的大小和資料磁碟的組態。

[在 Azure 入口網站中佈建的 SQL Server 映像](quickstart-sql-vm-create-portal.md)會遵循存一般的儲存體組態最佳做法 (如需關於如何設定儲存體的詳細資訊，請參閱 [SQL Server VM 的儲存體組態](virtual-machines-windows-sql-server-storage-configuration.md))。 佈建之後，請考慮套用本文所討論的其他最佳化作業。 依據您的工作負載做選擇，並透過測試進行確認。

> [!TIP]
> 您通常必須在最佳化成本與最佳化效能之間做出取捨。 本文的主題為如何讓 Azure VM 上的 SQL Server 達到最佳 ** 效能。 如果您的工作負載需求較低，可能就不需要採用下列每一項最佳化條件。 評估以下建議時，請考量您的效能需求、成本和工作負載模式。

## <a name="quick-check-list"></a>快速檢查清單

下列快速檢查清單能協助您讓 Azure 虛擬機器上的 SQL Server 達到最佳效能：

| 區域 | 最佳化 |
| --- | --- |
| [VM 大小](#vm-size-guidance) | -使用具有4個或更多 vCPU （例如[E4S_v3](../../ev3-esv3-series.md)或更高版本）或[DS12_v2](../../dv2-dsv2-series-memory.md)或更高版本的 VM 大小。<br/><br/> - [Es、Eas、Ds 和 Das 系列](../../sizes-general.md)為 OLTP 工作負載效能所需的 vCPU 比率提供最佳的記憶體。 <br/><br/> - [M 系列](../../m-series.md)提供最高的記憶體，以 vCPU 任務關鍵性效能所需的比率，適用于資料倉儲工作負載。 <br/><br/> -遵循[應用程式效能需求檢查清單](../premium-storage-performance.md#application-performance-requirements-checklist)，然後選取可根據工作負載的效能需求調整的[VM 大小](../sizes-general.md)，以在尖峰時間收集目標工作負載的[IOPS](../premium-storage-performance.md#iops)、[輸送量](../premium-storage-performance.md#throughput)和[延遲](../premium-storage-performance.md#latency)需求。|
| [儲存體](#storage-guidance) | -如需在 Azure Vm 上使用 TPC-E 和 TPC_C 基準測試 SQL Server 效能的詳細測試，請參閱將[OLTP 效能優化](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794)的 blog。 <br/><br/> -使用[Premium ssd](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794)可獲得最佳的價格/效能優勢。 設定資料檔案的[唯讀](../premium-storage-performance.md#disk-caching)快取，以及記錄檔的快取。 <br/><br/> -如果工作負載需要少於1毫秒的儲存延遲，請使用[Ultra 磁片](../disks-types.md#ultra-disk)。 <br/><br/> -在選擇磁片類型之前，請先[監視應用程式](../premium-storage-performance.md#application-performance-requirements-checklist)，以收集 SQL Server 資料、記錄檔和暫存資料庫檔案的儲存延遲需求。 如果需要 <1 毫秒儲存體延遲，請使用 Ultra 磁片，否則請使用 premium SSD。 如果只有記錄檔才需要低延遲，而不是針對資料檔案，則請只針對記錄檔，在所需的 IOPS 和輸送量層級布建[Ultra 磁片](../disks-enable-ultra-ssd.md)。 <br/><br/> -  建議將[Premium 檔案共用](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md)作為 SQL Server 容錯移轉叢集實例的共用存放裝置。 相較于高階 SSD 磁片，Premium 檔案共用不支援快取，且提供的效能有限。 針對獨立 SQL 實例，選擇 premium 檔案共用上的高階 SSD 受控磁片;但請利用高階檔案共用來進行容錯移轉叢集實例共用存放裝置，以方便維護和彈性地擴充性。 <br/><br/> -標準儲存體僅建議用於開發和測試用途，或用於備份檔案，不應用於生產工作負載。 <br/><br/> - 將[儲存體帳戶](../../../storage/common/storage-create-storage-account.md)和 SQL Server VM 置於同一個區域。<br/><br/> -停用儲存體帳戶上的 Azure[異地多餘儲存體](../../../storage/common/storage-redundancy.md)（異地複寫）。  |
| [磁碟](#disks-guidance) | -最少使用2個[PREMIUM SSD 磁片](../disks-types.md#premium-ssd)（1個用於記錄檔，1個用於資料檔案）。 <br/><br/> -對於需要 <1 毫秒 IO 延遲的工作負載，請為 M 系列啟用寫入加速器，並考慮使用 Ultra SSD 磁片做為 Es 和 DS 系列。 <br/><br/> -在裝載資料檔案的磁片上啟用[唯讀](../premium-storage-performance.md#disk-caching)快取。<br/><br/> -新增額外的 20% premium IOPS/輸送量容量，而不是您的工作負載在設定[SQL Server 資料、記錄檔和 TempDB 檔案的儲存體](virtual-machines-windows-sql-server-storage-configuration.md)時所需 <br/><br/> - 避免使用作業系統或暫存磁碟作為資料儲存體或進行記錄。<br/><br/> - 不要啟用裝載記錄檔案的磁碟上的 [快取] 功能。  **重要**事項：變更 Azure VM 磁片的快取設定時，請停止 SQL Server 服務。<br/><br/> -分割多個 Azure 資料磁片以增加儲存體輸送量。<br/><br/> - 以文件上記載的配置大小格式化。 <br/><br/> -將 TempDB 放在本機 SSD `D:\`磁片磁碟機上，以用於任務關鍵性 SQL Server 工作負載（在選擇正確的 VM 大小之後）。 如果您從 Azure 入口網站或 Azure 快速入門範本建立 VM，並[將暫存資料庫放在本機磁片上](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583)，則不需要任何進一步的動作;針對所有其他情況，請遵循 blog 中的步驟來[使用 ssd 來儲存 TempDB](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) ，以避免在重新開機之後發生失敗。 如果本機磁片磁碟機的容量不足以供您的暫存資料庫大小使用，請將暫存資料庫放在具有[唯讀](../premium-storage-performance.md#disk-caching)快取的高階 SSD 磁片上的存放集[區](../premium-storage-performance.md)上。 |
| [I/O](#io-guidance) |- 啟用 [資料庫頁面壓縮] 功能　。<br/><br/> - 針對資料檔案，啟用 [立即檔案初始化] 功能。<br/><br/> - 限制資料庫的 [自動成長] 功能。<br/><br/> - 停用資料庫的 [自動壓縮] 功能。<br/><br/> - 將所有的資料庫 (包括系統資料庫) 移到資料磁碟。<br/><br/> - 將 SQL Server 的錯誤記錄檔和追蹤檔案目錄移至資料磁碟。<br/><br/> -設定預設備份和資料庫檔案位置。<br/><br/> - [在記憶體中啟用鎖定的頁面](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows?view=sql-server-2017)。<br/><br/> - 套用 SQL Server 效能修正程式。 |
| [特定功能](#feature-specific-guidance) | - 直接備份至 Blob 儲存體。<br/><br/>-針對大於 12 TB 的資料庫使用檔案[快照集備份](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure)。 <br/><br/>-使用多個暫存資料庫檔案，每個核心1個檔案，最多8個檔案。<br/><br/>-將 [最大伺服器記憶體] 設定為90% 或保留最多 50 GB 的作業系統。 <br/><br/>-啟用軟體 NUMA。 |

如需有關「如何」** 和「為何」** 進行這些最佳化的詳細資訊，請檢閱下列各節提供的詳細資料與指導方針。

## <a name="vm-size-guidance"></a>VM 大小指引

從收集工作負載在尖峰時間的 cpu、記憶體和儲存體輸送量需求開始。 \LogicalDisk\Disk Reads/Sec 和 \LogicalDisk\Disk Writes/Sec 效能計數器可用來收集讀取和寫入 IOPS 需求，而 \LogicalDisk\Disk Bytes/Sec 計數器可用來收集資料、記錄檔和暫存資料庫檔案的[儲存體輸送量需求](../premium-storage-performance.md#disk-caching)。 定義尖峰的 IOPS 和輸送量需求之後，評估 VM 大小會提供該容量。 例如，如果您的工作負載需要尖峰的 20 K 讀取 IOPS 和10K 寫入 IOPS，您可以選擇 E16s_v3 （最多 32 K 快取和25600未快取 IOPS）或 M16_s （最多 20 K 快取和10K 未快取 IOPS）與2個 P30 磁片。 請務必瞭解工作負載的輸送量和 IOPS 需求，因為 Vm 對於 IOPS 和輸送量具有不同的調整限制。<br/><br/>[DSv_3](../../dv3-dsv3-series.md)和[Es_v3 系列](../../ev3-esv3-series.md)是以 Intel Haswell 或 Broadwell 處理器的一般用途硬體主控。 [M 系列](../../m-series.md)針對最大的 SQL Server 工作負載提供最高的 vCPU 計數和記憶體，並裝載在具有 Skylake 處理器系列的記憶體優化硬體上。 這些 VM 系列支援高階儲存體，建議使用主機層級的讀取快取來達到最佳效能。 Es_v3 和 M 系列也適用于[受限核心大小](../../windows/constrained-vcpu.md)，這可節省計算和高儲存體容量需求較低的工作負載成本。 

## <a name="storage-guidance"></a>儲存體指引

如需在 Azure Vm 上使用 TPC-E 和 TPC_C 基準測試 SQL Server 效能的詳細測試，請參閱將[OLTP 效能優化](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794)的 blog。 

針對所有生產環境工作負載，建議使用 premium Ssd 的 Azure blob 快取。 

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

標示為**D**磁片磁碟機的暫存磁片磁碟機不會保存至 Azure blob 儲存體。 請勿將使用者資料庫檔案或使用者交易記錄檔儲存在 **D**: 磁碟機。

將 TempDB 放在本機 SSD `D:\`磁片磁碟機上，以用於任務關鍵性 SQL Server 工作負載（在選擇正確的 VM 大小之後）。 如果您從 Azure 入口網站或 Azure 快速入門範本建立 VM，並[將暫存資料庫放在本機磁片上](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583)，則不需要任何進一步的動作;針對所有其他情況，請遵循 blog 中的步驟來[使用 ssd 來儲存 TempDB](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) ，以避免在重新開機之後發生失敗。 如果本機磁片磁碟機的容量不足以供您的暫存資料庫大小使用，請將暫存資料庫放在具有[唯讀](../premium-storage-performance.md#disk-caching)快取的高階 SSD 磁片上的存放集[區](../premium-storage-performance.md)上。

針對支援高階 Ssd 的 Vm，您也可以將 TempDB 儲存在支援 premium Ssd 並啟用讀取快取的磁片上。


### <a name="data-disks"></a>資料磁碟

* **針對資料和記錄檔使用 PREMIUM SSD 磁片**：如果您未使用磁片等量分割，請使用兩個 premium ssd 磁片，其中一個磁片包含記錄檔，另一個則包含資料。 每個 premium SSD 都會根據其大小提供數個 IOPS 和頻寬（MB/s），如[選取磁片類型一](../disks-types.md)文所述。 如果您使用磁碟等量化技術 (例如儲存空間)，您將會有兩個集區 (一個用於記錄檔，另一個用於資料檔案)，而達到最佳效能。 不過，如果您打算使用 SQL Server 容錯移轉叢集實例（FCI），則必須設定一個集區，或改為使用[premium 檔案共用](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md)。

   > [!TIP]
   > - 如需不同磁碟和工作負載組態的測試結果，請參閱下列部落格文章：[Azure VM 上的 SQL Server 適用的儲存體組態指導方針](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/)。
   > - 需要 ~ 50,000 IOPS 的 SQL Server 若要達到任務關鍵性效能，請考慮以 Ultra SSD 取代 10 個 -P30 磁碟。 如需詳細資訊，請參閱下列 blog 文章： [ULTRA SSD 的任務關鍵性效能](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/)。

   > [!NOTE]
   > 當您在入口網站中佈建 SQL Server VM 時，您可以選擇編輯儲存體組態。 根據您的設定，Azure 會設定一或多個磁碟。 多個磁碟會利用串接合併成單一儲存體集區。 資料和記錄檔皆在此設定中。 如需詳細資訊，請參閱 [SQL Server VM 的儲存體組態](virtual-machines-windows-sql-server-storage-configuration.md)。

* **磁片**等量分割：如需更多的輸送量，您可以新增其他資料磁片，並使用磁片等量分割。 為了判斷資料磁碟的數目，您需要分析記錄檔以及資料和 TempDB 檔案所需的 IOPS 和頻寬數目。 請注意，不同的 VM 大小在支援的 IOPS 和頻寬數目上有不同的限制，請參閱每個 [VM 大小](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)的 IOPS 相關表格。 請使用下列指引：

  * 若為 Windows 8/Windows Server 2012 以上版本，請遵循下列指導方針使用[儲存空間](https://technet.microsoft.com/library/hh831739.aspx)：

      1. 將 OLTP 工作負載的交錯（等量大小）設為 64 KB （65536個位元組），並針對資料倉儲工作負載設定 256 KB （262144位元組），以避免因為分割區對齊而造成效能影響。 必須使用 PowerShell 來設定。
      2. 設定資料行數目 = 實體磁碟數量。 設定 8 個以上的磁碟 (不是伺服器管理員 UI) 時，使用 PowerShell。 

    例如，下列 PowerShell 會建立新的儲存體集區，其間隔大小為 64 KB，且資料行數目為 2︰

    ```powershell
    $PoolCount = Get-PhysicalDisk -CanPool $True
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * 對於 Windows 2008 R2 之前的版本，可以使用動態磁碟 (OS 分割的磁碟區)，且等量磁碟區的大小一律為 64 KB。 從 Windows 8/Windows Server 2012，此選項已被取代。 如需相關資訊，請參閱 [虛擬磁碟服務正轉換為 Windows 存放管理 API](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx)中的支援聲明。

  * 如果您要搭配使用[儲存空間直接存取 (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) 與 [SQL Server 容錯移轉叢集執行個體](virtual-machines-windows-portal-sql-create-failover-cluster.md)，您必須設定單一集區。 雖然可以在該單一集區上建立不同的磁片區，但它們全都會共用相同的特性，例如相同的快取原則。

  * 請根據您預期的負載量，決定與您的儲存體集區相關聯的磁碟數量。 請注意，各 VM 大小所允許連接的資料磁碟數量皆不同。 如需詳細資訊，請參閱[虛擬機器的大小](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

  * 如果您不是使用進階 SSD (開發/測試案例)，建議您新增您 [VM 大小](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)所支援的最大數目資料磁碟，並使用磁碟等量分割。

* **快取原則**：請注意，下列快取原則皆是根據您的儲存體組態來建議。

  * 如果您針對資料和記錄檔使用不同磁碟，請在裝載資料檔和 TempDB 資料檔案的資料磁碟上啟用讀取快取。 這會帶來明顯的效能優勢。 請勿在裝載記錄檔的磁碟上啟用快取，這樣會導致效能小幅降低。

  * 如果您要在單一儲存體集區中使用磁碟等量化，大部分的工作負載將會從讀取快取中獲益。 如果您為記錄檔和資料檔案使用個別的儲存體集區，請僅在資料檔的儲存體集區上啟用讀取快取。 針對某些大量寫入的工作負載，沒有快取可能會達到更好的效能。 這只能透過測試來判斷。

  * 上述建議適用於進階 SSD。 如果您並非使用進階 SSD，請勿在任何資料磁碟上啟用任何快取功能。

  * 如需有關設定磁碟快取功能的指示，請參閱下列文章。 對於傳統 (ASM) 部署模型，請參閱：[Set-azureosdisk](https://msdn.microsoft.com/library/azure/jj152847) 和 [Set-azuredatadisk](https://msdn.microsoft.com/library/azure/jj152851.aspx)。 如需 Azure Resource Manager 部署模型，請參閱： [AzOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk)和[AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdatadisk)。

     > [!WARNING]
     > 變更 Azure VM 磁碟的快取設定時，請停止 SQL Server 服務，以避免任何發生資料庫損毀的可能性。

* **NTFS 配置單位大小**：格式化資料磁片時，建議您針對資料和記錄檔以及 TempDB，使用 64-KB 的配置單位大小。 如果將 TempDB 放在暫存磁片上（D:\磁片磁碟機）利用此磁片磁碟機所獲得的效能，超過64K 配置單位大小的需求。 

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

* 將 SQL Server 的錯誤記錄檔和追蹤檔案目錄移至資料磁碟。 在 SQL Server 組態管理員中，以滑鼠右鍵按一下您的 SQL Server 執行個體並選取內容，即可完成。 您可以在 [**啟動參數**] 索引標籤中變更錯誤記錄檔和追蹤檔案設定。傾印目錄會在 [ **Advanced** ] 索引標籤中指定。下列螢幕擷取畫面顯示 [錯誤記錄檔啟動] 參數的尋找位置。

    ![SQL ErrorLog 螢幕擷取畫面](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

* 設定預設備份和資料庫檔案位置。 請使用本文中的建議，並在 [伺服器屬性] 視窗中進行變更。 如需指示，請參閱 [檢視或變更資料及記錄檔的預設位置 (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx)。 下列螢幕擷取畫面示範進行這些變更的位置。

    ![SQL 資料記錄和備份檔案](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)
* 起用鎖定的頁面，以減少 IO 和任何分頁活動。 如需詳細資訊，請參閱 [啟用鎖定記憶體分頁選項 (Windows)](https://msdn.microsoft.com/library/ms190730.aspx)。

* 如果您執行的是 SQL Server 2012，請安裝 Service Pack 1 累計更新 10。 此更新包含一個修正程式，可修正在 SQL Server 2012 中執行 select into 暫存資料表陳述式時，I/O 效能不佳的狀況。 如需相關資訊，請參閱此 [知識庫文章](https://support.microsoft.com/kb/2958012)。

* 將資料檔案傳輸至 Azure，或從 Azure 往外傳輸時，請考慮先壓縮所有資料檔案。

## <a name="feature-specific-guidance"></a>特定功能的指引

有些部署作業可能會使用更進階的組態技術，提供額外的效能優點。 下列清單特別強調了一些可協助您達到更佳效能的 SQL Server 功能：

### <a name="back-up-to-azure-storage"></a>備份至 Azure 儲存體
對 Azure 虛擬機器中所執行的 SQL Server 執行備份時，可以使用 [SQL Server 備份至 URL](https://msdn.microsoft.com/library/dn435916.aspx)。 此功能從 SQL Server 2012 SP1 CU2 開始提供，建議在備份至連接的資料磁碟時使用。 在備份至 Azure 儲存體 (或從中還原) 時，請依照 [SQL Server 備份至 URL 的最佳作法和疑難排解，以及從儲存在 Azure 儲存體中的備份還原](https://msdn.microsoft.com/library/jj919149.aspx)中提供的建議執行。 您也可以使用 [Azure 虛擬機器中的 SQL Server 自動備份](virtual-machines-windows-sql-automated-backup.md)，自動執行這些備份作業。

對於 SQL Server 2012 之前的版本，您可以使用 [將 SQL Server 備份至 Azure 工具](https://www.microsoft.com/download/details.aspx?id=40740)。 這項工具可以透過多個備份等量磁碟區目標協助您提高備份的輸送量。

### <a name="sql-server-data-files-in-azure"></a>在 Azure 中 SQL Server 資料檔案

從 SQL Server 2014 開始提供 [Azure 中的 SQL Server 資料檔案](https://msdn.microsoft.com/library/dn385720.aspx)這項新功能。 在 Azure 中執行具有資料檔案的 SQL Server 的效能與使用 Azure 資料磁碟的效能特性相當。

### <a name="failover-cluster-instance-and-storage-spaces"></a>容錯移轉叢集實例和儲存空間

如果您使用的是儲存空間，請在 [**確認**] 頁面上，將節點新增至叢集時，清除標示為 **[將所有合格的存放裝置新增至**叢集] 的核取方塊。 

![取消核取合格的儲存體](media/virtual-machines-windows-sql-performance/uncheck-eligible-cluster-storage.png)

如果您使用「儲存空間」但未取消選取 [新增適合的儲存裝置到叢集]****，Windows 就會在進行叢集程序時將虛擬機器中斷連結。 因此，它們將不會顯示在「磁碟管理員」或「總管」中，直到您使用 PowerShell 將儲存空間從叢集中移除後再重新連接為止。 儲存空間可將多個磁碟分組為存放集區。 如需詳細資訊，請參閱[儲存空間](/windows-server/storage/storage-spaces/overview)。

## <a name="next-steps"></a>後續步驟

如需儲存體和效能的詳細資訊，請參閱 [Azure VM 上的 SQL Server 適用的儲存體組態指導方針](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/)

如需安全性的最佳作法，請參閱 [Azure 虛擬機器中的 SQL Server 安全性考量](virtual-machines-windows-sql-security.md)。

請檢閱 [Azure 虛擬機器上的 SQL Server 概觀](virtual-machines-windows-sql-server-iaas-overview.md)中其他「SQL Server 虛擬機器」的相關文章。 如果您有 SQL Server 虛擬機器的相關問題，請參閱[常見問題集](virtual-machines-windows-sql-server-iaas-faq.md)。
