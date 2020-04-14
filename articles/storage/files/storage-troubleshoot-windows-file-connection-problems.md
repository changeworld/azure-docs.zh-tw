---
title: 針對 Windows 中的 Azure 檔案服務問題進行疑難排解 | Microsoft Docs
description: 針對 Windows 中的 Azure 檔案服務問題進行疑難排解
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 3237fe7d87ad058f255d1c77cb6d814bcd1c292e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262242"
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>針對 Windows 中的 Azure 檔案服務問題進行疑難排解

本文列出當您從 Windows 用戶端連線時，與 Microsoft Azure 檔案服務相關的常見問題。 文中也會提供這些問題的可能原因和解決方案。 除了本文中的故障排除步驟外,您還可以使用[AzFile 診斷](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) 來確保 Windows 用戶端環境具有正確的先決條件。 AzFileDiagnostics 會自動偵測本文中提及的大部分徵兆，並協助設定您的環境以取得最佳效能。 您也可以在 [Azure 檔案共用疑難排解員](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares)中找到此資訊，當中有提供步驟來協助您解決連線/對應/掛接 Azure 檔案共用的問題。

<a id="error5"></a>
## <a name="error-5-when-you-mount-an-azure-file-share"></a>掛接 Azure 檔案共用時發生錯誤 5

您嘗試掛接檔案共用時，可能會收到下列錯誤：

- 發生系統錯誤 5。 存取遭到拒絕。

### <a name="cause-1-unencrypted-communication-channel"></a>原因 1：通訊通道未加密

基於安全考量，如果通訊通道未加密，而且未從 Azure 檔案共用所在的相同資料中心進行連線嘗試，與 Azure 檔案共用的連線就會遭到封鎖。 如果儲存體帳戶上啟用[需要安全傳輸](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer)設定，則也可能會封鎖相同資料中心內未加密的連線。 唯有當使用者的用戶端 OS 支援 SMB 加密時，系統才會提供加密的通訊通道。

Windows 8、Windows Server 2012 和更新版本的每個系統交涉都要求包含支援加密的 SMB 3.0。

### <a name="solution-for-cause-1"></a>原因 1 的解決方案

1. 從支援 SMB 加密的用戶端 (Windows 8、Windows Server 2012 和更新版本) 進行連線，或從 Azure 檔案共用所使用的 Azure 儲存體帳戶相同的資料中心上的虛擬機器進行連線。
2. 如果用戶端不支援 SMB 加密，請確認儲存體帳戶上的[需要安全傳輸](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer)設定已經停用。

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>原因 2:在儲存帳戶上啟用虛擬網路或防火牆規則 

如果在儲存體帳戶上設定虛擬網路 (VNET) 和防火牆規則，網路流量將會被拒絕存取，除非用戶端 IP 位址或虛擬網路獲准存取。

### <a name="solution-for-cause-2"></a>原因 2 的解決方案

確認已經在儲存體帳戶上正確設定虛擬網路和防火牆規則。 若要測試虛擬網路或防火牆規則是否造成問題，請暫時將儲存體帳戶上的設定變更為 [允許來自所有網路的存取]****。 若要深入了解，請參閱[設定 Azure 儲存體防火牆和虛擬網路](https://docs.microsoft.com/azure/storage/common/storage-network-security)。

### <a name="cause-3-share-level-permissions-are-incorrect-when-using-identity-based-authentication"></a>原因 3:使用基於標識的身份驗證時,共用級許可權不正確

如果使用者使用活動目錄 (AD) 或 Azure 活動目錄功能服務 (Azure AD DS) 身份驗證存取 Azure 檔案共享,則如果共用層級的許可權不正確,則存取檔案共享失敗,出現"拒絕存取"錯誤。 

### <a name="solution-for-cause-3"></a>原因 3 的解決方案

要更新分享等級權限,請參考[參考的權限](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-domain-service-enable#2-assign-access-permissions-to-an-identity)。

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>當您嘗試掛接或取消掛接 Azure 檔案共用時發生錯誤 53、錯誤 67 或錯誤 87

當您嘗試從內部部署或不同資料中心掛接檔案共用時，可能會收到下列錯誤：

- 發生系統錯誤 53。 找不到網路路徑。
- 發生系統錯誤 67。 找不到網路名稱。
- 發生系統錯誤 87。 參數錯誤。

### <a name="cause-1-port-445-is-blocked"></a>原因 1:埠 445 被阻止

如果連接埠 445 至 Azure 檔案服務資料中心的輸出通訊遭到封鎖，可能會發生系統錯誤 53 或系統錯誤 67。 若要查看 ISP 是否允許從連接埠 445 進行存取的摘要，請參閱 [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) \(英文\)。

若要檢查您的防火牆或 ISP 是否封鎖連接埠 445，請使用 [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) 工具或 `Test-NetConnection` Cmdlet。 

要使用`Test-NetConnection`cmdlet,必須安裝 Azure PowerShell 模組,有關詳細資訊,請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。 請記得以儲存體帳戶的相關名稱取代 `<your-storage-account-name>` 和 `<your-resource-group-name>`。

   
    $resourceGroupName = "<your-resource-group-name>"
    $storageAccountName = "<your-storage-account-name>"

    # This command requires you to be logged into your Azure account, run Login-AzAccount if you haven't
    # already logged in.
    $storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName

    # The ComputerName, or host, is <storage-account>.file.core.windows.net for Azure Public Regions.
    # $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign clouds
    # or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
    Test-NetConnection -ComputerName ([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) -Port 445
    
如果連線成功，您應會看見下列輸出：
    
  
    ComputerName     : <your-storage-account-name>
    RemoteAddress    : <storage-account-ip-address>
    RemotePort       : 445
    InterfaceAlias   : <your-network-interface>
    SourceAddress    : <your-ip-address>
    TcpTestSucceeded : True
 

> [!Note]  
> 上述命令會傳回儲存體帳戶的目前 IP 位址。 此 IP 位址不保證會維持不變，而且可能隨時變更。 請勿將此 IP 位址硬式編碼到任何指令碼，或防火牆組態中。

### <a name="solution-for-cause-1"></a>原因 1 的解決方案

#### <a name="solution-1---use-azure-file-sync"></a>解決方案 1 - 使用 Azure 檔案同步
Azure 檔案同步可以將本地 Windows 伺服器轉換為 Azure 檔案共享的快速快取。 您可以使用 Windows Server 上可用的任何通訊協定來從本機存取資料，包括 SMB、NFS 和 FTPS。 Azure 檔案同步在埠 443 上工作,因此可用作從埠 445 被阻止的用戶端訪問 Azure 檔的解決方法。 [瞭解如何設定 Azure 檔案同步](https://docs.microsoft.com/azure/storage/files/storage-sync-files-extend-servers)。

#### <a name="solution-2---use-vpn"></a>解決方案 2 - 使用 VPN
通過設置 VPN 到您的特定存儲帳戶,流量將經過一個安全隧道,而不是通過互聯網。 按照[說明設定 VPN](storage-files-configure-p2s-vpn-windows.md)從 Windows 訪問 Azure 檔。

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>解決方案 3 - 在您 ISP/IT 系統管理員的協助下解除封鎖連接埠 445
與 IT 部門或 ISP 合作,開啟連接埠 445 出站到[Azure IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>解決方案 4 - 使用儲存體總管/Powershell 等 REST API 類型的工具
除了 SMB 之外,Azure 檔還支援 REST。 REST 訪問在埠 443(標準 tcp)上工作。 使用 REST API 編寫的各種工具可實現豐富的 UI 體驗。 [存儲資源管理員](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)就是其中之一。 [下載並安裝儲存資源管理員](https://azure.microsoft.com/features/storage-explorer/)並連接到 Azure 檔案支援的檔案共用。 您還可以使用[PowerShell,](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-powershell)後者也允許使用者 REST API。

### <a name="cause-2-ntlmv1-is-enabled"></a>原因 2: 啟用 NTMv1

如果用戶端上已啟用 NTLMv1 通訊，就會發生系統錯誤 53 或系統錯誤 87。 Azure 檔案僅支援 NTLMv2 驗證。 啟用 NTLMv1 會使用戶端變得較不安全。 因此，Azure 檔案服務會封鎖通訊。 

若要判斷這是否為錯誤的原因，請確認已將下列登錄子機碼的值設為 3：

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

如需詳細資訊，請參閱 TechNet 上的 [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) 主題。

### <a name="solution-for-cause-2"></a>原因 2 的解決方案

在下列登錄子機碼中，將 **LmCompatibilityLevel** 值還原為預設值 3：

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816-not-enough-quota-is-available-to-process-this-command-when-you-copy-to-an-azure-file-share"></a>當您複製到 Azure 檔案共用時發生錯誤 1816「配額不足無法處理此命令」

### <a name="cause"></a>原因

當您到達同時開啟的控制代碼上限時 (此為針對掛接檔案共用之電腦上的檔案所允許的上限)，即會發生錯誤 1816。

### <a name="solution"></a>解決方法

關閉一些控制代碼以減少同時開啟的控制代碼數，然後再試一次。 有關詳細資訊,請參閱[Microsoft Azure 儲存效能和可伸縮性檢查表](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

要查看檔案共享、目錄或檔的打開句柄,請使用[獲取-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) PowerShell cmdlet。  

要關閉檔案共享、目錄或檔的打開句柄,請使用[Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) PowerShell cmdlet。

> [!Note]  
> 獲取-AzStorageFileHandle 和關閉 AzStorageFileHandle cmdlet 包含在 Az PowerShell 模組版本 2.4 或更高版本中。 要安裝最新的 Az PowerShell 模組,請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps)。

<a id="noaaccessfailureportal"></a>
## <a name="error-no-access-when-you-try-to-access-or-delete-an-azure-file-share"></a>嘗試訪問或刪除 Azure 文件共享時出錯"無訪問許可權"  
當您嘗試存取或刪除門戶中的 Azure 檔案共享時,可能會收到以下錯誤:

沒有存取權  
錯誤代碼: 403 

### <a name="cause-1-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>原因 1:在儲存帳戶上啟用虛擬網路或防火牆規則

### <a name="solution-for-cause-1"></a>原因 1 的解決方案

確認已經在儲存體帳戶上正確設定虛擬網路和防火牆規則。 若要測試虛擬網路或防火牆規則是否造成問題，請暫時將儲存體帳戶上的設定變更為 [允許來自所有網路的存取]****。 若要深入了解，請參閱[設定 Azure 儲存體防火牆和虛擬網路](https://docs.microsoft.com/azure/storage/common/storage-network-security)。

### <a name="cause-2-your-user-account-does-not-have-access-to-the-storage-account"></a>原因 2:您的使用者帳戶無法存取儲存帳戶

### <a name="solution-for-cause-2"></a>原因 2 的解決方案

瀏覽至 Azure 檔案共用所在的儲存體帳戶，按一下 [存取控制 (IAM)]****，並確認您的使用者帳戶擁有儲存體帳戶的存取權。 若要深入了解，請參閱[如何使用角色型存取控制 (RBAC) 保護儲存體帳戶](https://docs.microsoft.com/azure/storage/blobs/security-recommendations#data-protection)。

<a id="open-handles"></a>
## <a name="unable-to-delete-a-file-or-directory-in-an-azure-file-share"></a>無法刪除 Azure 檔案共用中的檔案或目錄
您試著刪除檔案時,您可能會收到以下錯誤:

指定的資源已標示為可供 SMB 用戶端刪除。

### <a name="cause"></a>原因
如果檔或目錄具有打開的句柄,則通常會出現此問題。 

### <a name="solution"></a>解決方法

如果 SMB 客戶端已關閉所有開啟的句柄,並且問題繼續發生,請執行以下操作:

- 使用[獲取-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) PowerShell cmdlet 查看打開的句柄。

- 使用[Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) PowerShell cmdlet 關閉打開的句柄。 

> [!Note]  
> 獲取-AzStorageFileHandle 和關閉 AzStorageFileHandle cmdlet 包含在 Az PowerShell 模組版本 2.4 或更高版本中。 要安裝最新的 Az PowerShell 模組,請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps)。

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>從 Windows 中的 Azure 檔案服務複製檔案或將檔案複製到其中的速度變慢

當您嘗試將檔案傳輸到 Azure 檔案服務時，可能會看到效能變慢。

- 如果您沒有特定的 I/O 大小需求下限，建議您使用 1 MB 的 I/O 大小以獲得最佳效能。
-   如果您知道擴充寫入檔案的最終大小，而且當檔案上未寫入的結尾中有零時您的軟體不會產生相容性問題，則請事先設定檔案大小，而不是將每次寫入設為擴充寫入。
-   使用正確的複製方法：
    -   在兩個檔共享之間進行任何傳輸,請使用[AzCopy。](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
    -   在內部部署電腦上的檔案共用之間，使用 [Robocopy](/azure/storage/files/storage-files-deployment-guide#robocopy) \(英文\)。

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>針對 Windows 8.1 或 Windows Server 2012 R2 的考量

若用戶端執行 Windows 8.1 或 Windows Server 2012 R2，請確定已安裝 [KB3114025](https://support.microsoft.com/help/3114025) Hotfix。 此 Hotfix 可改善建立和關閉控制代碼的效能。

您可以執行下列指令碼來檢查是否已安裝此 Hotfix：

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

如果已安裝 hotfix，則會顯示下列輸出︰

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> 自 2015 年 12 月起，Azure Marketplace 中的 Windows Server 2012 R2 映像預設已安裝 Hotfix KB3114025。

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer-or-this-pc"></a>我的電腦'或"此電腦"中沒有帶有磁碟機號的資料夾

如果您以系統管理員身分使用 net use 對應 Azure 檔案共用，該共用似乎就會遺失。

### <a name="cause"></a>原因

根據預設，Windows 檔案總管不會以系統管理員身分執行。 如果您從系統管理命令提示字元執行 net use，就是以系統管理員身分對應網路磁碟機。 因為對應的磁碟機是以使用者為中心，如果磁碟機掛接在不同的使用者帳戶下，登入的使用者帳戶不會顯示此磁碟機。

### <a name="solution"></a>解決方法
從非系統管理員命令掛接共用。 或者，您可以依照[此 TechNet 主題](https://technet.microsoft.com/library/ee844140.aspx)設定 **EnableLinkedConnections** 登錄值。

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>如果儲存體帳戶包含斜線，net use 命令就會失敗

### <a name="cause"></a>原因

Net use 命令會將斜線 (/) 解譯為命令列選項。 如果您的使用者帳戶名稱開頭為斜線，磁碟機對應將會失敗。

### <a name="solution"></a>解決方法

您可以使用下列其中一種方式來解決這個問題：

- 執行下列 PowerShell 命令：

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc"`

  從批次檔中，您可以使用此方式執行命令：

  `Echo new-smbMapping ... | powershell -command –`

- 除非斜線是第一個字元，否則，可以用雙引號括住金鑰來解決此問題。 如果是，可使用互動模式分開輸入您的密碼，或者，重新產生金鑰，以取得不是斜線開頭的金鑰。

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>應用程式或服務無法存取掛接的 Azure 檔案服務磁碟機

### <a name="cause"></a>原因

磁碟機是按每個使用者掛接。 如果您的應用程式或服務正在與掛接磁碟機之帳戶不同的使用者帳戶下執行，應用程式將不會看到該磁碟機。

### <a name="solution"></a>解決方法

使用下列其中一個解決方案：

-   從應用程式所屬的相同使用者帳戶掛接磁碟機。 您可以使用 PsExec 之類的工具。
- 在 net use 命令的使用者名稱和密碼參數中傳遞儲存體帳戶名稱和金鑰。
- 使用 cmdkey 命令以將認證新增至認證管理員。 透過互動式登入或使用 從服務帳號上下文下的命令列執行此`runas`動作 。
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- 直接對應共用而不使用對應磁碟機代號。 某些應用程式可能無法正確地重新連線至磁碟機代號，因此使用完整的 UNC 路徑可能比較可靠。 

  `net use * \\storage-account-name.file.core.windows.net\share`

遵循這些指示執行之後，當您針對系統/網路服務帳戶執行 net use 時，可能會收到下列錯誤訊息：「發生系統錯誤 1312。 指定的登入工作階段不存在。 可能已被終止。」 如果發生這種情況，請確定傳遞至 net use 的使用者名稱包含網域資訊 (例如，"[storage account name].file.core.windows.net")。

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>「您正將檔案複製到不支援加密的目的地」錯誤

透過網路複製檔案時，該檔案會在來源電腦上解密、以純文字傳送，並在目的地上重新加密。 不過，您可能會在嘗試複製加密的檔案時看到下列錯誤：「您正在將檔案複製到不支援加密的目的地。」

### <a name="cause"></a>原因
如果您使用加密檔案系統 (EFS)，可能會發生此問題。 BitLocker 加密的檔案可以複製到 Azure 檔案服務。 不過，Azure 檔案服務不支援 NTFS EFS。

### <a name="workaround"></a>因應措施
若要透過網路複製檔案，您必須先將它解密。 請使用下列其中一個方法：

- 使用 **copy /d** 命令。 它可讓加密的檔案另存為目的地上的解密檔案。
- 設定下列登錄機碼：
  - 路徑 = HKLM\Software\Policies\Microsoft\Windows\System
  - 數值類型 = DWORD
  - Name = CopyFileAllowDecryptedRemoteDestination
  - Value = 1

請注意，設定登錄機碼會影響所有對網路共用所做的複製作業。

## <a name="slow-enumeration-of-files-and-folders"></a>列舉檔案和資料夾的速度太慢

### <a name="cause"></a>原因

如果用戶端機器上沒有足夠的快取可供大型目錄使用時，就會發生此問題。

### <a name="solution"></a>解決方法

若要解決此問題，請調整 **DirectoryCacheEntrySizeMax** 登錄值，以允許在用戶端機器快取較大型的目錄清單：

- 位址：HKLM\System\CCS\Services\Lanmanworkstation\Parameters
- 值名稱：DirectoryCacheEntrySizeMax 
- 值類型：DWORD
 
 
例如，您可以將它設定為 0x100000，然後看看效能是否有變好。

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-domain-service-aad-ds-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>在為 Azure 檔啟用 Azure 活動目錄域服務 (AAD DS) 身份驗證時未找到錯誤 AadDstenant"無法查找具有租戶 ID ad-租戶 id 的活動租戶"

### <a name="cause"></a>原因

當您嘗試在儲存帳戶[上的 Azure 檔上啟用 Azure 檔上的 Azure 活動目錄域服務 (Azure AD DS) 身份驗證](storage-files-identity-auth-active-directory-domain-service-enable.md)時,將發生錯誤 AadDsTenantNotFound,該儲存帳戶上未在關聯訂閱的 AAD 租戶上創建[AAD 域服務 (AAD DS)。](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)  

### <a name="solution"></a>解決方法

在您儲存體帳戶部署所在訂用帳戶的 AAD 租用戶上啟用 AAD DS。 您必須有 AAD 租用戶的系統管理員權限，才能建立受控網域。 如果您不是 Azure AD 租用戶的系統管理員，請連絡系統管理員，並遵循[使用 Azure 入口網站啟用 Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) 的逐步指導。

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

## <a name="error-system-error-1359-has-occurred-an-internal-error-received-over-smb-access-to-file-shares-with-azure-active-directory-domain-service-aad-ds-authentication-enabled"></a>錯誤 '系統錯誤 1359 已發生。 以開啟 Azure 的目錄網域服務 (AAD DS) 身份驗證的 SMB 存取檔案分享而收到的內部錯誤

### <a name="cause"></a>原因

錯誤 '系統錯誤 1359 已發生。 當您嘗試透過啟用 AAD DS 身份驗證連接到檔案共享時,將發生內部錯誤,該身份驗證針對具有網域 DNS 名稱的 AAD DS 以數位字元開頭。 例如,如果您的 AAD DS 域 DNS 名稱是「1域」,則在嘗試使用 AAD 認證資料,則會嘗試使用 AAD 認證資料的資訊,將出現此錯誤。 

### <a name="solution"></a>解決方法

目前,您可以考慮使用適用於以下規則的新域 DNS 名稱重新部署 AAD DS:
- 名稱不能以數字字元開頭。
- 名稱必須長 3 到 63 個字元。

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員。
如果仍需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。
