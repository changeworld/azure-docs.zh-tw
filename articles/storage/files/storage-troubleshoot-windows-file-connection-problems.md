---
title: 針對 Windows 中的 Azure 檔案服務問題進行疑難排解 | Microsoft Docs
description: 針對 Windows 中的 Azure 檔案服務問題進行疑難排解
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 05/31/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 89a5fa0be104c3a7b7e035f82d2fed80d4781701
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85511981"
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>針對 Windows 中的 Azure 檔案服務問題進行疑難排解

本文列出當您從 Windows 用戶端連線時，與 Microsoft Azure 檔案服務相關的常見問題。 文中也會提供這些問題的可能原因和解決方案。 除了本文中的疑難排解步驟之外，您也可以使用[AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5)   來確保 Windows 用戶端環境具有正確的必要條件。 AzFileDiagnostics 會自動偵測本文中提及的大部分徵兆，並協助設定您的環境以取得最佳效能。 您也可以在 [Azure 檔案共用疑難排解員](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares)中找到此資訊，當中有提供步驟來協助您解決連線/對應/掛接 Azure 檔案共用的問題。

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

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>原因2：已在儲存體帳戶上啟用虛擬網路或防火牆規則 

如果在儲存體帳戶上設定虛擬網路 (VNET) 和防火牆規則，網路流量將會被拒絕存取，除非用戶端 IP 位址或虛擬網路獲准存取。

### <a name="solution-for-cause-2"></a>原因 2 的解決方案

確認已經在儲存體帳戶上正確設定虛擬網路和防火牆規則。 若要測試虛擬網路或防火牆規則是否造成問題，請暫時將儲存體帳戶上的設定變更為 [允許來自所有網路的存取]****。 若要深入了解，請參閱[設定 Azure 儲存體防火牆和虛擬網路](https://docs.microsoft.com/azure/storage/common/storage-network-security)。

### <a name="cause-3-share-level-permissions-are-incorrect-when-using-identity-based-authentication"></a>原因3：使用以身分識別為基礎的驗證時，共用層級的許可權不正確

如果使用者使用 Active Directory （AD）或 Azure Active Directory Domain Services （Azure AD DS）驗證來存取 Azure 檔案共用，則如果共用層級許可權不正確，檔案共用的存取將會失敗並出現「拒絕存取」錯誤。 

### <a name="solution-for-cause-3"></a>原因 3 的解決方案

若要更新共用層級許可權，請參閱[將存取權限指派](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-domain-service-enable#2-assign-access-permissions-to-an-identity)給身分識別。

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>當您嘗試掛接或取消掛接 Azure 檔案共用時發生錯誤 53、錯誤 67 或錯誤 87

當您嘗試從內部部署或不同資料中心掛接檔案共用時，可能會收到下列錯誤：

- 發生系統錯誤 53。 找不到網路路徑。
- 發生系統錯誤 67。 找不到網路名稱。
- 發生系統錯誤 87。 參數錯誤。

### <a name="cause-1-port-445-is-blocked"></a>原因1：已封鎖埠445

如果連接埠 445 至 Azure 檔案服務資料中心的輸出通訊遭到封鎖，可能會發生系統錯誤 53 或系統錯誤 67。 若要查看 ISP 是否允許從連接埠 445 進行存取的摘要，請參閱 [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) \(英文\)。

若要檢查您的防火牆或 ISP 是否封鎖連接埠 445，請使用 [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) 工具或 `Test-NetConnection` Cmdlet。 

若要使用 `Test-NetConnection` Cmdlet，必須安裝 Azure PowerShell 模組，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)以取得詳細資訊。 請記得以儲存體帳戶的相關名稱取代 `<your-storage-account-name>` 和 `<your-resource-group-name>`。

   
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
Azure 檔案同步可以將您的內部部署 Windows Server 轉換成 Azure 檔案共用的快速快取。 您可以使用 Windows Server 上可用的任何通訊協定來從本機存取資料，包括 SMB、NFS 和 FTPS。 Azure 檔案同步會透過連接埠 443 運作，因此可作為因應措施，從已封鎖連接埠 445 的用戶端存取 Azure 檔案儲存體。 [瞭解如何設定 Azure 檔案同步](https://docs.microsoft.com/azure/storage/files/storage-sync-files-extend-servers)。

#### <a name="solution-2---use-vpn"></a>解決方案 2 - 使用 VPN
藉由設定特定儲存體帳戶的 VPN，流量會通過安全通道，而不是透過網際網路。 遵循[設定 VPN 的指示](storage-files-configure-p2s-vpn-windows.md)，從 Windows 存取 Azure 檔案儲存體。

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>解決方案 3 - 在您 ISP/IT 系統管理員的協助下解除封鎖連接埠 445
與您的 IT 部門或 ISP 合作，開啟埠445輸出到[AZURE IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>解決方案 4 - 使用儲存體總管/Powershell 等 REST API 類型的工具
Azure 檔案儲存體也支援 SMB 以外的 REST。 REST 存取會透過連接埠 443 (標準 TCP) 運作。 有各種工具是使用 REST API 來撰寫，可帶來豐富的 UI 體驗。 [儲存體總管](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)是其中之一。 [下載並安裝儲存體總管](https://azure.microsoft.com/features/storage-explorer/)，並連線到 Azure 檔案儲存體所支援的檔案共用。 您也可以使用[PowerShell](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-powershell) ，也就是使用者 REST API。

### <a name="cause-2-ntlmv1-is-enabled"></a>原因2：已啟用 NTLMv1

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

### <a name="solution"></a>解決方案

關閉一些控制代碼以減少同時開啟的控制代碼數，然後再試一次。 如需詳細資訊，請參閱[Microsoft Azure 儲存體效能和擴充性檢查清單](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

若要查看檔案共用、目錄或檔案的開啟控制碼，請使用[AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) PowerShell Cmdlet。  

若要關閉檔案共用、目錄或檔案的開啟控制碼，請使用[AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) PowerShell Cmdlet。

> [!Note]  
> AzStorageFileHandle 和 AzStorageFileHandle Cmdlet 包含在 Az PowerShell 模組2.4 版或更新版本中。 若要安裝最新的 Az PowerShell 模組，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps)。

<a id="noaaccessfailureportal"></a>
## <a name="error-no-access-when-you-try-to-access-or-delete-an-azure-file-share"></a>當您嘗試存取或刪除 Azure 檔案共用時發生錯誤「沒有存取權」  
當您嘗試存取或刪除入口網站中的 Azure 檔案共用時，可能會收到下列錯誤：

無存取權  
錯誤碼：403 

### <a name="cause-1-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>原因1：已在儲存體帳戶上啟用虛擬網路或防火牆規則

### <a name="solution-for-cause-1"></a>原因 1 的解決方案

確認已經在儲存體帳戶上正確設定虛擬網路和防火牆規則。 若要測試虛擬網路或防火牆規則是否造成問題，請暫時將儲存體帳戶上的設定變更為 [允許來自所有網路的存取]****。 若要深入了解，請參閱[設定 Azure 儲存體防火牆和虛擬網路](https://docs.microsoft.com/azure/storage/common/storage-network-security)。

### <a name="cause-2-your-user-account-does-not-have-access-to-the-storage-account"></a>原因2：您的使用者帳戶沒有儲存體帳戶的存取權

### <a name="solution-for-cause-2"></a>原因 2 的解決方案

瀏覽至 Azure 檔案共用所在的儲存體帳戶，按一下 [存取控制 (IAM)]****，並確認您的使用者帳戶擁有儲存體帳戶的存取權。 若要深入了解，請參閱[如何使用角色型存取控制 (RBAC) 保護儲存體帳戶](https://docs.microsoft.com/azure/storage/blobs/security-recommendations#data-protection)。

<a id="open-handles"></a>
## <a name="unable-to-delete-a-file-or-directory-in-an-azure-file-share"></a>無法刪除 Azure 檔案共用中的檔案或目錄
當您嘗試刪除檔案時，可能會收到下列錯誤：

指定的資源已標示為可供 SMB 用戶端刪除。

### <a name="cause"></a>原因
如果檔案或目錄有開啟的控制碼，通常就會發生此問題。 

### <a name="solution"></a>解決方案

如果 SMB 用戶端已關閉所有開啟的控制碼，且問題持續發生，請執行下列動作：

- 使用[AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) PowerShell Cmdlet 來查看開啟的控制碼。

- 使用[AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) PowerShell Cmdlet 關閉開啟的控制碼。 

> [!Note]  
> AzStorageFileHandle 和 AzStorageFileHandle Cmdlet 包含在 Az PowerShell 模組2.4 版或更新版本中。 若要安裝最新的 Az PowerShell 模組，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps)。

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>從 Windows 中的 Azure 檔案服務複製檔案或將檔案複製到其中的速度變慢

當您嘗試將檔案傳輸到 Azure 檔案服務時，可能會看到效能變慢。

- 如果您沒有特定的 I/O 大小需求下限，建議您使用 1 MB 的 I/O 大小以獲得最佳效能。
-   如果您知道擴充寫入檔案的最終大小，而且當檔案上未寫入的結尾中有零時您的軟體不會產生相容性問題，則請事先設定檔案大小，而不是將每次寫入設為擴充寫入。
-   使用正確的複製方法：
    -   在兩個檔案共用之間進行任何傳輸時，請使用[AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) 。
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
## <a name="no-folder-with-a-drive-letter-in-my-computer-or-this-pc"></a>"我的電腦" 或 "這部 PC" 中沒有磁碟機號的資料夾

如果您以系統管理員身分使用 net use 對應 Azure 檔案共用，該共用似乎就會遺失。

### <a name="cause"></a>原因

根據預設，Windows 檔案總管不會以系統管理員身分執行。 如果您從系統管理命令提示字元執行 net use，就是以系統管理員身分對應網路磁碟機。 因為對應的磁碟機是以使用者為中心，如果磁碟機掛接在不同的使用者帳戶下，登入的使用者帳戶不會顯示此磁碟機。

### <a name="solution"></a>解決方案
從非系統管理員命令掛接共用。 或者，您可以遵循[此 TechNet 主題](https://technet.microsoft.com/library/ee844140.aspx)來設定**EnableLinkedConnections**登錄值。

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>如果儲存體帳戶包含斜線，net use 命令就會失敗

### <a name="cause"></a>原因

Net use 命令會將斜線 (/) 解譯為命令列選項。 如果您的使用者帳戶名稱開頭為斜線，磁碟機對應將會失敗。

### <a name="solution"></a>解決方案

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

### <a name="solution"></a>解決方案

使用下列其中一個解決方案：

-   從應用程式所屬的相同使用者帳戶掛接磁碟機。 您可以使用 PsExec 之類的工具。
- 在 net use 命令的使用者名稱和密碼參數中傳遞儲存體帳戶名稱和金鑰。
- 使用 cmdkey 命令以將認證新增至認證管理員。 請從服務帳戶內容底下的命令列執行這項操作，不論是透過互動式登入或使用 `runas` 。
  
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

### <a name="solution"></a>解決方案

若要解決此問題，請調整 **DirectoryCacheEntrySizeMax** 登錄值，以允許在用戶端機器快取較大型的目錄清單：

- 位址：HKLM\System\CCS\Services\Lanmanworkstation\Parameters
- 值名稱：DirectoryCacheEntrySizeMax 
- 值類型：DWORD
 
 
例如，您可以將它設定為 0x100000，然後看看效能是否有變好。

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-domain-service-aad-ds-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>啟用 Azure Active Directory 網域服務（AAD DS）驗證時發生錯誤，Azure 檔案儲存體「找不到具有租使用者識別碼 AAD 租使用者識別碼的作用中租使用者」

### <a name="cause"></a>原因

當您嘗試在未于相關聯訂用帳戶的 AAD 租使用者上建立[Aad 網域服務（AAD DS）](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)的儲存體帳戶[Azure 檔案儲存體上啟用 AZURE ACTIVE DIRECTORY DOMAIN SERVICES （Azure AD DS）驗證](storage-files-identity-auth-active-directory-domain-service-enable.md)時，就會發生錯誤 AadDsTenantNotFound。  

### <a name="solution"></a>解決方案

在您儲存體帳戶部署所在訂用帳戶的 AAD 租用戶上啟用 AAD DS。 您必須有 AAD 租用戶的系統管理員權限，才能建立受控網域。 如果您不是 Azure AD 租用戶的系統管理員，請連絡系統管理員，並遵循[使用 Azure 入口網站啟用 Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) 的逐步指導。

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

## <a name="error-system-error-1359-has-occurred-an-internal-error-received-over-smb-access-to-file-shares-with-azure-active-directory-domain-service-aad-ds-authentication-enabled"></a>錯誤「發生系統錯誤1359。 啟用 Azure Active Directory 網域服務（AAD DS）驗證的檔案共用上，收到透過 SMB 存取的內部錯誤

### <a name="cause"></a>原因

錯誤「發生系統錯誤1359。 當您嘗試使用以數位字元開頭的網域 DNS 名稱，針對 AAD DS 啟用 AAD DS 驗證來連線到檔案共用時，就會發生內部錯誤。 例如，如果您的 AAD DS 網域 DNS 名稱是 "1domain"，當您嘗試使用 AAD 認證掛接檔案共用時，就會收到此錯誤。 

### <a name="solution"></a>解決方案

目前，您可以考慮使用適用于下列規則的新網域 DNS 名稱來重新部署 AAD DS：
- 名稱的開頭不能是數位字元。
- 名稱的長度必須介於3到63個字元之間。

## <a name="unable-to-mount-azure-files-with-ad-credentials"></a>無法使用 AD 認證掛接 Azure 檔案儲存體 

### <a name="self-diagnostics-steps"></a>自我診斷步驟
首先，請確定您已遵循全部的四個步驟來[啟用 AZURE 檔案儲存體 AD 驗證](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-enable)。

第二，請嘗試[使用儲存體帳戶金鑰裝載 Azure 檔案共用](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows)。 如果您無法掛接，請下載[AzFileDiagnostics.ps1](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5)以協助您驗證用戶端執行環境、偵測不相容的用戶端設定，這會導致 Azure 檔案儲存體的存取失敗、提供自行修正的規範指引，以及收集診斷追蹤。

第三，您可以執行 AzStorageAccountAuth Cmdlet，在 AD 設定上使用登入的 AD 使用者進行一組基本的檢查。 [AzFilesHybrid v0.1.2+ 版本](https://github.com/Azure-Samples/azure-files-samples/releases)支援此 Cmdlet。 您必須以對目標儲存體帳戶具有擁有者權限的 AD 使用者身分執行此 Cmdlet。  
```PowerShell
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```
Cmdlet 會依序執行下列檢查，並提供失敗的指引：
1. CheckPort445Connectivity：檢查是否已針對 SMB 連線開啟埠445
2. CheckDomainJoined：驗證用戶端電腦是否已加入 AD 網域
3. CheckADObject：確認 Active Directory 中有代表儲存體帳戶的物件，而且具有正確的 SPN （服務主體名稱）。
4. CheckGetKerberosTicket：嘗試取得 Kerberos 票證以連接到儲存體帳戶 
5. CheckADObjectPasswordIsCorrect：確定在代表儲存體帳戶的 AD 身分識別上設定的密碼與儲存體帳戶 kerb1 或 kerb2 金鑰相符
6. CheckSidHasAadUser：檢查已登入的 AD 使用者是否已同步處理至 Azure AD。 如果您想要查看特定 AD 使用者是否同步處理到 Azure AD，您可以在輸入參數中指定-UserName 和-Domain。
7. CheckAadUserHasSid：檢查 Azure AD 的使用者在 AD 中是否有 SID，這項檢查需要使用者輸入具有參數-ObjectId 之 Azure AD 使用者的物件識別碼。 
8. CheckStorageAccountDomainJoined：檢查儲存體帳戶的屬性，以查看 AD 驗證是否已啟用，以及是否已填入帳戶的 AD 屬性。

## <a name="unable-to-configure-directoryfile-level-permissions-windows-acls-with-windows-file-explorer"></a>無法使用 Windows File Explorer 設定目錄/檔案層級許可權（Windows Acl）

### <a name="symptom"></a>徵狀

嘗試在已掛接的檔案共用上使用 File Explorer 設定 Windows Acl 時，您可能會遇到下列其中一個徵兆：
- 當您按一下 [安全性] 索引標籤下的 [編輯] 許可權之後，就不會載入許可權 wizard。 
- 當您嘗試選取新的使用者或群組時，網域位置不會顯示正確的 AD DS 網域。 

### <a name="solution"></a>解決方案

建議您使用[icacls 工具](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)，將目錄/檔案層級許可權設定為因應措施。 

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員。
如果仍需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。
