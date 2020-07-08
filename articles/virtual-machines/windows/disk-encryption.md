---
title: Azure 受控磁碟 - PowerShell 的伺服器端加密
description: Azure 儲存體可以先加密待用資料來保護您的資料，然後再將資料保存在儲存體叢集。 您可以依賴 Microsoft 管理的金鑰來加密您的受控磁碟，或使用客戶管理的金鑰，以利用您自己的金鑰來管理加密。
author: roygara
ms.date: 04/21/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-windows
ms.subservice: disks
ms.openlocfilehash: c3a73028350054d54c6714107bfdfa7ead3ee4a3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610421"
---
# <a name="server-side-encryption-of-azure-managed-disks"></a>Azure 受控磁碟的伺服器端加密

Azure 受控磁碟依預設會在將資料保存到雲端時，自動加密您的資料。 伺服器端加密 (SSE) 可保護您的資料安全，並協助您符合組織安全性和合規性承諾。

Azure 受控磁碟中的資料會使用 256 位元的 [AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) (可用的最強區塊編碼器之一)，以透明的方式進行加密，而且符合 FIPS 140-2 規範。 如需有關基礎 Azure 受控磁碟的加密模組詳細資訊，請參閱[密碼編譯 API：新一代](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

加密不會影響受控磁碟的效能，也不會產生額外的加密成本。 

> [!NOTE]
> 暫存磁碟不是受控磁碟，而且不是由 SSE 加密；如需暫存磁碟的詳細資訊，請參閱[受控磁碟概觀：磁碟角色](managed-disks-overview.md#disk-roles)。

## <a name="about-encryption-key-management"></a>關於加密金鑰管理

您可以依賴由平台管理的金鑰來加密受控磁碟，也可以使用您自己的金鑰來管理加密。 如果您選擇使用自己的金鑰來管理加密，您可以指定*客戶管理的金鑰*，以用於加密和解密受控磁碟中的所有資料。 

下列各節將詳細說明金鑰管理的每個選項。

## <a name="platform-managed-keys"></a>平台管理的金鑰

根據預設，受控磁碟會使用平台管理的加密金鑰。 從 2017 年 6 月 10 日開始，所有新的受控磁碟、快照集、映像和寫入至現有受控磁碟的新資料都會使用平台管理的金鑰自動加密待用資料。

## <a name="customer-managed-keys"></a>客戶管理的金鑰

您可以選擇使用自己的金鑰來管理每個受控磁碟層級的加密。 使用客戶管理的金鑰對受控磁碟進行伺服器端加密，提供與 Azure Key Vault 的整合體驗。 您可以將[您的 RSA 金鑰](../../key-vault/keys/hsm-protected-keys.md)匯入 Key Vault，或在 Azure Key Vault 中產生新的 RSA 金鑰。 

Azure 受控磁碟會使用[信封加密](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique)，以完全透明的方式處理加密和解密。 其會使用以 [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 為基礎的資料加密金鑰 (DEK) 來加密資料，而這是使用您的金鑰來保護。 儲存體服務會產生資料加密金鑰，並使用 RSA 加密，透過客戶管理的金鑰進行加密。 信封加密可讓您根據您的相容性原則，定期輪替 (變更) 您的金鑰，而不會影響您的 VM。 當您輪替金鑰時，儲存體服務會使用新的客戶管理的金鑰，重新加密資料加密金鑰。 

您必須在 Key Vault 中授與受控磁碟的存取權，才能使用您的金鑰來加密和解密 DEK。 這可讓您完全控制您的資料和金鑰。 您可以隨時停用金鑰或撤銷對受控磁碟的存取權。 您也可以使用 Azure Key Vault 監視稽核加密金鑰使用量，以確保只有受控磁碟或其他信任的 Azure 服務會存取您的金鑰。

對於進階 SSD、標準 SSD，以及標準 HDD：當您停用或刪除您的金鑰時，對於磁碟使用該金鑰的任何 VM，都將自動關閉。 之後，除非再次啟用金鑰或指派新的金鑰，否則將無法使用 VM。

若是 Ultra 磁碟，當您停用或刪除金鑰時，對於 Ultra 磁碟使用該金鑰的任何 VM，將不會自動關閉。 一旦解除配置並重新啟動 VM 之後，磁碟將會停止使用金鑰，而 VM 將不會重新上線。 若要讓 VM 重新上線，您必須指派新金鑰，或啟用現有金鑰。

下圖顯示受控磁碟如何透過 Azure Active Directory 和 Azure Key Vault，使用客戶管理的金鑰提出要求：

![受控磁碟和客戶管理的金鑰工作流程。 管理員會建立 Azure Key Vault，然後建立磁碟加密集，並設定磁碟加密集。 該集合與 VM 相關聯，可讓磁碟使用 Azure AD 進行驗證](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


下列清單詳細說明圖表：

1. Azure Key Vault 管理員會建立金鑰保存庫資源。
1. 金鑰保存庫管理員會將其 RSA 金鑰匯入 Key Vault，或在 Key Vault 中產生新的 RSA 金鑰。
1. 管理員會建立磁碟加密集資源的執行個體、指定 Azure Key Vault ID 和金鑰 URL。 磁碟加密集是為了簡化受控磁碟的金鑰管理而導入的新資源。 
1. 磁碟加密集建立後，會在 Azure Active Directory (AD) 中建立[系統指派的受控識別](../../active-directory/managed-identities-azure-resources/overview.md)，並與磁碟加密集建立關聯。 
1. 然後，Azure Key Vault 管理員會授與受控識別權限，以在金鑰保存庫中執行作業。
1. VM 使用者可以藉由將磁碟與磁碟加密集建立關聯，以建立磁碟。 VM 使用者也可以藉由將客戶管理的金鑰與磁碟加密集建立關聯，針對現有資源使用客戶管理的金鑰，啟用伺服器端加密。 
1. 受控磁碟使用受控識別，將要求傳送至 Azure Key Vault。
1. 對於讀取或寫入資料，受控磁碟會將要求傳送至 Azure Key Vault，以加密 (包裝) 和解密 (解除包裝) 資料加密金鑰，以便執行資料的加密和解密。 

若要撤銷客戶管理的金鑰的存取權，請參閱 [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) 和 [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault)。 撤銷存取權會有效封鎖對儲存體帳戶中所有資料的存取，因為 Azure 儲存體無法存取加密金鑰。

### <a name="supported-regions"></a>支援區域

[!INCLUDE [virtual-machines-disks-encryption-regions](../../../includes/virtual-machines-disks-encryption-regions.md)]

### <a name="restrictions"></a>限制

目前，客戶管理的金鑰具有下列限制：

- 如果您的磁碟已啟用此功能，則無法停用。
    如果您需要解決此問題，必須[將所有資料複製到](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk)完全不同且未使用客戶管理的金鑰的受控磁碟。
- 僅支援大小為2080的[軟體和 HSM RSA 金鑰](../../key-vault/keys/about-keys.md)，沒有其他金鑰或大小。
- 從自訂映像 (使用伺服器端加密和客戶管理的金鑰加密) 建立的磁碟，必須使用相同的客戶管理的金鑰進行加密，而且必須在相同的訂用帳戶中。
- 從磁碟 (使用伺服器端加密和客戶管理的金鑰加密) 建立的快照集，必須使用相同的客戶管理金鑰進行加密。
- 與客戶管理金鑰相關的所有資源 (Azure Key Vault、磁碟加密集、VM、磁碟和快照集) 必須位於相同的訂用帳戶和區域中。
- 使用客戶管理的金鑰加密的磁碟、快照集和映像無法移至另一個訂用帳戶。
- 搭配使用伺服器端加密與客戶管理的金鑰加密的受控磁碟，也無法以 Azure 磁碟加密進行加密，反之亦然
- 如需將客戶管理的金鑰與共用映像資源庫搭配使用的詳細資訊，請參閱[預覽：使用客戶管理的金鑰加密映像](../image-version-encryption.md)。

### <a name="powershell"></a>PowerShell

#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>設定您的 Azure Key Vault 與 DiskEncryptionSet

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-powershell](../../../includes/virtual-machines-disks-encryption-create-key-vault-powershell.md)]

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>使用 Marketplace 映像建立 VM，使用客戶管理的金鑰以加密作業系統和資料磁碟

```powershell
$VMLocalAdminUser = "yourVMLocalAdminUserName"
$VMLocalAdminSecurePassword = ConvertTo-SecureString <password> -AsPlainText -Force
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$ComputerName = "yourComputerName"
$VMName = "yourVMName"
$VMSize = "Standard_DS3_v2"
$diskEncryptionSetName="yourdiskEncryptionSetName"
    
$NetworkName = "yourNetworkName"
$NICName = "yourNICName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix
$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet
$NIC = New-AzNetworkInterface -Name $NICName -ResourceGroupName $ResourceGroupName -Location $LocationName -SubnetId $Vnet.Subnets[0].Id
    
$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);
    
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name $($VMName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage

$VirtualMachine = Add-AzVMDataDisk -VM $VirtualMachine -Name $($VMName +"DataDisk1") -DiskSizeInGB 128 -StorageAccountType Premium_LRS -CreateOption Empty -Lun 0 -DiskEncryptionSetId $diskEncryptionSet.Id 
    
New-AzVM -ResourceGroupName $ResourceGroupName -Location $LocationName -VM $VirtualMachine -Verbose
```

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>將伺服器端加密與客戶管理的金鑰搭配使用，以建立空磁碟，並將其連結至 VM

```PowerShell
$vmName = "yourVMName"
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskSKU = "Premium_LRS"
$diskSizeinGiB = 30
$diskLUN = 1
$diskEncryptionSetName="yourDiskEncryptionSetName"


$vm = Get-AzVM -Name $vmName -ResourceGroupName $ResourceGroupName 

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Empty -DiskSizeInGB $diskSizeinGiB -StorageAccountType $diskSKU -Lun $diskLUN -DiskEncryptionSetId $diskEncryptionSet.Id 

Update-AzVM -ResourceGroupName $ResourceGroupName -VM $vm

```

#### <a name="encrypt-existing-managed-disks"></a>加密現有的受控磁碟 

您的現有磁碟不能連結至執行中的 VM，您可以使用下列指令碼，以加密這些磁碟：

```PowerShell
$rgName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskEncryptionSetName = "yourDiskEncryptionSetName"
 
$diskEncryptionSet = Get-AzDiskEncryptionSet -ResourceGroupName $rgName -Name $diskEncryptionSetName
 
New-AzDiskUpdateConfig -EncryptionType "EncryptionAtRestWithCustomerKey" -DiskEncryptionSetId $diskEncryptionSet.Id | Update-AzDisk -ResourceGroupName $rgName -DiskName $diskName
```

#### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>使用 Marketplace 映像建立虛擬機器擴展集，使用客戶管理的金鑰以加密作業系統和資料磁碟

```PowerShell
$VMLocalAdminUser = "yourLocalAdminUser"
$VMLocalAdminSecurePassword = ConvertTo-SecureString Password@123 -AsPlainText -Force
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$ComputerNamePrefix = "yourComputerNamePrefix"
$VMScaleSetName = "yourVMSSName"
$VMSize = "Standard_DS3_v2"
$diskEncryptionSetName="yourDiskEncryptionSetName"
    
$NetworkName = "yourVNETName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix

$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet

$ipConfig = New-AzVmssIpConfig -Name "myIPConfig" -SubnetId $Vnet.Subnets[0].Id 

$VMSS = New-AzVmssConfig -Location $LocationName -SkuCapacity 2 -SkuName $VMSize -UpgradePolicyMode 'Automatic'

$VMSS = Add-AzVmssNetworkInterfaceConfiguration -Name "myVMSSNetworkConfig" -VirtualMachineScaleSet $VMSS -Primary $true -IpConfiguration $ipConfig

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

# Enable encryption at rest with customer managed keys for OS disk by setting DiskEncryptionSetId property 

$VMSS = Set-AzVmssStorageProfile $VMSS -OsDiskCreateOption "FromImage" -DiskEncryptionSetId $diskEncryptionSet.Id -ImageReferenceOffer 'WindowsServer' -ImageReferenceSku '2012-R2-Datacenter' -ImageReferenceVersion latest -ImageReferencePublisher 'MicrosoftWindowsServer'

$VMSS = Set-AzVmssOsProfile $VMSS -ComputerNamePrefix $ComputerNamePrefix -AdminUsername $VMLocalAdminUser -AdminPassword $VMLocalAdminSecurePassword

# Add a data disk encrypted at rest with customer managed keys by setting DiskEncryptionSetId property 

$VMSS = Add-AzVmssDataDisk -VirtualMachineScaleSet $VMSS -CreateOption Empty -Lun 1 -DiskSizeGB 128 -StorageAccountType Premium_LRS -DiskEncryptionSetId $diskEncryptionSet.Id

$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);

New-AzVmss -VirtualMachineScaleSet $VMSS -ResourceGroupName $ResourceGroupName -VMScaleSetName $VMScaleSetName
```

#### <a name="change-the-key-of-a-diskencryptionset-to-rotate-the-key-for-all-the-resources-referencing-the-diskencryptionset"></a>變更 DiskEncryptionSet 的金鑰，以輪替參考 DiskEncryptionSet 的所有資源的金鑰

```PowerShell
$ResourceGroupName="yourResourceGroupName"
$keyVaultName="yourKeyVaultName"
$keyName="yourKeyName"
$diskEncryptionSetName="yourDiskEncryptionSetName"

$keyVault = Get-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $ResourceGroupName

$keyVaultKey = Get-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName

Update-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -SourceVaultId $keyVault.ResourceId -KeyUrl $keyVaultKey.Id
```

#### <a name="find-the-status-of-server-side-encryption-of-a-disk"></a>尋找磁碟的伺服器端加密狀態

[!INCLUDE [virtual-machines-disks-encryption-status-powershell](../../../includes/virtual-machines-disks-encryption-status-powershell.md)]

> [!IMPORTANT]
> 客戶管理的金鑰須依賴 Azure 資源的受控識別 (Azure Active Directory (Azure AD) 的一項功能)。 當您設定客戶管理的金鑰時，受控識別會在幕後自動指派給您的資源。 如果您之後將訂用帳戶、資源群組或受控磁碟從一個 Azure AD 目錄移至另一個目錄，與受控磁碟相關聯的受控識別不會移轉至新的租用戶，因此，客戶管理的金鑰可能無法再運作。 如需詳細資訊，請參閱[在 Azure AD 目錄之間移轉訂用帳戶](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)。

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> 客戶管理的金鑰須依賴 Azure 資源的受控識別 (Azure Active Directory (Azure AD) 的一項功能)。 當您設定客戶管理的金鑰時，受控識別會在幕後自動指派給您的資源。 如果您之後將訂用帳戶、資源群組或受控磁碟從一個 Azure AD 目錄移至另一個目錄，與受控磁碟相關聯的受控識別不會移轉至新的租用戶，因此，客戶管理的金鑰可能無法再運作。 如需詳細資訊，請參閱[在 Azure AD 目錄之間移轉訂用帳戶](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)。

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>伺服器端加密與 Azure 磁碟加密

[Azure 磁碟加密](../../security/fundamentals/azure-disk-encryption-vms-vmss.md)會利用 Windows 的 [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) 功能，在來賓 VM 中使用客戶管理的金鑰來加密受控磁碟。  使用客戶管理的金鑰的伺服器端加密改善了 ADE 的效能，讓您可藉由加密儲存庫服務中的資料。為您的 VM 使用任何作業系統類型和映像。

## <a name="next-steps"></a>後續步驟

- [探索 Azure Resource Manager 範本，以使用客戶管理的金鑰來建立加密的磁碟](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [什麼是 Azure 金鑰保存庫？](../../key-vault/general/overview.md)
- [使用已啟用客戶管理的金鑰的磁碟來複寫機器](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [使用 PowerShell 設定 VMWare VM 至 Azure 的災害復原](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [使用 PowerShell 和 Azure Resource Manager，針對 Hyper-V VM 設定 Azure 的災害復原](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
