---
title: 使用 Azure 資料箱的離線備份
description: 瞭解如何使用 Azure 資料箱，從 MARS 代理程式離線將大型初始備份資料植入復原服務保存庫。
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 9a8288939adba8ec1b0cbe38d2243b1bdd84fa2e
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196275"
---
# <a name="azure-backup-offline-backup-by-using-azure-data-box"></a>使用 Azure 資料箱 Azure 備份離線備份

您可以使用[Azure 資料箱](https://docs.microsoft.com/azure/databox/data-box-overview)，將您的大型初始 MICROSOFT AZURE 復原服務（MARS）備份從離線（不使用網路）植入復原服務保存庫。 此程式會節省時間和網路頻寬，而這種情況會在透過高延遲的網路上線移動大量備份資料時使用。 這項增強功能目前為預覽狀態。 以 Azure 資料箱為基礎的離線備份可提供兩個不同的優點，而不[是根據 Azure 匯入/匯出服務進行離線備份](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export)：

* 不需要購買您自己的 Azure 相容磁片和連接器。 Azure 資料箱會運送與所選[資料箱 SKU](https://azure.microsoft.com/services/databox/data/)相關聯的磁片。
* Azure 備份（MARS 代理程式）可以直接將備份資料寫入支援的 Azure 資料箱 Sku 上。 這項功能可讓您不需要為初始備份資料布建預備位置。 您也不需要公用程式來格式化，並將該資料複製到磁片上。

## <a name="azure-data-box-with-the-mars-agent"></a>使用 MARS 代理程式 Azure 資料箱

本文說明如何使用 Azure 資料箱，從 MARS 代理程式離線將大型初始備份資料植入復原服務保存庫。

## <a name="supported-platforms"></a>支援的平台

下列 Windows Sku 支援使用 Azure 資料箱從 MARS 代理程式植入資料的處理常式。

| **作業系統**                                 | **SKU**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| **適於**                        |                                                              |
| Windows 10 64 位元                     | 企業版、專業版、家用版                                       |
| Windows 8.1 64 位元                    | Enterprise、Pro                                             |
| Windows 8 64 位元                      | Enterprise、Pro                                             |
| Windows 7 64 位元                      | Ultimate、Enterprise、Professional、Home Premium、Home Basic、Starter |
| **Server**                             |                                                              |
| Windows Server 2019 64 位元            | Standard、Datacenter、Essentials                            |
| Windows Server 2016 64 位元            | Standard、Datacenter、Essentials                            |
| Windows Server 2012 R2 64 位元         | Standard、Datacenter、Foundation                            |
| Windows Server 2012 64 位元            | Datacenter、Foundation、Standard                            |
| Windows Storage Server 2016 64 位元    | Standard、Workgroup                                         |
| Windows Storage Server 2012 R2 64 位元 | Standard、Workgroup、Essential                              |
| Windows Storage Server 2012 64 位元    | Standard、Workgroup                                         |
| Windows Server 2008 R2 SP1 64 位元     | Standard、Enterprise、Datacenter、Foundation                |
| Windows Server 2008 SP2 64 位        | Standard、Enterprise、Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>備份資料大小和支援的資料箱 Sku

| 每一伺服器的備份資料大小（依 MARS 的壓縮後） * | 支援的 Azure 資料箱 SKU                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| < = 7.2 TB                                                    | [Azure 資料箱磁片](https://docs.microsoft.com/azure/databox/data-box-disk-overview) |
| > 7.2 TB，< = 80 TB * *                                      | [Azure 資料箱（100 TB）](https://docs.microsoft.com/azure/databox/data-box-overview) |

\* 典型的壓縮速率會因10% 到20% 而有所不同。 <br>
\* * 如果您預期單一 MARS 伺服器有超過 80 TB 的初始備份資料，請聯絡[AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com)。

>[!IMPORTANT]
>單一伺服器的初始備份資料必須包含在單一 Azure 資料箱實例或 Azure 資料箱磁片中，而且不能在相同或不同 Sku 的多個裝置之間共用。 但是 Azure 資料箱裝置可以包含來自多部伺服器的初始備份。

## <a name="prerequisites"></a>必要條件

### <a name="azure-subscription-and-required-permissions"></a>Azure 訂用帳戶和必要許可權

* 此程式需要 Azure 訂用帳戶。
* 此程式要求指定要執行離線備份原則的使用者是 Azure 訂用帳戶的擁有者。
* 資料箱作業和復原服務保存庫（必須植入資料）必須位於相同的訂用帳戶中。
* 我們建議與 Azure 資料箱作業和復原服務保存庫相關聯的目標儲存體帳戶位於相同區域中。 不過，這並不是必要的。

### <a name="get-azure-powershell-370"></a>取得 Azure PowerShell 3.7。0

*這是處理常式最重要*的必要條件。 安裝 Azure PowerShell 版本3.7.0 之前，請執行下列檢查。

#### <a name="step-1-check-the-powershell-version"></a>步驟1：檢查 PowerShell 版本

1. 開啟 Windows PowerShell，然後執行下列命令：

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

1.  如果輸出顯示高於3.7.0 的版本，請執行「步驟2」。 否則，請跳至「步驟3」。

#### <a name="step-2-uninstall-the-powershell-version"></a>步驟2：卸載 PowerShell 版本

卸載目前的 PowerShell 版本。

1. 在 PowerShell 中執行下列命令，以移除相依的模組：

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

2. 若要確保成功刪除所有相依模組，請執行下列命令：

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>步驟3：安裝 PowerShell 版本3.7。0

在您確認沒有任何 AzureRM 模組存在之後，請使用下列其中一種方法來安裝版本3.7.0：

* 從 GitHub 使用[此連結](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)。

或者您可以：

* 在 PowerShell 視窗中執行下列命令：

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

Azure PowerShell 也可以使用 msi 檔案來安裝。 若要移除它，請使用 [控制台] 中的 [**卸載程式**] 選項將它卸載。

### <a name="order-and-receive-the-data-box-device"></a>訂購和接收資料箱裝置

使用 MARS 和 Azure 資料箱的離線備份程式需要在您使用 MARS 代理程式來觸發離線備份之前，資料箱裝置必須處於已傳遞狀態。 若要為您的需求訂購最適合的 SKU，請參閱[備份資料大小和支援的資料箱 sku](#backup-data-size-and-supported-data-box-skus)。 依照[教學課程：訂購 Azure 資料箱磁片](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-ordered)來訂購和接收您的資料箱裝置中的步驟進行。

> [!IMPORTANT]
> 請勿針對**帳戶種類**選取 [ *BlobStorage* ]。 MARS 代理程式需要支援分頁 blob 的帳戶，這在選取*BlobStorage*時不受支援。 當您建立 Azure 資料箱作業的目標儲存體帳戶時，請選取 [**儲存體 V2 （一般用途 V2）** ] 做為**帳戶類型**。

![選擇實例詳細資料中的帳戶種類](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-set-up-the-mars-agent"></a>安裝和設定 MARS 代理程式

1. 請務必卸載任何先前安裝的 MARS 代理程式。
1. 從[這個網站](https://aka.ms/azurebackup_agent)下載最新的 MARS 代理程式。
1. 執行*marsagentinstaller.exe*，並*只*執行將[代理程式安裝](https://docs.microsoft.com/azure/backup/backup-configure-vault#install-and-register-the-agent)到復原服務保存庫，並將其註冊到您想要儲存備份的步驟。

   > [!NOTE]
   > 復原服務保存庫必須與 Azure 資料箱作業位於相同的訂用帳戶中。

   向復原服務保存庫註冊代理程式之後，請依照下一節中的步驟進行。

## <a name="set-up-azure-data-box-devices"></a>設定 Azure 資料箱裝置

視您訂購的 Azure 資料箱 SKU 而定，請執行下列適當章節中所涵蓋的步驟。 這些步驟會示範如何設定和準備 MARS 代理程式的資料箱裝置，以識別和傳輸初始備份資料。

### <a name="set-up-azure-data-box-disks"></a>設定 Azure 資料箱磁片

如果您訂購一或多個 Azure 資料箱磁片（每個磁片最多 8 TB），請遵循這裡所述的步驟，將[您的資料箱磁片解壓縮、連接及解除鎖定](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-set-up)。

>[!NOTE]
>具有 MARS 代理程式的伺服器可能沒有 USB 埠。 在這種情況下，您可以將 Azure 資料箱磁片連接到另一個伺服器或用戶端，並將裝置的根目錄公開為網路共用。

### <a name="set-up-azure-data-box"></a>設定 Azure 資料箱

如果您已排序 Azure 資料箱實例（最多 100 TB），請遵循這裡的步驟[來設定您的資料箱實例](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up)。

#### <a name="mount-your-azure-data-box-instance-as-a-local-system"></a>將您的 Azure 資料箱實例掛接為本機系統

MARS 代理程式會在本機系統內容中運作，因此需要將相同層級的許可權提供給連接 Azure 資料箱實例的掛接路徑。 

若要確保您可以使用 NFS 通訊協定，將您的資料箱裝置掛接為本機系統：

1. 在安裝 MARS 代理程式的 Windows 伺服器上，啟用 [NFS] 功能的用戶端。 指定替代來源*WIM： D： \Sources\Install.wim： 4*。
1. 將 PSExec 從 <https://download.sysinternals.com/files/PSTools.zip> 下載到已安裝 MARS 代理程式的伺服器。
1. 開啟提升許可權的命令提示字元，然後執行下列命令，並將包含*PSExec*的目錄當做目前目錄。

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

   以先前命令的結果開啟的命令視窗位於本機系統內容中。 使用此命令視窗來執行步驟，以將 Azure 分頁 blob 共用掛接為 Windows 伺服器上的網路磁碟機機。
1. 依照[連接到資料箱](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-nfs#connect-to-data-box)中的步驟，透過 NFS 將伺服器與 MARS 代理程式連線到資料箱裝置。 在本機系統命令提示字元上執行下列命令，以掛接 Azure 分頁 blob 共用。

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

   裝載共用之後，請檢查您是否可以從伺服器存取 X：。 如果可以，請繼續進行本文的下一節。

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>將初始備份資料傳輸到 Azure 資料箱裝置

1. 在您的伺服器上開啟**Microsoft Azure 備份**應用程式。
1. 在 [**動作**] 窗格中，選取 [**排程備份**]。

    ![選取排程備份](./media/offline-backup-azure-data-box/schedule-backup.png)

1. 依照 [**排程備份] 嚮導**中的步驟進行。

1. 選取 [**新增專案**] 按鈕來新增專案。 將專案大小總計保留在您所訂購和接收[的 AZURE 資料箱 SKU 所支援的大小限制](#backup-data-size-and-supported-data-box-skus)內。

    ![將專案新增至備份](./media/offline-backup-azure-data-box/add-items.png)

1. 為檔案**和資料夾**和**系統狀態**選取適當的備份排程和保留原則。 系統狀態僅適用于 Windows 伺服器，不適用於 Windows 用戶端。
1. 在嚮導的 [**選擇初始備份類型（檔案和資料夾）** ] 頁面上，選取 [**使用 Microsoft Azure 傳送資料箱磁片**] 選項，然後選取 **[下一步]** 。

    ![選擇初始備份類型](./media/offline-backup-azure-data-box/initial-backup-type.png)

1. 當系統提示時，請使用具有 Azure 訂用帳戶之擁有者存取權的使用者認證來登入 Azure。 成功執行此動作之後，您應該會看到類似這一頁的頁面。

    ![建立資源並套用所需的許可權](./media/offline-backup-azure-data-box/creating-resources.png)

   然後 MARS 代理程式會在訂用帳戶中，提取處於已傳遞狀態的資料箱作業。

    ![提取訂用帳戶識別碼資料箱作業](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

1. 選取您已解除封裝、連線，並將資料箱磁片解除鎖定的正確資料箱順序。 選取 [下一步]。

    ![選取資料箱訂單](./media/offline-backup-azure-data-box/select-databox-order.png)

1. 在 [**資料箱裝置偵測**] 頁面上選取 [偵測**裝置**]。 此動作可讓 MARS 代理程式掃描本機連接 Azure 資料箱磁片，並偵測到它們。

    ![資料箱裝置偵測](./media/offline-backup-azure-data-box/databox-device-detection.png)

    如果您將 Azure 資料箱實例連線為網路共用（因為 USB 埠無法使用，或因為您已排序並掛接了 100-TB 資料箱裝置），偵測會先失敗。 您可以選擇輸入資料箱裝置的網路路徑。

    ![輸入網路路徑](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > 提供 Azure 資料箱磁片根目錄的網路路徑。 此目錄必須包含名稱為*PageBlob*的目錄。
    >
    >![Azure 資料箱磁片的根目錄](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >例如，如果磁片的路徑是 `\\mydomain\myserver\disk1\` 且*disk1*包含名為*PageBlob*的目錄，則會 `\\mydomain\myserver\disk1\`您在 MARS 代理程式的 [wizard] 頁面上輸入的路徑。
    >
    >如果您[設定 Azure 資料箱 100-TB 裝置](#set-up-azure-data-box-devices)，請輸入 `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` 作為裝置的網路路徑。

1. 選取 [**下一步**]，然後在下一個頁面上選取 **[完成]** ，以使用 Azure 資料箱來儲存備份和保留原則，並設定離線備份。

   下列頁面會確認已成功儲存原則。

    ![已成功儲存原則](./media/offline-backup-azure-data-box/policy-saved.png)

1. 選取前一頁的 [**關閉**]。

1. 在 MARS 代理程式主控台的 [**動作**] 窗格中，選取 [**立即備份**]。 在 [wizard] 頁面上選取 [**備份**]。

    ![立即備份 Wizard](./media/offline-backup-azure-data-box/backup-now.png)

MARS 代理程式會開始將您選取的資料備份到 Azure 資料箱裝置。 此程式可能需要數小時到數天的時間。 時間量取決於伺服器與 MARS 代理程式之間的檔案數目和連線速度，以及 Azure 資料箱磁片。

資料備份完成後，您會看到 MARS 代理程式上的頁面與此類似。

![顯示的備份進度](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>備份後步驟

本節說明將資料備份至 Azure 資料箱磁碟成功之後應採取的步驟。

* 請遵循本文中的步驟，將[Azure 資料箱磁片寄送至 Azure](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-picked-up)。 如果您使用 Azure 資料箱 100-TB 裝置，請遵循下列步驟將[Azure 資料箱裝置寄送至 Azure](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up)。

* [監視 Azure 入口網站中的資料箱作業](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-upload-verify)。 Azure 資料箱作業完成後，MARS 代理程式會在下一次排程備份時，自動將資料從儲存體帳戶移至復原服務保存庫。 然後，如果成功建立復原點，則會將備份作業標示為 [*作業已完成*]。

    >[!NOTE]
    >MARS 代理程式會在原則建立期間，于排程的時間觸發備份。 這些作業會標示為「等候 Azure 資料箱作業完成」，直到作業完成為止。

* 在 MARS 代理程式成功建立對應于初始備份的復原點之後，您可以刪除儲存體帳戶或與 Azure 資料箱作業相關聯的特定內容。

## <a name="troubleshooting"></a>疑難排解

Microsoft Azure 備份（MAB）代理程式會在您的租使用者中為您建立 Azure Active Directory （Azure AD）應用程式。 當您設定離線植入原則時，此應用程式需要憑證來進行已建立和上傳的驗證。 我們會使用 Azure PowerShell 來建立憑證，並將其上傳至 Azure AD 應用程式。

### <a name="problem"></a>問題

當您設定離線備份時，可能會因為 Azure PowerShell Cmdlet 中的錯誤而面臨問題。 您可能無法將多個憑證新增至 MAB 代理程式所建立的相同 Azure AD 應用程式。 如果您為相同或不同的伺服器設定離線植入原則，此問題將會影響您。

### <a name="verify-if-the-problem-is-caused-by-this-specific-root-cause"></a>確認問題是否由這個特定根本原因所造成

若要查看您的問題是否與先前所述的相同，請執行下列其中一個步驟。

#### <a name="step-1"></a>步驟 1

當您設定離線備份時，請檢查 MAB 主控台中是否出現下列錯誤訊息。

![無法為目前的 Azure 帳戶建立離線備份原則](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2"></a>步驟 2

1. 在安裝路徑中開啟**Temp**資料夾。 預設的暫存資料夾路徑是*C:\Program Files\Microsoft Azure Recovery Services Agent\Temp*。尋找*CBUICurr*檔案，然後開啟檔案。

1. 在*CBUICurr*檔案中，流覽至最後一行，並查看問題是否與此錯誤訊息中的相同： `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed`。

### <a name="workaround"></a>因應措施

解決此問題的因應措施是執行下列步驟，然後重試原則設定。

#### <a name="step-1"></a>步驟 1

使用具有已建立匯入或匯出作業之訂用帳戶的系統管理員存取權，登入出現在 MAB UI 上的 PowerShell。

#### <a name="step-2"></a>步驟 2

如果沒有其他伺服器已設定離線植入，而且沒有其他伺服器相依于 `AzureOfflineBackup_<Azure User Id>` 應用程式，請刪除此應用程式。 選取**Azure 入口網站** > **Azure Active Directory** > **應用程式註冊**。

>[!NOTE]
> 檢查 `AzureOfflineBackup_<Azure User Id>` 應用程式是否未設定任何其他離線植入，以及其他伺服器是否相依于此應用程式。 移至 [**設定**] > [**公開金鑰**] 區段底下的 [**金鑰**]。 不應新增任何其他公開金鑰。 如需參考，請參閱下列螢幕擷取畫面。
>
>![公開金鑰](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="step-3"></a>步驟 3

從您嘗試設定離線備份的伺服器，執行下列動作。

1. 移至 [**管理電腦憑證] 應用程式** > [**個人**] 索引標籤，然後尋找名稱為 `CB_AzureADCertforOfflineSeeding_<ResourceId>`的憑證。

2. 選取憑證，以滑鼠右鍵按一下 [**所有**工作]，然後選取 [**匯出**但不使用 .cer 格式的私密金鑰]。

3. 移至步驟2中所述的 Azure 離線備份應用程式。 選取 **設定** > **金鑰** > **上傳公開金鑰**。 上傳您在上一個步驟中匯出的憑證。

    ![上傳公開金鑰](./media/offline-backup-azure-data-box/upload-public-key.png)

4. 在伺服器中，于 [執行] 視窗中輸入**regedit**來開啟登錄。

5. 前往 registry *Computer \ HKEY_LOCAL_MACHINE \Software\microsoft\windows server\ Azure Backup\Config\CloudBackupProvider.* 以滑鼠右鍵按一下 [ **CloudBackupProvider**]，然後加入名稱為 `AzureADAppCertThumbprint_<Azure User Id>`的新字串值。

    >[!NOTE]
    > 若要取得 Azure 使用者識別碼，請執行下列其中一項動作：
    >
    >* 從 Azure 連線的 PowerShell 中，執行 `Get-AzureRmADUser -UserPrincipalName “Account Holder’s email as defined in the portal”` 命令。
    > * 移至登錄路徑*Computer \ HKEY_LOCAL_MACHINE \Software\microsoft\windows server\ Azure Backup\DbgSettings\OnlineBackup* ，其名稱為*CurrentUserId*。

6. 以滑鼠右鍵按一下上一個步驟中新增的字串，然後選取 [**修改**]。 在 [值] 中，提供您在步驟2中匯出之憑證的指紋。 選取 **[確定]** 。

7. 若要取得指紋的值，請按兩下憑證。 選取 [**詳細資料**] 索引標籤，然後向下滾動，直到您看到 [指紋] 欄位為止。 選取 [**指紋**]，然後複製值。

    ![憑證的指紋欄位](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>問題

如有任何疑問或您遇到任何問題，請聯絡[AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com)。
