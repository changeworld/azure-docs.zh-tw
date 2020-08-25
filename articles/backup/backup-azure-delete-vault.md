---
title: 刪除 Microsoft Azure 復原服務保存庫
description: 在本文中，您將瞭解如何移除相依性，然後刪除 Azure 備份復原服務保存庫。
ms.topic: conceptual
ms.date: 06/04/2020
ms.openlocfilehash: c0b75d147abba45a745f811de5e4b8ac45088bd8
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88826730"
---
# <a name="delete-an-azure-backup-recovery-services-vault"></a>刪除 Azure 備份復原服務保存庫

本文說明如何刪除 [Azure 備份](backup-overview.md) 復原服務保存庫。 它包含移除相依性，然後刪除保存庫的指示。

## <a name="before-you-start"></a>開始之前

您無法刪除具有下列任何相依性的復原服務保存庫：

- 您無法刪除包含受保護資料來源的保存庫 (例如 IaaS Vm、SQL 資料庫、Azure 檔案共用) 。
- 您無法刪除包含備份資料的保存庫。 一旦刪除備份資料後，就會進入虛刪除狀態。
- 您無法刪除包含已虛刪除狀態之備份資料的保存庫。
- 您無法刪除已註冊儲存體帳戶的保存庫。

如果您嘗試在不移除相依性的情況下刪除保存庫，您將會遇到下列其中一個錯誤訊息：

- 保存庫無法刪除，因為其中仍有既有資源。 請確定沒有與此保存庫相關聯的備份專案、受保護的伺服器或備份管理伺服器。 請先取消註冊下列與此保存庫相關聯的容器，再繼續進行刪除。

- 無法刪除復原服務保存庫，因為保存庫中有處於虛刪除狀態的備份項目。 虛刪除的專案會在刪除作業的14天后永久刪除。 請于永久刪除備份專案之後嘗試刪除保存庫，而且保存庫中沒有任何專案處於已虛刪除的狀態。 如需詳細資訊，請參閱 Azure 備份的虛 [刪除](./backup-azure-security-feature-cloud.md)。

## <a name="proper-way-to-delete-a-vault"></a>刪除保存庫的適當方式

>[!WARNING]
>下列作業是破壞性的作業，無法復原。 所有與受保護伺服器相關聯的備份資料和備份專案都會永久刪除。 請謹慎進行。

若要適當地刪除保存庫，您必須依照下列循序執行步驟：

- **步驟 1**：停用虛刪除功能。 [請參閱這裡](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) 以瞭解停用虛刪除的步驟。

- **步驟 2**：停用虛刪除之後，請檢查是否有任何先前在虛刪除狀態中剩餘的專案。 如果有專案處於虛刪除狀態，則您需要取消刪除 *並* 重新 *刪除* 。 [請遵循下列步驟](./backup-azure-security-feature-cloud.md#permanently-deleting-soft-deleted-backup-items) 來尋找虛刪除專案，並永久刪除專案。

- **步驟 3**：您必須檢查下列三個位置，以確認是否有任何受保護的專案：

  - **受雲端保護的專案**：移至保存庫儀表板功能表 > 的 **備份專案**。 您必須移除 [ **停止備份** ] 或 [ **刪除備份資料** ] 及其備份資料，以移除此處列出的所有專案。  [請遵循下列步驟](#delete-protected-items-in-the-cloud) 來移除這些專案。
  - **SQL Server 實例**：移至保存庫儀表板功能表 >**備份基礎結構**  >  **保護的伺服器**。 在 [受保護的伺服器] 中，選取要取消註冊的伺服器。 若要刪除保存庫，您必須取消註冊所有伺服器。 在受保護的伺服器上按一下滑鼠右鍵，然後選取 [ **取消註冊**]。
  - **MARS 受保護的伺服器**：移至保存庫儀表板功能表 >**備份基礎結構**  >  **保護的伺服器**。 如果您有 MARS 受保護的伺服器，則必須連同備份資料一併刪除此處列出的所有專案。 [請遵循下列步驟](#delete-protected-items-on-premises) 來刪除 MARS 受保護的伺服器。
  - **MABS 或 DPM 管理伺服器**：移至保存庫儀表板功能表 >**備份基礎結構**  >  **備份管理伺服器**。 如果您有 DPM 或 Azure 備份伺服器 (MABS) ，則此處所列的所有專案都必須刪除或取消註冊，以及它們的備份資料。 [請遵循下列步驟](#delete-protected-items-on-premises) 來刪除管理伺服器。

- **步驟 4**：您必須確定已刪除所有已註冊的儲存體帳戶。 移至保存庫儀表板功能表 >**備份基礎結構**  >  **儲存體帳戶**。 如果您在這裡列出儲存體帳戶，則必須取消註冊所有這些帳戶。 若要瞭解如何取消註冊帳戶，請參閱 [取消註冊儲存體帳戶](manage-afs-backup.md#unregister-a-storage-account)。

完成這些步驟之後，您可以繼續 [刪除保存庫](#delete-the-recovery-services-vault)。

如果您在內部部署或雲端沒有任何受保護的專案，但仍收到保存庫刪除錯誤，請[使用 Azure Resource Manager 來執行刪除復原服務保存庫](#delete-the-recovery-services-vault-by-using-azure-resource-manager)中的步驟

## <a name="delete-protected-items-in-the-cloud"></a>刪除雲端中的受保護專案

首先，請閱讀 [ **[開始之前](#before-you-start)** ] 區段，以瞭解相依性和保存庫刪除程式。

若要停止保護並刪除備份資料，請執行下列步驟：

1. 從入口網站移至 [復原 **服務保存庫**]，然後移至 [ **備份專案**]。 然後，選擇雲端中受保護的專案 (例如，Azure 虛擬機器、Azure 儲存體 [Azure 檔案儲存體 service] 或 Azure 虛擬機器上的 SQL Server) 。

    ![選取備份類型。](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. 以滑鼠右鍵按一下以選取備份專案。 根據備份專案是否受保護而定，功能表會顯示 [ **停止備份** ] 窗格或 [ **刪除備份資料** ] 窗格。

    - 如果出現 [ **停止備份** ] 窗格，請從下拉式功能表中選取 [ **刪除備份資料** ]。 輸入備份專案的名稱 (此欄位區分大小寫) ，然後從下拉式功能表中選取原因。 輸入您的批註（如果有的話）。 然後，選取 [ **停止備份**]。

        ![[停止備份] 窗格。](./media/backup-azure-delete-vault/stop-backup-item.png)

    - 如果出現 [ **刪除備份資料** ] 窗格，請輸入備份專案的名稱 (此欄位區分大小寫) ，然後從下拉式功能表中選取原因。 輸入您的批註（如果有的話）。 然後，選取 [刪除]。

         ![[刪除備份資料] 窗格。](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

3. 檢查 **通知** 圖示： ![ 通知圖示。](./media/backup-azure-delete-vault/messages.png) 程式完成之後，服務會顯示下列訊息： *停止備份並刪除*「*備份專案」* 的備份資料。 *已成功完成操作*。
4. 在 [**備份專案**]**功能表上選取**[重新整理]，確定已刪除備份專案。

      ![[刪除備份專案] 頁面。](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>刪除內部部署的受保護專案

首先，請閱讀 [ **[開始之前](#before-you-start)** ] 區段，以瞭解相依性和保存庫刪除程式。

1. 從保存庫的 [儀表板] 功能表選取 [ **備份基礎結構**]。
2. 根據您的內部部署案例，選擇下列其中一個選項：

      - 若為 MARS，請選取 [ **受保護的伺服器** ]，然後  **Azure 備份代理程式**]。 然後，選取您要刪除的伺服器。

        ![針對 MARS，請選取您的保存庫以開啟其儀表板。](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - 若為 MABS 或 DPM，請選取 [ **備份管理伺服器**]。 然後，選取您要刪除的伺服器。

          ![若為 MABS 或 DPM，請選取您的保存庫以開啟其儀表板。](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. [ **刪除** ] 窗格隨即出現，並顯示警告訊息。

     ![刪除窗格。](./media/backup-azure-delete-vault/delete-protected-server.png)

     檢查警告訊息和 [同意] 核取方塊中的指示。
    > [!NOTE]
    >
    >- 如果受保護的伺服器與 Azure 服務同步，而且有備份專案存在，[同意] 核取方塊會顯示相依備份專案的數目，以及用來查看備份專案的連結。
    >- 如果受保護的伺服器未與 Azure 服務進行同步處理，且備份專案不存在，則 [同意] 核取方塊只會顯示備份專案的數目。
    >- 如果沒有備份專案，[同意] 核取方塊將會要求刪除。

4. 選取 [同意] 核取方塊，然後選取 [ **刪除**]。

5. 檢查 **通知** 圖示是否 ![ 刪除備份資料 ](./media/backup-azure-delete-vault/messages.png) 。 作業完成之後，服務會顯示訊息： *停止備份並刪除「備份專案」的備份資料。* *已成功完成操作*。
6. 在 [**備份專案**]**功能表上選取**[重新整理]，以確定已刪除備份專案。

>[!NOTE]
>如果您從包含相依性的入口網站中刪除內部部署受保護的專案，您會收到一則警告，指出「刪除伺服器的註冊是破壞性作業，而且無法復原。 還原資料) 所需的所有備份資料 (復原點，以及與受保護伺服器相關聯的備份專案將會永久刪除。」

完成此程式之後，您可以從管理主控台刪除備份專案：

- [從 MARS 管理主控台刪除備份專案](#delete-backup-items-from-the-mars-management-console)
- [從 MABS 或 DPM 管理主控台刪除備份專案](#delete-backup-items-from-the-mabs-or-dpm-management-console)

### <a name="delete-backup-items-from-the-mars-management-console"></a>從 MARS 管理主控台刪除備份專案

>[!NOTE]
>如果您已刪除或遺失來源電腦，但未停止備份，則下次排定的備份將會失敗。 舊的復原點會根據原則過期，但永遠會保留最後一個復原點，直到您停止備份並刪除資料為止。 您可以遵循本節中的步驟來執行 [此動作](#delete-protected-items-on-premises)。

1. 開啟 MARS 管理主控台，移至 [ **動作** ] 窗格，然後選取 [ **排程備份**]。
2. 在 [ **修改或停止排定的備份** ] 頁面上，選取 [ **停止使用此備份排程]，然後刪除所有儲存的備份**。 然後，選取 [下一步]  。

    ![修改或停止已排程的備份。](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. 從 [ **停止排程備份** ] 頁面上，選取 **[完成]**。

    ![停止已排程的備份。](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. 系統會提示您輸入安全性 PIN 碼 (個人識別碼) ，您必須手動產生此識別碼。 若要這樣做，請先登入 Azure 入口網站。
5. 移至 [復原**服務保存庫**  >  **設定**  >  **屬性**]。
6. 在 [ **安全性 PIN**] 底下，選取 [ **產生**]。 複製此 PIN。 PIN 只適用于五分鐘。
7. 在管理主控台中，貼上 PIN，然後選取 **[確定]**。

    ![產生安全性 PIN 碼。](./media/backup-azure-delete-vault/security-pin.png)

8. 在 [ **修改備份進度** ] 頁面中，會出現下列訊息： *已刪除的備份資料會保留14天。在這段時間之後，將會永久刪除備份資料。*  

    ![刪除備份基礎結構。](./media/backup-azure-delete-vault/deleted-backup-data.png)

刪除內部部署備份專案之後，請遵循入口網站中的後續步驟。

### <a name="delete-backup-items-from-the-mabs-or-dpm-management-console"></a>從 MABS 或 DPM 管理主控台刪除備份專案

>[!NOTE]
>如果您已刪除或遺失來源電腦，但未停止備份，則下次排定的備份將會失敗。 舊的復原點會根據原則過期，但永遠會保留最後一個復原點，直到您停止備份並刪除資料為止。 您可以遵循本節中的步驟來執行 [此動作](#delete-protected-items-on-premises)。

您可以使用兩種方法來刪除 MABS 或 DPM 管理主控台中的備份專案。

#### <a name="method-1"></a>方法 1

若要停止保護並刪除備份資料，請執行下列步驟：

1. 開啟 DPM 管理主控台，然後選取巡覽列上的 [ **保護** ]。
2. 在 [顯示] 窗格中，選取要移除的保護群組成員。 以滑鼠右鍵按一下以選取 [ **停止保護群組成員** ] 選項。
3. 從 [ **停止保護** ] 對話方塊中，選取 [ **刪除受保護的資料**]，然後選取 [ **線上刪除儲存體** ] 核取方塊。 然後，選取 [ **停止保護**]。

    ![選取 [停止保護] 窗格中的 [刪除受保護的資料]。](./media/backup-azure-delete-vault/delete-storage-online.png)

    受保護的成員狀態會變更為「非作用中 *複本可用*」。

4. 在非作用中保護群組上按一下滑鼠右鍵，然後選取 [ **移除非**作用中保護]。

    ![移除非作用中保護。](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. 從 [ **刪除非** 作用中保護] 視窗中，選取 [ **刪除線上存放裝置** ] 核取方塊，然後選取 **[確定]**。

    ![刪除線上儲存體。](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

#### <a name="method-2"></a>方法 2

開啟 [ **MABS 管理** ] 或 [ **DPM 管理** ] 主控台。 在 [ **選取資料保護方式**] 下，清除 [  **我想要線上保護** ] 核取方塊。

  ![選取資料保護方式。](./media/backup-azure-delete-vault/data-protection-method.png)

刪除內部部署備份專案之後，請遵循入口網站中的後續步驟。

## <a name="delete-the-recovery-services-vault"></a>刪除復原服務保存庫

1. 移除所有相依性之後，請移至保存庫功能表中的 [ **基本** ] 窗格。
2. 確認沒有任何備份專案、備份管理伺服器或已複寫的專案列出。 如果專案仍出現在保存庫中，請參閱 [ [開始之前](#before-you-start) ] 區段。

3. 當保存庫中沒有任何專案時，請選取保存庫儀表板上的 [ **刪除** ]。

    ![選取保存庫儀表板上的 [刪除]。](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. 選取 **[是]** 以確認您要刪除保存庫。 保存庫已刪除。 入口網站會回到 [ **新增** 服務] 功能表。

## <a name="delete-the-recovery-services-vault-by-using-powershell"></a>使用 PowerShell 刪除復原服務保存庫

首先，請閱讀 [ **[開始之前](#before-you-start)** ] 區段，以瞭解相依性和保存庫刪除程式。

若要停止保護並刪除備份資料：

- 如果您使用 Azure Vm 備份中的 SQL 並啟用 SQL 實例的自動保護，請先停用自動保護。

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

  [深入瞭解](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection) 如何停用受 Azure 備份保護專案的保護。

- 停止保護並刪除雲端 (中所有備份保護專案的資料，例如： IaaS VM、Azure 檔案共用等) ：

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

    [深入瞭解](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection)  關於停用受備份保護之專案的保護。

- 針對使用 Azure 備份代理程式保護的內部部署檔案和資料夾 (MARS) 備份至 Azure，請使用下列 PowerShell 命令來刪除每個 MARS PowerShell 模組中的備份資料：

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    之後會出現下列提示：

    *Microsoft Azure 備份確定要移除此備份原則嗎？已刪除的備份資料會保留14天。在這段時間之後，將會永久刪除備份資料。 <br/> [Y] 是 [A] 全部都是 [N] 否 [L] 否全部 [S] 暫停 [？]Help (預設值為 "Y" ) ：*

- 針對使用 MABS 保護的內部部署機器 (Microsoft Azure 備份 Server) 或 DPM (System Center Data Protection Manager) 至 Azure，請使用下列命令來刪除 Azure 中的備份資料。

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    之後會出現下列提示：

   *Microsoft Azure 備份* 確定要移除此備份原則嗎？ 已刪除的備份資料會保留14天。 在這段時間之後，備份資料將會永久刪除。 <br/>
   [Y] 是 [A] 全部都是 [N] 否 [L] 否全部 [S] 暫停 [？]Help (預設值為 "Y" ) :*

刪除備份的資料後，請取消註冊任何內部部署容器和管理伺服器。

- 針對使用 Azure 備份代理程式保護的內部部署檔案和資料夾 (MARS) 備份至 Azure：

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

    [深入瞭解](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer) 如何從保存庫取消註冊 Windows Server 或其他容器。

- 針對使用 MABS 保護的內部部署機器 (Microsoft Azure 備份 Server) 或 DPM 至 Azure (System Center Data Protection 管理：

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

    [深入瞭解](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer) 如何從保存庫取消註冊備份管理容器。

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

[深入瞭解](/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault) 如何刪除復原服務保存庫。

## <a name="delete-the-recovery-services-vault-by-using-cli"></a>使用 CLI 刪除復原服務保存庫

首先，請閱讀 [ **[開始之前](#before-you-start)** ] 區段，以瞭解相依性和保存庫刪除程式。

> [!NOTE]
> Azure 備份 CLI 目前僅支援管理 Azure VM 備份，因此只有在保存庫包含 Azure VM 備份時，才會使用下列命令來刪除保存庫。 如果保存庫包含 Azure Vm 以外的任何類型的備份專案，您就無法使用 Azure 備份 CLI 來刪除保存庫。

若要刪除現有的復原服務保存庫，請執行下列步驟：

- 停止保護並刪除備份資料

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

    如需詳細資訊，請參閱這 [篇文章](/cli/azure/backup/vault?view=azure-cli-latest)

## <a name="delete-the-recovery-services-vault-by-using-azure-resource-manager"></a>使用 Azure Resource Manager 刪除復原服務保存庫

只有在移除所有相依性，但您仍收到保存 *庫刪除錯誤*時，才建議使用這個選項來刪除復原服務保存庫。 請嘗試下列任何或所有秘訣：

- 從保存庫功能表的 [ **基本** ] 窗格中，確認沒有列出任何備份專案、備份管理伺服器或已複寫的專案。 如果有備份專案，請參閱中的 [ [開始之前](#before-you-start) ] 區段。
- 請嘗試再次 [從入口網站刪除保存庫](#delete-the-recovery-services-vault) 。
- 如果所有的相依性都已移除，但您仍收到保存 *庫刪除錯誤*，請使用 ARMClient 工具來執行下列步驟， (在) 附注之後。

1. 移至 [chocolatey.org](https://chocolatey.org/) 以下載並安裝 chocolatey。 然後，執行下列命令來安裝 ARMClient：

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. 登入您的 Azure 帳戶，然後執行下列命令：

    `ARMClient.exe login [environment name]`

3. 在 Azure 入口網站中，針對您要刪除的保存庫收集訂用帳戶識別碼和資源組名。

如需 ARMClient 命令的詳細資訊，請參閱 [ARMCLIENT 讀我檔案](https://github.com/projectkudu/ARMClient/blob/master/README.md)。

### <a name="use-the-azure-resource-manager-client-to-delete-a-recovery-services-vault"></a>使用 Azure Resource Manager 用戶端來刪除復原服務保存庫

1. 使用您的訂用帳戶識別碼、資源組名和保存庫名稱來執行下列命令。 如果您沒有任何相依性，則會在您執行下列命令時刪除保存庫：

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<Recovery Services vault name>?api-version=2015-03-15
   ```

2. 如果保存庫不是空的，您會收到下列錯誤訊息： *因為此保存庫中有現有的資源，所以無法刪除保存庫。* 若要移除保存庫中的受保護專案或容器，請執行下列命令：

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<Recovery Services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. 在 Azure 入口網站中，請確定已刪除保存庫。

## <a name="next-steps"></a>後續步驟

[瞭解復原服務保存庫](backup-azure-recovery-services-vault-overview.md) 
[瞭解如何監視和管理復原服務保存庫](backup-azure-manage-windows-server.md)
