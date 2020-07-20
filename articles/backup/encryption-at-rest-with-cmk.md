---
title: 使用客戶管理的金鑰來加密備份資料
description: 瞭解 Azure 備份如何讓您使用客戶管理的金鑰來加密備份資料 (CMK) 。
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: ee64b9f2c6d260d91763cbe2d339640a9fab9967
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86172514"
---
# <a name="encryption-of-backup-data-using-customer-managed-keys"></a>使用客戶管理的金鑰來加密備份資料

Azure 備份可讓您使用客戶管理的金鑰 (CMK) 來加密備份資料，而不是使用預設啟用的平臺管理金鑰。 用來加密備份資料的金鑰必須儲存在[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/)中。

用於加密備份的加密金鑰可能與來源所使用的不同。 系統會使用以 AES 256 為基礎的資料加密金鑰來保護資料 (DEK) ，而這會使用您的金鑰 (KEK) 來加以保護。 這可讓您完全掌控資料和金鑰。 若要允許加密，復原服務保存庫必須被授與 Azure Key Vault 中加密金鑰的存取權。 您可以視需要將金鑰變更為和。

本文討論下列各項：

- 建立復原服務保存庫
- 將復原服務保存庫設定為使用客戶管理的金鑰來加密備份資料
- 執行使用客戶管理的金鑰加密的保存庫備份
- 從備份還原資料

## <a name="before-you-start"></a>在您開始使用 Intune 之前

- 這項功能可讓您**僅加密新**的復原服務保存庫。 不支援包含已註冊或嘗試向其註冊之現有專案的任何保存庫。

- 一旦啟用復原服務保存庫，使用客戶管理金鑰的加密將無法還原回使用平臺管理的金鑰 (預設) 。 您可以根據您的需求變更加密金鑰。

- 這項功能目前**不支援使用 MARS 代理程式進行備份**，而且您可能無法使用 CMK 加密的保存庫來進行相同的工作。 MARS 代理程式會使用以使用者複雜密碼為基礎的加密。 這項功能也不支援傳統 Vm 的備份。

- 這項功能與[Azure 磁碟加密](https://docs.microsoft.com/azure/security/fundamentals/azure-disk-encryption-vms-vmss)無關，其使用適用于 Windows) 的 BitLocker (以來賓為基礎的 VM 磁片加密，以及適用于 LINUX 的 DM Crypt () 

- 復原服務保存庫只能使用儲存在 Azure Key Vault （位於**相同區域**）中的金鑰進行加密。 此外，金鑰只能是**RSA 2048 金鑰**，且應為 [**已啟用**] 狀態。

- 目前不支援在資源群組和訂用帳戶之間移動 CMK 加密的復原服務保存庫。

- 這項功能目前僅可從 Azure 入口網站設定。

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>將保存庫設定為使用客戶管理的金鑰進行加密

本節包含下列步驟：

1. 為您的復原服務保存庫啟用受控識別

1. 指派許可權給保存庫，以存取 Azure Key Vault 中的加密金鑰

1. 在 Azure Key Vault 上啟用虛刪除和清除保護

1. 將加密金鑰指派給復原服務保存庫

所有這些步驟都必須遵循上述的順序，以達到預期的結果。 下面將詳細討論每個步驟。

### <a name="enable-managed-identity-for-your-recovery-services-vault"></a>為您的復原服務保存庫啟用受控識別

Azure 備份會使用系統指派的受控識別來驗證復原服務保存庫，以存取儲存在 Azure Key Vault 中的加密金鑰。 若要為您的復原服務保存庫啟用受控識別，請遵循以下所述的步驟。

>[!NOTE]
>啟用之後，即使暫時) ，也不得停用受控識別 (。 停用受控識別可能會導致不一致的行為。

1. 移至您的復原服務保存庫-> 身分**識別**

    ![身分識別設定](./media/encryption-at-rest-with-cmk/managed-identity.png)

1. 將**狀態**變更為 [**開啟**]，然後按一下 [**儲存**]。

1. 會產生物件識別碼，也就是系統指派的保存庫受控識別。

### <a name="assign-permissions-to-the-recovery-services-vault-to-access-the-encryption-key-in-the-azure-key-vault"></a>指派許可權給復原服務保存庫，以存取 Azure Key Vault 中的加密金鑰

您現在必須允許復原服務保存庫存取包含加密金鑰的 Azure Key Vault。 這是藉由允許復原服務保存庫的受控識別存取 Key Vault 來完成。

1. 移至您的 Azure Key Vault >**存取原則**。 繼續前往 **+ 新增存取原則**。

    ![新增存取原則](./media/encryption-at-rest-with-cmk/access-policies.png)

1. 在 [**金鑰許可權**] 底下，選取 [**取得**]、[**列出**]、[解除包裝**金鑰**] 和 [**包裝金鑰** 這會指定要允許的索引鍵上的動作。

    ![指派金鑰許可權](./media/encryption-at-rest-with-cmk/key-permissions.png)

1. 移至 [**選取主體**]，並使用其名稱或受控識別在搜尋方塊中搜尋您的保存庫。 顯示之後，請選取保存庫，然後按一下窗格底部的 [**選取**]。

    ![選取主體](./media/encryption-at-rest-with-cmk/select-principal.png)

1. 完成後 **，按一下 [新增]** 以加入新的存取原則。

1. 按一下 [**儲存**] 以儲存對 Azure Key Vault 存取原則所做的變更。

### <a name="enable-soft-delete-and-purge-protection-on-the-azure-key-vault"></a>在 Azure Key Vault 上啟用虛刪除和清除保護

您必須在儲存加密金鑰的 Azure Key Vault 上**啟用虛刪除和清除保護**。 您可以從 [Azure Key Vault] UI 執行此動作，如下所示。  (也可以在建立 Key Vault) 時設定這些屬性。 如需這些 Key Vault 屬性的詳細資訊，請參閱[這裡](https://docs.microsoft.com/azure/key-vault/general/overview-soft-delete)。

![啟用虛刪除和清除保護](./media/encryption-at-rest-with-cmk/soft-delete-purge-protection.png)

您也可以使用下列步驟，透過 PowerShell 啟用虛刪除和清除保護：

1. 登入您的 Azure 帳戶。

    ```azurepowershell
    Login-AzAccount
    ```

1. 選取包含您保存庫的訂用帳戶。

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
>   - 復原服務保存庫的受控識別已啟用，並已獲指派必要的許可權
>   - Azure Key Vault 已啟用虛刪除和清除保護
> - 您想要啟用 CMK 加密的復原服務保存庫沒有任何專案受到保護或已註冊到其中

一旦確保上述情況，請繼續選取保存庫的加密金鑰。

若要指派金鑰：

1. 移至您的復原服務保存庫->**屬性**

    ![加密設定](./media/encryption-at-rest-with-cmk/encryption-settings.png)

1. 按一下 [**加密設定**] 底下的 [**更新**]。

1. 在 [加密設定] 窗格中，選取 [**使用您自己的金鑰**]，然後使用下列其中一種方式繼續指定金鑰。 **確定您想要使用的金鑰是 RSA 2048 金鑰，其處於啟用狀態。**

    1. 輸入您要在此復原服務保存庫中用來加密資料的**金鑰 URI** 。 您也必須指定訂用帳戶，其中包含此金鑰) 的 Azure Key Vault (。 這個金鑰 URI 可以從您 Azure Key Vault 中的對應索引鍵取得。 請確定已正確複製金鑰 URI。 建議您使用金鑰識別碼所提供的 [**複製到剪貼**簿] 按鈕。

        ![輸入金鑰 URI](./media/encryption-at-rest-with-cmk/key-uri.png)

    1. 從 [金鑰選擇器] 窗格的 [Key Vault 中，流覽並選取 [金鑰]。

        ![從 key vault 選取金鑰](./media/encryption-at-rest-with-cmk/key-vault.png)

1. 按一下 [儲存]。

1. **追蹤加密金鑰更新的進度：** 您可以使用復原服務保存庫中的**活動記錄**，追蹤金鑰指派的進度。 狀態應該很快就會變更為 [**成功**]。 您的保存庫現在會將具有指定金鑰的所有資料加密為 KEK。

    ![使用活動記錄追蹤進度](./media/encryption-at-rest-with-cmk/activity-log.png)

    ![狀態成功](./media/encryption-at-rest-with-cmk/status-succeeded.png)

>[!NOTE]
> 當您想要更新/變更加密金鑰時，此程式會保持不變。 如果您想要更新並使用另一個 Key Vault 的金鑰 (不同于目前使用的) ，請確定：
>
> - Key Vault 位於與復原服務保存庫相同的區域中
>
> - 金鑰保存庫已啟用虛刪除和清除保護
>
> - 復原服務保存庫具有存取 Key Vault 的必要許可權。

## <a name="backing-up-to-a-vault-encrypted-with-customer-managed-keys"></a>備份至以客戶管理的金鑰加密的保存庫

在繼續設定保護之前，強烈建議您先確定遵守下列檢查清單。 這一點很重要，因為一旦專案已設定為要備份 (或嘗試) 設定為不 CMK 加密的保存庫，則不能在其上啟用使用客戶管理金鑰的加密，它會繼續使用平臺管理的金鑰。

>[!IMPORTANT]
> 在繼續設定保護之前，您必須已**成功**完成下列步驟：
>
>1. 已啟用您的訂用帳戶，以針對您的備份保存庫使用客戶管理的金鑰。
>1. 已建立您的備份保存庫
>1. 已啟用備份保存庫系統指派的受控識別
>1. 已將許可權指派給您的備份保存庫，以從您的 Key Vault 存取加密金鑰
>1. 已啟用 Key Vault 的虛刪除和清除保護
>1. 已為您的備份保存庫指派有效的加密金鑰
>
>如果上述所有步驟都已確認，則只會繼續進行備份的設定。

設定及執行以客戶管理金鑰加密之復原服務保存庫備份的程式，與使用平臺管理金鑰的保存庫相同，**不會變更經驗**。 這適用于[Azure vm 的備份](https://docs.microsoft.com/azure/backup/quick-backup-vm-portal)，以及在 VM 內執行的工作負載備份 (例如， [SAP Hana](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db) [SQL Server](https://docs.microsoft.com/azure/backup/tutorial-sql-backup)資料庫) 。

## <a name="restoring-data-from-backup"></a>從備份還原資料

### <a name="vm-backup"></a>VM 備份

儲存在復原服務保存庫中的資料可以根據[這裡](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms)所述的步驟來還原。 從使用客戶管理的金鑰加密的復原服務保存庫進行還原時，您可以選擇使用 (DES) 的磁片加密集來加密還原的資料。

#### <a name="restoring-vm--disk"></a>正在還原 VM/磁片

1. 從「快照集」復原點復原磁片/VM 時，會使用用來加密來源 VM 磁片的 DES 來加密還原的資料。

1. 從復原類型為「保存庫」的復原點復原磁碟/VM 時，您可以選擇使用在還原時指定的 DES 來加密還原的資料。 或者，您也可以選擇繼續還原資料，而不指定 DES，在此情況下，它會使用 Microsoft 管理的金鑰進行加密。

還原完成後，您可以加密還原的磁片/VM，而不論起始還原時所做的選擇為何。

![還原點](./media/encryption-at-rest-with-cmk/restore-points.png)

#### <a name="select-a-disk-encryption-set-while-restoring-from-vault-recovery-point"></a>從保存庫復原點還原時選取磁片加密集

磁片加密組會在 [還原] 窗格中的 [加密設定] 下指定，如下所示：

1. 在 [**加密磁片 (s]) 使用您的金鑰**，選取 **[是]**。

1. 從下拉式清單中，選取您想要用於還原的磁片 (s) 的 DES。 **請確定您具有 DES 的存取權。**

>[!NOTE]
>如果您要還原使用 Azure 磁碟加密的 VM，則可以在還原時選擇 DES 的功能無法使用。

![使用您的金鑰加密磁片](./media/encryption-at-rest-with-cmk/encrypt-disk-using-your-key.png)

#### <a name="restoring-files"></a>還原檔案

執行檔案還原時，還原的資料會以用來加密目標位置的金鑰進行加密。

### <a name="restoring-sap-hanasql-databases-in-azure-vms"></a>還原 Azure Vm 中的 SAP Hana/SQL 資料庫

從在 Azure VM 中執行的備份 SAP Hana/SQL 資料庫還原時，會使用目標儲存位置使用的加密金鑰來加密還原的資料。 它可能是客戶管理的金鑰，或是用來加密 VM 磁片的平臺管理金鑰。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="can-i-encrypt-an-existing-backup-vault-with-customer-managed-keys"></a>我可以使用客戶管理的金鑰來加密現有的備份保存庫嗎？

否，只能為新的保存庫啟用 CMK 加密。 因此，保存庫永遠不能有任何受保護的專案。 事實上，在使用客戶管理的金鑰啟用加密之前，都必須先嘗試保護保存庫的任何專案。

### <a name="i-tried-to-protect-an-item-to-my-vault-but-it-failed-and-the-vault-still-doesnt-contain-any-items-protected-to-it-can-i-enable-cmk-encryption-for-this-vault"></a>我嘗試保護保存庫中的某個專案，但失敗，而且保存庫仍未包含任何受保護的專案。 我可以啟用此保存庫的 CMK 加密嗎？

不可以，保存庫在過去不能嘗試保護任何專案。

### <a name="i-have-a-vault-that-is-using-cmk-encryption-can-i-later-revert-to-encryption-using-platform-managed-keys-even-if-i-have-backup-items-protected-to-the-vault"></a>我有一個使用 CMK 加密的保存庫。 即使我有保護保存庫的備份專案，我稍後可以使用平臺管理的金鑰來還原加密嗎？

否，一旦您啟用 CMK 加密，就無法將其還原為使用平臺管理的金鑰。 您可以根據您的需求變更所使用的金鑰。

### <a name="does-cmk-encryption-for-azure-backup-also-apply-to-azure-site-recovery"></a>Azure 備份的 CMK 加密是否也適用于 Azure Site Recovery？

否，本文只討論備份資料的加密。 針對 Azure Site Recovery，您需要將屬性分開設定為可從服務取得。

### <a name="i-missed-one-of-the-steps-in-this-article-and-went-on-to-protect-my-data-source-can-i-still-use-cmk-encryption"></a>我錯過了這篇文章中的其中一項步驟，並已進入保護我的資料來源。 我仍然可以使用 CMK 加密嗎？

未遵循本文中的步驟並繼續保護專案，可能會導致保存庫無法使用客戶管理的金鑰進行加密。 因此，建議您先參閱[這份檢查清單](#backing-up-to-a-vault-encrypted-with-customer-managed-keys)，然後再繼續保護專案。

### <a name="does-using-cmk-encryption-add-to-the-cost-of-my-backups"></a>使用 CMK 加密會增加我的備份成本嗎？

使用 CMK 加密進行備份並不會對您產生任何額外的成本。 不過，您可能會繼續產生使用您的 Azure Key Vault 儲存金鑰的費用。

## <a name="next-steps"></a>後續步驟

- [Azure 備份中的安全性功能概觀](security-overview.md)
