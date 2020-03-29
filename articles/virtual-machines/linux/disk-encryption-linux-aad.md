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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970615"
---
# <a name="enable-azure-disk-encryption-with-azure-ad-on-linux-vms-previous-release"></a>在 Linux VM 上使用 Azure AD 啟用 Azure 磁片加密（上一個版本）

Azure 磁片加密的新版本消除了提供 Azure 活動目錄 （Azure AD） 應用程式參數以啟用 VM 磁片加密的要求。 若使用新版本，您就不再需要在啟用加密步驟期間提供 Azure AD 認證。 使用新版本，必須對所有新 VM 進行加密，而無需 Azure AD 應用程式參數。 有關如何使用新版本啟用 VM 磁片加密的說明，請參閱[Linux VMS 的 Azure 磁片加密](disk-encryption-linux.md)。 已經使用 Azure AD 應用程式參數進行加密的 VM 仍然受支援，應該繼續使用 AAD 語法進行維護。

您可以啟用許多磁片加密方案，並且步驟可能因方案而異。 以下各節更詳細地介紹 Linux 基礎架構作為服務 （IaaS） VM 的方案。 您只能將磁片加密應用於[受支援的 VM 大小和作業系統](disk-encryption-overview.md#supported-vms-and-operating-systems)的虛擬機器。 您還必須滿足以下先決條件：

- [VM 的其他要求](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [網路和群組原則](disk-encryption-overview-aad.md#networking-and-group-policy)
- [加密金鑰存儲要求](disk-encryption-overview-aad.md#encryption-key-storage-requirements)

在加密磁片之前，先拍攝[快照](snapshot-copy-managed-disk.md)、進行備份或兩者兼而有之。 擁有備份可確保在加密期間發生任何非預期的失敗時，能有復原選項可供選擇。 具有受控磁碟的 VM 需要有備份，才能進行加密。 進行備份後，可以使用 Set-AzVMDisk 加密擴展 Cmdlet 通過指定 -skipVmBackup 參數來加密託管磁片。 有關如何備份和還原加密 VM 的詳細資訊，請參閱[Azure 備份](../../backup/backup-azure-vms-encryption.md)。 

>[!WARNING]
 > - 如果以前將[Azure 磁片加密與 Azure AD 應用一起使用](disk-encryption-overview-aad.md)以加密此 VM，則必須繼續使用此選項對 VM 進行加密。 不能在此加密 VM 上使用[Azure 磁片加密](disk-encryption-overview.md)，因為這是受支援的方案，這意味著尚不支援出於此加密 VM 從 Azure AD 應用程式切換。
 > - 為了確保加密機密不會跨越區域邊界，Azure 磁片加密需要金鑰保存庫和 VM 在同一區域中共存。 創建並使用與 VM 位於同一區域的金鑰保存庫進行加密。
 > - 加密 Linux 作業系統卷時，此過程可能需要幾個小時。 Linux 作業系統卷加密的時間比資料卷長是正常的。
> - 加密 Linux 作業系統卷時，應認為 VM 不可用。 我們強烈建議您在加密過程中避免 SSH 登錄，以避免阻止加密過程中需要訪問的任何打開的檔。 要檢查進度，請使用[獲取-AzVM 磁片加密狀態](/powershell/module/az.compute/get-azvmdiskencryptionstatus)或[vm 加密顯示命令](/cli/azure/vm/encryption#az-vm-encryption-show)。 對於 30 GB 的作業系統卷，此過程需要幾個小時，此外還有額外的時間來加密資料卷。 資料卷加密時間與資料卷的大小和數量成正比，除非使用**加密格式所有**選項。 
 > - 只有資料磁碟區支援在 Linux VM 上停用加密。 如果作業系統卷已加密，則資料或作業系統卷不支援它。 

 

## <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm"></a><a name="bkmk_RunningLinux"> </a> 在現有或執行中的 IaaS Linux VM 上啟用加密

在這種情況下，可以使用 Azure 資源管理器範本、PowerShell Cmdlet 或 Azure CLI 命令啟用加密。 

>[!IMPORTANT]
 >在啟用 Azure 磁片加密之前，必須在快照或備份基於磁片的託管 VM 實例之前進行備份。 可以從 Azure 門戶拍攝託管磁片的快照，也可以使用[Azure 備份](../../backup/backup-azure-vms-encryption.md)。 擁有備份可確保在加密期間發生任何非預期的失敗時，能有復原選項可供選擇。 進行備份後，使用 Set-AzVMDisk 加密擴展 Cmdlet 通過指定 -skipVmBackup 參數來加密託管磁片。 Set-AzVMDisk加密擴展命令對基於磁片的託管 VM 失敗，直到進行備份並指定此參數。 
>
>加密或禁用加密可能會導致 VM 重新開機。 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-the-azure-cli"></a><a name="bkmk_RunningLinuxCLI"> </a>使用 Azure CLI 在現有或正在運行的 Linux VM 上啟用加密 
您可以通過安裝和使用[Azure CLI 2.0](/cli/azure)命令列工具在加密的 VHD 上啟用磁片加密。 您可以在瀏覽器中將它與 [Azure Cloud Shell](../../cloud-shell/overview.md) 搭配使用，或可將它安裝在本機電腦上，並在任何 PowerShell 工作階段中使用它。 若要在 Azure 中現有或執行中的 IaaS Linux VM 上啟用加密，請使用下列 CLI 命令：

在 Azure 中使用 [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) 命令以在執行中的 IaaS 虛擬機器上啟用加密。

-  **使用用戶端金鑰加密正在運行的 VM：**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **使用 KEK 對正在運行的 VM 進行加密以包裝用戶端金鑰：**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

   >[!NOTE]
   > 磁片加密金鑰庫參數的值的語法是完整的識別碼字串：/訂閱/[訂閱-id-guid]/資源組組/[資源組名稱]/提供程式/Microsoft.KeyVault/vault/[金鑰庫名稱]。</br> </br> 金鑰加密金鑰參數的值的語法是 KEK 的完整 URI，如： HTTPs：//[金鑰庫名稱]vault.azure.net/金鑰/[kekname]/[kek-唯一-id]。

- **驗證磁片是否加密：** 要檢查 IaaS VM 的加密狀態，請使用[az vm 加密顯示](/cli/azure/vm/encryption#az-vm-encryption-show)命令。 

     ```azurecli-interactive
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **停用加密：** 若要停用加密，請使用 [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) 命令。 只能在 Linux VM 的資料磁碟區上停用加密。
    
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-powershell"></a><a name="bkmk_RunningLinuxPSH"> </a>使用 PowerShell 在現有或正在運行的 Linux VM 上啟用加密
使用[Set-AzVM 磁片加密擴展](/powershell/module/az.compute/set-azvmdiskencryptionextension)Cmdlet 在 Azure 中正在運行的 IaaS 虛擬機器上啟用加密。 在加密磁片之前，使用[Azure 備份](../../backup/backup-azure-vms-encryption.md)拍攝[快照](snapshot-copy-managed-disk.md)或備份 VM。 -skipVmBackup 參數已在 PowerShell 腳本中指定，用於加密正在運行的 Linux VM。

- **使用用戶端金鑰加密正在運行的 VM：** 以下腳本初始化變數並運行 Set-AzVM 磁片加密擴展 Cmdlet。 資源組、VM、金鑰保存庫、Azure AD 應用和用戶端機密應已創建為先決條件。 用您的價值觀替換 MyVirtualMachine 資源組、MyKeyVault 資源組、MySecureVM、MySecureVault、My-AAD 用戶端 ID 和 My-AAD 用戶端機密。 修改 -VolumeType 參數以指定要加密的磁片。

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
- **使用 KEK 對正在運行的 VM 進行加密以包裝用戶端金鑰：** Azure 磁片加密允許您在金鑰保存庫中指定現有金鑰，以包裝啟用加密時生成的磁片加密機密。 指定金鑰加密金鑰後，Azure 磁片加密將使用該金鑰在寫入金鑰保存庫之前隱藏加密機密。 修改 -VolumeType 參數以指定要加密的磁片。 

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
  > 磁片加密金鑰保管參數的值的語法是完整的識別碼字串：/訂閱/[訂閱-id-guid]/資源組/[KV資源組名稱]/提供程式/Microsoft.KeyVault/vault/[金鑰庫名稱]。</br> </br>
  金鑰加密金鑰參數的值的語法是 KEK 的完整 URI，如： HTTPs：//[金鑰庫名稱]vault.azure.net/金鑰/[kekname]/[kek-唯一-id]。 
    
- **驗證磁片是否加密：** 要檢查 IaaS VM 的加密狀態，請使用[獲取-AzVm 磁片加密狀態](/powershell/module/az.compute/get-azvmdiskencryptionstatus)Cmdlet。 
    
     ```azurepowershell-interactive 
         Get-AzVmDiskEncryptionStatus -ResourceGroupName MyVirtualMachineResourceGroup -VMName MySecureVM
     ```
    
- **停用磁碟加密：** 若要停用加密，請使用 [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet。 只能在 Linux VM 的資料磁碟區上停用加密。
     
     ```azurepowershell-interactive 
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-with-a-template"></a><a name="bkmk_RunningLinux"> </a>透過範本在現有或執行中的 IaaS Linux VM 上啟用加密

您可以使用 [Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)，在 Azure 中現有或執行中的 IaaS Linux VM 上啟用磁碟加密。

1. 在 Azure 快速啟動範本上選擇 **"部署到 Azure"。**

2. 選取訂用帳戶、資源群組、資源群組位置、參數、法律條款及合約。 選擇 **"創建**"可在現有或正在運行的 IaaS VM 上啟用加密。

針對使用 Azure AD 用戶端識別碼的現有或執行中 VM，以下資料表列出其 Resource Manager 範本參數︰

| 參數 | 描述 |
| --- | --- |
| AADClientID | 具有權限可將密碼寫入金鑰保存庫之 Azure AD 應用程式的用戶端識別碼。 |
| AADClientSecret | 具有權限可將密碼寫入金鑰保存庫之 Azure AD 應用程式的用戶端密碼。 |
| keyVaultName | 應上傳金鑰的金鑰保存庫名稱。 您可以使用 Azure CLI 命令 `az keyvault show --name "MySecureVault" --query KVresourceGroup` 來取得它。 |
|  keyEncryptionKeyURL | 用來加密所產生金鑰的金鑰加密金鑰 URL。 如果在 **"使用現有 Kek"** 下拉清單中選擇**nokek，** 則此參數是可選的。 如果在 **"使用現有 Kek"** 下拉清單中選擇**kek，** 則必須輸入_鍵加密 KeyURL_值。 |
| volumeType | 執行加密作業所在磁碟區的類型。 支援的有效值為_作業系統_或_全部_。 （請參閱前面先決條件部分中支援的 Linux 發行版本及其作業系統和資料磁片版本。 |
| sequenceVersion | BitLocker 作業的順序版本。 每當在相同的 VM 上執行磁碟加密作業時便遞增此版本號碼。 |
| vmName | 要執行加密作業所在 VM 的名稱。 |
| 複雜密碼 | 輸入強式複雜密碼做為資料加密金鑰。 |



## <a name="use-the-encryptformatall-feature-for-data-disks-on-linux-iaas-vms"></a><a name="bkmk_EFA"> </a>在 Linux IaaS VM 上對資料磁片使用加密格式所有功能
EncryptFormatAll 參數會減少加密 Linux 資料磁碟的時間。 滿足特定條件的分區設置格式（及其當前檔案系統）。 然後，它們被重新安裝回命令執行之前的位置。 如果要排除滿足條件的資料磁片，可以在運行命令之前將其卸載。

 運行此命令後，以前裝載的任何磁碟機都設置格式化。 然後，加密層從現在空的磁碟機頂部開始。 選擇此選項後，附加到 VM 的短暫資源磁片也會加密。 如果臨時磁碟機被重置，則下次由 Azure 磁片加密解決方案對其進行重新格式化和重新加密 VM。

>[!WARNING]
> 當 VM 的資料卷上需要資料時，不應使用加密格式所有。 您可以通過卸載磁片從加密中排除磁片。 在生產 VM 上嘗試之前，請先在測試 VM 上試用加密格式All 參數，以瞭解功能參數及其含義。 "加密格式所有"選項格式化資料磁片，因此資料磁片上的所有資料都將丟失。 在繼續操作之前，請驗證要排除的任何磁片是否正確卸載。 </br></br>
 >如果在更新加密設定時設置此參數，則可能導致在實際加密之前重新開機。 在這種情況下，您還希望從 fstab 檔中刪除不希望格式化的磁片。 同樣，在啟動加密操作之前，應將要加密格式化的分區添加到 fstab 檔中。 

### <a name="encryptformatall-criteria"></a><a name="bkmk_EFACriteria"> </a> EncryptFormatAll 準則
參數遍經所有分區並加密它們，只要它們滿足以下*所有*條件： 
- 不是根/OS/開機磁碟分割
- 尚未加密
- 不是 BEK 磁碟區
- 不是 RAID 磁碟區
- 不是 LVM 磁碟區
- 已掛接

對組成 RAID 或 LVM 磁碟區的磁碟進行加密，而非對 RAID 或 LVM 磁碟區本身進行加密。

### <a name="use-the-encryptformatall-parameter-with-a-template"></a><a name="bkmk_EFATemplate"> </a> 使用 EncryptFormatAll 參數搭配範本
要使用"加密格式全部"選項，請使用加密 Linux VM 的任何預先存在的 Azure 資源管理器範本，並更改 AzureDisk 加密資源的**加密操作**欄位。

1. 例如，使用 [Resource Manager 範本來加密執行中的 Linux IaaS VM](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm)。 
2. 在 Azure 快速啟動範本上選擇 **"部署到 Azure"。**
3. 將**加密操作**欄位從**啟用加密**更改為**啟用加密格式**。
4. 選取訂用帳戶、資源群組、資源群組位置、其他參數、法律條款及合約。 選擇 **"創建**"可在現有或正在運行的 IaaS VM 上啟用加密。


### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a><a name="bkmk_EFAPSH"> </a> 使用 EncryptFormatAll 參數搭配 PowerShell cmdlet
使用["集-AzVM 磁片加密擴展](/powershell/module/az.compute/set-azvmdiskencryptionextension)Cmdlet"與加密格式所有參數。

**使用用戶端金鑰和加密格式對所有正在運行的 VM 進行加密：** 例如，以下腳本初始化變數，並使用加密格式All參數運行 Set-AzVMDisk加密擴展 Cmdlet。 資源組、VM、金鑰保存庫、Azure AD 應用和用戶端機密應已創建為先決條件。 用您的價值觀替換 MyKeyVault 資源組、我的虛擬電腦資源組、MySecureVM、MySecureVault、My-AAD 用戶端 ID 和 My-AAD 用戶端機密。
  
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
我們建議進行 LVM-on-crypt 設定。 對於以下所有示例，請將設備路徑和裝載點替換為任何適合您的用例。 以下步驟可以完成此設定：

- 新增將構成 VM 的資料磁碟。
- 格式化、掛接這些磁碟，並將其新增至 fstab 檔案。

    1. 格式化剛新增的磁碟。 我們會 Azure 在此產生的符號連結。 使用符號連結，可避免裝置名稱變更的相關問題。 有關詳細資訊，請參閱[排除設備名稱問題的疑難排解](troubleshoot-device-names-problems.md)。
    
             `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
        
    2. 掛接磁碟。
         
             `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`    
        
    3. 新增至 fstab。
         
            `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
        
    4. 使用 -EncryptFormatAll 運行 Set-AzVM 磁片加密擴展電源 Shell Cmdlet 以加密這些磁片。
             ```azurepowershell-interactive
             Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
             ```
    5. 在這些新的磁碟上設定 LVM。 請注意，VM 完成開機之後，加密磁碟機就會解除鎖定。 因此，LVM 掛接後續也必須延遲。




## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"> </a>透過客戶加密的 VHD 和加密金鑰建立的新 IaaS VM
在這個案例中，您可以使用 Resource Manager 範本、PowerShell Cmdlet 或 CLI 命令啟用加密。 下列各節將更加詳細地說明 Resource Manager 範本和 CLI 命令。 

使用附錄中的指示來準備可用於 Azure 的預先加密映像。 映像建立之後，您可以使用下一節的步驟來建立加密的 Azure VM。

* [準備已預先加密的 Linux VHD](disk-encryption-sample-scripts.md)

>[!IMPORTANT]
 >在啟用 Azure 磁片加密之前，必須在快照或備份基於磁片的託管 VM 實例之前進行備份。 可以從門戶拍攝託管磁片的快照，也可以使用[Azure 備份](../../backup/backup-azure-vms-encryption.md)。 擁有備份可確保在加密期間發生任何非預期的失敗時，能有復原選項可供選擇。 進行備份後，使用 Set-AzVMDisk 加密擴展 Cmdlet 通過指定 -skipVmBackup 參數來加密託管磁片。 Set-AzVMDisk加密擴展命令對基於磁片的託管 VM 失敗，直到進行備份並指定此參數。 
>
>加密或禁用加密可能會導致 VM 重新開機。



### <a name="use-azure-powershell-to-encrypt-iaas-vms-with-pre-encrypted-vhds"></a><a name="bkmk_VHDprePSH"> </a> 使用 Azure PowerShell 來加密具有預先加密 VHD 的 IaaS VM 
您可以使用 PowerShell Cmdlet[集-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples)在加密的 VHD 上啟用磁片加密。 下面的示例提供了一些常見參數。 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>在新增的資料磁碟上啟用加密
可以使用[az vm 磁片附加](add-disk.md)或通過[Azure 門戶](attach-disk-portal.md)添加新資料磁片。 您必須先掛接新連結的資料磁碟，才可以加密。 您必須請求資料磁碟機的加密，因為在加密進行期間，磁碟機將不可用。 

### <a name="enable-encryption-on-a-newly-added-disk-with-the-azure-cli"></a>使用 Azure CLI 在新添加的磁片上啟用加密
 如果 VM 以前使用"全部"加密，則 --體積類型參數應保持全部。 全部包含作業系統與資料磁碟。 如果 VM 以前使用"OS"的卷類型進行加密，則應將 --卷類型參數更改為"全部"，以便同時包含作業系統和新資料磁片。 如果 VM 僅使用"資料"的卷類型進行加密，則可以保留資料，如下所示。 向 VM 添加新資料磁片並附加新資料磁片不足以為加密做好準備。 在啟用加密之前，還必須格式化新連接的磁片並將其正確安裝在 VM 中。 在 Linux 上，磁片必須裝入 /etc/fstab 中，帶有[持久塊設備名稱](troubleshoot-device-names-problems.md)。 

與 Powershell 語法相比，CLI 不需要您在啟用加密時提供唯一的序列版本。 CLI 為自動產生並使用其唯一序列版本的值。

-  **使用用戶端金鑰加密正在運行的 VM：** 
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **使用 KEK 對正在運行的 VM 進行加密以包裝用戶端金鑰：**

     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>透過 Azure PowerShell 在新增的磁碟上啟用加密
 當您使用 Powershell 加密 Linux 的新磁片時，需要指定新的序列版本。 序列版本必須是唯一的。 以下腳本為序列版本生成 GUID。 
 

-  **使用用戶端金鑰加密正在運行的 VM：** 以下腳本初始化變數並運行 Set-AzVM 磁片加密擴展 Cmdlet。 資源組、VM、金鑰保存庫、Azure AD 應用和用戶端機密應已創建為先決條件。 用您的價值觀替換 MyVirtualMachine 資源組、MyKeyVault 資源組、MySecureVM、MySecureVault、My-AAD 用戶端 ID 和 My-AAD 用戶端機密。 -VolumeType 參數會設定為資料磁碟，而非作業系統磁碟。 如果 VM 以前使用"OS"或"全部"的卷類型進行加密，則應將 -VolumeType 參數更改為"全部"，以便同時包含作業系統和新資料磁片。

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
- **使用 KEK 對正在運行的 VM 進行加密以包裝用戶端金鑰：** Azure 磁片加密允許您在金鑰保存庫中指定現有金鑰，以包裝啟用加密時生成的磁片加密機密。 指定金鑰加密金鑰後，Azure 磁片加密將使用該金鑰在寫入金鑰保存庫之前隱藏加密機密。 -VolumeType 參數會設定為資料磁碟，而非作業系統磁碟。 如果 VM 以前使用"OS"或"全部"的卷類型進行加密，則應將 -VolumeType 參數更改為"全部"，以便同時包含作業系統和新資料磁片。

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
> 磁片加密金鑰庫參數的值的語法是完整的識別碼字串：/訂閱/[訂閱-id-guid]/資源組組/[資源組名稱]/提供程式/Microsoft.KeyVault/vault/[金鑰庫名稱]。 </br> </br>
金鑰加密金鑰參數的值的語法是 KEK 的完整 URI，如： HTTPs：//[金鑰庫名稱]vault.azure.net/金鑰/[kekname]/[kek-唯一-id]。

## <a name="disable-encryption-for-linux-vms"></a>停用 Linux VM 的加密
可以使用 Azure PowerShell、Azure CLI 或資源管理器範本禁用加密。 

>[!IMPORTANT]
>只有資料磁碟區支援在 Linux VM 上透過 Azure 磁碟加密停用加密。 如果作業系統卷已加密，則資料或作業系統卷不支援它。 

- **使用 Azure PowerShell 禁用磁片加密：** 要禁用加密，請使用[禁用 AzureRmVM 磁片加密](/powershell/module/az.compute/disable-azvmdiskencryption)Cmdlet。 
     ```azurepowershell-interactive
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **透過 Azure CLI 停用加密：** 若要停用加密，請使用 [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) 命令。 
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **使用資源管理器範本禁用加密：** 要禁用加密，請使用正在運行的 Linux VM 範本[上的禁用加密](https://aka.ms/decrypt-linuxvm)。
     1. 選擇 **"部署到 Azure**"。
     2. 選取訂用帳戶、資源群組、位置、VM、法律條款及合約。
     3. 選擇 **"購買"** 以禁用正在運行的 Windows VM 上的磁片加密。 


## <a name="next-steps"></a>後續步驟

- [用於 Linux 概述的 Azure 磁片加密](disk-encryption-overview-aad.md)
- [使用 Azure AD 創建和配置 Azure 磁片加密的金鑰保存庫（上一版本）](disk-encryption-key-vault-aad.md)
