---
title: Windows VM 上的 Azure 磁碟加密案例
description: 本文提供有關為各種方案啟用適用于 Windows VM 的 Microsoft Azure 磁片加密的說明
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: ed64ee3d0e024c32be08ed4e010a6933033c3f87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476513"
---
# <a name="azure-disk-encryption-scenarios-on-windows-vms"></a>Windows VM 上的 Azure 磁碟加密案例

Azure 磁片加密使用 BitLocker 外部金鑰保護器為 Azure 虛擬機器 （VM） 的作業系統和資料磁片提供卷加密，並與 Azure 金鑰保存庫集成，以説明您控制和管理磁片加密金鑰和機密。 有關服務的概述，請參閱 Windows [VM 的 Azure 磁片加密](disk-encryption-overview.md)。

有許多磁片加密方案，步驟可能因方案而異。 以下各節更詳細地介紹 Windows VM 的方案。

您只能將磁片加密應用於[受支援的 VM 大小和作業系統](disk-encryption-overview.md#supported-vms-and-operating-systems)的虛擬機器。 您還必須滿足以下先決條件：

- [網路需求](disk-encryption-overview.md#networking-requirements)
- [群組原則要求](disk-encryption-overview.md#group-policy-requirements)
- [加密金鑰存儲要求](disk-encryption-overview.md#encryption-key-storage-requirements)

>[!IMPORTANT]
> - 如果以前使用 Azure 磁片加密與 Azure AD 一起加密 VM，則必須繼續使用此選項對 VM 進行加密。 有關詳細資訊[，請參閱 Azure 磁片加密與 Azure AD（上一版本）。](disk-encryption-overview-aad.md) 
>
> - 您應該在磁片加密之前[拍攝快照](snapshot-copy-managed-disk.md)和/或創建備份。 擁有備份可確保在加密期間發生任何非預期的失敗時，能有復原選項可供選擇。 具有受控磁碟的 VM 需要有備份，才能進行加密。 進行備份後，可以使用[Set-AzVMDisk 加密擴展 Cmdlet](/powershell/module/az.compute/set-azvmdiskencryptionextension)通過指定 -skipVmBackup 參數來加密託管磁片。 有關如何備份和還原加密 VM 的詳細資訊，請參閱[備份和恢復加密的 Azure VM。](../../backup/backup-azure-vms-encryption.md) 
>
> - 加密或禁用加密可能會導致 VM 重新開機。

## <a name="install-tools-and-connect-to-azure"></a>安裝工具並連接到 Azure

[!INCLUDE [disk-encryption-install-cli-powershell](../../../includes/disk-encryption-install-cli-powershell.md)]

## <a name="enable-encryption-on-an-existing-or-running-windows-vm"></a>在現有或正在運行的 Windows VM 上啟用加密
在這個案例中，您可以使用 Resource Manager 範本、PowerShell Cmdlet 或 CLI 命令啟用加密。 若您需要虛擬機器擴充功能之結構描述資訊，請參閱 [Windows 擴充功能的 Azure 磁碟加密](../extensions/azure-disk-enc-windows.md)一文。

## <a name="enable-encryption-on-existing-or-running-iaas-windows-vms"></a> 在現有或執行中的 IaaS Windows VM 上啟用加密
您可以使用範本、PowerShell Cmdlet 或 CLI 命令啟用加密。 若您需要虛擬機器擴充功能之結構描述資訊，請參閱 [Windows 擴充功能的 Azure 磁碟加密](../extensions/azure-disk-enc-windows.md)一文。

### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a>透過 Azure PowerShell 在現有或執行中的 VM 上啟用加密 
使用[Set-AzVM 磁片加密擴展](/powershell/module/az.compute/set-azvmdiskencryptionextension)Cmdlet 在 Azure 中正在運行的 IaaS 虛擬機器上啟用加密。 

-  **加密正在運行的 VM：** 下面的腳本初始化變數並運行 Set-AzVM 磁片加密擴展 Cmdlet。 資源群組、VM 和金鑰保存庫應該已經建立為必要條件。 將 MyKeyVault 資源組、我的虛擬電腦資源組、MySecureVM 和 MySecureVault 替換為您的值。

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
   > disk-encryption-keyvault 參數值的語法為完整識別碼字串：/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> key-encryption-key 參數值的語法為 KEK 的完整 URI：https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **驗證磁片是否加密：** 要檢查 IaaS VM 的加密狀態，請使用[獲取-AzVm 磁片加密狀態](/powershell/module/az.compute/get-azvmdiskencryptionstatus)Cmdlet。 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **禁用磁片加密：** 要禁用加密，請使用禁用[AzVM 磁片加密](/powershell/module/az.compute/disable-azvmdiskencryption)Cmdlet。 當作業系統和資料磁碟都已加密，在 Windows VM 上停用資料磁碟加密將無法按預期運作。 改為停用所有磁碟上的加密。

     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with-the-azure-cli"></a>使用 Azure CLI 對現有或正在運行的 VM 啟用加密
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
     > disk-encryption-keyvault 參數值的語法為完整識別碼字串：/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> key-encryption-key 參數值的語法為 KEK 的完整 URI：https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **驗證磁片是否加密：** 要檢查 IaaS VM 的加密狀態，請使用[az vm 加密顯示](/cli/azure/vm/encryption#az-vm-encryption-show)命令。 

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
|  keyEncryptionKeyURL | 金鑰加密金鑰的&lt;URL，格式HTTPs://金鑰庫名稱&gt;.vault.azure.net/key/&lt;鍵名稱&gt;。 如果不希望使用 KEK，請將此欄位留空。 |
| volumeType | 執行加密作業所在磁碟區的類型。 有效值為 _OS_、_Data_ 和 _All_。 
| forceUpdateTag | 每次需要強制執行作業時傳入唯一的值，例如 GUID。 |
| resizeOSDisk | 是否應該先將 OS 分割區調整大小以佔用完整的 OS VHD，然後才分割系統磁碟區。 |
| location | 所有資源的位置。 |


## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>透過客戶加密的 VHD 和加密金鑰建立的新 IaaS VM

在這種情況下，您可以使用 PowerShell Cmdlet 或 CLI 命令從預加密 VHD 和關聯的加密金鑰創建新 VM。 

使用["準備預加密的 Windows VHD"](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd)中的說明。 映像建立之後，您可以使用下一節的步驟來建立加密的 Azure VM。


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a> 透過 Azure PowerShell 加密包含預先加密 VHD 的 VM
您可以使用 PowerShell Cmdlet[集-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples)在加密的 VHD 上啟用磁片加密。 下列範例會提供一些常見的參數。 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>在新增的資料磁碟上啟用加密
您可以使用 PowerShell 或[透過 Azure 入口網站](attach-managed-disk-portal.md)，[將新的磁碟新增至 Windows VM](attach-disk-ps.md)。 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>透過 Azure PowerShell 在新增的磁碟上啟用加密
 使用 Powershell 來加密 Windows VM 的新磁碟時，應指定新的序列版本。 序列版本必須是唯一的。 下列指令碼會產生序列版本的 GUID。 在某些情況下，Azure 磁碟加密擴充功能可能會自動加密新增的資料磁碟。 當 VM 在新磁碟上線後重新開機時，通常會發生自動加密。 原因通常是磁碟加密之前在 VM 上執行時，將磁碟區類型指定為 "All"。 如果新添加的資料磁片上發生自動加密，我們建議使用新的序列版本再次運行 Set-AzVmDisk 加密擴展 Cmdlet。 如果新的資料磁碟自動加密，但您不想要加密，請先將所有磁碟機解密，再以磁碟區類型指定為 OS 的新序列版本重新加密。 
  
 

-  **加密正在運行的 VM：** 下面的腳本初始化變數並運行 Set-AzVM 磁片加密擴展 Cmdlet。 資源群組、VM 和金鑰保存庫應該已經建立為必要條件。 將 MyKeyVault 資源組、我的虛擬電腦資源組、MySecureVM 和 MySecureVault 替換為您的值。 此範例會對 -VolumeType 參數使用 "All"，其同時包含 OS 和資料磁碟區。 如果您只想要加密 OS 磁碟區，則請對 -VolumeType 參數使用 "OS"。 

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
    > disk-encryption-keyvault 參數值的語法為完整識別碼字串：/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> key-encryption-key 參數值的語法為 KEK 的完整 URI：https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>透過 Azure CLI 在新增的磁碟上啟用加密
 當您執行命令來啟用加密時，Azure CLI 命令會自動為您提供新的序列版本。 此範例會對 volume-type 參數使用 "All"。 如果您只要加密 OS 磁碟，則可能需要將 volume-type 參數變更為 OS。 在啟用加密時，與 Powershell 語法不同，CLI 不需要使用者提供唯一的序列版本。 CLI 為自動產生並使用其唯一序列版本的值。   

-  **加密執行中的 VM：**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **使用 KEK 加密執行中的 VM：**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```


## <a name="disable-encryption"></a>停用加密
您可以使用 Azure PowerShell、Azure CLI 或 Resource Manager 範本來停用加密。 當作業系統和資料磁碟都已加密，在 Windows VM 上停用資料磁碟加密將無法按預期運作。 改為停用所有磁碟上的加密。

- **使用 Azure PowerShell 禁用磁片加密：** 要禁用加密，請使用禁用[AzVM 磁片加密](/powershell/module/az.compute/disable-azvmdiskencryption)Cmdlet。 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **透過 Azure CLI 停用加密：** 若要停用加密，請使用 [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) 命令。 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **透過 Resource Manager 範本停用加密：** 

    1. 從[在執行中的 Windows VM 上停用磁碟加密](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad)範本，按一下 [部署至 Azure]****。
    2. 選取訂用帳戶、資源群組、位置、VM、磁碟區類型、法律條款及合約。
    3.  按一下 [購買]**** 以在執行中的 Windows VM 上停用磁碟加密。 

## <a name="unsupported-scenarios"></a>不支援的情節

Azure 磁片加密不適用於以下方案、功能和技術：

- 加密通過經典 VM 創建方法創建的基本層 VM 或 VM。
- 加密配置了基於軟體的 RAID 系統的 VM。
- 加密配置的 VM，這些 VM 與存儲空間直接 （S2D） 或 Windows 伺服器版本在 2016 年之前配置了 Windows 存儲空間。
- 與本地金鑰管理系統集成。
- Azure 檔案 (共用檔案系統)。
- 網路檔案系統 (NFS)。
- 動態磁碟區。
- Windows Server 容器，為每個容器創建動態磁碟區。
- 臨時作業系統磁片。
- 共用/分散式檔案系統（包括但不限於）DFS、GFS、DRDB 和 CephFS 的加密。
- 將加密 VM 移動到其他訂閱。

## <a name="next-steps"></a>後續步驟

- [Azure 磁碟加密概觀](disk-encryption-overview.md)
- [Azure 磁碟加密範例指令碼](disk-encryption-sample-scripts.md)
- [Azure 磁碟加密的疑難排解](disk-encryption-troubleshooting.md)
