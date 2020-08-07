---
title: 搭配 Windows 使用 Azure 檔案共用 | Microsoft Docs
description: 瞭解如何搭配 Windows 和 Windows Server 使用 Azure 檔案共用。 在內部部署或 Azure Vm 上執行的 Windows 安裝上，使用 SMB 3.0 的 Azure 檔案共用。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: fcc79a0763d7441a8a3c04723578ccc27cf5fe4e
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87905749"
---
# <a name="use-an-azure-file-share-with-windows"></a>搭配 Windows 使用 Azure 檔案共用
[Azure 檔案服務](storage-files-introduction.md)是 Microsoft 易於使用的雲端檔案系統。 Azure 檔案共用可在 Windows 和 Windows Server 中順暢地使用。 本文討論搭配 Windows 和 Windows Server 使用 Azure 檔案共用的考量。

若要在 Azure 區域之外使用 Azure 檔案共用，例如內部部署環境或是在不同的 Azure 區域，作業系統必須支援 SMB 3.0。 

您可以在於 Azure VM 或內部部署環境中執行的 Windows 安裝上使用 Azure 檔案共用。 下表說明哪些作業系統版本在哪個環境中支援存取檔案共用：

| Windows 版本        | SMB 版本 | 可在 Azure VM 中掛接 | 可在內部部署環境掛接 |
|------------------------|-------------|-----------------------|-----------------------|
| Windows Server 2019 | SMB 3.0 | 是 | 是 |
| Windows 10<sup>1</sup> | SMB 3.0 | 是 | 是 |
| Windows Server 半年度通道<sup>2</sup> | SMB 3.0 | 是 | 是 |
| Windows Server 2016 | SMB 3.0 | 是 | 是 |
| Windows 8.1 | SMB 3.0 | 是 | 是 |
| Windows Server 2012 R2 | SMB 3.0 | 是 | 是 |
| Windows Server 2012 | SMB 3.0 | 是 | 是 |
| Windows 7<sup>3</sup> | SMB 2.1 | 是 | 否 |
| Windows Server 2008 R2<sup>3</sup> | SMB 2.1 | 是 | 否 |

<sup>1</sup>Windows 10，版本 1507、1607、1709、1803、1809、1903 及 1909。  
<sup>2</sup>Windows Server，版本 1809、1903 及 1909。  
<sup>3</sup>Windows 7 及 Windows Server 2008 R2 的一般 Microsoft 支援已結束。 只能透過[延伸安全性更新 (ESU) 計畫](https://support.microsoft.com/help/4497181/lifecycle-faq-extended-security-updates)額外購買安全性更新。 強烈建議您移轉離開這類作業系統。

> [!Note]  
> 我們一律建議針對您的 Windows 版本採取最新的 KB。

## <a name="prerequisites"></a>Prerequisites 

請確定已開啟連接埠 445：使用 SMB 通訊協定時必須開啟 TCP 連接埠 445；如果連接埠 445 遭到封鎖，連線將會失敗。 您可以使用 Cmdlet 來檢查您的防火牆是否封鎖埠 445 `Test-NetConnection` 。 若要瞭解解決已封鎖445埠的方法，請參閱我們的 Windows 疑難排解指南的[原因1：埠445遭到封鎖](storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked)一節。

## <a name="using-an-azure-file-share-with-windows"></a>搭配 Windows 使用 Azure 檔案共用
若要搭配 Windows 使用 Azure 檔案共用，您必須掛接它 (這表示將磁碟機代號或掛接點路徑指派給它)，或透過 [UNC 路徑](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx)存取它。 

本文會使用儲存體帳戶金鑰來使用檔案共用。 儲存體帳戶金鑰是儲存體帳戶的管理員金鑰，包括您所存取檔案共用內所有檔案和資料夾的系統管理員權限，以及您儲存體帳戶內所有檔案共用和其他儲存體資源 (blob、佇列、資料表等) 的系統管理員權限。 如果不足以應對您的工作負載，則可使用 [Azure 檔案同步](storage-sync-files-planning.md)，也可使用 [SMB 身分識別驗證](storage-files-active-directory-overview.md)。

將應有 SMB 檔案共用的企業營運 (LOB) 應用程式隨即移轉至 Azure 的常見模式，就是使用 Azure 檔案共用作為在 Azure VM 中執行專用 Windows 檔案伺服器的替代方式。 成功移轉企業營運應用程式以使用 Azure 檔案共用的一項重要考量，就是許多企業營運應用程式使用有限的系統權限 (而不是 VM 的系統管理帳戶)，在專屬服務帳戶的內容之下執行。 因此，您必須確定從服務帳戶的內容 (而不是系統管理帳戶) 掛接/儲存 Azure 檔案共用的認證。

### <a name="mount-the-azure-file-share"></a>掛接 Azure 檔案共用

Azure 入口網站提供您可以用來直接將檔案共用掛接至主機的腳本。 我們建議使用此提供的腳本。

若要取得此腳本：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 流覽至包含您想要掛接之檔案共用的儲存體帳戶。
1. 選取 [檔案共用]。
1. 選取您想要掛接的檔案共用。

    :::image type="content" source="media/storage-how-to-use-files-windows/select-file-shares.png" alt-text="實例":::

1. 選取 [連接]。

    :::image type="content" source="media/storage-how-to-use-files-windows/file-share-connect-icon.png" alt-text="檔案共用之 [連線] 圖示的螢幕擷取畫面。":::

1. 選取要用來掛接共用的磁碟機號。
1. 複製提供的腳本。

    :::image type="content" source="media/storage-how-to-use-files-windows/files-portal-mounting-cmdlet-resize.png" alt-text="範例文字":::

1. 將腳本貼到您想要掛接檔案共用的主機 shell 中，並加以執行。

您現在已掛接 Azure 檔案共用。

### <a name="mount-the-azure-file-share-with-file-explorer"></a>使用檔案總管掛接 Azure 檔案共用
> [!Note]  
> 請注意，下列指示會顯示在 Windows 10 上，與較舊版本可能稍有不同。 

1. 開啟檔案總管。 做法是從 [開始] 功能表中開啟，或按 Win + E 捷徑。

1. 流覽至視窗左側的 [這部**電腦**]。 這會變更功能區中所提供的功能表。 在 [電腦] 功能表之下，選取 [連線網路磁碟機]。
    
    ![「連線網路磁碟機」下拉式功能表的螢幕擷取畫面](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

1. 選取硬碟機代號，並輸入 UNC 路徑，UNC 路徑格式如下：`\\<storageAccountName>.file.core.windows.net\<fileShareName>`。 例如： `\\anexampleaccountname.file.core.windows.net\example-share-name` 。
    
    ![「連線網路磁碟機」對話方塊的螢幕擷取畫面](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

1. 使用前面加上 `AZURE\` 的儲存體帳戶名稱作為使用者名稱，並使用儲存體帳戶金鑰作為密碼。
    
    ![網路認證對話方塊的螢幕擷取畫面](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

1. 視需要使用 Azure 檔案共用。
    
    ![現在已掛接 Azure 檔案共用](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

1. 當您準備好要卸載 Azure 檔案共用時，您可以滑鼠右鍵按一下 [檔案總管] 中 [網路位置] 下的共用項目，然後選取 [中斷連線]。

### <a name="accessing-share-snapshots-from-windows"></a>從 Windows 存取共用快照集
如果您已建立共用快照集 (透過指令碼或 Azure 備份之類的服務手動或自動建立)，您即可從 Windows 上的檔案共用檢視舊版的共用、目錄或特定檔案。 您可以使用[Azure PowerShell](storage-how-to-use-files-powershell.md)、 [Azure CLI](storage-how-to-use-files-cli.md)或[Azure 入口網站](storage-how-to-use-files-portal.md)來建立共用快照集。

#### <a name="list-previous-versions"></a>列出舊版
瀏覽至需要還原的項目或父項目。 按兩下以移至所需的目錄。 按一下滑鼠右鍵，然後從功能表中選取 [屬性]。

![以滑鼠右鍵按一下功能表以取得選取的目錄](./media/storage-how-to-use-files-windows/snapshot-windows-previous-versions.png)

選取 [舊版]，以查看此目錄之共用快照集的清單。 視網路速度和目錄中的共用快照集數目而定，清單可能需要幾秒鐘的時間才能載入。

![[舊版] 索引標籤](./media/storage-how-to-use-files-windows/snapshot-windows-list.png)

您可以選取 [開啟]，開啟特定的快照集。 

![已開啟快照集](./media/storage-how-to-use-files-windows/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>從舊版還原
選取 [還原] 可在共用快照集建立時，將整個目錄的內容以遞迴方式複製到原始位置。

 ![警告訊息中的 [還原] 按鈕](./media/storage-how-to-use-files-windows/snapshot-windows-restore.png) 

## <a name="securing-windowswindows-server"></a>保護 Windows/Windows Server
為了在 Windows 上掛接 Azure 檔案共用，必須可以存取連接埠 445。 許多組織會封鎖連接埠 445，因為 SMB 1 固有的安全性風險。 SMB 1 也稱為 CIFS (Common Internet File System)，是 Windows 和 Windows Server 隨附的舊版檔案系統通訊協定。 SMB 1 已過期、沒有效率，而且最重要的是不安全的通訊協定。 好消息是 Azure 檔案服務不支援 SMB 1，而且所有支援的 Windows 和 Windows Server 版本都允許移除或停用 SMB 1。 我們始終[強烈建議](https://aka.ms/stopusingsmb1)先在 Windows 中移除或停用 SMB 1 用戶端和伺服器，再於生產環境中使用 Azure 檔案共用。

下表針對每個 Windows 版本提供 SMB 1 狀態的詳細資訊：

| Windows 版本                           | SMB 1 預設狀態 | 停用/移除方法       | 
|-------------------------------------------|----------------------|-----------------------------|
| Windows Server 2019                       | 已停用             | 利用 Windows 功能移除 |
| Windows Server 版本 1709+            | 已停用             | 利用 Windows 功能移除 |
| Windows 10 版本 1709+                | 已停用             | 利用 Windows 功能移除 |
| Windows Server 2016                       | 啟用              | 利用 Windows 功能移除 |
| Windows 10 版本 1507、1607 和 1703 | 啟用              | 利用 Windows 功能移除 |
| Windows Server 2012 R2                    | 啟用              | 利用 Windows 功能移除 | 
| Windows 8.1                               | 啟用              | 利用 Windows 功能移除 | 
| Windows Server 2012                       | 啟用              | 利用登錄停用       | 
| Windows Server 2008 R2                    | 啟用              | 利用登錄停用       |
| Windows 7                                 | 啟用              | 利用登錄停用       | 

### <a name="auditing-smb-1-usage"></a>稽核 SMB 1 使用量
> 適用於 Windows Server 2019、Windows Server 半年度通道 (版本 1709 和 1803)、Windows Server 2016、Windows 10 (版本 1507、1607、1703、1709 和 1803)、Windows Server 2012 R2 和 Windows 8.1

移除您環境中的 SMB 1 之前，您可以稽核 SMB 1 使用情況，以查看是否有任何用戶端會因此變更而中斷。 如果對含有 SMB 1 的 SMB 共用 提出任何要求，稽核事件將會記錄在 `Applications and Services Logs > Microsoft > Windows > SMBServer > Audit` 之下的事件記錄中。 

> [!Note]  
> 若要在 Windows Server 2012 R2 和 Windows 8.1 上啟用稽核支援，請至少安裝 [KB4022720](https://support.microsoft.com/help/4022720/windows-8-1-windows-server-2012-r2-update-kb4022720)。

若要啟用稽核，請從提高權限的 PowerShell 工作階段執行下列 Cmdlet︰

```powershell
Set-SmbServerConfiguration –AuditSmb1Access $true
```

### <a name="removing-smb-1-from-windows-server"></a>從 Windows Server 移除 SMB 1
> 適用於 Windows Server 2019、Windows Server 半年度通道 (版本 1709 和 1803)、Windows Server 2016、Windows Server 2012 R2

若要從 Windows Server 執行個體移除 SMB 1，請從提高權限的 PowerShell 工作階段執行下列 Cmdlet︰

```powershell
Remove-WindowsFeature -Name FS-SMB1
```

若要完成移除程序，請重新啟動您的伺服器。 

> [!Note]  
> 從 Windows 10 和 Windows Server 版本 1709 開始，預設不會安裝 SMB 1，而且 SMB 1 用戶端和 SMB 1 伺服器具有不同的 Windows 功能。 我們一律建議不要安裝 SMB 1 伺服器 (`FS-SMB1-SERVER`) 和 SMB 1 用戶端 (`FS-SMB1-CLIENT`)。

### <a name="removing-smb-1-from-windows-client"></a>從 Windows 用戶端移除 SMB 1
> 套用至 Windows 10 (版本 1507、1607、1703、1709 和 1803) 和 Windows 8.1

若要從 Windows 用戶端移除 SMB 1，請從提高權限的 PowerShell 工作階段執行下列 Cmdlet︰

```powershell
Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol
```

若要完成移除程序，請重新啟動您的電腦。

### <a name="disabling-smb-1-on-legacy-versions-of-windowswindows-server"></a>在舊版 Windows/Windows Server 上停用 SMB 1
> 套用至 Windows Server 2012、Windows Server 2008 R2 和 Windows 7

SMB 1 無法完全從舊版的 Windows/Windows Server 中移除，但是可以透過登錄停用。 若要停用 SMB 1，請在 `HKEY_LOCAL_MACHINE > SYSTEM > CurrentControlSet > Services > LanmanServer > Parameters` 之下建立類型為 `DWORD` 且值為 `0` 的新登錄機碼 `SMB1`。

您也可以使用下列 PowerShell Cmdlet 輕鬆達成：

```powershell
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters" SMB1 -Type DWORD -Value 0 –Force
```

建立此登錄機碼之後，您必須重新啟動伺服器，才能停用 SMB 1。

### <a name="smb-resources"></a>SMB 資源
- [停只使用 SMB 1](https://blogs.technet.microsoft.com/filecab/2016/09/16/stop-using-smb1/)
- [SMB 1 產品情報交換所](https://blogs.technet.microsoft.com/filecab/2017/06/01/smb1-product-clearinghouse/)
- [使用 DSCEA 探索您環境中的 SMB 1](https://blogs.technet.microsoft.com/ralphkyttle/2017/04/07/discover-smb1-in-your-environment-with-dscea/)
- [透過群組原則停用 SMB 1](https://blogs.technet.microsoft.com/secguide/2017/06/15/disabling-smbv1-through-group-policy/)

## <a name="next-steps"></a>後續步驟
請參閱這些連結，以取得 Azure 檔案服務的詳細資訊：
- [規劃 Azure 檔案部署](storage-files-planning.md)
- [常見問題集](../storage-files-faq.md)
- [在 Windows 上進行疑難排解](storage-troubleshoot-windows-file-connection-problems.md)      
