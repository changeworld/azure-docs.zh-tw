---
title: Azure 檔案同步代理程式的版本資訊 | Microsoft Docs
description: Azure 檔案同步代理程式的版本資訊。
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 3/16/2020
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 4cb5a3d471aa3131d3233841cd6450f0a968dfe7
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82800969"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Azure 檔案同步代理程式的版本資訊
Azure 檔案同步可讓您將組織的檔案共用集中在「Azure 檔案服務」中，而不需要犧牲內部部署檔案伺服器的靈活度、效能及相容性。 您的 Windows Server 安裝會轉換成 Azure 檔案共用的快速快取。 您可以使用 Windows Server 上可用的任何通訊協定來從本機存取資料 (包括 SMB、NFS 和 FTPS)。 您可以視需要存取多個散佈於世界各地的快取。

本文提供 Azure 檔案同步代理程式支援版本的版本資訊。

## <a name="supported-versions"></a>支援的版本
Azure 檔案同步代理程式支援下列版本：

| 里程碑 | 代理程式版本號碼 | 發行日期 | 狀態 |
|----|----------------------|--------------|------------------|
| V10 版本- [4522409](https://support.microsoft.com/en-us/help/4522409)| 10.0.0.0 | 2020年4月9日 | 進行中 |
| 2019年12月更新彙總套件- [KB4522360](https://support.microsoft.com/help/4522360)| 9.1.0.0 | 2019 年 12 月 12 日 | 支援 |
| V9 版本- [KB4522359](https://support.microsoft.com/help/4522359)| 9.0.0.0 | 2019年12月2日 | 支援 |
| V8 版本- [KB4511224](https://support.microsoft.com/help/4511224)| 8.0.0.0 | 2019 年 10 月 8 日 | 支援 |
| 2019年7月更新彙總套件- [KB4490497](https://support.microsoft.com/help/4490497)| 7.2.0.0 | 2019 年 7 月 24 日 | 支援 |
| 2019年7月更新彙總套件- [KB4490496](https://support.microsoft.com/help/4490496)| 7.1.0.0 | 2019 年 7 月 12 日 | 支援 |
| V7 版本- [KB4490495](https://support.microsoft.com/help/4490495)| 7.0.0.0 | 2019年6月19日 | 支援 |
| 2019年6月更新彙總套件- [KB4489739](https://support.microsoft.com/help/4489739)| 6.3.0.0 | 2019年6月27日 | 支援的代理程式版本將于2020年4月21日過期 |
| 2019年6月更新彙總套件- [KB4489738](https://support.microsoft.com/help/4489738)| 6.2.0.0 | 2019年6月13日 | 支援的代理程式版本將于2020年4月21日過期 |
| 5月2019更新彙總套件- [KB4489737](https://support.microsoft.com/help/4489737)| 6.1.0.0 | 2019 5 月7日 | 支援的代理程式版本將于2020年4月21日過期 |
| V6 發行- [KB4489736](https://support.microsoft.com/help/4489736)| 6.0.0.0 | 2019年4月21日 | 支援的代理程式版本將于2020年4月21日過期 |
| V5 版本 | 5.0.2.0-5.2.0。0 | N/A | 不支援-代理程式版本已于2020年3月18日過期 |
| V4 版本 | 4.0.1.0-4.3.0.0 版 | N/A | 不支援-代理程式版本已于2019年11月6日過期 |
| V3 版本 | 3.1.0.0-v3.4.0。0 | N/A | 不支援-代理程式版本已于2019年8月19日到期 |
| 預先 GA 的代理程式 | 1.1.0.0 - 3.0.13.0 | N/A | 不支援 - 代理程式版本已於 2018 年 10 月1 日過期 |

### <a name="azure-file-sync-agent-update-policy"></a>Azure 檔案同步代理程式更新原則
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-10000"></a>代理程式10.0.0.0 版
下列版本資訊適用于 Azure 檔案同步代理程式的10.0.0.0 版（2020年4月9日發行）。

### <a name="improvements-and-issues-that-are-fixed"></a>增強功能和已修正的問題

- 已改善入口網站中的同步處理進度
    - 在 V10 代理程式版本中，Azure 入口網站很快就會開始顯示正在執行的同步會話類型。 例如 初始下載、定期下載、背景召回（快速嚴重損壞修復案例）和類似。 

- 改良的雲端階層入口網站體驗
    - 如果您有無法進行分層或回收的檔案，您現在可以在伺服器端點屬性中查看分層錯誤。
    - 伺服器端點可以使用其他雲端分層資訊：
        - 本機快取大小
        - 快取使用量效率
        - 雲端階層處理原則詳細資料：磁片區大小、目前的可用空間，或本機快取中最舊檔案的上次存取時間。
    - 這些變更將會在初始 V10 代理程式發行後不久于 Azure 入口網站中寄送。

- 支援將儲存體同步服務及/或儲存體帳戶移至不同的 Azure Active Directory （AAD）租使用者
    - Azure 檔案同步現在支援將儲存體同步服務及/或儲存體帳戶移至不同的資源群組、訂用帳戶或 Azure AD 租使用者。
    
- 其他效能和可靠性的改進
    - 如果在儲存體帳戶上設定虛擬網路（VNET）和防火牆規則，Azure 檔案共用上的變更偵測可能會失敗。
    - 減少與重新叫用相關聯的記憶體耗用量。 
    - 改善使用[AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) Cmdlet 時的效能。
    - 其他可靠性的增強功能。 
    
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
- 不支援在已安裝 Azure 檔案同步代理程式的伺服器上執行 sysprep，而且可能會導致非預期的結果。 在部署伺服器映像並完成 sysprep 迷你設定之後，請安裝 Azure 檔案同步代理程式。

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
- Azure 檔案同步支援直接對 Azure 檔案共用進行變更。 不過，在 Azure 檔案共用上所做的任何變更，都必須先由 Azure 檔案同步變更偵測作業做出探索。 針對雲端端點的變更偵測作業，每隔 24 小時會起始一次。 若要立即同步處理 Azure 檔案共用中變更的檔案，可使用[AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell Cmdlet，以手動方式在 azure 檔案共用中起始變更的偵測。 此外，透過 REST 通訊協定對 Azure 檔案共用所做的變更，將不會更新 SMB 上次修改時間，而且將不會同步顯示為變更。
- 可將儲存體同步服務及 (或) 儲存體帳戶移至現有 Azure AD 租用戶中的不同資源群組或訂用帳戶。 如果移動儲存體帳戶，您需要將儲存體帳戶的存取權給予混合式檔案同步服務 (請參閱[確保 Azure 檔案同步有儲存體帳戶的存取權](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac))。

    > [!Note]  
    > 建立雲端端點時，儲存體同步服務和儲存體帳戶必須位於相同的 Azure AD 租使用者中。 建立雲端端點之後，可以將儲存體同步服務和儲存體帳戶移至不同的 Azure AD 租使用者。

### <a name="cloud-tiering"></a>雲端階層處理
- 如果使用 Robocopy 將階層式檔案複製到另一個位置，則所產生的檔案不會進行階層處理。 因為 Robocopy 未正確地在複製作業中包含該屬性，所以可能設定離線屬性。
- 使用 robocopy 複製檔案時，請使用 /MIR 選項來保留檔案的時間戳記。 這可確保較舊的檔案會比最近存取的檔案更快進行階層處理。

## <a name="agent-version-9100"></a>代理程式版本9.1.0。0
下列版本資訊適用于2019年12月12日發行之 Azure 檔案同步代理程式的版本9.1.0.0。 這些注意事項是針對版本9.0.0.0 所列的版本資訊以外的資訊。

此版本修正的問題：  
- 升級至 Azure 檔案同步代理程式版本9.0 之後，同步處理失敗，發生下列其中一個錯誤：
    - 0x8e5e044e （JET_errWriteConflict）
    - 0x8e5e0450 （JET_errInvalidSesid）
    - 0x8e5e0442 （JET_errInstanceUnavailable）

## <a name="agent-version-9000"></a>代理程式版本9.0.0。0
下列版本資訊適用于 Azure 檔案同步代理程式的版本9.0.0.0 （2019年12月2日發行）。

### <a name="improvements-and-issues-that-are-fixed"></a>增強功能和已修正的問題

- 自助式還原支援
    - 使用者現在可以使用 [先前的版本] 功能來還原其檔案。 在 v9 版本之前，已啟用雲端階層處理的磁片區不支援先前的版本功能。 這項功能必須分別針對每個磁片區啟用，且已啟用雲端階層處理的端點存在。 若要深入了解，請參閱：  
[透過舊版和 VSS （磁碟區陰影複製服務）的自助式還原](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service)。 
 
- 支援較大的檔案共用大小 
    - Azure 檔案同步現在支援單一同步處理命名空間中的64TiB 和100000000檔案。  
 
- 伺服器2019上的重復資料刪除支援 
    - 已在 Windows Server 2019 上啟用雲端階層處理，現已支援重復資料刪除。 若要在具有雲端階層處理的磁片區上支援重復資料刪除，必須安裝 Windows update [KB4520062](https://support.microsoft.com/help/4520062) 。 
 
- 已將檔案的最小檔案大小提升至層級 
    - 檔案對層的最小檔案大小，現在是根據檔案系統叢集大小（這是檔案系統叢集大小的兩倍）。 例如，根據預設，NTFS 檔案系統叢集大小為 4 KB，針對檔案所產生的檔案大小下限為 8 KB。 
 
- 網路連線能力測試 Cmdlet 
    - 在 Azure 檔案同步設定的過程中，必須聯繫多個服務端點。 它們各自有自己的 DNS 名稱，必須可供伺服器存取。 這些 Url 也專屬於伺服器註冊所在的區域。 一旦註冊伺服器，就可以使用連線測試 Cmdlet （PowerShell 和伺服器註冊公用程式）來測試與此伺服器特定的所有 Url 的通訊。 此 Cmdlet 有助於疑難排解未完成的通訊，以防止伺服器完全使用 Azure 檔案同步，而且可以用來微調 proxy 和防火牆設定。  
 
        若要執行網路連線測試，請執行下列 PowerShell 命令： 
 
        Import-module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"  
        測試-StorageSyncNetworkConnectivity
 
- 啟用雲端階層處理時移除伺服器端點改善 
    - 如先前所述，移除伺服器端點並不會導致移除 Azure 檔案共用中的檔案。 不過，本機伺服器上重新分析點的行為已變更。 移除伺服器端點時，現在會刪除重新分析點（指向伺服器上非本機的檔案指標）。 完整快取檔案會保留在伺服器上。 這是為了避免在移除伺服器端點時，[孤立](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)的階層式檔案所做的改進。 如果重新建立伺服器端點，則會在伺服器上重新建立分層檔案的重新分析點。  
 
- 改善效能和可靠性 
    - 減少召回失敗。 重新叫用大小現在會根據網路頻寬自動調整。 
    - 改善將新的伺服器加入至同步處理群組時的下載效能。 
    - 減少因為條件約束衝突而無法同步的檔案。 
    - 如果伺服器端點路徑是磁片區掛接點，檔案就無法層級，或在某些情況下會意外重新叫用。
    
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
- 不支援在已安裝 Azure 檔案同步代理程式的伺服器上執行 sysprep，而且可能會導致非預期的結果。 在部署伺服器映像並完成 sysprep 迷你設定之後，請安裝 Azure 檔案同步代理程式。

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
- 如果未在刪除伺服器端點之前回收階層式檔案，階層式檔案會變成無法存取的狀態。 若要還原檔案的存取性，請重新建立伺服器端點。 如果在刪除伺服器端點後過了 30 天，或如果雲端端點已刪除，將會無法使用未重新呼叫的階層式檔案。 若要深入瞭解，請參閱在[刪除伺服器端點之後，伺服器上的](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)階層式檔案無法存取。
- 系統磁碟區上不支援雲端階層。 若要在系統磁碟區上建立伺服器端點，請於建立伺服器端點時，停用雲端階層處理。
- 只有叢集磁碟才支援容錯移轉叢集，但叢集共用磁碟區 (CSV) 不提供支援。
- 伺服器端點無法為巢狀。 它可與其他端點平行並存於相同的磁碟區上。
- 請勿在伺服器端點位置內儲存 OS 或應用程式分頁檔。
- 如果將伺服器重新命名，並不會更新入口網站中的伺服器名稱。

### <a name="cloud-endpoint"></a>雲端端點
- Azure 檔案同步支援直接對 Azure 檔案共用進行變更。 不過，在 Azure 檔案共用上所做的任何變更，都必須先由 Azure 檔案同步變更偵測作業做出探索。 針對雲端端點的變更偵測作業，每隔 24 小時會起始一次。 若要立即同步處理 Azure 檔案共用中變更的檔案，可使用[AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell Cmdlet，以手動方式在 azure 檔案共用中起始變更的偵測。 此外，透過 REST 通訊協定對 Azure 檔案共用所做的變更，將不會更新 SMB 上次修改時間，而且將不會同步顯示為變更。
- 可將儲存體同步服務及 (或) 儲存體帳戶移至現有 Azure AD 租用戶中的不同資源群組或訂用帳戶。 如果移動儲存體帳戶，您需要將儲存體帳戶的存取權給予混合式檔案同步服務 (請參閱[確保 Azure 檔案同步有儲存體帳戶的存取權](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac))。

    > [!Note]  
    > Azure 檔案同步不支援將訂用帳戶移至不同的 Azure AD 租用戶。

### <a name="cloud-tiering"></a>雲端階層處理
- 如果使用 Robocopy 將階層式檔案複製到另一個位置，則所產生的檔案不會進行階層處理。 因為 Robocopy 未正確地在複製作業中包含該屬性，所以可能設定離線屬性。
- 使用 robocopy 複製檔案時，請使用 /MIR 選項來保留檔案的時間戳記。 這可確保較舊的檔案會比最近存取的檔案更快進行階層處理。
- 如果分頁檔檔案位於已啟用雲端階層處理的磁片區上，則檔案可能無法進行層級處理。 分頁檔應該位於已停用雲端階層處理的磁片區上。

## <a name="agent-version-8000"></a>代理程式版本8.0.0。0
下列版本資訊適用于 Azure 檔案同步代理程式的版本8.0.0.0 （2019年10月8日發行）。

### <a name="improvements-and-issues-that-are-fixed"></a>增強功能和已修正的問題

- 還原效能改進
    - 透過 Azure 備份進行復原的復原時間更快。 還原的檔案會更快地同步到 Azure 檔案同步伺服器。 
- 改良的雲端階層入口網站體驗  
    - 如果您有無法重新叫用的階層式檔案，您現在可以在伺服器端點屬性中查看召回錯誤。 此外，如果伺服器上未載入雲端階層處理篩選器驅動程式，伺服器端點健全狀況現在會顯示錯誤和風險降低步驟。
- 較簡單的代理程式安裝
    - Az\AzureRM PowerShell 模組已不再需要註冊伺服器，讓安裝變得更簡單快速。
- 其他效能和可靠性的改進

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
- 不支援在已安裝 Azure 檔案同步代理程式的伺服器上執行 sysprep，而且可能會導致非預期的結果。 在部署伺服器映像並完成 sysprep 迷你設定之後，請安裝 Azure 檔案同步代理程式。

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
- 如果未在刪除伺服器端點之前回收階層式檔案，階層式檔案會變成無法存取的狀態。 若要還原檔案的存取性，請重新建立伺服器端點。 如果在刪除伺服器端點後過了 30 天，或如果雲端端點已刪除，將會無法使用未重新呼叫的階層式檔案。 若要深入瞭解，請參閱在[刪除伺服器端點之後，伺服器上的](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)階層式檔案無法存取。
- 系統磁碟區上不支援雲端階層。 若要在系統磁碟區上建立伺服器端點，請於建立伺服器端點時，停用雲端階層處理。
- 只有叢集磁碟才支援容錯移轉叢集，但叢集共用磁碟區 (CSV) 不提供支援。
- 伺服器端點無法為巢狀。 它可與其他端點平行並存於相同的磁碟區上。
- 請勿在伺服器端點位置內儲存 OS 或應用程式分頁檔。
- 如果將伺服器重新命名，並不會更新入口網站中的伺服器名稱。

### <a name="cloud-endpoint"></a>雲端端點
- Azure 檔案同步支援直接對 Azure 檔案共用進行變更。 不過，在 Azure 檔案共用上所做的任何變更，都必須先由 Azure 檔案同步變更偵測作業做出探索。 針對雲端端點的變更偵測作業，每隔 24 小時會起始一次。 若要立即同步處理 Azure 檔案共用中變更的檔案，可使用[AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell Cmdlet，以手動方式在 azure 檔案共用中起始變更的偵測。 此外，透過 REST 通訊協定對 Azure 檔案共用所做的變更，將不會更新 SMB 上次修改時間，而且將不會同步顯示為變更。
- 可將儲存體同步服務及 (或) 儲存體帳戶移至現有 Azure AD 租用戶中的不同資源群組或訂用帳戶。 如果移動儲存體帳戶，您需要將儲存體帳戶的存取權給予混合式檔案同步服務 (請參閱[確保 Azure 檔案同步有儲存體帳戶的存取權](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac))。

    > [!Note]  
    > Azure 檔案同步不支援將訂用帳戶移至不同的 Azure AD 租用戶。

### <a name="cloud-tiering"></a>雲端階層處理
- 如果使用 Robocopy 將階層式檔案複製到另一個位置，則所產生的檔案不會進行階層處理。 因為 Robocopy 未正確地在複製作業中包含該屬性，所以可能設定離線屬性。
- 使用 robocopy 複製檔案時，請使用 /MIR 選項來保留檔案的時間戳記。 這可確保較舊的檔案會比最近存取的檔案更快進行階層處理。

## <a name="agent-version-7200"></a>代理程式版本7.2.0。0
下列版本資訊適用于2019年7月24日發行之 Azure 檔案同步代理程式的版本7.2.0.0。 這些注意事項是針對版本7.0.0.0 所列的版本資訊以外的資訊。

此版本修正的問題清單：  
- 如果 proxy 設定為 null，儲存體同步代理程式（FileSyncSvc）會當機。
- 如果伺服器上有多個端點的名稱相同，伺服器端點就會開始 BCDR （錯誤 0x80c80257-ECS_E_BCDR_IN_PROGRESS）。
- 雲端階層處理可靠性改進。

## <a name="agent-version-7100"></a>代理程式版本7.1.0。0
下列版本資訊適用于2019年7月12日發行之 Azure 檔案同步代理程式的版本7.1.0.0。 這些注意事項是針對版本7.0.0.0 所列的版本資訊以外的資訊。

此版本修正的問題清單：  
- 在 Windows Server 2012 R2 上透過 SMB 存取或流覽伺服器端點位置的速度很慢。 
- 在安裝 Azure 檔案同步 v6 代理程式之後增加 CPU 使用率。
- 雲端階層處理遙測改進。
- 針對雲端階層處理和同步處理的其他可靠性改進。

## <a name="agent-version-7000"></a>代理程式版本7.0.0。0
下列版本資訊適用于 Azure 檔案同步代理程式的版本7.0.0.0 （2019年6月19日發行）。

### <a name="improvements-and-issues-that-are-fixed"></a>增強功能和已修正的問題

- 支援較大的檔案共用大小
    - 有了較大型 Azure 檔案共用的預覽，我們也增加了檔案同步的支援限制。 在第一個步驟中，Azure 檔案同步現在支援單一同步處理命名空間中的最多 25 TB 和50000000檔案。 若要申請大型檔案共用預覽，請填寫此表單https://aka.ms/azurefilesatscalesurvey。 
- 支援儲存體帳戶上的防火牆和虛擬網路設定
    - Azure 檔案同步現在支援儲存體帳戶上的防火牆和虛擬網路設定。 若要將您的部署設定為使用防火牆和虛擬網路設定，請參閱[設定防火牆和虛擬網路設定](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)。
- 用來立即同步處理 Azure 檔案共用中已變更檔案的 PowerShell Cmdlet
    - 若要立即同步處理 Azure 檔案共用中變更的檔案，可使用 AzStorageSyncChangeDetection PowerShell Cmdlet，以手動方式在 Azure 檔案共用中起始變更的偵測。 此 Cmdlet 適用于某些類型的自動化程式在 Azure 檔案共用中進行變更的案例，或由系統管理員完成的變更（例如將檔案和目錄移到共用中）。 針對使用者變更，建議您在 IaaS VM 中安裝 Azure 檔案同步代理程式，並讓終端使用者透過 IaaS VM 存取檔案共用。 如此一來，所有變更都會快速地同步處理至其他代理程式，而不需要使用 AzStorageSyncChangeDetection Cmdlet。 若要深入瞭解，請參閱[AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection)檔。
- 改善的入口網站體驗（如果您遇到未同步的檔案）
    - 如果您有無法同步的檔案，我們現在可以區別入口網站中的暫時性和持續性錯誤。 暫時性錯誤通常會自行解決，而不需要系統管理動作。 例如，在檔案控制代碼關閉之前，目前使用中的檔案將不會同步。 針對持續性錯誤，我們現在會顯示受到每個錯誤影響的檔案數目。 持續性錯誤計數也會顯示在同步處理群組中所有伺服器端點的 [檔案不同步] 資料行中。
- 改良的 Azure 備份檔案層級還原
    - 現在會偵測到使用 Azure 備份還原的個別檔案，並更快地同步處理到伺服器端點。
- 已改善雲端階層處理重新叫用 Cmdlet 的可靠性 
    - StorageSyncFileRecall Cmdlet 現在可讓客戶指定每個檔案重試計數，以及每個檔案重試延遲（類似 robocopy）。 先前，此 Cmdlet 會以隨機順序重新叫用指定路徑下的所有階層式檔案。 使用新的順序參數時，這個 Cmdlet 會先重新叫用最熱門的資料，並接受雲端階層處理原則（如果符合日期原則或符合磁片區可用空間，則停止重新呼叫，以先發生者為准）。
- 僅支援 TLS 1.2 （已停用 TLS 1.0 和1.1）
    - Azure 檔案同步現在只有在已停用 TLS 1.0 和1.1 的伺服器上，才支援使用 TLS 1.2。 在此改善之前，如果伺服器上已停用 TLS 1.0 和1.1，伺服器註冊將會失敗。
- 同步和雲端階層處理的其他效能和可靠性改善
    - 此版本有數個可靠性和效能改進。 其中一些目標是要讓雲端階層處理更有效率，並在您已設定頻寬節流排程的情況下，以更好的方式 Azure 檔案同步。

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
- 不支援在已安裝 Azure 檔案同步代理程式的伺服器上執行 sysprep，而且可能會導致非預期的結果。 在部署伺服器映像並完成 sysprep 迷你設定之後，請安裝 Azure 檔案同步代理程式。

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

## <a name="agent-version-6300"></a>代理程式版本6.3.0。0
下列版本資訊適用于2019年6月27日發行之 Azure 檔案同步代理程式的版本6.3.0.0。 這些注意事項是針對版本6.0.0.0 所列的版本資訊以外的資訊。

此版本修正的問題清單：  
- 在 Windows Server 2012 R2 上透過 SMB 存取或流覽伺服器端點位置的速度很慢 
- 安裝 Azure 檔案同步 v6 代理程式後增加的 CPU 使用率
- 雲端階層處理遙測改進

## <a name="agent-version-6200"></a>代理程式版本6.2.0。0
下列版本資訊適用于2019年6月13日發行之 Azure 檔案同步代理程式的版本6.2.0.0。 這些注意事項是針對版本6.0.0.0 所列的版本資訊以外的資訊。

此版本修正的問題清單：  
- 建立伺服器端點之後，當背景召回將檔下載到伺服器時，可能會發生高 CPU 使用量
- 由於權杖過期，同步和雲端階層處理作業可能會失敗，併發生錯誤 ECS_E_SERVER_CREDENTIAL_NEEDED
- 如果下載檔案的 URL 包含保留字元，重新叫用檔案可能會失敗 

## <a name="agent-version-6100"></a>代理程式版本6.1.0。0
下列版本資訊適用于2019年5月6日發行之 Azure 檔案同步代理程式的版本6.1.0.0。 這些注意事項是針對版本6.0.0.0 所列的版本資訊以外的資訊。

此版本修正的問題清單：  
- Windows 系統管理中心無法在已安裝 Azure 檔案同步代理程式版本6.0 的伺服器上顯示代理程式版本和伺服器端點設定。

## <a name="agent-version-6000"></a>代理程式版本6.0.0。0
下列版本資訊適用于 Azure 檔案同步代理程式的版本6.0.0.0 （2019年4月22日發行）。

### <a name="improvements-and-issues-that-are-fixed"></a>增強功能和已修正的問題

- 代理程式自動更新支援
  - 我們聽到了您的意見反應，並已在 Azure 檔案同步伺服器代理程式中新增自動更新功能。 如需詳細資訊，請參閱[Azure 檔案同步代理程式更新原則](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#azure-file-sync-agent-update-policy)。
- 支援 Azure 檔案共用 Acl
  - Azure 檔案同步一律支援在伺服器端點之間同步處理 Acl，但 Acl 並未同步到雲端端點（Azure 檔案共用）。 此版本新增了在伺服器與雲端端點之間同步 Acl 的支援。
- 平行上傳和下載伺服器端點的同步會話 
  - 伺服器端點現在支援同時上傳和下載檔案。 不再等候下載完成，因此可以將檔案上傳到 Azure 檔案共用。 
- 新的雲端階層處理 Cmdlet 可取得磁片區和階層處理狀態
  - 現在可以使用兩個新的伺服器本機 PowerShell Cmdlet 來取得雲端階層處理和檔案重新叫用資訊。 它們可讓伺服器上的兩個事件通道記錄資訊：
    - StorageSyncFileTieringResult 將會列出尚未階層式所有檔案及其路徑，並根據原因來報告。
    - StorageSyncFileRecallResult 會報告所有檔案召回事件。 它會列出每個回收的檔案及其路徑，以及該召回的成功或錯誤。
  - 根據預設，兩個事件通道可以儲存最多 1 MB，您可以藉由增加事件通道大小來增加報告的檔案數量。
- FIPS 模式的支援
  - Azure 檔案同步現在支援在已安裝 Azure 檔案同步代理程式的伺服器上啟用 FIPS 模式。
    - 在伺服器上啟用 FIPS 模式之前，請在您的伺服器上安裝 Azure 檔案同步 agent 和[PackageManagement 模組](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2)。 如果已在伺服器上啟用 FIPS，請[手動](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)將[PackageManagement 模組](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2)下載至您的伺服器。
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

## <a name="agent-version-5200"></a>代理程式版本5.2.0。0
下列版本資訊適用于2019年4月4日發行之 Azure 檔案同步代理程式的版本5.2.0.0。 這些注意事項是針對版本5.0.2.0 所列的版本資訊以外的資訊。

此版本修正的問題清單：  
- 離線資料傳輸和資料傳輸繼續功能的可靠性改善
- 同步遙測改善

## <a name="agent-version-5100"></a>代理程式版本5.1.0。0
下列版本資訊適用于2019年3月7日發行之 Azure 檔案同步代理程式的版本5.1.0.0。 這些注意事項是針對版本5.0.2.0 所列的版本資訊以外的資訊。

此版本修正的問題清單：  
- 如果伺服器上的變更列舉失敗，檔案可能會無法與錯誤0x80c8031d （ECS_E_CONCURRENCY_CHECK_FAILED）同步處理
- 如果同步會話或檔案收到錯誤0x80072f78 （WININET_E_INVALID_SERVER_RESPONSE），同步處理將立即重試此作業
- 檔案可能無法同步，發生錯誤0x80c80203 （ECS_E_SYNC_INVALID_STAGED_FILE）
- 重新叫用檔案時，可能會發生高記憶體使用量
- 雲端階層處理遙測改進 

## <a name="agent-version-5020"></a>代理程式版本 5.0.2.0
下列版本資訊適用於 Azure 檔案同步代理程式版本 5.0.2.0 (在 2019 年 2 月 12 日發行)。

### <a name="improvements-and-issues-that-are-fixed"></a>增強功能和已修正的問題

- 支援 Azure Government 雲端
  - 我們已新增對 Azure Government 雲端的預覽支援。 這需要在允許清單中的訂用帳戶，並從 Microsoft 下載特殊的代理程式。 若要取得預覽的存取權，請直接透過電子[AzureFiles@microsoft.com](mailto:AzureFiles@microsoft.com)郵件傳送給我們。
- 支援重複資料刪除
    - 在 Windows Server 2016 和 Windows Server 2019 上啟用的雲端階層處理現已完整支援重複資料刪除。 在已啟用雲端階層處理的磁碟區上啟用重複資料刪除，可讓您在內部部署中快取更多檔案，而不需佈建更多儲存空間。
- 支援離線資料傳輸 (例如透過資料箱)
    - 透過您選擇的任何方式，輕鬆地將大量資料移轉到 Azure 檔案同步中。 您可以選擇 Azure 資料箱、AzCopy，甚至是協力廠商的遷移服務。 您不需要使用大量的頻寬以將資料送進 Azure。若使用資料箱，只要將它寄過去即可！ 若要深入了解，請參閱[離線資料傳輸文件](https://aka.ms/AFS/OfflineDataTransfer) \(英文\)。
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
