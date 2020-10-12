---
title: 規劃 Azure 檔案同步部署 | Microsoft Docs
description: 規劃使用 Azure 檔案同步的部署，這項服務可讓您在內部部署 Windows Server 或雲端 VM 上快取數個 Azure 檔案共用。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 876a96f579bff8d30e454e927054a951734f44ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89441094"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>規劃 Azure 檔案同步部署

:::row:::
    :::column:::
        [![採訪和示範 Azure 檔案同步簡介 - 按一下即可播放！](./media/storage-sync-files-planning/azure-file-sync-interview-video-snapshot.png)](https://www.youtube.com/watch?v=nfWLO7F52-s)
    :::column-end:::
    :::column:::
        Azure 檔案同步是一項服務，可讓您在內部部署 Windows Server 或雲端 VM 上快取一些 Azure 檔案共用。 
        
        本文將向您介紹 Azure 檔案同步概念和功能。 一旦您熟悉 Azure 檔案同步，就請考慮遵循 [Azure 檔案同步部署指南](storage-sync-files-deployment-guide.md)來試用這項服務。        
    :::column-end:::
:::row-end:::

這些檔案將儲存在雲端的 [Azure 檔案共用](storage-files-introduction.md)中。 Azure 檔案共用的使用方式有兩種：直接裝載這些無伺服器 Azure 檔案共用 (SMB)，或使用 Azure 檔案同步快取內部部署的 Azure 檔案共用。您所選擇的部署選項會變更規劃部署時所需考慮的層面。 

- **直接裝戴 Azure 檔案共用**：由於 Azure 檔案儲存體會提供 SMB 存取，因此您可以使用 Windows、macOS 和 Linux 中提供的標準 SMB 用戶端，在內部部署或雲端中裝載 Azure 檔案共用。 由於 Azure 檔案共用是無伺服器的，因此針對生產案例進行部署並不需要管理檔案伺服器或 NAS 裝置。 這表示您不需要套用軟體修補程式或交換實體磁碟。 

- **使用 Azure 檔案同步快取內部部署的 Azure 檔案共用**：Azure 檔案同步可讓您將組織的檔案共用集中在 Azure 檔案服務中，同時保有內部部署檔案伺服器的靈活度、效能及相容性。 Azure 檔案同步會將內部部署 (或雲端) Windows Server 轉換成 Azure 檔案共用的快速快取。 

## <a name="management-concepts"></a>管理概念
Azure 檔案同步部署有三個基礎管理物件：

- **Azure 檔案共用**：Azure 檔案共用是無伺服器的雲端檔案共用，可提供「Azure 檔案同步」同步關係的*雲端端點*。 雖然 Azure 檔案共用中的檔案可以直接使用 SMB 或 FileREST 通訊協定進行存取，但是建議您在搭配 Azure 檔案同步使用 Azure 檔案共用時，主要透過 Windows Server 快取存取檔案。這是因為 Azure 檔案儲存體現今缺少有效率的變更偵測機制 (如 Windows Server 所具有的)，因此直接對 Azure 檔案共用進行變更將需要一段時間才能傳播回伺服器端點。
- **伺服器端點**：Windows Server上正在同步至 Azure 檔案共用的路徑。 這可以是磁碟區上的特定資料夾或磁碟區的根目錄。 如果伺服器端點的命名空間沒有重疊，則相同磁碟區中可以存在多個伺服器端點。
- **同步群組**：此為物件，可定義**雲端端點**或 Azure 檔案共用與伺服器端點之間的同步關係。 同步群組內的端點會與彼此保持同步。 例如，如果您有兩組不同的檔案需要透過 Azure 檔案同步管理，您會建立兩個同步群組，並將不同的端點個別新增至這兩個同步群組。

### <a name="azure-file-share-management-concepts"></a>Azure 檔案共用管理概念
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

### <a name="azure-file-sync-management-concepts"></a>Azure 檔案同步管理概念
同步群組會部署到**儲存體同步服務**，這些服務是最上層物件，用來註冊與 Azure 檔案同步搭配使用的伺服器，並包含同步群組關聯性。 儲存體同步服務資源是儲存體帳戶資源的對等，同樣可以部署到 Azure 資源群組中。 儲存體同步服務可以建立同步群組，跨多個儲存體帳戶和多個已註冊 Windows Server 包含 Azure 檔案共用。

您必須先向儲存體同步服務註冊 Windows Server，才能在儲存體同步服務中建立同步群組。 這會建立**已註冊的伺服器物件**，代表您的伺服器或叢集與儲存體同步服務之間的信任關係。 若要註冊儲存體同步服務，您必須先在伺服器上安裝 Azure 檔案同步代理程式。 一次只能向單一儲存體同步服務註冊個別伺服器或叢集。

同步群組包含一個雲端端點或 Azure 檔案共用，以及至少一個伺服器端點。 伺服器端點物件所包含的設定，可設定**雲端階層處理**功能，以提供 Azure 檔案同步的快取功能。為了與 Azure 檔案共用同步，包含 Azure 檔案共用的儲存體帳戶必須與儲存體同步服務位於相同的 Azure 區域中。

### <a name="management-guidance"></a>管理指導方針
部署 Azure 檔案同步時，我們建議：

- 部署 1:1 的 Windows 檔案共用與 Azure 檔案共用。 伺服器端點物件可讓您在同步關係的伺服器端上如何設定同步拓撲有很大的彈性。 為了簡化管理，請讓伺服器端點的路徑符合 Windows 檔案共用的路徑。 

- 儘可能使用最少的儲存體同步服務。 當您有包含多個伺服器端點的同步群組時，這將可簡化管理，因為一次只能將一部 Windows 伺服器註冊到一個儲存體同步服務。 

- 部署 Azure 檔案共用時，請注意儲存體帳戶的 IOPS 限制。 在理想的情況下，您會使檔案共用與儲存體帳戶 1:1 對應，不過，由於來自您組織和 Azure 的各種限制和制約，這種情況不一定始終可行。 當一個儲存體帳戶中不可能只部署一個檔案共用時，請考慮哪些共用高度活躍、哪些共用較不活躍，以確保最熱門的檔案共用不會放在同一個儲存體帳戶中。

## <a name="windows-file-server-considerations"></a>Windows 檔案伺服器考量
若要在 Windows Server 上啟用同步功能，您必須安裝 Azure 檔案同步的可下載代理程式。 Azure 檔案同步代理程式提供兩個主要元件：`FileSyncSvc.exe`，這是負責監視伺服器端點上變更並起始同步工作階段的背景 Windows 服務，以及 `StorageSync.sys`，這是可啟用雲端階層處理和快速災害復原的檔案系統篩選器。  

### <a name="operating-system-requirements"></a>作業系統需求
支援 Azure 檔案同步搭配下列 Windows Server 版本：

| 版本 | 支援的 SKU | 支援的部署選項 |
|---------|----------------|------------------------------|
| Windows Server 2019 | Datacenter、Standard 和 IoT | Full 和 Core |
| Windows Server 2016 | Datacenter、Standard 和 Storage Server | Full 和 Core |
| Windows Server 2012 R2 | Datacenter、Standard 和 Storage Server | Full 和 Core |

Windows Server 的未來版本將會於發佈時加入支援清單。

> [!Important]  
> 建議您透過 Windows Update 的最新更新，將搭配 Azure 檔案同步使用的所有伺服器保持在最新狀態。 

### <a name="minimum-system-resources"></a>最低系統資源
Azure 檔案同步需要一部伺服器 (實體或虛擬)，其中至少有一個 CPU 和最少 2 GiB 的記憶體。

> [!Important]  
> 如果伺服器在啟用動態記憶體的虛擬機器中執行，則 VM 的記憶體應最少設定為 2048 MiB。

對於大部分的生產工作負載，不建議您設定僅具有最低需求的「Azure 檔案同步」同步伺服器。 如需詳細資訊，請參閱[建議的系統資源](#recommended-system-resources)。

### <a name="recommended-system-resources"></a>建議的系統需求
就像任何伺服器功能或應用程式一樣，Azure 檔案同步的系統資源需求取決於部署的規模；伺服器上較大型的部署需要更多的系統資源。 對於 Azure 檔案同步，規模取決於伺服器端點間的物件數目和資料集上的變換。 單一伺服器可在多個同步群組中具有伺服器端點，而在下表中列出的物件數目則說明伺服器所附加的完整命名空間。 

例如，具有 1 千萬個物件的伺服器端點 A + 具有 1 千萬個物件的伺服器端點 B = 2 千萬個物件。 對於該範例部署，我們會建議 8 個 CPU、16 GiB 的記憶體以提供穩定的狀態，以及 (如果可能) 48 GiB 的記憶體以進行初始遷移。
 
基於效能考量，命名空間資料會儲存在記憶體中。 因此，較大的命名空間需要更多記憶體來維持良好的效能，而更多變換則需要更多的 CPU 才能處理。 
 
在下表中，我們提供了命名空間的大小，以及典型一般用途檔案共用的容量轉換，其中平均檔案大小為 512 KiB。 如果您的檔案大小較小，請考慮為相同容量新增額外的記憶體。 將您的記憶體組態以命名空間的大小為基礎。

| 命名空間大小 - 檔案與目錄 (數百萬)  | 一般總容量 (TiB)  | CPU 核心  | 建議的記憶體 (GiB) |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8 (初始同步)/2 (一般變換)      |
| 5        | 2.3     | 2        | 16 (初始同步)/4 (一般變換)    |
| 10       | 4.7     | 4        | 32 (初始同步)/8 (一般變換)   |
| 30       | 14.0    | 8        | 48 (初始同步)/16 (一般變換)   |
| 50       | 23.3    | 16       | 64 (初始同步)/32 (一般變換)  |
| 100*     | 46.6    | 32       | 128 (初始同步)/32 (一般變換)  |

\*目前不建議同步處理超過 1 千萬個檔案和目錄。 這是以測試閾值為基礎的軟性限制。 如需詳細資訊，請參閱 [Azure 檔案儲存體可擴縮性和效能目標](storage-files-scale-targets.md#azure-file-sync-scale-targets)。

> [!TIP]
> 命名空間的初始同步是一項使用大量記憶體的作業，我們建議您直到完成初始同步前，配置更多的記憶體。 這不是必要的，但可能會加速初始同步。 
> 
> 一般變換是每天命名空間變更的 0.5%。 對於更高等級的變換，請考慮新增更多 CPU。 

- 本機連結的磁碟區會以 NTFS 檔案系統進行格式化。

### <a name="evaluation-cmdlet"></a>評估 Cmdlet
在部署 Azure 檔案同步之前，您應該使用 Azure 檔案同步評估 Cmdlet 來評估其是否與您的系統相容。 此 Cmdlet 會檢查檔案系統和資料集的潛在問題，例如不支援的字元或作業系統版本。 也會檢查下列提到的大部分功能 (但不是全部)；我們建議您仔細閱讀本節的其餘部分，以確保您的部署可順利進行。 

您可以藉由安裝 Az PowerShell 模組來安裝評估 Cmdlet，此模組可依照此處的指示進行安裝：[安裝和設定 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps)。

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
Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name> -SkipNamespaceChecks
```
 
若要以 CSV 格式顯示結果：
```powershell
$validation = Invoke-AzStorageSyncCompatibilityCheck C:\DATA
$validation.Results | Select-Object -Property Type, Path, Level, Description, Result | Export-Csv -Path C:\results.csv -Encoding utf8
```

### <a name="file-system-compatibility"></a>檔案系統相容性
只在直接連接的 NTFS 磁碟區上支援 Azure 檔案同步。 Windows Server 上直接連接的儲存體或 DAS 表示 Windows Server 作業系統擁有檔案系統。 您可以透過將磁碟實際連接到檔案伺服器、將虛擬磁碟連接至檔案伺服器 VM (例如 Hyper-v 所裝載的 VM)，或甚至透過 ISCSI 來提供 DAS。

只支援 NTFS 磁碟區；不支援 ReFS、FAT、FAT32 及其他檔案系統。

下表顯示 NTFS 檔案系統功能的 Interop 狀態： 

| 功能 | 支援狀態 | 注意 |
|---------|----------------|-------|
| 存取控制清單 (ACL) | 完全支援 | Azure 檔案同步會保留 Windows 樣式 Discretionary 存取控制清單，並由伺服器端點上的 Windows Server 強制執行。 當直接裝載 Azure 檔案共用時，也可以強制執行 ACL，不過這需要額外的設定。 如需詳細資訊，請參閱[身分識別一節](#identity)。 |
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
| ehthumbs.db | 媒體暫存檔案的縮圖 |
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
**Windows Server 2016 與 Windows Server 2019**   
重複資料刪除會在 Windows Server 2016 和 Windows Server 2019 中，已啟用雲端階層處理的磁碟區上受到支援。 在已啟用雲端階層處理的磁碟區上啟用重複資料刪除，可讓您在內部部署中快取更多檔案，而不需佈建更多儲存空間。 

在啟用雲端階層處理的磁碟區上啟用重復資料刪除時，伺服器端點位置內的重復資料刪除最佳化檔案將會根據雲端階層處理原則設定進行階層處理，與一般檔案類似。 當重復資料刪除最佳化檔案已進行階層處理，重復資料刪除垃圾收集工作將會自動執行，藉由移除磁碟區上其他檔案不再需要參考的區塊來回收磁碟空間。

請注意，磁碟區節省空間僅適用於伺服器；您在 Azure 檔案共用中的資料將不會進行重復資料刪除。

> [!Note]  
> 為了支援在 Windows Server 2019 上啟用雲端階層處理的磁碟區上進行重復資料刪除，必須安裝 Windows 更新 [KB4520062](https://support.microsoft.com/help/4520062)，而且需要 Azure 檔案同步代理程式版本 9.0.0.0 或更新版本。

**Windows Server 2012 R2**  
Azure 檔案同步不支援在 Windows Server 2012 R2 的相同磁碟區上進行重復資料刪除和雲端階層處理。 如果已在磁碟區上啟用重復資料刪除，則必須停用雲端階層處理。 

**注意事項**
- 如果在安裝 Azure 檔案同步代理程式之前安裝重復資料刪除，則需要重新啟動，才能支援在相同磁碟區上進行重復資料刪除和雲端階層處理。
- 在啟用雲端階層處理之後，如果在磁碟區上啟用重復資料刪除，則初始重復資料刪除最佳化工作將會最佳化磁碟區上尚未進行階層處理的檔案，而且會對雲端階層處理產生下列影響：
    - 可用空間原則會根據磁碟區上的可用空間，使用熱度圖繼續對檔案進行階層處理。
    - 日期原則會略過檔案的階層處理，而這些檔案可能由於存取檔案的重復資料刪除最佳化作業而有資格進行階層處理。
- 針對進行中的重復資料刪除最佳化作業，如果檔案尚未進行階層處理，則重復資料刪除 [MinimumFileAgeDays](https://docs.microsoft.com/powershell/module/deduplication/set-dedupvolume?view=win10-ps) 設定會延遲以日期原則進行雲端階層處理。 
    - 範例：如果 MinimumFileAgeDays 設定為 7 天，而雲端階層處理日期原則為 30 天，則日期原則會在 37 天後對檔案進行階層處理。
    - 注意:一旦 Azure 檔案同步對檔案進行階層處理，重復資料刪除最佳化工作就會略過該檔案。
- 如果執行 Windows Server 2012 R2 並已安裝 Azure 檔案同步代理程式的伺服器已升級至 Windows Server 2016 或 Windows Server 2019，則必須執行下列步驟，才能支援在相同磁碟區上進行重復資料刪除和雲端階層處理：  
    - 解除安裝適用於 Windows Server 2012 R2 的 Azure 檔案同步代理程式，然後重新啟動伺服器。
    - 下載適用於新伺服器作業系統版本 (Windows Server 2016 或 Windows Server 2019) 的 Azure 檔案同步代理程式。
    - 安裝 Azure 檔案同步代理程式，並重新啟動伺服器。  
    
    注意:解除安裝並重新安裝代理程式時，會保留伺服器上的 Azure 檔案同步組態設定。

### <a name="distributed-file-system-dfs"></a>分散式檔案系統 (DFS)
Azure 檔案同步支援與 DFS 命名空間 (DFS-N) 和 DFS 複寫 (DFS-R) 互通。

**DFS 命名空間 (DFS-N)** ：DFS-N 伺服器上完全支援 Azure 檔案同步。 您可以將 Azure 檔案同步代理程式安裝在一或多個 DFS-N 成員上，同步伺服器端點與雲端端點之間的資料。 如需詳細資訊，請參閱 [DFS 命名空間概觀](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview)。
 
**DFS 複寫 (DFS-R)** ：因為 DFS-R 與 Azure 檔案同步都是複寫解決方案，所以建議您在大部分情況下，以 Azure 檔案同步取代 DFS-R。不過有幾個案例，您會想要一起使用 DFS-R 和 Azure 檔案同步：

- 您正要從 DFS-R 部署移轉至 Azure 檔案同步部署。 如需詳細資訊，請參閱[將 DFS 複寫 (DFS-R) 部署移轉至 Azure 檔案同步](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync)。
- 並非需要檔案資料複本的每部內部部署伺服器都能直接連線到網際網路。
- 分支伺服器將資料合併到您要使用 Azure 檔案同步的單一中樞伺服器。

Azure 檔案同步和 DFS-R 如需並存使用：

1. 務必在具有 DFS-R 複寫資料夾的磁碟區上停用 Azure 檔案同步雲端階層。
2. 不應在 DFS-R 唯讀複寫資料夾上設定伺服器端點。

如需詳細資訊，請參閱 [DFS 複寫概觀](https://technet.microsoft.com/library/jj127250)。

### <a name="sysprep"></a>Sysprep
不支援在已安裝 Azure 檔案同步代理程式的伺服器上使用 sysprep，而且此舉可能會導致非預期的結果。 請在部署伺服器映像和完成 sysprep 迷你設定之後，再進行代理程式安裝與伺服器註冊。

### <a name="windows-search"></a>Windows 搜尋
如果在伺服器端點上啟用雲端階層處理，則系統會略過階層式檔案，且 Windows 搜尋不會將這些檔案編製索引。 非階層式檔案則會正確編製索引。

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>其他階層式存放裝置管理 (HSM) 解決方案
不應該搭配 Azure 檔案同步使用其他 HSM 解決方案。

## <a name="identity"></a>身分識別
Azure 檔案同步會使用您的標準 AD 型身分識別，不需要設定同步以外的任何特殊設定。當您使用 Azure 檔案同步時，一般預期是大部分的存取都會透過 Azure 檔案同步快取伺服器進行，而不是透過 Azure 檔案共用進行。 由於伺服器端點位於 Windows Server 上，而且 Windows Server 已有很長時間支援 AD 和 Windows 樣式 ACL，因此除了確保向儲存體同步服務註冊的 Windows 檔案伺服器已加入網域之外，不需要採取任何動作。 Azure 檔案同步會將 ACL 儲存在 Azure 檔案共用中的檔案，並將其複寫至所有伺服器端點。

即使直接對 Azure 檔案共用所做的變更需要更長時間才能同步至同步群組中的伺服器端點，您可能仍想要確保您也可以在雲端直接對您的檔案共用強制執行 AD 權限。 若要這樣做，您必須使用網域將儲存體帳戶加入至您的內部部署 AD，就像 Windows 檔案伺服器加入網域的方式一樣。 若要深入了解如何使用網域將您的儲存體帳戶加入至客戶所擁有的 Active Directory，請參閱 [Azure 檔案儲存體 Active Directory 概觀](storage-files-active-directory-overview.md)。

> [!Important]  
> 不需要使用網域將您的儲存體帳戶加入至 Active Directory，即可成功部署 Azure 檔案同步。這是一個嚴格的選擇性步驟，可讓 Azure 檔案共用在使用者直接裝載 Azure 檔案共用時，強制執行內部部署 ACL。

## <a name="networking"></a>網路功能
Azure 檔案同步代理程式會使用 Azure 檔案同步 REST 通訊協定和 FileREST 通訊協定，與您的儲存體同步服務和 Azure 檔案共用進行通訊，而這兩個通訊協定一律使用經由連接埠 443 的 HTTPS。 SMB 永遠不會用來在您的 Windows Server 與 Azure 檔案共用之間上傳或下載資料。 由於大部分的組織允許經由連接埠 443 的 HTTPS 流量，因此若要造訪大部分的網站，通常不需要特殊的網路組態，就能部署 Azure 檔案同步。

根據組織的原則或獨特的法規需求，您與 Azure 的通訊可能需要受到更多約束，因此 Azure 檔案共用提供了數種機制，供您設定網路功能。 根據您的需求，您可以：

- 透過 ExpressRoute 或 Azure VPN 進行通道同步和檔案上傳/下載流量。 
- 利用 Azure 檔案儲存體和 Azure 網路功能，例如服務端點和私人端點。
- 設定 Azure 檔案同步，以在您的環境中支援您的 Proxy。
- 節流來自 Azure 檔案同步的網路活動。

若要深入瞭解 Azure 檔案同步和網路功能，請參閱 [Azure 檔案同步網路功能考慮](storage-sync-files-networking-overview.md)。

## <a name="encryption"></a>加密
使用 Azure 檔案同步時，需要考慮三個不同層級的加密：在 Windows Server 的待用儲存體加密、在 Azure 檔案同步代理程式與 Azure 之間的傳輸中加密，以及在 Azure 檔案共用中資料的待用加密。 

### <a name="windows-server-encryption-at-rest"></a>Windows Server 待用加密 
有兩種策略通常會使用 Azure 檔案同步，在 Windows Server 上加密資料：檔案系統底下的加密，讓檔案系統及寫入其中的所有資料都會進行加密，以及檔案格式本身內的加密。 這些方法並不互斥；如有需要，您可以將它們搭配使用，因為加密的目的不同。

為了在檔案系統底下提供加密，Windows Server 提供 BitLocker 收件匣。 BitLocker 對於 Azure 檔案同步而言完全透明。使用 BitLocker 這類加密機制的主要原因，是為了防止有人竊取磁碟，實際洩漏內部部署資料中心的資料，以及防止側載未授權的作業系統來執行未授權的讀取/寫入。 若要深入了解 BitLocker，請參閱 [BitLocker 概觀](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview)。

與 BitLocker 作用類似的第三方產品，由於位於 NTFS 磁碟區下方，應該也能以完全透明的方式使用 Azure 檔案同步。 

另一個用於加密資料的主要方法，就是在應用程式儲存檔案時，加密檔案的資料流。 某些應用程式可能會以原生方式執行此動作，但通常不會發生這種情況。 加密檔案資料流的方法範例是 Azure 資訊保護 (AIP)/Azure Rights Management Services (Azure RMS)/Active Directory RMS。 使用加密機制 (例如 AIP/RMS) 的主要原因，是要防止有人將資料從檔案共用複製到其他位置 (例如快閃磁碟機)，或透過電子郵件傳送給未授權人員，而將資料洩漏出去。 當檔案的資料流已加密為檔案格式的一部分時，此檔案會繼續在 Azure 檔案共用上加密。 

Azure 檔案同步無法與 NTFS 加密檔案系統 (NTFS EFS) 或位於檔案系統上但在檔案資料流下的第三方加密解決方案交互操作。 

### <a name="encryption-in-transit"></a>傳輸中加密

> [!NOTE]
> Azure 檔案同步服務將於 2020 年 8 月 1 日移除 TLS 1.0 和 1.1 的支援。 根據預設，所有支援的 Azure 檔案同步代理程式版本都已使用 TLS 1.2。 如果您的伺服器上已停用 TLS 1.2 或使用 Proxy，則可能會發生使用舊版 TLS 的情況。 如果您使用 Proxy，建議您檢查 Proxy 組態。 5/1/2020 之後新增的 Azure 檔案同步服務區域僅會支援 TLS 1.2，而且將會在 2020 年 8 月 1 日從現有區域中移除 TLS 1.0 和 1.1 的支援。  如需詳細資訊，請參閱[疑難排解指南](storage-sync-files-troubleshoot.md#tls-12-required-for-azure-file-sync)。

Azure 檔案同步代理程式會使用 Azure 檔案同步 REST 通訊協定和 FileREST 通訊協定，與您的儲存體同步服務和 Azure 檔案共用進行通訊，而這兩個通訊協定一律使用經由連接埠 443 的 HTTPS。 Azure 檔案同步不會透過 HTTP 傳送未加密的要求。 

Azure 儲存體帳戶包含一個需要傳輸中加密 (預設為啟用) 的切換。 即使已停用儲存體帳戶層級的切換，這表示有可能以未加密的方式連線至 Azure 檔案共用，但 Azure 檔案同步仍然只會使用加密通道來存取您的檔案共用。

停用儲存體帳戶傳輸中加密的主要原因，是為了支援必須在舊版作業系統上執行的繼承應用程式，例如 Windows Server 2008 R2 或舊版 Linux 散發套件，直接與 Azure 檔案共用交談。 如果繼承應用程式與檔案共用的 Windows Server 快取交談，切換此設定將不會有任何作用。 

我們強烈建議您確保傳輸中資料加密已啟用。

如需傳輸中加密的詳細資訊，請參閱[在 Azure 儲存體中需要安全傳輸](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

### <a name="azure-file-share-encryption-at-rest"></a>Azure 檔案共用的待用加密
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>儲存層
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>啟用標準檔案共用可跨越最多 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>區域可用性
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="azure-file-sync-region-availability"></a>Azure 檔案共用的區域可用性
Azure 檔案同步可在下列區域提供：

| Azure 雲端 | 地理區域 | Azure 區域 | 區域碼 |
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
| 公開 | 阿拉伯聯合大公國 | 阿拉伯聯合大公國中部* | `uaecentral` |
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

針對以星號標示的區域，您必須連絡 Azure 支援，要求可在那些區域存取 Azure 儲存體。 [本文件](https://azure.microsoft.com/global-infrastructure/geographies/)會簡要說明此程序。

## <a name="redundancy"></a>備援性
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

> [!Important]  
> 異地備援和異地區域備援儲存體可讓您將儲存體手動容錯移轉至次要區域。 當您使用 Azure 檔案同步時，我們建議您不要在災害外部執行此動作，因為這會提高資料遺失的可能性。 如果您想要在發生災害時起始儲存體的手動容錯移轉，您必須向 Microsoft 開啟支援案例，讓 Azure 檔案同步繼續與次要端點同步。

## <a name="migration"></a>遷移
如果您有現有的 Windows 檔案伺服器，Azure 檔案同步可以直接安裝，而不需要將資料移至新的伺服器。 如果您打算在採用 Azure 檔案同步時遷移至新的 Windows 檔案伺服器，有幾種移動資料的可行方法：

- 為您的舊檔案共用和新的檔案共用建立伺服器端點，並讓「Azure 檔案同步」同步處理伺服器端點之間的資料。 這種方法的優點是，可讓您非常輕鬆地在新的檔案伺服器上過度訂閱儲存體，因為 Azure 檔案同步可感知雲端階層處理。 當您準備好時，可以將終端使用者移交到新伺服器上的檔案共用，並移除舊檔案共用的伺服器端點。

- 只在新的檔案伺服器上建立伺服器端點，並使用 `robocopy` 將資料從舊的檔案共用複製到其中。 根據新伺服器上的檔案共用拓撲 (每個磁碟區上有多少共用、每個磁碟區的可用程度等等)，您可能需要暫時佈建額外的儲存體，因為預期從舊伺服器 `robocopy` 到內部部署資料中心內的新伺服器將會比 Azure 檔案同步將資料移至 Azure 更快完成。

您也可以使用資料箱，將資料遷移至 Azure 檔案同步部署。 在大部分的時候，當客戶想要使用資料箱來內嵌資料時，這麼做的原因是認為其會增加部署速度，或有助於頻寬受限的案例。 雖然使用資料箱將資料內嵌到您的 Azure 檔案同步部署的確會降低頻寬使用率，但在大部分的情況下，透過上述其中一種方法來執行線上資料上傳可能會更快。 若要深入了解如何使用資料箱將資料內嵌至您的 Azure 檔案同步部署，請參閱[使用 Azure 資料箱將資料遷移到 Azure 檔案同步](storage-sync-offline-data-transfer.md)。

客戶在將資料遷移至新的 Azure 檔案同步部署時，常見的錯誤是將資料直接複製到 Azure 檔案共用，而不是其 Windows 檔案伺服器。 雖然 Azure 檔案同步會識別 Azure 檔案共用上的所有新檔案，並將其同步回您的 Windows 檔案共用，但這通常會比透過 Windows 檔案伺服器載入資料的速度慢很多。 使用 Azure 複製工具（例如 AzCopy）時，請務必使用最新版本。 請檢查檔案 [複製工具表格](storage-files-migration-overview.md#file-copy-tools) ，以取得 Azure 複製工具的總覽，以確保您可以複製檔案的所有重要中繼資料，例如時間戳記和 acl。

## <a name="antivirus"></a>防毒
由於防毒軟體的運作方式是掃描檔案中的已知惡意程式碼，因此，防毒軟體可能會導致重新叫用階層式檔案，因而產生高輸出費用。 在 4.0 版和更新版本的 Azure 檔案同步代理程式中，階層式檔案已設定安全的 Windows 屬性 FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS。 建議您洽詢您的軟體廠商，以了解如何設定其解決方案來略過讀取已設定此屬性的檔案 (很多軟體會自動這麼做)。 

作為 Microsoft 內部防毒解決方案的 Windows Defender 和 System Center Endpoint Protection (SCEP)，皆會自動略過讀取已設定此屬性的檔案。 我們已經測試這兩個解決方案並找到一個小問題：當您將伺服器新增至現有同步群組時，會在新的伺服器上重新叫用 (下載) 小於 800 個位元組的檔案。 這些檔案會保留在新的伺服器上，而且不會分層，因為這些檔案不符合階層處理大小需求 (> 64 kb)。

> [!Note]  
> 防毒軟體廠商可以使用 [Azure 檔案同步防毒相容性測試套件](https://www.microsoft.com/download/details.aspx?id=58322) (可從 Microsoft 下載中心下載)，檢查其產品與 Azure 檔案同步之間的相容性。

## <a name="backup"></a>Backup 
如果已啟用雲端階層處理，則不應使用直接備份伺服器端點的解決方案，或不應使用伺服器端點所在的 VM。 雲端階層處理只會在伺服器端點上儲存您的資料子集，並將完整資料集放在您的 Azure 檔案共用中。 視所使用的備份解決方案而定，階層式檔案將會略過，而且不會備份 (因為它們已設定 FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS 屬性) ，或會將它們重新叫用到磁片，因而產生高輸出費用。 我們建議使用雲端備份解決方案來直接備份 Azure 檔案共用。 如需詳細資訊，請參閱 [關於 azure 檔案共用備份](https://docs.microsoft.com/azure/backup/azure-file-share-backup-overview?toc=/azure/storage/files/toc.json) ，或洽詢您的備份提供者，查看是否支援備份 Azure 檔案共用。

如果您想要使用內部部署備份解決方案，應該在已停用雲端階層處理的同步處理群組中的伺服器上執行備份。 執行還原時，請使用磁碟區層級或檔案層級的還原選項。 使用檔案層級還原選項進行還原的檔案會同步至同步群組中的所有端點，並使用從備份還原過來的版本取代現有檔案。  磁碟區層級還原將不會取代 Azure 檔案共用或其他伺服器端點中的較新檔案版本。

> [!Note]  
> 裸機 (BMR) 還原可能會導致非預期的結果，且目前不受支援。

> [!Note]  
> 搭配第 9 版的 Azure 檔案同步代理程式，已啟用雲端階層處理的磁碟區現在支援 VSS 快照集 (包括 [舊版] 索引標籤)。 不過，您必須透過 PowerShell 啟用舊版相容性。 [了解作法](storage-sync-files-deployment-guide.md#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service)。

## <a name="azure-file-sync-agent-update-policy"></a>Azure 檔案同步代理程式更新原則
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>後續步驟
* [考量防火牆和 Proxy 設定](storage-sync-files-firewall-and-proxy.md)
* [規劃 Azure 檔案部署](storage-files-planning.md)
* [部署 Azure 檔案服務](storage-files-deployment-guide.md)
* [部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)
* [監視 Azure 檔案同步](storage-sync-files-monitoring.md)
