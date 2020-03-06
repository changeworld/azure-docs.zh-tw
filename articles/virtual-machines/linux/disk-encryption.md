---
title: Azure 受控磁碟 Azure CLI 的伺服器端加密
description: Azure 儲存體保護您的資料，方法是在將它保存到儲存體叢集之前，將它加密。 您可以依賴 Microsoft 管理的金鑰來加密您的受控磁片，也可以使用客戶管理的金鑰來管理使用您自己的金鑰進行加密。
author: roygara
ms.date: 01/13/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.openlocfilehash: 48935dc74d6d22bcafa31afebbdfcb829e122815
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399239"
---
# <a name="server-side-encryption-of-azure-managed-disks"></a>Azure 受控磁片的伺服器端加密

Azure 受控磁片預設會在將資料保存到雲端時，自動將您的資料加密。 伺服器端加密可保護您的資料，並協助您符合組織的安全性和合規性承諾。 Azure 受控磁片中的資料會使用256位[AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)（可用的最強區塊密碼之一）以透明的方式進行加密，且符合 FIPS 140-2 規範。   

加密不會影響受控磁片的效能。 加密不會產生額外的費用。

如需 Azure 受控磁片基礎密碼編譯模組的詳細資訊，請參閱[密碼編譯 API：新一代](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

## <a name="about-encryption-key-management"></a>關於加密金鑰管理

您可以依賴平臺管理的金鑰來加密受控磁片，也可以使用您自己的金鑰來管理加密。 如果您選擇使用自己的金鑰來管理加密，您可以指定*客戶管理的金鑰*，以用於加密和解密受控磁片中的所有資料。 

下列各節會更詳細地說明金鑰管理的每個選項。

## <a name="platform-managed-keys"></a>平臺管理的金鑰

根據預設，受控磁片會使用平臺管理的加密金鑰。 自2017年6月10日起，所有新的受控磁片、快照集、映射和寫入至現有受控磁片的新資料，都會自動以平臺管理的金鑰進行待用加密。 

## <a name="customer-managed-keys"></a>客戶管理的金鑰

您可以選擇使用您自己的金鑰來管理每個受控磁片層級的加密。 使用客戶管理的金鑰組受控磁片進行伺服器端加密，可提供 Azure Key Vault 的整合體驗。 您可以將[您的 rsa 金鑰](../../key-vault/key-vault-hsm-protected-keys.md)匯入 Key Vault，或在 Azure Key Vault 中產生新的 rsa 金鑰。 Azure 受控磁片會使用[信封加密](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique)，以完全透明的方式處理加密和解密。 它會使用以[AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 為基礎的資料加密金鑰（DEK）來加密資料，而這也會使用您的金鑰來保護。 您必須在 Key Vault 中授與受控磁片的存取權，以使用您的金鑰來加密和解密 DEK。 這可讓您完全掌控您的資料和金鑰。 您可以隨時停用金鑰或撤銷對受控磁片的存取權。 您也可以使用 Azure Key Vault 監視來審核加密金鑰使用方式，以確保只有受控磁片或其他受信任的 Azure 服務會存取您的金鑰。

下圖顯示受控磁片如何使用 Azure Active Directory 和 Azure Key Vault，以使用客戶管理的金鑰來提出要求：

![受控磁片和客戶管理的金鑰工作流程。 系統管理員會建立 Azure Key Vault，然後建立磁片加密集，並設定磁片加密集。 該集合與 VM 相關聯，可讓磁片使用 Azure AD 進行驗證](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


下列清單更詳細地說明圖表：

1. Azure Key Vault 系統管理員會建立金鑰保存庫資源。
1. 金鑰保存庫系統管理員會匯入其 RSA 金鑰以 Key Vault 或在 Key Vault 中產生新的 RSA 金鑰。
1. 該系統管理員會建立磁片加密集資源的實例，並指定 Azure Key Vault 識別碼和金鑰 URL。 磁片加密集是引進的新資源，可簡化受控磁片的金鑰管理。 
1. 建立磁片加密組時，[系統指派的受控識別](../../active-directory/managed-identities-azure-resources/overview.md)會在 AZURE ACTIVE DIRECTORY （AD）中建立，並與磁片加密集建立關聯。 
1. 然後，Azure 金鑰保存庫系統管理員會授與受控識別許可權，以在金鑰保存庫中執行作業。
1. VM 使用者會建立磁片，方法是將它們與磁片加密集建立關聯。 VM 使用者也可以使用現有資源的客戶管理金鑰，將其與磁片加密集建立關聯，以啟用伺服器端加密。 
1. 受控磁片會使用受控識別，將要求傳送至 Azure Key Vault。
1. 若要讀取或寫入資料，受控磁片會將要求傳送至 Azure Key Vault 以加密（包裝）並解密（解除封裝）資料加密金鑰，以便執行資料的加密和解密。 

若要撤銷對客戶管理的金鑰的存取權，請參閱[Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/)和[Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault)。 撤銷存取權可有效封鎖對儲存體帳戶中所有資料的存取，因為 Azure 儲存體無法存取加密金鑰。

### <a name="supported-regions"></a>支援區域

目前僅支援下欄區域：

- 可在美國東部、美國西部2和美國中南部區域正式提供。
- 可在美國中西部、美國東部2、加拿大中部和北歐地區提供公開預覽。

### <a name="restrictions"></a>限制

目前，客戶管理的金鑰具有下列限制：

- 僅支援大小為2080的「[軟」和「硬性」 RSA 金鑰](../../key-vault/about-keys-secrets-and-certificates.md#keys-and-key-types)，沒有其他金鑰或大小。
- 從使用伺服器端加密和客戶管理金鑰加密的自訂映射建立的磁片，必須使用相同的客戶管理金鑰進行加密，而且必須在相同的訂用帳戶中。
- 從使用伺服器端加密和客戶管理金鑰加密的磁片建立的快照集，必須使用相同的客戶管理金鑰進行加密。
- 使用伺服器端加密和客戶管理的金鑰所加密的自訂映射，無法在共用映射資源庫中使用。
- 與客戶管理的金鑰相關的所有資源（Azure 金鑰保存庫、磁片加密集、Vm、磁片和快照）必須位於相同的訂用帳戶和區域中。
- 使用客戶管理的金鑰加密的磁片、快照集和映射無法移至另一個訂用帳戶。
- 如果您使用 Azure 入口網站建立磁片加密集，則目前無法使用快照。
- 使用客戶管理的金鑰加密的受控磁片，也無法使用 Azure 磁碟加密進行加密。

### <a name="cli"></a>CLI
#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>設定您的 Azure Key Vault 和 DiskEncryptionSet

1. 請確定您已安裝最新的 [Azure CLI](/cli/azure/install-az-cli2) 並使用 [az login](/cli/azure/reference-index) 登入 Azure 帳戶。

1. 建立 Azure Key Vault 和加密金鑰的實例。

    建立 Key Vault 實例時，您必須啟用虛刪除和清除保護。 虛刪除可確保 Key Vault 在指定的保留期間（預設為90天）保留已刪除的金鑰。 清除保護可確保已刪除的金鑰無法永久刪除，直到保留期限結束為止。 這些設定可防止您因為意外刪除而遺失資料。 使用 Key Vault 來加密受控磁片時，這些設定是必要的。

    ```azurecli
    subscriptionId=yourSubscriptionID
    rgName=yourResourceGroupName
    location=WestCentralUS
    keyVaultName=yourKeyVaultName
    keyName=yourKeyName
    diskEncryptionSetName=yourDiskEncryptionSetName
    diskName=yourDiskName

    az account set --subscription $subscriptionId

    az keyvault create -n $keyVaultName -g $rgName -l $location --enable-purge-protection true --enable-soft-delete true

    az keyvault key create --vault-name $keyVaultName -n $keyName --protection software
    ```

1.  建立 DiskEncryptionSet 的實例。 
    
    ```azurecli
    keyVaultId=$(az keyvault show --name $keyVaultName --query [id] -o tsv)

    keyVaultKeyUrl=$(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)

    az disk-encryption-set create -n $diskEncryptionSetName -l $location -g $rgName --source-vault $keyVaultId --key-url $keyVaultKeyUrl
    ```

1.  將金鑰保存庫的存取權授與 DiskEncryptionSet 資源。 

    > [!NOTE]
    > Azure 可能需要幾分鐘的時間，才能在您的 Azure Active Directory 中建立 DiskEncryptionSet 的身分識別。 如果您在執行下列命令時收到「找不到 Active Directory 物件」之類的錯誤，請稍候幾分鐘，然後再試一次。

    ```azurecli
    desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)

    az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get

    az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
    ```

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>使用 Marketplace 映射建立 VM，以客戶管理的金鑰將 OS 和資料磁片加密

```azurecli
rgName=yourResourceGroupName
vmName=yourVMName
location=WestCentralUS
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskencryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vm create -g $rgName -n $vmName -l $location --image $image --size $vmSize --generate-ssh-keys --os-disk-encryption-set $diskEncryptionSetId --data-disk-sizes-gb 128 128 --data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```


#### <a name="encrypt-existing-unattached-managed-disks"></a>加密現有未附加的受控磁片 

您的現有磁片不能連接到執行中的 VM，您可以使用下列腳本將它們加密：

```azurecli
rgName=yourResourceGroupName
diskName=yourDiskName
diskEncryptionSetName=yourDiskEncryptionSetName
 
az disk update -n $diskName -g $rgName --encryption-type EncryptionAtRestWithCustomerKey --disk-encryption-set $diskEncryptionSetId
```

#### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>使用 Marketplace 映射建立虛擬機器擴展集，使用客戶管理的金鑰來加密作業系統和資料磁片

```azurecli
rgName=yourResourceGroupName
vmssName=yourVMSSName
location=WestCentralUS
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskencryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)
az vmss create -g $rgName -n $vmssName --image UbuntuLTS --upgrade-policy automatic --admin-username azureuser --generate-ssh-keys --os-disk-encryption-set $diskEncryptionSetId --data-disk-sizes-gb 64 128 --data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>建立使用伺服器端加密搭配客戶管理的金鑰加密的空磁片，並將其附加至 VM

```azurecli
vmName=yourVMName
rgName=yourResourceGroupName
diskName=yourDiskName
diskSkuName=Premium_LRS
diskSizeinGiB=30
location=WestCentralUS
diskLUN=2
diskEncryptionSetName=yourDiskEncryptionSetName


diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az disk create -n $diskName -g $rgName -l $location --encryption-type EncryptionAtRestWithCustomerKey --disk-encryption-set $diskEncryptionSetId --size-gb $diskSizeinGiB --sku $diskSkuName

diskId=$(az disk show -n $diskName -g $rgName --query [id] -o tsv)

az vm disk attach --vm-name $vmName --lun $diskLUN --ids $diskId 

```

> [!IMPORTANT]
> 客戶管理的金鑰依賴 Azure 資源的受控識別，這是一項 Azure Active Directory （Azure AD）的功能。 當您設定客戶管理的金鑰時，受管理的身分識別會自動指派給您的資源。 如果您之後將訂用帳戶、資源群組或受控磁片從一個 Azure AD 目錄移到另一個，則與受控磁片相關聯的受控識別不會傳輸至新的租使用者，因此客戶管理的金鑰可能無法再使用。 如需詳細資訊，請參閱[在 Azure AD 目錄之間轉移訂用](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)帳戶。

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> 客戶管理的金鑰依賴 Azure 資源的受控識別，這是一項 Azure Active Directory （Azure AD）的功能。 當您設定客戶管理的金鑰時，受管理的身分識別會自動指派給您的資源。 如果您之後將訂用帳戶、資源群組或受控磁片從一個 Azure AD 目錄移到另一個，則與受控磁片相關聯的受控識別不會傳輸至新的租使用者，因此客戶管理的金鑰可能無法再使用。 如需詳細資訊，請參閱[在 Azure AD 目錄之間轉移訂用](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)帳戶。

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>伺服器端加密與 Azure 磁片加密

[虛擬機器和虛擬機器擴展集的 Azure 磁碟加密](../../security/fundamentals/azure-disk-encryption-vms-vmss.md)利用 Windows 的[BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview)功能和 Linux 的[DM CRYPT](https://en.wikipedia.org/wiki/Dm-crypt)功能，在來賓 VM 內使用客戶管理的金鑰來加密受控磁片。  使用客戶管理的金鑰進行伺服器端加密，可讓您透過加密儲存體服務中的資料，為您的 Vm 使用任何 OS 類型和映射，藉此改善 ADE。

## <a name="next-steps"></a>後續步驟

- [探索 Azure Resource Manager 範本，以使用客戶管理的金鑰來建立加密的磁片](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [什麼是 Azure 金鑰保存庫？](../../key-vault/key-vault-overview.md)
- [使用已啟用客戶管理金鑰的磁片來複寫機器](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [使用 PowerShell 設定 VMware Vm 至 Azure 的嚴重損壞修復](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [使用 PowerShell 和 Azure Resource Manager 設定 Hyper-v Vm 的嚴重損壞修復至 Azure](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
