---
title: 使用客戶管理的金鑰來加密備份資料
description: 瞭解 Azure 備份如何讓您使用客戶管理的金鑰 (CMK) 來加密備份資料。
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 30bcf907e1a2759c8a9977e50cb4880c2e254ca2
ms.sourcegitcommit: 61d2b2211f3cc18f1be203c1bc12068fc678b584
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/18/2021
ms.locfileid: "98562755"
---
# <a name="encryption-of-backup-data-using-customer-managed-keys"></a>使用客戶管理的金鑰來加密備份資料

Azure 備份可讓您使用客戶管理的金鑰 (CMK) 來加密備份資料，而不是使用預設啟用的平臺管理金鑰。 用來加密備份資料的金鑰必須儲存在 [Azure Key Vault](../key-vault/index.yml)中。

用於加密備份的加密金鑰可能與用於來源的加密金鑰不同。 使用 AES 256 型資料加密金鑰來保護資料 (DEK) ，然後使用您的金鑰來保護 (KEK) 。 這可讓您完整控制資料和金鑰。 若要允許加密，必須將 Azure Key Vault 中的加密金鑰存取權授與復原服務保存庫。 您可以視需要變更金鑰。

本文將討論下列各項：

- 建立復原服務保存庫
- 設定您的復原服務保存庫，以使用客戶管理的金鑰來加密備份資料
- 執行備份至使用客戶管理金鑰加密的保存庫
- 從備份還原資料

## <a name="before-you-start"></a>在您開始使用 Intune 之前

- 這項功能可讓您 **僅加密新** 的復原服務保存庫。 不支援任何包含已註冊或嘗試註冊之現有專案的保存庫。

- 啟用復原服務保存庫後，使用客戶管理的金鑰進行加密，就無法使用平臺管理的金鑰 (預設) 還原回。 您可以根據您的需求變更加密金鑰。

- 這項功能目前 **不支援使用 MARS 代理程式進行備份**，而且您可能無法使用 CMK 加密的保存庫進行相同的工作。 MARS 代理程式會使用以使用者複雜密碼為基礎的加密。 這項功能也不支援傳統 Vm 的備份。

- 這項功能與 [Azure 磁碟加密](../security/fundamentals/azure-disk-encryption-vms-vmss.md)無關，後者會使用 Windows) 的 BitLocker (和適用于 Linux 的 DM-Crypt (，來使用虛擬機器磁片的來賓型加密) 

- 復原服務保存庫只能使用儲存在 Azure Key Vault （位於 **相同區域**）中的金鑰進行加密。 此外，金鑰必須只有 **RSA 2048 金鑰** ，且應該處於 **啟用** 狀態。

- 目前不支援在資源群組和訂用帳戶之間移動 CMK 加密的復原服務保存庫。

- 這項功能可以透過 Azure 入口網站和 PowerShell 來設定。

    >[!NOTE]
    >使用 Az module 5.3.0 或更高版本，將客戶管理的金鑰用於復原服務保存庫中的備份。

如果您尚未建立並設定您的復原服務保存庫，您可以在 [這裡閱讀如何進行](backup-create-rs-vault.md)。

## <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>將保存庫設定為使用客戶管理的金鑰進行加密

本節包含下列步驟：

1. 啟用復原服務保存庫的受控識別

1. 將許可權指派給保存庫，以存取 Azure Key Vault 中的加密金鑰

1. 啟用 Azure Key Vault 的虛刪除和清除保護

1. 將加密金鑰指派給復原服務保存庫

所有這些步驟都必須依照上面所述的順序來達成預期的結果。 以下將詳細討論每個步驟。

### <a name="enable-managed-identity-for-your-recovery-services-vault"></a>啟用復原服務保存庫的受控識別

Azure 備份使用系統指派的受控識別來驗證復原服務保存庫，以存取儲存在 Azure Key Vault 中的加密金鑰。 若要為您的復原服務保存庫啟用受控識別，請遵循下列所述的步驟。

>[!NOTE]
>啟用之後，就 **不** 能 (停用受控識別，即使暫時) 也是如此。 停用受控身分識別可能會導致不一致的行為。

**在入口網站中：**

1. 移至您的復原服務保存庫-> 身分 **識別**

    ![身分識別設定](./media/encryption-at-rest-with-cmk/managed-identity.png)

1. 將 **狀態** 變更為 [ **開啟** ]，然後選取 [ **儲存**]。

1. 系統會產生物件識別碼，也就是系統指派的保存庫受控識別。

**使用 PowerShell：**

使用 [>new-azrecoveryservicesvault](https://docs.microsoft.com/powershell/module/az.recoveryservices/update-azrecoveryservicesvault) 命令為復原服務保存庫啟用系統指派的受控識別。

範例：

```AzurePowerShell
$vault=Get-AzRecoveryServicesVault -ResourceGroupName "testrg" -Name "testvault"

Update-AzRecoveryServicesVault -IdentityType SystemAssigned -VaultId $vault.ID

$vault.Identity | fl
```

輸出：

```output
PrincipalId : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
TenantId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Type        : SystemAssigned
```

### <a name="assign-permissions-to-the-recovery-services-vault-to-access-the-encryption-key-in-the-azure-key-vault"></a>將許可權指派給復原服務保存庫，以存取 Azure Key Vault 中的加密金鑰

您現在需要允許復原服務保存庫存取包含加密金鑰的 Azure Key Vault。 這是藉由允許復原服務保存庫的受控識別來存取 Key Vault 來完成。

**在入口網站中**：

1. 移至您的 Azure Key Vault > **存取原則**。 繼續前往 **+ 新增存取原則**。

    ![新增存取原則](./media/encryption-at-rest-with-cmk/access-policies.png)

1. 在 [ **金鑰許可權**] 底下，選取 [ **取得**、 **列出**、解除包裝 **金鑰** ] 和 [ **包裝金鑰** 作業]。 這會指定要允許的索引鍵上的動作。

    ![指派金鑰許可權](./media/encryption-at-rest-with-cmk/key-permissions.png)

1. 移至 [ **選取主體** ]，並使用其名稱或受控識別在搜尋方塊中搜尋您的保存庫。 一旦顯示之後，請選取保存庫，然後選擇窗格底部的 [ **選取** ]。

    ![選取主體](./media/encryption-at-rest-with-cmk/select-principal.png)

1. 完成之後，請 **選取 [新增]** 以新增新的存取原則。

1. 選取 [ **儲存** ] 以儲存對 Azure Key Vault 存取原則所做的變更。

**使用 PowerShell**：

使用 [AzRecoveryServicesVaultProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultproperty) 命令可使用客戶管理的金鑰來啟用加密，以及指派或更新要使用的加密金鑰。

範例：

```azurepowershell
$keyVault = Get-AzKeyVault -VaultName "testkeyvault" -ResourceGroupName "testrg" 
$key = Get-AzKeyVaultKey -VaultName $keyVault -Name "testkey" 
Set-AzRecoveryServicesVaultProperty -EncryptionKeyId $key.ID -KeyVaultSubscriptionId "xxxx-yyyy-zzzz"  -VaultId $vault.ID


$enc=Get-AzRecoveryServicesVaultProperty -VaultId $vault.ID
$enc.encryptionProperties | fl
```

輸出：

```output
EncryptionAtRestType          : CustomerManaged
KeyUri                        : testkey
SubscriptionId                : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx 
LastUpdateStatus              : Succeeded
InfrastructureEncryptionState : Disabled
```

### <a name="enable-soft-delete-and-purge-protection-on-the-azure-key-vault"></a>啟用 Azure Key Vault 的虛刪除和清除保護

您必須在儲存加密金鑰的 Azure Key Vault 上 **啟用虛刪除和清除保護** 。 您可以從 Azure Key Vault UI 進行此作業，如下所示。  (另外，您也可以在建立 Key Vault) 時設定這些屬性。 請 [在這裡](../key-vault/general/soft-delete-overview.md)閱讀更多有關這些 Key Vault 屬性的資訊。

![啟用虛刪除和清除保護](./media/encryption-at-rest-with-cmk/soft-delete-purge-protection.png)

您也可以使用下列步驟，透過 PowerShell 啟用虛刪除和清除保護：

1. 登入您的 Azure 帳戶。

    ```azurepowershell
    Login-AzAccount
    ```

1. 選取包含保存庫的訂用帳戶。

    ```azurepowershell
    Set-AzContext -SubscriptionId SubscriptionId
    ```

1. 啟用虛刪除

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

1. 啟用清除保護

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

### <a name="assign-encryption-key-to-the-rs-vault"></a>將加密金鑰指派給 RS 保存庫

>[!NOTE]
> 繼續進行之前，請確定下列事項：
>
> - 以上所述的所有步驟都已成功完成：
>   - 復原服務保存庫的受控識別已啟用，且已獲指派必要的許可權
>   - Azure Key Vault 已啟用虛刪除和清除保護
> - 您要啟用 CMK 加密的復原服務保存 **庫沒有任何** 專案受到保護或註冊

確認上述各項之後，請繼續選取您保存庫的加密金鑰。

若要指派金鑰：

1. 移至您的復原服務保存庫-> **屬性**

    ![加密設定](./media/encryption-at-rest-with-cmk/encryption-settings.png)

1. 選取 [**加密設定**] 下的 [**更新**]。

1. 在 [加密設定] 窗格中，選取 [ **使用您自己的金鑰** ]，然後使用下列其中一種方式繼續指定金鑰。 **確定您要使用的金鑰是處於已啟用狀態的 RSA 2048 金鑰。**

    1. 輸入您要用來加密此復原服務保存庫中資料的 **金鑰 URI** 。 您也必須指定訂用帳戶，其中包含此金鑰) 的 Azure Key Vault (存在。 您可以從 Azure Key Vault 中的對應機碼取得此金鑰 URI。 請確認金鑰 URI 已正確複製。 建議您使用金鑰識別碼所提供的 [ **複製到剪貼** 簿] 按鈕。

        >[!NOTE]
        >使用金鑰 URI 指定加密金鑰時，金鑰將不會自動輪替。 因此，必須在必要時指定新金鑰，以手動方式完成金鑰更新。

        ![輸入金鑰 URI](./media/encryption-at-rest-with-cmk/key-uri.png)

    1. 在 [金鑰選擇器] 窗格中，流覽並選取 Key Vault 中的機碼。

        >[!NOTE]
        >使用金鑰選擇器窗格來指定加密金鑰時，只要啟用新版本的金鑰，就會自動旋轉金鑰。

        ![從 key vault 選取金鑰](./media/encryption-at-rest-with-cmk/key-vault.png)

1. 選取 [儲存]。

1. **追蹤加密金鑰更新的進度和狀態**：您可以使用左側導覽列上的 [ **備份作業** ] 視圖，追蹤加密金鑰指派的進度和狀態。 狀態應該會變更為 [ **已完成**]。 您的保存庫現在會將具有指定索引鍵的所有資料加密為 KEK。

    ![狀態已完成](./media/encryption-at-rest-with-cmk/status-succeeded.png)

    加密金鑰更新也會記錄在保存庫的活動記錄中。

    ![活動記錄檔](./media/encryption-at-rest-with-cmk/activity-log.png)

>[!NOTE]
> 當您想要更新或變更加密金鑰時，此程式會維持不變。 如果您想要更新和使用來自另一個 Key Vault 的金鑰 (不同于目前) 的金鑰，請確定：
>
> - Key Vault 位於與復原服務保存庫相同的區域中
>
> - 金鑰保存庫已啟用虛刪除和清除保護
>
> - 復原服務保存庫具有存取 Key Vault 的必要許可權。

## <a name="backing-up-to-a-vault-encrypted-with-customer-managed-keys"></a>備份至使用客戶管理金鑰加密的保存庫

在繼續設定保護之前，強烈建議您確定遵守下列檢查清單。 這點很重要，因為一旦專案已設定為要備份 (或嘗試將) 設定為非 CMK 加密保存庫，則無法在其上啟用使用客戶管理金鑰進行加密，且會繼續使用平臺管理的金鑰。

>[!IMPORTANT]
> 在繼續設定保護之前，您必須已 **成功** 完成下列步驟：
>
>1. 已建立您的備份保存庫
>1. 已啟用備份保存庫系統指派的受控識別
>1. 已指派您備份保存庫的許可權，以從您的 Key Vault 存取加密金鑰
>1. 啟用 Key Vault 的虛刪除和清除保護
>1. 已為您的備份保存庫指派有效的加密金鑰
>
>如果已確認上述所有步驟，則只需繼續設定備份。

針對使用客戶管理金鑰加密的復原服務保存庫設定和執行備份的程式，與使用平臺管理金鑰的保存庫相同， **不需要變更體驗**。 這適用于 [備份 Azure vm](./quick-backup-vm-portal.md) ，以及備份在 VM 內執行的工作負載 (例如 [SAP Hana](./tutorial-backup-sap-hana-db.md) [SQL Server](./tutorial-sql-backup.md) 資料庫) 。

## <a name="restoring-data-from-backup"></a>從備份還原資料

### <a name="vm-backup"></a>VM 備份

您可以根據 [此處](./backup-azure-arm-restore-vms.md)所述的步驟，還原儲存在復原服務保存庫中的資料。 從使用客戶管理金鑰加密的復原服務保存庫還原時，您可以選擇使用磁片加密集 (DES) 來加密還原的資料。

#### <a name="restoring-vm--disk"></a>正在還原 VM/磁片

1. 從「快照集」復原點復原磁片/VM 時，還原的資料會以用來加密來源 VM 磁片的 DES 進行加密。

1. 從復原類型為「保存庫」的復原點復原磁碟/VM 時，您可以選擇讓還原的資料使用 DES 加密（在還原時指定）。 或者，您可以選擇在不指定 DES 的情況下繼續還原資料，在這種情況下，它會使用 Microsoft 管理的金鑰進行加密。

還原完成後，您可以將還原的磁片/VM 加密，不論起始還原時所做的選擇為何。

![還原點](./media/encryption-at-rest-with-cmk/restore-points.png)

#### <a name="select-a-disk-encryption-set-while-restoring-from-vault-recovery-point"></a>從保存庫復原點還原時，請選取磁片加密集

**在入口網站中**：

磁片加密集是在 [還原] 窗格的 [加密設定] 下指定，如下所示：

1. 在 [ **加密磁片 (s]) 使用您的金鑰**，請選取 **[是]**。

1. 從下拉式清單中，選取您要用於還原的磁片 (s) 的 DES。 **確定您具有 DES 的存取權。**

>[!NOTE]
>如果您要還原使用 Azure 磁碟加密的 VM，就無法在還原時選擇 DES。

![使用您的金鑰將磁片加密](./media/encryption-at-rest-with-cmk/encrypt-disk-using-your-key.png)

**使用 PowerShell**：

使用 [>backup-azrecoveryservicesbackupitem](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) 命令搭配參數 [ `-DiskEncryptionSetId <string>` ]，以 [指定](https://docs.microsoft.com/powershell/module/az.compute/get-azdiskencryptionset) 要用來加密復原磁碟的 DES。 如需從 VM 備份復原磁碟的詳細資訊，請參閱 [這篇文章](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#restore-an-azure-vm)。

範例：

```azurepowershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $vault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $vault.ID
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $vault.ID
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -DiskEncryptionSetId “testdes1” -VaultId $vault.ID
```

#### <a name="restoring-files"></a>還原檔案

執行檔案還原時，還原的資料會以用來加密目標位置的金鑰進行加密。

### <a name="restoring-sap-hanasql-databases-in-azure-vms"></a>在 Azure Vm 中還原 SAP Hana/SQL 資料庫

從執行于 Azure VM 中的備份 SAP Hana/SQL 資料庫還原時，會使用目標儲存位置使用的加密金鑰來加密還原的資料。 它可能是客戶管理的金鑰，或用來加密 VM 磁片的平臺管理金鑰。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="can-i-encrypt-an-existing-backup-vault-with-customer-managed-keys"></a>我可以使用客戶管理的金鑰來加密現有的備份保存庫嗎？

否，只能針對新的保存庫啟用 CMK 加密。 所以保存庫絕對不能有任何受保護的專案。 事實上，在使用客戶管理的金鑰啟用加密之前，都不會嘗試保護保存庫的任何專案。

### <a name="i-tried-to-protect-an-item-to-my-vault-but-it-failed-and-the-vault-still-doesnt-contain-any-items-protected-to-it-can-i-enable-cmk-encryption-for-this-vault"></a>我已嘗試保護保存庫的專案，但失敗，而且保存庫仍未包含任何受保護的專案。 我可以為此保存庫啟用 CMK 加密嗎？

否，保存庫在過去必須沒有任何嘗試保護其任何專案。

### <a name="i-have-a-vault-thats-using-cmk-encryption-can-i-later-revert-to-encryption-using-platform-managed-keys-even-if-i-have-backup-items-protected-to-the-vault"></a>我有一個使用 CMK 加密的保存庫。 我稍後是否可以使用平臺管理的金鑰來還原至加密，即使我的備份專案受保護于保存庫也是一樣？

否，一旦啟用 CMK 加密，就無法將它還原為使用平臺管理的金鑰。 您可以根據您的需求變更所使用的金鑰。

### <a name="does-cmk-encryption-for-azure-backup-also-apply-to-azure-site-recovery"></a>Azure 備份的 CMK 加密是否也適用于 Azure Site Recovery？

否，本文只討論備份資料的加密。 針對 Azure Site Recovery，您需要個別設定屬性（可從服務中使用）。

### <a name="i-missed-one-of-the-steps-in-this-article-and-went-on-to-protect-my-data-source-can-i-still-use-cmk-encryption"></a>我錯過了本文中的其中一個步驟，並在保護我的資料來源。 我仍然可以使用 CMK 加密嗎？

若未遵循本文中的步驟並繼續保護專案，可能會導致保存庫無法使用客戶管理的金鑰來使用加密。 因此，建議您在繼續保護專案之前，先參考 [此檢查清單](#backing-up-to-a-vault-encrypted-with-customer-managed-keys) 。

### <a name="does-using-cmk-encryption-add-to-the-cost-of-my-backups"></a>使用 CMK 加密是否新增至我的備份成本？

使用 CMK encryption 進行備份，不會對您產生任何額外的成本。 不過，您可能會繼續產生使用您的 Azure Key Vault 儲存金鑰的費用。

## <a name="next-steps"></a>後續步驟

- [Azure 備份中的安全性功能概觀](security-overview.md)
