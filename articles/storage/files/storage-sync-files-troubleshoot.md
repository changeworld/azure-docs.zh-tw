---
title: 對 Azure 檔案同步進行疑難排解 | Microsoft Docs
description: 針對 Azure 檔案同步常見問題進行疑難排解。
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 1/22/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: ebe5ddf72e13b1a66ded7a90976e0b6209a26dfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060974"
---
# <a name="troubleshoot-azure-file-sync"></a>針對 Azure 檔案同步進行移難排解
使用 Azure 檔案同步，將組織的檔案共用集中在 Azure 檔案服務中，同時保有內部部署檔案伺服器的彈性、效能及相容性。 Azure 檔案同步會將 Windows Server 轉換成 Azure 檔案共用的快速快取。 您可以使用 Windows Server 上可用的任何通訊協定來從本機存取資料，包括 SMB、NFS 和 FTPS。 您可以視需要存取多個散佈於世界各地的快取。

本文旨在協助您針對使用 Azure 檔案同步部署時所發生的問題進行疑難排解，並解決這些問題。 文中也說明如果需要更深入調查問題，如何從系統收集重要的記錄。 如果您找不到問題的答案，可透過下列管道 (依先後順序) 和我們連絡：

1. [Azure 儲存體論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)。
2. [Azure 檔案服務 UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) \(英文\)。
3. Microsoft 支援服務。 若要建立新的支援要求，在 Azure 入口網站的 [說明]**** 索引標籤上，選取 [說明 + 支援]**** 按鈕，然後選取 [新增支援要求]****。

## <a name="im-having-an-issue-with-azure-file-sync-on-my-server-sync-cloud-tiering-etc-should-i-remove-and-recreate-my-server-endpoint"></a>我在伺服器上的 Azure 檔案同步有問題 (同步、雲端分層等)。 我是否應移除並重新建立伺服器端點？
[!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]

## <a name="agent-installation-and-server-registration"></a>代理程式安裝和伺服器註冊
<a id="agent-installation-failures"></a>**排除代理安裝故障**  
如果 Azure 檔案同步代理程式安裝失敗，使用提升權限的命令提示字元，執行下列命令，以啟用代理程式安裝期間的記錄：

```
StorageSyncAgent.msi /l*v AFSInstaller.log
```

檢閱 installer.log 判斷安裝失敗的原因。

<a id="agent-installation-on-DC"></a>**在 Active Directory 網域控制站上安裝代理程式失敗**  
如果您嘗試在 PDC 角色擁有者執行 Windows Server 2008 R2 或更低 OS 版本的 Active Directory 網域控制站上安裝同步代理程式，則可能會遇到無法安裝同步代理程式的問題。

若要解決此問題，請將 PDC 角色轉移到另一個執行 Windows Server 2012 R2 或更新版本的網域控制站，然後安裝同步代理程式。

<a id="parameter-is-incorrect"></a>**訪問 Windows Server 2012 R2 上的卷失敗，失敗：參數不正確**  
在 Windows Server 2012 R2 上創建伺服器終結點後，訪問卷時會發生以下錯誤：

磁碟機號：*無法訪問。  
參數錯誤。

要解析，請安裝 Windows Server 2012 R2 的最新更新並重新啟動伺服器。

<a id="server-registration-missing-subscriptions"></a>**伺服器註冊不列出所有 Azure 訂閱**  
使用伺服器註冊.exe 註冊伺服器時，按一下 Azure 訂閱下拉清單時缺少訂閱。

出現此問題是因為伺服器註冊.exe 當前不支援多租戶環境。 此問題將在將來的 Azure 檔同步代理更新中修復。

要解決此問題，請使用以下 PowerShell 命令註冊伺服器：

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<guid>" -TenantID "<guid>"
Register-AzureRmStorageSyncServer -SubscriptionId "<guid>" -ResourceGroupName "<string>" -StorageSyncServiceName "<string>"
```

<a id="server-registration-prerequisites"></a>**伺服器註冊顯示以下消息："缺少先決條件"**  
如果 PowerShell 5.1 上未安裝 Az 或 AzureRM PowerShell 模組，則會顯示此消息。 

> [!Note]  
> 伺服器註冊.exe 不支援 PowerShell 6.x。 您可以使用 PowerShell 6.x 上的註冊-AzStorageSyncServer Cmdlet 來註冊伺服器。

要在 PowerShell 5.1 上安裝 Az 或 AzureRM 模組，請執行以下步驟：

1. 從提升的命令提示符鍵入**電源殼**並命中輸入。
2. 按照以下文檔安裝最新的 Az 或 AzureRM 模組：
    - [Az 模組（要求 .NET 4.7.2）](https://go.microsoft.com/fwlink/?linkid=2062890)
    - [AzureRM 模組]( https://go.microsoft.com/fwlink/?linkid=856959)
3. 執行 ServerRegistration.exe，然後完成精靈的指示向儲存體同步服務註冊伺服器。

<a id="server-already-registered"></a>**伺服器註冊顯示以下消息："此伺服器已註冊"** 

![[伺服器註冊] 對話方塊的螢幕擷取畫面顯示 [此伺服器已註冊] 錯誤訊息](media/storage-sync-files-troubleshoot/server-registration-1.png)

如果先前已向儲存體同步服務註冊該伺服器，就會出現此訊息。 若要向目前的儲存體同步服務取消註冊伺服器，再向新的儲存體同步服務註冊，請完成[向 Azure 檔案同步取消註冊伺服器](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service)中所述的步驟。

如果伺服器未列在"存儲同步服務"中的 **"已註冊伺服器**"下，則在要取消註冊的伺服器上運行以下 PowerShell 命令：

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> 如果伺服器是叢集的一部分，您可以使用選擇性的 *Reset-StorageSyncServer -CleanClusterRegistration* 參數，一併移除叢集的註冊。

<a id="web-site-not-trusted"></a>**註冊伺服器時，我看到許多"網站不受信任的"回應。為什麼？**  
如果在伺服器註冊期間啟用了**增強的 Internet Explorer 安全性**原則，便會發生此問題。 若想進一步了解如何正確停用**增強的 Internet Explorer 安全性**原則，請參閱[準備 Windows Server 以搭配 Azure 檔案同步使用](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync)和[如何部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)。

<a id="server-registration-missing"></a>**伺服器未列在 Azure 門戶中的註冊伺服器下**  
如果伺服器未列在存儲同步服務的 **"已註冊伺服器**"下：
1. 登入您要註冊的伺服器。
2. 開啟 [檔案總管]，然後移至儲存體同步代理程式的安裝目錄 (預設位置是 C:\Program Files\Azure\StorageSyncAgent)。 
3. 執行 ServerRegistration.exe，然後完成精靈的指示向儲存體同步服務註冊伺服器。

## <a name="sync-group-management"></a>同步群組管理
<a id="cloud-endpoint-using-share"></a>**雲終結點創建失敗，出現此錯誤："指定的 Azure 檔共用已被其他雲終結點使用"**  
如果 Azure 檔案共用已由另一個雲端端點使用中，則會發生這個錯誤。 

如果您看到這個訊息，而 Azure 檔案共用目前未由雲端端點使用中，請執行下列步驟，以清除 Azure 檔案共用上的 Azure 檔案同步中繼資料：

> [!Warning]  
> 刪除 Azure 檔案共用上目前正由雲端端點使用中的中繼資料時，會導致 Azure 檔案同步作業失敗。 

1. 在 Azure 入口網站中，移至您的 Azure 檔案共用。  
2. 按右鍵 Azure 檔共用，然後選擇 **"編輯中繼資料**"。
3. 按右鍵 **"同步服務**"，然後選擇 **"刪除**"。

<a id="cloud-endpoint-authfailed"></a>**雲終結點創建失敗，出現此錯誤："授權失敗"**  
如果您的使用者帳戶沒有足夠的許可權創建雲終結點，則會發生此錯誤。 

若要建立雲端端點，您的使用者帳戶必須具有下列 Microsoft 授權權限：  
* 讀取：取得角色定義
* 寫入：建立或更新自訂角色定義
* 讀取：取得角色指派
* 寫入：建立角色指派

下列內建角色具有所需的 Microsoft 授權權限：  
* 擁有者
* 使用者存取系統管理員

判斷您的使用者帳戶角色是否具有所需的權限：  
1. 在 Azure 入口網站中，按一下 [資源群組]****。
2. 選取儲存體帳戶所在的資源群組，然後選取 [存取控制 (IAM)]****。
3. 選取 [角色指派]**** 索引標籤。
4. 選擇使用者帳戶**的角色**（例如，擁有者或參與者）。
5. 在 **"資來源提供者"** 清單中，選擇**Microsoft 授權**。 
    * **角色指派**應具有 **"讀取**和**寫入"** 許可權。
    * **角色定義**應具有 **"讀****寫"** 許可權。

<a id="-2134375898"></a>**伺服器終結點創建失敗，出現此錯誤："MgmtServerJobFailed"（錯誤代碼：-2134375898 或 0x80c80226）**  
如果伺服器端點路徑位於系統磁碟區上，而且已啟用雲端階層處理，就會發生此錯誤。 系統磁碟區上不支援雲端階層。 若要在系統磁碟區上建立伺服器端點，請於建立伺服器端點時，停用雲端階層處理。

<a id="-2147024894"></a>**伺服器終結點創建失敗，出現此錯誤："MgmtServerJobFailed"（錯誤代碼：-2147024894 或 0x80070002）**  
如果指定的伺服器端點路徑無效，就會發生這個錯誤。 確認指定的伺服器端點路徑是本機連結的 NTFS 磁碟區。 請注意，Azure 檔同步不支援映射的磁碟機作為伺服器終結點路徑。

<a id="-2134375640"></a>**伺服器終結點創建失敗，出現此錯誤："MgmtServerJobFailed"（錯誤代碼：-2134375640 或 0x80c80328）**  
如果指定的伺服器終結點路徑不是 NTFS 卷，則會發生此錯誤。 確認指定的伺服器端點路徑是本機連結的 NTFS 磁碟區。 請注意，Azure 檔同步不支援映射的磁碟機作為伺服器終結點路徑。

<a id="-2134347507"></a>**伺服器終結點創建失敗，出現此錯誤："MgmtServerJobFailed"（錯誤代碼：-2134347507 或 0x80c8710d）**  
發生此錯誤的原因是 Azure 檔案同步不支援磁碟區上的伺服器端點，因為這些磁碟區具有壓縮的「系統磁碟區資訊」資料夾。 若要解決此問題，請將「系統磁碟區資訊」資料夾解壓縮。 如果「系統磁碟區資訊」資料夾是磁碟區上唯一已壓縮的資料夾，請執行下列步驟：

1. 下載[PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec)工具。
2. 從提升的命令提示符運行以下命令，以啟動在系統帳戶下運行的命令提示 **：PsExec.exe-i-s-d cmd**
3. 從系統帳戶下執行的命令提示字元中，輸入下列命令，然後按 Enter 鍵：   
    **cd /d"磁碟機號：*系統批量資訊"**  
    **緊湊 /u /s**

<a id="-2134376345"></a>**伺服器終結點創建失敗，出現此錯誤："MgmtServerJobFailed"（錯誤代碼：-2134376345 或 0x80C80067）**  
如果達到每個伺服器的伺服器端點限制，就會發生此錯誤。 Azure 檔案同步目前支援每個伺服器最多 30 個伺服器端點。 有關詳細資訊，請參閱[Azure 檔同步縮放目標](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-file-sync-scale-targets)。

<a id="-2134376427"></a>**伺服器終結點創建失敗，出現此錯誤："MgmtServerJobFailed"（錯誤代碼：-2134376427 或 0x80c80015）**  
如果另一個伺服器端點已經同步指定的伺服器端點路徑，就會發生此錯誤。 Azure 檔案同步不支援多個伺服器端點同步相同的目錄或磁碟區。

<a id="-2160590967"></a>**伺服器終結點創建失敗，出現此錯誤："MgmtServerJobFailed"（錯誤代碼：-2160590967 或 0x80c80077）**  
如果伺服器終結點路徑包含孤立的分層檔，則會發生此錯誤。 如果最近刪除了伺服器終結點，請等待孤立分層檔清理完成。 啟動孤立分層檔清理後，事件 ID 6662 將記錄到遙測事件日誌。 完成孤立分層檔清理後，將記錄事件 ID 6661，並且可以使用該路徑重新創建伺服器終結點。 如果在記錄事件 ID 6661 後伺服器終結點創建失敗，則刪除孤立的分層檔，在[刪除伺服器終結點部分後，在伺服器上無法訪問分層檔中](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)記錄的步驟。

<a id="-2134347757"></a>**伺服器終結點刪除失敗，出現此錯誤："MgmtServerJob 過期"（錯誤代碼：-2134347757 或 0x80c87013）**  
如果伺服器離線或沒有網路連線能力，就會發生此錯誤。 如果伺服器已無法再使用，請在入口網站中取消註冊伺服器，從而刪除伺服器端點。 若要刪除伺服器端點，請依照[向 Azure 檔案同步取消註冊伺服器](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service)中所述的步驟進行。

<a id="server-endpoint-provisioningfailed"></a>**無法開啟伺服器端點屬性頁面，或更新雲端階層處理原則**  
如果伺服器端點上的管理作業失敗，就會發生此問題。 如果伺服器端點屬性頁面未在 Azure 入口網站中開啟，則從伺服器使用 PowerShell 命令來更新伺服器端點可修正此問題。 

```powershell
# Get the server endpoint id based on the server endpoint DisplayName property
Get-AzStorageSyncServerEndpoint `
    -ResourceGroupName myrgname `
    -StorageSyncServiceName storagesvcname `
    -SyncGroupName mysyncgroup | `
Tee-Object -Variable serverEndpoint

# Update the free space percent policy for the server endpoint
Set-AzStorageSyncServerEndpoint `
    -InputObject $serverEndpoint
    -CloudTiering `
    -VolumeFreeSpacePercent 60
```
<a id="server-endpoint-noactivity"></a>**伺服器終結點的運行狀況為"無活動"或"掛起"，註冊伺服器邊欄選項卡上的伺服器狀態為"離線顯示"**  

如果存儲同步監視器進程 （AzureStorageSyncMonitor.exe） 未運行或伺服器無法訪問 Azure 檔同步服務，則可能會出現此問題。

在門戶中顯示為"離線"的伺服器上，查看遙測事件日誌中的事件 ID 9301（位於應用程式和服務下\Microsoft_FileSync_事件檢視器中的代理），以確定伺服器無法訪問 Azure 檔同步的原因服務。 

- 如果**GetNextJob 的狀態為 0**完成，則伺服器可以與 Azure 檔同步服務進行通信。 
    - 在伺服器上開啟工作管理員，並確認儲存體同步監視器 (AzureStorageSyncMonitor.exe) 程序正在執行。 如果此程序未執行，先嘗試重新啟動伺服器。 如果重新啟動伺服器無法解決此問題，請升級至最新版 Azure 檔案同步[代理程式版本](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes)。 

- 如果**GetNextJob 的狀態為 -2134347756，** 則由於防火牆或代理，伺服器無法與 Azure 檔同步服務通信。 
    - 如果伺服器位於防火牆後方，請確認允許連接埠 443 輸出。 如果防火牆將流量限制為特定域，請確認可以訪問防火牆[文檔中](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#firewall)列出的域。
    - 如果伺服器位於代理後面，請按照代理[文檔中](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#proxy)的步驟配置電腦範圍或特定于應用的代理設置。
    - 使用測試存儲同步網路連接 Cmdlet 檢查與服務終結點的網路連接。 要瞭解更多資訊，請參閱[測試與服務終結點的網路連接](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#test-network-connectivity-to-service-endpoints)。

- 如果**GetNextJob 的狀態為 -2134347764，** 則伺服器由於證書過期或刪除，無法與 Azure 檔同步服務通信。  
    - 在伺服器上運行以下 PowerShell 命令以重置用於身份驗證的證書：
    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```
<a id="endpoint-noactivity-sync"></a>**伺服器終結點的運行狀況為"無活動"，註冊伺服器邊欄選項卡上的伺服器狀態為"連線"**  

伺服器端點健康狀態為 [無活動] 時，表示伺服器端點在過去兩小時內未記錄任何同步活動。

若要查看伺服器上目前的同步活動，請參閱[如何監視目前同步工作階段的進度？](#how-do-i-monitor-the-progress-of-a-current-sync-session)。

由於錯誤或系統資源不足，伺服器終結點可能無法在幾個小時中記錄同步活動。 驗證安裝了最新的 Azure 檔同步[代理版本](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes)。 如果問題仍然存在，請打開支援請求。

> [!Note]  
> 如果 [已註冊的伺服器] 刀鋒視窗上的伺服器狀態為 [顯示為離線]，請執行[伺服器端點的健康狀態為 [無活動] 或 [擱置中]，且 [已註冊的伺服器] 刀鋒視窗上的伺服器狀態為 [顯示為離線]](#server-endpoint-noactivity) 一節所列的步驟。

## <a name="sync"></a>Sync
<a id="afs-change-detection"></a>**如果我通過 SMB 或通過門戶直接在 Azure 檔共用中創建了檔，則檔要同步到同步組中的伺服器需要多長時間？**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a>**伺服器終結點運行狀況處於掛起狀態數小時**  
如果您建立雲端端點並使用包含資料的 Azure 檔案共用，便會發生此問題。 在 Azure 檔案共用中掃描變更的變更列舉作業必須先完成，才能在雲端與伺服器端點之間同步檔案。 完成此作業的所需時間，取決於 Azure 檔案共用中的命名空間大小。 在變更列舉作業完成後，伺服器端點健康情況應會隨即更新。

### <a name="how-do-i-monitor-sync-health"></a><a id="broken-sync"></a>如何監視同步健康情況？
# <a name="portal"></a>[入口網站](#tab/portal1)
在每個同步群組內，您可以向下切入至其個別的伺服器端點，以查看上次完成的同步工作階段所處的狀態。 若 [健康情況] 資料行為綠色，且 [檔案無法同步] 的值為 0，表示同步運作正常。 若非如此，請查看下方的常見同步錯誤清單，並了解如何處理未同步的檔案。 

![Azure 入口網站的螢幕擷取畫面](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="server"></a>[伺服器](#tab/server)
移至伺服器的遙測記錄 (位於事件檢視器的 `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry` 上)。 事件 9102 對應於已完成的同步工作階段；如需最新同步狀態，請尋找識別碼為 9102 的最新事件。 SyncDirection 會指出此工作階段是否已上傳或下載。 HResult 為 0，表示同步工作階段成功。 非零的 HResult 表示在同步期間發生錯誤；請查看下方的常見錯誤清單。 如果 PerItemErrorCount 大於 0，表示某些檔案或資料夾未正確同步。 HResult 為 0，但 PerItemErrorCount 大於 0，是有可能的。

以下是成功上傳的範例。 為了方便說明，以下僅列出每個 9102 事件所包含的部分值。 

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: 0, 
SyncFileCount: 2, SyncDirectoryCount: 0,
AppliedFileCount: 2, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0,
TransferredFiles: 2, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

相反地，不成功的上傳可能如下所示：

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: -2134364065,
SyncFileCount: 0, SyncDirectoryCount: 0, 
AppliedFileCount: 0, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0, 
TransferredFiles: 0, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

有時候，同步工作階段會完全失敗，或是有非零的 PerItemErrorCount 但仍繼續進行，而有部分檔案成功同步。 這可以從 [已套用]* 欄位中看出 (AppliedFileCount、AppliedDirCount、AppliedTombstoneCount 和 AppliedSizeBytes)，在此處您可以了解工作階段成功執行的程度。 如果您發現資料列中有多個同步工作階段雖然失敗，但 [已套用]* 計數卻逐漸增加，您即應在建立支援票證之前，保留足夠的時間來重新嘗試同步。

---

### <a name="how-do-i-monitor-the-progress-of-a-current-sync-session"></a>如何監視目前同步工作階段的進度？
# <a name="portal"></a>[入口網站](#tab/portal1)
在您的同步群組中，移至有問題的伺服器端點，並查看 [同步活動] 區段以確認目前的同步工作階段中已上傳或下載的檔案計數。 請注意，此狀態大約會延遲 5 分鐘才顯示，因此如果同步工作階段較小而可在這段期間內完成，就可能不會報告在入口網站中。 

# <a name="server"></a>[伺服器](#tab/server)
在伺服器的遙測記錄中查看最新的 9302 事件 (在 [事件檢視器] 中，移至 Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry)。 此事件表示目前同步工作階段的狀態。 TotalItemCount 代表要同步的檔案數目、AppliedItemCount 是目前已同步的檔案數目，而 PerItemErrorCount 則是無法同步的檔案數目 (請參閱下方的處置方式)。

```
Replica Sync Progress. 
ServerEndpointName: <CI>sename</CI>, SyncGroupName: <CI>sgname</CI>, ReplicaName: <CI>rname</CI>, 
SyncDirection: Upload, CorrelationId: {AB4BA07D-5B5C-461D-AAE6-4ED724762B65}. 
AppliedItemCount: 172473, TotalItemCount: 624196. AppliedBytes: 51473711577, 
TotalBytes: 293363829906. 
AreTotalCountsFinal: true. 
PerItemErrorCount: 1006.
```
---

### <a name="how-do-i-know-if-my-servers-are-in-sync-with-each-other"></a>如何得知我的伺服器是否彼此保持同步？
# <a name="portal"></a>[入口網站](#tab/portal1)
針對給定同步群組中的每個伺服器，請確定：
- 上傳和下載的「上次嘗試的同步」時間戳記都是最新的。
- 上傳和下載的狀態都是綠色的。
- [同步活動] 欄位中僅顯示少許或沒有待同步的檔案。
- 上傳和下載的 [檔案無法同步] 欄位皆為 0。

# <a name="server"></a>[伺服器](#tab/server)
查看已完成的同步工作階段，這在每個伺服器的遙測事件記錄中會以 9102 事件標示 (在 [事件檢視器] 中，移至 `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`)。 

1. 在任何給定的伺服器上，您都會想要確定最新的上傳和下載工作階段已順利完成。 若要這樣做，請確認上傳和下載的 HResult 和 PerItemErrorCount 皆為 0 (SyncDirection 欄位會指出給定的工作階段是上傳還是下載工作階段)。 請注意，若未看到最近完成的同步工作階段，有可能是同步工作階段正在進行中，這是您剛剛新增或修改大量資料之後的正常情況。
2. 如果伺服器與雲端之間的狀態是最新的，且雙向都沒有需要同步的變更，您將會看到空的同步工作階段。 這些通過上載和下載事件指示，其中所有同步*欄位（同步檔計數、同步DirCount、SyncTombstoneCount 和 SyncSizeBytes）為零，這意味著沒有要同步的。請注意，這些空同步會話可能不會發生在高流失的伺服器上，因為總是有新內容需要同步。如果沒有同步活動，則應每 30 分鐘執行一次。 
3. 如果所有伺服器與雲端之間的狀態都是最新的，這表示其最近的上傳和下載工作階段都是空的同步工作階段，而您可以合理斷定整體系統皆已同步。 
    
請注意，如果您直接在 Azure 檔案共用中進行變更，在每 24 小時發生一次的變更列舉執行之前，Azure 檔案同步將不會偵測到此變更。 當伺服器實際上遺漏直接在 Azure 檔案共用中所做的最新變更時，它仍有可能認定本身與雲端之間的狀態是最新的。 

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>如何確認是否有特定的檔案或資料夾並未同步？
如果伺服器上的 PerItemErrorCount 或閘戶中的"不同步檔計數"對於任何給定的同步會話都大於 0，則意味著某些專案無法同步。檔和資料夾可以具有阻止它們同步的特徵。 特性可能是持續性的，且您必須執行明確的動作才能繼續同步，例如，從檔案或資料夾名稱中移除不支援的字元。 特性也有可能是暫時性的，這表示檔案或資料夾將會自動繼續同步；例如，具有開啟控制代碼的檔案，將會檔案關閉時自動繼續同步。 當 Azure 檔案同步引擎偵測到此類問題時，將會產生錯誤記錄，而此記錄可經由剖析列出目前未正確同步的項目。

若要查看這些錯誤，請執行 **FileSyncErrorsReport.ps1** PowerShell 指令碼 (位於 Azure 檔案同步代理程式的代理程式安裝目錄中)，以識別因開啟的控制代碼、不支援的字元或其他問題而無法同步的檔案。 ItemPath 欄位會指出檔案與根同步目錄的相對位置。 請參閱下方的常見同步錯誤清單，以取得補救步驟。

> [!Note]  
> 如果 FileSyncErrorsReport.ps1 腳本返回"未找到檔錯誤"或未列出同步組的每個專案錯誤，則原因要麼是：
>
>- 原因 1：上次完成的同步會話沒有每個專案錯誤。 門戶應儘快更新，以顯示 0 檔未同步。 
>   - 檢查遙測[事件日誌中的事件 ID 9102](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync)以確認 PerItemErrorCount 為 0。 
>
>- 原因 2：由於每個專案錯誤過多，伺服器上的 Item結果事件日誌已包裝，並且事件日誌不再包含此同步組的錯誤。
>   - 要防止此問題，請增加 Item結果事件日誌大小。 專案結果事件日誌可以在事件檢視器中的"應用程式和服務日誌_Microsoft_FileSync_代理"下找到。 

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>個別檔案/目錄同步錯誤的疑難排解
**ItemResults 記錄 - 個別項目同步錯誤**  

| HRESULT | HRESULT (十進位) | 錯誤字串 | 問題 | 補救 |
|---------|-------------------|--------------|-------|-------------|
| 0x80070043 | -2147942467 | ERROR_BAD_NET_NAME | 無法訪問伺服器上的分層檔。 如果未在刪除伺服器端點之前重新叫用分層檔案，就會發生此問題。 | 要解決此問題，請參閱[刪除伺服器終結點後，伺服器無法訪問分層檔](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)。 |
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | 檔或目錄更改無法同步，因為從屬資料夾尚未同步。 將在同步相依的變更之後同步此項目。 | 不需要任何動作。 如果錯誤持續數天，請使用 FileSyncErrorsReport.ps1 PowerShell 腳本來確定相關資料夾尚未同步的原因。 |
| 0 x80c80284 | -2134375804 | ECS_E_SYNC_CONSTRAINT_CONFLICT_SESSION_FAILED | 檔或目錄更改尚無法同步，因為從屬資料夾尚未同步，同步會話失敗。 將在同步相依的變更之後同步此項目。 | 不需要任何動作。 如果錯誤仍然存在，請調查同步會話失敗。 |
| 0x8007007b | -2147024773 | ERROR_INVALID_NAME | 檔案或目錄名稱無效。 | 請重新命名有問題的檔案或目錄。 如需詳細資訊，請參閱[處理不支援的字元](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters)。 |
| 0 x80c80255 | -2134375851 | ECS_E_XSMB_REST_INCOMPATIBILITY | 檔案或目錄名稱無效。 | 請重新命名有問題的檔案或目錄。 如需詳細資訊，請參閱[處理不支援的字元](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters)。 |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | 無法同步該檔，因為它正在使用中。 檔案不再處於使用中狀態時即會同步。 | 不需要任何動作。 Azure 檔案同步每天會在伺服器上建立一個暫時的 VSS 快照集，用以同步具有開啟控制代碼的檔案。 |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | 檔已更改，但同步尚未檢測到更改。檢測到此更改後，同步將恢復。 | 不需要任何動作。 |
| 0x80070002 | -2147024894 | ERROR_FILE_NOT_FOUND | 檔已被刪除，同步不知道更改。 | 不需要任何動作。 一旦更改檢測檢測到檔已被刪除，同步將停止記錄此錯誤。 |
| 0x80070003 | -2147942403 | ERROR_PATH_NOT_FOUND | 無法同步刪除檔或目錄，因為該專案已在目標中刪除，並且同步不知道更改。 | 不需要任何動作。 一旦在目標上運行更改檢測，同步將停止記錄此錯誤，同步將檢測到該專案已被刪除。 |
| 0 x80c80205 | -2134375931 | ECS_E_SYNC_ITEM_SKIP | 檔或目錄已跳過，但將在下一個同步會話期間同步。 如果在下載專案時報告此錯誤，則檔或目錄名稱很可能無效。 | 如果上載檔時報告此錯誤，則無需執行任何操作。 如果在下載檔案時報告錯誤，請重命名有問題的檔或目錄。 如需詳細資訊，請參閱[處理不支援的字元](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters)。 |
| 0x800700B7 | -2147024713 | ERROR_ALREADY_EXISTS | 無法同步檔的創建，因為目標中已存在該專案，並且同步不知道更改。 | 不需要任何動作。 一旦在目標上運行更改檢測，並且同步知道此新專案，同步將停止記錄此錯誤。 |
| 0x80c8603e | -2134351810 | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED | 無法同步檔案，因為已達 Azure 檔案共用限制。 | 若要解決此問題，請參閱疑難排解指南中的[您已達到 Azure 檔案共用儲存體限制](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#-2134351810)一節。 |
| 0x80c8027C | -2134375812 | ECS_E_ACCESS_DENIED_EFS | 該檔由不支援的解決方案（如 NTFS EFS）加密。 | 解密檔並使用受支援的加密解決方案。 如需支援解決方案的清單，請參閱計劃指南中的[加密解決方案](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#encryption)一節。 |
| 0 x80c80283 | -2160591491 | ECS_E_ACCESS_DENIED_DFSRRO | 該檔位於 DFS-R 唯讀複製資料夾中。 | 檔位於 DFS-R 唯讀複製資料夾中。 Azure 檔案同步不支援 DFS-R 唯讀複寫資料夾上的伺服器端點。 有關詳細資訊，請參閱[規劃指南](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#distributed-file-system-dfs)。 |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | 該檔具有刪除掛起狀態。 | 不需要任何動作。 關閉所有打開的檔案控制代碼後，將刪除檔。 |
| 0 x80c86044 | -2134351804 | ECS_E_AZURE_AUTHORIZATION_FAILED | 無法同步該檔，因為存儲帳戶上的防火牆和虛擬網路設置已啟用，並且伺服器無法訪問存儲帳戶。 | 按照部署指南中的["配置防火牆"和"虛擬網路設置](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)"部分中記錄的步驟添加伺服器 IP 位址或虛擬網路。 |
| 0 x80c80243 | -2134375869 | ECS_E_SECURITY_DESCRIPTOR_SIZE_TOO_LARGE | 無法同步檔，因為安全描述項大小超過 64 KiB 限制。 | 若要解決此問題，請移除檔案上的存取控制項目 (ACE)，以縮小安全描述項大小。 |
| 0x8000ffff | -2147418113 | E_UNEXPECTED | 由於意外錯誤，無法同步該檔。 | 如果錯誤持續數天，請打開一個支援案例。 |
| 0x80070020 | -2147024864 | ERROR_SHARING_VIOLATION | 無法同步該檔，因為它正在使用中。 檔案不再處於使用中狀態時即會同步。 | 不需要任何動作。 |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | 檔在同步期間已更改，因此需要再次同步。 | 不需要任何動作。 |
| 0x80070017 | -2147024873 | ERROR_CRC | 由於 CRC 錯誤，無法同步該檔。 如果在刪除伺服器終結點之前未調用分層檔或檔已損壞，則可能發生此錯誤。 | 要解決此問題，請參閱刪除伺服器終結點以刪除孤立分層檔[後，伺服器無法訪問分層檔](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)。 如果在刪除 oprhan 分層檔後繼續發生錯誤，請運行卷上的[chkdsk。](https://docs.microsoft.com/windows-server/administration/windows-commands/chkdsk) |
| 0 x80c80200 | -2134375936 | ECS_E_SYNC_CONFLICT_NAME_EXISTS | 無法同步該檔，因為已達到衝突檔的最大數量。 Azure 檔同步支援每個檔 100 個衝突檔。 要瞭解有關檔衝突的更多內容，請參閱 Azure 檔同步[常見問題解答](https://docs.microsoft.com/azure/storage/files/storage-files-faq#afs-conflict-resolution)。 | 要解決此問題，請減少衝突檔的數量。 衝突檔數小於 100 後，檔將同步。 |

#### <a name="handling-unsupported-characters"></a>處理不支援的字元
如果**FileSyncErrorsReport.ps1** PowerShell 腳本顯示由於不支援的字元（錯誤代碼 0x8007007b 或 0x80c80255）而導致的故障，則應從相應的檔案名中刪除或重命名出現故障的字元。 由於這些字元大多沒有標準的視覺編碼，PowerShell 可能會將這些字元列印為問號或空的矩形。 [評估工具](storage-sync-files-planning.md#evaluation-cmdlet)可用來識別不受支援的字元。

下表列出 Azure 檔案同步尚不支援的所有 Unicode 字元。

| 字元集 | 字元計數 |
|---------------|-----------------|
| <ul><li>0x0000009D (osc 作業系統命令)</li><li>0x00000090 (dcs 裝置控制字串)</li><li>0x0000008F (ss3 單一移位三)</li><li>0x00000081 (高八位元組前置)</li><li>0x0000007F (del 刪除)</li><li>0x0000008D (ri 反向換行)</li></ul> | 6 |
| 0x0000FDD0 - 0x0000FDEF (阿拉伯文表現形式-a) | 32 |
| 0x0000FFF0 - 0x0000FFFF (特殊符號) | 16 |
| <ul><li>0x0001FFFE - 0x0001FFFF = 2 (非字元)</li><li>0x0002FFFE - 0x0002FFFF = 2 (非字元)</li><li>0x0003FFFE - 0x0003FFFF = 2 (非字元)</li><li>0x0004FFFE - 0x0004FFFF = 2 (非字元)</li><li>0x0005FFFE - 0x0005FFFF = 2 (非字元)</li><li>0x0006FFFE - 0x0006FFFF = 2 (非字元)</li><li>0x0007FFFE - 0x0007FFFF = 2 (非字元)</li><li>0x0008FFFE - 0x0008FFFF = 2 (非字元)</li><li>0x0009FFFE - 0x0009FFFF = 2 (非字元)</li><li>0x000AFFFE - 0x000AFFFF = 2 (非字元)</li><li>0x000BFFFE - 0x000BFFFF = 2 (非字元)</li><li>0x000CFFFE - 0x000CFFFF = 2 (非字元)</li><li>0x000DFFFE - 0x000DFFFF = 2 (非字元)</li><li>0x000EFFFE - 0x000EFFFF = 2 (未定義)</li><li>0x000FFFFE - 0x000FFFFF = 2 (補充專用區)</li></ul> | 30 |
| 0x0010FFFE、0x0010FFFF | 2 |

### <a name="common-sync-errors"></a>常見同步錯誤
<a id="-2147023673"></a>**同步會話已取消。**  

| | |
|-|-|
| **HRESULT** | 0x800704c7 |
| **HRESULT (十進位)** | -2147023673 | 
| **錯誤字串** | ERROR_CANCELLED |
| **需要補救** | 否 |

同步會話可能會由於各種原因失敗，包括重新開機或更新的伺服器、VSS 快照等。儘管此錯誤看起來需要跟進，但可以安全地忽略此錯誤，除非該錯誤持續幾個小時。

<a id="-2147012889"></a>**無法與服務建立連接。**    

| | |
|-|-|
| **HRESULT** | 0x80072ee7 |
| **HRESULT (十進位)** | -2147012889 | 
| **錯誤字串** | WININET_E_NAME_NOT_RESOLVED |
| **需要補救** | 是 |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134376372"></a>**使用者要求已由服務進行節流。**  

| | |
|-|-|
| **HRESULT** | 0x80c8004c |
| **HRESULT (十進位)** | -2134376372 |
| **錯誤字串** | ECS_E_USER_REQUEST_THROTTLED |
| **需要補救** | 否 |

不需要任何動作；伺服器會再試一次。 如果此錯誤持續存在數小時，請建立支援要求。

<a id="-2134364043"></a>**同步被阻止，直到更改檢測完成還原後**  

| | |
|-|-|
| **HRESULT** | 0 x80c83075 |
| **HRESULT (十進位)** | -2134364043 |
| **錯誤字串** | ECS_E_SYNC_BLOCKED_ON_CHANGE_DETECTION_POST_RESTORE |
| **需要補救** | 否 |

您不需要執行任何動作。 使用 Azure 備份還原檔或檔共用（雲終結點）時，同步將被阻止，直到 Azure 檔共用上完成更改檢測。 變更偵測會在還原完成時立即執行，而執行時間會取決於檔案共用中的檔案數目。

<a id="-2147216747"></a>**同步失敗，因為同步資料庫已卸載。**  

| | |
|-|-|
| **HRESULT** | 0 x80041295 |
| **HRESULT (十進位)** | -2147216747 |
| **錯誤字串** | SYNC_E_METADATA_INVALID_OPERATION |
| **需要補救** | 否 |

通常，當備份應用程式建立 VSS 快照集，並且已卸載同步資料庫時，就會發生此錯誤。 如果此錯誤持續存在數小時，請建立支援要求。

<a id="-2134364065"></a>**同步作業無法存取雲端端點中指定的 Azure 檔案共用。**  

| | |
|-|-|
| **HRESULT** | 0x80c8305f |
| **HRESULT (十進位)** | -2134364065 |
| **錯誤字串** | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED |
| **需要補救** | 是 |

之所以發生此錯誤，是因為 Azure 檔案同步代理程式無法存取 Azure 檔案共用，而這可能是因為 Azure 檔案共用或加以裝載的儲存體帳戶已不存在。 您可以透過下列步驟，對此錯誤進行疑難排解：

1. [確認儲存體帳戶確實存在。](#troubleshoot-storage-account)
2. [確保 Azure 檔共用存在。](#troubleshoot-azure-file-share)
3. [確定 Azure 檔案同步具有儲存體帳戶的存取權。](#troubleshoot-rbac)
4. [請確認是否已在儲存體帳戶上正確設定防火牆和虛擬網路設定 (若已啟用的話)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134351804"></a>**同步失敗，因為請求無權執行此操作。**  

| | |
|-|-|
| **HRESULT** | 0 x80c86044 |
| **HRESULT (十進位)** | -2134351804 |
| **錯誤字串** | ECS_E_AZURE_AUTHORIZATION_FAILED |
| **需要補救** | 是 |

出現此錯誤的原因是 Azure 檔同步代理無權訪問 Azure 檔共用。 您可以透過下列步驟，對此錯誤進行疑難排解：

1. [確認儲存體帳戶確實存在。](#troubleshoot-storage-account)
2. [確保 Azure 檔共用存在。](#troubleshoot-azure-file-share)
3. [請確認是否已在儲存體帳戶上正確設定防火牆和虛擬網路設定 (若已啟用的話)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)
4. [確定 Azure 檔案同步具有儲存體帳戶的存取權。](#troubleshoot-rbac)

<a id="-2134364064"></a><a id="cannot-resolve-storage"></a>**無法解析使用的儲存體帳戶名稱。**  

| | |
|-|-|
| **HRESULT** | 0x80C83060 |
| **HRESULT (十進位)** | -2134364064 |
| **錯誤字串** | ECS_E_STORAGE_ACCOUNT_NAME_UNRESOLVED |
| **需要補救** | 是 |

1. 確認您可以解析來自伺服器的儲存體 DNS 名稱。

    ```powershell
    Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 443
    ```
2. [確認儲存體帳戶確實存在。](#troubleshoot-storage-account)
3. [請確認是否已在儲存體帳戶上正確設定防火牆和虛擬網路設定 (若已啟用的話)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364022"></a><a id="storage-unknown-error"></a>**訪問存儲帳戶時發生未知錯誤。**  

| | |
|-|-|
| **HRESULT** | 0x80c8308a |
| **HRESULT (十進位)** | -2134364022 |
| **錯誤字串** | ECS_E_STORAGE_ACCOUNT_UNKNOWN_ERROR |
| **需要補救** | 是 |

1. [確認儲存體帳戶確實存在。](#troubleshoot-storage-account)
2. [請確認是否已在儲存體帳戶上正確設定防火牆和虛擬網路設定 (若已啟用的話)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364014"></a>**由於存儲帳戶鎖定，同步失敗。**  

| | |
|-|-|
| **HRESULT** | 0x80c83092 |
| **HRESULT (十進位)** | -2134364014 |
| **錯誤字串** | ECS_E_STORAGE_ACCOUNT_LOCKED |
| **需要補救** | 是 |

出現此錯誤是因為存儲帳戶具有唯讀[資源鎖](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources)。 要解決此問題，請刪除存儲帳戶上的唯讀資源鎖。 

<a id="-1906441138"></a>**同步因同步資料庫發生問題而失敗。**  

| | |
|-|-|
| **HRESULT** | 0x8e5e044e |
| **HRESULT (十進位)** | -1906441138 |
| **錯誤字串** | JET_errWriteConflict |
| **需要補救** | 是 |

當 Azure 檔同步使用的內部資料庫出現問題時，將發生此錯誤。發生此問題時，請創建支援請求，我們將與您聯繫以説明您解決此問題。

<a id="-2134364053"></a>**不支援安裝在伺服器上的 Azure 檔案同步代理程式版本。**  

| | |
|-|-|
| **HRESULT** | 0x80C8306B |
| **HRESULT (十進位)** | -2134364053 |
| **錯誤字串** | ECS_E_AGENT_VERSION_BLOCKED |
| **需要補救** | 是 |

如果不支援安裝在伺服器上的 Azure 檔案同步代理程式版本，就會發生此錯誤。 要解決此問題，[應升級到]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#upgrade-paths)[受支援的代理版本]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions)。

<a id="-2134351810"></a>**已達到 Azure 檔案共用儲存空間限制。**  

| | |
|-|-|
| **HRESULT** | 0x80c8603e |
| **HRESULT (十進位)** | -2134351810 |
| **錯誤字串** | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED |
| **需要補救** | 是 |

在達到 Azure 檔案共用儲存空間限制時 (可能在 Azure 檔案共用套用了配額，或 Azure 檔案共用的使用量超出限制時發生)，就會發生此錯誤。 如需詳細資訊，請參閱 [Azure 檔案共用目前的限制](storage-files-scale-targets.md)。

1. 瀏覽至儲存體同步服務中的同步群組。
2. 選取同步群組內的雲端端點。
3. 記下開啟的窗格中顯示的 Azure 檔案共用名稱。
4. 選取連結的儲存體帳戶。 如果此連結失敗，表示參考的儲存體帳戶已移除。

    ![此螢幕擷取畫面顯示有連結通往儲存體帳戶的雲端端點詳細資料窗格。](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

5. 選擇 **"檔**"以查看檔共用清單。
6. 按一下資料列結尾處的三個點，以顯示雲端端點所參考的 Azure 檔案共用。
7. 確認 [使用量]**** 低於 [配額]****。 注意，除非指定了備用配額，否則配額將與 Azure[檔共用的最大大小](storage-files-scale-targets.md)匹配。

    ![Azure 檔案共用屬性的螢幕擷取畫面。](media/storage-sync-files-troubleshoot/file-share-limit-reached-1.png)

如果共用已滿，且未設定配額，修正此問題的可行方式之一，是使目前伺服器端點的每個子資料夾，成為其各自所屬同步群組內的專屬伺服器端點。 如此一來，每個子資料夾將會同步至個別的 Azure 檔案共用。

<a id="-2134351824"></a>**找不到 Azure 檔案共用。**  

| | |
|-|-|
| **HRESULT** | 0x80c86030 |
| **HRESULT (十進位)** | -2134351824 |
| **錯誤字串** | ECS_E_AZURE_FILE_SHARE_NOT_FOUND |
| **需要補救** | 是 |

無法存取 Azure 檔案共用時，就會發生此錯誤。 若要進行疑難排解：

1. [確認儲存體帳戶確實存在。](#troubleshoot-storage-account)
2. [確保 Azure 檔共用存在。](#troubleshoot-azure-file-share)

如果 Azure 檔案共用已刪除，您必須建立新的檔案共用，並重新建立同步群組。 

<a id="-2134364042"></a>**此 Azure 訂用帳戶暫止期間，會暫停同步。**  

| | |
|-|-|
| **HRESULT** | 0x80C83076 |
| **HRESULT (十進位)** | -2134364042 |
| **錯誤字串** | ECS_E_SYNC_BLOCKED_ON_SUSPENDED_SUBSCRIPTION |
| **需要補救** | 是 |

當 Azure 訂用帳戶暫止時，就會發生此錯誤。 同步將在 Azure 訂用帳戶恢復時重新啟用。 如需詳細資訊，請參閱[我的 Azure 訂用帳戶為何停用，以及如何重新啟動它？](../../cost-management-billing/manage/subscription-disabled.md)。

<a id="-2134364052"></a>**儲存體帳戶已設定了防火牆或虛擬網路。**  

| | |
|-|-|
| **HRESULT** | 0x80c8306c |
| **HRESULT (十進位)** | -2134364052 |
| **錯誤字串** | ECS_E_MGMT_STORAGEACLSNOTSUPPORTED |
| **需要補救** | 是 |

因為儲存體帳戶有防火牆，或因為儲存體帳戶屬於虛擬網路，而無法存取 Azure 檔案共用時，就會發生此錯誤。 驗證存儲帳戶上的防火牆和虛擬網路設置配置正確。 有關詳細資訊，請參閱[配置防火牆和虛擬網路設置](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)。 

<a id="-2134375911"></a>**同步因同步資料庫發生問題而失敗。**  

| | |
|-|-|
| **HRESULT** | 0x80c80219 |
| **HRESULT (十進位)** | -2134375911 |
| **錯誤字串** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **需要補救** | 否 |

此錯誤通常會自行解決；若有下列情況，即可能發生：

* 在同步群組的多個伺服器之間發生大量檔案變更。
* 在個別檔案和目錄上發生大量錯誤。

如果此錯誤持續存在超過數小時，請建立支援要求，我們會與您連絡，協助您解決這個問題。

<a id="-2146762487"></a>**伺服器無法建立安全連線。雲服務收到意外的證書。**  

| | |
|-|-|
| **HRESULT** | 0x800b0109 |
| **HRESULT (十進位)** | -2146762487 |
| **錯誤字串** | CERT_E_UNTRUSTEDROOT |
| **需要補救** | 是 |

如果您的組織目前使用 SSL 終止 Proxy 或惡意實體正在攔截您的伺服器與 Azure 檔案同步服務之間的流量，即可能發生此錯誤。 若您確定這是預期中的情況 (因為您的組織使用 SSL 終止 Proxy)，您可以透過登錄覆寫略過憑證驗證。

1. 建立 SkipVerifyingPinnedRootCertificate 登錄值。

    ```powershell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. 在已註冊的伺服器上重新啟動同步服務。

    ```powershell
    Restart-Service -Name FileSyncSvc -Force
    ```

設定此登錄值之後，在伺服器與雲端服務之間傳送資料時，Azure 檔案同步代理程式即會接受所有本機信任的 SSL 憑證。

<a id="-2147012894"></a>**無法與服務建立連接。**  

| | |
|-|-|
| **HRESULT** | 0x80072ee2 |
| **HRESULT (十進位)** | -2147012894 |
| **錯誤字串** | WININET_E_TIMEOUT |
| **需要補救** | 是 |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134375680"></a>**同步因驗證有問題而失敗。**  

| | |
|-|-|
| **HRESULT** | 0x80c80300 |
| **HRESULT (十進位)** | -2134375680 |
| **錯誤字串** | ECS_E_SERVER_CREDENTIAL_NEEDED |
| **需要補救** | 是 |

此錯誤通常是因為伺服器時間不正確所造成。 如果伺服器在虛擬機器中運行，請驗證主機上的時間是否正確。

<a id="-2134364040"></a>**由於證書過期，同步失敗。**  

| | |
|-|-|
| **HRESULT** | 0 x80c83078 |
| **HRESULT (十進位)** | -2134364040 |
| **錯誤字串** | ECS_E_AUTH_SRV_CERT_EXPIRED |
| **需要補救** | 是 |

發生此錯誤的原因是用於驗證的憑證已過期。

若要確認憑證是否已過期，請執行下列步驟：  
1. 開啟 [憑證 MMC 嵌入式管理單元]，選取 [電腦帳戶]，並瀏覽至 [憑證 (本機電腦)\個人\憑證]。
2. 檢查用戶端驗證憑證是否已過期。

如果用戶端驗證憑證已過期，請執行下列步驟來解決此問題：

1. 確認已安裝 Azure 檔案同步代理程式版本 4.0.1.0 或更新版本。
2. 在伺服器上執行下列 PowerShell 命令：

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134375896"></a>**由於找不到身份驗證憑證，同步失敗。**  

| | |
|-|-|
| **HRESULT** | 0 x80c80228 |
| **HRESULT (十進位)** | -2134375896 |
| **錯誤字串** | ECS_E_AUTH_SRV_CERT_NOT_FOUND |
| **需要補救** | 是 |

發生此錯誤的原因是找不到用於驗證的憑證。

若要解決此問題，請執行下列步驟：

1. 確認已安裝 Azure 檔案同步代理程式版本 4.0.1.0 或更新版本。
2. 在伺服器上執行下列 PowerShell 命令：

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134364039"></a>**由於找不到身份驗證標識，同步失敗。**  

| | |
|-|-|
| **HRESULT** | 0 x80c83079 |
| **HRESULT (十進位)** | -2134364039 |
| **錯誤字串** | ECS_E_AUTH_IDENTITY_NOT_FOUND |
| **需要補救** | 是 |

之所以發生此錯誤，是因為伺服器端點刪除失敗，而且端點現在處於部分刪除的狀態。 若要解決此問題，請再次嘗試刪除伺服器端點。

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**伺服器端點所在的磁碟區沒有足夠的磁碟空間。**  

| | |
|-|-|
| **HRESULT** | 0x8e5e0211 |
| **HRESULT (十進位)** | -1906441711 |
| **錯誤字串** | JET_errLogDiskFull |
| **需要補救** | 是 |
| | |
| **HRESULT** | 0x80c8031a |
| **HRESULT (十進位)** | -2134375654 |
| **錯誤字串** | ECS_E_NOT_ENOUGH_LOCAL_STORAGE |
| **需要補救** | 是 |

發生此錯誤的原因是磁碟區已滿。 之所以發生此錯誤，常是因為伺服器端點以外的檔案即將用盡磁碟區的空間。 請新增額外的伺服器端點、將檔案移至不同的磁碟區，或增加伺服器端點所在磁碟區的大小，以釋出磁碟區的空間。

<a id="-2134364145"></a><a id="replica-not-ready"></a>**服務尚未準備好要與此伺服器端點同步。**  

| | |
|-|-|
| **HRESULT** | 0x80c8300f |
| **HRESULT (十進位)** | -2134364145 |
| **錯誤字串** | ECS_E_REPLICA_NOT_READY |
| **需要補救** | 否 |

出現此錯誤的原因是，雲終結點是在 Azure 檔共用上已存在的內容創建的。 Azure 檔同步必須掃描 Azure 檔共用的所有內容，然後才能允許伺服器終結點繼續其初始同步。

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**同步因許多個別檔案有問題而失敗。**  

| | |
|-|-|
| **HRESULT** | 0x80c8023b |
| **HRESULT (十進位)** | -2134375877 |
| **錯誤字串** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **需要補救** | 是 |
| | |
| **HRESULT** | 0x80c8021c |
| **HRESULT (十進位)** | -2134375908 |
| **錯誤字串** | ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED |
| **需要補救** | 是 |
| | |
| **HRESULT** | 0x80c80253 |
| **HRESULT (十進位)** | -2134375853 |
| **錯誤字串** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **需要補救** | 是 |

如果有許多個別檔案同步錯誤，同步工作階段就可能開始失敗。 <!-- To troubleshoot this state, see [Troubleshooting per file/directory sync errors]().-->

> [!NOTE]
> Azure 檔案同步每天會在伺服器上建立一個暫時的 VSS 快照集，用以同步具有開啟控制代碼的檔案。

<a id="-2134376423"></a>**同步因伺服器端點路徑有問題而失敗。**  

| | |
|-|-|
| **HRESULT** | 0x80c80019 |
| **HRESULT (十進位)** | -2134376423 |
| **錯誤字串** | ECS_E_SYNC_INVALID_PATH |
| **需要補救** | 是 |

請確定路徑存在、位於本機 NTFS 磁碟區尚，且不是重新分析點或現有的伺服器端點。

<a id="-2134375817"></a>**同步處理失敗，因為篩選器驅動程式版本與代理程式版本不相容**  

| | |
|-|-|
| **HRESULT** | 0x80C80277 |
| **HRESULT (十進位)** | -2134375817 |
| **錯誤字串** | ECS_E_INCOMPATIBLE_FILTER_VERSION |
| **需要補救** | 是 |

發生此錯誤的原因是，載入的雲端階層處理篩選器驅動程式 (StorageSync.sys) 版本與儲存體同步代理程式 (FileSyncSvc) 服務不相容。 如果 Azure 檔案同步代理程式已升級，請重新啟動伺服器以完成安裝。 如果錯誤持續發生，請解除安裝代理程式、重新啟動伺服器，並重新安裝 Azure 檔案同步代理程式。

<a id="-2134376373"></a>**該服務當前不可用。**  

| | |
|-|-|
| **HRESULT** | 0x80c8004b |
| **HRESULT (十進位)** | -2134376373 |
| **錯誤字串** | ECS_E_SERVICE_UNAVAILABLE |
| **需要補救** | 否 |

之所以發生此錯誤，是因為 Azure 檔案同步服務無法使用。 此錯誤會在 Azure 檔案同步服務恢復運作時自動解決。

<a id="-2146233088"></a>**由於異常，同步失敗。**  

| | |
|-|-|
| **HRESULT** | 0x80131500 |
| **HRESULT (十進位)** | -2146233088 |
| **錯誤字串** | COR_E_EXCEPTION |
| **需要補救** | 否 |

發生此錯誤的原因是例外狀況造成同步失敗。 如果錯誤持續幾個小時，請創建一個支援請求。

<a id="-2134364045"></a>**同步失敗，因為存儲帳戶容錯移轉到其他區域。**  

| | |
|-|-|
| **HRESULT** | 0x80c83073 |
| **HRESULT (十進位)** | -2134364045 |
| **錯誤字串** | ECS_E_STORAGE_ACCOUNT_FAILED_OVER |
| **需要補救** | 是 |

之所以發生此錯誤，是因為儲存體帳戶已容錯移轉至另一個區域。 Azure 檔案同步不支援儲存體帳戶容錯移轉功能。 不應該容錯移轉包含在 Azure 檔案同步中作為雲端端點使用之 Azure 檔案共用的儲存體帳戶。 這麼做將導致同步停止運作，且可能會在新分層的檔案中產生未預期的資料遺失。 若要解決此問題，請將儲存體帳戶移至主要區域。

<a id="-2134375922"></a>**同步因同步資料庫發生暫時性問題而失敗。**  

| | |
|-|-|
| **HRESULT** | 0x80c8020e |
| **HRESULT (十進位)** | -2134375922 |
| **錯誤字串** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **需要補救** | 否 |

之所以發生此錯誤，是因為同步資料庫發生內部錯誤。 此錯誤會在重試同步時自動解決。 如果此錯誤持續存在一段時間，請建立支援要求，我們會與您連絡，協助您解決這個問題。

<a id="-2134364024"></a>**由於 Azure 活動目錄租戶中的更改而導致同步失敗**  

| | |
|-|-|
| **HRESULT** | 0x80c83088 |
| **HRESULT (十進位)** | -2134364024 | 
| **錯誤字串** | ECS_E_INVALID_AAD_TENANT |
| **需要補救** | 是 |

之所以發生此錯誤，是因為 Azure 檔案同步目前不支援將訂用帳戶移至不同的 Azure Active Directory 租用戶。
 
若要解決此問題，請執行下列其中一個選項：

- **選項 1（推薦）：** 將訂閱移回原始 Azure 活動目錄租戶
- **選項 2**：刪除並重新創建當前同步組。 如果已在伺服器端點上啟用雲端階層處理，請刪除同步群組，然後執行＜[雲端階層處理]( https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)＞區段中所述的步驟，以在重新建立同步群組之前移除孤立的階層式檔案。 

<a id="-2134364010"></a>**由於未配置防火牆和虛擬網路異常，同步失敗**  

| | |
|-|-|
| **HRESULT** | 0x80c83096 |
| **HRESULT (十進位)** | -2134364010 | 
| **錯誤字串** | ECS_E_MGMT_STORAGEACLSBYPASSNOTSET |
| **需要補救** | 是 |

如果在存儲帳戶上啟用了防火牆和虛擬網路設置，並且未選中"允許受信任的 Microsoft 服務訪問此存儲帳戶"異常，則會發生此錯誤。 若要解決此問題，請遵循部署指南中＜[設定防火牆和虛擬網路設定](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)＞一節中所述的步驟。

<a id="-2147024891"></a>**同步失敗，因為"系統磁碟區資訊"資料夾的許可權不正確。**  

| | |
|-|-|
| **HRESULT** | 0x80070005 |
| **HRESULT (十進位)** | -2147024891 |
| **錯誤字串** | ERROR_ACCESS_DENIED |
| **需要補救** | 是 |

如果 NT AUTHORITY\SYSTEM 帳戶無權存取伺服器端點所在磁碟區上的 [系統磁碟區資訊] 資料夾，就會發生此錯誤。 請注意，如果單個檔無法與ERROR_ACCESS_DENIED同步，請執行["每個檔/目錄同步錯誤疑難排解](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#troubleshooting-per-filedirectory-sync-errors)"部分中記錄的步驟。

若要解決此問題，請執行下列步驟：

1. 下載[Psexec](https://docs.microsoft.com/sysinternals/downloads/psexec)工具。
2. 從提升的命令提示符運行以下命令，以便使用系統帳戶啟動命令提示 **：PsExec.exe-i-s-d cmd** 
3. 在系統帳戶所執行的命令提示字元中，執行下列命令以確認 NT AUTHORITY\SYSTEM 帳戶沒有 [系統磁碟區資訊] 資料夾的存取權：**cacls "drive letter:\system volume information" /T /C**
4. 如果 NT AUTHORITY\SYSTEM 帳戶沒有 [系統磁碟區資訊] 資料夾的存取權，請執行下列命令：**cacls  "drive letter:\system volume information" /T /E /G "NT AUTHORITY\SYSTEM:F"**
    - 如果步驟 #4 因存取遭拒而失敗，請執行下列命令來取得 [系統磁碟區資訊] 資料夾的擁有權，然後重複步驟 #4：**takeown /A /R /F "drive letter:\System Volume Information"**

<a id="-2134375810"></a>**同步失敗，因為 Azure 檔共用已被刪除並重新創建。**  

| | |
|-|-|
| **HRESULT** | 0x80c8027e |
| **HRESULT (十進位)** | -2134375810 |
| **錯誤字串** | ECS_E_SYNC_REPLICA_ROOT_CHANGED |
| **需要補救** | 是 |

之所以發生此錯誤，是因為 Azure 檔案同步不支援在相同的同步群組中刪除和重新建立 Azure 檔案共用。 

若要解決此問題，請執行下列步驟來刪除同步群組，然後重新建立：

1. 刪除同步組中的所有伺服器終結點。
2. 刪除雲終結點。 
3. 刪除同步組。
4. 如果在伺服器終結點上啟用了雲分層，則刪除伺服器上的孤立分層檔，在[刪除伺服器終結點部分後](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)，通過執行分層檔中記錄的步驟在伺服器上無法訪問。
5. 重新創建同步組。

<a id="-2145844941"></a>**同步失敗，因為 HTTP 要求被重定向**  

| | |
|-|-|
| **HRESULT** | 0x80190133 |
| **HRESULT (十進位)** | -2145844941 |
| **錯誤字串** | HTTP_E_STATUS_REDIRECT_KEEP_VERB |
| **需要補救** | 是 |

出現此錯誤是因為 Azure 檔同步不支援 HTTP 重定向（3xx 狀態碼）。 要解決此問題，請禁用代理伺服器或網路設備上的 HTTP 重定向。

<a id="-2134364027"></a>**離線資料傳輸期間發生超時，但仍處於中。**  

| | |
|-|-|
| **HRESULT** | 0x80c83085 |
| **HRESULT (十進位)** | -2134364027 |
| **錯誤字串** | ECS_E_DATA_INGESTION_WAIT_TIMEOUT |
| **需要補救** | 否 |

當資料引入操作超過超時時，將發生此錯誤。 如果同步正在進行（應用ItemCount 大於 0），則可以忽略此錯誤。 請參閱[如何監視當前同步會話的進度？](#how-do-i-monitor-the-progress-of-a-current-sync-session)

### <a name="common-troubleshooting-steps"></a>常用的疑難排解步驟
<a id="troubleshoot-storage-account"></a>**驗證存儲帳戶是否存在。**  
# <a name="portal"></a>[入口網站](#tab/azure-portal)
1. 瀏覽至儲存體同步服務中的同步群組。
2. 選取同步群組內的雲端端點。
3. 記下開啟的窗格中顯示的 Azure 檔案共用名稱。
4. 選取連結的儲存體帳戶。 如果此連結失敗，表示參考的儲存體帳戶已移除。
    ![此螢幕擷取畫面顯示有連結通往儲存體帳戶的雲端端點詳細資料窗格。](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)
```powershell
# Variables for you to populate based on your configuration
$region = "<Az_Region>"
$resourceGroup = "<RG_Name>"
$syncService = "<storage-sync-service>"
$syncGroup = "<sync-group>"

# Log into the Azure account
Connect-AzAccount

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = [System.String[]]@()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the " + `
        " selected Azure Region or the region is mistyped.")
}

# Check to ensure resource group exists
$resourceGroups = [System.String[]]@()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    throw [System.Exception]::new("The provided resource group $resourceGroup does not exist.")
}

# Check to make sure the provided Storage Sync Service
# exists.
$syncServices = [System.String[]]@()

Get-AzStorageSyncService -ResourceGroupName $resourceGroup | ForEach-Object {
    $syncServices += $_.StorageSyncServiceName
}

if ($syncServices -notcontains $syncService) {
    throw [System.Exception]::new("The provided Storage Sync Service $syncService does not exist.")
}

# Check to make sure the provided Sync Group exists
$syncGroups = [System.String[]]@()

Get-AzStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $syncService | ForEach-Object {
    $syncGroups += $_.SyncGroupName
}

if ($syncGroups -notcontains $syncGroup) {
    throw [System.Exception]::new("The provided sync group $syncGroup does not exist.")
}

# Get reference to cloud endpoint
$cloudEndpoint = Get-AzStorageSyncCloudEndpoint `
    -ResourceGroupName $resourceGroup `
    -StorageSyncServiceName $syncService `
    -SyncGroupName $syncGroup

# Get reference to storage account
$storageAccount = Get-AzStorageAccount | Where-Object { 
    $_.Id -eq $cloudEndpoint.StorageAccountResourceId
}

if ($storageAccount -eq $null) {
    throw [System.Exception]::new("The storage account referenced in the cloud endpoint does not exist.")
}
```
---

<a id="troubleshoot-azure-file-share"></a>**確保 Azure 檔共用存在。**  
# <a name="portal"></a>[入口網站](#tab/azure-portal)
1. 按一下左側目錄上的 **"概述"** 以返回到主存儲帳戶頁面。
2. 選擇 **"檔**"以查看檔共用清單。
3. 確認雲端端點所參考的檔案共用出現在檔案共用清單中 (您應已先前的步驟 1 中記下這項資料)。

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)
```powershell
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $cloudEndpoint.AzureFileShareName -and
    $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    throw [System.Exception]::new("The Azure file share referenced by the cloud endpoint does not exist")
}
```
---

<a id="troubleshoot-rbac"></a>**確保 Azure 檔同步有權訪問存儲帳戶。**  
# <a name="portal"></a>[入口網站](#tab/azure-portal)
1. 按一下左側目錄的 [存取控制 (IAM)]****。
1. 按一下 [角色指派]**** 索引標籤，列出可存取儲存體帳戶的使用者和應用程式 (*服務主體*)。
1. 驗證**Microsoft.StorageSync**或**混合檔同步服務**（舊應用程式名稱）顯示在清單中，並帶有**讀取器和資料訪問**角色。 

    ![存儲帳戶的存取控制選項卡中混合檔同步服務主體的螢幕截圖](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    如果**Microsoft.StorageSync**或**混合檔同步服務**未顯示在清單中，則執行以下步驟：

    - 按一下 **[新增]**。
    - 在 [角色]**** 欄位中，選取 [讀取者及資料存取]****。
    - 在 **"選擇"** 欄位中，鍵入**Microsoft.StorageSync，** 選擇該角色並按一下"**保存**"。

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)
```powershell    
$role = Get-AzRoleAssignment -Scope $storageAccount.Id | Where-Object { $_.DisplayName -eq "Microsoft.StorageSync" }

if ($role -eq $null) {
    throw [System.Exception]::new("The storage account does not have the Azure File Sync " + `
                "service principal authorized to access the data within the " + ` 
                "referenced Azure file share.")
}
```
---

### <a name="how-do-i-prevent-users-from-creating-files-containing-unsupported-characters-on-the-server"></a>如何防止使用者在伺服器上建立所含的字元不受支援的檔案？
您可以使用[檔案伺服器資源管理員 (FSRM) 的檔案檢測](https://docs.microsoft.com/windows-server/storage/fsrm/file-screening-management)，阻止檔案名稱中包含非支援字元的檔案建立於伺服器上。 您可能必須使用 PowerShell 來執行此作業，因為不支援的字元大多是無法列印的，因此您必須先將其十六進位表示法轉換為字元。

請先使用 [New-FsrmFileGroup Cmdlet](https://docs.microsoft.com/powershell/module/fileserverresourcemanager/new-fsrmfilegroup) 建立 FSRM 檔案群組。 此範例會定義僅包含兩個非支援字元的群組，但您可以視需要在檔案群組中包含任意數目的字元。

```powershell
New-FsrmFileGroup -Name "Unsupported characters" -IncludePattern @(("*"+[char]0x00000090+"*"),("*"+[char]0x0000008F+"*"))
```

在定義 FSRM 檔案群組後，您可以使用 New-FsrmFileScreen Cmdlet 建立 FSRM 檔案檢測。

```powershell
New-FsrmFileScreen -Path "E:\AFSdataset" -Description "Filter unsupported characters" -IncludeGroup "Unsupported characters"
```

> [!Important]  
> 請注意，檔螢幕應僅用於阻止創建 Azure 檔同步不支援的字元。如果檔螢幕在其他方案中使用，同步將繼續嘗試將檔從 Azure 檔共用下載到伺服器，並且由於檔螢幕而被阻止，從而導致高資料出口。 

## <a name="cloud-tiering"></a>雲端階層處理 
在雲端階層中有以下兩種失敗：

- 檔案無法分層，表示 Azure 檔案同步嘗試將檔案分層至 Azure 檔案但不成功。
- 檔案無法回收，表示當使用者嘗試存取已分層的檔案時，Azure 檔案同步檔案系統篩選器 (StorageSync.sys) 無法下載資料。

上述任一種失敗會導致兩種主要類型的失敗：

- 雲端儲存體失敗
    - ** 暫時性儲存體服務可用性問題。 如需詳細資訊，請參閱 [Azure 儲存體服務等級協定 (SLA)](https://azure.microsoft.com/support/legal/sla/storage/v1_2/)。
    - ** 無法存取 Azure 檔案共用。 此失敗通常發生在您刪除的 Azure 檔案共用仍是同步群組中的雲端端點時。
    - ** 無法存取儲存體帳戶。 當您刪除的儲存體帳戶仍有 Azure 檔案共用是同步處理群組中的雲端端點時，通常會發生此失敗。 
- 伺服器失敗 
  - ** 未載入 Azure 檔案同步處理檔案系統篩選器 (StorageSync.sys)。 一定要載入 Azure 檔案同步處理檔案系統篩選器，才能回應分層/回收要求。 發生無法載入篩選器的情況可能有幾個原因，但最常見的是系統管理員手動將它卸載。 Azure 檔案同步處理檔案系統篩選器不論何時都必須保持載入，Azure 檔案同步才能正常運作。
  - ** 遺失、損毀或中斷的重新剖析點。 重新剖析點是檔案上的特殊資料結構，由以下兩部分所組成：
    1. 重新剖析標記。它會向作業系統指示，Azure 檔案同步處理檔案系統篩選器 (StorageSync.sys) 可能需要對檔案的 IO 採取一些動作。 
    2. 重新剖析資料。它會向檔案系統篩選器指示相關聯雲端端點 (Azure 檔案共用) 上檔案的 URI。 
        
       造成重新剖析點損毀的最常見狀況是系統管理員嘗試修改標記或其資料。 
  - ** 網路連線問題。 為了分層或回收檔案，伺服器必須有網際網路連線。

下列各節將說明如何針對雲端階層問題進行疑難排解，以及如何判別雲端儲存體問題或伺服器問題。

### <a name="how-to-monitor-tiering-activity-on-a-server"></a>如何監視伺服器上的分層活動  
若要監視伺服器上的分層活動，請在遙測事件記錄中使用事件識別碼 9003、9016 和 9029 (位於事件檢視器中的 Applications and Services\Microsoft\FileSync\Agent 下)。

- 事件識別碼 9003 會提供伺服器端點的錯誤分布。 例如，總錯誤計數、錯誤代碼等。請注意，每個錯誤代碼記錄一個事件。
- 事件識別碼 9016 會提供磁碟區的建立映像結果。 例如可用空間百分比、在工作階段中建立映像的檔案數目、無法建立映像的檔案數目等。
- 事件識別碼 9029 會提供伺服器端點的虛像化工作階段資訊。 例如，在工作階段中嘗試的檔案數目、在工作階段中分層的檔案數目、已分層的檔案數目等。

### <a name="how-to-monitor-recall-activity-on-a-server"></a>如何監視伺服器上的回收活動
若要監視伺服器上的回收活動，請在遙測事件記錄中使用事件識別碼 9005、9006、9009 和 9059 (位於事件檢視器中的 Applications and Services\Microsoft\FileSync\Agent 下)。

- 事件識別碼 9005 會提供伺服器端點的回收可靠性。 例如，存取的唯一檔案總數、存取失敗的唯一檔案總數等。
- 事件識別碼 9006 會提供伺服器端點的回收錯誤分布。 例如，失敗請求總數、錯誤代碼等。請注意，每個錯誤代碼記錄一個事件。
- 事件識別碼 9009 會提供伺服器端點的回收工作階段資訊。 例如，DurationSeconds、CountFilesRecallSucceeded、CountFilesRecallFailed 等。
- 事件識別碼 9059 會提供伺服器端點的應用程式回收分布。 例如，ShareId、應用程式名稱和 TotalEgressNetworkBytes。

### <a name="how-to-troubleshoot-files-that-fail-to-tier"></a>如何針對檔案無法分層的問題進行疑難排解
如果無法在 Azure 檔案中將檔案分層：

1. 在 [事件檢視器] 中，檢閱 Applications and Services\Microsoft\FileSync\Agent 下的遙測、作業和診斷事件記錄。 
   1. 確認檔案存在於 Azure 檔案共用中。

      > [!NOTE]
      > 檔案必須同步到 Azure 檔案共用，才能將它分層。

   2. 確認伺服器具有網際網路連線。 
   3. 確認 Azure 檔案同步篩選器驅動程式 (StorageSync.sys 與 StorageSyncGuard.sys) 正在執行：
       - 在提高權限的命令提示字元，執行 `fltmc`。 確認已列出 StorageSync.sys 和 StorageSyncGuard.sys 檔案系統篩選器驅動程式。

> [!NOTE]
> 如果檔案無法分層，則會每小時在遙測事件記錄中記錄一次事件識別碼 9003 (對於每個錯誤碼會記錄一個事件)。 檢查[分層錯誤和修正](#tiering-errors-and-remediation)部分，查看是否列出了錯誤代碼的修正步驟。

### <a name="tiering-errors-and-remediation"></a>分層錯誤和修正

| HRESULT | HRESULT (十進位) | 錯誤字串 | 問題 | 補救 |
|---------|-------------------|--------------|-------|-------------|
| 0 x80c86043 | -2134351805 | ECS_E_GHOSTING_FILE_IN_USE | 檔無法分層，因為它正在使用中。 | 不需要任何動作。 當檔不再使用時，該檔將被分層。 |
| 0 x80c80241 | -2134375871 | ECS_E_GHOSTING_EXCLUDED_BY_SYNC | 檔無法分層，因為它被同步排除。 | 不需要任何動作。 無法對同步排除清單中的檔進行分層。 |
| 0 x80c86042 | -2134351806 | ECS_E_GHOSTING_FILE_NOT_FOUND | 檔無法分層，因為它在伺服器上找不到。 | 不需要任何動作。 如果錯誤仍然存在，請檢查伺服器上是否存在該檔。 |
| 0 x80c83053 | -2134364077 | ECS_E_CREATE_SV_FILE_DELETED | 檔無法分層，因為它在 Azure 檔共用中被刪除。 | 不需要任何動作。 當下一個下載同步會話運行時，應在伺服器上刪除該檔。 |
| 0x80c8600e | -2134351858 | ECS_E_AZURE_SERVER_BUSY | 由於網路問題，檔無法分層。 | 不需要任何動作。 如果錯誤仍然存在，請檢查與 Azure 檔共用的網路連接。 |
| 0x80072ee7 | -2147012889 | WININET_E_NAME_NOT_RESOLVED | 由於網路問題，檔無法分層。 | 不需要任何動作。 如果錯誤仍然存在，請檢查與 Azure 檔共用的網路連接。 |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | 由於訪問被拒絕錯誤，檔無法分層。 如果檔位於 DFS-R 唯讀複製資料夾中，則可能發生此錯誤。 | Azure 檔案同步不支援 DFS-R 唯讀複寫資料夾上的伺服器端點。 有關詳細資訊，請參閱[規劃指南](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#distributed-file-system-dfs)。 |
| 0x80072efe | -2147012866 | WININET_E_CONNECTION_ABORTED | 由於網路問題，檔無法分層。 | 不需要任何動作。 如果錯誤仍然存在，請檢查與 Azure 檔共用的網路連接。 |
| 0 x80c80261 | -2134375839 | ECS_E_GHOSTING_MIN_FILE_SIZE | 檔無法分層，因為檔案大小小於受支援的大小。 | 如果代理版本小於 9.0，則受支援的最低檔案大小為 64kb。 如果代理版本為 9.0 和較新，則受支援的最低檔案大小基於檔案系統群集大小（雙檔案系統群集大小）。 例如，如果檔案系統群集大小為 4kb，則最小檔案大小為 8kb。 |
| 0x80c83007 | -2134364153 | ECS_E_STORAGE_ERROR | 由於 Azure 存儲問題，檔無法分層。 | 如果錯誤仍然存在，請打開支援請求。 |
| 0x800703e3 | -2147023901 | ERROR_OPERATION_ABORTED | 檔無法分層，因為它同時被調用。 | 不需要任何動作。 當撤回完成並且該檔不再使用時，該檔將被分層。 |
| 0 x80c80264 | -2134375836 | ECS_E_GHOSTING_FILE_NOT_SYNCED | 檔無法分層，因為它尚未同步到 Azure 檔共用。 | 不需要任何動作。 檔在同步到 Azure 檔共用後將分層。 |
| 0x80070001 | -2147942401 | ERROR_INVALID_FUNCTION | 檔無法分層，因為雲分層篩選器驅動程式 （storagesync.sys） 未運行。 | 要解決此問題，打開一個提升的命令提示符並運行以下命令：`fltmc load storagesync`<br>如果運行 fltmc 命令時存儲同步篩選器驅動程式載入失敗，請卸載 Azure 檔同步代理，重新開機伺服器並重新安裝 Azure 檔同步代理。 |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | 由於伺服器終結點所在的卷上的磁碟空間不足，檔無法分層。 | 要解決此問題，請釋放伺服器終結點所在的卷上至少 100 MB 的磁碟空間。 |
| 0x80070490 | -2147023728 | ERROR_NOT_FOUND | 檔無法分層，因為它尚未同步到 Azure 檔共用。 | 不需要任何動作。 檔在同步到 Azure 檔共用後將分層。 |
| 0 x80c80262 | -2134375838 | ECS_E_GHOSTING_UNSUPPORTED_RP | 檔無法分層，因為它是不受支援的重解析點。 | 如果檔是資料重復資料消除重複分析點，請按照[規劃指南](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#data-deduplication)中的步驟啟用資料重復資料消除支援。 不支援具有資料重復資料消除以外的重解析點的檔，也不會分層。  |
| 0 x80c83052 | -2134364078 | ECS_E_CREATE_SV_STREAM_ID_MISMATCH | 檔無法分層，因為它已被修改。 | 不需要任何動作。 修改後的檔將同步到 Azure 檔共用後，該檔將分層。 |
| 0 x80c80269 | -2134375831 | ECS_E_GHOSTING_REPLICA_NOT_FOUND | 檔無法分層，因為它尚未同步到 Azure 檔共用。 | 不需要任何動作。 檔在同步到 Azure 檔共用後將分層。 |
| 0x80072ee2 | -2147012894 | WININET_E_TIMEOUT | 由於網路問題，檔無法分層。 | 不需要任何動作。 如果錯誤仍然存在，請檢查與 Azure 檔共用的網路連接。 |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | 檔無法分層，因為它已被修改。 | 不需要任何動作。 修改後的檔將同步到 Azure 檔共用後，該檔將分層。 |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | 由於系統資源不足，檔無法分層。 | 如果錯誤仍然存在，請調查哪個應用程式或核心模式驅動程式耗盡了系統資源。 |



### <a name="how-to-troubleshoot-files-that-fail-to-be-recalled"></a>如何針對無法重新叫用檔案的問題進行疑難排解  
如果無法重新叫用檔案：
1. 在 [事件檢視器] 中，檢閱 Applications and Services\Microsoft\FileSync\Agent 下的遙測、作業和診斷事件記錄。
    1. 確認檔案存在於 Azure 檔案共用中。
    2. 確認伺服器具有網際網路連線。 
    3. 開啟 [服務 MMC 嵌入式管理單元]，並確認儲存體同步代理程式服務 (FileSyncSvc) 正在執行。
    4. 確認 Azure 檔案同步篩選器驅動程式 (StorageSync.sys 與 StorageSyncGuard.sys) 正在執行：
        - 在提高權限的命令提示字元，執行 `fltmc`。 確認已列出 StorageSync.sys 和 StorageSyncGuard.sys 檔案系統篩選器驅動程式。

> [!NOTE]
> 如果檔案無法回收，則會每小時在遙測事件記錄中記錄一次事件識別碼 9006 (對於每個錯誤碼會記錄一個事件)。 檢查["召回錯誤"和"修正](#recall-errors-and-remediation)"部分，查看是否列出了錯誤代碼的修正步驟。

### <a name="recall-errors-and-remediation"></a>召回錯誤和補救

| HRESULT | HRESULT (十進位) | 錯誤字串 | 問題 | 補救 |
|---------|-------------------|--------------|-------|-------------|
| 0x80070079 | -2147942521 | ERROR_SEM_TIMEOUT | 由於 I/O 超時，檔無法撤回。 出現此問題的原因有多種：伺服器資源約束、網路連接不良或 Azure 存儲問題（例如，限制）。 | 不需要任何動作。 如果錯誤持續數小時，請開啟支援案例。 |
| 0x80070036 | -2147024842 | ERROR_NETWORK_BUSY | 由於網路問題，檔無法撤回。  | 如果錯誤仍然存在，請檢查與 Azure 檔共用的網路連接。 |
| 0 x80c80037 | -2134376393 | ECS_E_SYNC_SHARE_NOT_FOUND | 檔無法撤回，因為伺服器終結點已被刪除。 | 要解決此問題，請參閱[刪除伺服器終結點後，伺服器無法訪問分層檔](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)。 |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | 由於訪問被拒絕錯誤，檔無法撤回。 如果啟用了存儲帳戶上的防火牆和虛擬網路設置，並且伺服器無法訪問存儲帳戶，則可能會出現此問題。 | 要解決此問題，請按照部署指南中的["配置防火牆"和"虛擬網路設置](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)"部分中記錄的步驟添加伺服器 IP 位址或虛擬網路。 |
| 0 x80c86002 | -2134351870 | ECS_E_AZURE_RESOURCE_NOT_FOUND | 無法撤回該檔，因為它在 Azure 檔共用中無法訪問。 | 要解決此問題，請驗證 Azure 檔共用中的檔存在。 如果該檔存在於 Azure 檔共用中，則升級到最新的 Azure 檔同步[代理版本](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions)。 |
| 0x80c8305f | -2134364065 | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED | 由於存儲帳戶的授權失敗，檔無法撤回。 | 要解決此問題，請驗證[Azure 檔同步有權訪問存儲帳戶](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#troubleshoot-rbac)。 |
| 0x80c86030 | -2134351824 | ECS_E_AZURE_FILE_SHARE_NOT_FOUND | 檔無法撤回，因為 Azure 檔共用不可訪問。 | 驗證檔共用是否存在且可訪問。 如果檔共用被刪除並重新創建，則執行同步中記錄的步驟[失敗，因為 Azure 檔共用已被刪除並重新創建](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#-2134375810)部分以刪除並重新創建同步組。 |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | 由於系統資源不足，檔無法調用。 | 如果錯誤仍然存在，請調查哪個應用程式或核心模式驅動程式耗盡了系統資源。 |
| 0x8007000e | -2147024882 | ERROR_OUTOFMEMORY | 由於記憶體不足，檔無法撤回。 | 如果錯誤仍然存在，請調查哪個應用程式或核心模式驅動程式導致記憶體不足的情況。 |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | 由於磁碟空間不足，檔無法調用。 | 要解決此問題，請通過將檔移動到其他卷、增加卷大小或使用"調用-存儲同步雲層"Cmdlet 強制檔分層來釋放卷上的空間。 |

### <a name="tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint"></a>刪除伺服器端點之後，無法存取伺服器上的分層檔案
如果在刪除伺服器終結點之前未調用這些檔，則伺服器上的分層檔將變得不可訪問。

如果無法訪問分層檔，則記錄錯誤
- 同步檔時，錯誤代碼 -2147942467 （0x80070043 - ERROR_BAD_NET_NAME） 記錄在 Item結果事件日誌中
- 召回檔時，錯誤代碼 -2134376393 （0x80c80037 - ECS_E_SYNC_SHARE_NOT_FOUND） 記錄在 Recall結果事件日誌中

如果符合下列條件，分層檔案的存取就有可能還原：
- 伺服器端點在過去 30 天內刪除
- 未刪除雲端端點 
- 檔案共用未刪除
- 未刪除同步群組

如果符合上述條件，您就可以在 30 天內還原伺服器上檔案的存取權，只要在相同同步群組中，將伺服器端點重新建立在伺服器上的相同路徑上即可。 

如果不符合上述條件，您就無法還原存取，因為伺服器上的這些分層檔案現在是孤立狀態。 請按照以下說明刪除孤立的分層檔。

**注意**
- 當在伺服器上無法訪問分層檔時，如果直接存取 Azure 檔共用，則仍應訪問完整檔。
- 為了防止將來孤立分層檔，請按照[刪除伺服器終結點](https://docs.microsoft.com/azure/storage/files/storage-sync-files-server-endpoint#remove-a-server-endpoint)中記錄的步驟刪除伺服器終結點。

<a id="get-orphaned"></a>**如何獲取孤立分層檔的清單** 

1. 已安裝驗證 Azure 檔同步代理版本 v5.1 或更高版本。
2. 運行以下 PowerShell 命令以列出孤立的分層檔：
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. 保存孤立TieredFiles.txt輸出檔案，以防檔在刪除後需要從備份中還原。

<a id="remove-orphaned"></a>**如何刪除孤立的分層檔** 

*選項 1：刪除孤立的分層檔*

此選項刪除 Windows 伺服器上的孤立分層檔，但要求在伺服器終結點因 30 天后的娛樂而存在或連接到其他同步組時將其刪除。 如果在重新創建伺服器終結點之前在 Windows 伺服器或 Azure 檔共用上更新檔，將會發生檔衝突。

1. 已安裝驗證 Azure 檔同步代理版本 v5.1 或更高版本。
2. 備份 Azure 檔共用和伺服器終結點位置。
3. 按照[刪除伺服器終結點](https://docs.microsoft.com/azure/storage/files/storage-sync-files-server-endpoint#remove-a-server-endpoint)中記錄的步驟刪除同步組中的伺服器終結點（如果存在）。

> [!Warning]  
> 如果在使用"刪除存儲同步孤立"Tierdd檔 Cmdlet 之前未刪除伺服器終結點，則刪除伺服器上的孤立分層檔將刪除 Azure 檔共用中的完整檔。 

4. 運行以下 PowerShell 命令以列出孤立的分層檔：

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
5. 保存孤立TieredFiles.txt輸出檔案，以防檔在刪除後需要從備份中還原。
6. 運行以下 PowerShell 命令以刪除孤立的分層檔：

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFilesRemoved = Remove-StorageSyncOrphanedTieredFiles -Path <folder path containing orphaned tiered files> -Verbose
$orphanFilesRemoved.OrphanedTieredFiles > DeletedOrphanFiles.txt
```
**注意** 
- 未同步到 Azure 檔共用的伺服器上修改的分層檔將被刪除。
- 不會刪除可訪問的分層檔（不是孤立檔案）。
- 非分層檔將保留在伺服器上。

7. 可選：如果在步驟 3 中刪除，則重新創建伺服器終結點。

*選項 2：裝載 Azure 檔共用，並在本地複製伺服器上孤立的檔*

此選項不需要刪除伺服器終結點，但需要足夠的磁碟空間才能在本地複製完整檔。

1. [將](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows)Azure 檔共用裝載到具有孤立分層檔的 Windows 伺服器上。
2. 運行以下 PowerShell 命令以列出孤立的分層檔：
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. 使用孤立分層檔.txt 輸出檔案標識伺服器上的孤立分層檔。
4. 通過將完整檔從 Azure 檔共用複製到 Windows 伺服器，覆蓋孤立的分層檔。

### <a name="how-to-troubleshoot-files-unexpectedly-recalled-on-a-server"></a>如何排除伺服器上意外召回的檔  
防毒程式、備份及其他讀取大量檔案的應用程式，除非採用離線屬性並略過讀取這些檔案的內容，否則將會導致非預期的重新叫用。 略過支援此選項之產品的離線檔案，有助於在執行防毒掃描、備份等作業時，避免發生非預期的重新叫用。

請洽詢您的軟體廠商，了解如何設定其解決方案以略過讀取離線檔案。

非預期的重新叫用也會在其他情況下發生，例如在 [檔案總管] 中瀏覽檔案時。 在伺服器的 [檔案總管]中開啟包含雲端層檔案的資料夾，可能會導致非預期的重新叫用。 特別是伺服器上已啟用防毒程式時，更容易發生。

> [!NOTE]
>使用遙測事件記錄中的事件識別碼 9059 來判斷哪些應用程式造成回收。 此事件會提供伺服器端點的應用程式回收分布，且一小時記錄一次。

## <a name="general-troubleshooting"></a>一般疑難排解
如果在伺服器上使用 Azure 檔案同步時發生問題，請完成以下步驟開始疑難排解：
1. 在 [事件檢視器] 中檢閱遙測、作業和診斷事件記錄。
    - 系統會將同步、分層和回收問題記錄在 Applications and Services\Microsoft\FileSync\Agent 下的遙測、診斷和作業事件記錄中。
    - 與管理伺服器相關的問題 (例如組態設定) 則記錄在 Applications and Services\Microsoft\FileSync\Management 下的作業和診斷事件記錄中。
2. 確認 Azure 檔案同步服務正在伺服器上執行：
    - 開啟 [服務] MMC 嵌入式管理單元，並確認儲存體同步代理程式服務 (FileSyncSvc) 正在執行。
3. 確認 Azure 檔案同步篩選器驅動程式 (StorageSync.sys 與 StorageSyncGuard.sys) 正在執行：
    - 在提高權限的命令提示字元，執行 `fltmc`。 確認已列出 StorageSync.sys 和 StorageSyncGuard.sys 檔案系統篩選器驅動程式。

如果問題沒有解決，執行 AFSDiag 工具：
1. 建立將用來儲存 AFSDiag 輸出的目錄 (例如 C:\Output)。
    > [!NOTE]
    >在收集日誌之前，AFSDiag 將刪除輸出目錄中的所有內容。 指定不包含資料的輸出位置。
2. 開啟提升權限的 PowerShell 視窗，然後執行下列命令 (每個命令後要按 Enter 鍵)：

    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. 針對 Azure 檔案同步核心模式追蹤層級，輸入 **1** (除非為了建立更詳細的追蹤而指定其他值)，然後按 Enter。
4. 針對 Azure 檔案同步使用者模式追蹤層級，輸入 **1** (除非為了建立更詳細的追蹤而指定其他值)，然後按 Enter。
5. 重現問題。 完成時，輸入 **D**。
6. 含有記錄和追蹤檔案的 .zip 檔案將會儲存在指定的輸出目錄中。

## <a name="see-also"></a>另請參閱
- [監視 Azure 檔案同步](storage-sync-files-monitoring.md)
- [Azure 檔案服務常見問題集](storage-files-faq.md)
- [針對 Windows 中的 Azure 檔案服務問題進行疑難排解](storage-troubleshoot-windows-file-connection-problems.md)
- [解決 Linux 中的 Azure 檔問題](storage-troubleshoot-linux-file-connection-problems.md)
