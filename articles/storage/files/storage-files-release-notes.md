---
title: Azure 檔案同步代理程式的版本資訊 | Microsoft Docs
description: Azure 檔案同步代理程式的版本資訊。
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 7/28/2020
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 76b55f7f50f688adb2b35a1b8ed596818cdfac19
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87372853"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Azure 檔案同步代理程式的版本資訊
Azure 檔案同步可讓您將組織的檔案共用集中在「Azure 檔案服務」中，而不需要犧牲內部部署檔案伺服器的靈活度、效能及相容性。 您的 Windows Server 安裝會轉換成 Azure 檔案共用的快速快取。 您可以使用 Windows Server 上可用的任何通訊協定來從本機存取資料 (包括 SMB、NFS 和 FTPS)。 您可以視需要存取多個散佈於世界各地的快取。

本文提供 Azure 檔案同步代理程式支援版本的版本資訊。

## <a name="supported-versions"></a>支援的版本
Azure 檔案同步代理程式支援下列版本：

| 里程碑 | 代理程式版本號碼 | 發行日期 | 狀態 |
|----|----------------------|--------------|------------------|
| 10.1 版本- [KB4522411](https://support.microsoft.com/en-us/help/4522411)| 10.1.0.0 | 2020年6月5日 | 支援 |
| 2020 年 5 月更新彙總套件 - [KB4522412](https://support.microsoft.com/help/4522412)| 10.0.2.0 | 2020 年 5 月 19 日 | 支援 |
| V10 發行版本 - [KB4522409](https://support.microsoft.com/en-us/help/4522409)| 10.0.0.0 | 2020 年 4 月 9 日 | 支援 |
| 2019 年 12 月更新彙總套件 - [KB4522360](https://support.microsoft.com/help/4522360)| 9.1.0.0 | 2019 年 12 月 12 日 | 支援 |
| V9 發行版本 - [KB4522359](https://support.microsoft.com/help/4522359)| 9.0.0.0 | 2019 年 12 月 2 日 | 支援 |
| V8 發行版本 - [KB4511224](https://support.microsoft.com/help/4511224)| 8.0.0.0 | 2019 年 10 月 8 日 | 支援 |
| 2019 年 7 月更新彙總套件 - [KB4490497](https://support.microsoft.com/help/4490497)| 7.2.0.0 | 2019 年 7 月 24 日 | 支援的代理程式版本將于2020年9月1日到期 |
| 2019 年 7 月更新彙總套件 - [KB4490496](https://support.microsoft.com/help/4490496)| 7.1.0.0 | 2019 年 7 月 12 日 | 支援的代理程式版本將于2020年9月1日到期 |
| V7 發行版本- [KB4490495](https://support.microsoft.com/help/4490495)| 7.0.0.0 | 2019 年 6 月 19 日 | 支援的代理程式版本將于2020年9月1日到期 |
| V6 發行版本 | 6.0.0.0 - 6.3.0.0 | N/A | 不支援 - 代理程式版本已於 2020 年 4 月 21 日過期 |
| V5 發行版本 | 5.0.2.0 - 5.2.0.0 | N/A | 不支援 - 代理程式版本已於 2020 年 3 月 18 日過期 |
| V4 版本 | 4.0.1.0 - 4.3.0.0 | N/A | 不支援 - 代理程式版本已於 2019 年 11 月 6 日過期 |
| V3 發行版本 | 3.1.0.0 - 3.4.0.0 | N/A | 不支援 - 代理程式版本已於 2019 年 8 月 19 日過期 |
| 預先正式發行的代理程式 | 1.1.0.0 - 3.0.13.0 | N/A | 不支援 - 代理程式版本已於 2018 年 10 月1 日過期 |

### <a name="azure-file-sync-agent-update-policy"></a>Azure 檔案同步代理程式更新原則
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-10100"></a>代理程式版本10.1.0。0
下列版本資訊適用于2020年6月5日發行之 Azure 檔案同步代理程式的版本10.1.0.0。 這些注意事項是針對10.0.0.0 和10.0.2.0 版所列出的版本資訊以外的資訊。

### <a name="improvements-and-issues-that-are-fixed"></a>增強功能和已修正的問題

- Azure 私用端點支援
    - 儲存體同步服務的同步流量現在可以傳送至私人端點。 這可讓您透過 ExpressRoute 或 VPN 連線進行通道處理。 若要深入瞭解，請參閱設定[Azure 檔案同步網路端點](https://docs.microsoft.com/azure/storage/files/storage-sync-files-networking-endpoints)。
- [已同步處理的檔案] 計量現在會在執行大型同步處理時顯示進度，而不是結束。
- 代理程式安裝、雲端階層處理、同步和遙測的其他可靠性改進

## <a name="agent-version-10020"></a>代理程式版本 10.0.2.0
下列版本資訊適用於 Azure 檔案同步代理程式版本 10.0.2.0 (2020 年 5 月 19 日發行)。 這些是針對版本 10.0.0.0 所列的版本資訊以外的版本資訊。

此發行版本所修正的問題：  
- 在安裝 Azure 檔案同步 V10 代理程式之後，儲存體同步代理程式 (FileSyncSvc) 經常當機。

> [!Note]  
>當有新版本可用時，此發行版本不會發行到設定為自動更新的伺服器。 若要安裝此更新，請使用 Microsoft Update 或 Microsoft Update Catalog (安裝指示請參閱 [KB4522412](https://support.microsoft.com/help/4522412))。

## <a name="agent-version-10000"></a>代理程式版本 10.0.0.0
下列版本資訊適用於 Azure 檔案同步代理程式版本 10.0.0.0 (2020 年 4 月 9 日發行)。

### <a name="improvements-and-issues-that-are-fixed"></a>增強功能和已修正的問題

- 改善入口網站的同步處理程序
    - 在 V10 代理程式發行版本中，Azure 入口網站很快就會開始顯示執行中的同步處理工作階段類型。 例如 初始下載、定期下載、背景重新叫用 (快速災害復原案例) 和類似情況。 

- 改進雲端階層處理入口網站體驗
    - 如果您有無法進行階層處理或重新叫用的檔案，您現在可以在伺服器端點屬性中查看階層處理錯誤。
    - 伺服器端點也可以使用其他雲端階層處理資訊：
        - 本機快取大小
        - 快取使用方式效率
        - 雲端階層處理原則詳細資料：磁碟區大小、目前的可用空間，或本機快取中最舊檔案的上次存取時間。
    - 這些變更將會在 V10 代理程式初始發行後不久於 Azure 入口網站中寄送。

- 支援將儲存體同步服務及/或儲存體帳戶移至不同的 Azure Active Directory (AAD) 租用戶
    - Azure 檔案同步現在支援將儲存體同步服務及/或儲存體帳戶移至不同的資源群組、訂用帳戶或 Azure AD 租用戶。
    
- 其他效能和可靠性改善
    - 如果在儲存體帳戶設定了虛擬網路 (VNET) 和防火牆規則，Azure 檔案共用上的變更偵測可能會失敗。
    - 減少與重新叫用相關聯的記憶體耗用。 
    - 改善使用 [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) Cmdlet 時的效能。
    - 其他針對可靠性的改進。 
    
### <a name="evaluation-tool"></a>評估工具
在部署 Azure 檔案同步之前，您應該使用 Azure 檔案同步評估工具來評估其是否與您的系統相容。 此工具是 Azure PowerShell Cmdlet，可檢查檔案系統和資料集的潛在問題，例如不支援的字元或 OS 版本。 關於安裝和使用方式指示，請參閱規劃指南中[評估工具](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet)小節。 

### <a name="agent-installation-and-server-configuration"></a>代理程式安裝和伺服器設定
如需如何使用 Windows Server 安裝及設定 Azure 檔案同步代理程式的詳細資訊，請參閱[規劃 Azure 檔案同步部署](storage-sync-files-planning.md)和[如何部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)。

- 必須以提升的 (系統管理員) 權限安裝代理程式安裝套件。
- Nano Server 部署選項不支援此代理程式。
- 只有 Windows Server 2019、Windows Server 2016 和 Windows Server 2012 R2 可支援此代理程式。
- 代理程式需要至少 2 GiB 的記憶體。 如果伺服器在啟用動態記憶體的虛擬機器中執行，則 VM 的記憶體應最少設定為 2048 MiB。
- 在已將系統磁碟區資訊 (SVI) 目錄壓縮的磁碟區上，儲存體同步代理程式 (FileSyncSvc) 服務不支援其中的伺服器端點。 此設定會導致非預期的結果。

### <a name="interoperability"></a>互通性
- 防毒程式、備份及其他存取階層式檔案的應用程式，除非採用離線屬性並略過讀取這些檔案的內容，否則將會導致不想要的重新叫用。 如需詳細資訊，請參閱[針對 Azure 檔案同步進行疑難排解](storage-sync-files-troubleshoot.md)。
- 如果檔案因為檔案檢測而遭到封鎖，檔案伺服器資源管理員 (FSRM) 檔案檢測會造成無止盡的同步處理失敗。
- 不支援在已安裝 Azure 檔案同步代理程式的伺服器上執行 sysprep，而且此舉可能會導致非預期的結果。 在部署伺服器映像並完成 sysprep 迷你設定之後，請安裝 Azure 檔案同步代理程式。

### <a name="sync-limitations"></a>同步限制
下列項目不會同步，但是系統的其餘部分會繼續正常運作：
- 具有不受支援字元的檔案。 如需不受支援字元的清單，請參閱[疑難排解指南](storage-sync-files-troubleshoot.md#handling-unsupported-characters)。
- 以句點結尾的檔案或目錄。
- 長度超過 2048 個字元的路徑。
- 用於稽核之安全性描述元的系統存取控制清單 (SACL) 部分。
- 擴充屬性。
- 替代資料流。
- 重新分析點。
- 永久連結。
- 如果變更從其他端點同步至該檔案，則不會保存壓縮 (如果設定於伺服器檔案上)。
- 任何使用 EFS (或其他使用者模式加密) 加密的檔案，會阻止服務讀取資料。

    > [!Note]  
    > Azure 檔案同步永遠會加密傳輸中的資料。 資料待用時一律會在 Azure 中加密。
 
### <a name="server-endpoint"></a>伺服器端點
- 只能在 NTFS 磁碟區上建立伺服器端點。 Azure 檔案同步目前不支援 ReFS、FAT、FAT32 及其他檔案系統。
- 系統磁碟區上不支援雲端階層。 若要在系統磁碟區上建立伺服器端點，請於建立伺服器端點時，停用雲端階層處理。
- 只有叢集磁碟才支援容錯移轉叢集，但叢集共用磁碟區 (CSV) 不提供支援。
- 伺服器端點無法為巢狀。 它可與其他端點平行並存於相同的磁碟區上。
- 請勿在伺服器端點位置內儲存 OS 或應用程式分頁檔。
- 如果將伺服器重新命名，並不會更新入口網站中的伺服器名稱。

### <a name="cloud-endpoint"></a>雲端端點
- Azure 檔案同步支援直接對 Azure 檔案共用進行變更。 不過，在 Azure 檔案共用上所做的任何變更，都必須先由 Azure 檔案同步變更偵測作業做出探索。 針對雲端端點的變更偵測作業，每隔 24 小時會起始一次。 若要立即同步處理 Azure 檔案共用中變更的檔案，您可以使用 [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell Cmdlet，手動在 Azure 檔案共用中起始變更偵測。 此外，透過 REST 通訊協定對 Azure 檔案共用所做的變更，將不會更新 SMB 上次修改時間，而且將不會同步顯示為變更。
- 可以將儲存體同步服務及/或儲存體帳戶移至不同的資源群組、訂用帳戶或 Azure AD 租使用者。 移動儲存體同步服務或儲存體帳戶之後，您必須將儲存體帳戶的存取權授與 Microsoft.storagesync 應用程式（請參閱[確認 Azure 檔案同步有儲存體帳戶的存取](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)權）。

    > [!Note]  
    > 建立雲端端點時，儲存體同步服務和儲存體帳戶必須位於相同的 Azure AD 租用戶中。 建立雲端端點之後，可以將儲存體同步服務和儲存體帳戶移至不同的 Azure AD 租用戶。

### <a name="cloud-tiering"></a>雲端階層處理
- 如果使用 Robocopy 將階層式檔案複製到另一個位置，則所產生的檔案不會進行階層處理。 因為 Robocopy 未正確地在複製作業中包含該屬性，所以可能設定離線屬性。
- 使用 robocopy 複製檔案時，請使用 /MIR 選項來保留檔案的時間戳記。 這可確保較舊的檔案會比最近存取的檔案更快進行階層處理。

## <a name="agent-version-9100"></a>代理程式版本 9.1.0.0
下列版本資訊適用於 Azure 檔案同步代理程式版本 9.1.0.0 (2019 年 12 月 12 日發行)。 這些是針對版本 9.0.0.0 所列的版本資訊以外的版本資訊。

此發行版本所修正的問題：  
- 升級至 Azure 檔案同步代理程式 9.0 版之後，同步處理失敗，發生下列其中一個錯誤：
    - 0x8e5e044e (JET_errWriteConflict)
    - 0x8e5e0450 (JET_errInvalidSesid)
    - 0x8e5e0442 (JET_errInstanceUnavailable)

## <a name="agent-version-9000"></a>代理程式版本 9.0.0.0
下列版本資訊適用於 Azure 檔案同步代理程式版本 9.0.0.0 (2019 年 12 月 2 日發行)。

### <a name="improvements-and-issues-that-are-fixed"></a>增強功能和已修正的問題

- 自助式還原支援
    - 使用者現在可以使用「先前版本」功能還原其檔案。 在 V9 版本之前，已啟用雲端階層處理的磁碟區不支援先前版本功能。 您必須分別為每個磁碟區啟用這項功能，且在磁碟區上存在已啟用雲端階層處理的端點。 若要深入了解，請參閱：  
[透過先前版本和 VSS 進行自助式還原 (磁碟區陰影複製服務)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service)。 
 
- 支援較大的檔案共用大小 
    - Azure 檔案同步現在支援在單一同步處理的命名空間中最高 64TB 和 1 億個檔案。  
 
- Server 2019 支援重複資料刪除 
    - 在 Windows Server 2019 上啟用的雲端階層處理現已支援重複資料刪除。 若要在具有雲端階層處理的磁碟區上支援重複資料刪除，必須安裝 Windows Update [KB4520062](https://support.microsoft.com/help/4520062)。 
 
- 階層處理的檔案大小下限改進 
    - 要階層處理的檔案現在的檔案大小下限是根據檔案系統叢集大小 (為檔案系統叢集大小的兩倍)。 例如，根據預設，NTFS 檔案系統叢集大小為 4 KB，所以要階層處理的檔案大小下限即為 8 KB。 
 
- 網路連線能力測試 Cmdlet 
    - 在 Azure 檔案同步設定的過程中，必須聯繫多個服務端點。 它們各有自己的 DNS 名稱，且名稱必須可供伺服器存取。 這些 URL 也是在伺服器註冊區域中專屬。 一旦註冊伺服器，就可以使用連線能力測試 Cmdlet (PowerShell 和伺服器註冊公用程式) 來測試與此伺服器專屬 URL 的所有通訊。 當未完成的通訊防止伺服器與 Azure 檔案同步完全合作時，此 Cmdlet 有助於疑難排解，而且還可以用來微調 Proxy 和防火牆設定。  
 
        若要執行網路連線能力測試，請執行下列 PowerShell 命令： 
 
        Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"  
        Test-StorageSyncNetworkConnectivity
 
- 啟用雲端階層處理時可移除伺服器端點的改進 
    - 如先前所述，移除伺服器端點並不會移除 Azure 檔案共用中的檔案。 不過，本機伺服器上重新剖析點的行為已變更。 現在，移除伺服器端點時，會刪除重新剖析點 (為指向伺服器上非本機檔案的指標)。 已完整快取的檔案仍會保留在伺服器上。 這項改進是為了防止在移除伺服器端點時產生[孤立的階層式檔案](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)。 如果重新建立伺服器端點，則會在伺服器上重新建立階層式檔案的重新剖析點。  
 
- 改善效能和可靠性 
    - 降低了重新叫用失敗。 重新叫用的大小現在會根據網路頻寬自動調整。 
    - 改進了將新伺服器加入同步處理群組時的下載效能。 
    - 減少了因為條件約束衝突而無法同步的檔案。 
    - 當伺服器端點路徑是磁碟區掛接點時，在某些情況下，檔案無法進行階層處理或會意外重新叫用。
    
### <a name="evaluation-tool"></a>評估工具
在部署 Azure 檔案同步之前，您應該使用 Azure 檔案同步評估工具來評估其是否與您的系統相容。 此工具是 Azure PowerShell Cmdlet，可檢查檔案系統和資料集的潛在問題，例如不支援的字元或 OS 版本。 關於安裝和使用方式指示，請參閱規劃指南中[評估工具](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet)小節。 

### <a name="agent-installation-and-server-configuration"></a>代理程式安裝和伺服器設定
如需如何使用 Windows Server 安裝及設定 Azure 檔案同步代理程式的詳細資訊，請參閱[規劃 Azure 檔案同步部署](storage-sync-files-planning.md)和[如何部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)。

- 必須以提升的 (系統管理員) 權限安裝代理程式安裝套件。
- Nano Server 部署選項不支援此代理程式。
- 只有 Windows Server 2019、Windows Server 2016 和 Windows Server 2012 R2 可支援此代理程式。
- 代理程式需要至少 2 GiB 的記憶體。 如果伺服器在啟用動態記憶體的虛擬機器中執行，則 VM 的記憶體應最少設定為 2048 MiB。
- 在已將系統磁碟區資訊 (SVI) 目錄壓縮的磁碟區上，儲存體同步代理程式 (FileSyncSvc) 服務不支援其中的伺服器端點。 此設定會導致非預期的結果。

### <a name="interoperability"></a>互通性
- 防毒程式、備份及其他存取階層式檔案的應用程式，除非採用離線屬性並略過讀取這些檔案的內容，否則將會導致不想要的重新叫用。 如需詳細資訊，請參閱[針對 Azure 檔案同步進行疑難排解](storage-sync-files-troubleshoot.md)。
- 如果檔案因為檔案檢測而遭到封鎖，檔案伺服器資源管理員 (FSRM) 檔案檢測會造成無止盡的同步處理失敗。
- 不支援在已安裝 Azure 檔案同步代理程式的伺服器上執行 sysprep，而且此舉可能會導致非預期的結果。 在部署伺服器映像並完成 sysprep 迷你設定之後，請安裝 Azure 檔案同步代理程式。

### <a name="sync-limitations"></a>同步限制
下列項目不會同步，但是系統的其餘部分會繼續正常運作：
- 具有不受支援字元的檔案。 如需不受支援字元的清單，請參閱[疑難排解指南](storage-sync-files-troubleshoot.md#handling-unsupported-characters)。
- 以句點結尾的檔案或目錄。
- 長度超過 2048 個字元的路徑。
- 安全性描述元的判別存取控制清單 (DACL) 部分 (如果大於 2 KB)。 (這個問題僅適用於單一項目上有超過 40 個存取控制項目 (ACE) 時。)
- 用於稽核之安全性描述元的系統存取控制清單 (SACL) 部分。
- 擴充屬性。
- 替代資料流。
- 重新分析點。
- 永久連結。
- 如果變更從其他端點同步至該檔案，則不會保存壓縮 (如果設定於伺服器檔案上)。
- 任何使用 EFS (或其他使用者模式加密) 加密的檔案，會阻止服務讀取資料。

    > [!Note]  
    > Azure 檔案同步永遠會加密傳輸中的資料。 資料待用時一律會在 Azure 中加密。
 
### <a name="server-endpoint"></a>伺服器端點
- 只能在 NTFS 磁碟區上建立伺服器端點。 Azure 檔案同步目前不支援 ReFS、FAT、FAT32 及其他檔案系統。
- 如果未在刪除伺服器端點之前回收階層式檔案，階層式檔案會變成無法存取的狀態。 若要還原檔案的存取性，請重新建立伺服器端點。 如果在刪除伺服器端點後過了 30 天，或如果雲端端點已刪除，將會無法使用未重新呼叫的階層式檔案。 若要深入了解，請參閱[刪除伺服器端點之後，無法存取伺服器上的階層式檔案](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)。
- 系統磁碟區上不支援雲端階層。 若要在系統磁碟區上建立伺服器端點，請於建立伺服器端點時，停用雲端階層處理。
- 只有叢集磁碟才支援容錯移轉叢集，但叢集共用磁碟區 (CSV) 不提供支援。
- 伺服器端點無法為巢狀。 它可與其他端點平行並存於相同的磁碟區上。
- 請勿在伺服器端點位置內儲存 OS 或應用程式分頁檔。
- 如果將伺服器重新命名，並不會更新入口網站中的伺服器名稱。

### <a name="cloud-endpoint"></a>雲端端點
- Azure 檔案同步支援直接對 Azure 檔案共用進行變更。 不過，在 Azure 檔案共用上所做的任何變更，都必須先由 Azure 檔案同步變更偵測作業做出探索。 針對雲端端點的變更偵測作業，每隔 24 小時會起始一次。 若要立即同步處理 Azure 檔案共用中變更的檔案，您可以使用 [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell Cmdlet，手動在 Azure 檔案共用中起始變更偵測。 此外，透過 REST 通訊協定對 Azure 檔案共用所做的變更，將不會更新 SMB 上次修改時間，而且將不會同步顯示為變更。
- 可將儲存體同步服務及 (或) 儲存體帳戶移至現有 Azure AD 租用戶中的不同資源群組或訂用帳戶。 如果移動儲存體帳戶，您需要將儲存體帳戶的存取權給予混合式檔案同步服務 (請參閱[確保 Azure 檔案同步有儲存體帳戶的存取權](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac))。

    > [!Note]  
    > Azure 檔案同步不支援將訂用帳戶移至不同的 Azure AD 租用戶。

### <a name="cloud-tiering"></a>雲端階層處理
- 如果使用 Robocopy 將階層式檔案複製到另一個位置，則所產生的檔案不會進行階層處理。 因為 Robocopy 未正確地在複製作業中包含該屬性，所以可能設定離線屬性。
- 使用 robocopy 複製檔案時，請使用 /MIR 選項來保留檔案的時間戳記。 這可確保較舊的檔案會比最近存取的檔案更快進行階層處理。
- 如果 pagefile.sys 位於已啟用雲端階層處理的磁碟區上，則檔案可能無法進行階層處理。 pagefile.sys 應該位於已停用雲端階層處理的磁碟區上。

## <a name="agent-version-8000"></a>代理程式版本 8.0.0.0
下列版本資訊適用於 Azure 檔案同步代理程式 8.0.0.0 版 (2019 年 10 月 8 日發行)。

### <a name="improvements-and-issues-that-are-fixed"></a>增強功能和已修正的問題

- 還原效能改進
    - 透過 Azure 備份進行復原的復原時間更快。 還原的檔案會更快地同步到 Azure 檔案同步伺服器。 
- 改進雲端階層處理入口網站體驗  
    - 現在，如果您有無法重新叫用的階層式檔案，可以在伺服器端點屬性中查看重新叫用錯誤。 此外，如果伺服器上未載入雲端階層處理的篩選驅動程式，伺服器端點健康情況現在會顯示錯誤和風險降低步驟。
- 更簡單的代理程式安裝
    - Az\AzureRM PowerShell 模組已不再需要註冊伺服器，讓安裝變得更簡單快速。
- 其他效能和可靠性改善

### <a name="evaluation-tool"></a>評估工具
在部署 Azure 檔案同步之前，您應該使用 Azure 檔案同步評估工具來評估其是否與您的系統相容。 此工具是 Azure PowerShell Cmdlet，可檢查檔案系統和資料集的潛在問題，例如不支援的字元或 OS 版本。 關於安裝和使用方式指示，請參閱規劃指南中[評估工具](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet)小節。 

### <a name="agent-installation-and-server-configuration"></a>代理程式安裝和伺服器設定
如需如何使用 Windows Server 安裝及設定 Azure 檔案同步代理程式的詳細資訊，請參閱[規劃 Azure 檔案同步部署](storage-sync-files-planning.md)和[如何部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)。

- 必須以提升的 (系統管理員) 權限安裝代理程式安裝套件。
- Nano Server 部署選項不支援此代理程式。
- 只有 Windows Server 2019、Windows Server 2016 和 Windows Server 2012 R2 可支援此代理程式。
- 代理程式需要至少 2 GiB 的記憶體。 如果伺服器在啟用動態記憶體的虛擬機器中執行，則 VM 的記憶體應最少設定為 2048 MiB。
- 在已將系統磁碟區資訊 (SVI) 目錄壓縮的磁碟區上，儲存體同步代理程式 (FileSyncSvc) 服務不支援其中的伺服器端點。 此設定會導致非預期的結果。

### <a name="interoperability"></a>互通性
- 防毒程式、備份及其他存取階層式檔案的應用程式，除非採用離線屬性並略過讀取這些檔案的內容，否則將會導致不想要的重新叫用。 如需詳細資訊，請參閱[針對 Azure 檔案同步進行疑難排解](storage-sync-files-troubleshoot.md)。
- 如果檔案因為檔案檢測而遭到封鎖，檔案伺服器資源管理員 (FSRM) 檔案檢測會造成無止盡的同步處理失敗。
- 不支援在已安裝 Azure 檔案同步代理程式的伺服器上執行 sysprep，而且此舉可能會導致非預期的結果。 在部署伺服器映像並完成 sysprep 迷你設定之後，請安裝 Azure 檔案同步代理程式。

### <a name="sync-limitations"></a>同步限制
下列項目不會同步，但是系統的其餘部分會繼續正常運作：
- 具有不受支援字元的檔案。 如需不受支援字元的清單，請參閱[疑難排解指南](storage-sync-files-troubleshoot.md#handling-unsupported-characters)。
- 以句點結尾的檔案或目錄。
- 長度超過 2048 個字元的路徑。
- 安全性描述元的判別存取控制清單 (DACL) 部分 (如果大於 2 KB)。 (這個問題僅適用於單一項目上有超過 40 個存取控制項目 (ACE) 時。)
- 用於稽核之安全性描述元的系統存取控制清單 (SACL) 部分。
- 擴充屬性。
- 替代資料流。
- 重新分析點。
- 永久連結。
- 如果變更從其他端點同步至該檔案，則不會保存壓縮 (如果設定於伺服器檔案上)。
- 任何使用 EFS (或其他使用者模式加密) 加密的檔案，會阻止服務讀取資料。

    > [!Note]  
    > Azure 檔案同步永遠會加密傳輸中的資料。 資料待用時一律會在 Azure 中加密。
 
### <a name="server-endpoint"></a>伺服器端點
- 只能在 NTFS 磁碟區上建立伺服器端點。 Azure 檔案同步目前不支援 ReFS、FAT、FAT32 及其他檔案系統。
- 如果未在刪除伺服器端點之前回收階層式檔案，階層式檔案會變成無法存取的狀態。 若要還原檔案的存取性，請重新建立伺服器端點。 如果在刪除伺服器端點後過了 30 天，或如果雲端端點已刪除，將會無法使用未重新呼叫的階層式檔案。 若要深入了解，請參閱[刪除伺服器端點之後，無法存取伺服器上的階層式檔案](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)。
- 系統磁碟區上不支援雲端階層。 若要在系統磁碟區上建立伺服器端點，請於建立伺服器端點時，停用雲端階層處理。
- 只有叢集磁碟才支援容錯移轉叢集，但叢集共用磁碟區 (CSV) 不提供支援。
- 伺服器端點無法為巢狀。 它可與其他端點平行並存於相同的磁碟區上。
- 請勿在伺服器端點位置內儲存 OS 或應用程式分頁檔。
- 如果將伺服器重新命名，並不會更新入口網站中的伺服器名稱。

### <a name="cloud-endpoint"></a>雲端端點
- Azure 檔案同步支援直接對 Azure 檔案共用進行變更。 不過，在 Azure 檔案共用上所做的任何變更，都必須先由 Azure 檔案同步變更偵測作業做出探索。 針對雲端端點的變更偵測作業，每隔 24 小時會起始一次。 若要立即同步處理 Azure 檔案共用中變更的檔案，您可以使用 [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell Cmdlet，手動在 Azure 檔案共用中起始變更偵測。 此外，透過 REST 通訊協定對 Azure 檔案共用所做的變更，將不會更新 SMB 上次修改時間，而且將不會同步顯示為變更。
- 可將儲存體同步服務及 (或) 儲存體帳戶移至現有 Azure AD 租用戶中的不同資源群組或訂用帳戶。 如果移動儲存體帳戶，您需要將儲存體帳戶的存取權給予混合式檔案同步服務 (請參閱[確保 Azure 檔案同步有儲存體帳戶的存取權](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac))。

    > [!Note]  
    > Azure 檔案同步不支援將訂用帳戶移至不同的 Azure AD 租用戶。

### <a name="cloud-tiering"></a>雲端階層處理
- 如果使用 Robocopy 將階層式檔案複製到另一個位置，則所產生的檔案不會進行階層處理。 因為 Robocopy 未正確地在複製作業中包含該屬性，所以可能設定離線屬性。
- 使用 robocopy 複製檔案時，請使用 /MIR 選項來保留檔案的時間戳記。 這可確保較舊的檔案會比最近存取的檔案更快進行階層處理。

## <a name="agent-version-7200"></a>代理程式版本 7.2.0.0
下列版本資訊適用於 Azure 檔案同步代理程式版本 7.2.0.0 (2019 年 7 月 24 日發行)。 這些是針對版本 7.0.0.0 所列的版本資訊以外的版本資訊。

此版本修正的問題清單：  
- 如果 Proxy 設定為 null，儲存體同步代理程式 (FileSyncSvc) 會當機。
- 如果伺服器上有多個端點具有相同名稱，伺服器端點就會起始 BCDR (0x80c80257 - ECS_E_BCDR_IN_PROGRESS 錯誤)。
- 雲端階層處理可靠性改進。

## <a name="agent-version-7100"></a>代理程式版本 7.1.0.0
下列版本資訊適用於 Azure 檔案同步代理程式版本 7.1.0.0 (2019 年 7 月 12 日發行)。 這些是針對版本 7.0.0.0 所列的版本資訊以外的版本資訊。

此版本修正的問題清單：  
- 在 Windows Server 2012 R2 上，透過 SMB 存取或瀏覽伺服器端點位置的速度很慢。 
- 提高了安裝 Azure 檔案同步 V6 代理程式之後的 CPU 使用率。
- 雲端階層處理遙測改進。
- 針對雲端階層處理和同步處理的其他可靠性改進。

## <a name="agent-version-7000"></a>代理程式版本 7.0.0.0
下列版本資訊適用於 Azure 檔案同步代理程式版本 7.0.0.0 (2019 年 6 月 19 日發行)。

### <a name="improvements-and-issues-that-are-fixed"></a>增強功能和已修正的問題

- 支援較大的檔案共用大小
    - 有了較大型 Azure 檔案共用的預覽，我們也增加了檔案同步的支援上限。 在此第一步驟中，Azure 檔案同步現在支援在單一同步處理的命名空間中最高 25TB 和 5 千萬個檔案。 若要申請此大型檔案共用預覽，請填寫此表單 https://aka.ms/azurefilesatscalesurvey 。 
- 支援儲存體帳戶的防火牆和虛擬網路設定
    - Azure 檔案同步現在支援儲存體帳戶上的防火牆和虛擬網路設定。 若要將您的部署設定為使用防火牆和虛擬網路設定，請參閱[設定防火牆和虛擬網路設定](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)。
- 變更了 Azure 檔案共用中用來立即同步處理 檔案的 PowerShell Cmdlet
    - 若要立即同步處理 Azure 檔案共用中變更的檔案，您可以使用 Invoke-AzStorageSyncChangeDetection PowerShell Cmdlet，手動在 Azure 檔案共用中起始變更偵測。 此 Cmdlet 是用於在 Azure 檔案共用中以自動化程序進行變更，或是由系統管理員完成變更的案例 (例如將檔案和目錄移到共用中)。 至於使用者變更，建議您在 IaaS VM 中安裝 Azure 檔案同步代理程式，並讓使用者透過 IaaS VM 存取檔案共用。 如此一來，所有變更都會快速同步處理至其他代理程式，而不需要使用 Invoke-AzStorageSyncChangeDetection Cmdlet。 若要深入瞭解，請參閱 [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) 文件。
- 改進入口網站體驗 (如果您遇到檔案未同步的情況)
    - 如果您有同步失敗的檔案，我們現在可以在入口網站中區別是暫時性或持續性錯誤。 暫時性錯誤通常會自行解決，不需要系統管理動作。 例如，目前使用中的檔案將不會同步，直到檔案控制代碼關閉為止。 針對持續性錯誤，我們現在會顯示受到每個錯誤影響的檔案數目。 持續性錯誤計數也會顯示在同步處理群組中所有伺服器端點的「檔案無法同步」資料行中。
- 改進了 Azure 備份的檔案層級還原
    - 現在，系統會偵測到使用 Azure 備份還原的個別檔案，並更快地同步到伺服器端點。
- 改進了雲端階層處理重新叫用 Cmdlet 的可靠性 
    - 現在，Invoke-StorageSyncFileRecall Cmdlet 可讓客戶指定每個檔案重試計數，以及每個檔案重試延遲 (類似 Robocopy)。 之前，此 Cmdlet 會以隨機順序重新叫用指定路徑下的所有階層式檔案。 使用新的 -Order 參數時，這個 Cmdlet 會先重新叫用最熱門的資料，並接受雲端階層處理原則 (如果符合日期原則或符合磁碟區可用空間，則停止重新呼叫，以先發生者為準)。
- 僅支援 TLS 1.2 (已停用 TLS 1.0 和 1.1)
    - 現在，Azure 檔案同步只有在已停用 TLS 1.0 和 1.1 的伺服器上，才支援使用 TLS 1.2。 在此改進之前，如果伺服器上已停用 TLS 1.0 和 1.1，伺服器註冊將會失敗。
- 同步處理和雲端階層處理的其他效能和可靠性改進
    - 此發行版本有數個可靠性和效能改進。 其中一些的目標是要讓雲端階層處理更有效率，並在您已設定頻寬節流排程的情況下，讓 Azure 檔案同步的整體運作更完善。

### <a name="evaluation-tool"></a>評估工具
在部署 Azure 檔案同步之前，您應該使用 Azure 檔案同步評估工具來評估其是否與您的系統相容。 此工具是 Azure PowerShell Cmdlet，可檢查檔案系統和資料集的潛在問題，例如不支援的字元或 OS 版本。 關於安裝和使用方式指示，請參閱規劃指南中[評估工具](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet)小節。 

### <a name="agent-installation-and-server-configuration"></a>代理程式安裝和伺服器設定
如需如何使用 Windows Server 安裝及設定 Azure 檔案同步代理程式的詳細資訊，請參閱[規劃 Azure 檔案同步部署](storage-sync-files-planning.md)和[如何部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)。

- 必須以提升的 (系統管理員) 權限安裝代理程式安裝套件。
- Nano Server 部署選項不支援此代理程式。
- 只有 Windows Server 2019、Windows Server 2016 和 Windows Server 2012 R2 可支援此代理程式。
- 代理程式需要至少 2 GiB 的記憶體。 如果伺服器在啟用動態記憶體的虛擬機器中執行，則 VM 的記憶體應最少設定為 2048 MiB。
- 在已將系統磁碟區資訊 (SVI) 目錄壓縮的磁碟區上，儲存體同步代理程式 (FileSyncSvc) 服務不支援其中的伺服器端點。 此設定會導致非預期的結果。

### <a name="interoperability"></a>互通性
- 防毒程式、備份及其他存取階層式檔案的應用程式，除非採用離線屬性並略過讀取這些檔案的內容，否則將會導致不想要的重新叫用。 如需詳細資訊，請參閱[針對 Azure 檔案同步進行疑難排解](storage-sync-files-troubleshoot.md)。
- 如果檔案因為檔案檢測而遭到封鎖，檔案伺服器資源管理員 (FSRM) 檔案檢測會造成無止盡的同步處理失敗。
- 不支援在已安裝 Azure 檔案同步代理程式的伺服器上執行 sysprep，而且此舉可能會導致非預期的結果。 在部署伺服器映像並完成 sysprep 迷你設定之後，請安裝 Azure 檔案同步代理程式。

### <a name="sync-limitations"></a>同步限制
下列項目不會同步，但是系統的其餘部分會繼續正常運作：
- 具有不受支援字元的檔案。 如需不受支援字元的清單，請參閱[疑難排解指南](storage-sync-files-troubleshoot.md#handling-unsupported-characters)。
- 以句點結尾的檔案或目錄。
- 長度超過 2048 個字元的路徑。
- 安全性描述元的判別存取控制清單 (DACL) 部分 (如果大於 2 KB)。 (這個問題僅適用於單一項目上有超過 40 個存取控制項目 (ACE) 時。)
- 用於稽核之安全性描述元的系統存取控制清單 (SACL) 部分。
- 擴充屬性。
- 替代資料流。
- 重新分析點。
- 永久連結。
- 如果變更從其他端點同步至該檔案，則不會保存壓縮 (如果設定於伺服器檔案上)。
- 任何使用 EFS (或其他使用者模式加密) 加密的檔案，會阻止服務讀取資料。

    > [!Note]  
    > Azure 檔案同步永遠會加密傳輸中的資料。 資料待用時一律會在 Azure 中加密。
 
### <a name="server-endpoint"></a>伺服器端點
- 只能在 NTFS 磁碟區上建立伺服器端點。 Azure 檔案同步目前不支援 ReFS、FAT、FAT32 及其他檔案系統。
- 如果未在刪除伺服器端點之前回收階層式檔案，階層式檔案會變成無法存取的狀態。 若要還原檔案的存取性，請重新建立伺服器端點。 如果在刪除伺服器端點後過了 30 天，或如果雲端端點已刪除，將會無法使用未重新呼叫的階層式檔案。
- 系統磁碟區上不支援雲端階層。 若要在系統磁碟區上建立伺服器端點，請於建立伺服器端點時，停用雲端階層處理。
- 只有叢集磁碟才支援容錯移轉叢集，但叢集共用磁碟區 (CSV) 不提供支援。
- 伺服器端點無法為巢狀。 它可與其他端點平行並存於相同的磁碟區上。
- 請勿在伺服器端點位置內儲存 OS 或應用程式分頁檔。
- 如果將伺服器重新命名，並不會更新入口網站中的伺服器名稱。

### <a name="cloud-endpoint"></a>雲端端點
- Azure 檔案同步支援直接對 Azure 檔案共用進行變更。 不過，在 Azure 檔案共用上所做的任何變更，都必須先由 Azure 檔案同步變更偵測作業做出探索。 針對雲端端點的變更偵測作業，每隔 24 小時會起始一次。 此外，透過 REST 通訊協定對 Azure 檔案共用所做的變更，將不會更新 SMB 上次修改時間，而且將不會同步顯示為變更。
- 可將儲存體同步服務及 (或) 儲存體帳戶移至現有 Azure AD 租用戶中的不同資源群組或訂用帳戶。 如果移動儲存體帳戶，您需要將儲存體帳戶的存取權給予混合式檔案同步服務 (請參閱[確保 Azure 檔案同步有儲存體帳戶的存取權](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac))。

    > [!Note]  
    > Azure 檔案同步不支援將訂用帳戶移至不同的 Azure AD 租用戶。

### <a name="cloud-tiering"></a>雲端階層處理
- 如果使用 Robocopy 將階層式檔案複製到另一個位置，則所產生的檔案不會進行階層處理。 因為 Robocopy 未正確地在複製作業中包含該屬性，所以可能設定離線屬性。
- 使用 robocopy 複製檔案時，請使用 /MIR 選項來保留檔案的時間戳記。 這可確保較舊的檔案會比最近存取的檔案更快進行階層處理。

## <a name="agent-version-6300"></a>代理程式版本 6.3.0.0
下列版本資訊適用於 Azure 檔案同步代理程式版本 6.3.0.0 (2019 年 6 月 27 日發行)。 這些是針對版本 6.0.0.0 所列的版本資訊以外的版本資訊。

此版本修正的問題清單：  
- 在 Windows Server 2012 R2 上，透過 SMB 存取或瀏覽伺服器端點位置的速度很慢 
- 提高了安裝 Azure 檔案同步 V6 代理程式之後的 CPU 使用率
- 雲端階層處理遙測改進

## <a name="agent-version-6200"></a>代理程式版本 6.2.0.0
下列版本資訊適用於 Azure 檔案同步代理程式版本 6.2.0.0 (2019 年 6 月 13 日發行)。 這些是針對版本 6.0.0.0 所列的版本資訊以外的版本資訊。

此版本修正的問題清單：  
- 建立伺服器端點之後，當背景重新叫用將檔案下載到伺服器時，可能會發生高 CPU 使用率
- 由於權杖過期，同步處理和雲端階層處理作業可能會失敗，並發生 ECS_E_SERVER_CREDENTIAL_NEEDED 錯誤
- 如果下載檔案的 URL 包含保留字元，重新叫用檔案可能會失敗 

## <a name="agent-version-6100"></a>代理程式版本 6.1.0.0
下列版本資訊適用於 Azure 檔案同步代理程式版本 6.1.0.0 (2019 年 5 月 6 日發行)。 這些是針對版本 6.0.0.0 所列的版本資訊以外的版本資訊。

此版本修正的問題清單：  
- 在已安裝 Azure 檔案同步代理程式 6.0 版的伺服器上，Windows Admin Center 無法顯示代理程式版本和伺服器端點設定。

## <a name="agent-version-6000"></a>代理程式版本 6.0.0.0
下列版本資訊適用於 Azure 檔案同步代理程式版本 6.0.0.0 (2019 年 4 月 22 日發行)。

### <a name="improvements-and-issues-that-are-fixed"></a>增強功能和已修正的問題

- 代理程式自動更新支援
  - 我們聽到了您的意見反應，已在 Azure 檔案同步伺服器代理程式中新增自動更新功能。 如需詳細資訊，請參閱 [Azure 檔案同步更新原則](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#azure-file-sync-agent-update-policy)。
- 支援 Azure 檔案共用 ACL
  - Azure 檔案同步一向支援在伺服器端點之間同步處理 ACL，但 ACL 並未同步到雲端端點 (Azure 檔案共用)。 此發行版本新增了在伺服器與雲端端點之間同步 ACL 的支援。
- 平行上傳和下載伺服器端點的同步工作階段 
  - 現在，伺服器端點支援同時上傳和下載檔案。 不需再等候下載完成，因此可以將檔案上傳到 Azure 檔案共用。 
- 新的雲端階層處理 Cmdlet 可取得磁碟區和階層處理的狀態
  - 現在，可以使用兩個新的「伺服器-本機」PowerShell Cmdlet 來取得雲端階層處理和檔案重新叫用資訊。 它們可取得伺服器上的兩個事件通道記錄資訊：
    - Get-StorageSyncFileTieringResult 將會列出尚未階層處理的所有檔案及其路徑，並報告原因。
    - Get-StorageSyncFileRecallResult 會報告所有檔案重新叫用事件。 它會列出每個重新叫用的檔案及其路徑，以及該重新叫用為成功或錯誤。
  - 根據預設，這兩個事件通道各自可以儲存最多 1 MB，您可以藉由增加事件通道大小來增加報告的檔案數量。
- 支援 FIPS 模式
  - 現在，Azure 檔案同步支援在已安裝 Azure 檔案同步代理程式的伺服器上啟用 FIPS 模式。
    - 在伺服器上啟用 FIPS 模式之前，請先在伺服器上安裝 Azure 檔案同步代理程式和 [PackageManagement 模組](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2)。 如果伺服器上已啟用 FIPS，請[手動下載](/powershell/scripting/gallery/how-to/working-with-packages/manual-download) [PackageManagement 模組](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) 至伺服器。
- 雲端階層處理和同步處理的其他可靠性改進

### <a name="evaluation-tool"></a>評估工具
在部署 Azure 檔案同步之前，您應該使用 Azure 檔案同步評估工具來評估其是否與您的系統相容。 此工具是 Azure PowerShell Cmdlet，可檢查檔案系統和資料集的潛在問題，例如不支援的字元或 OS 版本。 關於安裝和使用方式指示，請參閱規劃指南中[評估工具](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet)小節。 

### <a name="agent-installation-and-server-configuration"></a>代理程式安裝和伺服器設定
如需如何使用 Windows Server 安裝及設定 Azure 檔案同步代理程式的詳細資訊，請參閱[規劃 Azure 檔案同步部署](storage-sync-files-planning.md)和[如何部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)。

- 必須以提升的 (系統管理員) 權限安裝代理程式安裝套件。
- Nano Server 部署選項不支援此代理程式。
- 只有 Windows Server 2019、Windows Server 2016 和 Windows Server 2012 R2 可支援此代理程式。
- 代理程式需要至少 2 GiB 的記憶體。 如果伺服器在啟用動態記憶體的虛擬機器中執行，則 VM 的記憶體應最少設定為 2048 MiB。
- 在已將系統磁碟區資訊 (SVI) 目錄壓縮的磁碟區上，儲存體同步代理程式 (FileSyncSvc) 服務不支援其中的伺服器端點。 此設定會導致非預期的結果。

### <a name="interoperability"></a>互通性
- 防毒程式、備份及其他存取階層式檔案的應用程式，除非採用離線屬性並略過讀取這些檔案的內容，否則將會導致不想要的重新叫用。 如需詳細資訊，請參閱[針對 Azure 檔案同步進行疑難排解](storage-sync-files-troubleshoot.md)。
- 如果檔案因為檔案檢測而遭到封鎖，檔案伺服器資源管理員 (FSRM) 檔案檢測會造成無止盡的同步處理失敗。
- 不支援在已安裝 Azure 檔案同步代理程式的伺服器上執行 sysprep，而且此舉可能會導致非預期的結果。 在部署伺服器映像並完成 sysprep 迷你設定之後，請安裝 Azure 檔案同步代理程式。

### <a name="sync-limitations"></a>同步限制
下列項目不會同步，但是系統的其餘部分會繼續正常運作：
- 具有不受支援字元的檔案。 如需不受支援字元的清單，請參閱[疑難排解指南](storage-sync-files-troubleshoot.md#handling-unsupported-characters)。
- 以句點結尾的檔案或目錄。
- 長度超過 2048 個字元的路徑。
- 安全性描述元的判別存取控制清單 (DACL) 部分 (如果大於 2 KB)。 (這個問題僅適用於單一項目上有超過 40 個存取控制項目 (ACE) 時。)
- 用於稽核之安全性描述元的系統存取控制清單 (SACL) 部分。
- 擴充屬性。
- 替代資料流。
- 重新分析點。
- 永久連結。
- 如果變更從其他端點同步至該檔案，則不會保存壓縮 (如果設定於伺服器檔案上)。
- 任何使用 EFS (或其他使用者模式加密) 加密的檔案，會阻止服務讀取資料。

    > [!Note]  
    > Azure 檔案同步永遠會加密傳輸中的資料。 資料待用時一律會在 Azure 中加密。
 
### <a name="server-endpoint"></a>伺服器端點
- 只能在 NTFS 磁碟區上建立伺服器端點。 Azure 檔案同步目前不支援 ReFS、FAT、FAT32 及其他檔案系統。
- 如果未在刪除伺服器端點之前回收階層式檔案，階層式檔案會變成無法存取的狀態。 若要還原檔案的存取性，請重新建立伺服器端點。 如果在刪除伺服器端點後過了 30 天，或如果雲端端點已刪除，將會無法使用未重新呼叫的階層式檔案。
- 系統磁碟區上不支援雲端階層。 若要在系統磁碟區上建立伺服器端點，請於建立伺服器端點時，停用雲端階層處理。
- 只有叢集磁碟才支援容錯移轉叢集，但叢集共用磁碟區 (CSV) 不提供支援。
- 伺服器端點無法為巢狀。 它可與其他端點平行並存於相同的磁碟區上。
- 請勿在伺服器端點位置內儲存 OS 或應用程式分頁檔。
- 如果將伺服器重新命名，並不會更新入口網站中的伺服器名稱。

### <a name="cloud-endpoint"></a>雲端端點
- Azure 檔案同步支援直接對 Azure 檔案共用進行變更。 不過，在 Azure 檔案共用上所做的任何變更，都必須先由 Azure 檔案同步變更偵測作業做出探索。 針對雲端端點的變更偵測作業，每隔 24 小時會起始一次。 此外，透過 REST 通訊協定對 Azure 檔案共用所做的變更，將不會更新 SMB 上次修改時間，而且將不會同步顯示為變更。
- 可將儲存體同步服務及 (或) 儲存體帳戶移至現有 Azure AD 租用戶中的不同資源群組或訂用帳戶。 如果移動儲存體帳戶，您需要將儲存體帳戶的存取權給予混合式檔案同步服務 (請參閱[確保 Azure 檔案同步有儲存體帳戶的存取權](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac))。

    > [!Note]  
    > Azure 檔案同步不支援將訂用帳戶移至不同的 Azure AD 租用戶。

### <a name="cloud-tiering"></a>雲端階層處理
- 如果使用 Robocopy 將階層式檔案複製到另一個位置，則所產生的檔案不會進行階層處理。 因為 Robocopy 未正確地在複製作業中包含該屬性，所以可能設定離線屬性。
- 使用 robocopy 複製檔案時，請使用 /MIR 選項來保留檔案的時間戳記。 這可確保較舊的檔案會比最近存取的檔案更快進行階層處理。
- 從 SMB 用戶端檢視檔案屬性時，因為 SMB 快取檔案中繼資料，所以離線屬性可能會顯示為不正確設定。

## <a name="agent-version-5200"></a>代理程式版本 5.2.0.0
下列版本資訊適用於 Azure 檔案同步代理程式版本 5.2.0.0 (2019 年 4 月 4 日發行)。 這些是針對版本 5.0.2.0 所列的版本資訊以外的版本資訊。

此版本修正的問題清單：  
- 離線資料傳輸和資料傳輸繼續功能的可靠性改進
- 同步處理遙測改進

## <a name="agent-version-5100"></a>代理程式版本 5.1.0.0
下列版本資訊適用於 Azure 檔案同步代理程式版本 5.1.0.0 (2019 年 3 月 7 日發行)。 這些是針對版本 5.0.2.0 所列的版本資訊以外的版本資訊。

此版本修正的問題清單：  
- 如果伺服器上的變更列舉失敗，檔案可能無法同步處理，並發生 0x80c8031d (ECS_E_CONCURRENCY_CHECK_FAILED) 錯誤
- 現在，如果同步工作階段或檔案收到 0x80072f78 (WININET_E_INVALID_SERVER_RESPONSE) 錯誤，同步處理將立即重試作業
- 檔案可能無法同步，並發生 0x80c80203 (ECS_E_SYNC_INVALID_STAGED_FILE) 錯誤
- 重新叫用檔案時，可能會發生高記憶體使用量
- 雲端階層處理遙測改進 

## <a name="agent-version-5020"></a>代理程式版本 5.0.2.0
下列版本資訊適用於 Azure 檔案同步代理程式版本 5.0.2.0 (在 2019 年 2 月 12 日發行)。

### <a name="improvements-and-issues-that-are-fixed"></a>增強功能和已修正的問題

- 支援 Azure Government 雲端
  - 我們已新增對 Azure Government 雲端的預覽支援。 這需要在允許清單中的訂用帳戶，並從 Microsoft 下載特殊的代理程式。 若要存取預覽，請直接傳送電子郵件給我們 ([AzureFiles@microsoft.com](mailto:AzureFiles@microsoft.com))。
- 支援重複資料刪除
    - 在 Windows Server 2016 和 Windows Server 2019 上啟用的雲端階層處理現已完整支援重複資料刪除。 在已啟用雲端階層處理的磁碟區上啟用重複資料刪除，可讓您在內部部署中快取更多檔案，而不需佈建更多儲存空間。
- 支援離線資料傳輸 (例如透過資料箱)
    - 透過您選擇的任何方式，輕鬆地將大量資料移轉到 Azure 檔案同步中。 您可以選擇 Azure 資料箱、AzCopy、甚至是協力廠商移轉服務。 您不需要使用大量的頻寬以將資料送進 Azure。若使用資料箱，只要將它寄過去即可！ 若要深入了解，請參閱[離線資料傳輸文件](https://aka.ms/AFS/OfflineDataTransfer) \(英文\)。
- 改進同步效能
    - 在相同磁碟區上有多個伺服器端點的客戶，在此版本之前可能發生同步處理效能緩慢的問題。 Azure 檔案同步每天會在伺服器上建立一個暫時的 VSS 快照集，用以同步具有開啟控制代碼的檔案。 同步處理現在支援 VSS 同步工作階段為作用中時，在磁碟區上的多個伺服器端點同步處理。 您不必再等候 VSS 同步工作階段完成，如此一來磁碟區上的其他伺服器端點可繼續同步處理。
- 改進入口網站中的監視
    - 儲存體同步服務入口網站中已新增圖表以供檢視：
        - 已同步的檔案數
        - 已傳輸的資料大小
        - 尚未同步的檔案數目
        - 重新叫用的資料大小
        - 伺服器連線狀態
    - 若要深入了解，請參閱[監視 Azure 檔案同步](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring)。
- 改進延展性和可靠性
    - 目錄中的檔案系統物件 (目錄和檔案) 數目上限已增加到 1,000,000。 先前的限制為 200,000。
    - 當大型檔案的傳輸中斷時，同步處理將嘗試繼續資料傳輸，而不是重新傳輸 

### <a name="evaluation-tool"></a>評估工具
在部署 Azure 檔案同步之前，您應該使用 Azure 檔案同步評估工具來評估其是否與您的系統相容。 此工具是 Azure PowerShell Cmdlet，可檢查檔案系統和資料集的潛在問題，例如不支援的字元或 OS 版本。 關於安裝和使用方式指示，請參閱規劃指南中[評估工具](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet)小節。 

### <a name="agent-installation-and-server-configuration"></a>代理程式安裝和伺服器設定
如需如何使用 Windows Server 安裝及設定 Azure 檔案同步代理程式的詳細資訊，請參閱[規劃 Azure 檔案同步部署](storage-sync-files-planning.md)和[如何部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)。

- 必須以提升的 (系統管理員) 權限安裝代理程式安裝套件。
- Windows Server Core 或 Nano Server 部署選項不支援此代理程式。
- 只有 Windows Server 2019、Windows Server 2016 和 Windows Server 2012 R2 可支援此代理程式。
- 代理程式需要至少 2 GiB 的記憶體。 如果伺服器在啟用動態記憶體的虛擬機器中執行，則 VM 的記憶體應最少設定為 2048 MiB。
- 在已將系統磁碟區資訊 (SVI) 目錄壓縮的磁碟區上，儲存體同步代理程式 (FileSyncSvc) 服務不支援其中的伺服器端點。 此設定會導致非預期的結果。
- 不支援 FIPS 模式，且必須停用該模式。 

### <a name="interoperability"></a>互通性
- 防毒程式、備份及其他存取階層式檔案的應用程式，除非採用離線屬性並略過讀取這些檔案的內容，否則將會導致不想要的重新叫用。 如需詳細資訊，請參閱[針對 Azure 檔案同步進行疑難排解](storage-sync-files-troubleshoot.md)。
- 如果檔案因為檔案檢測而遭到封鎖，檔案伺服器資源管理員 (FSRM) 檔案檢測會造成無止盡的同步處理失敗。
- 不支援在已安裝 Azure 檔案同步代理程式的伺服器上執行 sysprep，而且此舉可能會導致非預期的結果。 在部署伺服器映像並完成 sysprep 迷你設定之後，請安裝 Azure 檔案同步代理程式。

### <a name="sync-limitations"></a>同步限制
下列項目不會同步，但是系統的其餘部分會繼續正常運作：
- 具有不受支援字元的檔案。 如需不受支援字元的清單，請參閱[疑難排解指南](storage-sync-files-troubleshoot.md#handling-unsupported-characters)。
- 以句點結尾的檔案或目錄。
- 長度超過 2048 個字元的路徑。
- 安全性描述元的判別存取控制清單 (DACL) 部分 (如果大於 2 KB)。 (這個問題僅適用於單一項目上有超過 40 個存取控制項目 (ACE) 時。)
- 用於稽核之安全性描述元的系統存取控制清單 (SACL) 部分。
- 擴充屬性。
- 替代資料流。
- 重新分析點。
- 永久連結。
- 如果變更從其他端點同步至該檔案，則不會保存壓縮 (如果設定於伺服器檔案上)。
- 任何使用 EFS (或其他使用者模式加密) 加密的檔案，會阻止服務讀取資料。

    > [!Note]  
    > Azure 檔案同步永遠會加密傳輸中的資料。 資料待用時一律會在 Azure 中加密。
 
### <a name="server-endpoint"></a>伺服器端點
- 只能在 NTFS 磁碟區上建立伺服器端點。 Azure 檔案同步目前不支援 ReFS、FAT、FAT32 及其他檔案系統。
- 如果未在刪除伺服器端點之前回收階層式檔案，階層式檔案會變成無法存取的狀態。 若要還原檔案的存取性，請重新建立伺服器端點。 如果在刪除伺服器端點後過了 30 天，或如果雲端端點已刪除，將會無法使用未重新呼叫的階層式檔案。
- 系統磁碟區上不支援雲端階層。 若要在系統磁碟區上建立伺服器端點，請於建立伺服器端點時，停用雲端階層處理。
- 只有叢集磁碟才支援容錯移轉叢集，但叢集共用磁碟區 (CSV) 不提供支援。
- 伺服器端點無法為巢狀。 它可與其他端點平行並存於相同的磁碟區上。
- 請勿在伺服器端點位置內儲存 OS 或應用程式分頁檔。
- 如果將伺服器重新命名，並不會更新入口網站中的伺服器名稱。

### <a name="cloud-endpoint"></a>雲端端點
- Azure 檔案同步支援直接對 Azure 檔案共用進行變更。 不過，在 Azure 檔案共用上所做的任何變更，都必須先由 Azure 檔案同步變更偵測作業做出探索。 針對雲端端點的變更偵測作業，每隔 24 小時會起始一次。 此外，透過 REST 通訊協定對 Azure 檔案共用所做的變更，將不會更新 SMB 上次修改時間，而且將不會同步顯示為變更。
- 可將儲存體同步服務及 (或) 儲存體帳戶移至現有 Azure AD 租用戶中的不同資源群組或訂用帳戶。 如果移動儲存體帳戶，您需要將儲存體帳戶的存取權給予混合式檔案同步服務 (請參閱[確保 Azure 檔案同步有儲存體帳戶的存取權](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac))。

    > [!Note]  
    > Azure 檔案同步不支援將訂用帳戶移至不同的 Azure AD 租用戶。

### <a name="cloud-tiering"></a>雲端階層處理
- 如果使用 Robocopy 將階層式檔案複製到另一個位置，則所產生的檔案不會進行階層處理。 因為 Robocopy 未正確地在複製作業中包含該屬性，所以可能設定離線屬性。
- 使用 robocopy 複製檔案時，請使用 /MIR 選項來保留檔案的時間戳記。 這可確保較舊的檔案會比最近存取的檔案更快進行階層處理。
- 從 SMB 用戶端檢視檔案屬性時，因為 SMB 快取檔案中繼資料，所以離線屬性可能會顯示為不正確設定。
