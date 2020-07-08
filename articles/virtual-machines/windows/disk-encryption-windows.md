---
title: Windows VM 上的 Azure 磁碟加密案例
description: 本文提供針對各種案例啟用 Windows Vm Microsoft Azure 磁片加密的指示
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 81ac76ef5eeebd278dc10e03d661bb21469c8f4f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610555"
---
# <a name="azure-disk-encryption-scenarios-on-windows-vms"></a>Windows VM 上的 Azure 磁碟加密案例

Windows 虛擬機器（Vm）的 Azure 磁碟加密會使用 Windows 的 BitLocker 功能來提供作業系統磁片和資料磁片的完整磁片加密。 此外，當 VolumeType 參數為 All 時，也會提供暫存磁碟的加密。

Azure 磁碟加密會與 [Azure Key Vault](disk-encryption-key-vault.md) 整合，協助您控制及管理磁碟加密金鑰與祕密。 如需服務的總覽，請參閱[適用于 Windows vm 的 Azure 磁碟加密](disk-encryption-overview.md)。

您只能將磁碟加密套用到[受支援 VM 大小和作業系統](disk-encryption-overview.md#supported-vms-and-operating-systems)的虛擬機器。 您也必須符合下列必要條件：

- [網路需求](disk-encryption-overview.md#networking-requirements)
- [群組原則需求](disk-encryption-overview.md#group-policy-requirements)
- [加密金鑰儲存體需求](disk-encryption-overview.md#encryption-key-storage-requirements)

>[!IMPORTANT]
> - 如果您先前曾使用 Azure 磁碟加密搭配 Azure AD 來加密 VM，則必須繼續使用此選項來加密您的 VM。 如需詳細資料，請參閱[將 Azure 磁碟加密與 Azure AD 搭配使用 (舊版)](disk-encryption-overview-aad.md)。 
>
> - 在磁片加密之前，您應該先建立[快照](snapshot-copy-managed-disk.md)集和/或建立備份。 擁有備份可確保在加密期間發生任何非預期的失敗時，能有復原選項可供選擇。 具有受控磁碟的 VM 需要有備份，才能進行加密。 在建立備份後，您可以使用 [Set-AzVMDiskEncryptionExtension Cmdlet](/powershell/module/az.compute/set-azvmdiskencryptionextension) 並指定 -skipVmBackup 參數來加密受控磁碟。 如需有關如何備份和還原已加密 Vm 的詳細資訊，請參閱[備份及還原已加密的 AZURE VM](../../backup/backup-azure-vms-encryption.md)。 
>
> - 加密或停用加密可能會導致 VM 重新開機。

## <a name="install-tools-and-connect-to-azure"></a>安裝工具並連線至 Azure

[!INCLUDE [disk-encryption-install-cli-powershell](../../../includes/disk-encryption-install-cli-powershell.md)]

## <a name="enable-encryption-on-an-existing-or-running-windows-vm"></a>在現有或執行中的 Windows VM 上啟用加密
在這個案例中，您可以使用 Resource Manager 範本、PowerShell Cmdlet 或 CLI 命令啟用加密。 若您需要虛擬機器擴充功能之結構描述資訊，請參閱 [Windows 擴充功能的 Azure 磁碟加密](../extensions/azure-disk-enc-windows.md)一文。

### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a>透過 Azure PowerShell 在現有或執行中的 VM 上啟用加密 
使用[AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) Cmdlet，在 Azure 中執行中的 IaaS 虛擬機器上啟用加密。 

-  **加密執行中的 VM：** 下面的指令碼會初始化您的變數並執行 Set-AzVMDiskEncryptionExtension Cmdlet。 資源群組、VM 和金鑰保存庫應該已經建立為必要條件。 以您的值取代 MyKeyVaultResourceGroup、MyVirtualMachineResourceGroup、MySecureVM 和 MySecureVault。

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **使用 KEK 加密執行中的 VM：** 

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > disk-encryption-keyvault 參數值的語法為完整識別碼字串：/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> key-encryption-key 參數值的語法為 KEK 的完整 URI： https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **確認磁片已加密：** 若要檢查 IaaS VM 的加密狀態，請使用[AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) Cmdlet。 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **停用磁碟加密**：若要停用加密，請使用 [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) Cmdlet。 當作業系統和資料磁碟都已加密，在 Windows VM 上停用資料磁碟加密將無法按預期運作。 改為停用所有磁碟上的加密。

     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with-the-azure-cli"></a>使用 Azure CLI 在現有或執行中的 Vm 上啟用加密
在 Azure 中使用 [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) 命令以在執行中的 IaaS 虛擬機器上啟用加密。

- **加密執行中的 VM：**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **使用 KEK 加密執行中的 VM：**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > disk-encryption-keyvault 參數值的語法為完整識別碼字串：/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> key-encryption-key 參數值的語法為 KEK 的完整 URI： https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **確認磁片已加密：** 若要檢查 IaaS VM 的加密狀態，請使用[az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show)命令。 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **停用加密：** 若要停用加密，請使用 [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) 命令。 當作業系統和資料磁碟都已加密，在 Windows VM 上停用資料磁碟加密將無法按預期運作。 改為停用所有磁碟上的加密。

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```

### <a name="using-the-resource-manager-template"></a>使用 Resource Manager 範本

您可以使用 [Resource Manager 範本來加密執行中的 Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)，以在 Azure 中現有或執行中的 IaaS Windows VM 上啟用磁碟加密。


1. 在 Azure 快速入門範本中，按一下 [部署至 Azure]****。

2. 選取訂用帳戶、資源群組、位置、設定、法律條款及合約。 按一下 [購買]****，以在現有或執行中的 IaaS VM 上啟用加密。

下表列出現有或執行中 VM 的 Resource Manager 範本參數︰

| 參數 | 描述 |
| --- | --- |
| vmName | 要執行加密作業的 VM 名稱。 |
| keyVaultName | 應上傳 BitLocker 金鑰的金鑰保存庫名稱。 您可以使用 Cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` 或 Azure CLI 命令 `az keyvault list --resource-group "MyKeyVaultResourceGroup"` 來取得|
| keyVaultResourceGroup | 包含金鑰保存庫的資源群組名稱|
|  keyEncryptionKeyURL | 金鑰加密金鑰的 URL，格式為 HTTPs:// &lt; keyvault-name &gt; . vault.azure.net/key/ &lt; key-name &gt; 。 如果您不想使用 KEK，請將此欄位保留空白。 |
| volumeType | 執行加密作業所在磁碟區的類型。 有效值為 _OS_、_Data_ 和 _All_。 
| forceUpdateTag | 每次需要強制執行作業時傳入唯一的值，例如 GUID。 |
| resizeOSDisk | 是否應該先將 OS 分割區調整大小以佔用完整的 OS VHD，然後才分割系統磁碟區。 |
| location | 所有資源的位置。 |


## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>透過客戶加密的 VHD 和加密金鑰建立的新 IaaS VM

在此案例中，您可以使用 PowerShell Cmdlet 或 CLI 命令，從預先加密的 VHD 和相關聯的加密金鑰建立新的 VM。 

使用[準備已預先加密的 WINDOWS VHD](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd)中的指示。 映像建立之後，您可以使用下一節的步驟來建立加密的 Azure VM。


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a> 透過 Azure PowerShell 加密包含預先加密 VHD 的 VM
您可以使用 PowerShell Cmdlet [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples) 在加密的 VHD 上啟用磁碟加密。 下列範例會提供一些常見的參數。 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>在新增的資料磁碟上啟用加密
您可以使用 PowerShell 或[透過 Azure 入口網站](attach-managed-disk-portal.md)，[將新的磁碟新增至 Windows VM](attach-disk-ps.md)。 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>透過 Azure PowerShell 在新增的磁碟上啟用加密
 使用 PowerShell 為 Windows Vm 加密新磁片時，應指定新的序列版本。 序列版本必須是唯一的。 下列指令碼會產生序列版本的 GUID。 在某些情況下，Azure 磁碟加密擴充功能可能會自動加密新增的資料磁碟。 當 VM 在新磁碟上線後重新開機時，通常會發生自動加密。 原因通常是磁碟加密之前在 VM 上執行時，將磁碟區類型指定為 "All"。 如果在新增的資料磁片上發生自動加密，建議您再次使用新的順序版本來執行 AzVmDiskEncryptionExtension 指令程式。 如果新的資料磁碟自動加密，但您不想要加密，請先將所有磁碟機解密，再以磁碟區類型指定為 OS 的新序列版本重新加密。 
  
 

-  **加密執行中的 VM：** 下面的指令碼會初始化您的變數並執行 Set-AzVMDiskEncryptionExtension Cmdlet。 資源群組、VM 和金鑰保存庫應該已經建立為必要條件。 以您的值取代 MyKeyVaultResourceGroup、MyVirtualMachineResourceGroup、MySecureVM 和 MySecureVault。 此範例會對 -VolumeType 參數使用 "All"，其同時包含 OS 和資料磁碟區。 如果您只想要加密 OS 磁碟區，則請對 -VolumeType 參數使用 "OS"。 

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;
    ```
- **使用 KEK 加密執行中的 VM：** 此範例會對 -VolumeType 參數使用 "All"，其同時包含 OS 和資料磁碟區。 如果您只想要加密 OS 磁碟區，則請對 -VolumeType 參數使用 "OS"。

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     $sequenceVersion = [Guid]::NewGuid();

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > disk-encryption-keyvault 參數值的語法為完整識別碼字串：/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> key-encryption-key 參數值的語法為 KEK 的完整 URI： https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>透過 Azure CLI 在新增的磁碟上啟用加密
 當您執行命令來啟用加密時，Azure CLI 命令會自動為您提供新的序列版本。 此範例會對 volume-type 參數使用 "All"。 如果您只要加密 OS 磁碟，則可能需要將 volume-type 參數變更為 OS。 在啟用加密時，與 PowerShell 語法不同，CLI 不需要使用者提供唯一的序列版本。 CLI 為自動產生並使用其唯一序列版本的值。   

-  **加密執行中的 VM：**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **使用 KEK 加密執行中的 VM：**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```


## <a name="disable-encryption"></a>停用加密
[!INCLUDE [disk-encryption-disable-encryption-powershell](../../../includes/disk-encryption-disable-powershell.md)]

## <a name="unsupported-scenarios"></a>不支援的情節

Azure 磁碟加密不適用於下列案例、功能和技術：

- 將基本層 VM 或透過傳統 VM 建立方法所建立的 VM 加密。
- 加密以軟體為基礎的 RAID 系統設定的 Vm。
- 在設定 Windows 儲存空間的2016之前，使用儲存空間直接存取（S2D）或 Windows Server 版本設定的 Vm 加密。
- 與內部部署金鑰管理系統整合。
- Azure 檔案 (共用檔案系統)。
- 網路檔案系統 (NFS)。
- 動態磁碟區。
- Windows Server 容器，為每個容器建立動態磁碟區。
- 暫時性 OS 磁碟。
- 共用/分散式檔案系統的加密，例如（但不限於） DFS、GFS、DRDB 和 CephFS。
- 將已加密的 Vm 移到另一個訂用帳戶或區域。
- 建立已加密 VM 的映射或快照集，並使用它來部署額外的 Vm。
- Gen2 Vm （請參閱：[在 Azure 上支援第2代 vm](generation-2.md#generation-1-vs-generation-2-capabilities)）
- Lsv2 系列 Vm （請參閱： [Lsv2 系列](../lsv2-series.md)）
- 具有寫入加速器磁片的 M 系列 Vm。

## <a name="next-steps"></a>後續步驟

- [Azure 磁碟加密概觀](disk-encryption-overview.md)
- [Azure 磁碟加密範例指令碼](disk-encryption-sample-scripts.md)
- [Azure 磁碟加密的疑難排解](disk-encryption-troubleshooting.md)
