---
title: 協助保護雲端工作負載的安全性功能
description: 瞭解如何使用 Azure 備份中的安全性功能，讓備份更加安全。
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 3435b9455af3362cdce2dceb20e183a8b05a15dd
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395530"
---
# <a name="security-features-to-help-protect-cloud-workloads-that-use-azure-backup"></a>協助保護使用 Azure 備份之雲端工作負載的安全性功能

現在越來越重視安全性問題，例如惡意程式碼、勒索軟體和入侵。 這些安全性問題在成本和資料方面付出的代價很高。 為了防範這類攻擊，Azure 備份現在提供安全性功能來協助保護備份資料，即使在刪除後也是如此。

其中一項功能就是虛刪除。 使用虛刪除時，即使惡意執行者刪除 VM 的備份（或不小心刪除備份資料），備份資料仍會保留14個額外的天數，讓該備份專案不會遺失資料。 在「虛刪除」狀態中，備份資料的額外14天保留期不會對客戶產生任何費用。 Azure 也會使用[儲存體服務加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)來加密待用的所有備份資料，以進一步保護您的資料。

Azure 虛擬機器的虛刪除保護已正式推出。

>[!NOTE]
>Azure VM 中的 SQL server 虛刪除和 Azure VM 工作負載中的 SAP Hana 虛刪除現已提供預覽。<br>
>若要註冊預覽版，請在 AskAzureBackupTeam@microsoft.com 寫信給我們

## <a name="soft-delete"></a>虛刪除

### <a name="soft-delete-for-vms"></a>Vm 的虛刪除

Vm 的虛刪除可保護 Vm 的備份不會遭到意外刪除。 即使在刪除備份之後，它們仍會以虛刪除狀態保留14個額外的天數。

> [!NOTE]
> 虛刪除只會保護已刪除的備份資料。 如果 VM 在沒有備份的情況下刪除，虛刪除功能將不會保留資料。 所有資源都應該使用 Azure 備份來保護，以確保完整的復原能力。
>

### <a name="supported-regions"></a>支援區域

美國中西部、東亞、加拿大中部、加拿大東部、法國中部、法國南部、韓國中部、南韓南部、英國南部、英國西部、澳大利亞東部、澳大利亞東南部、北歐、美國西部、West 美國2、美國中部、南部，目前支援虛刪除東亞、美國中北部、美國中南部、日本東部、日本西部、印度南部、印度中部、印度西部、美國東部2、瑞士北部、瑞士西部和所有國家地區。

### <a name="soft-delete-for-vms-using-azure-portal"></a>使用 Azure 入口網站的 Vm 虛刪除

1. 若要刪除 VM 的備份資料，必須停止備份。 在 Azure 入口網站中，移至您的復原服務保存庫，以滑鼠右鍵按一下備份專案，然後選擇 [**停止備份**]。

   ![Azure 入口網站備份專案的螢幕擷取畫面](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. 在下列視窗中，您將會獲得刪除或保留備份資料的選項。 如果您選擇 [**刪除備份資料**] 然後**停止備份**，將不會永久刪除 VM 備份。 相反地，備份資料會保留14天的「虛刪除」狀態。 如果選擇 [**刪除備份資料**]，則會將刪除電子郵件警示傳送至設定的電子郵件識別碼，通知使用者14天保留備份資料的延伸保留期。 此外，第12天會傳送電子郵件警示，通知您有兩天的時間來 resurrect 已刪除的資料。 刪除作業會延後到15天后，將會進行永久刪除，並傳送最終的電子郵件警示來通知永久刪除資料。

   ![[停止備份] 畫面 Azure 入口網站的螢幕擷取畫面](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. 在這14天的復原服務保存庫中，虛刪除的 VM 旁會出現紅色的「虛刪除」圖示。

   ![Azure 入口網站的螢幕擷取畫面，VM 處於虛刪除狀態](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > 如果保存庫中有任何虛刪除的備份專案，就無法在該時間刪除保存庫。 請在永久刪除備份專案之後嘗試刪除保存庫，而且保存庫中沒有任何已虛刪除狀態的專案。

4. 若要還原已虛刪除的 VM，則必須先將它取消刪除。 若要取消刪除，請選擇虛刪除的 VM，然後選取 [取消**刪除**] 選項。

   ![Azure 入口網站、取消刪除 VM 的螢幕擷取畫面](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   將會出現一個警告，指出如果選擇 [取消刪除]，將會取消刪除 VM 的所有還原點，並可執行還原作業。 VM 將會保留在「停止保護並保留資料」狀態，並暫停備份並保留備份資料，且不會有任何備份原則生效。

   ![Azure 入口網站的螢幕擷取畫面，確認刪除 VM](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   此時，您也可以從選擇的還原點選取 [**還原 vm** ] 來還原 vm。  

   ![Azure 入口網站，Restore VM 選項的螢幕擷取畫面](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > 只有在使用者執行**繼續備份**作業之後，垃圾收集行程才會執行並清除過期的復原點。

5. 在取消刪除程式完成之後，狀態會回到 [停止備份並保留資料]，然後您可以選擇 [**繼續備份**]。 [**繼續備份**] 作業會將備份專案帶回 [作用中] 狀態，並與定義備份和保留排程的使用者所選取的備份原則相關聯。

   ![[繼續備份] 選項 Azure 入口網站的螢幕擷取畫面](./media/backup-azure-security-feature-cloud/resume-backup.png)

此流程圖會顯示已啟用虛刪除時，備份專案的不同步驟和狀態：

![虛刪除之備份專案的生命週期](./media/backup-azure-security-feature-cloud/lifecycle.png)

如需詳細資訊，請參閱下面的[常見問題一節](backup-azure-security-feature-cloud.md#frequently-asked-questions)。

### <a name="soft-delete-for-vms-using-azure-powershell"></a>使用 Azure Powershell 進行 Vm 的虛刪除

> [!IMPORTANT]
> 使用 Azure PS 進行虛刪除所需的 Az. Azurerm.recoveryservices 版本是 min 2.2.0。 請使用 ```Install-Module -Name Az.RecoveryServices -Force``` 來取得最新版本。

如上 Azure 入口網站所述，使用 Azure Powershell 時，步驟的順序也相同。

#### <a name="delete-the-backup-item-using-azure-powershell"></a>使用 Azure Powershell 刪除備份專案

使用[Enable-azrecoveryservicesbackupprotection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS Cmdlet 刪除備份專案。

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

備份專案的 ' DeleteState ' 會從 ' NotDeleted ' 變更為 ' ToBeDeleted '。 備份資料將會保留14天。 如果您想要還原刪除作業，則應該執行復原-刪除。

#### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>使用 Azure Powershell 復原刪除作業

首先，提取處於虛刪除狀態（也就是即將刪除）的相關備份專案。

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

然後，使用[復原-AzRecoveryServicesBackupItemDeletion](https://docs.microsoft.com/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion?view=azps-3.1.0) PS Cmdlet 執行復原刪除作業。

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

備份專案的 ' DeleteState ' 將會還原為 ' NotDeleted '。 但仍會停止保護。 您需要[繼續備份](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#change-policy-for-backup-items)以重新啟用保護。

### <a name="soft-delete-for-vms-using-rest-api"></a>使用 REST API 的 Vm 虛刪除

- 如[這裡](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)所述，使用 REST API 刪除備份。
- 如果使用者希望復原這些刪除作業，請參閱[這裡](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data)所述的步驟。

## <a name="disabling-soft-delete"></a>停用虛刪除

預設會在新建立的保存庫上啟用虛刪除，以防止意外或惡意刪除的備份資料。  不建議停用這項功能。 如果您打算將受保護的專案移至新的保存庫，且在刪除和重新保護之前，不能等待14天（例如在測試環境中），您應該考慮停用虛刪除的唯一情況。只有保存庫擁有者可以停用這項功能。 如果您停用此功能，所有未來的受保護專案刪除都會導致立即移除，而不會有還原功能。 在停用此功能之前，在已虛刪除狀態中的備份資料將會在14天的期間內保持為「虛刪除」狀態。 如果您想要立即永久刪除這些專案，則必須重新刪除並刪除它們，才能永久刪除。

### <a name="disabling-soft-delete-using-azure-portal"></a>使用 Azure 入口網站停用虛刪除

若要停用虛刪除，請遵循下列步驟：

1. 在 [Azure 入口網站中，移至您的保存庫，然後移至 [**設定**] -> **屬性**]。
2. 在 屬性 窗格中，選取 **安全性設定** -> **更新**。  
3. 在 [安全性設定] 窗格的 [虛**刪除**] 底下，選取 [**停**用]。

![停用虛刪除](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

### <a name="disabling-soft-delete-using-azure-powershell"></a>使用 Azure Powershell 停用虛刪除

> [!IMPORTANT]
> 使用 Azure PS 進行虛刪除所需的 Az. Azurerm.recoveryservices 版本是 min 2.2.0。 請使用 ```Install-Module -Name Az.RecoveryServices -Force``` 來取得最新版本。

若要停用，請使用[AzRecoveryServicesVaultBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty?view=azps-3.1.0) PS Cmdlet。

```powershell
Set-AzRecoveryServicesVaultProperty -VaultId $myVaultID -SoftDeleteFeatureState Disable


StorageModelType       :
StorageType            :
StorageTypeState       :
EnhancedSecurityState  : Enabled
SoftDeleteFeatureState : Disabled
```

### <a name="disabling-soft-delete-using-rest-api"></a>使用 REST API 停用虛刪除

若要使用 REST API 停用虛刪除功能，請參閱[這裡](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)所述的步驟。

## <a name="permanently-deleting-soft-deleted-backup-items"></a>永久刪除虛刪除的備份專案

在停用此功能之前，已虛刪除狀態中的備份資料將會保留在虛刪除狀態。 如果您想要立即永久刪除這些專案，請重新刪除並刪除它們，以永久刪除。

### <a name="using-azure-portal"></a>使用 Azure 入口網站

請遵循下列步驟：

1. 請遵循下列步驟來停用虛[刪除](#disabling-soft-delete)。
2. 在 Azure 入口網站中，移至您的保存庫，移至 [**備份專案**]，然後選擇虛刪除的 VM。

![選擇虛刪除的 VM](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

3. 選取 [取消**刪除**] 選項。

![選擇取消刪除](./media/backup-azure-security-feature-cloud/choose-undelete.png)


4. 視窗隨即出現。 選取 [取消**刪除**]。

![選取取消刪除](./media/backup-azure-security-feature-cloud/undelete-vm.png)

5. 選擇 [**刪除備份資料**]，以永久刪除備份資料。

![選擇 [刪除備份資料]](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-buttom.png)

6. 輸入備份專案的名稱，以確認您想要刪除復原點。

![輸入備份專案的名稱](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-data1.png)

7. 若要刪除專案的備份資料，請選取 [**刪除**]。 通知訊息可讓您知道備份資料已被刪除。

### <a name="using-azure-powershell"></a>使用 Azure Powershell

如果在停用虛刪除之前刪除了專案，則會處於虛刪除狀態。 若要立即刪除它們，刪除作業必須反轉，然後再次執行。

識別處於虛刪除狀態的專案。

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

然後，反轉啟用虛刪除時所執行的刪除作業。

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

因為虛刪除現在已停用，所以刪除作業會導致立即移除備份資料。

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

### <a name="using-rest-api"></a>使用 REST API

如果在停用虛刪除之前刪除了專案，則會處於虛刪除狀態。 若要立即刪除它們，刪除作業必須反轉，然後再次執行。

1. 首先，使用[此處](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data)所述的步驟來復原刪除作業。
2. 然後使用[此處](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)所述的步驟，透過 REST API 停用虛刪除功能。
3. 然後使用 REST API 來刪除備份，如[這裡](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)所述。

## <a name="encryption"></a>加密

### <a name="encryption-of-backup-data-using-microsoft-managed-keys"></a>使用 Microsoft 管理的金鑰來加密備份資料

備份資料會使用 Azure 儲存體加密來自動加密。 加密可保護您的資料，並協助您符合組織的安全性和合規性承諾。 資料會使用256位 AES 加密（可用的最強區塊密碼之一），以透明的方式加密和解密，且符合 FIPS 140-2 規範。 Azure 儲存體加密類似于 Windows 上的 BitLocker 加密。

在 Azure 中，Azure 儲存體與保存庫之間傳輸中的資料會受到 HTTPS 保護。 此資料會保持在 Azure 中樞網路上。

如需詳細資訊，請參閱待用[資料的加密 Azure 儲存體](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)。 請參閱[AZURE 備份常見問題](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#encryption)，以回答您可能會有關于加密的任何問題。

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>使用客戶管理的金鑰來加密備份資料

備份 Azure 虛擬機器時，您也可以選擇使用儲存在 Azure Key Vault 中的加密金鑰，來加密復原服務保存庫中的備份資料。

>[!NOTE]
>這項功能目前正在早期使用。 如果您想要使用客戶管理的金鑰來加密備份資料，請填寫[這份問卷](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURE9TTDRIUEUyNFhNT1lZS1BNVDdZVllHWi4u)。 請注意，使用這項功能的功能可能會受到 Azure 備份服務的核准。

### <a name="backup-of-managed-disk-vm-encrypted-using-customer-managed-keys"></a>使用客戶管理的金鑰進行加密的受控磁片 VM 備份

Azure 備份可讓您備份 Azure 虛擬機器，其中包含使用客戶管理的金鑰加密的磁片。 如需詳細資訊，請參閱[使用客戶管理的金鑰來加密受控磁片](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys)。

### <a name="backup-of-encrypted-vms"></a>備份已加密的 Vm

您可以使用 Azure 備份服務，備份及還原具有加密磁片的 Windows 或 Linux Azure 虛擬機器（Vm）。 如需指示，請參閱[使用 Azure 備份備份和還原已加密的虛擬機器](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)。

## <a name="other-security-features"></a>其他安全性功能

### <a name="protection-of-azure-backup-recovery-points"></a>保護 Azure 備份復原點

復原服務保存庫所使用的儲存體帳戶會被隔離，而且使用者無法存取以因應任何惡意用途。 只有 Azure 備份管理作業（例如 restore）允許存取。 這些管理作業是透過以角色為基礎的存取控制（RBAC）來控制。

如需詳細資訊，請參閱[使用以角色為基礎的存取控制來管理 Azure 備份復原點](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault)。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="for-soft-delete"></a>虛刪除

#### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>我是否需要在每個保存庫上啟用虛刪除功能？

否，預設會針對所有復原服務保存庫建立並啟用。

#### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-delete-operation-is-complete"></a>我可以設定在刪除作業完成後，我的資料會保留在虛刪除狀態的天數嗎？

否，在刪除作業之後，它會固定為14天的額外保留期。

#### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>我是否需要支付此額外14天保留期的費用？

否，這是14天的額外保留空間，不是虛刪除功能的一部分。

#### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>當我的資料處於虛刪除狀態時，可以執行還原作業嗎？

否，您必須取消刪除已虛刪除的資源，才能還原。 取消刪除作業會將資源恢復為 [**停止保護並保留資料] 狀態**，讓您可以還原到任何時間點。 在此狀態下，垃圾收集行程會保持暫停。

#### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>我的快照集會遵循與保存庫中的復原點相同的生命週期嗎？

是。

#### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>如何針對虛刪除的資源，再次觸發排程備份？

取消刪除後接著繼續作業將會再次保護資源。 Resume 作業會建立備份原則的關聯，以觸發已選取保留期限的排程備份。 此外，一旦繼續作業完成，垃圾收集行程就會立即執行。 如果您想要從已超過到期日的復原點執行還原，建議您在觸發繼續作業之前先執行此操作。

#### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>如果保存庫中有虛刪除的專案，是否可以刪除我的保存庫？

如果保存庫中有已虛刪除狀態的備份專案，就無法刪除復原服務保存庫。 刪除作業之後的14天會永久刪除虛刪除的專案。 如果您無法等待14天，則請[停](#disabling-soft-delete)用虛刪除、取消刪除虛刪除的專案，然後再將它們刪除，以永久刪除。 確保沒有受保護的專案，而且沒有虛刪除的專案之後，即可刪除保存庫。  

#### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>刪除之後，我可以刪除14天之前的資料嗎？

否。 您不能強制刪除虛刪除的專案，它們會在14天之後自動刪除。 這項安全性功能可防止意外或惡意刪除已備份的資料。  在 VM 上執行任何其他動作之前，您應該先等待14天。  虛刪除的專案將不會收費。  如果您需要將14天內標記為要虛刪除的 Vm 重新保護至新的保存庫，請洽詢 Microsoft 支援服務。

#### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>可以在 PowerShell 或 CLI 中執行虛刪除作業嗎？

您可以使用[Powershell](#soft-delete-for-vms-using-azure-powershell)來執行虛刪除作業。 目前不支援 CLI。

#### <a name="is-soft-delete-supported-for-other-cloud-workloads-like-sql-server-in-azure-vms-and-sap-hana-in-azure-vms"></a>是否支援其他雲端工作負載的虛刪除，例如 Azure Vm 中的 SQL Server 和 Azure Vm 中的 SAP Hana？

否。 目前只有 Azure 虛擬機器支援虛刪除。

## <a name="next-steps"></a>後續步驟

- 閱讀[Azure 備份的安全性控制](backup-security-controls.md)。
