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
ms.openlocfilehash: 578d00d4bd65b3ffbfb6cdac439762344604e6b8
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804872"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Azure 檔案同步代理程式的版本資訊
Azure 檔案同步可讓您將組織的檔案共用集中在「Azure 檔案服務」中，而不需要犧牲內部部署檔案伺服器的靈活度、效能及相容性。 您的 Windows Server 安裝會轉換成 Azure 檔案共用的快速快取。 您可以使用 Windows Server 上可用的任何通訊協定來從本機存取資料 (包括 SMB、NFS 和 FTPS)。 您可以視需要存取多個散佈於世界各地的快取。

本文提供 Azure 檔案同步代理程式支援版本的版本資訊。

## <a name="supported-versions"></a>支援的版本
Azure 檔案同步代理程式支援下列版本：

| 里程碑 | 代理程式版本號碼 | 發行日期 | 狀態 |
|----|----------------------|--------------|------------------|
| 2019 年 12 月更新匯總 - [KB4522360](https://support.microsoft.com/help/4522360)| 9.1.0.0 | 2019 年 12 月 12 日 | 支援 |
| V9 版本 - [KB4522359](https://support.microsoft.com/help/4522359)| 9.0.0.0 | 2019 年 12 月 2 日 | 支援 |
| V8 版本 - [KB4511224](https://support.microsoft.com/help/4511224)| 8.0.0.0 | 2019 年 10 月 8 日 | 支援 |
| 2019 年 7 月更新匯總 - [KB4490497](https://support.microsoft.com/help/4490497)| 7.2.0.0 | 2019 年 7 月 24 日 | 支援 |
| 2019 年 7 月更新匯總 - [KB4490496](https://support.microsoft.com/help/4490496)| 7.1.0.0 | 2019 年 7 月 12 日 | 支援 |
| V7 版本 - [KB4490495](https://support.microsoft.com/help/4490495)| 7.0.0.0 | 2019年6月19日 | 支援 |
| 2019 年 6 月更新匯總 - [KB4489739](https://support.microsoft.com/help/4489739)| 6.3.0.0 | 六月 27, 2019 | 支援 - 代理版本將於 2020 年 4 月 21 日過期 |
| 2019 年 6 月更新匯總 - [KB4489738](https://support.microsoft.com/help/4489738)| 6.2.0.0 | 六月 13, 2019 | 支援 - 代理版本將於 2020 年 4 月 21 日過期 |
| 2019 年 5 月更新匯總 - [KB4489737](https://support.microsoft.com/help/4489737)| 6.1.0.0 | 2019年5月7日 | 支援 - 代理版本將於 2020 年 4 月 21 日過期 |
| V6 版本 - [KB4489736](https://support.microsoft.com/help/4489736)| 6.0.0.0 | 2019年4月21日 | 支援 - 代理版本將於 2020 年 4 月 21 日過期 |
| V5 版本 | 5.0.2.0 - 5.2.0.0 | N/A | 不支援 - 代理版本於 2020 年 3 月 18 日過期 |
| V4 版本 | 4.0.1.0 - 4.3.0.0 | N/A | 不支援 - 代理版本於 2019 年 11 月 6 日過期 |
| V3 版本 | 3.1.0.0 - 3.4.0.0 | N/A | 不支援 - 代理版本於 2019 年 8 月 19 日過期 |
| GA 前代理 | 1.1.0.0 - 3.0.13.0 | N/A | 不支援 - 代理程式版本已於 2018 年 10 月1 日過期 |

### <a name="azure-file-sync-agent-update-policy"></a>Azure 檔案同步代理程式更新原則
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-9100"></a>代理版本 9.1.0.0
以下發行說明適用於 2019 年 12 月 12 日發布的 Azure 檔同步代理版本 9.1.0.0。 這些註釋是版本 9.0.0.0 列出的發行說明的補充。

此版本中修復的問題:  
- 升級到 Azure 檔案同步代理版本 9.0 後,同步失敗,出現以下錯誤之一:
    - 0x8e5e044e (JET_errWriteConflict)
    - 0x8e5e0450 (JET_errInvalidSesid)
    - 0x8e5e0442 (JET_errInstanceUnavailable)

## <a name="agent-version-9000"></a>代理版本 9.0.0.0
以下發行說明適用於 Azure 檔同步代理的版本 9.0.0.0(2019 年 12 月 2 日發布)。

### <a name="improvements-and-issues-that-are-fixed"></a>增強功能和已修正的問題

- 自助服務復原支援
    - 用戶現在可以使用以前的版本功能還原其檔。 在 v9 版本之前,啟用了雲端分層的卷不支援以前的版本功能。 必須為每個卷單獨啟用此功能,其中存在啟用了雲分層的終結點。 若要深入了解，請參閱：  
[從早期版本與 VSS(陰影複製服務)進行自助服務還原](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service)。 
 
- 支援更多檔案分享大小 
    - Azure 檔案同步現在支援單個同步命名空間中多達 64TiB 和 1 億個檔。  
 
- 伺服器 2019 上的資料重複資料消除支援 
    - 現在,Windows Server 2019 上啟用了雲端分層,支援資料重複資料消除。 為了支援具有雲分層的卷上的數據重複數據消除,必須安裝 Windows 更新[KB4520062。](https://support.microsoft.com/help/4520062) 
 
- 改進檔案到層的最小檔案大小 
    - 檔到層的最小檔案大小現在基於檔案系統群集大小(將檔案系統叢集大小的兩倍)。 例如,預設情況下,NTFS 檔案系統群集大小為 4KB,生成的檔到層的最小檔大小為 8KB。 
 
- 網路連線測試 cmdlet 
    - 作為 Azure 檔同步配置的一部分,必須聯繫多個服務終結點。 它們各自都有自己的 DNS 名稱,伺服器需要訪問該名稱。 這些 URL 還特定於伺服器註冊到的區域。 註冊伺服器後,連接測試 cmdlet(PowerShell 和伺服器註冊實用程式)可用於測試與特定於此伺服器的所有 URL 的通訊。 當通訊不完整阻止伺服器完全使用 Azure 檔同步並且可用於微調代理和防火牆配置時,此 cmdlet 可幫助進行故障排除。  
 
        要執行網路連線測試,執行以下 PowerShell 命令: 
 
        匯入模組"C:\程式檔\Azure_存儲同步代理\存儲同步.管理.ServerCmdlet.dll"  
        測試儲存同步網路連線
 
- 開啟雲階層後移除伺服器終結點改進 
    - 與以前一樣,刪除伺服器終結點不會導致刪除 Azure 檔共享中的檔。 但是,本地伺服器上的重新分析點的行為已更改。 現在刪除伺服器終結點時,將刪除重新分析點(指向伺服器上不是本地的檔的指標)。 完全緩存的檔將保留在伺服器上。 進行此改進是為了防止在刪除伺服器終結點時[。](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) 如果重新建立伺服器終結點,將在伺服器上重新創建分層檔的重新分析點。  
 
- 改善效能和可靠性 
    - 減少召回失敗。 現在,根據網路頻寬自動調整召回大小。 
    - 將新伺服器添加到同步組時提高了下載性能。 
    - 由於約束衝突,檔未同步的數量減少。 
    - 如果伺服器終結點路徑是捲裝載點,則檔無法分層或意外在某些情況下被調用。
    
### <a name="evaluation-tool"></a>評估工具
在部署 Azure 檔案同步之前，您應該使用 Azure 檔案同步評估工具來評估其是否與您的系統相容。 此工具是 Azure PowerShell Cmdlet，可檢查檔案系統和資料集的潛在問題，例如不支援的字元或 OS 版本。 關於安裝和使用方式指示，請參閱規劃指南中[評估工具](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet)小節。 

### <a name="agent-installation-and-server-configuration"></a>代理程式安裝和伺服器設定
如需如何使用 Windows Server 安裝及設定 Azure 檔案同步代理程式的詳細資訊，請參閱[規劃 Azure 檔案同步部署](storage-sync-files-planning.md)和[如何部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)。

- 必須以提升的 (系統管理員) 權限安裝代理程式安裝套件。
- 納米伺服器部署選項不支援代理。
- 只有 Windows Server 2019、Windows Server 2016 和 Windows Server 2012 R2 可支援此代理程式。
- 代理程式需要至少 2 GiB 的記憶體。 如果伺服器在啟用動態記憶體的虛擬機器中執行，則 VM 的記憶體應最少設定為 2048 MiB。
- 在已將系統磁碟區資訊 (SVI) 目錄壓縮的磁碟區上，儲存體同步代理程式 (FileSyncSvc) 服務不支援其中的伺服器端點。 此設定會導致非預期的結果。

### <a name="interoperability"></a>互通性
- 防毒程式、備份及其他存取階層式檔案的應用程式，除非採用離線屬性並略過讀取這些檔案的內容，否則將會導致不想要的重新叫用。 如需詳細資訊，請參閱[針對 Azure 檔案同步進行疑難排解](storage-sync-files-troubleshoot.md)。
- 如果檔案因為檔案檢測而遭到封鎖，檔案伺服器資源管理員 (FSRM) 檔案檢測會造成無止盡的同步處理失敗。
- 不支援在安裝了 Azure 檔案同步代理的伺服器上運行 sysprep,並可能導致意外的結果。 在部署伺服器映像並完成 sysprep 迷你設定之後，請安裝 Azure 檔案同步代理程式。

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
- 如果未在刪除伺服器端點之前回收階層式檔案，階層式檔案會變成無法存取的狀態。 若要還原檔案的存取性，請重新建立伺服器端點。 如果在刪除伺服器端點後過了 30 天，或如果雲端端點已刪除，將會無法使用未重新呼叫的階層式檔案。 要瞭解更多資訊,請參閱[刪除伺服器終結點後,伺服器無法存取分層檔](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)。
- 系統磁碟區上不支援雲端階層。 若要在系統磁碟區上建立伺服器端點，請於建立伺服器端點時，停用雲端階層處理。
- 只有叢集磁碟才支援容錯移轉叢集，但叢集共用磁碟區 (CSV) 不提供支援。
- 伺服器端點無法為巢狀。 它可與其他端點平行並存於相同的磁碟區上。
- 請勿在伺服器端點位置內儲存 OS 或應用程式分頁檔。
- 如果將伺服器重新命名，並不會更新入口網站中的伺服器名稱。

### <a name="cloud-endpoint"></a>雲端端點
- Azure 檔案同步支援直接對 Azure 檔案共用進行變更。 不過，在 Azure 檔案共用上所做的任何變更，都必須先由 Azure 檔案同步變更偵測作業做出探索。 針對雲端端點的變更偵測作業，每隔 24 小時會起始一次。 要立即同步 Azure 檔案共享中更改的檔案,可以使用[Invoke-AzStorageSyncChange 檢測](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection)電源 Shell cmdlet 手動啟動檢測 Azure 檔案共享中的更改。 此外，透過 REST 通訊協定對 Azure 檔案共用所做的變更，將不會更新 SMB 上次修改時間，而且將不會同步顯示為變更。
- 可將儲存體同步服務及 (或) 儲存體帳戶移至現有 Azure AD 租用戶中的不同資源群組或訂用帳戶。 如果移動儲存體帳戶，您需要將儲存體帳戶的存取權給予混合式檔案同步服務 (請參閱[確保 Azure 檔案同步有儲存體帳戶的存取權](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac))。

    > [!Note]  
    > Azure 檔案同步不支援將訂用帳戶移至不同的 Azure AD 租用戶。

### <a name="cloud-tiering"></a>雲端階層處理
- 如果使用 Robocopy 將階層式檔案複製到另一個位置，則所產生的檔案不會進行階層處理。 因為 Robocopy 未正確地在複製作業中包含該屬性，所以可能設定離線屬性。
- 使用 robocopy 複製檔案時，請使用 /MIR 選項來保留檔案的時間戳記。 這可確保較舊的檔案會比最近存取的檔案更快進行階層處理。
- 如果 pagefile.sys 位於啟用了雲端的卷上,則檔可能無法分層。 pagefile.sys 應位於禁用雲分層的卷上。

## <a name="agent-version-8000"></a>代理版本 8.0.0.0
以下發行說明適用於 Azure 檔同步代理的版本 8.0.0.0(2019 年 10 月 8 日發布)。

### <a name="improvements-and-issues-that-are-fixed"></a>增強功能和已修正的問題

- 回復效能改進
    - 通過 Azure 備份完成恢復的恢復時間更快。 還原的檔將更快地同步回 Azure 檔同步伺服器。 
- 改進的雲分層門戶體驗  
    - 如果具有無法調用的分層文件,現在可以查看伺服器終結點屬性中的撤回錯誤。 此外,如果雲分層篩選器驅動程式未載入到伺服器上,則伺服器終結點運行狀況現在將顯示錯誤和緩解步驟。
- 更簡單的代理安裝
    - 不再需要 Az_AzureRM PowerShell 模組來註冊伺服器,使安裝更簡單、更快速。
- 各種效能與可靠性改進

### <a name="evaluation-tool"></a>評估工具
在部署 Azure 檔案同步之前，您應該使用 Azure 檔案同步評估工具來評估其是否與您的系統相容。 此工具是 Azure PowerShell Cmdlet，可檢查檔案系統和資料集的潛在問題，例如不支援的字元或 OS 版本。 關於安裝和使用方式指示，請參閱規劃指南中[評估工具](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet)小節。 

### <a name="agent-installation-and-server-configuration"></a>代理程式安裝和伺服器設定
如需如何使用 Windows Server 安裝及設定 Azure 檔案同步代理程式的詳細資訊，請參閱[規劃 Azure 檔案同步部署](storage-sync-files-planning.md)和[如何部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)。

- 必須以提升的 (系統管理員) 權限安裝代理程式安裝套件。
- 納米伺服器部署選項不支援代理。
- 只有 Windows Server 2019、Windows Server 2016 和 Windows Server 2012 R2 可支援此代理程式。
- 代理程式需要至少 2 GiB 的記憶體。 如果伺服器在啟用動態記憶體的虛擬機器中執行，則 VM 的記憶體應最少設定為 2048 MiB。
- 在已將系統磁碟區資訊 (SVI) 目錄壓縮的磁碟區上，儲存體同步代理程式 (FileSyncSvc) 服務不支援其中的伺服器端點。 此設定會導致非預期的結果。

### <a name="interoperability"></a>互通性
- 防毒程式、備份及其他存取階層式檔案的應用程式，除非採用離線屬性並略過讀取這些檔案的內容，否則將會導致不想要的重新叫用。 如需詳細資訊，請參閱[針對 Azure 檔案同步進行疑難排解](storage-sync-files-troubleshoot.md)。
- 如果檔案因為檔案檢測而遭到封鎖，檔案伺服器資源管理員 (FSRM) 檔案檢測會造成無止盡的同步處理失敗。
- 不支援在安裝了 Azure 檔案同步代理的伺服器上運行 sysprep,並可能導致意外的結果。 在部署伺服器映像並完成 sysprep 迷你設定之後，請安裝 Azure 檔案同步代理程式。

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
- 如果未在刪除伺服器端點之前回收階層式檔案，階層式檔案會變成無法存取的狀態。 若要還原檔案的存取性，請重新建立伺服器端點。 如果在刪除伺服器端點後過了 30 天，或如果雲端端點已刪除，將會無法使用未重新呼叫的階層式檔案。 要瞭解更多資訊,請參閱[刪除伺服器終結點後,伺服器無法存取分層檔](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)。
- 系統磁碟區上不支援雲端階層。 若要在系統磁碟區上建立伺服器端點，請於建立伺服器端點時，停用雲端階層處理。
- 只有叢集磁碟才支援容錯移轉叢集，但叢集共用磁碟區 (CSV) 不提供支援。
- 伺服器端點無法為巢狀。 它可與其他端點平行並存於相同的磁碟區上。
- 請勿在伺服器端點位置內儲存 OS 或應用程式分頁檔。
- 如果將伺服器重新命名，並不會更新入口網站中的伺服器名稱。

### <a name="cloud-endpoint"></a>雲端端點
- Azure 檔案同步支援直接對 Azure 檔案共用進行變更。 不過，在 Azure 檔案共用上所做的任何變更，都必須先由 Azure 檔案同步變更偵測作業做出探索。 針對雲端端點的變更偵測作業，每隔 24 小時會起始一次。 要立即同步 Azure 檔案共享中更改的檔案,可以使用[Invoke-AzStorageSyncChange 檢測](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection)電源 Shell cmdlet 手動啟動檢測 Azure 檔案共享中的更改。 此外，透過 REST 通訊協定對 Azure 檔案共用所做的變更，將不會更新 SMB 上次修改時間，而且將不會同步顯示為變更。
- 可將儲存體同步服務及 (或) 儲存體帳戶移至現有 Azure AD 租用戶中的不同資源群組或訂用帳戶。 如果移動儲存體帳戶，您需要將儲存體帳戶的存取權給予混合式檔案同步服務 (請參閱[確保 Azure 檔案同步有儲存體帳戶的存取權](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac))。

    > [!Note]  
    > Azure 檔案同步不支援將訂用帳戶移至不同的 Azure AD 租用戶。

### <a name="cloud-tiering"></a>雲端階層處理
- 如果使用 Robocopy 將階層式檔案複製到另一個位置，則所產生的檔案不會進行階層處理。 因為 Robocopy 未正確地在複製作業中包含該屬性，所以可能設定離線屬性。
- 使用 robocopy 複製檔案時，請使用 /MIR 選項來保留檔案的時間戳記。 這可確保較舊的檔案會比最近存取的檔案更快進行階層處理。

## <a name="agent-version-7200"></a>代理版本 7.2.0.0
以下發行說明適用於 2019 年 7 月 24 日發布的 Azure 檔同步代理的版本 7.2.0.0。 這些註釋是版本 7.0.0.0 列出的發行說明的補充。

此版本修正的問題清單：  
- 如果代理設定為空,則儲存同步代理 (FileSyncSvc) 崩潰。
- 如果伺服器上的多個終結點具有相同的名稱,伺服器終結點將啟動BCDR(錯誤0x80c80257 - ECS_E_BCDR_IN_PROGRESS)。
- 雲分層可靠性改進。

## <a name="agent-version-7100"></a>代理版本 7.1.0.0
以下發行說明適用於 2019 年 7 月 12 日發布的 Azure 檔同步代理的版本 7.1.0.0。 這些註釋是版本 7.0.0.0 列出的發行說明的補充。

此版本修正的問題清單：  
- 在 Windows Server 2012 R2 上透過 SMB 訪問或瀏覽伺服器終結點位置的速度很慢。 
- 安裝 Azure 檔同步 v6 代理後提高 CPU 利用率。
- 雲分層遙測改進。
- 針對雲端階層處理和同步處理的其他可靠性改進。

## <a name="agent-version-7000"></a>代理版本 7.0.0.0
以下發行說明適用於 Azure 檔同步代理的版本 7.0.0.0(2019 年 6 月 19 日發布)。

### <a name="improvements-and-issues-that-are-fixed"></a>增強功能和已修正的問題

- 支援更多檔案分享大小
    - 隨著較大 Azure 檔共享的預覽,我們也在增加對檔同步的支援限制。 在第一步中,Azure 檔同步現在支援單個同步命名空間中多達 25 TB 和 5000 萬個檔。 要申請大型檔案分享預覽,請填寫此表單https://aka.ms/azurefilesatscalesurvey。 
- 支援儲存帳戶上的防火牆和虛擬網路設定
    - Azure 檔案同步現在支援儲存帳戶上的防火牆和虛擬網路設置。 要將部署設定為使用防火牆和虛擬網路設定,請參閱[設定防火牆和虛擬網路設定](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)。
- PowerShell cmdlet 可立即同步 Azure 檔案分享中更改的檔案
    - 要立即同步 Azure 檔案共享中更改的檔案,可以使用 Invoke-AzStorageSyncChange 檢測電源 Shell cmdlet 手動啟動檢測 Azure 檔案共享中的更改。 此 cmdlet 適用於某些類型的自動行程在 Azure 檔案共享中進行更改或由管理員執行更改(例如將檔案和目錄移動到共享中)的情況。 對於最終使用者更改,建議在 IaaS VM 中安裝 Azure 檔同步代理,並讓最終使用者透過 IaaS VM 訪問檔案共用。 這樣,所有更改都將快速同步到其他代理,而無需使用 Invoke-AzStorageSyncChange 檢測 cmdlet。 要瞭解更多資訊,請參閱[調用-Azstorage 同步更改檢測](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection)文檔。
- 如果遇到未同步的檔,則改進了門戶體驗
    - 如果您有無法同步的文件,我們現在會區分門戶中的瞬態錯誤和持久性錯誤。 暫時性錯誤通常無需管理員操作即可自行解決。 例如,在關閉檔句柄之前,當前正在使用的檔案不會同步。 對於持久性錯誤,我們現在顯示受每個錯誤影響的文件數。 持久錯誤計數也顯示在同步組中所有伺服器終結點未同步列的檔中。
- 改進的 Azure 備份檔級還原
    - 現在,使用 Azure 備份還原的單個檔將更快地檢測到並同步到伺服器終結點。
- 改進了雲分層召回 cmdlet 可靠性 
    - "呼叫-儲存同步檔案呼叫 cmdlet"現在允許客戶指定每個檔案重試計數和每個檔重試延遲(類似於 robocopy)。 以前,此 cmdlet 會隨機呼叫給定路徑下的所有分層檔。 使用新的 -Order 參數,此 cmdlet 將首先調用最熱門的數據,並遵循雲分層策略(如果滿足日期策略或滿足卷可用空間,則停止調用;以先發生者為準)。
- 只支援 TLS 1.2(關閉 TLS 1.0 和 1.1)
    - Azure 檔案同步現在僅支援在禁用 TLS 1.0 和 1.1 的伺服器上使用 TLS 1.2。 在此改進之前,如果 TLS 1.0 和 1.1 在伺服器上被禁用,則伺服器註冊將失敗。
- 同步與雲端分層的其他效能和可靠性改進
    - 此版本中有幾個可靠性和性能改進。 其中一些旨在使雲分層更高效,而 Azure 檔同步作為一個整體,在您設置了頻寬限制計劃時,可以更好地工作。

### <a name="evaluation-tool"></a>評估工具
在部署 Azure 檔案同步之前，您應該使用 Azure 檔案同步評估工具來評估其是否與您的系統相容。 此工具是 Azure PowerShell Cmdlet，可檢查檔案系統和資料集的潛在問題，例如不支援的字元或 OS 版本。 關於安裝和使用方式指示，請參閱規劃指南中[評估工具](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet)小節。 

### <a name="agent-installation-and-server-configuration"></a>代理程式安裝和伺服器設定
如需如何使用 Windows Server 安裝及設定 Azure 檔案同步代理程式的詳細資訊，請參閱[規劃 Azure 檔案同步部署](storage-sync-files-planning.md)和[如何部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)。

- 必須以提升的 (系統管理員) 權限安裝代理程式安裝套件。
- 納米伺服器部署選項不支援代理。
- 只有 Windows Server 2019、Windows Server 2016 和 Windows Server 2012 R2 可支援此代理程式。
- 代理程式需要至少 2 GiB 的記憶體。 如果伺服器在啟用動態記憶體的虛擬機器中執行，則 VM 的記憶體應最少設定為 2048 MiB。
- 在已將系統磁碟區資訊 (SVI) 目錄壓縮的磁碟區上，儲存體同步代理程式 (FileSyncSvc) 服務不支援其中的伺服器端點。 此設定會導致非預期的結果。

### <a name="interoperability"></a>互通性
- 防毒程式、備份及其他存取階層式檔案的應用程式，除非採用離線屬性並略過讀取這些檔案的內容，否則將會導致不想要的重新叫用。 如需詳細資訊，請參閱[針對 Azure 檔案同步進行疑難排解](storage-sync-files-troubleshoot.md)。
- 如果檔案因為檔案檢測而遭到封鎖，檔案伺服器資源管理員 (FSRM) 檔案檢測會造成無止盡的同步處理失敗。
- 不支援在安裝了 Azure 檔案同步代理的伺服器上運行 sysprep,並可能導致意外的結果。 在部署伺服器映像並完成 sysprep 迷你設定之後，請安裝 Azure 檔案同步代理程式。

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

## <a name="agent-version-6300"></a>代理版本 6.3.0.0
以下發行說明適用於 2019 年 6 月 27 日發布的 Azure 檔同步代理版本 6.3.0.0。 這些註釋是版本 6.0.0.0 列出的發行說明的補充。

此版本修正的問題清單：  
- 在 Windows 伺服器 2012 R2 上透過 SMB 存取或瀏覽伺服器終結點位置的速度很慢 
- 安裝 Azure 檔案同步 v6 代理後提高 CPU 利用率
- 雲分層遙測改進

## <a name="agent-version-6200"></a>代理版本 6.2.0.0
以下發行說明適用於 2019 年 6 月 13 日發布的 Azure 檔同步代理版本 6.2.0.0。 這些註釋是版本 6.0.0.0 列出的發行說明的補充。

此版本修正的問題清單：  
- 建立伺服器終結點後,後台撤回將檔案下載到伺服器時,可能會出現高 CPU 使用率
- 由於權杖過期同步和雲端操作可能會失敗,ECS_E_SERVER_CREDENTIAL_NEEDED錯誤
- 如果下載檔案的網址 包含保留字元,則撤回檔案可能會失敗 

## <a name="agent-version-6100"></a>代理版本 6.1.0.0
以下發行說明適用於 2019 年 5 月 6 日發布的 Azure 檔同步代理版本 6.1.0.0。 這些註釋是版本 6.0.0.0 列出的發行說明的補充。

此版本修正的問題清單：  
- Windows 管理中心無法在安裝了 Azure 檔同步代理版本 6.0 的伺服器上顯示代理版本和伺服器終結點配置。

## <a name="agent-version-6000"></a>代理版本 6.0.0.0
以下發行說明適用於 Azure 檔同步代理的版本 6.0.0.0(2019 年 4 月 22 日發布)。

### <a name="improvements-and-issues-that-are-fixed"></a>增強功能和已修正的問題

- 代理自動更新支援
  - 我們已收到您的反饋,並將自動更新功能添加到 Azure 檔同步伺服器代理中。 有關詳細資訊,請參閱[Azure 檔同步代理更新原則](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#azure-file-sync-agent-update-policy)。
- 支援 Azure 檔案分享 ACL
  - Azure 檔案同步始終支援在伺服器終結點之間同步 ACL,但 ACL 未同步到雲端終結點(Azure 檔案共享)。 此版本添加了對伺服器和雲端終結點之間同步 ACL 的支援。
- 伺服器終結點的並行上載並下載同步工作階段 
  - 伺服器終結點現在支援同時上傳和下載檔。 無需再等待下載完成,以便檔可以上傳到 Azure 檔共用。 
- 新的雲分層 cmdlet,以取得卷和分層狀態
  - 兩個新的伺服器本地 PowerShell cmdlet 現在可用於獲取雲端分層和檔撤回資訊。 它們使來自伺服器上兩個事件頻道的紀錄記錄資訊可用:
    - 獲取存儲同步檔分層結果將列出所有檔及其路徑尚未分層,並報告原因。
    - 取得儲存同步檔案撤回結果報告所有檔撤回事件。 它列出了每個被召回的檔及其路徑以及該調用的成功或錯誤。
  - 預設情況下,兩個事件通道每個都可以儲存多達 1 MB - 您可以通過增加事件通道大小來增加報告的檔案量。
- 支援 FIPS 模式
  - Azure 檔案同步現在支援在安裝了 Azure 檔案同步代理的伺服器上啟用 FIPS 模式。
    - 在伺服器上啟用 FIPS 模式之前,請在伺服器上安裝 Azure 檔案同步代理與[套件管理模組](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2)。 如果已在伺服器上啟用了 FIPS,[請手動將](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)[包管理模組](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2)下載到您的伺服器。
- 雲分層和同步的其他可靠性改進

### <a name="evaluation-tool"></a>評估工具
在部署 Azure 檔案同步之前，您應該使用 Azure 檔案同步評估工具來評估其是否與您的系統相容。 此工具是 Azure PowerShell Cmdlet，可檢查檔案系統和資料集的潛在問題，例如不支援的字元或 OS 版本。 關於安裝和使用方式指示，請參閱規劃指南中[評估工具](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet)小節。 

### <a name="agent-installation-and-server-configuration"></a>代理程式安裝和伺服器設定
如需如何使用 Windows Server 安裝及設定 Azure 檔案同步代理程式的詳細資訊，請參閱[規劃 Azure 檔案同步部署](storage-sync-files-planning.md)和[如何部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)。

- 必須以提升的 (系統管理員) 權限安裝代理程式安裝套件。
- 納米伺服器部署選項不支援代理。
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

## <a name="agent-version-5200"></a>代理版本 5.2.0.0
以下發行說明適用於 2019 年 4 月 4 日發布的 Azure 檔同步代理的版本 5.2.0.0。 這些註釋是版本 5.0.2.0 列出的發行說明的補充。

此版本修正的問題清單：  
- 離線資料傳輸和資料傳輸復原功能的可靠性改進
- 同步遙測改進

## <a name="agent-version-5100"></a>代理版本 5.1.0.0
以下發行說明適用於 2019 年 3 月 7 日發布的 Azure 檔同步代理的版本 5.1.0.0。 這些註釋是版本 5.0.2.0 列出的發行說明的補充。

此版本修正的問題清單：  
- 如果變更枚舉在伺服器上失敗,檔案可能無法與錯誤 0x80c8031d (ECS_E_CONCURRENCY_CHECK_FAILED) 同步
- 如果同步作業階段或檔收到錯誤 0x80072f78 (WININET_E_INVALID_SERVER_RESPONSE),則同步現在將重試操作
- 檔案可能無法與錯誤 0x80c80203 (ECS_E_SYNC_INVALID_STAGED_FILE) 同步
- 召回檔案時可能會出現高記憶體使用率
- 雲分層遙測改進 

## <a name="agent-version-5020"></a>代理程式版本 5.0.2.0
下列版本資訊適用於 Azure 檔案同步代理程式版本 5.0.2.0 (在 2019 年 2 月 12 日發行)。

### <a name="improvements-and-issues-that-are-fixed"></a>增強功能和已修正的問題

- 支援 Azure Government 雲端
  - 我們已新增對 Azure Government 雲端的預覽支援。 這需要在允許清單中的訂用帳戶，並從 Microsoft 下載特殊的代理程式。 要存取預覽版,請直接傳送電子[AzureFiles@microsoft.com](mailto:AzureFiles@microsoft.com)郵件至 。
- 支援重複資料刪除
    - 在 Windows Server 2016 和 Windows Server 2019 上啟用的雲端階層處理現已完整支援重複資料刪除。 在已啟用雲端階層處理的磁碟區上啟用重複資料刪除，可讓您在內部部署中快取更多檔案，而不需佈建更多儲存空間。
- 支援離線資料傳輸 (例如透過資料箱)
    - 透過您選擇的任何方式，輕鬆地將大量資料移轉到 Azure 檔案同步中。 您可以選擇 Azure 資料框、AzCopy 甚至第三方遷移服務。 您不需要使用大量的頻寬以將資料送進 Azure。若使用資料箱，只要將它寄過去即可！ 若要深入了解，請參閱[離線資料傳輸文件](https://aka.ms/AFS/OfflineDataTransfer) \(英文\)。
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
