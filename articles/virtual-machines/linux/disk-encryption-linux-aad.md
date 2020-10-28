---
title: 以 Azure AD App Linux IaaS VM (舊版) 進行 Azure 磁碟加密
description: 本文提供啟用 Linux IaaS VM 適用的 Microsoft Azure 磁碟加密的指示。
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: c8228086eb67478d80aa041004e0da3eed71f896
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92741805"
---
# <a name="enable-azure-disk-encryption-with-azure-ad-on-linux-vms-previous-release"></a>在 Linux Vm 上使用 Azure AD 啟用 Azure 磁碟加密 (舊版) 

Azure 磁碟加密的新版本不需要提供 Azure Active Directory (Azure AD) 應用程式參數，即可啟用 VM 磁片加密。 若使用新版本，您就不再需要在啟用加密步驟期間提供 Azure AD 認證。 您必須使用新的版本，在沒有 Azure AD 應用程式參數的情況下加密所有新的 Vm。 如需如何使用新版本啟用 VM 磁片加密的指示，請參閱 [LINUX vm 的 Azure 磁碟加密](disk-encryption-linux.md)。 已經使用 Azure AD 應用程式參數進行加密的 VM 仍然受支援，應該繼續使用 AAD 語法進行維護。

您可以啟用許多磁片加密案例，而這些步驟可能會因案例而異。 下列各節涵蓋 Linux 基礎結構即服務 (IaaS) Vm 的更詳細案例。 您只能將磁碟加密套用到[受支援 VM 大小和作業系統](disk-encryption-overview.md#supported-vms-and-operating-systems)的虛擬機器。 您也必須符合下列必要條件：

- [VM 的其他需求](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [網路功能與群組原則](disk-encryption-overview-aad.md#networking-and-group-policy)
- [加密金鑰儲存體需求](disk-encryption-overview-aad.md#encryption-key-storage-requirements)

在您加密磁片之前，請建立 [快照](snapshot-copy-managed-disk.md)集、建立備份或兩者。 擁有備份可確保在加密期間發生任何非預期的失敗時，能有復原選項可供選擇。 具有受控磁碟的 VM 需要有備份，才能進行加密。 進行備份之後，您可以使用 Set-AzVMDiskEncryptionExtension Cmdlet，藉由指定-skipVmBackup 參數來加密受控磁片。 如需有關如何備份和還原已加密 Vm 的詳細資訊，請參閱 [Azure 備份](../../backup/backup-azure-vms-encryption.md)。 

>[!WARNING]
 > - 如果您先前使用 [Azure 磁碟加密搭配 Azure AD 應用程式](disk-encryption-overview-aad.md) 來加密此 vm，則必須繼續使用此選項來加密您的 vm。 因為這不是支援的案例，所以您無法在此加密的 VM 上使用 [Azure 磁碟加密](disk-encryption-overview.md) ，這表示尚不支援將此加密 VM 的 Azure AD 應用程式切換離開。
 > - 為了確保加密秘密不會跨越區域界限，Azure 磁碟加密需要將金鑰保存庫和 Vm 共置於相同區域中。 在與要加密之 VM 相同的區域中，建立和使用金鑰保存庫。
 > - 當您加密 Linux OS 磁片區時，此程式可能需要幾個小時的時間。 Linux OS 磁片區所需的時間比要加密的資料磁片區長很正常。
> - 當您加密 Linux OS 磁片區時，VM 應視為無法使用。 強烈建議您在進行加密時避免 SSH 登入，以避免封鎖任何需要在加密過程中存取的開啟檔案。 若要檢查進度，請使用 [>get-azvmdiskencryptionstatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 或 [vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) 命令。 您可以預期此程式需要幾個小時的時間來處理 30 GB 的 OS 磁片區，再加上額外的時間來加密資料磁片區。 除非使用 **encrypt format all** 選項，否則資料磁片區加密時間會與資料磁片區的大小和數量成正比。 
 > - 只有資料磁碟區支援在 Linux VM 上停用加密。 如果 OS 磁片區已加密，則不支援在資料或 OS 磁片區上。 

 

## <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm"></a><a name="bkmk_RunningLinux"> </a> 在現有或執行中的 IaaS Linux VM 上啟用加密

在此案例中，您可以使用 Azure Resource Manager 範本、PowerShell Cmdlet 或 Azure CLI 命令啟用加密。 

>[!IMPORTANT]
 >在啟用 Azure 磁碟加密之前，您必須先建立快照集或備份受控磁片型 VM 實例。 您可以從 Azure 入口網站取得受控磁片的快照集，也可以使用 [Azure 備份](../../backup/backup-azure-vms-encryption.md)。 擁有備份可確保在加密期間發生任何非預期的失敗時，能有復原選項可供選擇。 進行備份之後，請使用 Set-AzVMDiskEncryptionExtension Cmdlet，藉由指定-skipVmBackup 參數來加密受控磁片。 在進行備份並指定此參數之前，Set-AzVMDiskEncryptionExtension 命令會針對受控磁片型 Vm 失敗。 
>
>加密或停用加密可能會導致 VM 重新開機。 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-the-azure-cli"></a><a name="bkmk_RunningLinuxCLI"> </a>使用 Azure CLI 在現有或執行中的 Linux VM 上啟用加密 
您可以藉由安裝和使用 [Azure CLI 2.0](/cli/azure) 命令列工具，在加密的 VHD 上啟用磁片加密。 您可以在瀏覽器中將它與 [Azure Cloud Shell](../../cloud-shell/overview.md) 搭配使用，或可將它安裝在本機電腦上，並在任何 PowerShell 工作階段中使用它。 若要在 Azure 中現有或執行中的 IaaS Linux VM 上啟用加密，請使用下列 CLI 命令：

在 Azure 中使用 [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) 命令以在執行中的 IaaS 虛擬機器上啟用加密。

-  **使用用戶端密碼來加密執行中的 VM：**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **使用 KEK 加密執行中的 VM 以包裝用戶端秘密：**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

   >[!NOTE]
   > 磁片加密-keyvault 參數值的語法是完整識別碼字串：/subscriptions/[訂用帳戶識別碼-guid]/resourceGroups/[資源群組名稱] 組名/providers/microsoft.keyvault/vaults/[keyvault 名稱]。</br> </br> 機碼加密金鑰參數值的語法是 KEK 的完整 URI，如： HTTPs：//[keyvault-name].. u t//[kekname]/[KEK-unique-id]。

- **確認磁片已加密：** 若要檢查 IaaS VM 的加密狀態，請使用 [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) 命令。 

     ```azurecli-interactive
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **停用加密：** 若要停用加密，請使用 [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) 命令。 只能在 Linux VM 的資料磁碟區上停用加密。
    
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-powershell"></a><a name="bkmk_RunningLinuxPSH"> </a>使用 PowerShell 在現有或執行中的 Linux VM 上啟用加密
使用 [>set-azvmdiskencryptionextension](/powershell/module/az.compute/set-azvmdiskencryptionextension) 指令 Cmdlet 在 Azure 中執行中的 IaaS 虛擬機器上啟用加密。 在磁片加密之前，請先建立 [快照](snapshot-copy-managed-disk.md) 集，或使用 [Azure 備份](../../backup/backup-azure-vms-encryption.md) 建立 VM 的備份。 PowerShell 指令碼中已指定 -skipVmBackup 參數以加密執行中的 Linux VM。

- **使用用戶端密碼來加密執行中的 VM：** 下列腳本會初始化您的變數，並執行 Set-AzVMDiskEncryptionExtension Cmdlet。 資源群組、VM、金鑰保存庫、Azure AD 應用程式和用戶端密碼都應該已建立為必要條件。 以您的值取代 MyVirtualMachineResourceGroup、MyKeyVaultResourceGroup、MySecureVM、MySecureVault、我的 AAD 用戶端識別碼和我的 AAD 用戶端密碼。 修改 -VolumeType 參數，以指定您要加密的磁碟。

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
- **使用 KEK 加密執行中的 VM 以包裝用戶端秘密：** Azure 磁碟加密可讓您在金鑰保存庫中指定現有的金鑰，以包裝在啟用加密時所產生的磁片加密秘密。 指定金鑰加密金鑰時，Azure 磁碟加密在寫入金鑰保存庫之前，會先使用該金鑰來包裝加密秘密。 修改 -VolumeType 參數，以指定您要加密的磁碟。 

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
  > 磁片加密-keyvault 參數值的語法是完整識別碼字串：/subscriptions/[訂用帳戶識別碼-guid]/resourceGroups/[KVresource 組名] 組名/providers/microsoft.keyvault/vaults/[keyvault 名稱]。</br> </br>
  機碼加密金鑰參數值的語法是 KEK 的完整 URI，如： HTTPs：//[keyvault-name].. u t//[kekname]/[KEK-unique-id]。 
    
- **確認磁片已加密：** 若要檢查 IaaS VM 的加密狀態，請使用 [>get-azvmdiskencryptionstatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) Cmdlet。 
    
     ```azurepowershell-interactive 
         Get-AzVmDiskEncryptionStatus -ResourceGroupName MyVirtualMachineResourceGroup -VMName MySecureVM
     ```
    
- **停用磁碟加密：** 若要停用加密，請使用 [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet。 只能在 Linux VM 的資料磁碟區上停用加密。
     
     ```azurepowershell-interactive 
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-with-a-template"></a><a name="bkmk_RunningLinux"> </a>透過範本在現有或執行中的 IaaS Linux VM 上啟用加密

您可以使用 [Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)，在 Azure 中現有或執行中的 IaaS Linux VM 上啟用磁碟加密。

1. 選取 Azure 快速入門範本上的 [ **部署至 azure** ]。

2. 選取訂用帳戶、資源群組、資源群組位置、參數、法律條款及合約。 選取 [ **建立** ]，在現有或執行中的 IaaS VM 上啟用加密。

針對使用 Azure AD 用戶端識別碼的現有或執行中 VM，以下資料表列出其 Resource Manager 範本參數︰

| 參數 | 描述 |
| --- | --- |
| AADClientID | 具有權限可將密碼寫入金鑰保存庫之 Azure AD 應用程式的用戶端識別碼。 |
| AADClientSecret | 具有權限可將密碼寫入金鑰保存庫之 Azure AD 應用程式的用戶端密碼。 |
| keyVaultName | 應上傳金鑰的金鑰保存庫名稱。 您可以使用 Azure CLI 命令 `az keyvault show --name "MySecureVault" --query KVresourceGroup` 來取得它。 |
|  keyEncryptionKeyURL | 用來加密所產生金鑰的金鑰加密金鑰 URL。 如果您在 [ **UseExistingKek** ] 下拉式清單中選取 [ **nokek** ]，則此參數是選擇性的。 如果您在 [ **UseExistingKek** ] 下拉式清單中選取 [ **kek** ]，則必須輸入 _keyEncryptionKeyURL_ 值。 |
| volumeType | 執行加密作業所在磁碟區的類型。 有效的支援值為 _OS_ 或 _全部_ 。  (在先前的必要條件一節中，請參閱作業系統和資料磁片支援的 Linux 發行版本及其版本。 )  |
| sequenceVersion | BitLocker 作業的順序版本。 每當在相同的 VM 上執行磁碟加密作業時便遞增此版本號碼。 |
| vmName | 要執行加密作業所在 VM 的名稱。 |
| 複雜密碼 | 輸入強式複雜密碼做為資料加密金鑰。 |



## <a name="use-the-encryptformatall-feature-for-data-disks-on-linux-iaas-vms"></a><a name="bkmk_EFA"> </a>在 Linux IaaS vm 上使用資料磁片的 EncryptFormatAll 功能
EncryptFormatAll 參數會減少加密 Linux 資料磁碟的時間。 符合特定準則的資料分割會格式化 (與其目前的檔案系統) 。 然後，它們會重新掛接回命令執行之前的位置。 如果您想要排除符合準則的資料磁片，您可以在執行命令之前將它卸載。

 執行此命令之後，任何先前掛接的磁片磁碟機都會格式化。 然後，加密層會在現在的空磁片磁碟機上啟動。 選取此選項時，連接至 VM 的暫存磁片也會加密。 如果暫時磁片磁碟機已重設，則會在下一個商機重新格式化並重新加密 VM 的 Azure 磁碟加密解決方案。

>[!WARNING]
> 當 VM 的資料磁片區上有需要的資料時，不應使用 EncryptFormatAll。 您可以將磁片取消掛接，以將其從加密中排除。 先在測試 VM 上試用 EncryptFormatAll 參數，以瞭解 feature 參數和其含意，然後再于生產 VM 上進行試用。 EncryptFormatAll 選項會格式化資料磁片，因此其上的所有資料都會遺失。 繼續之前，請確認您要排除的任何磁片都已正確卸載。 </br></br>
 >如果您在更新加密設定時設定此參數，則可能會在實際加密之前重新開機。 在此情況下，您也會想要從 fstab 檔中移除您不想格式化的磁片。 同樣地，您應該在起始加密作業之前，先將您想要加密格式的資料分割新增至 fstab 檔案。 

### <a name="encryptformatall-criteria"></a><a name="bkmk_EFACriteria"> </a> EncryptFormatAll 準則
此參數會通過所有分割區， *只要符合下列* 條件，就會加密這些資料分割： 
- 不是根/OS/開機磁碟分割
- 尚未加密
- 不是 BEK 磁碟區
- 不是 RAID 磁碟區
- 不是 LVM 磁碟區
- 已掛接

對組成 RAID 或 LVM 磁碟區的磁碟進行加密，而非對 RAID 或 LVM 磁碟區本身進行加密。

### <a name="use-the-encryptformatall-parameter-with-a-template"></a><a name="bkmk_EFATemplate"> </a> 使用 EncryptFormatAll 參數搭配範本
若要使用 EncryptFormatAll 選項，請使用任何已預先存在的 Azure Resource Manager 範本來加密 Linux VM，並變更 AzureDiskEncryption 資源的 [ **>encryptionoperation** ] 欄位。

1. 例如，使用 [Resource Manager 範本來加密執行中的 Linux IaaS VM](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm)。 
2. 選取 Azure 快速入門範本上的 [ **部署至 azure** ]。
3. 將 **>encryptionoperation** 欄位從 **EnableEncryption** 變更為 **EnableEncryptionFormatAl** 。
4. 選取訂用帳戶、資源群組、資源群組位置、其他參數、法律條款及合約。 選取 [ **建立** ]，在現有或執行中的 IaaS VM 上啟用加密。


### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a><a name="bkmk_EFAPSH"> </a>使用 EncryptFormatAll 參數搭配 PowerShell Cmdlet
使用 [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) Cmdlet 搭配 EncryptFormatAll 參數。

**使用用戶端密碼和 EncryptFormatAll 來加密執行中的 VM：** 例如，下列腳本會初始化您的變數，並使用 EncryptFormatAll 參數來執行 Set-AzVMDiskEncryptionExtension Cmdlet。 資源群組、VM、金鑰保存庫、Azure AD 應用程式和用戶端密碼都應該已建立為必要條件。 以您的值取代 MyKeyVaultResourceGroup、MyVirtualMachineResourceGroup、MySecureVM、MySecureVault、我的 AAD 用戶端識別碼和我的 AAD 用戶端密碼。
  
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


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a><a name="bkmk_EFALVM"> </a>使用 EncryptFormatAll 參數搭配邏輯磁片區管理員 (LVM)  
我們建議進行 LVM-on-crypt 設定。 針對下列所有範例，請將裝置路徑和掛接點取代為您的使用案例所適用的任何內容。 以下步驟可以完成此設定：

- 新增將構成 VM 的資料磁碟。
- 格式化、掛接這些磁碟，並將其新增至 fstab 檔案。

    1. 格式化剛新增的磁碟。 我們會 Azure 在此產生的符號連結。 使用符號連結，可避免裝置名稱變更的相關問題。 如需詳細資訊，請參閱針對 [裝置名稱問題進行疑難排解](../troubleshooting/troubleshoot-device-names-problems.md)。
    
        ```console
        mkfs -t ext4 /dev/disk/azure/scsi1/lun0
        ```

    2. 掛接磁碟。

        ```console
        mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint
        ```

    3. 新增至 fstab。

        ```console
        echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab
        ```

    4. 使用-EncryptFormatAll 執行 Set-AzVMDiskEncryptionExtension PowerShell Cmdlet 來加密這些磁片。

       ```azurepowershell-interactive
        Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
        ```

    5. 在這些新的磁碟上設定 LVM。 請注意，VM 完成開機之後，加密磁碟機就會解除鎖定。 因此，LVM 掛接後續也必須延遲。




## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"> </a>透過客戶加密的 VHD 和加密金鑰建立的新 IaaS VM
在這個案例中，您可以使用 Resource Manager 範本、PowerShell Cmdlet 或 CLI 命令啟用加密。 下列各節將更加詳細地說明 Resource Manager 範本和 CLI 命令。 

使用附錄中的指示來準備可用於 Azure 的預先加密映像。 映像建立之後，您可以使用下一節的步驟來建立加密的 Azure VM。

* [準備已預先加密的 Linux VHD](disk-encryption-sample-scripts.md)

>[!IMPORTANT]
 >在啟用 Azure 磁碟加密之前，您必須先建立快照集或備份受控磁片型 VM 實例。 您可以從入口網站取得受控磁片的快照集，也可以使用 [Azure 備份](../../backup/backup-azure-vms-encryption.md)。 擁有備份可確保在加密期間發生任何非預期的失敗時，能有復原選項可供選擇。 進行備份之後，請使用 Set-AzVMDiskEncryptionExtension Cmdlet，藉由指定-skipVmBackup 參數來加密受控磁片。 在進行備份並指定此參數之前，Set-AzVMDiskEncryptionExtension 命令會針對受控磁片型 Vm 失敗。 
>
>加密或停用加密可能會導致 VM 重新開機。



### <a name="use-azure-powershell-to-encrypt-iaas-vms-with-pre-encrypted-vhds"></a><a name="bkmk_VHDprePSH"> </a> 使用 Azure PowerShell 來加密具有預先加密 VHD 的 IaaS VM 
您可以使用 PowerShell Cmdlet [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples) 在加密的 VHD 上啟用磁碟加密。 下列範例會為您提供一些常見的參數。 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>在新增的資料磁碟上啟用加密
您可以使用 [az vm disk attach](add-disk.md) 或 [Azure 入口網站](attach-disk-portal.md)來新增資料磁片。 您必須先掛接新連結的資料磁碟，才可以加密。 您必須要求資料磁片磁碟機的加密，因為加密正在進行中時，磁片磁碟機將無法使用。 

### <a name="enable-encryption-on-a-newly-added-disk-with-the-azure-cli"></a>使用 Azure CLI 在新增的磁片上啟用加密
 如果 VM 先前是以「全部」加密，則--磁片區類型參數應該保持全部。 全部包含作業系統與資料磁碟。 如果 VM 先前是以「OS」磁片區類型加密，則--磁片區類型參數應變更為「全部」，如此一來，就會包含作業系統和新的資料磁片。 如果 VM 僅以「資料」磁片區類型加密，則它可以保留資料，如下所示。 將新的資料磁片新增至 VM 並將其附加至 VM，並不會準備好進行加密。 在啟用加密之前，必須先將新連接的磁片格式化並正確地掛接在 VM 中。 在 Linux 上，必須將磁片掛接在具有持續性 [區塊裝置名稱](../troubleshooting/troubleshoot-device-names-problems.md)的/etc/fstab 中。 

相較于 PowerShell 語法，CLI 不會要求您在啟用加密時提供唯一的順序版本。 CLI 為自動產生並使用其唯一序列版本的值。

-  **使用用戶端密碼來加密執行中的 VM：** 
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **使用 KEK 加密執行中的 VM 以包裝用戶端秘密：**

     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>透過 Azure PowerShell 在新增的磁碟上啟用加密
 當您使用 PowerShell 為 Linux 的新磁片加密時，必須指定新的序列版本。 序列版本必須是唯一的。 下列腳本會產生序列版本的 GUID。 
 

-  **使用用戶端密碼來加密執行中的 VM：** 下列腳本會初始化您的變數，並執行 Set-AzVMDiskEncryptionExtension Cmdlet。 資源群組、VM、金鑰保存庫、Azure AD 應用程式和用戶端密碼都應該已建立為必要條件。 以您的值取代 MyVirtualMachineResourceGroup、MyKeyVaultResourceGroup、MySecureVM、MySecureVault、我的 AAD 用戶端識別碼和我的 AAD 用戶端密碼。 -VolumeType 參數會設定為資料磁碟，而非作業系統磁碟。 如果 VM 先前是以「OS」或「全部」磁片區類型加密，則-VolumeType 參數應變更為「全部」，如此一來，就會包含作業系統和新的資料磁片。

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
- **使用 KEK 加密執行中的 VM 以包裝用戶端秘密：** Azure 磁碟加密可讓您在金鑰保存庫中指定現有的金鑰，以包裝在啟用加密時所產生的磁片加密秘密。 指定金鑰加密金鑰時，Azure 磁碟加密在寫入金鑰保存庫之前，會先使用該金鑰來包裝加密秘密。 -VolumeType 參數會設定為資料磁碟，而非作業系統磁碟。 如果 VM 先前是以「OS」或「全部」磁片區類型加密，則-VolumeType 參數應變更為「全部」，如此一來，就會包含作業系統和新的資料磁片。

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
> 磁片加密-keyvault 參數值的語法是完整識別碼字串：/subscriptions/[訂用帳戶識別碼-guid]/resourceGroups/[資源群組名稱] 組名/providers/microsoft.keyvault/vaults/[keyvault 名稱]。 </br> </br>
機碼加密金鑰參數值的語法是 KEK 的完整 URI，如： HTTPs：//[keyvault-name].. u t//[kekname]/[KEK-unique-id]。

## <a name="disable-encryption-for-linux-vms"></a>停用 Linux VM 的加密
您可以使用 Azure PowerShell、Azure CLI 或 Resource Manager 範本來停用加密。 

>[!IMPORTANT]
>只有資料磁碟區支援在 Linux VM 上透過 Azure 磁碟加密停用加密。 如果 OS 磁片區已加密，則不支援在資料或 OS 磁片區上。 

- **使用 Azure PowerShell 停用磁片加密：** 若要停用加密，請使用 [>disable-azurermvmdiskencryption](/powershell/module/az.compute/disable-azvmdiskencryption) Cmdlet。 
     ```azurepowershell-interactive
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **使用 Azure CLI 停用加密：** 若要停用加密，請使用 [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) 命令。 
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **使用 Resource Manager 範本停用加密：** 若要停用加密，請使用在 [執行中的 LINUX VM 範本上停用加密](https://aka.ms/decrypt-linuxvm) 。
     1. 選取 [ **部署至 Azure** ]。
     2. 選取訂用帳戶、資源群組、位置、VM、法律條款及合約。
     3. 選取 [ **購買** ] 以在執行中的 Windows VM 上停用磁片加密。 


## <a name="next-steps"></a>後續步驟

- [適用于 Linux 的 Azure 磁碟加密總覽](disk-encryption-overview-aad.md)
- [使用 Azure AD (舊版建立和設定 Azure 磁碟加密的金鑰保存庫) ](disk-encryption-key-vault-aad.md)
