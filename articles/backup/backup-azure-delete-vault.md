---
title: 刪除 Microsoft Azure 復原服務保存庫
description: 在本文中，瞭解如何移除相依性，然後刪除 Azure 備份復原服務保存庫。
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 5fcf8004cd5792b30ec57537d5d8ab0bc085dfb3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82183750"
---
# <a name="delete-an-azure-backup-recovery-services-vault"></a>刪除 Azure 備份復原服務保存庫

本文說明如何刪除[Azure 備份](backup-overview.md)復原服務保存庫。 其中包含移除相依性，然後刪除保存庫的指示。

## <a name="before-you-start"></a>在您開始使用 Intune 之前

您無法刪除具有相依性的復原服務保存庫（例如受保護的伺服器或備份管理伺服器）與其相關聯。

- 包含備份資料的保存庫無法刪除（即使已停止保護，但仍保留備份資料）。

- 如果您刪除包含相依性的保存庫，則會出現下列訊息：

  ![刪除保存庫錯誤。](./media/backup-azure-delete-vault/error.png)

- 如果您從包含相依性的入口網站刪除內部部署受保護專案，就會出現警告訊息：

  ![刪除受保護的伺服器錯誤。](./media/backup-azure-delete-vault/error-message.jpg)

- 如果備份專案處於虛刪除狀態，則會出現下列警告訊息，而且您必須等到它們永久刪除為止。 如需詳細資訊，請參閱這[篇文章](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud)。

   ![刪除保存庫錯誤。](./media/backup-azure-delete-vault/error-message-soft-delete.png)

- 無法刪除具有已註冊之儲存體帳戶的保存庫。 若要瞭解如何取消註冊帳戶，請參閱[取消註冊儲存體帳戶](manage-afs-backup.md#unregister-a-storage-account)。
  
若要刪除保存庫，請選擇符合您設定的案例，並遵循建議的步驟：

狀況 | 移除相依性以刪除保存庫的步驟 |
-- | --
我有使用 Azure 備份代理程式保護的內部部署檔案和資料夾，並備份至 Azure | 執行[從 MARS 管理主控台刪除備份專案](#delete-backup-items-from-the-mars-management-console)中的步驟
我有使用 MABS （Microsoft Azure 備份 Server）或 DPM （System Center Data Protection Manager）保護的內部部署機器至 Azure | 執行[從 MABS 管理主控台刪除備份專案](#delete-backup-items-from-the-mabs-management-console)中的步驟
我在雲端中有受保護的專案（例如，laaS 虛擬機器或 Azure 檔案儲存體共用）  | 執行[刪除雲端中受保護專案](#delete-protected-items-in-the-cloud)中的步驟
我在內部部署和雲端都有受保護的專案 | 依照下列循序執行下列所有章節中的步驟： <br> 1.[刪除雲端中受保護的專案](#delete-protected-items-in-the-cloud)<br> 2.[從 MARS 管理主控台刪除備份專案](#delete-backup-items-from-the-mars-management-console) <br> 3.[從 MABS 管理主控台刪除備份專案](#delete-backup-items-from-the-mabs-management-console)
我在內部部署或雲端沒有任何受保護的專案;不過，我仍在取得保存庫刪除錯誤 | 執行刪除復原服務保存庫中的步驟，[方法是使用 Azure Resource Manager](#delete-the-recovery-services-vault-by-using-azure-resource-manager) <br><br> 確定沒有向保存庫註冊的儲存體帳戶。 若要瞭解如何取消註冊帳戶，請參閱[取消註冊儲存體帳戶](manage-afs-backup.md#unregister-a-storage-account)。

## <a name="delete-protected-items-in-the-cloud"></a>刪除雲端中受保護的專案

首先，閱讀 [**[開始之前](#before-you-start)**] 區段，以瞭解相依性和保存庫刪除程式。

若要停止保護並刪除備份資料，請執行下列步驟：

1. 在入口網站中，移至 [復原**服務保存庫**]，然後移至 [**備份專案**]。 然後，選擇雲端中的受保護專案（例如 Azure 虛擬機器、Azure 儲存體 [Azure 檔案儲存體服務] 或 Azure 虛擬機器上的 SQL Server）。

    ![選取備份類型。](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. 以滑鼠右鍵按一下以選取備份專案。 根據備份專案是否受保護而定，功能表會顯示 [**停止備份**] 窗格或 [**刪除備份資料**] 窗格。

    - 如果出現 [**停止備份**] 窗格，請從下拉式功能表中選取 [**刪除備份資料**]。 輸入備份專案的名稱（此欄位會區分大小寫），然後從下拉式功能表中選取原因。 輸入您的批註（如果有的話）。 然後，選取 [**停止備份**]。

        ![[停止備份] 窗格。](./media/backup-azure-delete-vault/stop-backup-item.png)

    - 如果出現 [**刪除備份資料**] 窗格，請輸入備份專案的名稱（此欄位會區分大小寫），然後從下拉式功能表中選取原因。 輸入您的批註（如果有的話）。 然後選取 [**刪除**]。

         ![[刪除備份資料] 窗格。](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

3. 檢查**通知**圖示： ![通知圖示。](./media/backup-azure-delete-vault/messages.png) 在此程式完成之後，服務會顯示下列訊息： [*正在停止備份並刪除備份資料] 的*[備份專案 *]*。 *已成功完成*作業。
4. 選取 [**備份專案**] 功能表**上的 [** 重新整理]，確認已刪除備份專案。

      ![[刪除備份專案] 頁面。](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>刪除內部部署的受保護專案

首先，閱讀 [**[開始之前](#before-you-start)**] 區段，以瞭解相依性和保存庫刪除程式。

1. 從保存庫的 [儀表板] 功能表，選取 [**備份基礎結構**]。
2. 視您的內部部署案例而定，選擇下列其中一個選項：

      - 若是 MARS，請選取 [**受保護的伺服器**]，然後**Azure 備份代理程式**]。 然後，選取您想要刪除的伺服器。

        ![若是 MARS，請選取您的保存庫以開啟其儀表板。](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - 針對 MABS 或 DPM，請選取 [**備份管理伺服器**]。 然後，選取您想要刪除的伺服器。

          ![針對 [MABS]，選取您的保存庫以開啟其儀表板。](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. [**刪除**] 窗格隨即出現，並顯示警告訊息。

     ![[刪除] 窗格。](./media/backup-azure-delete-vault/delete-protected-server.png)

     檢查警告訊息和 [同意] 核取方塊中的指示。
    > [!NOTE]
    >
    >- 如果受保護的伺服器與 Azure 服務同步處理，且有備份專案存在，同意核取方塊會顯示相依的備份專案數目，以及用來查看備份專案的連結。
    >- 如果受保護的伺服器未與 Azure 服務同步處理，而且存在備份專案，則 [同意] 核取方塊只會顯示備份專案的數目。
    >- 如果沒有備份專案，[同意] 核取方塊會要求刪除。

4. 選取 [同意] 核取方塊，然後選取 [**刪除**]。

5. 檢查**通知**圖示![[刪除備份資料](./media/backup-azure-delete-vault/messages.png)]。 作業完成後，服務會顯示訊息： [*正在停止備份並刪除備份資料] 的 [備份專案]。* *已成功完成*作業。
6. 選取 [**備份專案**] 功能表**上的 [** 重新整理]，確認已刪除備份專案。

完成此程式之後，您可以從管理主控台刪除備份專案：

- [從 MARS 管理主控台刪除備份專案](#delete-backup-items-from-the-mars-management-console)
- [從 MABS 管理主控台刪除備份專案](#delete-backup-items-from-the-mabs-management-console)

### <a name="delete-backup-items-from-the-mars-management-console"></a>從 MARS 管理主控台刪除備份專案

1. 開啟 MARS 管理主控台，移至 [**動作**] 窗格，然後選取 [**排程備份**]。
2. 從 [**修改或停止排程備份**] 頁面，選取 [**停止使用此備份排程] 並刪除所有儲存的備份**。 然後，選取 [下一步]  。

    ![修改或停止排定的備份。](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. 從 [**停止排程的備份**] 頁面，選取 **[完成]**。

    ![停止排程的備份。](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. 系統會提示您輸入您必須手動產生的安全性 PIN 碼（個人識別碼）。 若要這麼做，請先登入 Azure 入口網站。
5. 移至 [復原**服務保存庫** > ] [**設定** > ] [**屬性**]。
6. 在 [**安全性 PIN**] 底下，選取 [**產生**]。 複製此 PIN。 PIN 僅適用于五分鐘。
7. 在管理主控台中，貼上 PIN，然後選取 **[確定]**。

    ![產生安全性 PIN 碼。](./media/backup-azure-delete-vault/security-pin.png)

8. 在 [**修改備份進度**] 頁面中，會出現下列訊息：*已刪除的備份資料將會保留14天。在這段時間之後，備份資料將會永久刪除。*  

    ![刪除備份基礎結構。](./media/backup-azure-delete-vault/deleted-backup-data.png)

刪除內部部署備份專案之後，請遵循入口網站中的後續步驟。

### <a name="delete-backup-items-from-the-mabs-management-console"></a>從 MABS 管理主控台刪除備份專案

有兩種方法可用來從 MABS 管理主控台刪除備份專案。

#### <a name="method-1"></a>方法 1

若要停止保護並刪除備份資料，請執行下列步驟：

1. 開啟 [DPM 管理主控台]，然後選取導覽列上的 [**保護**]。
2. 在 [顯示] 窗格中，選取要移除的保護群組成員。 以滑鼠右鍵按一下以選取 [**停止保護群組成員**] 選項。
3. 從 [**停止保護**] 對話方塊中，選取 [**刪除受保護的資料**]，然後選取 [**線上刪除存放裝置**] 核取方塊。 然後，選取 [**停止保護**]。

    ![從 [停止保護] 窗格中選取 [刪除受保護的資料]。](./media/backup-azure-delete-vault/delete-storage-online.png)

    受保護的成員狀態會變更為 [*非使用中複本*]。

4. 以滑鼠右鍵按一下非作用中的保護群組，然後選取 [**移除非**作用中保護]。

    ![移除非作用中保護。](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. 從 [**刪除非**作用中保護] 視窗中，選取 [**刪除線上存放裝置**] 核取方塊，然後選取 **[確定]**。

    ![刪除線上存放裝置。](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

#### <a name="method-2"></a>方法 2

開啟**MABS 管理**主控台。 在 [**選取資料保護方式**] 下，清除 [**我想要線上保護**] 核取方塊。

  ![選取資料保護方式。](./media/backup-azure-delete-vault/data-protection-method.png)

刪除內部部署備份專案之後，請遵循入口網站中的後續步驟。

## <a name="delete-the-recovery-services-vault"></a>刪除復原服務保存庫

1. 移除所有相依性之後，請在 [保存庫] 功能表中，移至 [**基本**] 窗格。
2. 確認沒有任何 [備份專案]、[備份管理伺服器] 或 [複寫的專案] 已列出。 如果專案仍然出現在保存庫中，請參閱[開始之前](#before-you-start)一節。

3. 當保存庫中沒有其他專案時，請選取保存庫儀表板上的 [**刪除**]。

    ![選取保存庫儀表板上的 [刪除]。](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. 選取 **[是]** ，確認您想要刪除保存庫。 已刪除保存庫。 入口網站會回到 [**新增**服務] 功能表。

## <a name="delete-the-recovery-services-vault-by-using-powershell"></a>使用 PowerShell 刪除復原服務保存庫

首先，閱讀 [**[開始之前](#before-you-start)**] 區段，以瞭解相依性和保存庫刪除程式。

若要停止保護並刪除備份資料：

- 如果您使用 Azure Vm 中的 SQL 備份並啟用 SQL 實例的自動保護，請先停用自動保護。

    ```PowerShell
        Disable-AzRecoveryServicesBackupAutoProtection
           [-InputItem] <ProtectableItemBase>
           [-BackupManagementType] <BackupManagementType>
           [-WorkloadType] <WorkloadType>
           [-PassThru]
           [-VaultId <String>]
           [-DefaultProfile <IAzureContextContainer>]
           [-WhatIf]
           [-Confirm]
           [<CommonParameters>]
    ```

  [深入瞭解](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection?view=azps-2.6.0)如何停用受 Azure 備份保護專案的保護。

- 停止保護並刪除雲端中所有受備份保護專案的資料（例如 laaS VM、Azure 檔案共用等）：

    ```PowerShell
       Disable-AzRecoveryServicesBackupProtection
       [-Item] <ItemBase>
       [-RemoveRecoveryPoints]
       [-Force]
       [-VaultId <String>]
       [-DefaultProfile <IAzureContextContainer>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
    ```

    [深入瞭解](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-2.6.0&viewFallbackFrom=azps-2.5.0)停用受備份保護專案的保護。 

- 針對使用備份至 Azure Azure 備份代理程式（MARS）保護的內部部署檔案和資料夾，請使用下列 PowerShell 命令來刪除每個 MARS PowerShell 模組中的已備份資料：

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    出現下列提示的文章：

    *Microsoft Azure 備份您確定要移除此備份原則嗎？已刪除的備份資料將會保留14天。在這段時間之後，備份資料將會永久刪除。<br/> [Y] 是 [A] 全部都是 [N] 否 [L] 否全部 [S] 暫停 [？]說明（預設為 "Y"）：*

- 對於使用 MABS （Microsoft Azure 備份 Server）或 DPM 至 Azure （System Center Data Protection Manager）保護的內部部署機器，請使用下列命令來刪除 Azure 中的備份資料。

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    出現下列提示的文章：

   *Microsoft Azure 備份*您確定要移除此備份原則嗎？ 已刪除的備份資料將會保留14天。 在這段時間之後，備份資料將會永久刪除。 <br/>
   [Y] 是 [A] 全部都是 [N] 否 [L] 否全部 [S] 暫停 [？]說明（預設為 "Y"）:*

刪除備份的資料之後，請取消註冊任何內部部署容器和管理伺服器。

- 針對使用 Azure 備份代理程式（MARS）備份至 Azure 的內部部署檔案和資料夾：

    ```PowerShell
    Unregister-AzRecoveryServicesBackupContainer
              [-Container] <ContainerBase>
              [-PassThru]
              [-VaultId <String>]
              [-DefaultProfile <IAzureContextContainer>]
              [-WhatIf]
              [-Confirm]
              [<CommonParameters>]
    ```

    [深入瞭解](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0)如何從保存庫取消註冊 Windows Server 或其他容器。

- 若為使用 MABS （Microsoft Azure 備份 Server）或 DPM 保護至 Azure 的內部部署機器（System Center 資料保護管理：

    ```PowerShell
        Unregister-AzRecoveryServicesBackupManagementServer
          [-AzureRmBackupManagementServer] <BackupEngineBase>
          [-PassThru]
          [-VaultId <String>]
          [-DefaultProfile <IAzureContextContainer>]
          [-WhatIf]
          [-Confirm]
          [<CommonParameters>]
    ```

    [深入瞭解](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0)從保存庫取消註冊備份管理容器。

在永久刪除備份的資料並取消註冊所有容器之後，請繼續刪除保存庫。

刪除復原服務保存庫：

   ```PowerShell
       Remove-AzRecoveryServicesVault
      -Vault <ARSVault>
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
   ```

[深入瞭解](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault)如何刪除復原服務保存庫。

## <a name="delete-the-recovery-services-vault-by-using-cli"></a>使用 CLI 刪除復原服務保存庫

首先，閱讀 [**[開始之前](#before-you-start)**] 區段，以瞭解相依性和保存庫刪除程式。

> [!NOTE]
> 目前，Azure 備份 CLI 僅支援管理 Azure VM 備份，因此只有在保存庫包含 Azure VM 備份時，才能刪除保存庫的下列命令才有效。 如果保存庫包含 Azure Vm 以外類型的任何備份專案，您就無法使用 Azure 備份 CLI 來刪除保存庫。

若要刪除現有的復原服務保存庫，請執行下列步驟：

- 若要停止保護並刪除備份資料

    ```azurecli
    az backup protection disable --container-name
                             --item-name
                             [--delete-backup-data {false, true}]
                             [--ids]
                             [--resource-group]
                             [--subscription]
                             [--vault-name]
                             [--yes]
    ```

    如需詳細資訊，請參閱這 [篇文章](/cli/azure/backup/protection#az-backup-protection-disable)。

- 刪除現有的復原服務保存庫：

    ```azurecli
    az backup vault delete [--force]
                       [--ids]
                       [--name]
                       [--resource-group]
                       [--subscription]
                       [--yes]
    ```

    如需詳細資訊，請參閱這 [篇文章](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest)

## <a name="delete-the-recovery-services-vault-by-using-azure-resource-manager"></a>使用 Azure Resource Manager 刪除復原服務保存庫

只有當移除所有相依性，而且您仍在取得保存*庫刪除錯誤*時，才建議使用此選項來刪除復原服務保存庫。 請嘗試下列任何或所有秘訣：

- 從保存庫功能表的 [**基本**] 窗格中，確認沒有任何 [備份專案]、[備份管理伺服器] 或 [複寫的專案] 已列出。 如果有備份專案，請參閱[開始之前](#before-you-start)一節。
- 再次嘗試[從入口網站刪除保存庫](#delete-the-recovery-services-vault)。
- 如果移除所有相依性，而您仍在取得保存*庫刪除錯誤*，請使用 ARMClient 工具來執行下列步驟（在便箋之後）。

1. 請移至[chocolatey.org](https://chocolatey.org/)以下載並安裝 chocolatey。 然後，執行下列命令來安裝 ARMClient：

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. 登入您的 Azure 帳戶，然後執行下列命令：

    `ARMClient.exe login [environment name]`

3. 在 [Azure 入口網站中，收集您想要刪除之保存庫的訂用帳戶識別碼和資源組名。

如需 ARMClient 命令的詳細資訊，請參閱[ARMCLIENT 讀我檔案](https://github.com/projectkudu/ARMClient/blob/master/README.md)。

### <a name="use-the-azure-resource-manager-client-to-delete-a-recovery-services-vault"></a>使用 Azure Resource Manager 用戶端來刪除復原服務保存庫

1. 使用您的訂用帳戶識別碼、資源組名和保存庫名稱來執行下列命令。 如果您沒有任何相依性，當您執行下列命令時，會刪除保存庫：

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```

2. 如果保存庫不是空的，您會收到下列錯誤訊息：*因為此保存庫中有現有的資源，所以無法刪除保存庫。* 若要移除保存庫中受保護的專案或容器，請執行下列命令：

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. 在 Azure 入口網站中，請確定已刪除保存庫。

## <a name="next-steps"></a>後續步驟

[瞭解復原服務保存庫](backup-azure-recovery-services-vault-overview.md)<br/>
[瞭解如何監視和管理復原服務保存庫](backup-azure-manage-windows-server.md)
