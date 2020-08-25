---
title: 使用 Azure 資料箱進行離線備份
description: 瞭解如何使用 Azure 資料箱將從 MARS 代理程式離線的大型初始備份資料植入復原服務保存庫。
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 6ad97ee60c3c7debea72357cf7fc8d483a3c1d46
ms.sourcegitcommit: f1b18ade73082f12fa8f62f913255a7d3a7e42d6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/24/2020
ms.locfileid: "88761554"
---
# <a name="azure-backup-offline-backup-by-using-azure-data-box"></a>使用 Azure 資料箱進行 Azure 備份離線備份

您可以使用 [Azure 資料箱](../databox/data-box-overview.md) ，在不使用網路 (復原服務保存庫的情況下，將您的大量初始 MICROSOFT AZURE 復原服務 (MARS) 備份離線。 此程序可節省在透過高延遲網路線上移動大量備份資料時使會取用的時間和網路頻寬。 這項增強功能目前為預覽狀態。 相較於[基於 Azure 匯入/匯出服務的離線備份](./backup-azure-backup-import-export.md)，基於 Azure 資料箱的離線備份能提供兩個不同的優點：

- 不需要購買您自己的 Azure 相容磁片和連接器。 Azure 資料箱會隨附與所選[資料箱 SKU](https://azure.microsoft.com/services/databox/data/) 相關聯的磁碟。
- Azure 備份 (MARS 代理程式) 可以直接將備份資料寫入到支援的 Azure 資料箱 SKU 上。 這項功能讓您不必為初始備份資料佈建預備位置。 您也不需要透過公用程式來格式化資料，並將該資料複製到磁碟上。

## <a name="azure-data-box-with-the-mars-agent"></a>使用 MARS 代理程式 Azure 資料箱

本文說明如何使用 Azure 資料箱，從 MARS 代理程式離線將大量初始備份資料植入復原服務保存庫。

## <a name="supported-platforms"></a>支援的平台

下列 Windows Sku 支援使用 Azure 資料箱從 MARS 代理程式植入資料的進程。

| **作業系統**                                 | **SKU**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| **工作站**                        |                                                              |
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

## <a name="backup-data-size-and-supported-data-box-skus"></a>備份資料大小和支援的資料箱 SKU

| 每一伺服器的 MARS) * (壓縮後的備份資料大小 | 支援的 Azure 資料箱 SKU                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <= 7.2 TB                                                    | [Azure 資料箱磁片](../databox/data-box-disk-overview.md) |
| >7.2 TB 和 <= 80 TB * *                                      | [Azure 資料箱 (100 TB)](../databox/data-box-overview.md) |

* 一般壓縮速率會隨著10% 和20% 而有所不同。 <br>
* * 如果您預期單一 MARS 伺服器有超過 80 TB 的初始備份資料，請聯絡 [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) 。

>[!IMPORTANT]
>單一伺服器的初始備份資料必須包含在單一 Azure 資料箱實例或 Azure 資料箱磁片中，且不能在相同或不同 Sku 的多個裝置之間共用。 但 Azure 資料箱的裝置可以包含來自多部伺服器的初始備份。

## <a name="prerequisites"></a>Prerequisites

### <a name="azure-subscription-and-required-permissions"></a>Azure 訂用帳戶和必要許可權

- 此程式需要 Azure 訂用帳戶。
- 此程式需要指定執行離線備份原則的使用者是 Azure 訂用帳戶的擁有者。
- 資料箱作業和復原服務保存庫 (需要植入資料) 必須位於相同的訂用帳戶中。
- 建議與 Azure 資料箱作業和復原服務保存庫相關聯的目標儲存體帳戶位於相同的區域。 不過，這並不是必要的。

### <a name="get-azure-powershell-370"></a>取得 Azure PowerShell 3.7。0

*這是此程式最重要的先決條件*。 在安裝 Azure PowerShell 3.7.0 版之前，請先執行下列檢查。

#### <a name="step-1-check-the-powershell-version"></a>步驟1：檢查 PowerShell 版本

1. 開啟 Windows PowerShell，然後執行下列命令：

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

1. 如果輸出顯示高於3.7.0 的版本，請執行「步驟2」。 否則，請跳至「步驟3」。

#### <a name="step-2-uninstall-the-powershell-version"></a>步驟2：卸載 PowerShell 版本

卸載 PowerShell 的目前版本。

1. 在 PowerShell 中執行下列命令，以移除相依模組：

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

2. 若要確保成功刪除所有相依模組，請執行下列命令：

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>步驟3：安裝 PowerShell 版本3.7。0

確認沒有 AzureRM 模組存在之後，請使用下列其中一種方法來安裝版本3.7.0：

- 在 GitHub 中，請使用 [此連結](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)。

或者您可以：

- 在 PowerShell 視窗中執行下列命令：

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

您也可以使用 msi 檔案來安裝 Azure PowerShell。 若要將它移除，請使用主控台中的 [ **卸載程式** ] 選項將其卸載。

### <a name="order-and-receive-the-data-box-device"></a>訂購和接收資料箱裝置

使用 MARS 和 Azure 資料箱的離線備份程式要求資料箱裝置必須處於已傳送的狀態，您才能使用 MARS 代理程式來觸發離線備份。 若要為您的需求訂購最適合的 SKU，請參閱 [備份資料大小和支援的資料箱 sku](#backup-data-size-and-supported-data-box-skus)。 遵循教學課程中的步驟 [：訂購 Azure 資料箱磁片](../databox/data-box-disk-deploy-ordered.md) 來訂購和接收您的資料箱裝置。

> [!IMPORTANT]
> 請勿選取**帳戶種類**的*BlobStorage* 。 MARS 代理程式需要支援分頁 blob 的帳戶，但在選取 *BlobStorage* 時不支援此選項。 當您建立 Azure 資料箱作業的目標儲存體帳戶時，請選取 **儲存體 V2 (一般用途 V2) ** 作為 **帳戶類型** 。

![選擇實例詳細資料中的帳戶種類](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-set-up-the-mars-agent"></a>安裝和設定 MARS 代理程式

1. 請務必卸載任何先前安裝的 MARS 代理程式。
1. 從 [這個網站](https://aka.ms/azurebackup_agent)下載最新的 MARS 代理程式。
1. 執行 *MARSAgentInstaller.exe*，並 *只* 執行安裝的步驟， [並將代理程式註冊](./install-mars-agent.md#install-and-register-the-agent) 至您想要儲存備份的復原服務保存庫。

   > [!NOTE]
   > 復原服務保存庫必須位於與 Azure 資料箱作業相同的訂用帳戶中。

   將代理程式註冊到復原服務保存庫之後，請遵循下一節中的步驟。

## <a name="set-up-azure-data-box-devices"></a>設定 Azure 資料箱裝置

根據您所訂購的 Azure 資料箱 SKU，執行下列適當章節中所述的步驟。 這些步驟會示範如何設定和準備 MARS 代理程式的資料箱裝置，以識別和傳送初始備份資料。

### <a name="set-up-azure-data-box-disks"></a>設定 Azure 資料箱磁片

如果您訂購一或多個 Azure 資料箱磁片 (每個) 最多 8 TB，請依照此處所述的步驟，將 [您的資料箱磁片解壓縮、連線並解除鎖定](../databox/data-box-disk-deploy-set-up.md)。

>[!NOTE]
>使用 MARS 代理程式的伺服器可能沒有 USB 埠。 在這種情況下，您可以將 Azure 資料箱磁片連線到另一部伺服器或用戶端，並將裝置的根目錄公開為網路共用。

### <a name="set-up-azure-data-box"></a>設定 Azure 資料箱

如果您已排序 Azure 資料箱實例 (高達 100 TB 的) ，請依照此處的步驟 [來設定您的資料箱實例](../databox/data-box-deploy-set-up.md)。

#### <a name="mount-your-azure-data-box-instance-as-a-local-system"></a>將您的 Azure 資料箱實例掛接為本機系統

MARS 代理程式會在本機系統內容中運作，因此需要將相同層級的許可權提供給 Azure 資料箱實例連線所在的掛接路徑。

若要確保您可以使用 NFS 通訊協定，將資料箱裝置掛接為本機系統：

1. 在已安裝 MARS 代理程式的 Windows server 上，啟用 NFS 功能的用戶端。 指定替代來源 *WIM： D： \Sources\Install.wim： 4*。
1. 將 PsExec 從 [Sysinternals](/sysinternals/downloads/psexec) 頁面下載到已安裝 MARS 代理程式的伺服器。
1. 開啟提升許可權的命令提示字元，然後以包含 *PSExec.exe* 的目錄執行下列命令，做為目前的目錄。

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

   因為前一個命令而開啟的命令視窗是在本機系統內容中。 您可以使用此命令視窗來執行步驟，以將 Azure 分頁 blob 共用掛接為 Windows server 上的網路磁碟機機。
1. 遵循 [連線 [至資料]](../databox/data-box-deploy-copy-data-via-nfs.md#connect-to-data-box) 方塊中的步驟，透過 NFS 將您的伺服器與 MARS 代理程式連線到資料箱裝置。 在本機系統命令提示字元上執行下列命令，以掛接 Azure 分頁 blob 共用。

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

   裝載共用之後，請檢查您是否可以從伺服器存取 X：。 如果可以，請繼續進行本文的下一節。

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>將初始備份資料傳輸至 Azure 資料箱裝置

1. 開啟您伺服器上的 **Microsoft Azure 備份** 應用程式。
1. 在 [ **動作** ] 窗格中，選取 [ **排程備份**]。

    ![選取排程備份](./media/offline-backup-azure-data-box/schedule-backup.png)

1. 依照 [ **排程備份嚮導]** 中的步驟執行。

1. 選取 [ **加入專案** ] 按鈕以新增專案。 將專案的總大小保留在您所訂購和接收 [的 AZURE 資料箱 SKU 所支援的大小限制](#backup-data-size-and-supported-data-box-skus) 內。

    ![將專案新增至備份](./media/offline-backup-azure-data-box/add-items.png)

1. 針對檔案 **、資料夾** 和 **系統狀態**，選取適當的備份排程和保留原則。 系統狀態僅適用于 Windows 伺服器，不適用於 Windows 用戶端。
1. 在嚮導的 [ **選擇初始備份類型 (檔案和資料夾]) ** 頁面上，選取 [ **使用 Microsoft Azure 資料箱磁片傳送** ] 選項，然後選取 **[下一步]**。

    ![選擇初始備份類型](./media/offline-backup-azure-data-box/initial-backup-type.png)

1. 使用具有 Azure 訂用帳戶擁有者存取權的使用者認證，在出現提示時登入 Azure。 成功完成之後，您應該會看到類似此頁面的頁面。

    ![建立資源並套用必要的許可權](./media/offline-backup-azure-data-box/creating-resources.png)

   然後，MARS 代理程式會在訂用帳戶中提取處於已傳遞狀態的資料箱作業。

    ![提取訂用帳戶識別碼的資料箱作業](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

1. 選取您已解除封裝、連線並解除鎖定資料箱磁片的正確資料箱訂單。 選取 [下一步]  。

    ![選取資料箱訂單](./media/offline-backup-azure-data-box/select-databox-order.png)

1. 選取 [在**資料箱裝置偵測上偵測****裝置**] 頁面。 此動作會讓 MARS 代理程式在本機連接的 Azure 資料箱磁片進行掃描，並偵測到它們。

    ![資料箱裝置偵測](./media/offline-backup-azure-data-box/databox-device-detection.png)

    如果您將 Azure 資料箱實例連接為網路共用 (因為無法使用 USB 埠，或因為您已訂購並掛接 100-TB 的資料箱裝置) ，所以偵測會在一開始就失敗。 系統會提供您輸入資料箱裝置之網路路徑的選項。

    ![輸入網路路徑](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > 提供 Azure 資料箱磁碟根目錄的網路路徑。 此目錄必須包含名稱為 *PageBlob*的目錄。
    >
    >![Azure 資料箱磁片的根目錄](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >例如，如果磁片的路徑是 `\\mydomain\myserver\disk1\` ，而 *disk1* 包含名為 *PageBlob*的目錄，則您在 MARS 代理程式的 [代理程式] 頁面上輸入的路徑就是 `\\mydomain\myserver\disk1\` 。
    >
    >如果您 [設定 Azure 資料箱 100-TB 裝置](#set-up-azure-data-box-devices)，請輸入 `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` 作為裝置的網路路徑。

1. 選取 [ **下一步**]，然後選取下一個頁面上的 **[完成]** ，使用 Azure 資料箱設定離線備份的備份和保留原則。

   下列頁面會確認已成功儲存原則。

    ![已成功儲存原則](./media/offline-backup-azure-data-box/policy-saved.png)

1. 選取上一個頁面上的 [ **關閉** ]。

1. 在 MARS 代理程式主控台的 [**動作**] 窗格中，選取 [**立即備份**]。 選取 [嚮導] 頁面上的 [ **備份** ]。

    ![立即備份嚮導](./media/offline-backup-azure-data-box/backup-now.png)

MARS 代理程式會開始將您選取的資料備份到 Azure 資料箱裝置。 此程式可能需要數小時到數天的時間。 時間長短取決於伺服器與 MARS 代理程式和 Azure 資料箱磁片之間的檔案數目和連線速度。

完成資料備份之後，您會在 MARS 代理程式上看到一個與此類似的頁面。

![顯示備份進度](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>備份後步驟

本節說明將資料備份至 Azure 資料箱磁碟成功之後所要採取的步驟。

- 遵循本文中的步驟，將 [Azure 資料箱磁片寄送至 Azure](../databox/data-box-disk-deploy-picked-up.md)。 如果您使用 Azure 資料箱 100-TB 的裝置，請遵循下列步驟，將 [Azure 資料箱裝置送至 Azure](../databox/data-box-deploy-picked-up.md)。

- 在 Azure 入口網站中[監視資料箱作業](../databox/data-box-disk-deploy-upload-verify.md)。 Azure 資料箱作業完成之後，MARS 代理程式會在下一次排程備份時，自動將資料從儲存體帳戶移至復原服務保存庫。 然後，如果復原點已成功建立，則會將備份作業標示為 *已完成* 。

    >[!NOTE]
    >MARS 代理程式會在原則建立期間，于排程的時間觸發備份。 這些作業會標示「正在等候 Azure 資料箱工作完成」，直到作業完成為止。

- MARS 代理程式成功建立對應至初始備份的復原點之後，您可以刪除儲存體帳戶或與 Azure 資料箱作業相關聯的特定內容。

## <a name="troubleshooting"></a>疑難排解

Microsoft Azure 復原服務 (MARS) 代理程式會在您的租使用者中為您建立 Azure Active Directory (Azure AD 應用程式。 當您設定離線植入原則時，此應用程式需要憑證以進行建立和上傳的驗證。 我們會使用 Azure PowerShell 來建立憑證，並將其上傳至 Azure AD 應用程式。

### <a name="problem"></a>問題

當您設定離線備份時，可能會因為 Azure PowerShell Cmdlet 中的錯誤而面臨問題。 您可能無法將多個憑證新增至 MAB 代理程式所建立的相同 Azure AD 應用程式。 如果您為相同或不同的伺服器設定離線植入原則，此問題會對您造成影響。

### <a name="verify-if-the-problem-is-caused-by-this-specific-root-cause"></a>確認問題是否由這個特定的根本原因所造成

若要查看您的問題是否與先前所述的問題相同，請執行下列其中一項步驟。

#### <a name="step-1-of-verification"></a>驗證的步驟1

當您設定離線備份時，請檢查 MAB 主控台中是否出現下列錯誤訊息。

![無法為目前的 Azure 帳戶建立離線備份原則](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2-of-verification"></a>驗證的步驟2

1. 在安裝路徑中開啟 **Temp** 資料夾。 預設的暫存資料夾路徑為 *C:\Program Files\Microsoft Azure Recovery Services Agent\Temp*。尋找 *CBUICurr* 檔案，然後開啟檔案。

1. 在 *CBUICurr* 檔案中，滾動至最後一行並檢查問題是否與此錯誤訊息中的問題相同： `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed` 。

### <a name="workaround"></a>因應措施

解決此問題的因應措施是執行下列步驟，然後重試原則設定。

#### <a name="step-1-of-workaround"></a>解決方法的步驟1

使用在將建立匯入或匯出作業的訂用帳戶上具有系統管理員存取權的不同帳戶，登入出現在 MAB UI 上的 PowerShell。

#### <a name="step-2-of-workaround"></a>解決方法的步驟2

如果沒有其他伺服器已設定離線植入，而且沒有其他伺服器相依于 `AzureOfflineBackup_<Azure User Id>` 應用程式，請刪除此應用程式。 選取**Azure 入口網站**  >  **Azure Active Directory**  >  **應用程式註冊**。

>[!NOTE]
> 檢查應用程式是否沒有設定 `AzureOfflineBackup_<Azure User Id>` 任何其他離線植入，以及其他伺服器是否相依于此應用程式。 移至**Settings**  >  [**公開金鑰**] 區段下的 [設定] 索引**鍵**。 它不應該加入任何其他的公開金鑰。 請參閱下列螢幕擷取畫面以取得參考。
>
>![公開金鑰](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="step-3"></a>步驟 3

從您嘗試為離線備份設定的伺服器中，執行下列動作。

1. 移至 [**管理電腦憑證應用程式**  >  **個人**] 索引標籤，然後尋找名稱為的憑證 `CB_AzureADCertforOfflineSeeding_<ResourceId>` 。

2. 選取憑證，以滑鼠右鍵按一下 [ **所有**工作]，然後選取 [不使用私密金鑰的 **匯出** ] （.cer 格式）。

3. 移至步驟2中所述的 Azure 離線備份應用程式。 選取 [**設定**] 索引  >  **鍵**  >  **上傳公開金鑰**。 上傳您在上一個步驟中匯出的憑證。

    ![上傳公開金鑰](./media/offline-backup-azure-data-box/upload-public-key.png)

4. 在伺服器中，在 [執行] 視窗中輸入 **regedit** 以開啟登錄。

5. 移至 registry *電腦 \ HKEY_LOCAL_MACHINE \Software\microsoft\windows Azure Backup\Config\CloudBackupProvider.* 以滑鼠右鍵按一下 [ **CloudBackupProvider**]，並以名稱加入新的字串值 `AzureADAppCertThumbprint_<Azure User Id>` 。

    >[!NOTE]
    > 若要取得 Azure 使用者識別碼，請執行下列其中一項動作：
    >
    >- 從與 Azure 連線的 PowerShell 中，執行 `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"` 命令。
    > - 移至名稱為 CurrentUserId 的登錄路徑 `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup` 。 *CurrentUserId*

6. 以滑鼠右鍵按一下上一個步驟中所新增的字串，然後選取 [ **修改**]。 在 [值] 中，提供您在步驟2中匯出之憑證的指紋。 選取 [確定]  。

7. 若要取得憑證指紋的值，請按兩下該憑證。 選取 [ **詳細資料** ] 索引標籤，直到您看到 [指紋] 欄位為止。 選取 [ **指紋**]，然後複製值。

    ![憑證的憑證指紋欄位](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>問題

如有任何問題，或對您遇到的任何問題進行說明，請聯絡 [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) 。
