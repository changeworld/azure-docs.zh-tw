---
title: 使用 Azure 資料框離線備份
description: 瞭解如何使用 Azure 資料框將大型初始備份資料從 MARS 代理離線設定為恢復服務保存庫。
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: a031a8cac357e7d212f8f6a3a5dbec749fbccc21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672958"
---
# <a name="azure-backup-offline-backup-by-using-azure-data-box"></a>使用 Azure 資料框進行 Azure 備份離線備份

可以使用[Azure 資料框](https://docs.microsoft.com/azure/databox/data-box-overview)將大型初始 Microsoft Azure 恢復服務 （MARS） 備份離線（不使用網路）設定為恢復服務保存庫。 此過程可節省時間和網路頻寬，否則將消耗大量備份資料通過高延遲網路線上移動。 此增強功能當前處於預覽狀態。 基於 Azure 資料框的離線備份提供了兩個明顯的優勢，而不是[基於 Azure 導入/匯出服務的離線備份](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export)：

* 無需採購自己的與 Azure 相容的磁片和連接器。 Azure 資料框提供與所選[資料框 SKU](https://azure.microsoft.com/services/databox/data/)關聯的磁片。
* Azure 備份 （MARS 代理） 可以直接將備份資料寫入 Azure 資料框的受支援 SKU。 此功能無需為初始備份資料預配暫存位置。 您也不需要實用程式來格式化這些資料並將其複製到磁片上。

## <a name="azure-data-box-with-the-mars-agent"></a>帶有 MARS 代理的 Azure 資料框

本文介紹如何使用 Azure 資料框將大型初始備份資料從 MARS 代理離線設定為恢復服務保存庫。

## <a name="supported-platforms"></a>支援的平台

使用 Azure 資料框從 MARS 代理中設定資料種子的過程在以下 Windows SKU 上受支援。

| **作業系統**                                 | **Sku**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| **工作站**                        |                                                              |
| Windows 10 64 位元                     | 企業版、專業版、家用版                                       |
| Windows 8.1 64 位元                    | Enterprise、Pro                                             |
| Windows 8 64 位元                      | Enterprise、Pro                                             |
| Windows 7 64 位元                      | Ultimate、Enterprise、Professional、Home Premium、Home Basic、Starter |
| **伺服器**                             |                                                              |
| Windows Server 2019 64 位元            | Standard、Datacenter、Essentials                            |
| Windows Server 2016 64 位元            | Standard、Datacenter、Essentials                            |
| Windows Server 2012 R2 64 位元         | Standard、Datacenter、Foundation                            |
| Windows Server 2012 64 位元            | Datacenter、Foundation、Standard                            |
| Windows Storage Server 2016 64 位元    | Standard、Workgroup                                         |
| Windows Storage Server 2012 R2 64 位元 | Standard、Workgroup、Essential                              |
| Windows Storage Server 2012 64 位元    | Standard、Workgroup                                         |
| Windows Server 2008 R2 SP1 64 位元     | Standard、Enterprise、Datacenter、Foundation                |
| Windows 伺服器 2008 SP2 64 位        | Standard、Enterprise、Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>備份資料大小和支援的資料框 SKU

| 每個伺服器備份資料大小（按 MARS 壓縮後）* | 受支援的 Azure 資料框 SKU                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <±7.2 TB                                                    | [Azure 資料框磁片](https://docs.microsoft.com/azure/databox/data-box-disk-overview) |
| >7.2 TB 和 <[80 TB]                                      | [Azure 資料框（100 TB）](https://docs.microsoft.com/azure/databox/data-box-overview) |

*典型壓縮率在 10% 到 20% 之間變化。 <br>
*如果您希望單個 MARS 伺服器的初始備份資料超過 80 TB，請與[AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com).

>[!IMPORTANT]
>單個伺服器的初始備份資料必須包含在單個 Azure 資料框實例或 Azure 資料盒磁片中，並且不能在同一或不同 SKU 的多個設備之間共用。 但是，Azure 資料盒設備可以包含來自多個伺服器的初始備份。

## <a name="prerequisites"></a>Prerequisites

### <a name="azure-subscription-and-required-permissions"></a>Azure 訂閱和所需許可權

* 該過程需要 Azure 訂閱。
* 該過程要求指定用於執行離線備份策略的使用者是 Azure 訂閱的擁有者。
* 資料框作業和恢復服務保存庫（需要對資料進行種子設定）需要位於同一訂閱中。
* 我們建議與 Azure 資料框作業和恢復服務保存庫關聯的目標存儲帳戶位於同一區域中。 但是，這是沒有必要的。

### <a name="get-azure-powershell-370"></a>獲取 Azure 電源外殼 3.7.0

*這是這個過程最重要的先決條件*。 在安裝 Azure PowerShell 之前，版本 3.7.0 將執行以下檢查。

#### <a name="step-1-check-the-powershell-version"></a>第 1 步：檢查電源外殼版本

1. 打開 Windows PowerShell，並運行以下命令：

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

1.  如果輸出顯示的版本高於 3.7.0，則執行"步驟 2"。 否則，請跳到"步驟 3"。

#### <a name="step-2-uninstall-the-powershell-version"></a>第 2 步：卸載 PowerShell 版本

卸載當前版本的 PowerShell。

1. 通過在 PowerShell 中運行以下命令來刪除相關模組：

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

2. 為確保成功刪除所有從屬模組，運行以下命令：

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>第 3 步：安裝 PowerShell 版本 3.7.0

驗證不存在 AzureRM 模組後，請使用以下方法之一安裝版本 3.7.0：

* 從 GitHub，使用此[連結](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)。

或者您可以：

* 在 PowerShell 視窗中運行以下命令：

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

Azure PowerShell 也可以通過使用 msi 檔進行安裝。 要將其刪除，請使用"控制台"中的 **"卸載程式**"選項卸載它。

### <a name="order-and-receive-the-data-box-device"></a>訂購並接收資料盒設備

使用 MARS 和 Azure 資料盒的離線備份過程要求資料盒設備在使用 MARS 代理觸發離線備份之前處於"已交付"狀態。 要根據您的要求訂購最合適的 SKU，請參閱[備份資料大小和支援的資料框 SKU](#backup-data-size-and-supported-data-box-skus)。 按照教程中的步驟[操作：訂購 Azure 資料盒磁片](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-ordered)以訂購和接收資料盒設備。

> [!IMPORTANT]
> 不要為**帳戶類型**選擇*Blob 存儲*。 MARS 代理需要一個帳戶，該帳戶支援頁面 Blob，在選擇*Blob 存儲*時不支援該帳戶。 在為 Azure 資料框作業創建目標存儲帳戶時，選擇**存儲 V2（通用 v2）** 作為**帳戶類型**。

![在實例詳細資訊中選擇帳戶類型](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-set-up-the-mars-agent"></a>安裝並設置 MARS 代理

1. 請確保卸載 MARS 代理的任何以前安裝。
1. 從[本網站](https://aka.ms/azurebackup_agent)下載最新的MARS代理。
1. 運行*MARSAgentInstaller.exe*，並*僅*執行[步驟，將代理安裝並註冊](https://docs.microsoft.com/azure/backup/install-mars-agent#install-and-register-the-agent)到希望存儲備份的恢復服務保存庫。

   > [!NOTE]
   > 恢復服務保存庫必須與 Azure 資料框作業處於同一訂閱中。

   在代理註冊到恢復服務保存庫後，請按照以下部分中的步驟操作。

## <a name="set-up-azure-data-box-devices"></a>設置 Azure 資料框設備

根據所訂購的 Azure 資料框 SKU，執行以下相應部分仲介紹的步驟。 這些步驟演示如何為 MARS 代理設置和準備資料盒設備，以識別和傳輸初始備份資料。

### <a name="set-up-azure-data-box-disks"></a>設置 Azure 資料框磁片

如果訂購了一個或多個 Azure 資料框磁片（每個磁片最多 8 TB），請按照此處提到的步驟[解壓縮、連接和解鎖資料框磁片](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-set-up)。

>[!NOTE]
>具有 MARS 代理的伺服器可能沒有 USB 埠。 在這種情況下，您可以將 Azure 資料盒磁片連接到另一台伺服器或用戶端，並將設備的根公開為網路共用。

### <a name="set-up-azure-data-box"></a>設置 Azure 資料框

如果訂購了 Azure 資料框實例（最多 100 TB），請按照此處的步驟[設置資料框實例](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up)。

#### <a name="mount-your-azure-data-box-instance-as-a-local-system"></a>將 Azure 資料框實例裝載為本地系統

MARS 代理在本地系統上下文中運行，因此需要向連接 Azure 資料框實例的裝載路徑提供相同級別的許可權。 

為確保可以使用 NFS 協定將資料盒設備裝載為本地系統， 請：

1. 為安裝了 MARS 代理的 Windows 伺服器上的 NFS 功能啟用用戶端。 指定備用源*WIM：D：\源\安裝.wim：4*。
1. 安裝 MARS<https://download.sysinternals.com/files/PSTools.zip>代理後，將 PSExec 從伺服器下載。
1. 打開提升的命令提示符，並將包含*PSExec.exe*的目錄作為目前的目錄運行以下命令。

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

   由於上一個命令而打開的命令視窗位於本地系統上下文中。 使用此命令視窗執行將 Azure 頁 Blob 共用裝載為 Windows 伺服器上的網路磁碟機的步驟。
1. 按照["連接到資料框](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-nfs#connect-to-data-box)"中的步驟，通過 NFS 將伺服器與 MARS 代理連接到資料盒設備。 在本地系統命令提示符上運行以下命令以裝載 Azure 頁 blob 共用。

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

   裝載共用後，請檢查是否可以從伺服器訪問 X： 。 如果可以，請繼續本文的下一部分。

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>將初始備份資料傳輸到 Azure 資料盒設備

1. 在伺服器上打開**Microsoft Azure 備份**應用程式。
1. 在 **"操作"** 窗格中，選擇 **"計畫備份**"。

    ![選擇計畫備份](./media/offline-backup-azure-data-box/schedule-backup.png)

1. 按照**計畫備份嚮導**中的步驟操作。

1. 通過選擇"**添加專案"按鈕添加專案**。 將專案的總大小保持在排序和接收[的 Azure 資料框 SKU 支援的大小限制](#backup-data-size-and-supported-data-box-skus)內。

    ![將專案添加到備份](./media/offline-backup-azure-data-box/add-items.png)

1. 為 **"檔"和"資料夾"和****"系統狀態**"選擇適當的備份計畫和保留原則。 系統狀態僅適用于 Windows 伺服器，不適用於 Windows 用戶端。
1. 在嚮導的 **"選擇初始備份類型（檔和資料夾）"** 頁上，選擇使用 Microsoft **Azure 資料框磁片進行傳輸**的選項，然後選擇 **"下一步**"。

    ![選擇初始備份類型](./media/offline-backup-azure-data-box/initial-backup-type.png)

1. 使用 Azure 訂閱上具有擁有者存取權限的使用者認證提示時，請登錄到 Azure。 成功完成之後，您應該會看到一個類似于此頁面的頁面。

    ![創建資源並應用所需的許可權](./media/offline-backup-azure-data-box/creating-resources.png)

   然後，MARS 代理獲取處於"已交付"狀態的訂閱中的資料框作業。

    ![獲取訂閱 ID 的資料框作業](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

1. 選擇已為其解壓縮、連接和解鎖資料盒磁片的正確資料框順序。 選取 [下一步]****。

    ![選擇資料框訂單](./media/offline-backup-azure-data-box/select-databox-order.png)

1. 在 **"資料箱設備檢測"** 頁上選擇 **"檢測設備**"。 此操作使 MARS 代理掃描本地附加的 Azure 資料框磁片並檢測它們。

    ![資料框設備檢測](./media/offline-backup-azure-data-box/databox-device-detection.png)

    如果將 Azure 資料盒實例連接到網路共用（由於 USB 埠不可用或由於您訂購和裝載了 100 TB 資料盒設備），則檢測最初將失敗。 您可以選擇輸入到資料盒設備的網路路徑。

    ![輸入網路路徑](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > 提供 Azure 資料框磁片根目錄的網路路徑。 此目錄必須包含名稱*PageBlob*的目錄。
    >
    >![Azure 資料框磁片的根目錄](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >`\\mydomain\myserver\disk1\`例如，如果磁片的路徑是，*並且磁片 1*包含名為*PageBlob*的目錄，則您在 MARS 代理嚮導頁上輸入的`\\mydomain\myserver\disk1\`路徑為 。
    >
    >如果[設置 Azure 資料盒 100 TB 設備](#set-up-azure-data-box-devices)，`\\<DeviceIPAddress>\<StorageAccountName>_PageBlob`請輸入為設備的網路路徑。

1. 選擇 **"下一步**"，然後在下一頁上選擇 **"完成"，** 使用 Azure 資料框使用離線備份的配置保存備份和保留原則。

   以下頁確認策略已成功保存。

    ![已成功保存策略](./media/offline-backup-azure-data-box/policy-saved.png)

1. 選擇上一頁上的 **"關閉**"。

1. 在 MARS 代理主控台的 **"操作"** 窗格中選擇 **"立即備份**"。 在嚮導頁面上選擇 **"備份**"。

    ![立即備份嚮導](./media/offline-backup-azure-data-box/backup-now.png)

MARS 代理開始備份所選資料盒設備的資料。 此過程可能需要幾個小時到幾天。 時間量取決於具有 MARS 代理和 Azure 資料盒磁片的伺服器之間的檔數量和連線速度。

資料備份完成後，您將在 MARS 代理上看到類似于此頁面的頁面。

![顯示備份進度](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>備份後步驟

本節介紹將資料備份到 Azure 資料盒磁片成功後要採取的步驟。

* 按照本文中的步驟將[Azure 資料框磁片運送到 Azure](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-picked-up)。 如果使用 Azure 資料盒 100 TB 設備，請按照以下步驟將[Azure 資料框設備運送到 Azure](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up)。

* 監視 Azure 門戶中[的資料框作業](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-upload-verify)。 Azure 資料框作業完成後，MARS 代理會在下次計畫備份時將資料從存儲帳戶移動到恢復服務保存庫。 然後，如果成功創建復原點，它將備份作業標記為 *"作業已完成*"。

    >[!NOTE]
    >MARS 代理在策略創建期間計畫的時間觸發備份。 這些作業標記"等待 Azure 資料框作業完成"，直到作業完成。

* 在 MARS 代理成功創建與初始備份對應的復原點後，可以刪除與 Azure 資料框作業關聯的存儲帳戶或特定內容。

## <a name="troubleshooting"></a>疑難排解

Microsoft Azure 備份 （MAB） 代理會為租戶中為您創建 Azure 活動目錄 （Azure AD） 應用程式。 此應用程式需要一個證書來進行身份驗證，該證書在配置離線種子設定策略時創建和上載。 我們使用 Azure PowerShell 創建證書並將其上載到 Azure AD 應用程式。

### <a name="problem"></a>問題

配置離線備份時，由於 Azure PowerShell Cmdlet 中的錯誤，可能會遇到問題。 可能無法向 MAB 代理創建的同一 Azure AD 應用程式添加多個證書。 如果為同一伺服器或其他伺服器配置離線種子設定策略，則此問題將影響您。

### <a name="verify-if-the-problem-is-caused-by-this-specific-root-cause"></a>驗證問題是否由此特定根本原因引起

要查看您的問題是否與前面描述的問題相同，請執行以下步驟之一。

#### <a name="step-1"></a>步驟 1

在配置離線備份時，請檢查 MAB 主控台中是否顯示以下錯誤訊息。

![無法為當前 Azure 帳戶創建離線備份策略](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2"></a>步驟 2

1. 打開安裝路徑中的 **"臨時"** 資料夾。 預設暫存檔案夾路徑為*C：\程式檔\微軟 Azure 恢復服務代理\Temp*。查找*CBUICurr*檔，然後打開該檔。

1. 在*CBUICurr*檔中，滾動到最後一行，並檢查問題是否與此錯誤訊息中的相同： `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed`。

### <a name="workaround"></a>因應措施

作為解決此問題的解決方法，請執行以下步驟並重試策略配置。

#### <a name="step-1"></a>步驟 1

使用具有管理員存取權限的訂閱上具有管理員存取權限的不同帳戶登錄到 MAB UI 上顯示的 PowerShell，該帳戶將創建導入或匯出作業。

#### <a name="step-2"></a>步驟 2

如果沒有其他伺服器配置了離線種子設定，並且沒有其他伺服器依賴于該應用程式`AzureOfflineBackup_<Azure User Id>`，請刪除此應用程式。 選擇**Azure 門戶** > **Azure 活動目錄** > **應用註冊**。

>[!NOTE]
> 檢查`AzureOfflineBackup_<Azure User Id>`應用程式是否未配置任何其他離線種子設定，以及沒有其他伺服器依賴于此應用程式。 轉到 **"公開金鑰**"部分下的 **"設置** > **金鑰**"。 它不應該添加任何其他公開金鑰。 請參閱以下螢幕截圖供參考。
>
>![公開金鑰](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="step-3"></a>步驟 3

從您嘗試為離線備份配置的伺服器中，執行以下操作。

1. 轉到 **"管理電腦證書應用程式** > **個人"** 選項卡，然後查找名稱 的證書`CB_AzureADCertforOfflineSeeding_<ResourceId>`。

2. 選擇證書，按右鍵 **"所有任務**"，然後選擇 .cer 格式的 **"** 匯出"而不帶私密金鑰。

3. 轉到步驟 2 中提到的 Azure 離線備份應用程式。 選擇**設置** > **鍵** > **上傳公開金鑰**。 上傳您在上一步中匯出的證書。

    ![上傳公開金鑰](./media/offline-backup-azure-data-box/upload-public-key.png)

4. 在伺服器中，通過在運行視窗中輸入**註冊來**打開註冊表。

5. 轉到註冊表*電腦[HKEY_LOCAL_MACHINE_SOFTWARE_微軟_Windows Azure 備份\配置\雲備份提供程式。* 按右鍵**CloudBackupProvider**，並添加名稱的新字串值`AzureADAppCertThumbprint_<Azure User Id>`。

    >[!NOTE]
    > 要獲取 Azure 使用者 ID，可以執行以下操作之一：
    >
    >* 從 Azure 連接的 PowerShell`Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"`運行命令。
    > * 轉到註冊表路徑*電腦\HKEY_LOCAL_MACHINE\軟體\微軟\Windows Azure 備份\DbgSettings_線上備份*的名稱為*CurrentUserId*。

6. 按右鍵上一步中添加的字串，然後選擇 **"修改**"。 在 值中，提供步驟 2 中匯出的證書的指紋。 選取 [確定]****。

7. 要獲取指紋的值，請按兩下證書。 選擇"**詳細資訊**"選項卡，然後向下滾動，直到看到指紋欄位。 選擇 **"拇指列印"** 並複製該值。

    ![證書的指紋欄位](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>問題

有關您遇到的任何問題或澄清，請聯繫[AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com)。
