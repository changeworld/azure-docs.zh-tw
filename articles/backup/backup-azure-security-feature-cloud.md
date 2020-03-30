---
title: 安全功能，説明保護雲工作負載
description: 瞭解如何使用 Azure 備份中的安全功能使備份更安全。
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 57c9fd76ae32aea49f480f2a88d8296538d8052d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156067"
---
# <a name="security-features-to-help-protect-cloud-workloads-that-use-azure-backup"></a>安全功能，説明保護使用 Azure 備份的雲工作負載

現在越來越重視安全性問題，例如惡意程式碼、勒索軟體和入侵。 這些安全性問題在成本和資料方面付出的代價很高。 為了防止此類攻擊，Azure 備份現在提供安全功能，即使在刪除後也能説明保護備份資料。

其中一個功能是虛刪除。 使用虛刪除，即使惡意參與者刪除 VM 的備份（或意外刪除備份資料），備份資料也會再保留 14 天，從而在無資料丟失時恢復備份項。 在"虛刪除"狀態下額外保留備份資料 14 天不會給客戶帶來任何成本。 Azure 還使用[存儲服務加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)對所有靜態備份的資料進行加密，以進一步保護資料。

Azure 虛擬機器的虛刪除保護通常可用。

>[!NOTE]
>Azure VM 中的 SQL 伺服器虛刪除和 Azure VM 工作負荷中 SAP HANA 的虛刪除現在在預覽版中可用。<br>
>要註冊預覽版，請寫信給我們：AskAzureBackupTeam@microsoft.com

## <a name="soft-delete"></a>虛刪除

### <a name="soft-delete-for-vms"></a>對 VM 的虛刪除

適用于 VM 的虛刪除可保護 VM 的備份免受意外刪除。 即使在刪除備份後，備份也會在虛刪除狀態中保留 14 天。

> [!NOTE]
> 虛刪除僅保護已刪除的備份資料。 如果在沒有備份的情況下刪除 VM，虛刪除功能將不會保留資料。 所有資源都應使用 Azure 備份進行保護，以確保完全恢復能力。
>

### <a name="supported-regions"></a>支援區域

虛刪除目前支援在美國中西部，東亞，加拿大中部，加拿大東部，法國中部，法國南部，韓國中部，韓國，英國南部，英國西部，澳大利亞東部，澳大利亞東南，北歐，美國西部，美國西部，美國中部，南部東亞、美國中北部、美國中南部、日本東部、日本西部、印度南部、印度中部、印度西部、美國東部2個、瑞士北部、瑞士西部以及所有國家/

### <a name="soft-delete-for-vms-using-azure-portal"></a>使用 Azure 門戶的 VM 虛刪除

1. 要刪除 VM 的備份資料，必須停止備份。 在 Azure 門戶中，轉到恢復服務保存庫，按右鍵備份項並選擇 **"停止備份**"。

   ![Azure 門戶備份專案的螢幕截圖](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. 在以下視窗中，您可以選擇刪除或保留備份資料。 如果選擇 **"刪除備份資料****"，然後停止備份**，則 VM 備份不會永久刪除。 相反，備份資料將在虛刪除狀態下保留 14 天。 如果選擇了 **"刪除備份資料**"，則刪除電子郵件警報將發送到配置的電子郵件 ID，通知使用者備份資料的延長保留期仍為 14 天。 此外，第 12 天會發送電子郵件警報，通知您還有兩天時間恢復已刪除的資料。 刪除將推遲到第 15 天，此時將發生永久刪除，併發送最終電子郵件警報，告知永久刪除資料。

   ![Azure 門戶的螢幕截圖，停止備份螢幕](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. 在這 14 天內，在恢復服務保存庫中，虛刪除的 VM 將旁邊出現一個紅色的"虛刪除"圖示。

   ![Azure 門戶的螢幕截圖，處於虛刪除狀態的 VM](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > 如果保存庫中存在任何虛刪除備份項，則此時無法刪除保存庫。 請嘗試刪除備份專案後，備份專案被永久刪除，並且沒有專案在虛刪除狀態留在保存庫。

4. 要還原虛刪除的 VM，必須首先取消刪除它。 要取消刪除，請選擇虛刪除的 VM，然後選擇 **"取消刪除"** 選項。

   ![Azure 門戶的螢幕截圖，取消刪除 VM](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   將顯示一個視窗，警告如果選擇了取消刪除，則 VM 的所有還原點都將取消刪除，可用於執行還原操作。 VM 將保留處於"停止保護、保留資料"狀態，備份暫停，備份資料永久保留，且備份策略無效。

   ![Azure 門戶的螢幕截圖，確認未刪除 VM](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   此時，您還可以通過從所選還原點選擇**還原 VM**來還原 VM。  

   ![Azure 門戶的螢幕截圖，還原 VM 選項](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > 垃圾回收器僅在使用者執行 **"恢復備份**操作"後運行並清理過期的復原點。

5. 取消刪除過程完成後，狀態將返回到"使用保留資料停止備份"，然後您可以選擇 **"恢復備份**"。 **"恢復備份**"操作將備份項帶回活動狀態，與定義備份和保留計畫的使用者選擇的備份策略相關聯。

   ![Azure 門戶的螢幕截圖，恢復備份選項](./media/backup-azure-security-feature-cloud/resume-backup.png)

此流程圖顯示啟用"虛刪除"時備份項的不同步驟和狀態：

![虛刪除備份專案的生命週期](./media/backup-azure-security-feature-cloud/lifecycle.png)

有關詳細資訊，請參閱下面的[常見問題](backup-azure-security-feature-cloud.md#frequently-asked-questions)部分。

### <a name="soft-delete-for-vms-using-azure-powershell"></a>使用 Azure PowerShell 的 VM 虛刪除

> [!IMPORTANT]
> 使用 Azure PS 使用虛刪除所需的 Az.恢復服務版本為最小 2.2.0。 用於```Install-Module -Name Az.RecoveryServices -Force```獲取最新版本。

如上文對 Azure 門戶所述，在使用 Azure PowerShell 時，步驟順序相同。

#### <a name="delete-the-backup-item-using-azure-powershell"></a>使用 Azure PowerShell 刪除備份專案

使用[禁用-Az 恢復服務備份保護](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0)PS Cmdlet 刪除備份項。

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

備份專案的"刪除狀態"將從"未刪除"更改為"刪除"。 備份資料將保留 14 天。 如果要還原刪除操作，則應執行撤銷刪除。

#### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>使用 Azure PowerShell 撤銷刪除操作

首先，獲取處於虛刪除狀態（即即將刪除）的相關備份項。

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

然後，使用[撤銷-AzRecovery服務備份專案刪除](https://docs.microsoft.com/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion?view=azps-3.1.0)PS Cmdlet 執行撤銷刪除操作。

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

備份專案的"刪除狀態"將恢復為"未刪除"。 但是保護仍然停止了。 [恢復備份](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#change-policy-for-backup-items)以重新啟用保護。

### <a name="soft-delete-for-vms-using-rest-api"></a>使用 REST API 的 VM 虛刪除

- 刪除[此處提到的](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)REST API 的備份。
- 如果使用者希望撤銷這些刪除操作，請參閱[此處](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data)提及的步驟。

## <a name="disabling-soft-delete"></a>禁用虛刪除

預設情況下，在新創建的保存庫中啟用虛刪除，以保護備份資料免遭意外或惡意刪除。  不建議禁用此功能。 應考慮禁用虛刪除的唯一情況是，如果您計畫將受保護的專案移動到新保存庫，並且無法等待刪除和重新保護所需的 14 天（例如在測試環境中）。只有保存庫擁有者才能禁用此功能。 如果禁用此功能，以後所有刪除受保護專案將導致立即刪除，無法還原。 在禁用此功能之前，處於虛刪除狀態的備份資料將保持在虛刪除狀態 14 天。 如果您希望立即永久刪除這些，則需要取消刪除它們並再次刪除它們才能永久刪除。

### <a name="disabling-soft-delete-using-azure-portal"></a>使用 Azure 門戶禁用虛刪除

要禁用虛刪除，請按照以下步驟操作：

1. 在 Azure 門戶中，轉到保存庫，然後轉到 **"設置** -> **屬性**"。
2. 在屬性窗格中，選擇 **"安全設置** -> **更新**"。  
3. 在"安全設置"窗格中，在 **"虛刪除"** 下，選擇 **"禁用**"。

![禁用虛刪除](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

### <a name="disabling-soft-delete-using-azure-powershell"></a>使用 Azure PowerShell 禁用虛刪除

> [!IMPORTANT]
> 使用 Azure PS 使用虛刪除所需的 Az.恢復服務版本為最小 2.2.0。 用於```Install-Module -Name Az.RecoveryServices -Force```獲取最新版本。

要禁用，請使用[集-Az 恢復服務Vault備份屬性](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty?view=azps-3.1.0)PS Cmdlet。

```powershell
Set-AzRecoveryServicesVaultProperty -VaultId $myVaultID -SoftDeleteFeatureState Disable


StorageModelType       :
StorageType            :
StorageTypeState       :
EnhancedSecurityState  : Enabled
SoftDeleteFeatureState : Disabled
```

### <a name="disabling-soft-delete-using-rest-api"></a>使用 REST API 禁用虛刪除

要禁用使用 REST API 的虛刪除功能，請參閱[此處](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)提及的步驟。

## <a name="permanently-deleting-soft-deleted-backup-items"></a>永久刪除虛刪除備份專案

在禁用此功能之前，處於虛刪除狀態的備份資料將保持在虛刪除狀態。 如果要立即永久刪除這些，請取消刪除並再次刪除它們以永久刪除它們。

### <a name="using-azure-portal"></a>使用 Azure 入口網站

請遵循下列步驟：

1. 按照步驟[禁用虛刪除](#disabling-soft-delete)。
2. 在 Azure 門戶中，轉到保存庫，轉到**備份專案**，然後選擇虛刪除的 VM。

   ![選擇虛刪除的 VM](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

3. 選擇"**取消刪除"** 選項。

   ![選擇"取消刪除"](./media/backup-azure-security-feature-cloud/choose-undelete.png)

4. 將顯示一個視窗。 選擇 **"取消刪除**"。

   ![選擇"取消刪除"](./media/backup-azure-security-feature-cloud/undelete-vm.png)

5. 選擇 **"刪除備份資料**"以永久刪除備份資料。

   ![選擇"刪除備份資料"](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-buttom.png)

6. 鍵入備份項的名稱以確認要刪除復原點。

   ![鍵入備份項的名稱](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-data1.png)

7. 要刪除專案的備份資料，請選擇 **"刪除**"。 通知訊息讓您知道備份資料已被刪除。

### <a name="using-azure-powershell"></a>使用 Azure PowerShell

如果在禁用虛刪除之前刪除了專案，則這些專案將處於虛刪除狀態。 要立即刪除它們，刪除操作需要反轉，然後再次執行。

標識處於虛刪除狀態的項。

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

然後反轉啟用虛刪除時執行的刪除操作。

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

由於虛刪除現已禁用，刪除操作將導致立即刪除備份資料。

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

### <a name="using-rest-api"></a>使用 REST API

如果在禁用虛刪除之前刪除了專案，則這些專案將處於虛刪除狀態。 要立即刪除它們，刪除操作需要反轉，然後再次執行。

1. 首先，使用[此處](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data)提到的步驟撤銷刪除操作。
2. 然後使用 REST API 使用[此處](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)提到的步驟禁用虛刪除功能。
3. 然後刪除備份使用 REST API，[如此處](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)所述。

## <a name="encryption"></a>加密

使用 Azure 存儲加密將所有備份資料存儲在雲中時自動加密，這有助於您履行安全性和合規性承諾。 靜態資料使用 256 位 AES 加密進行加密，這是可用的最強塊密碼之一，並且符合 FIPS 140-2 標準。

除了靜態加密外，傳輸中的所有備份資料都通過 HTTPS 傳輸。 它始終保留在 Azure 骨幹絡上。

有關詳細資訊，請參閱[靜態資料的 Azure 存儲加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)。 請參閱[Azure 備份常見問題解答](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#encryption)，回答有關加密的任何問題。

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>使用平臺管理的金鑰加密備份資料

預設情況下，所有資料都使用平臺管理的金鑰進行加密。 您無需從末尾執行任何顯式操作來啟用此加密，它適用于備份到恢復服務保存庫的所有工作負載。

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>使用客戶管理的金鑰加密備份資料

備份 Azure 虛擬機器時，現在可以使用您擁有和管理的金鑰組資料進行加密。 Azure 備份允許您使用存儲在 Azure 金鑰保存庫中的 RSA 金鑰來加密備份。 用於加密備份的加密金鑰可能與用於源的加密金鑰不同。 資料使用基於 AES 256 的資料加密金鑰 （DEK） 進行保護，金鑰反過來又使用金鑰進行保護。 這使您可以完全控制資料和鍵。 要允許加密，需要授予恢復服務保存庫對 Azure 金鑰保存庫中的加密金鑰的存取權限。 您可以根據需要禁用金鑰或撤銷存取權限。 但是，在嘗試保護保存庫上的任何專案之前，必須使用金鑰啟用加密。

>[!NOTE]
>此功能當前可用性有限。 如果您希望使用客戶託管金鑰加密備份資料，AskAzureBackupTeam@microsoft.com請填寫[此調查](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURE9TTDRIUEUyNFhNT1lZS1BNVDdZVllHWi4u)併發送電子郵件至我們這裡。 請注意，使用此功能的功能須經 Azure 備份服務的批准。

### <a name="backup-of-managed-disk-vms-encrypted-using-customer-managed-keys"></a>使用客戶託管金鑰加密的託管磁片 VM 的備份

Azure 備份還允許您備份使用金鑰進行伺服器端加密的 Azure VM。 用於加密磁片的金鑰存儲在 Azure 金鑰保存庫中並由您管理。 使用客戶管理的金鑰的伺服器端加密不同于 Azure 磁片加密，因為 ADE 利用 BitLocker（用於 Windows）和 DM-Crypt（用於 Linux）來執行訪客內加密，SSE 對存儲服務中的資料進行加密，使您能夠使用任何作業系統或VM 的圖像。 有關詳細資訊，請參閱[使用客戶託管金鑰加密託管磁片](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys)。

### <a name="backup-of-vms-encrypted-using-ade"></a>使用 ADE 加密的 VM 備份

使用 Azure 備份，還可以備份使用 Azure 磁片加密加密其作業系統或資料磁片的 Azure 虛擬機器。 ADE 使用用於 Windows VM 的 BitLocker 和用於 Linux VM 的 DM-Crypt 以執行訪客內加密。 有關詳細資訊，請參閱[使用 Azure 備份 備份備份和還原加密虛擬機器](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)。

## <a name="private-endpoints"></a>專用終結點

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="other-security-features"></a>其他安全功能

### <a name="protection-of-azure-backup-recovery-points"></a>保護 Azure 備份復原點

恢復服務保存庫使用的存儲帳戶是隔離的，使用者無法出於任何惡意目的訪問這些帳戶。 只能通過 Azure 備份管理操作（如還原）允許訪問。 這些管理操作通過基於角色的存取控制 （RBAC） 進行控制。

有關詳細資訊，請參閱[使用基於角色的存取控制來管理 Azure 備份復原點](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault)。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="for-soft-delete"></a>對於虛刪除

#### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>我是否需要在每個保存庫上啟用虛刪除功能？

否，預設情況下，它是為所有恢復服務保存庫構建和啟用的。

#### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-delete-operation-is-complete"></a>是否可以配置刪除操作完成後資料將以虛刪除狀態保留的天數？

否，在刪除操作後，它固定為 14 天的額外保留時間。

#### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>我需要為此額外保留 14 天的費用嗎？

不，作為虛刪除功能的一部分，此 14 天的額外保留是免費的。

#### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>當資料處於虛刪除狀態時，是否可以執行還原操作？

否，您需要取消刪除已刪除的軟資源才能還原。 取消刪除操作將使資源回到**保留資料狀態的"停止"保護**中，您可以在其中還原到任何時間點。 垃圾回收器在此狀態下保持暫停狀態。

#### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>我的快照是否會遵循與保存庫中的復原點相同的生命週期？

是。

#### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>如何再次為虛刪除資源觸發計畫備份？

取消刪除後跟"恢復操作"將再次保護資源。 恢復操作關聯備份策略以在所選保留期間觸發計畫備份。 此外，垃圾回收器在恢復操作完成後立即運行。 如果要從超過到期日期的復原點執行還原，建議您在觸發恢復操作之前執行還原。

#### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>如果保存庫中有虛刪除的專案，是否可以刪除保存庫？

如果保存庫中存在處於虛刪除狀態的備份專案，則無法刪除恢復服務保存庫。 虛刪除專案在刪除操作後 14 天內被永久刪除。 如果無法等待 14 天，則[禁用虛刪除](#disabling-soft-delete)，取消刪除虛刪除的專案，然後再次刪除它們以永久刪除。 在確保沒有受保護的專案和沒有虛刪除項後，可以刪除保存庫。  

#### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>我可以在刪除後 14 天虛刪除期間之前刪除資料嗎？

否。 您無法強制刪除虛刪除的專案，這些專案會在 14 天后自動刪除。 此功能用於保護備份的資料免受意外或惡意刪除。  您應該等待 14 天，然後再對 VM 執行任何其他操作。  虛刪除專案贏得'收費。  如果需要在 14 天內重新保護標記為虛刪除的 VM 到新保存庫，請與 Microsoft 支援部門聯繫。

#### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>能否在 PowerShell 或 CLI 中執行虛刪除操作？

虛刪除操作可以使用[PowerShell](#soft-delete-for-vms-using-azure-powershell)執行。 目前不支援 CLI。

#### <a name="is-soft-delete-supported-for-other-cloud-workloads-like-sql-server-in-azure-vms-and-sap-hana-in-azure-vms"></a>是否支援虛刪除其他雲工作負荷，如 Azure VM 中的 SQL 伺服器和 Azure VM 中的 SAP HANA？

否。 目前僅支援 Azure 虛擬機器的虛刪除。

## <a name="next-steps"></a>後續步驟

- 閱讀有關[Azure 備份的安全控制項](backup-security-controls.md)。
