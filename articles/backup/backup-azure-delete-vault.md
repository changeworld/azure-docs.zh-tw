---
title: 移除 Microsoft Azure 復原服務保存庫
description: 在本文中,瞭解如何刪除依賴項,然後刪除 Azure 備份恢復服務保管庫。
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 69fed6c53914ed7aa16b04b5311ec69966734f25
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025130"
---
# <a name="delete-an-azure-backup-recovery-services-vault"></a>移除 Azure 備份復原服務保存庫

本文介紹如何刪除 Azure[備份](backup-overview.md)恢復服務保管庫。 它包含刪除依賴項然後刪除保管庫的說明。

## <a name="before-you-start"></a>開始之前

不能刪除具有依賴項(如受保護伺服器或備份管理伺服器)的恢復服務保管庫。

- 無法刪除包含備份資料的保管庫(即使您已停止保護但保留備份資料)。

- 如果刪除包含相依項的保管庫,將顯示以下訊息:

  ![刪除保管庫錯誤。](./media/backup-azure-delete-vault/error.png)

- 如果從包含依賴項的門戶中刪除本地受保護項,則會顯示一條警告消息:

  ![刪除受保護的伺服器錯誤。](./media/backup-azure-delete-vault/error-message.jpg)

- 如果備份項處於軟刪除狀態,則會出現警告消息下方,您必須等待,直到它們被永久刪除。 有關詳細資訊,請參閱[本文](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud)。

   ![刪除保管庫錯誤。](./media/backup-azure-delete-vault/error-message-soft-delete.png)

- 無法刪除已註冊存儲帳戶的保管庫。 要瞭解如何取消註冊帳戶,請參閱[取消註冊存儲帳戶](manage-afs-backup.md#unregister-a-storage-account)。
  
要刪除保管庫,請選擇與您的設定匹配的方案,然後按照建議的步驟操作:

狀況 | 刪除刪除保管庫的依賴項的步驟 |
-- | --
我使用 Azure 備份代理保護了本地檔案和資料夾,備份到 Azure | 執行[從 MARS 管理控制台中移除備份項目](#delete-backup-items-from-the-mars-management-console)的步驟
我有使用 MABS(Microsoft Azure 備份伺服器)或 DPM(系統中心資料保護管理員)對 Azure 進行保護的本地電腦 | 執行[從 MABS 管理控制台中移除備份項目](#delete-backup-items-from-the-mabs-management-console)的步驟
我在雲中具有受保護的專案(例如,laaS 虛擬機器或 Azure 檔案共用)  | 在[「刪除雲中受保護的項目」中](#delete-protected-items-in-the-cloud)執行這些步驟
我在本地和雲中都有保護物品 | 依以下順序執行以下所有部分中的步驟: <br> 1.[刪除雲中的受保護項目](#delete-protected-items-in-the-cloud)<br> 2.[從 MARS 管理控制中移除備份專案](#delete-backup-items-from-the-mars-management-console) <br> 3.[從 MABS 管理控制中移除備份專案](#delete-backup-items-from-the-mabs-management-console)
我沒有任何受保護的專案在本地或雲;但是,我仍然收到保管庫刪除錯誤 | 使用 Azure[資源管理員執行移除回復服務保管庫](#delete-the-recovery-services-vault-by-using-azure-resource-manager)中的步驟 <br><br> 確保沒有在保管庫註冊的存儲帳戶。 要瞭解如何取消註冊帳戶,請參閱[取消註冊存儲帳戶](manage-afs-backup.md#unregister-a-storage-account)。

## <a name="delete-protected-items-in-the-cloud"></a>移除雲中的受保護項目

首先,閱讀**[「開始之前」](#before-you-start)** 部分,瞭解依賴項和保管庫刪除過程。

要停止保護並刪除備份資料,請執行以下步驟:

1. 從門戶轉到**恢復服務保管庫**,然後轉到**備份項**。 然後,選擇雲中的受保護專案(例如,Azure 虛擬機器、Azure儲存[Azure 檔服務]或 Azure 虛擬機器上的 SQL Server)。

    ![選取備份類型。](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. 右鍵單擊以選擇備份項。 根據備份項目是否受保護,選單將顯示 **「停止備份**」窗格或 **「刪除備份資料」** 窗格。

    - 如果出現 **「停止備份**」 ,請選擇從下拉選單**中移除備份資料**。 輸入備份項的名稱(此欄位區分大小寫),然後從下拉菜單中選擇原因。 輸入您的評論,如果你有。 然後,選擇 **「停止備份**」。

        !["停止備份"窗格。](./media/backup-azure-delete-vault/stop-backup-item.png)

    - 如果出現 **「刪除備份資料」** 窗格,請輸入備份項的名稱(此欄位區分大小寫),然後從下拉選單中選擇原因。 輸入您的評論,如果你有。 然後,選擇 **「刪除**」。

         ![「刪除備份資料」窗格。](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

3. 選中**通知**圖![示: 通知圖示。](./media/backup-azure-delete-vault/messages.png) 此過程完成後,服務會顯示以下訊息:*停止備份並刪除「備份專案」的備份資料*。* * *完成操作*。
4. 在 **「備份項目**」功能表上選擇 **「刷新**」,以確保已刪除備份項。

      ![「刪除備份項目」 頁。](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>移除內部受保護的項目

首先,閱讀**[「開始之前」](#before-you-start)** 部分,瞭解依賴項和保管庫刪除過程。

1. 在保管庫儀錶板功能表中,選擇 **「備份基礎結構**」。。
2. 根據您的本地方案,選擇以下選項之一:

      - 此顏色,選擇**受保護的伺服器**,然後選擇**Azure 備份代理**。 然後,選擇要刪除的伺服器。

        ![對於 MARS,選擇保管庫以打開其儀表板。](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - 對 MABS 或 DPM,請選擇**備份管理伺服器**。 然後,選擇要刪除的伺服器。

          ![對於 MABS,選擇保管庫以打開其儀表板。](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. 「**刪除**」窗格將顯示警告訊息。

     ![刪除窗格。](./media/backup-azure-delete-vault/delete-protected-server.png)

     查看"同意"複選框中的警告消息和說明。
    > [!NOTE]
    >- 如果受保護的伺服器與 Azure 服務和備份項同步,則"同意"複選框將顯示相關備份項的數量以及查看備份項的連結。
    >- 如果受保護的伺服器未與 Azure 服務同步,並且存在備份項,則"同意"複選框將僅顯示備份項的數量。
    >- 如果沒有備份專案,同意複選框將要求刪除。

4. 選擇"同意"複選框,然後選擇 **"刪除**"。

5. 選取**的 通知**![圖示 刪除](./media/backup-azure-delete-vault/messages.png)備份資料 。 操作完成後,服務將顯示消息:*停止備份並刪除「備份專案」的備份數據。* *完成操作*。
6. 在 **「備份項目**」功能表上選擇 **「刷新**」,以確保刪除備份專案。

此過程完成後,可以從管理控制台中刪除備份項:

- [從 MARS 管理控制中移除備份項目](#delete-backup-items-from-the-mars-management-console)
- [從 MABS 管理控制中移除備份項目](#delete-backup-items-from-the-mabs-management-console)

### <a name="delete-backup-items-from-the-mars-management-console"></a>從 MARS 管理控制中移除備份項目

1. 打開 MARS 管理主控台,轉到 **"操作"** 窗格,然後選擇 **「計畫備份**」。。
2. 在 **「修改或停止計劃備份」 頁中**,選擇 **「停止使用此備份計畫」 並移除所有儲存的備份**。 然後，選取 [下一步]  。

    ![修改或停止計劃備份。](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. 在 **「停止計劃備份**」頁中,選擇 **「完成**」。。

    ![停止計劃備份。](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. 系統將提示您輸入安全 PIN(個人識別號),您必須手動生成該 PIN 碼。 為此,請先登錄到 Azure 門戶。
5. 跳到**修復服務保存庫** > **設定** > **屬性**。
6. 在 **"安全 PIN"** 下,選擇 **"生成**"。 複製此 PIN。 PIN 僅有效五分鐘。
7. 在管理控制台中,粘貼 PIN,然後選擇 **「確定**」。。

    ![生成安全 PIN。](./media/backup-azure-delete-vault/security-pin.png)

8. 在 **「修改備份進度」** 頁中,將顯示以下消息:*已刪除的備份資料將保留 14 天。之後,備份數據將被永久刪除。*  

    ![刪除備份基礎結構。](./media/backup-azure-delete-vault/deleted-backup-data.png)

刪除本地備份項后,請按照門戶中的後續步驟操作。

### <a name="delete-backup-items-from-the-mabs-management-console"></a>從 MABS 管理控制中移除備份項目

有兩種方法可以從 MABS 管理控制台中刪除備份項。

#### <a name="method-1"></a>方法 1

要停止保護和刪除備份資料,請執行以下步驟:

1. 打開 DPM 管理員控制台,然後在導航欄上選擇 **「保護**」。。
2. 在 [顯示] 窗格中，選取要移除的保護群組成員。 右鍵按一下選擇 **「停止保護組成員」** 選項。
3. 在 **「停止保護」** 對話方塊中,選擇 **「刪除受保護的資料**」,然後選擇「**刪除連線儲存**」複選框。 然後,選擇 **「停止保護**」。

    ![從「停止保護」窗格中選擇「刪除受保護的數據」。](./media/backup-azure-delete-vault/delete-storage-online.png)

    受保護成員狀態變更為*可用的非作用 。*

4. 右鍵按一下非活動保護組並選擇 **「刪除非活動保護**」。

    ![刪除非活動保護。](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. 移除**非作用保護**視窗中,選擇「**刪除連線儲存**」 選單機儲存 「 複選框」,然後選擇 **「確定**」 。

    ![刪除連線儲存。](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

#### <a name="method-2"></a>方法 2

打開**MABS 管理**主控台。 選擇**資料保護方法**下,清除 **「我想要的線上保護**」複選框。

  ![選取資料保護方式。](./media/backup-azure-delete-vault/data-protection-method.png)

刪除本地備份項后,請按照門戶中的後續步驟操作。

## <a name="delete-the-recovery-services-vault"></a>刪除復原服務保存庫

1. 刪除所有依賴項後,滾動到保管庫功能表中的 **「基本」** 窗格。
2. 驗證未列出任何備份項、備份管理伺服器或複製項。 如果專案仍顯示在保管庫中,請參閱"[開始之前"](#before-you-start)部分。

3. 當保管庫中沒有更多專案時,在保管庫儀錶板上選擇 **「刪除**」。。

    ![在保管庫儀錶板上選擇"刪除"。](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. 選擇 **「以**驗證是否要刪除保管庫。 將刪除保管庫。 門戶返回到 **「新建**服務」功能表。

## <a name="delete-the-recovery-services-vault-by-using-powershell"></a>使用 PowerShell 移除復原服務保存庫

首先,閱讀**[「開始之前」](#before-you-start)** 部分,瞭解依賴項和保管庫刪除過程。

要停止保護並刪除備份資料,請:

- 如果在 Azure VM 備份中使用 SQL 並為 SQL 實例啟用了自動保護,請首先禁用自動保護。

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

- 停止保護並移除雲中所有受備份保護的項目的資料(例如 laaS VM、Azure 檔案共享等:

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

- 對於使用 Azure 備份代理 (MARS) 備份到 Azure 保護的本地檔案和資料夾,請使用以下 PowerShell 命令從每個 MARS PowerShell 模組中刪除備份資料:

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    發佈時顯示以下提示的位置:

    *Microsoft Azure 備份是否確實要刪除此備份策略?已刪除的備份數據將保留 14 天。之後,備份數據將被永久刪除。<br/> [Y] 是 [A] 是所有 [N] 否 [L] 否 到所有 [S] 暫停 [?]幫助(預設值為"Y"):*

- 對於使用 MABS(Microsoft Azure 備份伺服器)或 DPM 到 Azure(系統中心資料保護管理員)保護的本地電腦,請使用以下命令刪除 Azure 中的備份數據。

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    發佈時顯示以下提示的位置:

   *微軟 Azure 備份*是否確實要刪除此備份策略? 已刪除的備份數據將保留 14 天。 之後,備份數據將被永久刪除。 <br/>
   [Y] 是 [A] 是所有 [N] 否 [L] 否 到所有 [S] 暫停 [?]幫助(預設值為"Y'):*

刪除備份資料後,取消註冊任何本地容器和管理伺服器。

- 對 Azure 備份代理 (MARS) 備份到 Azure 保護的本地檔案與資料夾:

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

    [瞭解有關](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0)從保管庫取消註冊 Windows 伺服器或其他容器的更多資訊。

- 對於使用 MABS(Microsoft Azure 備份伺服器)或 DPM 到 Azure(系統中心資料保護管理)保護的本地電腦:

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

    [瞭解有關](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0)從保管庫取消註冊備份管理容器的更多詳細資訊。

永久刪除備份的數據並取消註冊所有容器後,繼續刪除保管庫。

刪除復原服務保存庫：

   ```PowerShell
       Remove-AzRecoveryServicesVault
      -Vault <ARSVault>
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
   ```

[瞭解有關](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault)刪除恢復服務保管庫的更多內容。

## <a name="delete-the-recovery-services-vault-by-using-cli"></a>使用 CLI 移除服務保存庫

首先,閱讀**[「開始之前」](#before-you-start)** 部分,瞭解依賴項和保管庫刪除過程。

> [!NOTE]
> 目前,Azure 備份 CLI 僅支援管理 Azure VM 備份,因此以下刪除保管庫的命令僅在保管庫包含 Azure VM 備份時才有效。 如果保管庫包含 Azure VM 以外的任何類型的備份項,則無法使用 Azure 備份 CLI 刪除保管庫。

要刪除現有的復原服務保管庫,請執行以下操作:

- 停止保護並移除備份資料

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

    有關詳細資訊,請參閱 [本文](/cli/azure/backup/protection#az-backup-protection-disable)。

- 移除現有的復原服務保存庫:

    ```azurecli
    az backup vault delete [--force]
                       [--ids]
                       [--name]
                       [--resource-group]
                       [--subscription]
                       [--yes]
    ```

    有關詳細資訊,請參閱 [本文](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest)

## <a name="delete-the-recovery-services-vault-by-using-azure-resource-manager"></a>使用 Azure 資源管理員移除復原服務保存庫

僅當刪除所有依賴項並且您仍然收到*保管庫刪除錯誤*時,才建議使用此選項刪除恢復服務保管庫。 請嘗試以下任何或所有提示:

- 在保管**庫功能表中的「基本」** 窗格中,驗證未列出任何備份項、備份管理伺服器或複製的專案。 如果有備份專案,請參閱["開始之前"](#before-you-start)部分。
- 請試[著 重新從門戶中移除保存庫](#delete-the-recovery-services-vault)。
- 如果刪除了所有依賴項,但仍收到*Vault 刪除錯誤*,請使用 ARMClient 工具執行以下步驟(在註釋之後)。

1. 轉到[chocolatey.org](https://chocolatey.org/)下載並安裝巧克力。 然後,透過執行以下命令安裝 ARMClient:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. 登入 Azure 帳戶,然後執行以下指令:

    `ARMClient.exe login [environment name]`

3. 在 Azure 門戶中,收集要刪除的保管庫的訂閱 ID 和資源組名稱。

有關 ARMClient 指令的詳細資訊,請參閱[ARMClient README](https://github.com/projectkudu/ARMClient/blob/master/README.md)。

### <a name="use-the-azure-resource-manager-client-to-delete-a-recovery-services-vault"></a>使用 Azure 資源管理員客戶端移除回復服務保管庫

1. 使用訂閱 ID、資源組名稱和保管庫名稱運行以下命令。 如果您沒有任何相依項,則在執行以下命令時將刪除保管庫:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```

2. 如果保存庫不為空,您將收到以下錯誤訊息:*無法刪除保管庫,因為此保管庫中有現有資源。* 要刪除保管庫中的受保護項或容器,請執行以下命令:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. 在 Azure 門戶中,請確保刪除保管庫。

## <a name="next-steps"></a>後續步驟

[瞭解恢復服務保管庫](backup-azure-recovery-services-vault-overview.md)<br/>
[瞭解監視和管理復原服務保存庫](backup-azure-manage-windows-server.md)
