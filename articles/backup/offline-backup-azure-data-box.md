---
title: 使用 Azure 資料箱的離線備份
description: 瞭解如何使用 Azure 資料箱，從 MARS 代理程式離線將大型初始備份資料植入至 Azure 復原服務保存庫。
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 553939df8aa7a1b097b2cd6c7f29365302101324
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027026"
---
# <a name="azure-backup-offline-backup-using-azure-data-box"></a>使用 Azure 資料箱 Azure 備份離線備份

您可以使用[Azure 資料箱](https://docs.microsoft.com/azure/databox/data-box-overview)服務，將您的大型初始 MARS 備份離線（不使用網路）植入 Azure 復原服務保存庫。  這會節省時間和網路頻寬，否則會透過高延遲的網路在線上使用移動大量的備份資料。 這項增強功能目前為預覽狀態。 以 Azure 資料箱為基礎的離線備份透過 Azure 匯[入/匯出服務的離線備份](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export)，提供兩個不同的優點。

1. 不需要購買您自己的 Azure 相容磁片和連接器。 Azure 資料箱服務會將與所選[資料箱 SKU](https://azure.microsoft.com/services/databox/data/)相關聯的磁片寄送

2. Azure 備份（MARS 代理程式）可以直接將備份資料寫入支援的 Azure 資料箱 Sku 上。 這不需要布建初始備份資料的預備位置，而且需要公用程式將資料格式化並複製到磁片上。

## <a name="azure-data-box-with-mars-agent"></a>使用 MARS 代理程式 Azure 資料箱

本文說明如何使用 Azure 資料箱，從 MARS 代理程式離線將大型初始備份資料植入至 Azure 復原服務保存庫。 本文分成下列幾個部分：

* 支援的平台
* 支援的資料箱 Sku
* 必要條件
* 設定 MARS 代理程式
* 安裝 Azure 資料箱
* 備份資料傳輸至 Azure 資料箱
* 備份後步驟

## <a name="supported-platforms"></a>支援的平台

下列 Windows Sku 支援使用 Azure 資料箱從 MARS 代理程式植入資料的處理常式：

| **作業系統**                                 | **SKU**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| **適於**                        |                                                              |
| Windows 10 64 位                     | Enterprise、Pro、Home                                       |
| Windows 8.1 64 位                    | Enterprise、Pro                                             |
| Windows 8 64 位                      | Enterprise、Pro                                             |
| Windows 7 64 位                      | 旗艦版、企業版、專業版、家用 Premium、Home Basic、Starter |
| **Server**                             |                                                              |
| Windows Server 2019 64 位            | Standard、Datacenter、Essentials                            |
| Windows Server 2016 64 位            | Standard、Datacenter、Essentials                            |
| Windows Server 2012 R2 64 位         | Standard、Datacenter、Foundation                            |
| Windows Server 2012 64 位            | Datacenter、Foundation、Standard                            |
| Windows Storage Server 2016 64 位    | Standard、Workgroup                                         |
| Windows Storage Server 2012 R2 64 位 | Standard、Workgroup、必備                              |
| Windows Storage Server 2012 64 位    | Standard、Workgroup                                         |
| Windows Server 2008 R2 SP1 64 位     | Standard、Enterprise、Datacenter、Foundation                |
| Windows Server 2008 SP2 64 位        | Standard、Enterprise、Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>備份資料大小和支援的資料箱 Sku

| 備份資料大小（依 MARS 的後置壓縮） * 每一伺服器 | 支援的 Azure 資料箱 SKU                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| < = 7.2 TB                                                    | [Azure 資料箱磁碟](https://docs.microsoft.com/azure/databox/data-box-disk-overview) |
| > 7.2 TB 和 < = 80 TB * *                                      | [Azure 資料箱（100 TB）](https://docs.microsoft.com/azure/databox/data-box-overview) |

\* 一般壓縮速率會因10-20% 而異 <br>
\* * 如果您預期單一 MARS 伺服器有超過 80 TB 的初始備份資料，請與[AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com)聯繫。

>[!IMPORTANT]
>單一伺服器的初始備份資料必須包含在單一 Azure 資料箱或 Azure 資料箱磁碟中，而且不能在相同或不同 Sku 的多個裝置之間共用。 不過，Azure 資料箱裝置可能包含來自多部伺服器的初始備份。

## <a name="pre-requisites"></a>必要條件

### <a name="azure-subscription-and-required-permissions"></a>Azure 訂用帳戶和必要許可權

* 此流程需要 Azure 訂用帳戶
* 此程式要求指定要執行離線備份原則的使用者，是 Azure 訂用帳戶的「擁有者」
* 資料箱作業和復原服務保存庫（必須植入資料）必須位於相同的訂用帳戶中。
* 建議與 Azure 資料箱作業和復原服務保存庫相關聯的目標儲存體帳戶位於相同區域中。 不過，這不是必要的。

### <a name="get-azure-powershell-370"></a>取得 Azure PowerShell 3.7。0

**這是處理常式的最重要**必要條件。 安裝 Azure PowerShell 之前（ver。 3.7.0），請執行下列檢查 * *

#### <a name="step-1-check-powershell-version"></a>步驟1：檢查 PowerShell 版本

* 開啟 Windows PowerShell 並執行下列命令：

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

* 如果輸出顯示高於**3.7.0**的版本，請執行下列步驟2。 否則，請跳到步驟 3。

#### <a name="step-2-uninstall-the-powershell-version"></a>步驟2：卸載 PowerShell 版本

執行下列動作來卸載目前的 PowerShell 版本：

* 在 PowerShell 中執行下列命令，以移除相依的模組：

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

* 執行下列命令，以確保成功刪除所有相依模組：

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>步驟3：安裝 PowerShell 版本3.7。0

一旦您確認沒有任何 AzureRM 模組存在，請使用下列其中一種方法來安裝版本3.7.0：

* 從 GitHub，[連結](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)

或

* 在 PowerShell 視窗中執行下列命令：

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

Azure PowerShell 也可以使用 msi 檔案來安裝。 若要移除它，請使用 [控制台] 中的 [卸載程式] 選項將它卸載。

### <a name="order-and-receive-the-data-box-device"></a>訂購和接收資料箱裝置

使用 MARS 和 Azure 資料箱的離線備份程式需要在使用 MARS 代理程式觸發離線備份之前，所需的資料箱裝置必須處於「已傳遞」狀態。 請參閱[備份資料大小和支援的資料箱 sku](#backup-data-size-and-supported-data-box-skus) ，為您的需求訂購最適合的 sku。 依照[這篇文章](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-ordered)中的步驟，訂購和接收您的資料箱裝置。

>[!IMPORTANT]
>請勿針對帳戶種類選取 [BlobStorage]。 當選取 BlobStorage 時，MARS 代理程式需要支援分頁 Blob 的帳戶。 我們強烈建議您在建立 Azure 資料箱作業的目標儲存體帳戶時，選取*儲存體 V2* （*一般用途 V2*）做為帳戶類型。

![選擇實例詳細資料中的帳戶種類](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-setup-the-mars-agent"></a>安裝和設定 MARS 代理程式

* 請務必卸載任何先前安裝的 MARS 代理程式。

* 從[這裡](https://aka.ms/azurebackup_agent)下載最新的 MARS 代理程式。

* 執行*marsagentinstaller.exe* ，並**只**執行 * 將[代理程式安裝和註冊](https://docs.microsoft.com/azure/backup/backup-configure-vault#install-and-register-the-agent)到您想要儲存備份的復原服務保存庫的步驟。

  >[!NOTE]
  > 復原服務保存庫必須與 Azure 資料箱作業位於相同的訂用帳戶中。

* 代理程式註冊到復原服務保存庫後，請遵循下列各節中的步驟。

## <a name="setup-azure-data-box-devices"></a>設定 Azure 資料箱裝置

視您訂購的 Azure 資料箱 SKU 而定，請執行下列適當章節所涵蓋的步驟，以設定並準備資料箱裝置，讓 MARS 代理程式能夠識別和傳輸初始備份資料。

### <a name="setup-azure-data-box-disk"></a>安裝 Azure 資料箱磁碟

如果您訂購一或多個 Azure 資料箱磁片（每個磁片最多 8 TB），請遵循這裡所述的步驟，將[您的資料箱磁碟解壓縮、連接及解除鎖定](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-set-up)。

>[!NOTE]
>具有 MARS 代理程式的伺服器可能沒有 USB 埠。 在這種情況下，您可以將 Azure 資料箱磁碟連接到另一個伺服器/用戶端，並將裝置的根目錄公開為網路共用。

### <a name="setup-azure-data-box"></a>安裝 Azure 資料箱

如果您已排序 Azure 資料箱（最多 100 TB），請遵循此處所述的步驟[來設定您的資料箱](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up)。

#### <a name="mount-your-azure-data-box-as-local-system"></a>將您的 Azure 資料箱掛接為本機系統

MARS 代理程式會在本機系統內容中運作，因此需要將相同層級的許可權提供給連接 Azure 資料箱的掛接路徑。 請遵循下列步驟，以確保您可以使用 NFS 通訊協定，將您的資料箱裝置掛接為本機系統：

* 在 Windows Server 上啟用 [Client for NFS] 功能（已安裝 MARS 代理程式）。<br>
  指定替代來源： *WIM： D： \Sources\Install.wim： 4*

* 將 PSExec 從 <https://download.sysinternals.com/files/PSTools.zip> 下載到已安裝 MARS 代理程式的伺服器。

* 開啟提升許可權的命令提示字元，並執行下列命令，並將包含 PSExec 的目錄當做目前的目錄：

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

* 在上述命令中開啟的命令視窗是在本機系統內容中。 使用此命令視窗來執行步驟，以將 Azure 分頁 Blob 共用掛接為 Windows 伺服器上的網路磁碟機機。

* 依照[這裡](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-nfs#connect-to-data-box)的步驟，透過 NFS 將您的伺服器與 MARS 代理程式連線到資料箱裝置，並在本機系統命令提示字元上執行下列命令，以掛接 Azure 分頁 blob 共用：

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

* 裝載之後，請檢查您是否可以從伺服器存取 X：。 如果是，請繼續進行本文的下一節。

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>將初始備份資料傳輸到 Azure 資料箱裝置

* 在您的伺服器上開啟**Microsoft Azure 備份**應用程式。

* 按一下 [**動作**] 窗格中的 [**排程備份**]。

    ![按一下 [排程備份]](./media/offline-backup-azure-data-box/schedule-backup.png)

* 遵循**排程備份嚮導**中的步驟

* **新增專案**，讓專案大小總計在您訂購和接收[的 Azure 資料箱 SKU 所支援的大小限制](#backup-data-size-and-supported-data-box-skus)內。

    ![將專案新增至備份](./media/offline-backup-azure-data-box/add-items.png)

* 為檔案和資料夾和系統狀態選取適當的備份排程和保留原則（系統狀態僅適用于 Windows 伺服器，不適用於 Windows 用戶端）

* 在嚮導的 [**選擇初始備份類型（檔案和資料夾）** ] 畫面上，選擇 [**使用 Microsoft Azure 資料箱磁片傳輸**] 選項，然後按 **[下一步]**

    ![選擇初始備份類型](./media/offline-backup-azure-data-box/initial-backup-type.png)

* 當系統提示您使用具有 Azure 訂用帳戶之擁有者存取權的使用者認證時，請登入 Azure。 成功執行此動作之後，您應該會看到類似下面的畫面：

    ![建立資源及套用必要許可權](./media/offline-backup-azure-data-box/creating-resources.png)

* MARS 代理程式會接著提取訂用帳戶中處於「已傳遞」狀態的資料箱作業。

    ![正在提取訂用帳戶識別碼的 databox 作業](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

* 選取您已解除封裝、連線，然後將資料箱磁片解除鎖定的正確資料箱順序。 按 [下一步]。

    ![選取資料箱順序](./media/offline-backup-azure-data-box/select-databox-order.png)

* 在 [**資料箱裝置偵測**] 畫面上，按一下 [偵測**裝置**]。 這會讓 MARS 代理程式掃描本機連接 Azure 資料箱磁片並加以偵測，如下所示：

    ![資料箱裝置偵測](./media/offline-backup-azure-data-box/databox-device-detection.png)

    如果您已將 Azure 資料箱連接為網路共用（因為 USB 埠無法使用，或因為您已訂購並掛接 100 TB 資料箱裝置），則偵測會先失敗，但會提供選項，讓您輸入資料箱裝置的網路路徑，做為 shown 如下：

    ![輸入網路路徑](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > 提供 Azure 資料箱磁片根目錄的網路路徑。 此目錄必須包含名稱為*PageBlob*的目錄，如下所示：
    >
    >![Azure 資料箱磁片的根目錄](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >例如，如果磁片的路徑是 `\\mydomain\myserver\disk1\` 且*disk1*包含名為*PageBlob*的目錄，則 MARS 代理程式 wizard 上提供的路徑會是 `\\mydomain\myserver\disk1\`
    >
    >如果您[設定 Azure 資料箱 100 TB 裝置](#setup-azure-data-box)，請提供下列各項作為裝置的網路路徑 `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob`

* 按 [**下一步**]，然後在下一個畫面上按一下 **[完成]** ，使用 Azure 資料箱來儲存備份和保留原則，並設定離線備份。

* 下列畫面會確認已成功儲存原則：

    ![已成功儲存原則](./media/offline-backup-azure-data-box/policy-saved.png)

* 按一下上方畫面上的 [**關閉**]。

* 在 MARS 代理程式主控台的 [**動作**] 窗格中，按一下 [**立即備份**]，然後按一下嚮導畫面中的 [**備份**]，如下所示：

    ![立即備份 Wizard](./media/offline-backup-azure-data-box/backup-now.png)

* MARS 代理程式會開始將您選取的資料備份到 Azure 資料箱裝置。 這可能需要數小時到數天，取決於伺服器與 MARS 代理程式之間的檔案數目和連線速度，以及 Azure 資料箱磁碟。

* 完成資料的備份之後，您會在 MARS 代理程式上看到與下面類似的畫面：

    ![顯示的備份進度](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>備份後步驟

本節說明將資料備份至 Azure 資料箱磁碟成功之後要採取的步驟。

* 請遵循本文中的步驟，將[Azure 資料箱磁片寄送至 Azure](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-picked-up)。 如果您使用 Azure 資料箱 100-TB 裝置，請遵循下列步驟將[Azure 資料箱寄送至 Azure](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up)。

* [監視 Azure 入口網站中的資料箱作業](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-upload-verify)。 Azure 資料箱作業「完成」之後，MARS 代理程式會在下一次排程備份時，自動將資料從儲存體帳戶移至復原服務保存庫。 如果成功建立復原點，它會將備份作業標示為「作業已完成」。

    >[!NOTE]
    >MARS 代理程式會在原則建立期間，于排程的時間觸發備份。 不過，這些工作會標示為「等候 Azure 資料箱作業完成」，直到作業完成為止。

* 在 MARS 代理程式成功建立對應到初始備份的復原點之後，您可以刪除與 Azure 資料箱作業相關聯的儲存體帳戶（或特定內容）。

## <a name="troubleshooting"></a>疑難排解

Microsoft Azure 備份（MAB）代理程式會在您的租使用者中為您建立 Azure AD 應用程式。 此應用程式需要憑證，以便在設定離線植入原則時所建立和上傳的驗證。 我們會使用 Azure PowerShell 來建立憑證，並將其上傳至 Azure AD 應用程式。

### <a name="issue"></a>問題

在設定離線備份時，您可能會遇到問題，因為 Azure PowerShell Cmdlet 中的錯誤，您無法將多個憑證新增至 MAB 代理程式所建立的相同 Azure AD 應用程式。 如果您已為相同或不同的伺服器設定離線植入原則，這會對您造成影響。

### <a name="how-to-verify-if-the-issue-is-caused-by-this-specific-root-cause"></a>如何驗證問題是否由這個特定根本原因所造成

若要確保失敗是由於上述問題所造成，請執行下列其中一個步驟：

#### <a name="step-1"></a>步驟 1

檢查在設定離線備份時，是否在 MAB 主控台中看到下列錯誤訊息：

![無法為目前的 Azure 帳戶建立離線備份原則](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2"></a>步驟 2

* 在安裝路徑中開啟**temp**資料夾（預設的 temp 資料夾路徑是*C:\Program Files\Microsoft Azure Recovery Services Agent\Temp*）。 尋找**CBUICurr**檔案，然後開啟檔案。

* 在**CBUICurr**檔案中，流覽至最後一行，並檢查失敗是否因為 `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed`。

### <a name="workaround"></a>因應措施

解決此問題的因應措施是執行下列步驟，然後重試原則設定。

#### <a name="first-step"></a>第一步

使用具有系統管理員存取權的其他帳戶，登入出現在 MAB UI 上的 PowerShell，該訂用帳戶將會建立匯入匯出作業。

#### <a name="second-step"></a>第二個步驟

如果沒有其他伺服器設定離線植入，而且沒有其他伺服器相依于 `AzureOfflineBackup_<Azure User Id>` 應用程式，則從**Azure 入口網站** > **Azure Active Directory** > 應用程式註冊中刪除此應用程式 **。**

>[!NOTE]
> 檢查應用程式 `AzureOfflineBackup_<Azure User Id>` 是否未設定任何其他離線植入，也不會有其他伺服器相依于此應用程式。 移至 [**公開金鑰**] 區段下的 [**設定**] > [**金鑰**]，不應新增任何其他公開金鑰。 如需參考，請參閱下列螢幕擷取畫面：
>
>![公開金鑰](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="third-step"></a>第三個步驟

從您嘗試設定離線備份的伺服器，執行下列動作：

1. 開啟 [**管理電腦憑證應用程式** > **個人**] 索引標籤，然後尋找名稱為 `CB_AzureADCertforOfflineSeeding_<ResourceId>` 的憑證

2. 選取上述憑證，以滑鼠右鍵按一下 [**所有**工作] 和 [**匯出**但不含私密金鑰]，格式為 .cer。

3. 移至**第2點**中所述的 Azure 離線備份應用程式。 在 [**設定**] > [**金鑰** > **上傳公開金鑰**] 中，上傳上述步驟中所匯出的憑證。

    ![上傳公開金鑰](./media/offline-backup-azure-data-box/upload-public-key.png)

4. 在伺服器中，于 [執行] 視窗中輸入**regedit**來開啟登錄。

5. 前往 registry *Computer \ HKEY_LOCAL_MACHINE \Software\microsoft\windows server\ Azure Backup\Config\CloudBackupProvider.* 以滑鼠右鍵按一下**CloudBackupProvider** ，並新增名稱為 `AzureADAppCertThumbprint_<Azure User Id>` 的新字串值

    >[!NOTE]
    > 若要取得 Azure 使用者識別碼，請執行下列其中一個動作：
    >
    >1. 從 Azure 連線的 PowerShell 執行 `Get-AzureRmADUser -UserPrincipalName “Account Holder’s email as defined in the portal”` 命令。
    > 2. 流覽至登錄路徑： *Computer \ HKEY_LOCAL_MACHINE \Software\microsoft\windows server\ Azure Backup\DbgSettings\OnlineBackup*;名稱： *CurrentUserId*

6. 在上述步驟中新增的字串上按一下滑鼠右鍵，然後選取 [**修改**]。 在 [值] 中，提供您在**第2點**匯出的憑證指紋，然後按一下 **[確定]** 。

7. 若要取得 [指紋] 的值，請按兩下憑證，然後選取 [**詳細資料**] 索引標籤並向下滾動，直到您看到 [指紋] 欄位為止。 按一下 [**指紋**] 並複製值。

    ![憑證的指紋欄位](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>問題

如有任何問題或一提，關於任何問題，請洽詢[AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com)
