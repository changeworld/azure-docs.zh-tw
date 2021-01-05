---
title: 針對 Windows 中的 Azure 檔案服務問題進行疑難排解
description: 針對 Windows 中的 Azure 檔案儲存體問題進行疑難排解。 當您從 Windows 用戶端連線時，請參閱與 Azure 檔案儲存體相關的常見問題，並查看可能的解決方法。 僅適用于 SMB 共用
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/13/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: f2d55d1fcc92abdc629581d6e4d277ec0294dce0
ms.sourcegitcommit: 89c0482c16bfec316a79caa3667c256ee40b163f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "97858683"
---
# <a name="troubleshoot-azure-files-problems-in-windows-smb"></a>針對 Windows (SMB) 中的 Azure 檔案儲存體問題進行疑難排解

本文列出當您從 Windows 用戶端連線時，與 Microsoft Azure 檔案服務相關的常見問題。 文中也會提供這些問題的可能原因和解決方案。 除了本文中的疑難排解步驟，您也可以使用 [AzFileDiagnostics](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) \(英文\) 來確保 Windows 用戶端環境具備正確的必要條件。 AzFileDiagnostics 會自動偵測本文中提及的大部分徵兆，並協助設定您的環境以取得最佳效能。

> [!IMPORTANT]
> 本文內容僅適用于 SMB 共用。 如需 NFS 共用的詳細資訊，請參閱 [疑難排解 AZURE nfs 檔案共用](storage-troubleshooting-files-nfs.md)。

<a id="error5"></a>
## <a name="error-5-when-you-mount-an-azure-file-share"></a>掛接 Azure 檔案共用時發生錯誤 5

您嘗試掛接檔案共用時，可能會收到下列錯誤：

- 發生系統錯誤 5。 存取遭到拒絕。

### <a name="cause-1-unencrypted-communication-channel"></a>原因 1：通訊通道未加密

基於安全考量，如果通訊通道未加密，而且未從 Azure 檔案共用所在的相同資料中心進行連線嘗試，與 Azure 檔案共用的連線就會遭到封鎖。 如果儲存體帳戶上啟用[需要安全傳輸](../common/storage-require-secure-transfer.md)設定，則也可能會封鎖相同資料中心內未加密的連線。 唯有當使用者的用戶端 OS 支援 SMB 加密時，系統才會提供加密的通訊通道。

Windows 8、Windows Server 2012 和更新版本的每個系統交涉都要求包含支援加密的 SMB 3.0。

### <a name="solution-for-cause-1"></a>原因 1 的解決方案

1. 從支援 SMB 加密的用戶端 (Windows 8、Windows Server 2012 和更新版本) 進行連線，或從 Azure 檔案共用所使用的 Azure 儲存體帳戶相同的資料中心上的虛擬機器進行連線。
2. 如果用戶端不支援 SMB 加密，請確認儲存體帳戶上的[需要安全傳輸](../common/storage-require-secure-transfer.md)設定已經停用。

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>原因2：已在儲存體帳戶上啟用虛擬網路或防火牆規則 

如果在儲存體帳戶上設定虛擬網路 (VNET) 和防火牆規則，網路流量將會被拒絕存取，除非用戶端 IP 位址或虛擬網路獲准存取。

### <a name="solution-for-cause-2"></a>原因 2 的解決方案

確認已經在儲存體帳戶上正確設定虛擬網路和防火牆規則。 若要測試虛擬網路或防火牆規則是否造成問題，請暫時將儲存體帳戶上的設定變更為 [允許來自所有網路的存取]。 若要深入了解，請參閱[設定 Azure 儲存體防火牆和虛擬網路](../common/storage-network-security.md)。

### <a name="cause-3-share-level-permissions-are-incorrect-when-using-identity-based-authentication"></a>原因3：使用以身分識別為基礎的驗證時，共用層級許可權不正確

如果使用者使用 Active Directory (AD) 或 Azure Active Directory Domain Services (Azure AD DS) 驗證來存取 Azure 檔案共用，則如果共用層級許可權不正確，檔案共用的存取將會失敗並出現「拒絕存取」錯誤。 

### <a name="solution-for-cause-3"></a>原因 3 的解決方案

驗證是否已正確設定許可權：

- **Active Directory (AD)** 請參閱 [將共用層級許可權指派給身分識別](./storage-files-identity-ad-ds-assign-permissions.md)。

    針對已從 Active Directory (AD) 同步至 Azure Active Directory (使用 Azure AD) Azure AD Connect 的群組和使用者，支援共用層級的許可權指派。  確認指派共用層級許可權的群組和使用者，不支援「僅限雲端」群組。
- **Azure Active Directory Domain Services (AZURE AD DS)** 請參閱 [將存取權限指派給身分識別](./storage-files-identity-auth-active-directory-domain-service-enable.md?tabs=azure-portal#assign-access-permissions-to-an-identity)。

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>當您嘗試掛接或取消掛接 Azure 檔案共用時發生錯誤 53、錯誤 67 或錯誤 87

當您嘗試從內部部署或不同資料中心掛接檔案共用時，可能會收到下列錯誤：

- 發生系統錯誤 53。 找不到網路路徑。
- 發生系統錯誤 67。 找不到網路名稱。
- 發生系統錯誤 87。 參數錯誤。

### <a name="cause-1-port-445-is-blocked"></a>原因1：已封鎖埠445

如果連接埠 445 至 Azure 檔案服務資料中心的輸出通訊遭到封鎖，可能會發生系統錯誤 53 或系統錯誤 67。 若要查看 ISP 是否允許從連接埠 445 進行存取的摘要，請參閱 [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) \(英文\)。

若要檢查您的防火牆或 ISP 是否封鎖連接埠 445，請使用 [AzFileDiagnostics](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) 工具或 `Test-NetConnection` Cmdlet。 

若要使用 `Test-NetConnection` Cmdlet，必須安裝 Azure PowerShell 模組，請參閱 [安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps) 以取得詳細資訊。 請記得以儲存體帳戶的相關名稱取代 `<your-storage-account-name>` 和 `<your-resource-group-name>`。

   
```azurepowershell
$resourceGroupName = "<your-resource-group-name>"
$storageAccountName = "<your-storage-account-name>"

# This command requires you to be logged into your Azure account, run Login-AzAccount if you haven't
# already logged in.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName

# The ComputerName, or host, is <storage-account>.file.core.windows.net for Azure Public Regions.
# $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign clouds
# or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
Test-NetConnection -ComputerName ([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) -Port 445
```
    
如果連線成功，您應會看見下列輸出：
    
  
```azurepowershell
ComputerName     : <your-storage-account-name>
RemoteAddress    : <storage-account-ip-address>
RemotePort       : 445
InterfaceAlias   : <your-network-interface>
SourceAddress    : <your-ip-address>
TcpTestSucceeded : True
```
 

> [!Note]  
> 上述命令會傳回儲存體帳戶的目前 IP 位址。 此 IP 位址不保證會維持不變，而且可能隨時變更。 請勿將此 IP 位址硬式編碼到任何指令碼，或防火牆組態中。

### <a name="solution-for-cause-1"></a>原因 1 的解決方案

#### <a name="solution-1---use-azure-file-sync"></a>解決方案 1 - 使用 Azure 檔案同步
Azure 檔案同步可以將您的內部部署 Windows Server 轉換成 Azure 檔案共用的快速快取。 您可以使用 Windows Server 上可用的任何通訊協定來從本機存取資料，包括 SMB、NFS 和 FTPS。 Azure 檔案同步會透過連接埠 443 運作，因此可作為因應措施，從已封鎖連接埠 445 的用戶端存取 Azure 檔案儲存體。 [瞭解如何設定 Azure 檔案同步](./storage-sync-files-extend-servers.md)。

#### <a name="solution-2---use-vpn"></a>解決方案 2 - 使用 VPN
藉由設定特定儲存體帳戶的 VPN，流量會透過安全通道進行，而不是透過網際網路。 遵循[設定 VPN 的指示](storage-files-configure-p2s-vpn-windows.md)，從 Windows 存取 Azure 檔案儲存體。

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>解決方案 3 - 在您 ISP/IT 系統管理員的協助下解除封鎖連接埠 445
與您的 IT 部門或 ISP 合作，以開啟埠445輸出至 [AZURE IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>解決方案 4 - 使用儲存體總管/Powershell 等 REST API 類型的工具
除了 SMB 之外，Azure 檔案儲存體也支援 REST。 REST 存取會透過連接埠 443 (標準 TCP) 運作。 有各種工具是使用 REST API 來撰寫，可帶來豐富的 UI 體驗。 [儲存體總管](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows) 是其中之一。 [下載並安裝儲存體總管](https://azure.microsoft.com/features/storage-explorer/)，並連線到 Azure 檔案儲存體所支援的檔案共用。 您也可以使用 [PowerShell](./storage-how-to-use-files-powershell.md) ，也就是使用者 REST API。

### <a name="cause-2-ntlmv1-is-enabled"></a>原因2： NTLMv1 已啟用

如果用戶端上已啟用 NTLMv1 通訊，就會發生系統錯誤 53 或系統錯誤 87。 Azure 檔案僅支援 NTLMv2 驗證。 啟用 NTLMv1 會使用戶端變得較不安全。 因此，Azure 檔案服務會封鎖通訊。 

若要判斷這是否為錯誤的原因，請確認已將下列登錄子機碼的值設為 3：

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

如需詳細資訊，請參閱 TechNet 上的 [LmCompatibilityLevel](/previous-versions/windows/it-pro/windows-2000-server/cc960646(v=technet.10)) 主題。

### <a name="solution-for-cause-2"></a>原因 2 的解決方案

在下列登錄子機碼中，將 **LmCompatibilityLevel** 值還原為預設值 3：

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816---not-enough-quota-is-available-to-process-this-command"></a>錯誤 1816-沒有足夠的配額可用來處理此命令

### <a name="cause"></a>原因

當您達到 Azure 檔案共用上的檔案或目錄允許的並行開啟控制碼上限時，就會發生錯誤1816。 如需詳細資訊，請參閱 [Azure 檔案服務擴展目標](./storage-files-scale-targets.md#azure-files-scale-targets)。

### <a name="solution"></a>解決方案

關閉一些控制代碼以減少同時開啟的控制代碼數，然後再試一次。 如需詳細資訊，請參閱 [Microsoft Azure 儲存體效能和擴充性檢查清單](../blobs/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

若要查看檔案共用、目錄或檔案的開啟控制碼，請使用 [>get-azstoragefilehandle](/powershell/module/az.storage/get-azstoragefilehandle) PowerShell Cmdlet。  

若要關閉檔案共用、目錄或檔案的開啟控制碼，請使用 [>get-azstoragefilehandle](/powershell/module/az.storage/close-azstoragefilehandle) PowerShell Cmdlet。

> [!Note]  
> Get-AzStorageFileHandle 和 Close-AzStorageFileHandle Cmdlet 包含在 Az PowerShell 模組2.4 版或更新版本中。 若要安裝最新的 Az PowerShell 模組，請參閱 [安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。

<a id="noaaccessfailureportal"></a>
## <a name="error-no-access-when-you-try-to-access-or-delete-an-azure-file-share"></a>當您嘗試存取或刪除 Azure 檔案共用時發生「無存取」錯誤  
當您嘗試在入口網站中存取或刪除 Azure 檔案共用時，可能會收到下列錯誤：

無存取權  
錯誤碼：403 

### <a name="cause-1-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>原因1：已在儲存體帳戶上啟用虛擬網路或防火牆規則

### <a name="solution-for-cause-1"></a>原因 1 的解決方案

確認已經在儲存體帳戶上正確設定虛擬網路和防火牆規則。 若要測試虛擬網路或防火牆規則是否造成問題，請暫時將儲存體帳戶上的設定變更為 [允許來自所有網路的存取]。 若要深入了解，請參閱[設定 Azure 儲存體防火牆和虛擬網路](../common/storage-network-security.md)。

### <a name="cause-2-your-user-account-does-not-have-access-to-the-storage-account"></a>原因2：您的使用者帳戶沒有儲存體帳戶的存取權

### <a name="solution-for-cause-2"></a>原因 2 的解決方案

瀏覽至 Azure 檔案共用所在的儲存體帳戶，按一下 [存取控制 (IAM)]，並確認您的使用者帳戶擁有儲存體帳戶的存取權。 若要深入瞭解，請參閱 [如何使用 azure 角色型存取控制來保護儲存體帳戶 (AZURE RBAC) ](../blobs/security-recommendations.md#data-protection)。

<a id="open-handles"></a>
## <a name="unable-to-modify-moverename-or-delete-a-file-or-directory"></a>無法修改、移動/重新命名或刪除檔案或目錄
檔案共用的主要用途之一，就是多個使用者和應用程式可以同時與共享中的檔案和目錄互動。 為了協助進行此互動，檔案共用提供數種方式來調節檔案和目錄的存取權。

當您透過 SMB 從掛接的 Azure 檔案共用開啟檔案時，您的應用程式/作業系統要求檔案控制代碼，也就是檔案的參考。 此外，您的應用程式會在要求檔案控制代碼時指定檔案共用模式，以指定 Azure 檔案儲存體強制執行之檔案的存取層級獨佔性： 

- `None`：您有獨佔存取權。 
- `Read`：當您開啟檔案時，其他人可能會讀取該檔案。
- `Write`：當您開啟檔案時，其他人可能會寫入檔案。 
- `ReadWrite`： `Read` 和 `Write` 共用模式的組合。
- `Delete`：其他人可能會在您開啟檔案時將其刪除。 

雖然這是無狀態的通訊協定，但 FileREST 通訊協定並沒有檔案控制代碼的概念，但它提供了類似的機制，可協調您的腳本、應用程式或服務可能會使用的檔案和資料夾的存取：檔案租用。 當檔案被租用時，它會被視為相當於檔案共用模式的檔案控制代碼 `None` 。 

雖然檔案控制代碼和租用有很重要的用途，有時檔案控制代碼和租用可能是孤立的。 發生這種情況時，可能會導致修改或刪除檔案時發生問題。 您可能會看到類似下列的錯誤訊息：

- 由於已有另一個處理序正在使用該檔案，所以此處理序無法存取該檔案。
- 因為檔案已在另一個程式中開啟，所以無法完成此動作。
- 另一位使用者已鎖定檔以進行編輯。
- 指定的資源已標示為可供 SMB 用戶端刪除。

此問題的解決方式取決於是否由孤立的檔案控制代碼或租用所造成。 

### <a name="cause-1"></a>原因 1
檔案控制代碼防止修改或刪除檔案/目錄。 您可以使用 [>get-azstoragefilehandle](/powershell/module/az.storage/get-azstoragefilehandle) PowerShell Cmdlet 來查看開啟的控制碼。 

如果所有 SMB 用戶端已關閉其檔案/目錄的開啟控制碼，且問題持續發生，您可以強制關閉檔案控制代碼。

### <a name="solution-1"></a>解決方案 1
若要強制關閉檔案控制代碼，請使用 [>get-azstoragefilehandle](/powershell/module/az.storage/close-azstoragefilehandle) PowerShell Cmdlet。 

> [!Note]  
> Get-AzStorageFileHandle 和 Close-AzStorageFileHandle Cmdlet 包含在 Az PowerShell 模組2.4 版或更新版本中。 若要安裝最新的 Az PowerShell 模組，請參閱 [安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。

### <a name="cause-2"></a>原因 2
檔案租用防止修改或刪除檔案。 您可以使用下列 PowerShell 來檢查檔案是否有檔案租用，並將、、和取代為 `<resource-group>` `<storage-account>` `<file-share>` `<path-to-file>` 您環境的適當值：

```PowerShell
# Set variables 
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"
$fileShareName = "<file-share>"
$fileForLease = "<path-to-file>"

# Get reference to storage account
$storageAccount = Get-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageAccountName

# Get reference to file
$file = Get-AzStorageFile `
        -Context $storageAccount.Context `
        -ShareName $fileShareName `
        -Path $fileForLease

$fileClient = $file.ShareFileClient

# Check if the file has a file lease
$fileClient.GetProperties().Value
```

如果檔案有租用，則傳回的物件應該包含下列屬性：

```Output
LeaseDuration         : Infinite
LeaseState            : Leased
LeaseStatus           : Locked
```

### <a name="solution-2"></a>解決方案 2
若要從檔案中移除租用，您可以釋放租用或中斷租用。 若要釋放租用期，您需要租用的 LeaseId，這是您在建立租用時所設定的租用。 您不需要 LeaseId 來中斷租用。

下列範例會示範如何中斷原因2中指出的檔案租用 (此範例會繼續來自原因 2) 的 PowerShell 變數：

```PowerShell
$leaseClient = [Azure.Storage.Files.Shares.Specialized.ShareLeaseClient]::new($fileClient)
$leaseClient.Break() | Out-Null
```

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>從 Windows 中的 Azure 檔案服務複製檔案或將檔案複製到其中的速度變慢

當您嘗試將檔案傳輸到 Azure 檔案服務時，可能會看到效能變慢。

- 如果您沒有特定的 I/O 大小需求下限，建議您使用 1 MB 的 I/O 大小以獲得最佳效能。
-   如果您知道擴充寫入檔案的最終大小，而且當檔案上未寫入的結尾中有零時您的軟體不會產生相容性問題，則請事先設定檔案大小，而不是將每次寫入設為擴充寫入。
-   使用正確的複製方法：
    -   針對兩個檔案共用之間的任何傳輸使用 [AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) 。
    -   在內部部署電腦上的檔案共用之間，使用 [Robocopy](./storage-files-deployment-guide.md#robocopy) \(英文\)。

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>針對 Windows 8.1 或 Windows Server 2012 R2 的考量

若用戶端執行 Windows 8.1 或 Windows Server 2012 R2，請確定已安裝 [KB3114025](https://support.microsoft.com/help/3114025) Hotfix。 此 Hotfix 可改善建立和關閉控制代碼的效能。

您可以執行下列指令碼來檢查是否已安裝此 Hotfix：

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

如果已安裝 hotfix，則會顯示下列輸出︰

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> 自 2015 年 12 月起，Azure Marketplace 中的 Windows Server 2012 R2 映像預設已安裝 Hotfix KB3114025。

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer-or-this-pc"></a>在「我的電腦」或「這部電腦」中沒有磁碟機號的資料夾

如果您以系統管理員身分使用 net use 對應 Azure 檔案共用，該共用似乎就會遺失。

### <a name="cause"></a>原因

根據預設，Windows 檔案總管不會以系統管理員身分執行。 如果您從系統管理命令提示字元執行 net use，就是以系統管理員身分對應網路磁碟機。 因為對應的磁碟機是以使用者為中心，如果磁碟機掛接在不同的使用者帳戶下，登入的使用者帳戶不會顯示此磁碟機。

### <a name="solution"></a>解決方案
從非系統管理員命令掛接共用。 或者，您也可以遵循 [本 TechNet 主題](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee844140(v=ws.10)) 來設定 **EnableLinkedConnections** 登錄值。

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
- 使用 cmdkey 命令以將認證新增至認證管理員。 從服務帳戶內容底下的命令列執行這項操作，不論是透過互動式登入或使用 `runas` 。
  
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

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-domain-service-azure-ad-ds-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>啟用 Azure Active Directory 網域服務時，發生錯誤 AadDsTenantNotFound (Azure AD DS) 驗證 Azure 檔案儲存體「找不到租使用者識別碼為 aad-租使用者識別碼的作用中租使用者」

### <a name="cause"></a>原因

當您嘗試在未于相關聯訂用帳戶的 Azure 檔案儲存體租使用者上建立[Azure AD 網域服務 (AZURE AD ds) ](../../active-directory-domain-services/overview.md)的儲存體帳戶上，于[Azure AD 上啟用 AZURE ACTIVE DIRECTORY DOMAIN SERVICES (Azure AD ds) 驗證](storage-files-identity-auth-active-directory-domain-service-enable.md)時，就會發生錯誤 AadDsTenantNotFound。  

### <a name="solution"></a>解決方案

在您的儲存體帳戶部署所在訂用帳戶的 Azure AD 租使用者上，啟用 Azure AD DS。 您需要 Azure AD 租使用者的系統管理員許可權，才能建立受控網域。 如果您不是 Azure AD 租使用者的系統管理員，請洽詢系統管理員，並遵循逐步指引來 [建立和設定 Azure Active Directory Domain Services 受控網域](../../active-directory-domain-services/tutorial-create-instance.md)。

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

## <a name="unable-to-mount-azure-files-with-ad-credentials"></a>無法使用 AD 認證掛接 Azure 檔案儲存體 

### <a name="self-diagnostics-steps"></a>自我診斷步驟
首先，請確定您已遵循全部四個步驟來 [啟用 AZURE 檔案儲存體 AD 驗證](./storage-files-identity-auth-active-directory-enable.md)。

其次，請嘗試 [使用儲存體帳戶金鑰來裝載 Azure 檔案共用](./storage-how-to-use-files-windows.md)。 如果您無法掛接，請下載 [AzFileDiagnostics.ps1](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) 以協助您驗證用戶端執行環境、偵測不相容的用戶端設定，這會造成 Azure 檔案儲存體的存取失敗、提供自行修正的規範指引，以及收集診斷追蹤。

第三，您可以執行 Debug-AzStorageAccountAuth Cmdlet，以登入的 AD 使用者進行 AD 設定的一組基本檢查。 [AzFilesHybrid v0.1.2+ 版本](https://github.com/Azure-Samples/azure-files-samples/releases)支援此 Cmdlet。 您必須以對目標儲存體帳戶具有擁有者權限的 AD 使用者身分執行此 Cmdlet。  
```PowerShell
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```
此 Cmdlet 會依序執行以下檢查，並提供失敗的指引：
1. CheckADObjectPasswordIsCorrect：確定在代表儲存體帳戶的 AD 身分識別上設定的密碼，與儲存體帳戶 kerb1 或 kerb2 金鑰的設定相符。 如果密碼不正確，您可以執行 [更新 AzStorageAccountADObjectPassword](./storage-files-identity-ad-ds-update-password.md) 以重設密碼。 
2. CheckADObject：確認 Active Directory 中的物件代表儲存體帳戶，且具有正確的 SPN (服務主體名稱) 。 如果 SPN 未正確設定，請執行 debug Cmdlet 中傳回的 Set-AD Cmdlet 來設定 SPN。
3. CheckDomainJoined：驗證用戶端電腦是否已加入 AD 網域。 如果您的電腦未加入 AD 的網域，請參閱這 [篇文章](/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain) 以瞭解加入網域的指示。
4. CheckPort445Connectivity：確認已針對 SMB 連線開啟埠445。 如果所需的埠未開啟，請參閱疑難排解工具 [AzFileDiagnostics.ps1](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) ，以瞭解 Azure 檔案儲存體的連線能力問題。
5. CheckSidHasAadUser：檢查登入的 AD 使用者是否已同步處理至 Azure AD。 如果您想要查閱特定 AD 使用者是否同步處理至 Azure AD，您可以在輸入參數中指定-UserName 和-Domain。 
6. CheckGetKerberosTicket：嘗試取得 Kerberos 票證以連接至儲存體帳戶。 如果沒有有效的 Kerberos 權杖，請執行 klist 取得 cifs/儲存體---------net.tcp Cmdlet，並檢查錯誤碼的根本原因，使票證抓取失敗。
7. CheckStorageAccountDomainJoined：檢查是否已啟用 AD 驗證，並已填入帳戶的 AD 屬性。 如果沒有，請參閱 [此處](./storage-files-identity-ad-ds-enable.md) 的指示，在 Azure 檔案儲存體上啟用 AD DS 驗證。 
8. CheckUserRbacAssignment：檢查 AD 使用者是否具有適當的 RBAC 角色指派，以提供共用層級許可權來存取 Azure 檔案儲存體。 如果沒有，請參閱 [此處](https://docs.microsoft.com/azure/storage/files/storage-files-identity-ad-ds-assign-permissions) 的指示來設定共用層級許可權。 AzFilesHybrid v 0.2.3 + version) 支援 (
9. CheckUserFileAccess：檢查 AD 使用者是否具有適當的目錄/檔案許可權 (Windows Acl) 存取 Azure 檔案儲存體。 如果沒有，請參閱 [此處](https://docs.microsoft.com/azure/storage/files/storage-files-identity-ad-ds-configure-permissions) 的指示，以設定目錄/檔案層級許可權。 AzFilesHybrid v 0.2.3 + version) 支援 (

## <a name="unable-to-configure-directoryfile-level-permissions-windows-acls-with-windows-file-explorer"></a>無法設定目錄/檔案層級許可權 (windows Acl) Windows 檔案總管

### <a name="symptom"></a>徵狀

當您嘗試在已掛接的檔案共用上使用檔案總管設定 Windows Acl 時，可能會遇到下列徵兆所述的徵兆：
- 按一下 [安全性] 索引標籤下的 [編輯] 許可權之後，就不會載入許可權嚮導。 
- 當您嘗試選取新的使用者或群組時，網域位置不會顯示正確的 AD DS 網域。 

### <a name="solution"></a>解決方案

建議您使用 [icacls 工具](/windows-server/administration/windows-commands/icacls) 來設定目錄/檔案層級許可權，以作為因應措施。 

## <a name="errors-when-running-join-azstorageaccountforauth-cmdlet"></a>執行 Join-AzStorageAccountForAuth Cmdlet 時發生錯誤

### <a name="error-the-directory-service-was-unable-to-allocate-a-relative-identifier"></a>錯誤：「目錄服務無法配置相對識別碼」

如果持有 RID 主機 FSMO 角色的網域控制站無法使用，或已從網域中移除，並從備份還原，就可能發生此錯誤。  確認所有網域控制站都在執行且可用。

### <a name="error-cannot-bind-positional-parameters-because-no-names-were-given"></a>錯誤： 「無法繫結位置參數，因為未指定任何名稱」

此錯誤最有可能是 Join-AzStorageAccountforAuth 命令中的語法錯誤所觸發。  檢查命令是否有拼寫錯誤或語法錯誤，並確認已安裝最新版的 AzFilesHybrid 模組 (https://github.com/Azure-Samples/azure-files-samples/releases) 。  

## <a name="azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption"></a>AES 256 Kerberos 加密 Azure 檔案儲存體內部部署 AD DS 驗證支援

我們為使用 [AzFilesHybrid module v 0.2.2](https://github.com/Azure-Samples/azure-files-samples/releases)的 Azure 檔案儲存體內部內部部署 AD DS 驗證引進了 AES 256 Kerberos 加密支援。 如果您已使用低於 v 0.2.2 的模組版本啟用 AD DS authentication，您將需要下載最新的 AzFilesHybrid 模組 (v 0.2.2 +) 並執行下列 PowerShell。 如果您尚未在您的儲存體帳戶上啟用 AD DS 驗證，則可以遵循本 [指南](./storage-files-identity-ad-ds-enable.md#option-one-recommended-use-azfileshybrid-powershell-module) 來啟用。 

```PowerShell
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

Update-AzStorageAccountAuthForAES256 -ResourceGroupName $ResourceGroupName -StorageAccountName $StorageAccountName
```


## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員。
如果仍需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。
