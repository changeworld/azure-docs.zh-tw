---
title: Azure 中的 SQL Server 效能指導方針 | Microsoft Docs
description: 提供在 Microsoft Azure 虛擬機器中優化 SQL Server 效能的指導方針。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.subservice: performance
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/09/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 6ba30436b363353ad183396e07111b33ca912dbf
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737433"
---
# <a name="performance-guidelines-for-sql-server-on-azure-virtual-machines"></a>Azure 虛擬機器中的 SQL Server 效能指導方針
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文提供在 Microsoft Azure 虛擬機器中優化 SQL Server 效能的指引。

## <a name="overview"></a>概觀

在 Azure 虛擬機器上執行 SQL Server 時，建議您繼續使用適用于內部部署伺服器環境中 SQL Server 的相同資料庫效能微調選項。 不過，公用雲端中關聯式資料庫的效能優劣取決於許多因素，例如虛擬機器的大小和資料磁碟的組態。

[Azure 入口網站中布建的 SQL Server 映射](sql-vm-create-portal-quickstart.md) 遵循一般儲存體設定的 [最佳做法](storage-configuration.md)。 佈建之後，請考慮套用本文所討論的其他最佳化作業。 依據您的工作負載做選擇，並透過測試進行確認。

> [!TIP]
> 您通常必須在最佳化成本與最佳化效能之間做出取捨。 本文著重于在 Azure 虛擬機器上取得 SQL Server 的 *最佳* 效能。 如果您的工作負載需求較低，可能就不需要採用下列每一項最佳化條件。 評估以下建議時，請考量您的效能需求、成本和工作負載模式。

## <a name="quick-checklist"></a>快速檢查清單

以下是在 Azure 虛擬機器上 SQL Server 最佳效能的快速檢查清單：

| 區域 | 最佳化 |
| --- | --- |
| [VM 大小](#vm-size-guidance) | -使用具有4個或更多 vCPU 的 VM 大小，例如 [Standard_M8 4 毫秒](../../../virtual-machines/m-series.md)、 [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series)或 [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) 或更高版本。 <br/><br/> -使用 [記憶體優化](../../../virtual-machines/sizes-memory.md) 的虛擬機器大小，以獲得 SQL Server 工作負載的最佳效能。 <br/><br/> - [DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md)、 [Edsv4](../../../virtual-machines/edv4-edsv4-series.md) 系列、 [M](../../../virtual-machines/m-series.md)和 [Mv2](../../../virtual-machines/mv2-series.md) 系列提供 OLTP 工作負載所需的最佳記憶體對 vCore 比率。 這兩個 M 系列 Vm 都提供任務關鍵性工作負載所需的最高記憶體對 vCore 比例，也適用于資料倉儲工作負載。 <br/><br/> -任務關鍵性和資料倉儲工作負載可能需要較高的記憶體對 vCore 比率。 <br/><br/> -利用 Azure 虛擬機器 marketplace 映射作為 SQL Server 設定，並設定儲存體選項以獲得最佳 SQL Server 效能。 <br/><br/> -收集目標工作負載的效能特性，並使用它們來為您的企業判斷適當的 VM 大小。|
| [儲存體](#storage-guidance) | -如需在 Azure 虛擬機器上使用 TPC-E 和 TPC_C 基準測試 SQL Server 效能的詳細測試，請參閱 blog 將 [OLTP 效能優化](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794)。 <br/><br/> - 使用[進階 SSD](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794) 以獲得最佳價格/效能優勢。 設定資料檔案的 [唯讀](../../../virtual-machines/premium-storage-performance.md#disk-caching) 快取，而不設定記錄檔的快取。 <br/><br/> -如果工作負載需要少於1毫秒的儲存體延遲，請使用 [Ultra 磁片](../../../virtual-machines/disks-types.md#ultra-disk) 。 若要深入瞭解，請參閱 [遷移至 ultra 磁片](storage-migrate-to-ultradisk.md) 。 <br/><br/> - 在選擇磁碟類型之前，[監視應用程式](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist)以收集 SQL Server 資料、記錄檔和暫存資料庫檔案的儲存延遲需求。 如果需要 < 1 毫秒的儲存體延遲，則請使用 Ultra 磁片，否則請使用 premium SSD。 如果只有記錄檔才需要低延遲 (資料檔案不需要)，則僅針對記錄檔以所需的 IOPS 和輸送量層級[佈建 Ultra 磁碟](../../../virtual-machines/disks-enable-ultra-ssd.md)。 <br/><br/>  - 標準儲存體僅建議用於開發和測試用途或備份檔案，而不應用於生產工作負載。 <br/><br/> - 將[儲存體帳戶](../../../storage/common/storage-account-create.md)和 SQL Server VM 置於同一個區域。<br/><br/> - 停用儲存體帳戶上的 Azure [異地備援儲存體](../../../storage/common/storage-redundancy.md) (異地複寫)。  |
| [磁碟](#disks-guidance) | - 使用至少 2 個[進階 SSD](../../../virtual-machines/disks-types.md#premium-ssd) (1 個用於儲存記錄檔，另 1 個用於儲存資料檔案)。 <br/><br/> -針對需要 < 1 毫秒 IO 延遲的工作負載，請啟用 M 系列的寫入加速器，並考慮針對 Es 和 DS 系列使用 Ultra SSD 磁片。 <br/><br/> - 在託管資料檔案的磁碟上啟用[唯讀快取](../../../virtual-machines/premium-storage-performance.md#disk-caching)功能。<br/><br/> -在設定[SQL Server 資料、記錄檔和 TempDB 檔案的儲存體時，](storage-configuration.md)新增額外的 20% premium IOPS/輸送量容量（以您的工作負載所需） <br/><br/> - 避免使用作業系統或暫存磁碟作為資料儲存體或進行記錄。<br/><br/> - 不要啟用裝載記錄檔案的磁碟上的 [快取] 功能。  **重要**：變更 Azure 虛擬機器磁片的快取設定時，請停止 SQL Server 服務。<br/><br/> - 分割多個 Azure 資料磁碟，以提高儲存體輸送量。<br/><br/> - 以文件上記載的配置大小格式化。 <br/><br/> - 針對任務關鍵性 SQL Server 工作負載，將 TempDB 置於本機 SSD 的 `D:\` 磁碟機上 (在選擇正確的 VM 大小後)。 如果您從 Azure 入口網站或 Azure 快速入門範本建立 VM，並 [將 TEMP DB 放在本機磁片上](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583)，則不需要採取任何進一步的動作;在其他所有情況下，請遵循 blog 中的步驟，  [使用 ssd 來儲存 TempDB](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-TempDB-and-buffer-pool-extensions/) ，以避免在重新開機後發生失敗。 如果本機磁碟的容量不足以供暫存資料庫大小使用，請將暫存資料庫放在存放集區 ([等量分配](../../../virtual-machines/premium-storage-performance.md)在具[唯讀快取](../../../virtual-machines/premium-storage-performance.md#disk-caching)的進階 SSD 磁碟上)。 |
| [I/O](#io-guidance) |- 啟用 [資料庫頁面壓縮] 功能　。<br/><br/> - 針對資料檔案，啟用 [立即檔案初始化] 功能。<br/><br/> - 限制資料庫的 [自動成長] 功能。<br/><br/> - 停用資料庫的 [自動壓縮] 功能。<br/><br/> - 將所有的資料庫 (包括系統資料庫) 移到資料磁碟。<br/><br/> - 將 SQL Server 的錯誤記錄檔和追蹤檔案目錄移至資料磁碟。<br/><br/> - 設定預設備份和資料庫檔案位置。<br/><br/> - [啟用記憶體中的鎖定分頁](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows)。<br/><br/> -針對已安裝的 SQL Server 版本評估並套用 [最新的累積更新](/sql/database-engine/install-windows/latest-updates-for-microsoft-sql-server) 。 |
| [特定功能](#feature-specific-guidance) | -直接備份至 Azure Blob 儲存體。<br/><br/>- 針對大於 12 TB 的資料庫使用[檔案快照集備份](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure)。 <br/><br/>- 使用多個暫存資料庫檔案，每個核心 1 個檔案，最多 8 個檔案。<br/><br/>- 將最大伺服器記憶體設定為 90%，或為作業系統保留最多 50 GB 的記憶體。 <br/><br/>- 啟用軟體 NUMA。 |


<br/>
如需有關「如何」和「為何」進行這些最佳化的詳細資訊，請檢閱下列各節提供的詳細資料與指導方針。
<br/><br/>

## <a name="getting-started"></a>開始使用

如果您要在 Azure VM 上建立新的 SQL Server，而不是遷移目前的來源系統，請根據您的廠商需求建立新的 SQL Server VM。  SQL Server VM 的廠商需求與您要在內部部署環境中部署的需求相同。 

如果您要使用為雲端建立的新應用程式來建立新的 SQL Server VM，您可以輕鬆地調整 SQL Server VM 的大小，因為您的資料和使用需求會不斷演進。
使用較低層的 D 系列、B 系列或 Av2 系列來開始開發環境，並在一段時間後擴大您的環境。 

針對生產 OLTP 環境建議的最小值為 4 vCore、32 GB 的記憶體，以及8的記憶體對 vCore 比例。 針對新環境，請從4部 vCore 機器開始，並在您的資料和計算需求變更時，調整為8、16、32虛擬核心或更多。 若為 OLTP 輸送量，請將 SQL Server 目標設為每個 vCore 都有 5000 IOPS 的 Vm。 

使用 SQL Server VM marketplace 映射搭配入口網站中的儲存體設定。 這可讓您更輕鬆地適當地建立儲存集區，以取得您工作負載所需的大小、IOPS 和輸送量。 選擇支援 premium 儲存體和高階儲存體快取 SQL Server Vm 很重要。 若要深入瞭解，請參閱 [儲存體](#storage-guidance) 一節。 

SQL Server 資料倉儲和任務關鍵性環境通常需要調整超過 8 vCore 的比率。 針對中型環境，您可能會想要選擇16個核心到記憶體的比率，以及針對較大的資料倉儲環境選擇32核心到記憶體的比率。 

SQL Server 資料倉儲環境通常受益于大型機器的平行處理。 基於這個理由，M 系列和 Mv2 系列是較大型資料倉儲環境的強大選項。

## <a name="vm-size-guidance"></a>VM 大小指引

使用來源電腦的 vCPU 和記憶體設定，作為將目前內部部署 SQL Server 資料庫移轉至 Azure Vm 上 SQL Server 的基準。 將您的核心授權帶到 Azure，以利用 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) 並節省 SQL Server 的授權成本。

**Microsoft 建議將記憶體 vCore 比例從8作為生產環境 SQL Server 工作負載的起點。** 非生產工作負載可以接受較小的比率。 

根據您 (OLTP 或資料倉儲) 的工作負載，選擇最適合 SQL Server 效能的 [記憶體優化](../../../virtual-machines/sizes-memory.md)、 [一般用途](../../../virtual-machines/sizes-general.md)、 [儲存體優化](../../../virtual-machines/sizes-storage.md)或 [受限制的 vCore](../../../virtual-machines/constrained-vcpu.md) 虛擬機器大小。 

### <a name="memory-optimized"></a>記憶體最佳化

[記憶體優化的虛擬機器大小](../../../virtual-machines/sizes-memory.md)是 SQL Server vm 的主要目標，也是 Microsoft 所建議的選擇。 記憶體優化的虛擬機器提供更強的記憶體到 CPU 比例和中型至大型快取選項。 

#### <a name="m-and-mv2-series"></a>M 與 Mv2 系列

[M 系列](../../../virtual-machines/m-series.md)針對某些最大 SQL Server 工作負載提供 vCore 計數和記憶體。  

[Mv2 系列](../../../virtual-machines/mv2-series.md)具有最高的 vCore 計數和記憶體，建議用於任務關鍵性和資料倉儲工作負載。 Mv2 系列實例是記憶體優化的 VM 大小，可提供無與倫比的計算效能，以支援大型記憶體內部資料庫和工作負載，且最適合關係資料庫伺服器、大型快取和記憶體內部分析。

例如， [Standard_M64ms](../../../virtual-machines/m-series.md) 有 28 vCore 的比率。

M 和 Mv2 系列的部分功能可針對 SQL Server 效能提供吸引，包括高階 [儲存體](../../../virtual-machines/premium-storage-performance.md) 和高階 [儲存體](../../../virtual-machines/premium-storage-performance.md#disk-caching) 快取支援、 [ultra 磁片](../../../virtual-machines/disks-enable-ultra-ssd.md) 支援，以及 [寫入加速](../../../virtual-machines/how-to-enable-write-accelerator.md)。

#### <a name="edsv4-series"></a>Edsv4-series

[Edsv4 系列](../../../virtual-machines/edv4-edsv4-series.md)是針對記憶體密集型應用程式所設計。 這些 Vm 擁有大型的本機儲存體 SSD 容量、強式本機磁片 IOPS、最多 504 GiB 的 RAM，以及相較于先前的 Ev3/Esv3 大小與 Gen2 Vm 的改進計算。 這些虛擬機器之間的記憶體與 vCore 比例幾乎一致，這是標準 SQL Server 工作負載的理想選擇。 

此 VM 系列適用于記憶體密集型的企業應用程式和應用程式，可受益于低延遲、高速的本機儲存體。

Edsv4 系列虛擬機器支援 [premium 儲存體](../../../virtual-machines/premium-storage-performance.md)，以及 [premium 儲存體](../../../virtual-machines/premium-storage-performance.md#disk-caching)快取。

#### <a name="dsv2-series-11-15"></a>DSv2 系列 11-15

[DSv2 系列 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15)具有與前 D 系列相同的記憶體和磁片設定。 在所有虛擬機器上，此系列的記憶體與 CPU 比例都一致。 

[DSv2 系列 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15)支援高階[儲存體](../../../virtual-machines/premium-storage-performance.md)和高階[儲存體](../../../virtual-machines/premium-storage-performance.md#disk-caching)快取，這是強烈建議的最佳效能。

### <a name="general-purpose"></a>一般用途

[一般用途的虛擬機器大小](../../../virtual-machines/sizes-general.md)是針對較小的進入層級工作負載（例如開發和測試、網頁伺服器和較小的資料庫伺服器）提供平衡的記憶體對 vCore 比例。 

由於具有一般用途虛擬機器的記憶體與 vCore 比例較小，因此請務必仔細監視記憶體型效能計數器，以確保 SQL Server 能取得所需的緩衝區快取記憶體。 如需詳細資訊，請參閱 [記憶體效能基準](#memory) 。 

由於生產工作負載的開始建議是記憶體對 vCore 比例8，因此執行 SQL Server 之一般用途 VM 的最低建議設定為 4 vCPU 和 32 GB 的記憶體。 

#### <a name="ddsv4-series"></a>Ddsv4 系列

[Ddsv4 系列](../../../virtual-machines/ddv4-ddsv4-series.md)提供 vCPU、記憶體和暫存磁片的公平組合，但記憶體對 vCore 的支援較小。 

Ddsv4 Vm 包含較低的延遲和更高速的本機儲存體。

這些機器適用于並存的 SQL 和應用程式部署，需要能夠快速存取暫存儲存體和部門關係資料庫。 此系列中的所有虛擬機器都有標準的記憶體對 vCore 比例4。 

基於這個理由，建議您利用此系列中的「入門虛擬機器」 D8ds_v4，其中包含8虛擬核心和 32 Gb 的記憶體。 最大的機器是 D64ds_v4，其具有64虛擬核心和 256 Gb 的記憶體。

[Ddsv4 系列](../../../virtual-machines/ddv4-ddsv4-series.md)虛擬機器支援[premium 儲存體](../../../virtual-machines/premium-storage-performance.md)和[premium 儲存體](../../../virtual-machines/premium-storage-performance.md#disk-caching)快取。

> [!NOTE]
> [Ddsv4 系列](../../../virtual-machines/ddv4-ddsv4-series.md)沒有針對 SQL Server 工作負載建議的記憶體對 vCore 比率8。 因此，只考慮將這些虛擬機器用於較小的應用程式和開發工作負載。

#### <a name="b-series"></a>B 系列

[高載 B 系列](../../../virtual-machines/sizes-b-series-burstable.md)虛擬機器大小適用于不需要一致效能的工作負載，例如概念證明和非常小型的應用程式和開發伺服器。 

大部分的 [高載 B 系列](../../../virtual-machines/sizes-b-series-burstable.md) 虛擬機器大小都有4的記憶體對 vCore 比率。 這些機器的最大值是具有20虛擬核心和 80 GB 記憶體的 [Standard_B20ms](../../../virtual-machines/sizes-b-series-burstable.md) 。

此系列是唯一的，因為應用程式可以在上班時間內進行高載，並根據機器 **大小來改變** 高載點數。 

當點數用盡時，VM 會回到基準機器的效能。

B 系列的優點是，相較于其他系列中的其他 VM 大小，您可以節省的計算成本，特別是在一天內需要謹慎處理能力的情況下。

此系列支援 [premium 儲存體](../../../virtual-machines/premium-storage-performance.md)，但 **不支援** [premium 儲存體](../../../virtual-machines/premium-storage-performance.md#disk-caching)快取。

> [!NOTE] 
> [高載 B 系列](../../../virtual-machines/sizes-b-series-burstable.md)沒有針對 SQL Server 工作負載建議的記憶體對 vCore 比率8。 因此，請考慮只針對較小的應用程式、網頁伺服器和開發工作負載使用這些虛擬機器。

#### <a name="av2-series"></a>Av2 系列

[Av2 系列](../../../virtual-machines/av2-series.md)vm 最適合用於入門層級的工作負載，例如開發和測試、低流量網頁伺服器、小型至中型應用程式資料庫，以及概念證明。

只有 [Standard_A2m_v2](../../../virtual-machines/av2-series.md) (2 虛擬核心和16GBs 的記憶體) 、 [Standard_A4m_v2](../../../virtual-machines/av2-series.md) (4 虛擬核心和32GBs 的記憶體) ，以及 [Standard_A8m_v2](../../../virtual-machines/av2-series.md) (8 虛擬核心和64GBs 記憶體) 具有適用于這三個前三部虛擬機器的良好記憶體/vCore 比率8。 

這些虛擬機器對於較小的開發和測試 SQL Server 電腦都是不錯的選擇。 

8 vCore [Standard_A8m_v2](../../../virtual-machines/av2-series.md) 也可能是適用于小型應用程式和網頁伺服器的絕佳選項。

> [!NOTE] 
> Av2 系列不支援 premium 儲存體，因此不建議在生產環境 SQL Server 工作負載使用，即使虛擬機器的 vCore 比率為8。

### <a name="storage-optimized"></a>儲存體最佳化

[儲存體優化的 VM 大小](../../../virtual-machines/sizes-storage.md)適用于特定的使用案例。 這些虛擬機器特別設計為具有優化的磁片輸送量和 IO。 此虛擬機器系列適用于海量資料案例、資料倉儲和大型交易資料庫。 

#### <a name="lsv2-series"></a>Lsv2 系列

[Lsv2 系列](../../../virtual-machines/lsv2-series.md)具有高輸送量、低延遲和本機 NVMe 儲存體。 Lsv2 系列 Vm 已優化，可使用直接連接至 VM 之節點上的本機磁片，而不是使用永久性資料磁片。 

這些虛擬機器是大型資料、資料倉儲、報告和 ETL 工作負載的強大選項。 本機 NVMe 儲存體的高輸送量和 IOPs 適合用來處理將載入您資料庫的檔案，以及可從來源系統或其他存放庫（例如 Azure Blob 儲存體或 Azure Data Lake）重新建立來源資料的其他情況。 [Lsv2 系列](../../../virtual-machines/lsv2-series.md) Vm 也可以一次提高磁片效能，最多可達30分鐘。

這些虛擬機器的大小是從8到 80 vCPU，每個 vCPU 有 8 GiB 的記憶體，每8個個 vcpu 都有 1.92 TB 的 NVMe SSD。 這代表此系列的最大 VM （ [L80s_v2](../../../virtual-machines/lsv2-series.md)）有 80 vCPU 和 640 BiB 的記憶體，且有10倍以上的 NVMe 儲存體 x 1.92 tb。  所有這些虛擬機器之間的記憶體與 vCore 比例一致。

NVMe 儲存體是暫時的，這表示如果您重新開機虛擬機器，資料將會遺失在這些磁片上。

Lsv2 和 Ls 系列支援 [premium](../../../virtual-machines/premium-storage-performance.md)儲存體，但不支援 premium 儲存體快取。 不支援建立本機快取來增加 IOPs。 

> [!WARNING]
> 將資料檔案儲存在暫時 NVMe 儲存體上，可能會在 VM 解除配置時導致資料遺失。 

### <a name="constrained-vcores"></a>限制虛擬核心

高效能 SQL Server 工作負載通常需要較高的記憶體、IO 和輸送量，而不會有較高的 vCore 計數。 

大部分的 OLTP 工作負載都是由大量較小交易所驅動的應用程式資料庫。 使用 OLTP 工作負載時，只會讀取或修改少量的資料，但是依使用者計數驅動的交易數量會高出許多。 請務必將 SQL Server 記憶體提供給快取計畫、儲存最近存取的資料以獲得效能，並確保實體讀取可以快速讀入記憶體中。 

這些 OLTP 環境需要較高的記憶體量、快速儲存體，以及最佳執行所需的 i/o 頻寬。 

為了維持此層級的效能，而不會有較高的 SQL Server 授權成本，Azure 會提供具有 [受限 vCPU 計數](../../../virtual-machines/constrained-vcpu.md)的 VM 大小。 

這有助於藉由減少可用的虛擬核心來控制授權成本，同時維持相同的記憶體、儲存體和父系虛擬機器的 i/o 頻寬。

VCPU 計數可以限制為原始 VM 大小的一半到一季。 減少虛擬機器可用的虛擬核心，將可達到更高的記憶體對 vCore 比率。

這些新的 VM 大小具有尾碼，可指定使用中個 vcpu 的數目，使其更容易識別。 

例如， [m64 可使用-m64-32ms](../../../virtual-machines/constrained-vcpu.md) 只需要授權 32 SQL Server 虛擬核心搭配 [M64ms](../../../virtual-machines/m-series.md) 的記憶體、IO 和輸送量，而 [m64 可使用-16 毫秒](../../../virtual-machines/constrained-vcpu.md) 只需要授權16虛擬核心。  雖然 [m64 可使用-16 毫秒](../../../virtual-machines/constrained-vcpu.md) 具有 M64ms 的 SQL Server 授權成本，但虛擬機器的計算成本也相同。

> [!NOTE] 
> - 中型至大型資料倉儲工作負載可能仍受益于 [受限制的 VCore vm](../../../virtual-machines/constrained-vcpu.md)，但資料倉儲工作負載的特性通常是較少的使用者和處理常式，透過平行執行的查詢計劃來處理更大量的資料。 
> - 計算成本（包括作業系統授權）將維持與父虛擬機器相同。 

## <a name="storage-guidance"></a>儲存體指引

若要使用 TPC-E 和 TPC C 基準測試來對 Azure 虛擬機器上的 SQL Server 效能進行詳細測試，請參閱 blog 將 [OLTP 效能優化](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794)。 

針對所有生產工作負載，建議使用具進階 SSD 的 Azure Blob 快取。 

> [!WARNING]
> 標準 HDD 具有與 SSD 不同的延遲和頻寬，因此僅建議用於開發/測試工作負載。 生產工作負載應該使用進階 SSD。

此外，我們建議您在存放 SQL Server 虛擬機器的同一個資料中心內建立 Azure 儲存體帳戶，以減少傳輸延遲的狀況。 建立儲存體帳戶時，請停用 [異地複寫] 功能，因為跨多個磁碟時無法保證寫入順序一致。 請考慮另一個方法，在兩個 Azure 資料中心之間設定 SQL Server 災害復原技術。 如需詳細資訊，請參閱 [Azure 虛擬機器中的 SQL Server 高可用性和災害復原](business-continuity-high-availability-disaster-recovery-hadr-overview.md)。

## <a name="disks-guidance"></a>磁碟指引

Azure 虛擬機器上有三種主要的磁片類型：

* **OS 磁片**：當您建立 Azure 虛擬機器時，此平臺會至少將一個磁片 (標示為 **C** 磁片磁碟機，) 至 VM 作為您的作業系統磁片。 此磁碟是以分頁 Blob 的形式儲存於儲存體的 VHD。
* **暫存磁碟**：Azure Stack 虛擬機器會包含另一個稱為暫存磁碟的磁碟 (標示為 **D**: 磁碟機)。 此磁碟位於可用於塗銷空間的節點上。
* **資料磁碟**：您也可以將其他磁碟連接至虛擬機器作為資料磁碟，這些磁碟將會以分頁 Blob 形式儲存於儲存體。

下列各節說明使用這些不同磁碟的建議。

### <a name="operating-system-disk"></a>作業系統磁碟

作業系統磁片是指您可以開機並掛接為執行中作業系統版本的 VHD，並標示為 **C** 磁片磁碟機。

作業系統磁碟上的預設快取原則是 **讀取/寫入**。 對於需要高效能的應用程式，我們建議您使用資料磁碟取代作業系統磁碟。 請參閱下面的＜資料磁碟＞一節。

### <a name="temporary-disk"></a>暫存磁碟

標示為 **D** 磁片磁碟機的暫存磁片磁碟機不會保存到 Azure Blob 儲存體。 請勿將使用者資料庫檔案或使用者交易記錄檔儲存在 **D**: 磁碟機。

針對任務關鍵性 SQL Server 工作負載，將 TempDB 置於本機 SSD 的 `D:\` 磁碟機上 (在選擇正確的 VM 大小後)。 如果您從 Azure 入口網站或 Azure 快速入門範本建立 VM，並 [將 TEMP DB 放在本機磁片上](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583)，則不需要採取任何進一步的動作;在其他所有情況下，請遵循 blog 中的步驟，  [使用 ssd 來儲存 TempDB](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-TempDB-and-buffer-pool-extensions/) ，以避免在重新開機後發生失敗。 如果本機磁碟的容量不足以供暫存資料庫大小使用，請將暫存資料庫放在存放集區 ([等量分配](../../../virtual-machines/premium-storage-performance.md)在具[唯讀快取](../../../virtual-machines/premium-storage-performance.md#disk-caching)的進階 SSD 磁碟上)。

對於支援進階 SSD 的 VM，您也可將 TempDB 儲存在支援進階 SSD 且啟用讀取快取的磁碟上。


### <a name="data-disks"></a>資料磁碟

* **將進階 SSD 用於資料檔和記錄檔**：如果您未使用磁碟等量分割，請使用兩個進階 SSD；其中一個磁碟包含記錄檔，另一個則包含資料。 每個進階 SSD 都會根據其大小提供數個 IOPS 和頻寬 (MB/s)，如[選取磁碟類型](../../../virtual-machines/disks-types.md)一文所述。 如果您使用磁碟等量化技術 (例如儲存空間)，您將會有兩個集區 (一個用於記錄檔，另一個用於資料檔案)，而達到最佳效能。 不過，如果您打算使用 SQL Server 容錯移轉叢集執行個體 (FCI)，則必須設定一個集區，或改為使用[進階檔案共用](failover-cluster-instance-premium-file-share-manually-configure.md)。

   > [!TIP]
   > - 如需各種磁片和工作負載設定的測試結果，請參閱下列 blog 文章： [Azure 虛擬機器上 SQL Server 的儲存體設定指導方針](/archive/blogs/sqlserverstorageengine/storage-configuration-guidelines-for-sql-server-on-azure-vm)。
   > - 需要 ~ 50,000 IOPS 的 SQL Server 若要達到任務關鍵性效能，請考慮以 Ultra SSD 取代 10 個 -P30 磁碟。 如需詳細資訊，請參閱以下部落格文章：[Ultra SSD 的任務關鍵性效能](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/)。

   > [!NOTE]
   > 當您在入口網站中佈建 SQL Server VM 時，您可以選擇編輯儲存體組態。 根據您的設定，Azure 會設定一或多個磁碟。 多個磁碟會利用串接合併成單一儲存體集區。 資料和記錄檔皆在此設定中。 如需詳細資訊，請參閱 [SQL Server VM 的儲存體組態](storage-configuration.md)。

* **磁片** 等量：若要取得更多輸送量，您可以新增其他資料磁片，並使用磁片等量分割。 為了判斷資料磁碟的數目，您需要分析記錄檔以及資料和 TempDB 檔案所需的 IOPS 和頻寬數目。 請注意，不同的 VM 大小在支援的 IOPS 和頻寬數目上有不同的限制，請參閱每個 [VM 大小](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)的 IOPS 相關表格。 請使用下列指引：

  * 若為 Windows 8/Windows Server 2012 以上版本，請遵循下列指導方針使用[儲存空間](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831739(v=ws.11))：

      1. 將 OLTP 工作負載的間隔 (等量磁碟區大小) 設為 64 KB (65,536 位元組)，資料倉儲的工作負載則設為 256 KB (262,144 位元組)，以避免分割對齊錯誤影響效能。 必須使用 PowerShell 來設定。
      2. 設定資料行數目 = 實體磁碟數量。 設定 8 個以上的磁碟 (不是伺服器管理員 UI) 時，使用 PowerShell。 

    例如，下列 PowerShell 會建立一個新的儲存集區，其交錯大小為 64 KB，且資料行數目等於存放集區中的實體磁片數量：

    ```powershell
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}
    
    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" `
        -PhysicalDisks $PhysicalDisks | New- VirtualDisk -FriendlyName "DataFiles" `
        -Interleave 65536 -NumberOfColumns $PhysicalDisks .Count -ResiliencySettingName simple `
        –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter `
        -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" `
        -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * 對於 Windows 2008 R2 之前的版本，可以使用動態磁碟 (OS 分割的磁碟區)，且等量磁碟區的大小一律為 64 KB。 Windows 8/Windows Server 2012 已不再提供此選項。 如需相關資訊，請參閱 [虛擬磁碟服務正轉換為 Windows 存放管理 API](/windows/win32/w8cookbook/vds-is-transitioning-to-wmiv2-based-windows-storage-management-api)中的支援聲明。

  * 如果您要搭配使用[儲存空間直接存取 (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) 與 [SQL Server 容錯移轉叢集執行個體](failover-cluster-instance-storage-spaces-direct-manually-configure.md)，您必須設定單一集區。 雖然可以在單一集區上建立不同的磁碟區，但這些磁碟區將共用相同特性，例如相同的快取原則。

  * 請根據您預期的負載量，決定與您的儲存體集區相關聯的磁碟數量。 請注意，各 VM 大小所允許連接的資料磁碟數量皆不同。 如需詳細資訊，請參閱 [虛擬機器的大小](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

  * 如果您未使用 premium Ssd (開發/測試案例) ，建議您新增 [VM 大小](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 所支援的最大資料磁片數目，並使用磁片等量分割。

* **快取原則**：請注意，下列快取原則的建議會視您的儲存體設定而有所不同。

  * 如果您針對資料和記錄檔使用不同磁碟，請在裝載資料檔和 TempDB 資料檔案的資料磁碟上啟用讀取快取。 這會帶來明顯的效能優勢。 請勿在裝載記錄檔的磁碟上啟用快取，這樣會導致效能小幅降低。

  * 如果您要在單一儲存體集區中使用磁碟等量化，大部分的工作負載將會從讀取快取中獲益。 如果您為記錄檔和資料檔案使用個別的儲存體集區，請僅在資料檔的儲存體集區上啟用讀取快取。 針對某些大量寫入的工作負載，沒有快取可能會達到更好的效能。 這只能透過測試來判斷。

  * 上述建議適用於進階 SSD。 如果您並非使用進階 SSD，請勿在任何資料磁碟上啟用任何快取功能。

  * 如需有關設定磁碟快取功能的指示，請參閱下列文章。 針對傳統 (ASM) 部署模型，請參閱：[Set-AzureOSDisk](/previous-versions/azure/jj152847(v=azure.100)) \(英文\) 和 [Set-AzureDataDisk](/previous-versions/azure/jj152851(v=azure.100)) \(英文\)。 如需 Azure Resource Manager 部署模型，請參閱：[Set-AzOSDisk](/powershell/module/az.compute/set-azvmosdisk) \(英文\) 和 [Set-AzVMDataDisk](/powershell/module/az.compute/set-azvmdatadisk) \(英文\)。

     > [!WARNING]
     > 變更 Azure 虛擬機器磁片的快取設定時，請停止 SQL Server 服務，以避免發生任何資料庫損毀的可能性。

* **NTFS 配置單位大小**：格式化資料磁碟時，建議您針對資料/記錄檔案和 TempDB，採用 64 KB 的配置單位大小。 如果 TempDB 放置在暫存磁片上 (D:\利用此磁片磁碟機) 的效能，超過 64 KB 配置單位大小的需求。 

* **磁碟管理最佳做法**：移除資料磁碟或變更其快取類型時，請於變更期間停止 SQL Server 服務。 當 OS 磁碟上的快取設定發生變更時，Azure 會停止 VM、變更快取類型，然後重新啟動 VM。 當資料磁碟的快取設定發生變更時，系統不會停止 VM，但在變更期間會從 VM 卸離資料磁碟，然後再重新連接。

  > [!WARNING]
  > 如果在這些作業期間無法停止 SQL Server 服務，可能就會造成資料庫損毀。


## <a name="io-guidance"></a>I/O 指引

* 進階 SSD 在您以平行方式處理應用程式和要求時可以達到最佳效能。 進階 SSD 是專為 IO 佇列深度大於 1 的案例所設計，所以您會發現單一執行緒的序列要求 (即使其為儲存密集型) 的效能只有微幅提升，或沒有提升。 舉例來說，這可能會影響效能分析工具 (如 SQLIO) 的單一執行緒測試結果。

* 請考慮使用 [資料庫頁面壓縮](/sql/relational-databases/data-compression/data-compression) 功能，其有助於改善 I/O 密集型工作負載的效能。 不過，資料壓縮可能會增加資料庫伺服器的 CPU 使用量。

* 請考慮啟用 [立即檔案初始化] 功能，以減少配置初始檔案所需的時間。 若要發揮「立即檔案初始化」的優點，請將 SE_MANAGE_VOLUME_NAME 授與 SQL Server (MSSQLSERVER) 服務帳戶，並將該帳戶加入[執行磁碟區維護工作] 安全性原則。 如果您使用的是 Azure 的 SQL Server 平台映像，預設服務帳戶 (NT Service\MSSQLSERVER) 不會加入 [執行磁碟區維護工作] 安全性原則。 也就是說，SQL Server Azure 平台映像未啟用 [立即檔案初始化] 功能。 將 SQL Server 服務帳戶加入 [執行磁碟區維護工作]  安全性原則之後，請重新啟動 SQL Server 服務。 使用此功能時，可能有安全性考量。 如需詳細資訊，請參閱 [資料庫檔案初始化](/sql/relational-databases/databases/database-instant-file-initialization)。

* 請注意，自動成長會視為非預期 **成長的應變** 措施。 請勿每天使用「自動成長」功能，管理資料和記錄成長。 若已使用「自動成長」功能，請透過 大小參數預先放大檔案。

* 請確定已停用「自動壓縮」  ，以避免不必要的額外負荷，而對效能造成負面影響。

* 將所有的資料庫 (包括系統資料庫) 移到資料磁碟。 如需詳細資訊，請參閱 [移動系統資料庫](/sql/relational-databases/databases/move-system-databases)。

* 將 SQL Server 的錯誤記錄檔和追蹤檔案目錄移至資料磁碟。 在 SQL Server 組態管理員中，以滑鼠右鍵按一下您的 SQL Server 執行個體並選取內容，即可完成。 您可以在 [啟動參數]  索引標籤中變更錯誤記錄和追蹤檔案設定。若要指定「傾印目錄」，則是在 [進階]  索引標籤中指定。下列螢幕擷取畫面顯示錯誤記錄啟動參數的位置。

    ![SQL ErrorLog 螢幕擷取畫面](./media/performance-guidelines-best-practices/sql_server_error_log_location.png)

* 設定預設備份和資料庫檔案位置。 請使用本文中的建議，並在 [伺服器屬性] 視窗中進行變更。 如需指示，請參閱 [檢視或變更資料及記錄檔的預設位置 (SQL Server Management Studio)](/sql/database-engine/configure-windows/view-or-change-the-default-locations-for-data-and-log-files)。 下列螢幕擷取畫面示範進行這些變更的位置。

    ![SQL 資料記錄和備份檔案](./media/performance-guidelines-best-practices/sql_server_default_data_log_backup_locations.png)
* 起用鎖定的頁面，以減少 IO 和任何分頁活動。 如需詳細資訊，請參閱 [啟用鎖定記憶體分頁選項 (Windows)](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows)。

* 如果您執行的是 SQL Server 2012，請安裝 Service Pack 1 累計更新 10。 此更新包含一個修正程式，可修正在 SQL Server 2012 中執行 select into 暫存資料表陳述式時，I/O 效能不佳的狀況。 如需相關資訊，請參閱此 [知識庫文章](https://support.microsoft.com/kb/2958012)。

* 將資料檔案傳輸至 Azure，或從 Azure 往外傳輸時，請考慮先壓縮所有資料檔案。

## <a name="feature-specific-guidance"></a>特定功能的指引

有些部署作業可能會使用更進階的組態技術，提供額外的效能優點。 下列清單特別強調了一些可協助您達到更佳效能的 SQL Server 功能：

### <a name="back-up-to-azure-storage"></a>備份至 Azure 儲存體
針對在 Azure 虛擬機器中執行的 SQL Server 執行備份時，您可以使用 [SQL Server 備份至 URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)。 此功能從 SQL Server 2012 SP1 CU2 開始提供，建議在備份至連接的資料磁碟時使用。 當您備份/還原至 Azure 儲存體時，請遵循 [SQL Server 備份至 URL 的最佳作法和疑難排解，以及從儲存在 Azure 儲存體中的備份還原](/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting)的建議。 您也可以使用 [Azure 虛擬機器上 SQL Server 的自動備份](../../../azure-sql/virtual-machines/windows/automated-backup-sql-2014.md)，將這些備份自動化。

對於 SQL Server 2012 之前的版本，您可以使用 [將 SQL Server 備份至 Azure 工具](https://www.microsoft.com/download/details.aspx?id=40740)。 這項工具可以透過多個備份等量磁碟區目標協助您提高備份的輸送量。

### <a name="sql-server-data-files-in-azure"></a>Azure 中的 SQL Server 資料檔案

從 SQL Server 2014 開始提供 [Azure 中的 SQL Server 資料檔案](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure)這項新功能。 在 Azure 中執行具有資料檔案的 SQL Server 的效能與使用 Azure 資料磁碟的效能特性相當。

### <a name="failover-cluster-instance-and-storage-spaces"></a>容錯移轉叢集執行個體和儲存空間

如果您使用的是「儲存空間」，在 [ **確認** ] 頁面上將節點新增至叢集時，請清除 [ **新增適合的儲存裝置到** 叢集] 核取方塊。 

![取消核取合格的儲存體](./media/performance-guidelines-best-practices/uncheck-eligible-cluster-storage.png)

如果您使用「儲存空間」但未取消選取 [新增適合的儲存裝置到叢集]，Windows 就會在進行叢集程序時將虛擬機器中斷連結。 因此，它們將不會顯示在「磁碟管理員」或「總管」中，直到您使用 PowerShell 將儲存空間從叢集中移除後再重新連接為止。 儲存空間可將多個磁碟分組為存放集區。 如需詳細資訊，請參閱[儲存空間](/windows-server/storage/storage-spaces/overview)。

## <a name="multiple-instances"></a>多個執行個體 

將多個 SQL Server 實例部署至單一虛擬機器時，請考慮下列最佳做法： 

- 設定每個 SQL Server 實例的最大伺服器記憶體，以確保作業系統剩餘的記憶體。 如果您變更配置給虛擬機器的記憶體數量，請務必更新 SQL Server 實例的記憶體限制。 
- 針對資料、記錄和 TempDB 具有不同的 Lun，因為它們都有不同的工作負載模式，而您不想讓它們彼此影響。 
- 在大量類似生產環境的工作負載下，徹底測試您的環境，以確保它可以處理您應用程式 Sla 內的尖峰工作負載容量。 

多載系統的徵兆可包括（但不限於）工作者執行緒耗盡、回應時間變慢及/或停止發送器系統記憶體。 



## <a name="collect-performance-baseline"></a>收集效能基準

如需更有規範的方法，請使用 PerfMon/LogMan 收集效能計數器，並取得 SQL Server 等候統計資料，以深入瞭解來源環境的一般壓力和潛在的瓶頸。 

從收集[應用程式效能檢查清單](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist)之後的尖峰時間，收集來源工作負載的 CPU、記憶體、 [IOPS](../../../virtual-machines/premium-storage-performance.md#iops)、[輸送量](../../../virtual-machines/premium-storage-performance.md#throughput)和[延遲](../../../virtual-machines/premium-storage-performance.md#latency)。 

在您的一般工作天期間收集資料，例如工作負載，以及其他高負載進程（例如，結束日期處理）和週末 ETL 工作負載。 請考慮相應增加您的資源，以取得非常頻繁的工作負載（例如，季末處理），然後在工作負載完成後調整規模。 

使用效能分析來選取可以調整為工作負載效能需求的 [VM 大小](../../../virtual-machines/sizes-memory.md) 。


### <a name="iops-and-throughput"></a>IOPS 和輸送量

SQL Server 的效能取決於 i/o 子系統。 除非您的資料庫符合實體記憶體，否則 SQL Server 會不斷地將資料庫頁面帶入和移出緩衝集區。 SQL Server 的資料檔案應該以不同的方式處理。 記錄檔的存取是連續的，但交易必須回復時，才會隨機存取資料檔案（包括 TempDB）。 如果您的 i/o 子系統很慢，您的使用者可能會遇到效能問題，例如因為超時時間而未完成的回應時間和工作。 

Azure Marketplace 的虛擬機器在實體磁片上的記錄檔預設會與資料檔案不同。 TempDB 資料檔案的計數和大小符合最佳做法，並以暫時 D：/驅動。。 

下列 PerfMon 計數器可協助驗證 SQL Server 所需的 IO 輸送量： 
* **\LogicalDisk\Disk Reads/Sec** (讀取和寫入 IOPS) 
* **\LogicalDisk\Disk Writes/Sec** (讀取和寫入 IOPS)  
* **\LogicalDisk\Disk Bytes/Sec** (資料、記錄檔和 TempDB 檔案的輸送量需求) 

使用尖峰層級的 IOPS 和輸送量需求，評估符合您測量容量的 VM 大小。 

如果您的工作負載需要 20 Kb 的讀取 IOPS 和 1 Kb 的寫入 IOPS，您可以選擇 E16s_v3 (，最高可達 32 K 快取和25600未快取的 iops) 或 M16_s (，最多可達 20 K 快取和10K 未快取的 iops，) 使用儲存空間等量 2 P30 磁片 

請務必瞭解工作負載的輸送量和 IOPS 需求，因為 Vm 對於 IOPS 和輸送量具有不同的規模限制。

### <a name="memory"></a>記憶體

追蹤作業系統所使用的外部記憶體，以及 SQL Server 內部使用的記憶體。 識別任一元件的壓力將有助於調整虛擬機器的大小，並找出調整的機會。 

下列 PerfMon 計數器可協助驗證 SQL Server 虛擬機器的記憶體健全狀況： 
* [\記憶體\可用的 MB](/azure/monitoring/infrastructure-health/vmhealth-windows/winserver-memory-availmbytes)
* [\SQLServer： Memory Manager\Target Server Memory (KB) ](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer： Memory Manager\Total Server Memory (KB) ](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer： Buffer Manager\Lazy writes/sec](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer：緩衝區 Manager\Page 壽命預期](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)

### <a name="compute--processing"></a>計算/處理

Azure 中的計算與內部部署的管理方式不同。 內部部署伺服器在過去幾年內都不需要升級，因為管理額外負荷和取得新硬體的成本。 虛擬化可減少其中一些問題，但應用程式會經過優化以充分利用基礎硬體，這表示對資源耗用量的任何重大變更都需要重新平衡整個實體環境。 

這在 Azure 中並不是一項挑戰，因為在不同的硬體系列（甚至在不同的區域）中的新虛擬機器很容易達成。 

在 Azure 中，您想要盡可能充分利用虛擬機器的資源，因此，您應該將 Azure 虛擬機器設定為盡可能將平均 CPU 維持在最高，而不會影響工作負載。 

下列 PerfMon 計數器可協助驗證 SQL Server 虛擬機器的計算健康狀態：
* **\Processor 資訊 (_Total) \% 處理器時間**
* **\Process (sqlservr.exe) \% 處理器時間**

> [!NOTE] 
> 在理想的情況下，請嘗試使用80% 的計算，其尖峰高於90%，但在任何持續時間內不會達到100%。 基本上，您只想要布建應用程式所需的計算，然後規劃在企業需要時擴大或縮小。 

## <a name="next-steps"></a>後續步驟

如需安全性最佳作法，請參閱 [Azure 虛擬機器上 SQL Server 的安全性考慮](security-considerations-best-practices.md)。

請檢閱 [Azure 虛擬機器上的 SQL Server 概觀](sql-server-on-azure-vm-iaas-what-is-overview.md)中其他「SQL Server 虛擬機器」的相關文章。 如果您有 SQL Server 虛擬機器的相關問題，請參閱[常見問題集](frequently-asked-questions-faq.md)。