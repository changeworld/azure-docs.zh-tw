---
title: 刪除 Microsoft Azure 恢復服務保存庫
description: 在本文中，瞭解如何刪除依賴項，然後刪除 Microsoft Azure 備份恢復服務 （MARS） 保存庫。
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: f33f52048729b50015ba86db71118b9a21e1a2fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500399"
---
# <a name="delete-an-azure-backup-recovery-services-vault"></a>刪除 Azure 備份恢復服務保存庫

本文介紹如何刪除 Microsoft [Azure 備份](backup-overview.md)恢復服務 （MARS） 保存庫。 它包含刪除依賴項然後刪除保存庫的說明。

## <a name="before-you-start"></a>開始之前

不能刪除具有依賴項（如受保護伺服器或備份管理伺服器）的恢復服務保存庫。

- 無法刪除包含備份資料的保存庫（即使您已停止保護但保留備份資料）。

- 如果刪除包含依賴項的保存庫，將顯示以下消息：

  ![刪除保存庫錯誤。](./media/backup-azure-delete-vault/error.png)

- 如果從包含依賴項的門戶中刪除本地受保護項，則會顯示一條警告訊息：

  ![刪除受保護的伺服器錯誤。](./media/backup-azure-delete-vault/error-message.jpg)

- 如果備份項處於虛刪除狀態，則會出現警告訊息下方，您必須等待，直到它們被永久刪除。 有關詳細資訊，請參閱[本文](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud)。

   ![刪除保存庫錯誤。](./media/backup-azure-delete-vault/error-message-soft-delete.png)

- 無法刪除已註冊存儲帳戶的保存庫。 要瞭解如何取消註冊帳戶，請參閱[取消註冊存儲帳戶](manage-afs-backup.md#unregister-a-storage-account)。
  
要刪除保存庫，請選擇與您的設置匹配的方案，然後按照建議的步驟操作：

狀況 | 刪除刪除保存庫的依賴項的步驟 |
-- | --
我使用 Azure 備份代理保護了本地檔和資料夾，備份到 Azure | 執行[從 MARS 管理主控台中刪除備份專案](#delete-backup-items-from-the-mars-management-console)的步驟
我有使用 MABS（Microsoft Azure 備份伺服器）或 DPM（系統中心資料保護管理器）對 Azure 進行保護的本地電腦 | 執行[從 MABS 管理主控台中刪除備份項](#delete-backup-items-from-the-mabs-management-console)的步驟
我在雲中具有受保護的專案（例如，laaS 虛擬機器或 Azure 檔共用）  | 在["刪除雲中受保護的專案"中](#delete-protected-items-in-the-cloud)執行這些步驟
我在本地和雲中都有保護物品 | 按照以下循序執行以下所有部分中的步驟： <br> 1.[刪除雲中的受保護專案](#delete-protected-items-in-the-cloud)<br> 2.[從 MARS 管理主控台中刪除備份專案](#delete-backup-items-from-the-mars-management-console) <br> 3.[從 MABS 管理主控台中刪除備份專案](#delete-backup-items-from-the-mabs-management-console)
我沒有任何受保護的專案在本地或雲;但是，我仍然收到保存庫刪除錯誤 | 使用 Azure[資源管理器執行刪除恢復服務保存庫](#delete-the-recovery-services-vault-by-using-azure-resource-manager)中的步驟 <br><br> 確保沒有在保存庫註冊的存儲帳戶。 要瞭解如何取消註冊帳戶，請參閱[取消註冊存儲帳戶](manage-afs-backup.md#unregister-a-storage-account)。

## <a name="delete-protected-items-in-the-cloud"></a>刪除雲中的受保護專案

首先，閱讀**["開始之前"](#before-you-start)** 部分，瞭解依賴項和保存庫刪除過程。

要停止保護並刪除備份資料，請執行以下步驟：

1. 從門戶轉到**恢復服務保存庫**，然後轉到**備份項**。 然後，選擇雲中的受保護專案（例如，Azure 虛擬機器、Azure 存儲 [Azure 檔服務]或 Azure 虛擬機器上的 SQL Server）。

    ![選取備份類型。](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. 按右鍵以選擇備份項。 根據備份項是否受保護，功能表將顯示 **"停止備份**"窗格或 **"刪除備份資料"** 窗格。

    - 如果出現 **"停止備份**"窗格，請選擇從下拉式功能表**中刪除備份資料**。 輸入備份項的名稱（此欄位區分大小寫），然後從下拉式功能表中選擇原因。 輸入您的評論，如果你有。 然後，選擇 **"停止備份**"。

        !["停止備份"窗格。](./media/backup-azure-delete-vault/stop-backup-item.png)

    - 如果出現 **"刪除備份資料"** 窗格，請輸入備份項的名稱（此欄位區分大小寫），然後從下拉式功能表中選擇原因。 輸入您的評論，如果你有。 然後，選擇 **"刪除**"。

         !["刪除備份資料"窗格。](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

3. 選中**通知**圖示：![通知圖示。](./media/backup-azure-delete-vault/messages.png) 此過程完成後，服務將顯示以下消息：*停止備份並刪除"備份專案"的備份資料*。* * *成功完成操作*。
4. 在 **"備份專案**"功能表上選擇 **"刷新**"，以確保已刪除備份項。

      !["刪除備份專案"頁。](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>刪除內部受保護的專案

首先，閱讀**["開始之前"](#before-you-start)** 部分，瞭解依賴項和保存庫刪除過程。

1. 在保存庫儀表板功能表中，選擇 **"備份基礎結構**"。
2. 根據您的本地方案，選擇以下選項之一：

      - 對於 MARS，選擇**受保護的伺服器**，然後選擇**Azure 備份代理**。 然後，選擇要刪除的伺服器。

        ![對於 MARS，選擇保存庫以打開其儀表板。](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - 對於 MABS 或 DPM，請選擇**備份管理伺服器**。 然後，選擇要刪除的伺服器。

          ![對於 MABS，選擇保存庫以打開其儀表板。](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. "**刪除**"窗格將顯示警告訊息。

     ![刪除窗格。](./media/backup-azure-delete-vault/delete-protected-server.png)

     查看"同意"核取方塊中的警告訊息和說明。
    > [!NOTE]
    >- 如果受保護的伺服器與 Azure 服務和備份項同步，則"同意"核取方塊將顯示相關備份項的數量以及查看備份項的連結。
    >- 如果受保護的伺服器未與 Azure 服務同步，並且存在備份項，則"同意"核取方塊將僅顯示備份項的數量。
    >- 如果沒有備份專案，同意核取方塊將要求刪除。

4. 選擇"同意"核取方塊，然後選擇 **"刪除**"。

5. 選中**通知**圖示![刪除備份資料](./media/backup-azure-delete-vault/messages.png)。 操作完成後，服務將顯示消息：*停止備份並刪除"備份專案"的備份資料。* *成功完成操作*。
6. 在 **"備份專案**"功能表上選擇 **"刷新**"，以確保刪除備份專案。

此過程完成後，可以從管理主控台中刪除備份項：

- [從 MARS 管理主控台中刪除備份專案](#delete-backup-items-from-the-mars-management-console)
- [從 MABS 管理主控台中刪除備份專案](#delete-backup-items-from-the-mabs-management-console)

### <a name="delete-backup-items-from-the-mars-management-console"></a>從 MARS 管理主控台中刪除備份專案

1. 打開 MARS 管理主控台，轉到 **"操作"** 窗格，然後選擇 **"計畫備份**"。
2. 在 **"修改或停止計畫備份"頁中**，選擇 **"停止使用此備份計畫"並刪除所有存儲的備份**。 然後，選擇 **"下一步**"。

    ![修改或停止計畫備份。](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. 在 **"停止計畫備份**"頁中，選擇 **"完成**"。

    ![停止計畫備份。](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. 系統將提示您輸入安全 PIN（個人識別碼），您必須手動生成該 PIN 碼。 為此，請先登錄到 Azure 門戶。
5. 轉到**恢復服務保存庫** > **設置** > **屬性**。
6. 在 **"安全 PIN"** 下，選擇 **"生成**"。 複製此 PIN。 PIN 僅有效五分鐘。
7. 在管理主控台中，粘貼 PIN，然後選擇 **"確定**"。

    ![生成安全 PIN。](./media/backup-azure-delete-vault/security-pin.png)

8. 在 **"修改備份進度"** 頁中，將顯示以下消息：*已刪除的備份資料將保留 14 天。之後，備份資料將被永久刪除。*  

    ![刪除備份基礎結構。](./media/backup-azure-delete-vault/deleted-backup-data.png)

刪除本地備份項後，請按照門戶中的後續步驟操作。

### <a name="delete-backup-items-from-the-mabs-management-console"></a>從 MABS 管理主控台中刪除備份專案

有兩種方法可以從 MABS 管理主控台中刪除備份項。

#### <a name="method-1"></a>方法 1

要停止保護和刪除備份資料，請執行以下步驟：

1. 打開 DPM 管理員主控台，然後在巡覽列上選擇 **"保護**"。
2. 在 [顯示] 窗格中，選取要移除的保護群組成員。 按右鍵以選擇 **"停止保護組成員"** 選項。
3. 在 **"停止保護"** 對話方塊中，選擇 **"刪除受保護的資料**"，然後選擇"**刪除連線存儲**"核取方塊。 然後，選擇 **"停止保護**"。

    ![從"停止保護"窗格中選擇"刪除受保護的資料"。](./media/backup-azure-delete-vault/delete-storage-online.png)

    受保護成員狀態更改為*可用的非活動副本*。

4. 按右鍵非活動保護組並選擇 **"刪除非活動保護**"。

    ![刪除非活動保護。](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. 從 **"刪除非活動保護**"視窗中，選擇"**刪除連線存儲**"核取方塊，然後選擇 **"確定**"。

    ![刪除連線存儲。](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

#### <a name="method-2"></a>方法 2

打開**MABS 管理**主控台。 在**選擇資料保護方法**下，清除 **"我想要的線上保護**"核取方塊。

  ![選取資料保護方式。](./media/backup-azure-delete-vault/data-protection-method.png)

刪除本地備份項後，請按照門戶中的後續步驟操作。

## <a name="delete-the-recovery-services-vault"></a>刪除復原服務保存庫

1. 刪除所有依賴項後，滾動到保存庫功能表中的 **"基本"** 窗格。
2. 驗證未列出任何備份項、備份管理伺服器或複製項。 如果專案仍顯示在保存庫中，請參閱"[開始之前"](#before-you-start)部分。

3. 當保存庫中沒有更多專案時，在保存庫儀表板上選擇 **"刪除**"。

    ![在保存庫儀表板上選擇"刪除"。](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. 選擇 **"是**"以驗證是否要刪除保存庫。 將刪除保存庫。 門戶返回到 **"新建**服務"功能表。

## <a name="delete-the-recovery-services-vault-by-using-powershell"></a>使用 PowerShell 刪除恢復服務保存庫

首先，閱讀**["開始之前"](#before-you-start)** 部分，瞭解依賴項和保存庫刪除過程。

要停止保護並刪除備份資料，請：

- 如果在 Azure VM 備份中使用 SQL 並為 SQL 實例啟用了自動保護，請首先禁用自動保護。

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

  [詳細瞭解](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection?view=azps-2.6.0)如何禁用 Azure 備份保護項的保護。

- 停止保護並刪除雲中所有受備份保護的專案的資料（例如 laaS VM、Azure 檔共用等：

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

    [瞭解有關](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-2.6.0&viewFallbackFrom=azps-2.5.0)禁用受備份保護項保護的更多詳細資訊。 

- 對於使用 Azure 備份代理 （MARS） 備份到 Azure 保護的本地檔和資料夾，請使用以下 PowerShell 命令從每個 MARS PowerShell 模組中刪除備份資料：

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    發佈出現以下提示的位置：

    *Microsoft Azure 備份是否確實要刪除此備份策略？已刪除的備份資料將保留 14 天。之後，備份資料將被永久刪除。<br/> [Y] 是 [A] 是所有 [N] 否 [L] 否 到所有 [S] 暫停 [？]説明（預設值為"Y"）：*

- 對於使用 MABS（Microsoft Azure 備份伺服器）或 DPM 到 Azure（系統中心資料保護管理器）保護的本地電腦，請使用以下命令刪除 Azure 中的備份資料。

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    發佈出現以下提示的位置：

   *微軟 Azure 備份*是否確實要刪除此備份策略？ 已刪除的備份資料將保留 14 天。 之後，備份資料將被永久刪除。 <br/>
   [Y] 是 [A] 是所有 [N] 否 [L] 否 到所有 [S] 暫停 [？]説明（預設值為"Y"）:*

刪除備份資料後，取消註冊任何本地容器和管理伺服器。

- 對於使用 Azure 備份代理 （MARS） 備份到 Azure 保護的本地檔和資料夾：

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

    [瞭解有關](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0)從保存庫取消註冊 Windows 伺服器或其他容器的更多資訊。

- 對於使用 MABS（Microsoft Azure 備份伺服器）或 DPM 到 Azure（系統中心資料保護管理）保護的本地電腦：

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

    [瞭解有關](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0)從保存庫取消註冊備份管理容器的更多詳細資訊。

永久刪除備份的資料並取消註冊所有容器後，繼續刪除保存庫。

刪除復原服務保存庫：

   ```PowerShell
       Remove-AzRecoveryServicesVault
      -Vault <ARSVault>
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
   ```

[瞭解有關](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault)刪除恢復服務保存庫的更多內容。

## <a name="delete-the-recovery-services-vault-by-using-cli"></a>使用 CLI 刪除恢復服務保存庫

首先，閱讀**["開始之前"](#before-you-start)** 部分，瞭解依賴項和保存庫刪除過程。

> [!NOTE]
> 目前，Azure 備份 CLI 僅支援管理 Azure VM 備份，因此以下刪除保存庫的命令僅在保存庫包含 Azure VM 備份時才有效。 如果保存庫包含 Azure VM 以外的任何類型的備份項，則無法使用 Azure 備份 CLI 刪除保存庫。

要刪除現有的恢復服務保存庫，請執行以下操作：

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

    有關詳細資訊，請參閱 [本文](/cli/azure/backup/protection#az-backup-protection-disable)。

- 刪除現有的恢復服務保存庫：

    ```azurecli
    az backup vault delete [--force]
                       [--ids]
                       [--name]
                       [--resource-group]
                       [--subscription]
                       [--yes]
    ```

    有關詳細資訊，請參閱 [本文](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest)

## <a name="delete-the-recovery-services-vault-by-using-azure-resource-manager"></a>使用 Azure 資源管理器刪除恢復服務保存庫

僅當刪除所有依賴項並且您仍然收到*保存庫刪除錯誤*時，才建議使用此選項刪除恢復服務保存庫。 請嘗試以下任何或所有提示：

- 在保管**庫功能表中的"基本"** 窗格中，驗證未列出任何備份項、備份管理伺服器或複製的專案。 如果有備份專案，請參閱["開始之前"](#before-you-start)部分。
- 請嘗試[再次從門戶中刪除保存庫](#delete-the-recovery-services-vault)。
- 如果刪除了所有依賴項，但仍收到*Vault 刪除錯誤*，請使用 ARMClient 工具執行以下步驟（在注釋之後）。

1. 轉到[chocolatey.org](https://chocolatey.org/)下載並安裝巧克力。 然後，通過運行以下命令安裝 ARMClient：

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. 登錄到 Azure 帳戶，然後運行以下命令：

    `ARMClient.exe login [environment name]`

3. 在 Azure 門戶中，收集要刪除的保存庫的訂閱 ID 和資源組名稱。

有關 ARMClient 命令的詳細資訊，請參閱[ARMClient README](https://github.com/projectkudu/ARMClient/blob/master/README.md)。

### <a name="use-the-azure-resource-manager-client-to-delete-a-recovery-services-vault"></a>使用 Azure 資源管理器用戶端刪除恢復服務保存庫

1. 使用訂閱 ID、資源組名稱和保存庫名稱運行以下命令。 如果您沒有任何依賴項，則在運行以下命令時將刪除保存庫：

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```

2. 如果保存庫不為空，您將收到以下錯誤訊息：*無法刪除保存庫，因為此保存庫中有現有資源。* 要刪除保存庫中的受保護項或容器，請運行以下命令：

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. 在 Azure 門戶中，請確保刪除保存庫。

## <a name="next-steps"></a>後續步驟

[瞭解恢復服務保存庫](backup-azure-recovery-services-vault-overview.md)<br/>
[瞭解監視和管理恢復服務保存庫](backup-azure-manage-windows-server.md)
