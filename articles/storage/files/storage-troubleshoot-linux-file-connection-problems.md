---
title: 針對 Linux 中的 Azure 檔案服務問題進行疑難排解 | Microsoft Docs
description: 針對 Linux 中的 Azure 檔案服務問題進行疑難排解
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 95e220102cba290664a32cb6bbebef881ae4ffde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159484"
---
# <a name="troubleshoot-azure-files-problems-in-linux"></a>針對 Linux 中的 Azure 檔案服務問題進行疑難排解

本文會列出當您從 Linux 用戶端連線時，與 Azure 檔案服務相關的常見問題。 文中也會提供這些問題的可能原因和解決方案。 

除了本文中的疑難排解步驟外，您還可以使用 [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089) 來確保 Windows 用戶端環境具備正確的必要條件。 AzFileDiagnostics 會自動偵測本文提及的大部分徵兆。 它可協助設定您的環境，以取得最佳效能。 您也可以在 [Azure 檔案共用疑難排解員](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares)中找到這項資訊。 疑難排解員提供的步驟有助您處理連接、對應以及掛接 Azure 檔案共用的問題。

## <a name="cannot-connect-to-or-mount-an-azure-file-share"></a>無法連線或裝載 Azure 檔案共用

### <a name="cause"></a>原因

此問題的常見原因為：

- 您使用的是不相容的 Linux 散發套件用戶端。 建議您使用下列的 Linux 散發套件來連線到 Azure 檔案共用：

|   | SMB 2.1 <br>(掛接在相同 Azure 區域內的 VM 上) | SMB 3.0 <br>(從內部部署環境和跨區域掛接) |
| --- | :---: | :---: |
| Ubuntu Server | 14.04+ | 16.04+ |
| RHEL | 7+ | 7.5+ |
| CentOS | 7+ |  7.5+ |
| Debian | 8+ |   |
| openSUSE | 13.2+ | 42.3+ |
| SUSE Linux Enterprise Server | 12 | 12 SP3+ |

- 用戶端上未安裝 CIFS 實用程式（cifs-utils）。
- 用戶端上未安裝 SMB/CIFS 的最低版本 (2.1 版)。
- 用戶端不支援 SMB 3.0 加密。 上表提供了支援使用加密從本地和跨區域安裝的 Linux 發行版本的清單。 其他散發套件需要核心 4.11 和更新版本。
- 您嘗試透過 TCP 通訊埠 445 連線到儲存體帳戶，但目前並不支援。
- 您嘗試從 Azure VM 連線到 Azure 檔案共用，而該 VM 與儲存體帳戶位於不同的區域。
- 如果儲存體帳戶上已啟用 [需要安全傳輸]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer)設定，則 Azure 檔案服務僅允許使用 SMB 3.0 加密的連線。

### <a name="solution"></a>解決方法

若要解決此問題，請使用[適用於 Linux 上 Azure 檔案服務掛接錯誤的疑難排解工具](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089)。 這項工具可以：

* 協助您驗證用戶端執行環境。
* 偵測可能造成 Azure 檔案服務存取錯誤的不相容用戶端設定。
* 提供自行修正的規範指引。
* 收集診斷追蹤。

<a id="mounterror13"></a>
## <a name="mount-error13-permission-denied-when-you-mount-an-azure-file-share"></a>裝載 Azure 檔共用時"裝載錯誤（13）：許可權被拒絕"

### <a name="cause-1-unencrypted-communication-channel"></a>原因 1：通訊通道未加密

基於安全考量，如果通訊通道未加密，而且未從 Azure 檔案共用所在的相同資料中心進行連線嘗試，與 Azure 檔案共用的連線就會遭到封鎖。 如果儲存體帳戶上啟用[需要安全傳輸](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer)設定，則也可能會封鎖相同資料中心內未加密的連線。 唯有當使用者的用戶端 OS 支援 SMB 加密時，系統才會提供加密的通訊通道。

若要深入了解，請參閱[以 Linux 和 cifs-utils 套件掛接 Azure 檔案共用的必要條件](storage-how-to-use-files-linux.md#prerequisites)。 

### <a name="solution-for-cause-1"></a>原因 1 的解決方案

1. 從支援 SMB 加密的用戶端，或從與用於 Azure 檔案共用的 Azure 儲存體帳戶相同資料中心中的虛擬機器進行連線。
2. 如果用戶端不支援 SMB 加密，請確認儲存體帳戶上的[需要安全傳輸](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer)設定已經停用。

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>原因 2：在存儲帳戶上啟用虛擬網路或防火牆規則 

如果在儲存體帳戶上設定虛擬網路 (VNET) 和防火牆規則，網路流量將會被拒絕存取，除非用戶端 IP 位址或虛擬網路獲准存取。

### <a name="solution-for-cause-2"></a>原因 2 的解決方案

確認已經在儲存體帳戶上正確設定虛擬網路和防火牆規則。 若要測試虛擬網路或防火牆規則是否造成問題，請暫時將儲存體帳戶上的設定變更為 [允許來自所有網路的存取]****。 若要深入了解，請參閱[設定 Azure 儲存體防火牆和虛擬網路](https://docs.microsoft.com/azure/storage/common/storage-network-security)。

<a id="permissiondenied"></a>
## <a name="permission-denied-disk-quota-exceeded-when-you-try-to-open-a-file"></a>當您嘗試開啟檔案時，「[使用權限被拒] 超出磁碟配額」

在 Linux 中，您會收到類似以下的錯誤訊息︰

**\<檔案名> [許可權被拒絕] 磁片配額超過**

### <a name="cause"></a>原因

您已達到檔案所允許的同時開啟控點上限。

單一檔案的開啟控制代碼配額為 2,000 個。 當您擁有 2,000 個開啟控制代碼時，會顯示一則錯誤訊息以指出已達到配額。

### <a name="solution"></a>解決方法

關閉一些控點以減少同時開啟的控點數，然後再次嘗試操作。

要查看檔共用、目錄或檔的打開控制碼，請使用[獲取-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) PowerShell Cmdlet。  

要關閉檔共用、目錄或檔的打開控制碼，請使用[Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) PowerShell Cmdlet。

> [!Note]  
> 獲取-AzStorageFileHandle 和關閉 AzStorageFileHandle Cmdlet 包含在 Az PowerShell 模組版本 2.4 或更高版本中。 要安裝最新的 Az PowerShell 模組，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps)。

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>從 Linux 中的 Azure 檔案服務複製檔案或將檔案複製到其中的速度變慢

- 如果您沒有特定的 I/O 大小需求下限，建議您使用 1 MB 的 I/O 大小以獲得最佳效能。
- 使用正確的複製方法：
    - 在兩個檔共用之間進行任何傳輸，請使用[AzCopy。](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
    - 使用並行 cp 或 dd 可以提高複製速度，執行緒數取決於您的用例和工作負載。 以下示例使用六個示例： 
    - cp 示例（cp 將使用檔案系統的預設塊大小作為塊大小）： `find * -type f | parallel --will-cite -j 6 cp {} /mntpremium/ &`。
    - dd 示例（此命令顯式將塊大小設置為 1 MiB）：`find * -type f | parallel --will-cite-j 6 dd if={} of=/mnt/share/{} bs=1M`
    - 開源協力廠商工具，例如：
        - [GNU 並行](https://www.gnu.org/software/parallel/)。
        - [Fpart](https://github.com/martymac/fpart) - 對檔進行排序，並將它們打包到分區中。
        - [Fpsync](https://github.com/martymac/fpart/blob/master/tools/fpsync) - 使用 Fpart 和複製工具生成多個實例將資料從src_dir遷移到dst_url。
        - 基於 GNU 內核的[多](https://github.com/pkolano/mutil)執行緒 cp 和 md5sum.
- 提前設置檔案大小，而不是使每次寫入都擴展寫入，這有助於在已知檔案大小的情況下提高複製速度。 如果需要避免擴展寫入，可以使用`truncate - size <size><file>`命令設置目的檔案大小。 之後，`dd if=<source> of=<target> bs=1M conv=notrunc`命令將複製原始檔案，而無需重複更新目的檔案的大小。 例如，您可以為要複製的每個檔設置目的檔案大小（假設共用已安裝在 /mnt/share 下）：
    - `$ for i in `` find * -type f``; do truncate --size ``stat -c%s $i`` /mnt/share/$i; done`
    - 然後 - 複製檔而不並行擴展寫入：`$find * -type f | parallel -j6 dd if={} of =/mnt/share/{} bs=1M conv=notrunc`

<a id="error115"></a>
## <a name="mount-error115-operation-now-in-progress-when-you-mount-azure-files-by-using-smb-30"></a>當您使用 SMB 3.0 掛接 Azure 檔案服務時，發生「掛接錯誤 (115)：作業進行中」

### <a name="cause"></a>原因

部分 Linux 散發套件尚未支援 SMB 3.0 中的加密功能。 如果使用者嘗試使用 SMB 3.0 來掛接 Azure 檔案，可能會因缺少功能而收到「115」錯誤訊息。 目前僅有使用 Ubuntu 16.04 或更新版本時才支援 SMB 3.0 與完整加密。

### <a name="solution"></a>解決方法

Linux 4.11 核心已推出 SMB 3.0 適用的加密功能。 此功能讓您可從內部部署或不同 Azure 區域的 Azure 檔案共用進行掛接。 某些 Linux 發行版本可能具有從 4.11 內核到舊版本的 Linux 內核的更改，它們維護這些更改。 為了説明確定 Linux 版本是否支援使用加密的 SMB 3.0，請參閱[使用 Azure 檔進行 Linux](storage-how-to-use-files-linux.md)。 

如果您的 Linux SMB 用戶端不支援加密，請從位於檔案共用相同資料中心的 Azure Linux VM 使用 SMB 2.1 來掛接 Azure 檔案服務。 驗證儲存體帳戶上已停用[需要安全傳輸]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer)設定。 

<a id="noaaccessfailureportal"></a>
## <a name="error-no-access-when-you-try-to-access-or-delete-an-azure-file-share"></a>嘗試訪問或刪除 Azure 檔共用時出錯"無存取權限"  
當您嘗試訪問或刪除門戶中的 Azure 檔共用時，可能會收到以下錯誤：

沒有存取權  
錯誤代碼： 403 

### <a name="cause-1-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>原因 1：在存儲帳戶上啟用虛擬網路或防火牆規則

### <a name="solution-for-cause-1"></a>原因 1 的解決方案

確認已經在儲存體帳戶上正確設定虛擬網路和防火牆規則。 若要測試虛擬網路或防火牆規則是否造成問題，請暫時將儲存體帳戶上的設定變更為 [允許來自所有網路的存取]****。 若要深入了解，請參閱[設定 Azure 儲存體防火牆和虛擬網路](https://docs.microsoft.com/azure/storage/common/storage-network-security)。

### <a name="cause-2-your-user-account-does-not-have-access-to-the-storage-account"></a>原因 2：您的使用者帳戶無法訪問存儲帳戶

### <a name="solution-for-cause-2"></a>原因 2 的解決方案

瀏覽至 Azure 檔案共用所在的儲存體帳戶，按一下 [存取控制 (IAM)]****，並確認您的使用者帳戶擁有儲存體帳戶的存取權。 若要深入了解，請參閱[如何使用角色型存取控制 (RBAC) 保護儲存體帳戶](https://docs.microsoft.com/azure/storage/blobs/security-recommendations#data-protection)。

<a id="open-handles"></a>
## <a name="unable-to-delete-a-file-or-directory-in-an-azure-file-share"></a>無法刪除 Azure 檔案共用中的檔案或目錄

### <a name="cause"></a>原因
如果檔或目錄具有打開的控制碼，則通常會出現此問題。 

### <a name="solution"></a>解決方法

如果 SMB 用戶端已關閉所有打開的控制碼，並且問題繼續發生，請執行以下操作：

- 使用[獲取-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) PowerShell Cmdlet 查看打開的控制碼。

- 使用[Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) PowerShell Cmdlet 關閉打開的控制碼。 

> [!Note]  
> 獲取-AzStorageFileHandle 和關閉 AzStorageFileHandle Cmdlet 包含在 Az PowerShell 模組版本 2.4 或更高版本中。 要安裝最新的 Az PowerShell 模組，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps)。

<a id="slowperformance"></a>
## <a name="slow-performance-on-an-azure-file-share-mounted-on-a-linux-vm"></a>掛接在 Linux VM 上的 Azure 檔案共用效能變慢

### <a name="cause-1-caching"></a>原因 1：緩存

效能變慢可能的一個原因是已停用快取。 如果重複訪問檔，緩存非常有用，否則，緩存可能是開銷。 在禁用緩存之前，請檢查是否正在使用緩存。

### <a name="solution-for-cause-1"></a>原因 1 的解決方案

若要查看是否停用快取，請尋找 **cache=** 項目。

**Cache=none** 表示已停用快取。 使用預設的 mount 命令或明確地為 mount 命令加上 **cache=strict** 選項來重新掛接共用，以確保啟用預設快取或 "strict" 快取模式。

在某些情況下，**serverino** 掛接選項可能導致 **ls** 命令對每個目錄項目執行 stat。 當您列出大型目錄時，此行為會導致性能下降。 您可以檢查 **/etc/fstab** 項目中的掛接選項：

`//azureuser.file.core.windows.net/cifs /cifs cifs vers=2.1,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

您也可以執行 **sudo mount | grep cifs** 命令並檢查其輸出，來檢查是否使用正確的選項。 以下是範例輸出：

```
//azureuser.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=2.1,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777, dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)
```

如果沒有 **cache=strict** 或 **serverino** 選項，請執行[文件](../storage-how-to-use-files-linux.md)中的掛接命令，將 Azure 檔案服務卸載並再次掛接。 然後，重新檢查 **/etc/fstab** 項目是否有正確的選項。

### <a name="cause-2-throttling"></a>原因 2：限制

您可能會遇到限制，並且您的請求正在發送到佇列。 可以通過在 Azure 監視器 中利用[Azure 存儲指標](../common/storage-metrics-in-azure-monitor.md)來驗證這一點。

### <a name="solution-for-cause-2"></a>原因 2 的解決方案

確保你的應用在[Azure 檔縮放目標](storage-files-scale-targets.md#azure-files-scale-targets)內。

<a id="timestampslost"></a>
## <a name="time-stamps-were-lost-in-copying-files-from-windows-to-linux"></a>將檔案從 Windows 複製到 Linux 時，遺失時間戳記

在 Linux/Unix 平台上，如果不同的使用者擁有檔案 1 和檔案 2，則 **cp -p** 命令會失敗。

### <a name="cause"></a>原因

COPYFILE 中的強制旗標 **f** 會導致在 Unix 上執行 **cp -p -f**。 此命令也無法保留您並不擁有之檔案的時間戳記。

### <a name="workaround"></a>因應措施

使用儲存體帳戶使用者來複製檔案：

- `Useadd : [storage account name]`
- `Passwd [storage account name]`
- `Su [storage account name]`
- `Cp -p filename.txt /share`

## <a name="ls-cannot-access-ltpathgt-inputoutput-error"></a>ls：無法存取 '&lt;path&gt;'：輸入/輸出錯誤

當您嘗試使用 ls 命令列出 Azure 檔案共用中的檔案時，命令會在列出檔案時停滯， 而您會收到下列錯誤：

**ls：無法存取 '&lt;path&gt;'：輸入/輸出錯誤**


### <a name="solution"></a>解決方法
將 Linux 核心升級為下列已修正此問題的版本：

- 4.4.87+
- 4.9.48+
- 4.12.11+
- 4.13 (含) 以上的所有版本

## <a name="cannot-create-symbolic-links---ln-failed-to-create-symbolic-link-t-operation-not-supported"></a>無法建立符號連結 - ln：無法建立符號連結 't'：不支援作業

### <a name="cause"></a>原因
根據預設，使用 CIFS 在 Linux 上掛接 Azure 檔案共用，並不會啟用對符號連結的支援。 您會看到類似以下錯誤：
```
ln -s linked -n t
ln: failed to create symbolic link 't': Operation not supported
```
### <a name="solution"></a>解決方法
Linux CIFS 用戶端不支援透過 SMB 2 或 SMB 3 通訊協定，建立 Windows 樣式的符號連結。 Linux 用戶端目前支援另一種符號連結樣式，稱為 [Minshall+French symlinks](https://wiki.samba.org/index.php/UNIX_Extensions#Minshall.2BFrench_symlinks) (Mishall + 法文符號連結)，可用於建立和遵循作業。 需要符號連結的客戶可以使用 "mfsymlinks" 掛接選項。 我們建議您使用 "mfsymlinks"，因為它也是 Mac 使用的格式。

若要使用符號連結，請將下列內容新增至 CIFS 掛接命令結尾：

```
,mfsymlinks
```

該命令看起來會像這樣：

```
sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino,mfsymlinks
```

然後，您就可以依據 [wiki](https://wiki.samba.org/index.php/UNIX_Extensions#Storing_symlinks_on_Windows_servers) 建議的方式來建立符號連結。

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

<a id="error112"></a>
## <a name="mount-error112-host-is-down-because-of-a-reconnection-time-out"></a>因連線逾時而發生「掛接錯誤 (112)：主機已關機」

當用戶端閒置很長時間時，Linux 用戶端上發生「112」掛接錯誤。 加長閒置時間之後，用戶端中斷連線且連線逾時。  

### <a name="cause"></a>原因

連線可能因下列原因而閒置：

-   使用預設的「軟」掛接選項時，造成無法重新建立 TCP 連線以連線到伺服器的網路通訊失敗
-   未出現在較舊核心中的最近重新連線修正

### <a name="solution"></a>解決方法

此 Linux 核心中的重新連線問題已隨下列變更修正：

- [修正重新連線在通訊端重新連線許久之後不會延遲 SMB3 工作階段重新連線 (英文)](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93)
- [在通訊端重新連線之後立即呼叫 Echo 服務 (英文)](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7)
- [CIFS：修正重新連線期間可能發生的記憶體損毀 (英文)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b)
- [CIFS：修正重新連線期間可能發生的 Mutex 雙重鎖定 (針對核心 4.9 版與更新版本)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183) \(英文\)

但是，這些變更可能尚未移植到所有 Linux 發行版本。 如果使用流行的 Linux 發行版本，可以檢查[使用 Azure 檔與 Linux，](storage-how-to-use-files-linux.md)以查看哪個版本的發行版本具有必要的內核更改。

### <a name="workaround"></a>因應措施

您可以指定硬掛接，以解決此問題。 硬掛接會強制用戶端一直等候到連線建立或明確中斷為止。 您可用它來防止因網路逾時而發生的錯誤。 不過，這個因應措施可能會導致無限期等候。 請準備好視需要停止連線。

如果您無法升級至最新的核心版本，您可以使用下列因應措施解決此問題：在 Azure 檔案共用中保留一個每 30 秒 (或更短時間) 就會寫入的檔案。 這必須是寫入作業，例如重寫檔案的建立或修改日期。 否則，您可能會取得快取的結果，而您的作業可能不會觸發重新連線。

## <a name="cifs-vfs-error--22-on-ioctl-to-get-interface-list-when-you-mount-an-azure-file-share-by-using-smb-30"></a>使用 SMB 3.0 裝載 Azure 檔共用時，"CIFS VFS：ioctl 上的錯誤 -22 獲取介面清單"

### <a name="cause"></a>原因
記錄此錯誤是因為 Azure 檔[當前不支援 SMB 多通道](https://docs.microsoft.com/rest/api/storageservices/features-not-supported-by-the-azure-file-service)。

### <a name="solution"></a>解決方法
可以忽略這個錯誤。

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員。

如果仍需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。
