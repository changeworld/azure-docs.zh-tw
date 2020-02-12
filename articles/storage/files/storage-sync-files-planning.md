---
title: 規劃 Azure 檔案同步部署 | Microsoft Docs
description: 了解規劃 Azure 檔案服務部署時的考量事項。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d2dbe29c5a348363172f57da86483ccf3fd787f0
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2020
ms.locfileid: "76046092"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>規劃 Azure 檔案同步部署
使用 Azure 檔案同步，將組織的檔案共用集中在 Azure 檔案服務中，同時保有內部部署檔案伺服器的彈性、效能及相容性。 Azure 檔案同步會將 Windows Server 轉換成 Azure 檔案共用的快速快取。 您可以使用 Windows Server 上可用的任何通訊協定來從本機存取資料，包括 SMB、NFS 和 FTPS。 您可以視需要存取多個散佈於世界各地的快取。

本文章說明 Azure 檔案同步部署的重要考量。 建議您另行閱讀[規劃 Azure 檔案服務部署](storage-files-planning.md)。 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="azure-file-sync-terminology"></a>Azure 檔案同步術語
在了解規劃 Azure 檔案同步部署的細節之前，請務必先了解其術語。

### <a name="storage-sync-service"></a>儲存體同步服務
儲存體同步服務是 Azure 檔案同步最上層的 Azure 資源。儲存體同步服務資源是儲存體帳戶資源的對等，可以同樣地部署至 Azure 資源群組。 由於儲存體同步服務可以透過多個同步群組與多個儲存體帳戶建立同步關係，因此最上層資源必須與儲存體帳戶資源不同。 一個訂用帳戶可以部署多個儲存體同步服務資源。

### <a name="sync-group"></a>同步群組
同步群組定義一組檔案的同步拓撲。 同步群組內的端點會與彼此保持同步。 例如，如果您想要使用 Azure 檔案同步來管理兩組不同的檔案，您會建立兩個同步群組，並將不同的端點新增至每個同步處理群組。 儲存體同步服務可以視需要裝載許多同步群組。  

### <a name="registered-server"></a>已註冊的伺服器
已註冊的伺服器物件代表您的伺服器 (或叢集) 與儲存體同步服務之間的信任關係。 您可以視需要向儲存體同步服務執行個體註冊多個伺服器。 不過，一次只能向單一儲存體同步服務註冊單一伺服器 (或叢集)。

### <a name="azure-file-sync-agent"></a>Azure 檔案同步代理程式
Azure 檔案同步代理程式是可下載的套件，可讓 Windows Server 能夠和 Azure 檔案共用進行同步處理。 Azure 檔案同步代理程式有三個主要元件： 
- **FileSyncSvc**：負責監視伺服器端點上的變更，以及起始同步至 Azure 之會話的背景服務。
- **StorageSync.sys**：負責將檔案分層處理到 Azure 檔案服務 (啟用雲端階層處理時) 的 Azure 檔案同步檔案系統篩選器。
- **PowerShell 管理 Cmdlet**：用來與 Microsoft.StorageSync Azure 資源提供者互動的 PowerShell Cmdlet。 您可以在下列 (預設) 位置找到這些 Cmdlet：
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>伺服器端點
伺服器端點代表已註冊伺服器上的特定位置，例如伺服器磁碟區上的資料夾。 如果伺服器端點的命名空間沒有重疊 (例如 `F:\sync1` 和 `F:\sync2`)，則相同磁碟區中可以存在多個伺服器端點。 您可以為每個伺服器端點個別設定雲端階層原則。 

您可以透過掛接點建立伺服器端點。 請注意，伺服器端點內的掛接點會略過。  

您可以在系統磁碟區上建立伺服器端點，但這樣做有兩個限制：
* 無法啟用雲端階層處理。
* 不會執行快速命名空間還原 (其中系統會快速中斷連線整個命名空間，然後開始重新叫用內容)。


> [!Note]  
> 僅支援非卸除式磁碟區。  不支援使用從遠端共用對應的磁碟機作為伺服器端點路徑。  此外，伺服器端點可以位於 Windows 系統磁碟區，但系統磁碟區上不支援雲端階層。

如果您將具有一組現有檔案的伺服器位置作為伺服器端點新增至同步群組，那些檔案會與同步群組中其他端點上已存在的任何其他檔案合併。

### <a name="cloud-endpoint"></a>雲端端點
雲端端點是屬於同步群組之一部分的 Azure 檔案共用。 整個 Azure 檔案共用都會同步，而且 Azure 檔案共用只能是單一雲端端點的成員。 因此，Azure 檔案共用只能是單一同步處理群組的成員。 如果您將內含一組現有檔案的 Azure 檔案共用作為雲端端點新增至同步群組，現有的檔案會與同步群組中其他端點上已存在的任何其他檔案合併。

> [!Important]  
> Azure 檔案同步支援直接對 Azure 檔案共用進行變更。 不過，在 Azure 檔案共用上所做的任何變更，都必須先由 Azure 檔案同步變更偵測作業做出探索。 針對雲端端點的變更偵測作業，每隔 24 小時才會起始一次。 此外，透過 REST 通訊協定對 Azure 檔案共用所做的變更，將不會更新 SMB 上次修改時間，而且不會視為同步變更。如需詳細資訊，請參閱[Azure 檔案儲存體常見問題](storage-files-faq.md#afs-change-detection)。

### <a name="cloud-tiering"></a>雲端階層處理 
雲端階層處理是 Azure 檔案同步的一個選用功能，其中經常存取的檔案會快取到伺服器本機上，而其他的檔案會依原則設定分層處理至 Azure 檔案服務。 如需詳細資訊，請參閱[了解雲端階層處理](storage-sync-cloud-tiering.md)。

## <a name="azure-file-sync-system-requirements-and-interoperability"></a>Azure 檔案同步系統需求和互通性 
本節涵蓋 Azure 檔案同步代理程式與 Windows Server 功能和角色以及第三方解決方案的系統需求和互通性。

### <a name="evaluation-cmdlet"></a>評估 Cmdlet
部署 Azure 檔案同步之前，您應該先使用 Azure 檔案同步評估 Cmdlet 來評估它是否與您的系統相容。 此 Cmdlet 會檢查檔案系統和資料集的潛在問題，例如不支援的字元或不支援的作業系統版本。 其檢查涵蓋了下列大部分但並非全部的功能;我們建議您仔細閱讀本節的其餘部分，以確保您的部署順利進行。 

您可以藉由安裝 Az PowerShell module 來安裝評估 Cmdlet，此模組可依照以下指示安裝：[安裝和設定 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps)。

#### <a name="usage"></a>用量  
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

### <a name="system-requirements"></a>系統需求
- 執行下列其中一個作業系統版本的伺服器：

    | 版本 | 支援的 SKU | 支援的部署選項 |
    |---------|----------------|------------------------------|
    | Windows Server 2019 | Datacenter 和 Standard | 完整和核心 |
    | Windows Server 2016 | Datacenter 和 Standard | 完整和核心 |
    | Windows Server 2012 R2 | Datacenter 和 Standard | 完整和核心 |
    | 適用于儲存體的 Windows Server IoT 2019| Datacenter 和 Standard | 完整和核心 |
    | Windows Storage Server 2016| Datacenter 和 Standard | 完整和核心 |
    | Windows Storage Server 2012 R2| Datacenter 和 Standard | 完整和核心 |

    Windows Server 的未來版本將會於發佈時加入支援清單。

    > [!Important]  
    > 建議您透過 Windows Update 的最新更新，將搭配 Azure 檔案同步使用的所有伺服器保持在最新狀態。 

- 至少有 2 GiB 記憶體的伺服器。

    > [!Important]  
    > 如果伺服器在啟用動態記憶體的虛擬機器中執行，則 VM 的記憶體應最少設定為 2048 MiB。
    
- 本機連結的磁碟區會以 NTFS 檔案系統進行格式化。

### <a name="file-system-features"></a>檔案系統功能

| 功能 | 支援狀態 | 注意 |
|---------|----------------|-------|
| 存取控制清單 (ACL) | 完全支援 | Azure 檔案同步會保留 Windows ACL，並由 Windows Server 在伺服器端點上強制執行。 如果檔案是直接在雲端中存取，則 Azure 檔案服務尚未支援 Windows ACL。 |
| 永久連結 | 已略過 | |
| 符號連結 | 已略過 | |
| 掛接點 | 部分支援 | 掛接點可能是伺服器端點的根目錄，但如果伺服器端點的命名空間中包含它們，系統會予以略過。 |
| 接合 | 已略過 | 例如，分散式檔案系統 DfrsrPrivate 和 DFSRoots 資料夾。 |
| 重新分析點 | 已略過 | |
| NTFS 壓縮 | 完全支援 | |
| 疏鬆檔案 | 完全支援 | 疏鬆檔案會同步 (不會封鎖)，但它們會以完整檔案的形式同步至雲端。 如果檔案內容在雲端中 (或另一部伺服器上) 有所變更，該檔案在變更下載之後就不再是疏鬆檔案。 |
| 替代資料流 (ADS) | 已保留，但未同步 | 例如，不會同步「檔案分類基礎結構」所建立的分類標籤。 每個伺服器端點上檔案的現有分類標籤會原封不動。 |

> [!Note]  
> 僅支援 NTFS 磁碟區。 不支援 ReFS、FAT、FAT32 及其他檔案系統。

### <a name="files-skipped"></a>跳過的檔案

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

**注意事項**
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

### <a name="antivirus-solutions"></a>防毒解決方案
因為防毒程式的運作方式是掃描檔案中的已知惡意程式碼，所以防毒產品可能會導致階層式檔案的重新叫用。 在 4.0 版和更新版本的 Azure 檔案同步代理程式中，階層式檔案已設定安全的 Windows 屬性 FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS。 建議您洽詢您的軟體廠商，以了解如何設定其解決方案來略過讀取已設定此屬性的檔案 (很多軟體會自動這麼做)。 

作為 Microsoft 內部防毒解決方案的 Windows Defender 和 System Center Endpoint Protection (SCEP)，皆會自動略過讀取已設定此屬性的檔案。 我們已經測試這兩個解決方案並找到一個小問題：當您將伺服器新增至現有同步群組時，會在新的伺服器上重新叫用 (下載) 小於 800 個位元組的檔案。 這些檔案會保留在新的伺服器上，而且不會分層，因為這些檔案不符合階層處理大小需求 (> 64 kb)。

> [!Note]  
> 防毒軟體廠商可以使用[Azure 檔案同步的防毒軟體相容性測試套件](https://www.microsoft.com/download/details.aspx?id=58322)（可從 Microsoft 下載中心下載），檢查其產品與 Azure 檔案同步之間的相容性。

### <a name="backup-solutions"></a>備份解決方案
備份解決方案類似防毒解決方案，可能會導致階層式檔案的重新叫用。 建議使用雲端備份解決方案來備份 Azure 檔案共用，而不要使用內部部署備份產品。

如果您使用內部部署備份解決方案，則應在已停用雲端階層處理的同步群組中的伺服器上執行備份。 執行還原時，請使用磁碟區層級或檔案層級的還原選項。 使用檔案層級還原選項進行還原的檔案會同步至同步群組中的所有端點，並使用從備份還原過來的版本取代現有檔案。  磁碟區層級還原將不會取代 Azure 檔案共用或其他伺服器端點中的較新檔案版本。

> [!Note]  
> 裸機 (BMR) 還原可能會導致非預期的結果，且目前不受支援。

> [!Note]  
> 在已啟用雲端階層處理的磁片區上，現在支援第9版的 Azure 檔案同步代理程式、VSS 快照集（包括 [先前的版本] 索引標籤）。 不過，您必須透過 PowerShell 啟用先前的版本相容性。 [了解作法](storage-files-deployment-guide.md)。

### <a name="encryption-solutions"></a>加密解決方案
加密解決方案的支援取決於其實作方式。 Azure 檔案共用已知可用於：

- BitLocker 加密
- Azure 資訊保護、Azure Rights Management Services (Azure RMS) 及 Active Directory RMS

Azure 檔案共用已知無法用於：

- NTFS 加密檔案系統 (EFS)

一般來說，Azure 檔案同步應該會支援與位於檔案系統下的加密解決方案 (例如 BitLocker)，以及實作於檔案格式中的解決方案 (例如 Azure 資訊保護) 之間的互通性。 對於位於檔案系統之上的解決方案 (例如 NTFS EFS)，並沒有做出任何特殊的互通性。

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>其他階層式存放裝置管理 (HSM) 解決方案
不應該搭配 Azure 檔案同步使用其他 HSM 解決方案。

## <a name="region-availability"></a>區域供應狀況
Azure 檔案同步僅於下列區域提供：

| 地區 | 資料中心位置 |
|--------|---------------------|
| 澳大利亞東部 | 新南威爾斯 |
| 澳大利亞東南部 | 維多利亞 |
| 巴西南部 | 聖保羅州 |
| 加拿大中部 | 多倫多 |
| 加拿大東部 | 魁北克市 |
| 印度中部 | 鵬尼 |
| 美國中部 | 愛荷華州 |
| 東亞 | 香港特別行政區 |
| 美國東部 | 維吉尼亞州 |
| 美國東部 2 | 維吉尼亞州 |
| 法國中部 | 巴黎 |
| 法國南部 * | 馬賽 |
| 南韓中部 | 首爾 |
| 南韓南部 | 釜山 |
| 日本東部 | 東京，埼玉 |
| 日本西部 | 大阪 |
| 美國中北部 | 伊利諾州 |
| 北歐 | 愛爾蘭 |
| 南非北部 | 約翰尼斯堡 |
| 南非西部 * | 開普敦 |
| 美國中南部 | 德克薩斯州 |
| 印度南部 | 辰內 |
| 東南亞 | 新加坡 |
| 英國南部 | 倫敦 |
| 英國西部 | 卡地夫 |
| US Gov 亞利桑那州 | 亞利桑那州 |
| US Gov 德克薩斯州 | 德克薩斯州 |
| US Gov 維吉尼亞州 | 維吉尼亞州 |
| 阿拉伯聯合大公國北部 | 杜拜 |
| 阿拉伯聯合大公國中部 * | 阿布達比 |
| 西歐 | 荷蘭 |
| 美國中西部 | 懷俄明州 |
| 美國西部 | 加利福尼亞州 |
| 美國西部 2 | 華盛頓州 |

Azure 檔案同步僅支援與位於和儲存體同步服務相同之區域中的 Azure 檔案共用進行同步。

針對以星號標示的區域，您必須聯絡 Azure 支援以要求存取這些區域中的 Azure 儲存體。 [本檔概述此](https://azure.microsoft.com/global-infrastructure/geographies/)程式。

### <a name="azure-disaster-recovery"></a>Azure 災害復原
為防範遺失 Azure 區域，Azure 檔案同步會與[異地備援儲存體備援](../common/storage-redundancy-grs.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) (GRS) 選項整合。 GRS 儲存體的運作方式是在主要區域中您通常與其互動的儲存體，與配對的次要區域中的儲存體之間，使用非同步區塊複寫。 萬一發生導致 Azure 區域暫時或永久離線的嚴重損壞狀況，Microsoft 會將儲存體容錯移轉到配對的區域。 

> [!Warning]  
> 若您使用 Azure 檔案共用作為 GRS 儲存體帳戶中的雲端端點，您不應該啟動儲存體帳戶的容錯移轉。 這麼做將導致同步停止運作，且可能會在新分層的檔案中產生未預期的資料遺失。 若發生 Azure 區域遺失，Microsoft 將會觸發與 Azure 檔案同步相容的儲存體帳戶容錯移轉。

為支援異地備援儲存體和 Azure 檔案同步之間的容錯移轉整合，所有 Azure 檔案同步區域都會與符合儲存體所使用之次要區域的次要區域配對。 這些配對如下：

| 主要區域      | 配對的區域      |
|---------------------|--------------------|
| 澳大利亞東部      | 澳大利亞東南部|
| 澳大利亞東南部 | 澳大利亞東部     |
| 巴西南部        | 美國中南部   |
| 加拿大中部      | 加拿大東部        |
| 加拿大東部         | 加拿大中部     |
| 印度中部       | 印度南部        |
| 美國中部          | 美國東部 2          |
| 東亞           | 東南亞     |
| 美國東部             | 美國西部            |
| 美國東部 2           | 美國中部         |
| 法國中部      | 法國南部       |
| 法國南部        | 法國中部     |
| 日本東部          | 日本西部         |
| 日本西部          | 日本東部         |
| 南韓中部       | 南韓南部        |
| 南韓南部         | 南韓中部      |
| 北歐        | 西歐        |
| 美國中北部    | 美國中南部   |
| 南非北部  | 南非西部  |
| 南非西部   | 南非北部 |
| 美國中南部    | 美國中北部   |
| 印度南部         | 印度中部      |
| 東南亞      | 東亞          |
| 英國南部            | 英國西部            |
| 英國西部             | 英國南部           |
| US Gov 亞利桑那州      | US Gov 德克薩斯州       |
| US Gov 愛荷華州         | US Gov 維吉尼亞州    |
| US Gov 維吉尼亞州      | US Gov 德克薩斯州       |
| 西歐         | 北歐       |
| 美國中西部     | 美國西部 2          |
| 美國西部             | 美國東部            |
| 美國西部 2           | 美國中西部    |

## <a name="azure-file-sync-agent-update-policy"></a>Azure 檔案同步代理程式更新原則
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="recommended-azure-file-sync-machine-configuration"></a>建議的 Azure 檔案同步電腦設定

Azure 檔案同步的機器需求取決於命名空間中的物件數目和資料集的變換。 單一伺服器可以附加到多個同步處理群組，以及下表中列出的物件數目，以取得伺服器所附加的完整命名空間。 例如，具有10000000物件的伺服器端點 A + 具有10000000物件的伺服器端點 B = 20000000 物件。 針對該範例部署，建議您8CPU、16GiB 穩定狀態的記憶體，以及（如果可能的話）48GiB 記憶體以進行初始遷移。
 
基於效能考慮，命名空間資料會儲存在記憶體中。 因此，較大的命名空間會需要更多記憶體來維持良好的效能，而更多變換則需要更多的 CPU 才能處理。 
 
在下表中，我們提供了命名空間的大小以及一般一般用途檔案共用的容量轉換，其中平均檔案大小為512KiB。 如果您的檔案大小較小，請考慮為相同容量增加額外的記憶體。 根據命名空間的大小，以您的記憶體設定為基礎。

| 命名空間大小-檔案 & 目錄（百萬）  | 一般容量（TiB）  | CPU 核心  | 建議的記憶體（GiB） |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8（初始同步處理）/2 （一般變換）      |
| 5        | 2.4     | 2        | 16（初始同步）/4 （一般變換）    |
| 10       | 4.8     | 4        | 32（初始同步處理）/8 （一般變換）   |
| 30       | 14.3    | 8        | 48（初始同步處理）/16 （一般變換）   |
| 50       | 23.8    | 16       | 64（初始同步處理）/32 （一般變換）  |
| 100*     | 47.7   | 32       | 128（初始同步處理）/32 （一般變換）  |

\*超過100000000個檔案 & 目錄尚未經過測試。 這是軟性限制。

> [!TIP]
> 命名空間的初始同步處理是一項密集的作業，我們建議您在完成初始同步處理之前，配置更多記憶體。 這不是必要的，但可能會加速初始同步處理。 
> 
> 一般變換是每日變更的0.5% 命名空間。 如需更高的變換層級，請考慮新增更多 CPU。 

## <a name="next-steps"></a>後續步驟
* [考量防火牆和 Proxy 設定](storage-sync-files-firewall-and-proxy.md)
* [規劃 Azure 檔案部署](storage-files-planning.md)
* [部署 Azure 檔案服務](storage-files-deployment-guide.md)
* [部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)
* [監視 Azure 檔案同步](storage-sync-files-monitoring.md)
