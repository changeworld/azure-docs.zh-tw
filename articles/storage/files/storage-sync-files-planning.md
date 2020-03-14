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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79255115"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>規劃 Azure 檔案同步部署
[Azure 檔案儲存體](storage-files-introduction.md)可以透過兩種主要方式來部署：直接裝載無伺服器 Azure 檔案共用，或使用 Azure 檔案同步快取內部部署的 Azure 檔案共用。您所選擇的部署選項會變更規劃部署時所需考慮的事項。 

- **直接掛接 Azure 檔案共用**：因為 Azure 檔案儲存體會提供 SMB 存取，所以您可以使用 Windows、MacOS 和 Linux 中提供的標準 SMB 用戶端，在內部部署或雲端中掛接 azure 檔案共用。 由於 Azure 檔案共用是無伺服器的，因此針對生產案例進行部署並不需要管理檔案伺服器或 NAS 裝置。 這表示您不需要套用軟體修補程式或交換實體磁片。 

- **使用 Azure 檔案同步**來快取內部部署的 Azure 檔案共用： Azure 檔案同步可讓您將組織的檔案共用集中在 Azure 檔案儲存體，同時保有內部部署檔案伺服器的彈性、效能及相容性。 Azure 檔案同步將內部部署（或雲端） Windows Server 轉換成 Azure 檔案共用的快速快取。 

本文主要討論部署 Azure 檔案同步的部署考慮。若要規劃由內部部署或雲端用戶端直接裝載的 Azure 檔案共用部署，請參閱[規劃 Azure 檔案儲存體部署](storage-files-planning.md)。

## <a name="management-concepts"></a>管理概念
Azure 檔案同步部署有三個基本管理物件：

- **Azure 檔案共用**： azure 檔案共用是無伺服器雲端檔案共用，可提供 Azure 檔案同步同步關係的*雲端端點*。 Azure 檔案共用中的檔案可以直接使用 SMB 或 FileREST 通訊協定來存取，雖然我們建議您在搭配 Azure 檔案同步使用 Azure 檔案共用時，透過 Windows Server 快取來存取檔案。這是因為 Azure 檔案儲存體今天缺少 Windows Server 這類有效率的變更偵測機制，因此直接對 Azure 檔案共用進行變更將需要一段時間才能傳播回伺服器端點。
- **伺服器端點**： Windows 伺服器上正在同步處理至 Azure 檔案共用的路徑。 這可以是磁片區上的特定資料夾或磁片區的根目錄。 如果多個伺服器端點的命名空間沒有重迭，就可以存在於相同的磁片區上。
- **同步群組**：定義**雲端端點**、Azure 檔案共用和伺服器端點之間同步關係的物件。 同步群組內的端點會與彼此保持同步。 例如，如果您想要使用 Azure 檔案同步來管理兩組不同的檔案，您會建立兩個同步群組，並將不同的端點新增至每個同步處理群組。

### <a name="azure-file-share-management-concepts"></a>Azure 檔案共用管理概念
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

### <a name="azure-file-sync-management-concepts"></a>Azure 檔案同步管理概念
同步群組會部署至**儲存體同步服務**，這是用來註冊伺服器以與 Azure 檔案同步並包含同步群組關聯性的最上層物件。 儲存體同步服務資源是儲存體帳戶資源的對等，同樣可以部署到 Azure 資源群組中。 儲存體同步服務可以在多個儲存體帳戶和多個已註冊的 Windows 伺服器上，建立包含 Azure 檔案共用的同步群組。

您必須先向儲存體同步服務註冊 Windows Server，才能在儲存體同步服務中建立同步處理群組。 這會建立一個**已註冊的伺服器**物件，代表您的伺服器或叢集與儲存體同步服務之間的信任關係。 若要註冊儲存體同步服務，您必須先在伺服器上安裝 Azure 檔案同步代理程式。 個別的伺服器或叢集一次只能註冊一個儲存體同步服務。

同步群組包含一個雲端端點、Azure 檔案共用，以及至少一個伺服器端點。 伺服器端點物件包含設定**雲端分層**功能的設定，其提供 Azure 檔案同步的快取功能。為了與 Azure 檔案共用同步，包含 Azure 檔案共用的儲存體帳戶必須位於與儲存體同步服務相同的 Azure 區域中。

### <a name="management-guidance"></a>管理指導方針
部署 Azure 檔案同步時，我們建議：

- 使用 Windows 檔案共用部署 Azure 檔案共用1:1。 伺服器端點物件可讓您在同步處理關聯性的伺服器端上設定同步拓朴的方式有很大的彈性。 若要簡化管理，請讓伺服器端點的路徑符合 Windows 檔案共用的路徑。 

- 盡可能使用最少的儲存體同步服務。 當您有包含多個伺服器端點的同步群組時，這將可簡化管理，因為一次只能將一部 Windows 伺服器註冊到一個儲存體同步服務。 

- 部署 Azure 檔案共用時，請注意儲存體帳戶的 IOPS 限制。 在理想的情況下，您會使用儲存體帳戶對應檔案共用1:1，不過，這可能不一定是因為您的組織和 Azure 有各種限制和限制。 當一個儲存體帳戶中不可能只部署一個檔案共用時，請考慮哪些共用會高度使用，哪些共用會較不活躍，以確保最熱門的檔案共用不會放在同一個儲存體帳戶中。

## <a name="windows-file-server-considerations"></a>Windows 檔案伺服器考慮
若要在 Windows Server 上啟用同步處理功能，您必須安裝 Azure 檔案同步可下載的代理程式。 Azure 檔案同步代理程式提供兩個主要元件： `FileSyncSvc.exe`、負責監視伺服器端點上之變更並起始同步會話的背景 Windows 服務，以及 `StorageSync.sys`，這是可啟用雲端階層處理和快速嚴重損壞修復的檔案系統篩選器。  

### <a name="operating-system-requirements"></a>作業系統需求
下列 Windows Server 版本支援 Azure 檔案同步：

| 版本 | 支援的 SKU | 支援的部署選項 |
|---------|----------------|------------------------------|
| Windows Server 2019 | Datacenter、Standard 和 IoT | 完整和核心 |
| Windows Server 2016 | Datacenter、Standard 和 Storage Server | 完整和核心 |
| Windows Server 2012 R2 | Datacenter、Standard 和 Storage Server | 完整和核心 |

Windows Server 的未來版本將會於發佈時加入支援清單。

> [!Important]  
> 建議您透過 Windows Update 的最新更新，將搭配 Azure 檔案同步使用的所有伺服器保持在最新狀態。 

### <a name="minimum-system-resources"></a>最低系統資源
Azure 檔案同步需要伺服器（實體或虛擬），而且至少有一個 CPU 和 2 GiB 的記憶體。

> [!Important]  
> 如果伺服器是在啟用動態記憶體的虛擬機器中執行，則 VM 應設定為至少使用 2048 MiB 的記憶體。

對於大部分的生產工作負載，我們不建議您設定僅具有最低需求的 Azure 檔案同步同步伺服器。 如需詳細資訊，請參閱[建議的系統資源](#recommended-system-resources)。

### <a name="recommended-system-resources"></a>建議的系統資源
就像任何伺服器功能或應用程式一樣，Azure 檔案同步的系統資源需求取決於部署的規模;伺服器上較大型的部署需要更多的系統資源。 針對 Azure 檔案同步，小數位數是由伺服器端點上的物件數目和資料集上的變換所決定。 單一伺服器在多個同步處理群組中可以有伺服器端點，而在下表中列出的物件數目則適用于伺服器所附加的完整命名空間。 

例如，具有10000000物件的伺服器端點 A + 具有10000000物件的伺服器端點 B = 20000000 物件。 針對該範例部署，我們會建議8個 Cpu、16 GiB 的記憶體以提供穩定的狀態，以及（如果可能） 48 GiB 的記憶體以供初始遷移。
 
基於效能考慮，命名空間資料會儲存在記憶體中。 因此，較大的命名空間會需要更多記憶體來維持良好的效能，而更多變換則需要更多的 CPU 才能處理。 
 
在下表中，我們提供了命名空間的大小以及一般一般用途檔案共用的容量轉換，其中平均檔案大小為 512 KiB。 如果您的檔案大小較小，請考慮為相同容量增加額外的記憶體。 根據命名空間的大小，以您的記憶體設定為基礎。

| 命名空間大小-檔案 & 目錄（百萬）  | 一般容量（TiB）  | CPU 核心  | 建議的記憶體（GiB） |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8（初始同步處理）/2 （一般變換）      |
| 5        | 2.3     | 2        | 16（初始同步）/4 （一般變換）    |
| 10       | 4.7     | 4        | 32（初始同步處理）/8 （一般變換）   |
| 30       | 14.0    | 8        | 48（初始同步處理）/16 （一般變換）   |
| 50       | 23.3    | 16       | 64（初始同步處理）/32 （一般變換）  |
| 100*     | 46.6    | 32       | 128（初始同步處理）/32 （一般變換）  |

目前不建議 \*同步處理超過100000000個檔案 & 目錄。 這是以我們測試的臨界值為基礎的軟限制。 如需詳細資訊，請參閱[Azure 檔案儲存體擴充性和效能目標](storage-files-scale-targets.md#azure-file-sync-scale-targets)。

> [!TIP]
> 命名空間的初始同步處理是一項密集的作業，我們建議您在完成初始同步處理之前，配置更多記憶體。 這不是必要的，但可能會加速初始同步處理。 
> 
> 一般變換是每日變更的0.5% 命名空間。 如需更高的變換層級，請考慮新增更多 CPU。 

- 本機連結的磁碟區會以 NTFS 檔案系統進行格式化。

### <a name="evaluation-cmdlet"></a>評估 Cmdlet
部署 Azure 檔案同步之前，您應該先使用 Azure 檔案同步評估 Cmdlet 來評估它是否與您的系統相容。 此 Cmdlet 會檢查檔案系統和資料集的潛在問題，例如不支援的字元或不支援的作業系統版本。 其檢查涵蓋了下列大部分但並非全部的功能;我們建議您仔細閱讀本節的其餘部分，以確保您的部署順利進行。 

您可以藉由安裝 Az PowerShell module 來安裝評估 Cmdlet，此模組可依照以下指示安裝：[安裝和設定 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps)。

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
只有直接附加的 NTFS 磁片區才支援 Azure 檔案同步。 Windows Server 上直接連接的存放裝置（或 DAS）表示 Windows Server 作業系統擁有檔案系統。 您可以透過將磁片實際連接到檔案伺服器、將虛擬磁片連接至檔案伺服器 VM （例如 Hyper-v 所裝載的 VM），或甚至透過 ISCSI 來提供 DAS。

僅支援 NTFS 磁片區;不支援 ReFS、FAT、FAT32 及其他檔案系統。

下表顯示 NTFS 檔案系統功能的 interop 狀態： 

| 功能 | 支援狀態 | 注意 |
|---------|----------------|-------|
| 存取控制清單 (ACL) | 完全支援 | Windows 樣式任意存取控制清單會由 Azure 檔案同步保留，並由 Windows Server 在伺服器端點上強制執行。 當直接裝載 Azure 檔案共用時，也可以強制執行 Acl，不過這需要額外的設定。 如需詳細資訊，請參閱身分[識別一節](#identity)。 |
| 永久連結 | 已略過 | |
| 符號連結 | 已略過 | |
| 掛接點 | 部分支援 | 掛接點可能是伺服器端點的根目錄，但如果伺服器端點的命名空間中包含它們，系統會予以略過。 |
| 接合 | 已略過 | 例如，分散式檔案系統 DfrsrPrivate 和 DFSRoots 資料夾。 |
| 重新分析點 | 已略過 | |
| NTFS 壓縮 | 完全支援 | |
| 疏鬆檔案 | 完全支援 | 疏鬆檔案會同步 (不會封鎖)，但它們會以完整檔案的形式同步至雲端。 如果檔案內容在雲端中 (或另一部伺服器上) 有所變更，該檔案在變更下載之後就不再是疏鬆檔案。 |
| 替代資料流 (ADS) | 已保留，但未同步 | 例如，不會同步「檔案分類基礎結構」所建立的分類標籤。 每個伺服器端點上檔案的現有分類標籤會原封不動。 |

<a id="files-skipped"></a>Azure 檔案同步也會略過特定的暫存檔案和系統資料夾：

| 檔案/資料夾 | 附註 |
|-|-|
| pagefile.sys | 系統專用檔案 |
| Desktop.ini | 系統專用檔案 |
| thumbs.db | 暫存檔案的縮圖 |
| ehthumbs.db | 媒體縮圖的暫存檔案 |
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
**Windows server 2016 和 Windows server 2019**   
重複資料刪除會在 Windows Server 2016 和 Windows Server 2019 中，已啟用雲端階層處理的磁碟區上受到支援。 在啟用雲端階層處理的磁片區上啟用重復資料刪除，可讓您在內部部署快取更多檔案，而不需要布建更多 

在啟用雲端階層處理的磁片區上啟用重復資料刪除時，伺服器端點位置中的重復資料刪除優化檔案將會根據雲端階層處理原則設定，與一般檔案分層。 當重復資料刪除優化檔案已分層之後，重復資料刪除垃圾收集工作將會自動執行，藉由移除磁片區上其他檔案不再參考的不必要區塊來回收磁碟空間。

請注意，磁片區節省費用僅適用于伺服器;您在 Azure 檔案共用中的資料將不會重復資料刪除。

> [!Note]  
> 為了支援在 Windows Server 2019 上啟用雲端階層處理的磁片區上進行重復資料刪除，必須安裝 Windows update [KB4520062](https://support.microsoft.com/help/4520062) ，而且需要 Azure 檔案同步代理程式版本9.0.0.0 或更新版本。

**Windows Server 2012 R2**  
Azure 檔案同步不支援在 Windows Server 2012 R2 的相同磁片區上進行重復資料刪除和雲端階層處理。 如果已在磁片區上啟用重復資料刪除，則必須停用雲端階層處理。 

**注意**
- 如果在安裝 Azure 檔案同步代理程式之前已安裝重復資料刪除，則需要重新開機，以支援相同磁片區上的重復資料刪除和雲端階層處理。
- 如果在啟用雲端階層處理之後，磁片區上啟用重復資料刪除功能，初始重復資料刪除優化工作將會優化尚未階層式磁片區上的檔案，而且會對雲端階層處理產生下列影響：
    - [可用空間] 原則會根據磁片區上的可用空間，使用熱度圖繼續將檔案分層。
    - 日期原則會略過可能因為存取檔案的重復資料刪除優化作業而有資格進行階層式檔案分層。
- 針對進行中的重復資料刪除優化作業，如果檔案尚未分層，則 [重復資料刪除] [MinimumFileAgeDays](https://docs.microsoft.com/powershell/module/deduplication/set-dedupvolume?view=win10-ps)設定會延遲具有日期原則的雲端階層處理。 
    - 範例：如果 MinimumFileAgeDays 設定為7天，而雲端階層處理日期原則為30天，則日期原則會在37天后將檔案分層。
    - 注意：一旦以 Azure 檔案同步將檔案分層之後，重復資料刪除優化工作將會略過該檔案。
- 如果執行 Windows Server 2012 R2 並已安裝 Azure 檔案同步代理程式的伺服器已升級為 Windows Server 2016 或 Windows Server 2019，則必須執行下列步驟，以支援相同磁片區上的重復資料刪除和雲端階層處理：  
    - 卸載 Windows Server 2012 R2 的 Azure 檔案同步代理程式，然後重新開機伺服器。
    - 下載新伺服器作業系統版本的 Azure 檔案同步代理程式（Windows Server 2016 或 Windows Server 2019）。
    - 安裝 Azure 檔案同步代理程式並重新啟動伺服器。  
    
    注意：卸載並重新安裝代理程式時，會保留伺服器上的 Azure 檔案同步設定。

### <a name="distributed-file-system-dfs"></a>分散式檔案系統 (DFS)
Azure 檔案同步支援與 DFS 命名空間 (DFS-N) 和 DFS 複寫 (DFS-R) 互通。

**DFS 命名空間 (DFS-N)** ：DFS-N 伺服器上完全支援 Azure 檔案同步。 您可以將 Azure 檔案同步代理程式安裝在一或多個 DFS-N 成員上，同步伺服器端點與雲端端點之間的資料。 如需詳細資訊，請參閱 [DFS 命名空間概觀](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview)。
 
**DFS 複寫（DFS）** ：由於 dfs 和 Azure 檔案同步都是複寫解決方案，因此在大部分情況下，我們建議您使用 Azure 檔案同步來取代 dfs。不過，在某些情況下，您會想要同時使用 Azure 檔案同步的 DFS 和：

- 您正要從 DFS-R 部署移轉至 Azure 檔案同步部署。 如需詳細資訊，請參閱[將 DFS 複寫 (DFS-R) 部署移轉至 Azure 檔案同步](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync)。
- 並非每個需要檔案資料複本的內部部署伺服器都可以直接連線到網際網路。
- 分支伺服器將資料合併到您要使用 Azure 檔案同步的單一中樞伺服器。

若要讓 Azure 檔案同步和 DFS-R 並存工作：

1. 務必在具有 DFS-R 複寫資料夾的磁碟區上停用 Azure 檔案同步雲端階層。
2. 不應在 DFS-R 唯讀複寫資料夾上設定伺服器端點。

如需詳細資訊，請參閱 [DFS 複寫概觀](https://technet.microsoft.com/library/jj127250)。

### <a name="sysprep"></a>Sysprep
不支援在已安裝 Azure 檔案同步代理程式的伺服器上使用 sysprep，而且可能會導致非預期的結果。 請在部署伺服器映像和完成 sysprep 迷你設定之後，再進行代理程式安裝與伺服器註冊。

### <a name="windows-search"></a>Windows 搜尋
如果在伺服器端點上啟用雲端階層處理，則系統會略過階層式檔案，且 Windows 搜尋不會將這些檔案編製索引。 非階層式檔案則會正確編製索引。

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>其他階層式存放裝置管理 (HSM) 解決方案
不應該搭配 Azure 檔案同步使用其他 HSM 解決方案。

## <a name="identity"></a>身分識別
Azure 檔案同步與您的標準 AD 型身分識別搭配使用，而不需要任何特殊設定即可設定同步。當您使用 Azure 檔案同步時，一般會預期大部分的存取都會通過 Azure 檔案同步快取伺服器，而不是透過 Azure 檔案共用。 由於伺服器端點位於 Windows Server 上，而 Windows Server 有很長一段時間支援 AD 和 Windows 樣式的 Acl，因此除了確保向儲存體同步服務註冊的 Windows 檔案伺服器已加入網域之外，不需要任何東西。 Azure 檔案同步會將 Acl 儲存在 Azure 檔案共用中的檔案，並將其複寫至所有伺服器端點。

雖然直接對 Azure 檔案共用所做的變更需要較長的時間才能同步到同步群組中的伺服器端點，但您也可能想要確保您也可以在雲端直接對您的檔案共用強制執行 AD 許可權。 若要這樣做，您必須將儲存體帳戶加入至您的內部部署 AD，就像 Windows 檔案伺服器加入網域的方式一樣。 若要深入瞭解將您的儲存體帳戶加入至客戶擁有之 Active Directory 的網域，請參閱[Azure 檔案儲存體 Active Directory 總覽](storage-files-active-directory-overview.md)。

> [!Important]  
> 將您的儲存體帳戶加入至 Active Directory 的網域，不是成功部署 Azure 檔案同步的必要條件。這是一個嚴格的選擇性步驟，可讓 Azure 檔案共用在使用者直接掛接 Azure 檔案共用時，強制執行內部部署 Acl。

## <a name="networking"></a>網路功能
Azure 檔案同步代理程式會使用 Azure 檔案同步 REST 通訊協定和 FileREST 通訊協定來與您的儲存體同步服務和 Azure 檔案共用進行通訊，這兩者一律會使用 HTTPS over 埠443。 SMB 永遠不會用來在您的 Windows Server 和 Azure 檔案共用之間上傳或下載資料。 由於大部分的組織允許透過埠443的 HTTPS 流量，因此若要造訪大部分的網站，通常不需要特殊的網路設定就能部署 Azure 檔案同步。

根據貴組織的原則或獨特的法規需求，您可能需要與 Azure 進行更嚴格的通訊，因此 Azure 檔案同步會提供數種機制來設定網路功能。 根據您的需求，您可以：

- 透過 ExpressRoute 或 Azure VPN 的通道同步處理和檔案上傳/下載流量。 
- 利用 Azure 檔案儲存體和 Azure 網路功能，例如服務端點和私人端點。
- 設定 Azure 檔案同步，以在您的環境中支援您的 proxy。
- 節流 Azure 檔案同步的網路活動。

若要深入瞭解如何設定 Azure 檔案同步的網路功能，請參閱：
- [Azure 檔案同步 Proxy 和防火牆設定](storage-sync-files-firewall-and-proxy.md)
- [確保 Azure 檔案同步是資料中心的良好鄰近處](storage-sync-files-server-registration.md)

## <a name="encryption"></a>加密
使用 Azure 檔案同步時，需要考慮三個不同層級的加密：在 Windows Server 的待用儲存體上進行加密、在 Azure 檔案同步代理程式與 Azure 之間傳輸加密，以及在 Azure 檔案共用中待用資料的加密。 

### <a name="windows-server-encryption-at-rest"></a>待用的 Windows Server 加密 
有兩種策略可在 Windows Server 上以 Azure 檔案同步的方式進行資料加密：檔案系統底下的加密，因此檔案系統及其寫入的所有資料都會經過加密，並在檔案格式本身內加密。 這些方法不是互斥的;如有需要，您可以將它們搭配使用，因為加密的目的不同。

為了在檔案系統底下提供加密，Windows Server 提供 BitLocker 收件匣。 BitLocker 對於 Azure 檔案同步而言完全透明。使用加密機制（例如 BitLocker）的主要原因，是為了避免有人竊取磁片，並防止在未授權的 OS 中執行未經授權的讀取/寫入，讓資料從內部部署資料中心進行實體外泄。 若要深入瞭解 BitLocker，請參閱[bitlocker 總覽](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview)。

與 BitLocker 類似的協力廠商產品，在其位於 NTFS 磁片區之下，也同樣能以完全透明的方式與 Azure 檔案同步搭配使用。 

另一個用來加密資料的主要方法，就是在應用程式儲存檔案時，加密檔案的資料流程。 某些應用程式可能會以原生方式執行此動作，但通常不會發生這種情況。 加密檔案資料流程的方法範例是 Azure 資訊保護（AIP）/Azure Rights Management Services （Azure RMS）/Active Directory RMS。 使用加密機制（例如 AIP/RMS）的主要原因，是要防止使用者將資料從檔案共用複製到其他位置（例如快閃磁片磁碟機），或透過電子郵件傳送給未經授權的人員。 當檔案的資料流程已加密為檔案格式的一部分時，此檔案會繼續在 Azure 檔案共用上加密。 

Azure 檔案同步無法與位於檔案系統上但檔案資料流程底下的 NTFS 加密檔案系統（NTFS EFS）或協力廠商加密解決方案相交互操作。 

### <a name="encryption-in-transit"></a>傳輸中加密
Azure 檔案同步代理程式會使用 Azure 檔案同步 REST 通訊協定和 FileREST 通訊協定來與您的儲存體同步服務和 Azure 檔案共用進行通訊，這兩者一律會使用 HTTPS over 埠443。 Azure 檔案同步不會透過 HTTP 傳送未加密的要求。 

Azure 儲存體帳戶包含一個交換器，用於要求傳輸中的加密（預設為啟用）。 即使已停用儲存體帳戶層級的切換，這表示您可以不加密的 Azure 檔案共用連線，Azure 檔案同步仍然只會使用加密通道來存取您的檔案共用。

針對儲存體帳戶停用傳輸中加密的主要原因，是為了支援必須在舊版作業系統（例如 Windows Server 2008 R2 或舊版 Linux 發行版本）上執行的繼承應用程式，直接與 Azure 檔案共用交談。 如果繼承應用程式與檔案共用的 Windows Server 快取交談，切換此設定將不會有任何作用。 

我們強烈建議您確保在傳輸中的資料加密已啟用。

如需傳輸中加密的詳細資訊，請參閱[在 Azure 儲存體中需要安全傳輸](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

### <a name="azure-file-share-encryption-at-rest"></a>Azure 檔案共用待用加密
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>儲存層
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>啟用標準檔案共用以跨越最多 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>區域可用性
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="azure-file-sync-region-availability"></a>Azure 檔案同步區域可用性
Azure 檔案同步會在下欄區域內上市：

| Azure 雲端 | 地理區域 | Azure 區域 | 地區碼 |
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
| 公開 | 法國 | 法國南部 * | `francesouth` |
| 公開 | 印度 | 印度中部 | `centralindia` |
| 公開 | 印度 | 印度南部 | `southindia` |
| 公開 | 日本 | 日本東部 | `japaneast` |
| 公開 | 日本 | 日本西部 | `japanwest` |
| 公開 | 南韓 | 南韓中部 | `koreacentral` |
| 公開 | 南韓 | 南韓南部 | `koreasouth` |
| 公開 | 南非 | 南非北部 | `southafricanorth` |
| 公開 | 南非 | 南非西部 * | `southafricawest` |
| 公開 | 阿拉伯聯合大公國 | 阿拉伯聯合大公國中部 * | `uaecentral` |
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

針對以星號標示的區域，您必須聯絡 Azure 支援以要求存取這些區域中的 Azure 儲存體。 [本檔概述此](https://azure.microsoft.com/global-infrastructure/geographies/)程式。

## <a name="redundancy"></a>備援性
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

> [!Important]  
> 異地冗余和異地區域冗余儲存體可將儲存體手動容錯移轉至次要區域。 當您使用 Azure 檔案同步時，我們建議您不要在發生嚴重損壞的情況下執行此動作，因為資料遺失的可能性較高。 萬一您想要起始儲存體的手動容錯移轉時，您必須開啟 Microsoft 的支援案例，讓 Azure 檔案同步繼續與次要端點同步。

## <a name="migration"></a>移轉
如果您有現有的 Windows 檔案伺服器，Azure 檔案同步可以直接安裝，而不需要將資料移至新的伺服器。 如果您打算在採用 Azure 檔案同步的過程中遷移至新的 Windows 檔案伺服器，有幾種可能的方法可以將資料移至：

- 為您的舊檔案共用和新的檔案共用建立伺服器端點，並讓 Azure 檔案同步同步處理伺服器端點之間的資料。 這種方法的優點是，它可讓您輕鬆地在新的檔案伺服器上過度訂閱存放裝置，因為 Azure 檔案同步是可感知的雲端階層處理。 當您準備好時，可以將終端使用者剪下到新伺服器上的檔案共用，並移除舊的檔案共用的伺服器端點。

- 只在新的檔案伺服器上建立伺服器端點，並使用 `robocopy`將資料從舊的檔案共用複製到。 視新伺服器上的檔案共用拓撲而定（每個磁片區上有多少共用、每個磁片區的可用空間等等），您可能需要暫時布建額外的存放裝置，因為從舊的伺服器 `robocopy` 到內部部署資料中心內的新伺服器將會比 Azure 檔案同步將資料移至 Azure 的速度更快。

您也可以使用資料箱，將資料移轉至 Azure 檔案同步部署。 在大部分的情況下，當客戶想要使用資料箱來內嵌資料時，它們會這麼做，因為他們認為它會增加其部署速度，或因為它有助於限制頻寬案例。 雖然使用資料箱將資料內嵌到您的 Azure 檔案同步部署會降低頻寬使用率，但大部分情況下，透過上述其中一種方法來執行線上資料上傳可能會更快。 若要深入瞭解如何使用資料箱將資料內嵌至您的 Azure 檔案同步部署，請參閱[使用 Azure 資料箱將資料移轉至 Azure 檔案同步](storage-sync-offline-data-transfer.md)。

客戶在將資料移轉至新的 Azure 檔案同步部署時，常見的錯誤是將資料直接複製到 Azure 檔案共用，而不是在其 Windows 檔案伺服器上。 雖然 Azure 檔案同步會識別 Azure 檔案共用上的所有新檔案，並將其同步處理回您的 Windows 檔案共用，但這通常會比透過 Windows 檔案伺服器載入資料的速度慢很多。 許多 Azure 複製工具（例如 AzCopy）都有額外的缺點，那就是不復制檔案的所有重要中繼資料，例如時間戳記和 Acl。

## <a name="antivirus"></a>防毒
因為防毒程式的運作方式是掃描檔案中的已知惡意程式碼，所以防毒產品可能會導致階層式檔案的重新叫用。 在 4.0 版和更新版本的 Azure 檔案同步代理程式中，階層式檔案已設定安全的 Windows 屬性 FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS。 建議您洽詢您的軟體廠商，以了解如何設定其解決方案來略過讀取已設定此屬性的檔案 (很多軟體會自動這麼做)。 

作為 Microsoft 內部防毒解決方案的 Windows Defender 和 System Center Endpoint Protection (SCEP)，皆會自動略過讀取已設定此屬性的檔案。 我們已經測試這兩個解決方案並找到一個小問題：當您將伺服器新增至現有同步群組時，會在新的伺服器上重新叫用 (下載) 小於 800 個位元組的檔案。 這些檔案會保留在新的伺服器上，而且不會分層，因為這些檔案不符合階層處理大小需求 (> 64 kb)。

> [!Note]  
> 防毒軟體廠商可以使用[Azure 檔案同步的防毒軟體相容性測試套件](https://www.microsoft.com/download/details.aspx?id=58322)（可從 Microsoft 下載中心下載），檢查其產品與 Azure 檔案同步之間的相容性。

## <a name="backup"></a>Backup 
備份解決方案類似防毒解決方案，可能會導致階層式檔案的重新叫用。 建議使用雲端備份解決方案來備份 Azure 檔案共用，而不要使用內部部署備份產品。

如果您使用內部部署備份解決方案，則應在已停用雲端階層處理的同步群組中的伺服器上執行備份。 執行還原時，請使用磁碟區層級或檔案層級的還原選項。 使用檔案層級還原選項進行還原的檔案會同步至同步群組中的所有端點，並使用從備份還原過來的版本取代現有檔案。  磁碟區層級還原將不會取代 Azure 檔案共用或其他伺服器端點中的較新檔案版本。

> [!Note]  
> 裸機 (BMR) 還原可能會導致非預期的結果，且目前不受支援。

> [!Note]  
> 在已啟用雲端階層處理的磁片區上，現在支援第9版的 Azure 檔案同步代理程式、VSS 快照集（包括 [先前的版本] 索引標籤）。 不過，您必須透過 PowerShell 啟用先前的版本相容性。 [了解作法](storage-files-deployment-guide.md)。

## <a name="azure-file-sync-agent-update-policy"></a>Azure 檔案同步代理程式更新原則
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>後續步驟
* [考量防火牆和 Proxy 設定](storage-sync-files-firewall-and-proxy.md)
* [規劃 Azure 檔案部署](storage-files-planning.md)
* [部署 Azure 檔案服務](storage-files-deployment-guide.md)
* [部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)
* [監視 Azure 檔案同步](storage-sync-files-monitoring.md)