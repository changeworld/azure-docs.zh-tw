---
title: 以 Azure AD App Linux IaaS VM (舊版) 進行 Azure 磁碟加密
description: 本文提供啟用 Linux IaaS VM 適用的 Microsoft Azure 磁碟加密的指示。
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: ee365d37a957350fa8a68da0f34149d3210d6238
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "78970615"
---
# <a name="enable-azure-disk-encryption-with-azure-ad-on-linux-vms-previous-release"></a>在 Linux Vm 上使用 Azure AD 啟用 Azure 磁碟加密（舊版）

新版本的 Azure 磁碟加密不需要提供 Azure Active Directory （Azure AD）應用程式參數，就能啟用 VM 磁片加密。 若使用新版本，您就不再需要在啟用加密步驟期間提供 Azure AD 認證。 所有新的 Vm 都必須使用新版本，在沒有 Azure AD 應用程式參數的情況下進行加密。 如需如何使用新版本來啟用 VM 磁片加密的指示，請參閱[LINUX vm 的 Azure 磁碟加密](disk-encryption-linux.md)。 已經使用 Azure AD 應用程式參數進行加密的 VM 仍然受支援，應該繼續使用 AAD 語法進行維護。

您可以啟用許多磁片加密案例，而步驟可能會根據案例而有所不同。 下列各節涵蓋 Linux 基礎結構即服務（IaaS） Vm 的更詳細案例。 您只能將磁片加密套用至[支援的 VM 大小和作業系統](disk-encryption-overview.md#supported-vms-and-operating-systems)的虛擬機器。 您也必須符合下列必要條件：

- [Vm 的其他需求](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [網路和群組原則](disk-encryption-overview-aad.md#networking-and-group-policy)
- [加密金鑰儲存需求](disk-encryption-overview-aad.md#encryption-key-storage-requirements)

在加密磁片之前，請建立[快照](snapshot-copy-managed-disk.md)集、進行備份或兩者。 擁有備份可確保在加密期間發生任何非預期的失敗時，能有復原選項可供選擇。 具有受控磁碟的 VM 需要有備份，才能進行加密。 進行備份之後，您可以藉由指定-skipVmBackup 參數，使用 AzVMDiskEncryptionExtension Cmdlet 來加密受控磁片。 如需有關如何備份和還原已加密 Vm 的詳細資訊，請參閱[Azure 備份](../../backup/backup-azure-vms-encryption.md)。 

>[!WARNING]
 > - 如果您先前使用[Azure 磁碟加密搭配 Azure AD 應用程式](disk-encryption-overview-aad.md)來加密此 vm，則必須繼續使用此選項來加密您的 vm。 因為這不是支援的案例，所以您無法在此加密的 VM 上使用[Azure 磁碟加密](disk-encryption-overview.md)，這表示不支援針對此加密的 vm 切換離開 Azure AD 應用程式。
 > - 為確保加密秘密不會跨越區域界限，Azure 磁碟加密需要將金鑰保存庫和 Vm 共置於相同區域中。 建立並使用與要加密的 VM 位於相同區域中的金鑰保存庫。
 > - 當您加密 Linux OS 磁片區時，此程式可能需要幾個小時的時間。 Linux OS 磁片區通常需要比資料磁片區加密更長的時間。
> - 當您加密 Linux OS 磁片區時，VM 應視為無法使用。 強烈建議您在加密進行時避免 SSH 登入，以避免在加密程式期間封鎖任何需要存取的開啟檔案。 若要檢查進度，請使用[AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus)或[vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show)命令。 您可以預期此程式需要幾個小時的時間來處理 30 GB 的 OS 磁片區，再加上額外的時間來加密資料磁片區。 資料磁片區加密時間與資料磁片區的大小和數量成正比，除非使用 [**加密格式**] 選項。 
 > - 只有資料磁碟區支援在 Linux VM 上停用加密。 如果 OS 磁片區已加密，則不支援在資料或 OS 磁片區上。 

 

## <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm"></a><a name="bkmk_RunningLinux"> </a> 在現有或執行中的 IaaS Linux VM 上啟用加密

在此案例中，您可以使用 Azure Resource Manager 範本、PowerShell Cmdlet 或 Azure CLI 命令來啟用加密。 

>[!IMPORTANT]
 >在啟用 Azure 磁碟加密之前，您必須先建立快照集，或將受控磁片型 VM 實例備份到以外的地方。 您可以從 Azure 入口網站建立受控磁片的快照集，也可以使用[Azure 備份](../../backup/backup-azure-vms-encryption.md)。 擁有備份可確保在加密期間發生任何非預期的失敗時，能有復原選項可供選擇。 進行備份之後，請透過指定-skipVmBackup 參數，使用 AzVMDiskEncryptionExtension Cmdlet 來加密受控磁片。 對受控磁片型 Vm 的 AzVMDiskEncryptionExtension 命令會失敗，直到進行備份並指定此參數為止。 
>
>加密或停用加密可能會導致 VM 重新開機。 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-the-azure-cli"></a><a name="bkmk_RunningLinuxCLI"> </a>使用 Azure CLI 在現有或執行中的 Linux VM 上啟用加密 
您可以藉由安裝和使用[Azure CLI 2.0](/cli/azure)命令列工具，在加密的 VHD 上啟用磁片加密。 您可以在瀏覽器中將它與 [Azure Cloud Shell](../../cloud-shell/overview.md) 搭配使用，或可將它安裝在本機電腦上，並在任何 PowerShell 工作階段中使用它。 若要在 Azure 中現有或執行中的 IaaS Linux VM 上啟用加密，請使用下列 CLI 命令：

在 Azure 中使用 [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) 命令以在執行中的 IaaS 虛擬機器上啟用加密。

-  **使用用戶端秘密來加密執行中的 VM：**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **使用 KEK 來加密執行中的 VM 以包裝用戶端秘密：**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

   >[!NOTE]
   > 磁片加密-keyvault 參數值的語法是完整的識別碼字串：/subscriptions/[訂用帳戶識別碼-guid]/resourceGroups/[資源群組名稱]/providers/Microsoft.KeyVault/vaults/[keyvault-name]。</br> </br> 金鑰加密金鑰參數值的語法是 KEK 的完整 URI，如下所示 .. HTTPs：//[keyvault-name]. t a t/key/[kekname]/[KEK-unique-id]。

- **確認磁片已加密：** 若要檢查 IaaS VM 的加密狀態，請使用[az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show)命令。 

     ```azurecli-interactive
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **停用加密：** 若要停用加密，請使用 [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) 命令。 只能在 Linux VM 的資料磁碟區上停用加密。
    
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-powershell"></a><a name="bkmk_RunningLinuxPSH"> </a>使用 PowerShell 在現有或執行中的 Linux VM 上啟用加密
使用[AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) Cmdlet，在 Azure 中執行中的 IaaS 虛擬機器上啟用加密。 建立[快照](snapshot-copy-managed-disk.md)集，或在磁片加密之前，使用[Azure 備份](../../backup/backup-azure-vms-encryption.md)進行 VM 的備份。 在 PowerShell 腳本中，已指定-skipVmBackup 參數來加密執行中的 Linux VM。

- **使用用戶端秘密來加密執行中的 VM：** 下列腳本會初始化您的變數，並執行 AzVMDiskEncryptionExtension 指令程式。 資源群組、VM、金鑰保存庫、Azure AD 應用程式和用戶端秘密應該已經建立為必要條件。 以您的值取代 MyVirtualMachineResourceGroup、MyKeyVaultResourceGroup、MySecureVM、MySecureVault、我的 AAD 用戶端識別碼和我的 AAD 用戶端密碼。 修改-VolumeType 參數，以指定您要加密的磁片。

     ```azurepowershell
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $KVRGname = 'MyKeyVaultResourceGroup';
         $vmName = 'MySecureVM';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```
- **使用 KEK 來加密執行中的 VM 以包裝用戶端秘密：** Azure 磁碟加密可讓您指定金鑰保存庫中的現有金鑰，以包裝在啟用加密時所產生的磁片加密秘密。 當指定金鑰加密金鑰時，Azure 磁碟加密會在寫入金鑰保存庫之前，使用該金鑰來包裝加密秘密。 修改-VolumeType 參數，以指定您要加密的磁片。 

     ```azurepowershell
         $KVRGname = 'MyKeyVaultResourceGroup';
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $keyEncryptionKeyName = 'MyKeyEncryptionKey';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

  >[!NOTE]
  > 磁片加密-keyvault 參數值的語法是完整識別碼字串：/subscriptions/[訂用帳戶識別碼-guid]/resourceGroups/[KVresource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]。</br> </br>
  金鑰加密金鑰參數值的語法是 KEK 的完整 URI，如下所示 .. HTTPs：//[keyvault-name]. t a t/key/[kekname]/[KEK-unique-id]。 
    
- **確認磁片已加密：** 若要檢查 IaaS VM 的加密狀態，請使用[AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) Cmdlet。 
    
     ```azurepowershell-interactive 
         Get-AzVmDiskEncryptionStatus -ResourceGroupName MyVirtualMachineResourceGroup -VMName MySecureVM
     ```
    
- **停用磁碟加密：** 若要停用加密，請使用 [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet。 只能在 Linux VM 的資料磁碟區上停用加密。
     
     ```azurepowershell-interactive 
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-with-a-template"></a><a name="bkmk_RunningLinux"> </a>透過範本在現有或執行中的 IaaS Linux VM 上啟用加密

您可以使用 [Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)，在 Azure 中現有或執行中的 IaaS Linux VM 上啟用磁碟加密。

1. 選取 Azure 快速入門範本上的 [**部署至 azure** ]。

2. 選取訂用帳戶、資源群組、資源群組位置、參數、法律條款及合約。 選取 [**建立**] 以在現有或執行中的 IaaS VM 上啟用加密。

針對使用 Azure AD 用戶端識別碼的現有或執行中 VM，以下資料表列出其 Resource Manager 範本參數︰

| 參數 | 說明 |
| --- | --- |
| AADClientID | 具有權限可將密碼寫入金鑰保存庫之 Azure AD 應用程式的用戶端識別碼。 |
| AADClientSecret | 具有權限可將密碼寫入金鑰保存庫之 Azure AD 應用程式的用戶端密碼。 |
| keyVaultName | 應上傳金鑰的金鑰保存庫名稱。 您可以使用 Azure CLI 命令 `az keyvault show --name "MySecureVault" --query KVresourceGroup` 來取得它。 |
|  keyEncryptionKeyURL | 用來加密所產生金鑰的金鑰加密金鑰 URL。 如果您在 [ **UseExistingKek** ] 下拉式清單中選取 [ **nokek** ]，則此參數是選擇性的。 如果您在 [ **UseExistingKek** ] 下拉式清單中選取 [ **kek** ]，則必須輸入_keyEncryptionKeyURL_值。 |
| volumeType | 執行加密作業所在磁碟區的類型。 支援的有效值為_OS_或_All_。 （請參閱稍早的必要條件一節中的作業系統和資料磁片支援的 Linux 發行版本及其版本。） |
| sequenceVersion | BitLocker 作業的順序版本。 每當在相同的 VM 上執行磁碟加密作業時便遞增此版本號碼。 |
| vmName | 要執行加密作業所在 VM 的名稱。 |
| 複雜密碼 | 輸入強式複雜密碼做為資料加密金鑰。 |



## <a name="use-the-encryptformatall-feature-for-data-disks-on-linux-iaas-vms"></a><a name="bkmk_EFA"> </a>針對 Linux IaaS vm 上的資料磁片使用 EncryptFormatAll 功能
EncryptFormatAll 參數會減少加密 Linux 資料磁碟的時間。 符合特定準則的分割區會格式化（使用其目前的檔案系統）。 然後，它們會重新掛接回到執行命令之前的位置。 如果您想要排除符合準則的資料磁片，您可以在執行命令之前，先將它卸載。

 執行此命令之後，任何先前掛接的磁片磁碟機都會格式化。 然後，加密層會在現在的空磁片磁碟機上啟動。 選取此選項時，也會加密附加至 VM 的暫時資源磁片。 如果暫時磁片磁碟機已重設，則 Azure 磁碟加密解決方案會在下一次的機會重新格式化虛擬機器，並為 VM 重新加密。

>[!WARNING]
> 當 VM 的資料磁片區上需要資料時，不應使用 EncryptFormatAll。 您可以將磁片取消掛接，將它們從加密中排除。 請先在測試 VM 上試用 EncryptFormatAll 參數，以瞭解功能參數及其含意，然後再于生產 VM 上進行試用。 EncryptFormatAll 選項會格式化資料磁片，因此將會遺失其上的所有資料。 在繼續之前，請確認您想要排除的任何磁片都已正確卸載。 </br></br>
 >如果您在更新加密設定時設定此參數，可能會導致在實際加密之前重新開機。 在此情況下，您也會想要從 fstab 檔案中移除您不想要格式化的磁片。 同樣地，您應該在起始加密作業之前，先將您想要加密格式的資料分割新增至 fstab 檔案。 

### <a name="encryptformatall-criteria"></a><a name="bkmk_EFACriteria"> </a> EncryptFormatAll 準則
參數會通過所有分割區，只要符合下列*所有*條件，就會將它們加密： 
- 不是根/OS/開機磁碟分割
- 尚未加密
- 不是 BEK 磁碟區
- 不是 RAID 磁碟區
- 不是 LVM 磁碟區
- 已掛接

對組成 RAID 或 LVM 磁碟區的磁碟進行加密，而非對 RAID 或 LVM 磁碟區本身進行加密。

### <a name="use-the-encryptformatall-parameter-with-a-template"></a><a name="bkmk_EFATemplate"> </a> 使用 EncryptFormatAll 參數搭配範本
若要使用 EncryptFormatAll 選項，請使用任何可加密 Linux VM 的預先存在 Azure Resource Manager 範本，並變更 AzureDiskEncryption 資源的 [ **EncryptionOperation** ] 欄位。

1. 例如，使用 [Resource Manager 範本來加密執行中的 Linux IaaS VM](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm)。 
2. 選取 Azure 快速入門範本上的 [**部署至 azure** ]。
3. 將 [ **EncryptionOperation** ] 欄位從 [ **EnableEncryption** ] 變更為 [ **EnableEncryptionFormatAl**]。
4. 選取訂用帳戶、資源群組、資源群組位置、其他參數、法律條款及合約。 選取 [**建立**] 以在現有或執行中的 IaaS VM 上啟用加密。


### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a><a name="bkmk_EFAPSH"> </a> 使用 EncryptFormatAll 參數搭配 PowerShell cmdlet
使用[AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) Cmdlet 搭配 EncryptFormatAll 參數。

**使用用戶端密碼和 EncryptFormatAll 來加密執行中的 VM：** 例如，下列腳本會初始化您的變數，並使用 EncryptFormatAll 參數來執行 AzVMDiskEncryptionExtension Cmdlet。 資源群組、VM、金鑰保存庫、Azure AD 應用程式和用戶端秘密應該已經建立為必要條件。 以您的值取代 MyKeyVaultResourceGroup、MyVirtualMachineResourceGroup、MySecureVM、MySecureVault、我的 AAD 用戶端識別碼和我的 AAD 用戶端密碼。
  
   ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup'; 
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
      
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
   ```


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a><a name="bkmk_EFALVM"> </a> 使用 EncryptFormatAll 參數搭配邏輯磁碟區管理員 (LVM) 
我們建議進行 LVM-on-crypt 設定。 針對下列所有範例，請將裝置路徑和掛接點取代為您的使用案例。 以下步驟可以完成此設定：

- 新增將構成 VM 的資料磁碟。
- 格式化、掛接這些磁碟，並將其新增至 fstab 檔案。

    1. 格式化剛新增的磁碟。 我們會 Azure 在此產生的符號連結。 使用符號連結，可避免裝置名稱變更的相關問題。 如需詳細資訊，請參閱針對[裝置名稱問題進行疑難排解](troubleshoot-device-names-problems.md)。
    
             `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
        
    2. 掛接磁碟。
         
             `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`    
        
    3. 新增至 fstab。
         
            `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
        
    4. 執行 AzVMDiskEncryptionExtension PowerShell Cmdlet 搭配-EncryptFormatAll 來加密這些磁片。
             ```azurepowershell-interactive
             Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
             ```
    5. 在這些新的磁碟上設定 LVM。 請注意，VM 完成開機之後，加密磁碟機就會解除鎖定。 因此，LVM 掛接後續也必須延遲。




## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"> </a>透過客戶加密的 VHD 和加密金鑰建立的新 IaaS VM
在這個案例中，您可以使用 Resource Manager 範本、PowerShell Cmdlet 或 CLI 命令啟用加密。 下列各節將更加詳細地說明 Resource Manager 範本和 CLI 命令。 

使用附錄中的指示來準備可用於 Azure 的預先加密映像。 映像建立之後，您可以使用下一節的步驟來建立加密的 Azure VM。

* [準備已預先加密的 Linux VHD](disk-encryption-sample-scripts.md)

>[!IMPORTANT]
 >在啟用 Azure 磁碟加密之前，您必須先建立快照集，或將受控磁片型 VM 實例備份到以外的地方。 您可以從入口網站建立受控磁片的快照集，也可以使用[Azure 備份](../../backup/backup-azure-vms-encryption.md)。 擁有備份可確保在加密期間發生任何非預期的失敗時，能有復原選項可供選擇。 進行備份之後，請透過指定-skipVmBackup 參數，使用 AzVMDiskEncryptionExtension Cmdlet 來加密受控磁片。 對受控磁片型 Vm 的 AzVMDiskEncryptionExtension 命令會失敗，直到進行備份並指定此參數為止。 
>
>加密或停用加密可能會導致 VM 重新開機。



### <a name="use-azure-powershell-to-encrypt-iaas-vms-with-pre-encrypted-vhds"></a><a name="bkmk_VHDprePSH"> </a> 使用 Azure PowerShell 來加密具有預先加密 VHD 的 IaaS VM 
您可以使用 PowerShell Cmdlet [Set-set-azvmosdisk](/powershell/module/az.compute/set-azvmosdisk#examples)，在加密的 VHD 上啟用磁片加密。 下列範例會提供一些常見的參數。 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>在新增的資料磁碟上啟用加密
您可以使用[az vm disk attach](add-disk.md)或[透過 Azure 入口網站](attach-disk-portal.md)來新增資料磁片。 您必須先掛接新連結的資料磁碟，才可以加密。 您必須要求加密資料磁片磁碟機，因為在加密進行時，磁片磁碟機將無法使用。 

### <a name="enable-encryption-on-a-newly-added-disk-with-the-azure-cli"></a>使用 Azure CLI 在新增的磁片上啟用加密
 如果 VM 先前是以「全部」加密，則--磁片區類型參數應該保持全部。 全部包含作業系統與資料磁碟。 如果 VM 先前是以「OS」磁片區類型加密，則--磁片區類型參數應變更為 [全部]，以便同時包含 OS 和新的資料磁片。 如果 VM 只以「資料」磁片區類型加密，則它可以保留資料，如下所示。 將新的資料磁片新增至 VM 並不是足夠的加密準備。 在啟用加密之前，必須先將新連接的磁片格式化並正確掛接到 VM 中。 在 Linux 上，磁片必須以[持續性區塊裝置名稱](troubleshoot-device-names-problems.md)掛接在/etc/fstab 中。 

與 Powershell 語法不同的是，當您啟用加密時，CLI 不會要求您提供唯一的序列版本。 CLI 為自動產生並使用其唯一序列版本的值。

-  **使用用戶端秘密來加密執行中的 VM：** 
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **使用 KEK 來加密執行中的 VM 以包裝用戶端秘密：**

     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>透過 Azure PowerShell 在新增的磁碟上啟用加密
 當您使用 Powershell 為 Linux 的新磁片加密時，必須指定新的序列版本。 序列版本必須是唯一的。 下列腳本會產生序列版本的 GUID。 
 

-  **使用用戶端秘密來加密執行中的 VM：** 下列腳本會初始化您的變數，並執行 AzVMDiskEncryptionExtension 指令程式。 資源群組、VM、金鑰保存庫、Azure AD 應用程式和用戶端秘密應該已經建立為必要條件。 以您的值取代 MyVirtualMachineResourceGroup、MyKeyVaultResourceGroup、MySecureVM、MySecureVault、我的 AAD 用戶端識別碼和我的 AAD 用戶端密碼。 -VolumeType 參數會設定為資料磁碟，而非作業系統磁碟。 如果 VM 先前是以「OS」或「全部」的磁片區類型加密，則-VolumeType 參數應該變更為 All，以便同時包含 OS 和新的資料磁片。

     ```azurepowershell
         $KVRGname = 'MyKeyVaultResourceGroup';
         $VMRGName = 'MyVirtualMachineResourceGroup'; 
         $vmName = 'MySecureVM';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
     ```
- **使用 KEK 來加密執行中的 VM 以包裝用戶端秘密：** Azure 磁碟加密可讓您指定金鑰保存庫中的現有金鑰，以包裝在啟用加密時所產生的磁片加密秘密。 當指定金鑰加密金鑰時，Azure 磁碟加密會在寫入金鑰保存庫之前，使用該金鑰來包裝加密秘密。 -VolumeType 參數會設定為資料磁碟，而非作業系統磁碟。 如果 VM 先前是以「OS」或「全部」的磁片區類型加密，則-VolumeType 參數應該變更為 All，以便同時包含 OS 和新的資料磁片。

     ```azurepowershell
         $KVRGname = 'MyKeyVaultResourceGroup';
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $vmName = 'MyExtraSecureVM';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $keyEncryptionKeyName = 'MyKeyEncryptionKey';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
     ```


>[!NOTE]
> 磁片加密-keyvault 參數值的語法是完整的識別碼字串：/subscriptions/[訂用帳戶識別碼-guid]/resourceGroups/[資源群組名稱]/providers/Microsoft.KeyVault/vaults/[keyvault-name]。 </br> </br>
金鑰加密金鑰參數值的語法是 KEK 的完整 URI，如下所示 .. HTTPs：//[keyvault-name]. t a t/key/[kekname]/[KEK-unique-id]。

## <a name="disable-encryption-for-linux-vms"></a>停用 Linux VM 的加密
您可以使用 Azure PowerShell、Azure CLI 或 Resource Manager 範本來停用加密。 

>[!IMPORTANT]
>只有資料磁碟區支援在 Linux VM 上透過 Azure 磁碟加密停用加密。 如果 OS 磁片區已加密，則不支援在資料或 OS 磁片區上。 

- **使用 Azure PowerShell 停用磁片加密：** 若要停用加密，請使用[disable-azurermvmdiskencryption](/powershell/module/az.compute/disable-azvmdiskencryption) Cmdlet。 
     ```azurepowershell-interactive
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **透過 Azure CLI 停用加密：** 若要停用加密，請使用 [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) 命令。 
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **使用 Resource Manager 範本來停用加密：** 若要停用加密，請使用在[執行中的 LINUX VM 上停用加密](https://aka.ms/decrypt-linuxvm)範本。
     1. 選取 [**部署至 Azure**]。
     2. 選取訂用帳戶、資源群組、位置、VM、法律條款及合約。
     3. 選取 [**購買**] 以在執行中的 Windows VM 上停用磁片加密。 


## <a name="next-steps"></a>後續步驟

- [適用于 Linux 的 Azure 磁碟加密總覽](disk-encryption-overview-aad.md)
- [使用 Azure AD 建立和設定 Azure 磁碟加密的金鑰保存庫（舊版）](disk-encryption-key-vault-aad.md)
