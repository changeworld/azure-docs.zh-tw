---
title: 規劃 Azure 檔案同步部署 | Microsoft Docs
description: 了解規劃 Azure 檔案服務部署時的考量事項。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 0684f626553946619a0db2cd895df39576bd17b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255115"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>規劃 Azure 檔案同步部署
[Azure 檔](storage-files-introduction.md)可通過兩種主要方式進行部署：直接安裝無伺服器 Azure 檔共用或使用 Azure 檔同步在本機快取 Azure 檔共用。您選擇的部署選項會更改在規劃部署時需要考慮的事項。 

- **Azure 檔共用的直接裝載**：由於 Azure 檔提供 SMB 訪問，因此可以使用 Windows、macOS 和 Linux 中可用的標準 SMB 用戶端在本地或雲中裝載 Azure 檔共用。 由於 Azure 檔共用是無伺服器的，因此為生產方案部署不需要管理檔案伺服器或 NAS 設備。 這意味著您不必應用軟體修補程式或交換物理磁片。 

- **使用 Azure 檔同步在本機快取 Azure 檔共用**：Azure 檔同步使您能夠在 Azure 檔中集中組織的檔共用，同時保持本地檔案伺服器的靈活性、性能和相容性。 Azure 檔同步將本地（或雲）Windows 伺服器轉換為 Azure 檔共用的快速緩存。 

本文主要介紹部署 Azure 檔同步的部署注意事項。要計畫由本地或雲用戶端直接裝載 Azure 檔共用的部署，請參閱[規劃 Azure 檔部署](storage-files-planning.md)。

## <a name="management-concepts"></a>管理概念
Azure 檔同步部署有三個基本管理物件：

- **Azure 檔共用**：Azure 檔共用是無伺服器雲檔共用，它提供 Azure 檔同步關係的*雲終結點*。 Azure 檔共用中的檔可以直接使用 SMB 或 FileREST 協定訪問，但我們鼓勵您主要在 Azure 檔共用與 Azure 檔同步一起使用時通過 Windows Server 緩存訪問檔。這是因為 Azure 檔今天缺少像 Windows Server 那樣的有效更改檢測機制，因此對 Azure 檔共用的更改需要時間才能傳播回伺服器終結點。
- **伺服器終結點**：正在同步到 Azure 檔共用的 Windows 伺服器上的路徑。 這可以是卷或卷根上的特定資料夾。 如果多個伺服器終結點的命名空間不重疊，則同一卷上可以存在多個伺服器終結點。
- **同步組**：定義**雲終結點**、 或 Azure 檔共用和伺服器終結點之間的同步關係的物件。 同步群組內的端點會與彼此保持同步。 例如，如果您有兩組不同的檔，您希望使用 Azure 檔同步進行管理，則可以創建兩個同步組，並將不同的終結點添加到每個同步組。

### <a name="azure-file-share-management-concepts"></a>Azure 檔共用管理概念
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

### <a name="azure-file-sync-management-concepts"></a>Azure 檔同步管理概念
同步組被部署到**存儲同步服務**中，這些服務是註冊伺服器以與 Azure 檔同步一起使用並包含同步組關係的頂級物件。 儲存體同步服務資源是儲存體帳戶資源的對等，同樣可以部署到 Azure 資源群組中。 存儲同步服務可以創建包含跨多個存儲帳戶和多個已註冊的 Windows 伺服器的 Azure 檔共用的同步組。

在存儲同步服務中創建同步組之前，必須先將 Windows 伺服器與存儲同步服務註冊。 這將創建**一個註冊的伺服器**物件，該物件表示伺服器或群集與存儲同步服務之間的信任關係。 要註冊存儲同步服務，必須首先在伺服器上安裝 Azure 檔同步代理。 單個伺服器或群集一次只能註冊一個存儲同步服務。

同步組包含一個雲終結點或 Azure 檔共用，以及至少一個伺服器終結點。 伺服器終結點物件包含配置**雲分層**功能的設置，它提供了 Azure 檔同步的緩存功能。為了與 Azure 檔共用同步，包含 Azure 檔共用的存儲帳戶必須與存儲同步服務位於同一 Azure 區域中。

### <a name="management-guidance"></a>管理指南
部署 Azure 檔同步時，我們建議：

- 部署 Azure 檔與 Windows 檔共用共用 1：1。 伺服器終結點物件在如何設置同步關係的伺服器端的同步拓撲方面提供了很大的靈活性。 為了簡化管理，使伺服器終結點的路徑與 Windows 檔共用的路徑匹配。 

- 使用盡可能少的存儲同步服務。 當您具有包含多個伺服器終結點的同步組時，這將簡化管理，因為 Windows 伺服器一次只能註冊到一個存儲同步服務。 

- 在部署 Azure 檔共用時，注意存儲帳戶的 IOPS 限制。 理想情況下，您將檔共用 1：1 映射到存儲帳戶，但由於組織與 Azure 的各種限制和限制，這並不總是可能的。 如果不可能在一個存儲帳戶中只部署一個檔共用，請考慮哪些共用將高度活躍，哪些共用將不太活躍，以確保最熱門的檔共用不會放在同一個存儲帳戶中。

## <a name="windows-file-server-considerations"></a>Windows 檔案伺服器注意事項
要在 Windows 伺服器上啟用同步功能，必須安裝 Azure 檔同步可下載代理。 Azure 檔同步代理提供兩個主要元件：、`FileSyncSvc.exe`負責監視伺服器終結點上的更改和啟動同步會話的背景 Windows 服務，以及`StorageSync.sys`支援雲分層和快速災害復原的檔案系統篩選器。  

### <a name="operating-system-requirements"></a>作業系統需求
以下版本的 Windows 伺服器支援 Azure 檔同步：

| 版本 | 支援的 SKU | 支援的部署選項 |
|---------|----------------|------------------------------|
| Windows Server 2019 | 資料中心、標準和 IoT | 完整和核心 |
| Windows Server 2016 | 資料中心、標準和存儲伺服器 | 完整和核心 |
| Windows Server 2012 R2 | 資料中心、標準和存儲伺服器 | 完整和核心 |

Windows Server 的未來版本將會於發佈時加入支援清單。

> [!Important]  
> 建議您透過 Windows Update 的最新更新，將搭配 Azure 檔案同步使用的所有伺服器保持在最新狀態。 

### <a name="minimum-system-resources"></a>最少的系統資源
Azure 檔同步需要一台伺服器（物理伺服器或虛擬伺服器）至少具有一個 CPU 和至少 2 GiB 的記憶體。

> [!Important]  
> 如果伺服器在啟用動態記憶體的虛擬機器中運行，則應配置 VM 時至少具有 2048 MiB 的記憶體。

對於大多數生產工作負荷，我們不建議僅配置具有最低要求的 Azure 檔同步同步伺服器。 有關詳細資訊，請參閱[建議的系統資源](#recommended-system-resources)。

### <a name="recommended-system-resources"></a>推薦的系統資源
與任何伺服器功能或應用程式一樣，Azure 檔同步的系統資源要求由部署的規模決定;伺服器上的較大部署需要更大的系統資源。 對於 Azure 檔同步，縮放由伺服器終結點中的物件數和資料集上的改動決定。 單個伺服器可以在多個同步組中具有伺服器終結點，並且下表中列出的物件數用於伺服器附加到的完整命名空間。 

例如，伺服器終結點 A 具有 1000 萬個物件 = 伺服器終結點 B，其中 1000 萬個物件 = 2000 萬個物件。 對於該示例部署，我們建議使用 8 個 CPU，為穩定狀態建議記憶體 16 GiB，（如果可能）48 GiB 記憶體用於初始遷移。
 
出於性能原因，命名空間資料存儲在記憶體中。 因此，更大的命名空間需要更多的記憶體來保持良好的性能，而更多的改動需要更多的 CPU 來處理。 
 
在下表中，我們提供了命名空間的大小以及轉換為典型通用檔共用的容量，其中平均檔案大小為 512 KiB。 如果檔案大小較小，請考慮為相同容量添加額外記憶體。 基於命名空間的大小來配置記憶體。

| 命名空間大小 - 檔&目錄（百萬）  | 典型容量 （TiB）  | CPU 核心  | 推薦記憶體 （GiB） |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8（初始同步）/2（典型改動）      |
| 5        | 2.3     | 2        | 16 （初始同步）/ 4 （典型改動）    |
| 10       | 4.7     | 4        | 32 （初始同步）/ 8 （典型改動）   |
| 30       | 14.0    | 8        | 48 （初始同步）/ 16 （典型改動）   |
| 50       | 23.3    | 16       | 64 （初始同步）/ 32 （典型改動）  |
| 100*     | 46.6    | 32       | 128 （初始同步）/ 32 （典型改動）  |

\*目前不建議將超過 1 億個檔&目錄同步。 這是基於我們測試的閾值的軟限制。 有關詳細資訊，請參閱[Azure 檔可伸縮性和性能目標](storage-files-scale-targets.md#azure-file-sync-scale-targets)。

> [!TIP]
> 命名空間的初始同步是一項密集的操作，我們建議在初始同步完成之前分配更多記憶體。 這不是必需的，但可能會加快初始同步。 
> 
> 典型的改動是每天更改命名空間的 0.5%。 對於更高的改動級別，請考慮添加更多 CPU。 

- 本機連結的磁碟區會以 NTFS 檔案系統進行格式化。

### <a name="evaluation-cmdlet"></a>評估 Cmdlet
在部署 Azure 檔同步之前，應使用 Azure 檔同步評估 Cmdlet 評估它是否與系統相容。 此 Cmdlet 檢查檔案系統和資料集的潛在問題，例如不支援的字元或不支援的作業系統版本。 其檢查涵蓋以下提到的大多數（但不是所有功能）;我們建議您仔細閱讀本節的其餘部分，以確保部署順利進行。 

可通過安裝 Az PowerShell 模組來安裝評估 Cmdlet，該模組可通過此處的說明進行安裝：[安裝和配置 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps)。

#### <a name="usage"></a>使用量  
您可以使用幾個不同的方式來叫用評估工具：您可以執行系統檢查、資料集檢查，或兩者都執行。 若要執行系統和資料集的檢查： 

```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path>
```

若要只要測試資料集：
```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path> -SkipSystemChecks
```
 
若只要測試系統需求：
```powershell
Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name>
```
 
若要以 CSV 格式顯示結果：
```powershell
$errors = Invoke-AzStorageSyncCompatibilityCheck […]
$errors | Select-Object -Property Type, Path, Level, Description | Export-Csv -Path <csv path>
```

### <a name="file-system-compatibility"></a>檔案系統相容性
Azure 檔同步僅在直接附加的 NTFS 卷上受支援。 Windows 伺服器上的直接連接存儲或 DAS 表示 Windows Server 作業系統擁有檔案系統。 DAS 可以通過物理將磁片附加到檔案伺服器、將虛擬磁片附加到檔案伺服器 VM（例如由 Hyper-V 託管的 VM）甚至通過 ISCSI 提供。

僅支援 NTFS 卷;不支援 ReFS、FAT、FAT32 和其他檔案系統。

下表顯示了 NTFS 檔案系統的功能的交互操作狀態： 

| 功能 | 支援狀態 | 注意 |
|---------|----------------|-------|
| 存取控制清單 (ACL) | 完全支援 | Windows 風格的可自由存取控制清單由 Azure 檔同步保留，並由 Windows 伺服器在伺服器終結點上強制執行。 在直接安裝 Azure 檔共用時，也可以強制執行 ACL，但這需要額外的配置。 有關詳細資訊，請參閱[標識部分](#identity)。 |
| 永久連結 | 已略過 | |
| 符號連結 | 已略過 | |
| 掛接點 | 部分支援 | 掛接點可能是伺服器端點的根目錄，但如果伺服器端點的命名空間中包含它們，系統會予以略過。 |
| 接合 | 已略過 | 例如，分散式檔案系統 DfrsrPrivate 和 DFSRoots 資料夾。 |
| 重新分析點 | 已略過 | |
| NTFS 壓縮 | 完全支援 | |
| 疏鬆檔案 | 完全支援 | 疏鬆檔案會同步 (不會封鎖)，但它們會以完整檔案的形式同步至雲端。 如果檔案內容在雲端中 (或另一部伺服器上) 有所變更，該檔案在變更下載之後就不再是疏鬆檔案。 |
| 替代資料流 (ADS) | 已保留，但未同步 | 例如，不會同步「檔案分類基礎結構」所建立的分類標籤。 每個伺服器端點上檔案的現有分類標籤會原封不動。 |

<a id="files-skipped"></a>Azure 檔同步還將跳過某些暫存檔案和系統資料夾：

| 檔案/資料夾 | 附註 |
|-|-|
| pagefile.sys | 系統專用檔案 |
| Desktop.ini | 系統專用檔案 |
| thumbs.db | 暫存檔案的縮圖 |
| ehthumbs.db | 媒體縮略圖的暫存檔案 |
| ~$\*.\* | Office 暫存檔 |
| \*.tmp | 暫存檔 |
| \*.laccdb | Access DB 鎖定檔|
| 635D02A9D91C401B97884B82B3BCDAEA.* | 內部同步檔案|
| \\系統磁碟區資訊 | 特定磁碟區的資料夾 |
| $RECYCLE.BIN| 資料夾 |
| \\SyncShareState | 同步處理的資料夾 |

### <a name="failover-clustering"></a>容錯移轉叢集
Azure 檔案同步的 [一般用途的檔案伺服器] 部署選項支援 Windows Server 容錯移轉叢集。 「適用於應用程式資料的向外延展檔案伺服器」(SOFS) 或叢集共用磁碟區 (CSV) 上並不支援容錯移轉叢集。

> [!Note]  
> 必須在容錯移轉叢集中的每個節點上安裝 Azure 檔案同步代理程式，同步才能正確運作。

### <a name="data-deduplication"></a>重複資料刪除
**Windows 伺服器 2016 和 Windows 伺服器 2019**   
重複資料刪除會在 Windows Server 2016 和 Windows Server 2019 中，已啟用雲端階層處理的磁碟區上受到支援。 啟用啟用雲階層式卷上的資料重復資料消除功能可讓您在本機快取更多檔，而無需預配更多存儲。 

啟用雲階層式卷上啟用資料重復資料消除後，伺服器終結點位置內的 Dedup 優化檔將根據雲分層策略設置與普通檔進行分層。 Dedup 優化檔分層後，資料重復資料消除垃圾回收作業將自動運行，通過刪除卷上其他檔不再引用的不必要資料塊來回收磁碟空間。

請注意，節省的卷僅適用于伺服器;Azure 檔共用中的資料將不會被更新。

> [!Note]  
> 為了支援 Windows Server 2019 上啟用了雲階層式卷上的資料重復資料消除，必須安裝 Windows 更新[KB4520062，](https://support.microsoft.com/help/4520062)並且需要 Azure 檔同步代理版本 9.0.0.0 或更新。

**視窗伺服器 2012 R2**  
Azure 檔同步不支援 Windows Server 2012 R2 上同一卷上的資料重復資料消除和雲分層。 如果在卷上啟用了資料重復資料消除，則必須禁用雲分層。 

**注意**
- 如果在安裝 Azure 檔同步代理之前安裝了資料重復資料，則需要重新開機以支援同一卷上的資料重復資料消除和雲分層。
- 如果在啟用雲分層後在卷上啟用了資料重復資料消除，則初始重復資料消除優化作業將優化卷上尚未階層式檔，並將對雲分層產生以下影響：
    - 可用空間策略將繼續使用熱圖根據卷上的可用空間對檔進行分層。
    - 日期策略將跳過由於訪問檔的重復資料消除優化作業而可能以其他方式有資格階層式檔分層。
- 對於正在進行的重復資料消除優化作業，如果檔尚未分層，則使用日期策略的雲分層將被資料重復資料消除[最小檔日](https://docs.microsoft.com/powershell/module/deduplication/set-dedupvolume?view=win10-ps)設置延遲。 
    - 示例：如果"最小檔日"設置為 7 天，雲分層日期策略為 30 天，則日期策略將在 37 天后對檔進行分層。
    - 注意：一旦檔被 Azure 檔同步分層，重復資料消除優化作業將跳過該檔。
- 如果運行安裝了 Azure 檔同步代理的 Windows Server 2012 R2 伺服器升級到 Windows Server 2016 或 Windows Server 2019，則必須執行以下步驟以支援同一卷上的資料重復資料消除和雲分層：  
    - 卸載 Windows 伺服器 2012 R2 的 Azure 檔同步代理並重新啟動伺服器。
    - 下載新伺服器作業系統版本的 Azure 檔同步代理（Windows 伺服器 2016 或 Windows 伺服器 2019）。
    - 安裝 Azure 檔同步代理並重新啟動伺服器。  
    
    注意：卸載和重新安裝代理時，將保留伺服器上的 Azure 檔同步配置設置。

### <a name="distributed-file-system-dfs"></a>分散式檔案系統 (DFS)
Azure 檔案同步支援與 DFS 命名空間 (DFS-N) 和 DFS 複寫 (DFS-R) 互通。

**DFS 命名空間 (DFS-N)**：DFS-N 伺服器上完全支援 Azure 檔案同步。 您可以將 Azure 檔案同步代理程式安裝在一或多個 DFS-N 成員上，同步伺服器端點與雲端端點之間的資料。 如需詳細資訊，請參閱 [DFS 命名空間概觀](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview)。
 
**DFS 複製 （DFS-R）**： 由於 DFS-R 和 Azure 檔同步都是複製解決方案，在大多數情況下，我們建議使用 Azure 檔同步替換 DFS-R。但是，有幾個方案需要一起使用 DFS-R 和 Azure 檔同步：

- 您正要從 DFS-R 部署移轉至 Azure 檔案同步部署。 如需詳細資訊，請參閱[將 DFS 複寫 (DFS-R) 部署移轉至 Azure 檔案同步](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync)。
- 並非所有需要檔資料副本的本機伺服器都可以直接連接到互聯網。
- 分支伺服器將資料合併到您要使用 Azure 檔案同步的單一中樞伺服器。

對於 Azure 檔同步和 DFS-R 並排工作：

1. 務必在具有 DFS-R 複寫資料夾的磁碟區上停用 Azure 檔案同步雲端階層。
2. 不應在 DFS-R 唯讀複寫資料夾上設定伺服器端點。

如需詳細資訊，請參閱 [DFS 複寫概觀](https://technet.microsoft.com/library/jj127250)。

### <a name="sysprep"></a>Sysprep
不支援在安裝了 Azure 檔同步代理的伺服器上使用 sysprep，並可能導致意外的結果。 請在部署伺服器映像和完成 sysprep 迷你設定之後，再進行代理程式安裝與伺服器註冊。

### <a name="windows-search"></a>Windows 搜尋
如果在伺服器端點上啟用雲端階層處理，則系統會略過階層式檔案，且 Windows 搜尋不會將這些檔案編製索引。 非階層式檔案則會正確編製索引。

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>其他階層式存放裝置管理 (HSM) 解決方案
不應該搭配 Azure 檔案同步使用其他 HSM 解決方案。

## <a name="identity"></a>身分識別
Azure 檔同步適用于基於 AD 的標準標識，除了設置同步之外，沒有任何特殊設置。使用 Azure 檔同步時，通常期望大多數訪問都通過 Azure 檔同步快取服務器，而不是通過 Azure 檔共用。 由於伺服器終結點位於 Windows 伺服器上，並且 Windows 伺服器長期以來一直支援 AD 和 Windows 樣式 ACL，因此除了確保與存儲同步服務註冊的 Windows 檔案伺服器已加入域之外，不需要什麼。 Azure 檔同步將在 Azure 檔共用中的檔上存儲 ACL，並將它們複製到所有伺服器終結點。

即使直接對 Azure 檔共用所做的更改將需要更長的時間才能同步到同步組中的伺服器終結點，您可能還希望確保可以直接在雲中對檔共用強制執行 AD 許可權。 為此，必須域將存儲帳戶加入本地 AD，就像 Windows 檔案伺服器加入域的方式一樣。 要瞭解有關域將存儲帳戶加入客戶擁有的活動目錄的詳細資訊，請參閱[Azure 檔活動目錄概述](storage-files-active-directory-overview.md)。

> [!Important]  
> 成功部署 Azure 檔同步不需要將存儲帳戶加入活動目錄的域。這是一個嚴格可選的步驟，允許 Azure 檔共用在使用者直接裝載 Azure 檔共用時強制實施本地 ACL。

## <a name="networking"></a>網路
Azure 檔同步代理使用 Azure 檔同步 REST 協定和 FileREST 協定與存儲同步服務和 Azure 檔共用通信，這兩個協定始終在埠 443 上使用 HTTPS。 SMB 永遠不會用於在 Windows 伺服器和 Azure 檔共用之間上載或下載資料。 由於大多陣列織允許 HTTPS 流量通過埠 443，作為訪問大多數網站的要求，因此部署 Azure 檔同步通常不需要特殊的網路設定。

根據組織的策略或獨特的法規要求，您可能需要與 Azure 進行更嚴格的通信，因此 Azure 檔同步為配置網路提供了多種機制。 根據您的要求，您可以：

- 通過 ExpressRoute 或 Azure VPN 進行隧道同步和檔上傳/下載流量。 
- 使用 Azure 檔和 Azure 網路功能（如服務終結點和專用終結點）。
- 配置 Azure 檔同步以支援環境中的代理。
- 從 Azure 檔同步限制網路活動。

要瞭解有關配置 Azure 檔同步的網路功能的更多內容，請參閱：
- [Azure 檔案同步 Proxy 和防火牆設定](storage-sync-files-firewall-and-proxy.md)
- [確認 Azure 檔案同步對於您的資料中心不會有不良影響](storage-sync-files-server-registration.md)

## <a name="encryption"></a>加密
使用 Azure 檔同步時，需要考慮三個不同的加密層：Windows 伺服器靜態存儲上的加密、Azure 檔同步代理和 Azure 之間傳輸的加密以及 Azure 檔共用中其餘資料的加密。 

### <a name="windows-server-encryption-at-rest"></a>靜態 Windows 伺服器加密 
在 Windows 伺服器上加密資料的策略通常適用于 Azure 檔同步：檔案系統下方的加密，以便檔案系統和寫入該檔的所有資料都加密，以及檔案格式本身中的加密。 這些方法不是互斥的;因此，這些方法並不相互排斥。如果需要，可以一起使用，因為加密的目的不同。

為了在檔案系統下提供加密，Windows 伺服器提供 BitLocker 收件匣。 BitLocker 對 Azure 檔同步是完全透明的。使用 BitLocker 等加密機制的主要原因是，防止有人竊取磁片從本地資料中心物理exby資料，並防止側載入未經授權的作業系統以執行未經授權的讀取/寫入資料。 要瞭解有關 BitLocker 的詳細資訊，請參閱[BitLocker 概述](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview)。

與 BitLocker 類似的協力廠商產品，因為它們位於 NTFS 卷下方，因此同樣應該與 Azure 檔同步完全透明地工作。 

加密資料的另一個主要方法是在應用程式保存檔時加密檔的資料流程。 某些應用程式可能本機執行此操作，但通常並非如此。 加密檔資料流程的方法是 Azure 資訊保護 （AIP）/Azure 版權管理服務 （Azure RMS）/活動目錄 RMS。 使用 AIP/RMS 等加密機制的主要原因是，使用者將資料從檔共用中的資料從檔共用中複製到備用位置（如快閃記憶體磁碟機）或通過電子郵件發送給未經授權的人員。" 當檔的資料流程作為檔案格式的一部分進行加密時，此檔將繼續在 Azure 檔共用上加密。 

Azure 檔同步不與 NTFS 加密檔案系統 （NTFS EFS） 或協力廠商加密解決方案交互操作，這些解決方案在檔案系統上方，但低於檔的資料流程。 

### <a name="encryption-in-transit"></a>傳輸中加密
Azure 檔同步代理使用 Azure 檔同步 REST 協定和 FileREST 協定與存儲同步服務和 Azure 檔共用通信，這兩個協定始終在埠 443 上使用 HTTPS。 Azure 檔同步不會通過 HTTP 發送未加密的請求。 

Azure 存儲帳戶包含一個開關，用於在傳輸中需要加密，預設情況下啟用該交換器。 即使禁用存儲帳戶級別的交換器，這意味著與 Azure 檔共用的未加密連接也是可能的，Azure 檔同步仍將僅使用加密通道訪問檔共用。

禁用存儲帳戶在傳輸中的加密的主要原因是支援必須在較舊的作業系統（如 Windows Server 2008 R2 或較舊的 Linux 發行版本）上運行的舊應用程式，直接與 Azure 檔共用對話。 如果舊應用程式與檔共用的 Windows Server 緩存進行切換，則切換此設置將不起作用。 

我們強烈建議確保啟用傳輸中資料的加密。

如需傳輸中加密的詳細資訊，請參閱[在 Azure 儲存體中需要安全傳輸](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

### <a name="azure-file-share-encryption-at-rest"></a>靜態 Azure 檔共用加密
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>儲存層
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>使標準檔共用跨越多達 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>區域可用性
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="azure-file-sync-region-availability"></a>Azure 檔同步區域可用性
Azure 檔同步在以下區域可用：

| Azure 雲端 | 地理區域 | Azure 區域 | 區功能變數代碼 |
|-------------|-------------------|--------------|-------------|
| 公開 | Asia | 東亞 | `eastasia` |
| 公開 | Asia | 東南亞 | `southeastasia` |
| 公開 | 澳大利亞 | 澳大利亞東部 | `australiaeast` |
| 公開 | 澳大利亞 | 澳大利亞東南部 | `australiasoutheast` |
| 公開 | 巴西 | 巴西南部 | `brazilsouth` |
| 公開 | Canada | 加拿大中部 | `canadacentral` |
| 公開 | Canada | 加拿大東部 | `canadaeast` |
| 公開 | 歐洲 | 北歐 | `northeurope` |
| 公開 | 歐洲 | 西歐 | `westeurope` |
| 公開 | 法國 | 法國中部 | `francecentral` |
| 公開 | 法國 | 法國南部* | `francesouth` |
| 公開 | 印度 | 印度中部 | `centralindia` |
| 公開 | 印度 | 印度南部 | `southindia` |
| 公開 | 日本 | 日本東部 | `japaneast` |
| 公開 | 日本 | 日本西部 | `japanwest` |
| 公開 | 南韓 | 南韓中部 | `koreacentral` |
| 公開 | 南韓 | 南韓南部 | `koreasouth` |
| 公開 | 南非 | 南非北部 | `southafricanorth` |
| 公開 | 南非 | 南非西部* | `southafricawest` |
| 公開 | 阿拉伯聯合大公國 | 阿聯酋中部* | `uaecentral` |
| 公開 | 阿拉伯聯合大公國 | 阿拉伯聯合大公國北部 | `uaenorth` |
| 公開 | 英國 | 英國南部 | `uksouth` |
| 公開 | 英國 | 英國西部 | `ukwest` |
| 公開 | US | 美國中部 | `centralus` |
| 公開 | US | 美國東部 | `eastus` |
| 公開 | US | 美國東部 2 | `eastus2` |
| 公開 | US | 美國中北部 | `northcentralus` |
| 公開 | US | 美國中南部 | `southcentralus` |
| 公開 | US | 美國中西部 | `westcentralus` |
| 公開 | US | 美國西部 | `westus` |
| 公開 | US | 美國西部 2 | `westus2` |
| US Gov | US | US Gov 亞利桑那州 | `usgovarizona` |
| US Gov | US | US Gov 德克薩斯州 | `usgovtexas` |
| US Gov | US | US Gov 維吉尼亞州 | `usgovvirginia` |

Azure 檔案同步僅支援與位於和儲存體同步服務相同之區域中的 Azure 檔案共用進行同步。

對於標有星號的區域，必須聯繫 Azure 支援以請求訪問這些區域中的 Azure 存儲。 [本文檔](https://azure.microsoft.com/global-infrastructure/geographies/)概述了該過程。

## <a name="redundancy"></a>備援性
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

> [!Important]  
> 地理冗余和地理區域冗余存儲能夠手動容錯移轉存儲到次要區域。 我們建議您在使用 Azure 檔同步時不要在災難之外執行此操作，因為資料丟失的可能性增加。 如果發生災難，希望啟動存儲的手動容錯移轉，則需要與 Microsoft 一起打開支援案例，以便 Azure 檔同步恢復與輔助終結點同步。

## <a name="migration"></a>移轉
如果您有現有的 Windows 檔案伺服器，則可以直接安裝 Azure 檔同步，而無需將資料移動到新伺服器。 如果計畫遷移到新的 Windows 檔案伺服器作為採用 Azure 檔同步的一部分，則有幾種方法可以將資料移過：

- 為舊檔共用和新檔共用創建伺服器終結點，並讓 Azure 檔同步伺服器終結點之間的資料。 此方法的優點是，它使得在新檔案伺服器上超額訂閱存儲變得非常容易，因為 Azure 檔同步是雲分層感知的。 準備就緒後，可以將最終使用者剪切到新伺服器上的檔共用，並刪除舊檔共用的伺服器終結點。

- 僅在新檔案伺服器上創建伺服器終結點，並使用 從舊檔共用將資料複製到`robocopy`中。 根據新伺服器上的檔共用的拓撲（每個卷上有多少共用、每個卷的可用程度等），您可能需要臨時預配其他存儲，`robocopy`因為從舊伺服器到本地資料中心內的新伺服器完成的速度將比 Azure 檔同步將資料移動到 Azure 的速度要快。

還可以使用資料框將資料移轉到 Azure 檔同步部署中。 大多數情況下，當客戶希望使用資料框來引入資料時，他們這樣做是因為他們認為這將加快部署速度，或者因為它有助於解決受限頻寬方案。 雖然使用資料框將資料引入 Azure 檔同步部署會降低頻寬利用率，但大多數方案通過上述方法之一進行連線資料上載可能會更快。 要瞭解有關如何使用資料框將資料引入 Azure 檔同步部署中，請參閱[將資料移轉到 Azure 檔同步與 Azure 資料框](storage-sync-offline-data-transfer.md)。

客戶在將資料移轉到其新的 Azure 檔同步部署時犯的常見錯誤是將資料直接複製到 Azure 檔共用中，而不是在其 Windows 檔案伺服器上。 儘管 Azure 檔同步將標識 Azure 檔共用上的所有新檔，並將它們同步回 Windows 檔共用，但這通常比通過 Windows 檔案伺服器載入資料要慢得多。 許多 Azure 複製工具（如 AzCopy）具有不復制檔的所有重要中繼資料（如時間戳記和 ACL）的附加缺點。

## <a name="antivirus"></a>防毒
因為防毒程式的運作方式是掃描檔案中的已知惡意程式碼，所以防毒產品可能會導致階層式檔案的重新叫用。 在 4.0 版和更新版本的 Azure 檔案同步代理程式中，階層式檔案已設定安全的 Windows 屬性 FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS。 建議您洽詢您的軟體廠商，以了解如何設定其解決方案來略過讀取已設定此屬性的檔案 (很多軟體會自動這麼做)。 

作為 Microsoft 內部防毒解決方案的 Windows Defender 和 System Center Endpoint Protection (SCEP)，皆會自動略過讀取已設定此屬性的檔案。 我們已經測試這兩個解決方案並找到一個小問題：當您將伺服器新增至現有同步群組時，會在新的伺服器上重新叫用 (下載) 小於 800 個位元組的檔案。 這些檔案會保留在新的伺服器上，而且不會分層，因為這些檔案不符合階層處理大小需求 (> 64 kb)。

> [!Note]  
> 防病毒供應商可以使用[Azure 檔同步防病毒相容性測試套件](https://www.microsoft.com/download/details.aspx?id=58322)（可在 Microsoft 下載中心下載）檢查其產品與 Azure 檔同步之間的相容性。

## <a name="backup"></a>Backup  
備份解決方案類似防毒解決方案，可能會導致階層式檔案的重新叫用。 建議使用雲端備份解決方案來備份 Azure 檔案共用，而不要使用內部部署備份產品。

如果使用本地備份解決方案，則應在已禁用雲階層式同步組中的伺服器上執行備份。 執行還原時，請使用磁碟區層級或檔案層級的還原選項。 使用檔案層級還原選項進行還原的檔案會同步至同步群組中的所有端點，並使用從備份還原過來的版本取代現有檔案。  磁碟區層級還原將不會取代 Azure 檔案共用或其他伺服器端點中的較新檔案版本。

> [!Note]  
> 裸機 (BMR) 還原可能會導致非預期的結果，且目前不受支援。

> [!Note]  
> 使用 Azure 檔同步代理的版本 9，現在啟用了雲階層式卷上支援 VSS 快照（包括早期版本選項卡）。 但是，您必須通過 PowerShell 啟用以前的版本相容性。 [了解作法](storage-files-deployment-guide.md)。

## <a name="azure-file-sync-agent-update-policy"></a>Azure 檔案同步代理程式更新原則
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>後續步驟
* [考量防火牆和 Proxy 設定](storage-sync-files-firewall-and-proxy.md)
* [規劃 Azure 檔案服務部署](storage-files-planning.md)
* [部署 Azure 檔案服務](storage-files-deployment-guide.md)
* [部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)
* [監視 Azure 檔案同步](storage-sync-files-monitoring.md)