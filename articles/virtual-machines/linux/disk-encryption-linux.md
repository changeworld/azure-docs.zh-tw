---
title: Linux VM 上的 Azure 磁碟加密案例
description: 本文提供有關為 Linux VM 啟用 Microsoft Azure 磁碟加密的說明,用於各種方案
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: b7df3c07518a9211bd1abf785e3f4954c41ebffc
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529458"
---
# <a name="azure-disk-encryption-scenarios-on-linux-vms"></a>Linux VM 上的 Azure 磁碟加密案例

Azure 磁碟加密使用 Linux 的 DM-Crypt 功能為 Azure 虛擬機器 (VM) 的作業系統和資料磁碟提供卷加密,並與 Azure 金鑰保管庫整合,以説明您控制和管理磁碟加密密鑰和機密。 有關服務的概述,請參閱 Linux [VM 的 Azure 磁碟加密](disk-encryption-overview.md)。

有許多磁碟加密方案,步驟可能因方案而異。 以下各節更詳細地介紹 Linux VM 的方案。

您只能將磁碟加密應用於[受支援的 VM 大小和作業系統](disk-encryption-overview.md#supported-vms-and-operating-systems)的虛擬機器。 您必須滿足以下先決條件:

- [VM 的其他要求](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [網路需求](disk-encryption-overview.md#networking-requirements)
- [加密金鑰儲存要求](disk-encryption-overview.md#encryption-key-storage-requirements)

在所有情況下,都應在磁碟加密之前[拍攝快照](snapshot-copy-managed-disk.md)和/或創建備份。 擁有備份可確保在加密期間發生任何非預期的失敗時，能有復原選項可供選擇。 具有受控磁碟的 VM 需要有備份，才能進行加密。 進行備份後,可以使用[Set-AzVMDisk 加密擴展 cmdlet](/powershell/module/az.compute/set-azvmdiskencryptionextension)通過指定 -skipVmBackup 參數來加密託管磁碟。 如需如何備份和還原已加密 VM 的詳細資訊，請參閱 [Azure 備份](../../backup/backup-azure-vms-encryption.md)一文。 

>[!WARNING]
> - 如果以前使用 Azure 磁碟加密與 Azure AD 一起加密 VM,則必須繼續使用此選項對 VM 進行加密。 有關詳細資訊[,請參閱 Azure 磁碟加密與 Azure AD(上一版本)。](disk-encryption-overview-aad.md) 
>
> - 加密 Linux 操作系統卷時,應認為 VM 不可用。 我們強烈建議在加密過程中避免 SSH 登錄,以避免在加密過程中阻止任何需要訪問的打開文件的問題。 要檢查進度,請使用[獲取-AzVM 磁碟加密狀態](/powershell/module/az.compute/get-azvmdiskencryptionstatus)PowerShell cmdlet 或[vm 加密顯示](/cli/azure/vm/encryption#az-vm-encryption-show)CLI 命令。 對於 30GB OS 磁碟區，此過程可能需要幾個小時，再加上額外的時間來進行加密資料磁碟區。 除非使用加密格式所有選項，否則資料磁碟區加密時間將與資料磁碟區的大小和數量成正比。 
> - 只有資料磁碟區支援在 Linux VM 上停用加密。 如果 OS 磁碟區已加密，則不支援在資料或 OS 磁碟區上停用加密。  

## <a name="install-tools-and-connect-to-azure"></a>安裝工具並連線到 Azure

Azure 磁碟加密可以通過[Azure CLI](/cli/azure)和[Azure PowerShell](/powershell/azure/new-azureps-module-az)啟用和管理。 為此,必須在本地安裝工具並連接到 Azure 訂閱。

### <a name="azure-cli"></a>Azure CLI

[Azure CLI 2.0](/cli/azure) 是命令列工具，可用於管理 Azure 資源。 CLI 的設計是要讓您能夠彈性地查詢資料、以非封鎖處理序的形式支援長時間執行作業，並輕鬆地撰寫指令碼。 您可以按照[安裝 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)中的步驟在本地安裝它。

 

要[使用 Azure CLI 登入到 Azure 帳戶](/cli/azure/authenticate-azure-cli),請使用[az 登入](/cli/azure/reference-index?view=azure-cli-latest#az-login)命令。

```azurecli
az login
```

如果您想要選取用於登入的租用戶，請使用：
    
```azurecli
az login --tenant <tenant>
```

如果您有多個訂用帳戶並想要指定特定訂用帳戶，請使用 [az account list](/cli/azure/account#az-account-list) 取得訂用帳戶清單並以 [az account set](/cli/azure/account#az-account-set) 進行指定。
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

如需詳細資訊，請參閱[開始使用 Azure CLI 2.0](/cli/azure/get-started-with-azure-cli)。 

### <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell az 模組](/powershell/azure/new-azureps-module-az)提供一組 cmdlet,這些 cmdlet 使用 Azure[資源管理員](../../azure-resource-manager/management/overview.md)模型來管理 Azure 資源。 您可以在瀏覽器中將其與 Azure[雲外殼](../../cloud-shell/overview.md)一起使用,也可以使用[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)中的說明將其安裝到本地電腦上。 

如果您已將它安裝在本機上，請確定您是使用最新版的 Azure PowerShell SDK 版本來設定 Azure 磁碟加密。 下載最新版的 [Azure PowerShell 版本](https://github.com/Azure/azure-powershell/releases)。

要[使用 Azure PowerShell 登入到 Azure 帳戶](/powershell/azure/authenticate-azureps?view=azps-2.5.0),請使用[連接-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) cmdlet。

```powershell
Connect-AzAccount
```

如果您有多個訂閱並希望指定一個訂閱,請使用[取得-Az訂閱](/powershell/module/Az.Accounts/Get-AzSubscription)cmdlet 列出它們,後跟[Set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) cmdlet:

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

運行[Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) cmdlet 將驗證是否選擇了正確的訂閱。

要確認安裝 Azure 磁碟加密 cmdlet,請使用[Get 命令](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6)cmdlet:
     
```powershell
Get-command *diskencryption*
```
有關詳細資訊,請參閱使用[Azure PowerShell 入門](/powershell/azure/get-started-azureps)。 

## <a name="enable-encryption-on-an-existing-or-running-linux-vm"></a>在現有或正在執行的 Linux VM 上啟用加密
在這個案例中，您可以使用 Resource Manager 範本、PowerShell Cmdlet 或 CLI 命令啟用加密。 若您需要虛擬機器擴充之結構描述資訊，請參閱 [Linux 擴充功能之 Azure 磁碟加密](../extensions/azure-disk-enc-linux.md)一文。

>[!IMPORTANT]
 >您必須在啟用 Azure 磁碟加密之前，先在 Azure 磁碟加密之外對以受控磁碟為基礎的 VM 執行個體建立快照集和/或備份。 可以從門戶或通過[Azure 備份](../../backup/backup-azure-vms-encryption.md)獲取託管磁碟的快照。 擁有備份可確保在加密期間發生任何非預期的失敗時，能有復原選項可供選擇。 進行備份後,Set-AzVMDiskEncrypt 擴展 cmdlet 可用於通過指定 -skipVmBackup 參數來加密託管磁碟。 Set-AzVMDisk加密擴展命令將針對基於託管磁碟的 VM 失敗,直到進行備份並指定此參數。 
>
>加密或停用加密功能，可能會導致 VM 重新啟動。 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-azure-cli"></a>使用 Azure CLI 在現有或執行中的 Linux VM 上啟用加密 

您可以通過安裝和使用[Azure CLI](/cli/azure/?view=azure-cli-latest)命令列工具在加密的 VHD 上啟用磁碟加密。 您可以在瀏覽器中將它與 [Azure Cloud Shell](../../cloud-shell/overview.md) 搭配使用，或可將它安裝在本機電腦上，並在任何 PowerShell 工作階段中使用它。 在 Azure 中的現有或正在執行的 Linux VM 上啟用加密,請使用以下 CLI 命令:

使用[az vm 加密啟用](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-show)命令在 Azure 中正在執行的虛擬機器上啟用加密。

- **加密執行中的 VM：**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **使用 KEK 加密執行中的 VM：**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > disk-encryption-keyvault 參數值的語法為完整識別碼字串：/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br>
key-encryption-key 參數值的語法為 KEK 的完整 URI：https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **驗證磁碟是否加密:** 要檢查 VM 的加密狀態,請使用[az vm 加密顯示](/cli/azure/vm/encryption#az-vm-encryption-show)命令。 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **停用加密：** 若要停用加密，請使用 [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) 命令。 只能在 Linux VM 的資料磁碟區上停用加密。

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-powershell"></a>使用 PowerShell 在現有或執行中的 Linux VM 上啟用加密
使用[Set-AzVM 磁碟加密擴展](/powershell/module/az.compute/set-azvmdiskencryptionextension)cmdlet 在 Azure 中正在執行的虛擬機器上啟用加密。 在加密磁碟之前,使用[Azure 備份](../../backup/backup-azure-vms-encryption.md)拍攝[快照](snapshot-copy-managed-disk.md)和/或備份 VM。 -skipVmBackup 參數已在 PowerShell 文本中指定,用於加密正在運行的 Linux VM。

-  **加密正在執行的 VM:** 下面的文本初始化變數並運行 Set-AzVM 磁碟加密擴展 cmdlet。 資源組、VM 和密鑰保管庫是作為先決條件創建的。 將我的虛擬電腦資源組、MySecureVM 和 MySecureVault 替換為您的值。 修改 -VolumeType 參數以指定要加密的磁碟。

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();  

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```
- **使用 KEK 加密執行中的 VM：** 如果您要加密資料磁碟而非作業系統磁碟，則可能需要新增 -VolumeType 參數。 

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

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > disk-encryption-keyvault 參數值的語法為完整識別碼字串：/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> key-encryption-key 參數值的語法為 KEK 的完整 URI：https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 
    
- **驗證磁碟是否加密:** 要檢查 VM 的加密狀態,請使用[獲取-AzVm 磁碟加密狀態](/powershell/module/az.compute/get-azvmdiskencryptionstatus)cmdlet。 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **關閉磁碟加密:** 要關閉加密,請使用禁用[AzVM 磁碟加密](/powershell/module/az.compute/disable-azvmdiskencryption)cmdlet。 只能在 Linux VM 的資料磁碟區上停用加密。
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-with-a-template"></a>使用樣本在現有或正在執行的 Linux VM 上啟用加密

可以使用[資源管理器範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)在 Azure 中現有或正在執行的 Linux VM 上啟用磁碟加密。

1. 按一下 Azure 快速入門範本上的 [部署至 Azure]****。

2. 選取訂用帳戶、資源群組、資源群組位置、參數、法律條款及合約。 按下 **「建立**」可在現有或正在執行的 VM 上啟用加密。

下表列出現有或執行中 VM 的 Resource Manager 範本參數︰

| 參數 | 描述 |
| --- | --- |
| vmName | 要執行加密作業的 VM 名稱。 |
| keyVaultName | 應上載到加密金鑰的金鑰保管庫的名稱。 可以使用 cmdlet`(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname`或 Azure`az keyvault list --resource-group "MyKeyVaultResourceGroupName"`CLI 命令 獲取它。|
| keyVaultResourceGroup | 包含金鑰保管庫的資源組的名稱。 |
|  keyEncryptionKeyURL | 用於加密加密金鑰的金鑰加密金鑰的網址。 如果您在 UseExistingKek 下拉式清單中選取 [nokek]****，此參數是選擇性的。 如果您在 UseExistingKek 下拉式清單中選取 [kek]****，您必須輸入 _keyEncryptionKeyURL_ 值。 |
| volumeType | 執行加密作業所在磁碟區的類型。 有效值為 _OS_、_Data_ 和 _All_。 
| forceUpdateTag | 每次需要強制執行作業時傳入唯一的值，例如 GUID。 |
| location | 所有資源的位置。 |

有關設定 Linux VM 磁碟加密樣本的詳細資訊,請參閱[Linux 的 Azure 磁碟加密](https://docs.microsoft.com/azure/virtual-machines/extensions/azure-disk-enc-linux)。

## <a name="use-encryptformatall-feature-for-data-disks-on-linux-vms"></a>Linux VM 上對資料磁碟使用加密格式所有功能

**EncryptFormatAll** 參數會減少加密 Linux 資料磁碟的時間。 滿足特定條件的分割區將設定格式(其當前檔案系統),然後重新安裝回命令執行之前的位置。 如果想要排除符合準則的資料磁碟，您可以在執行命令前將它取消掛接。

 運行此命令後,將格式化以前裝載的任何驅動器,加密層將在現在空驅動器的頂部啟動。 選取此選項後，也會加密連結至 VM 的暫時資源磁碟。 如果重設暫時磁碟機，則 Azure 磁碟加密解決方案會為 VM 將它重新格式化並重新加密。 資源磁碟加密後[,Microsoft Azure Linux 代理](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)將無法管理資源磁碟並啟用交換檔,但您可以手動配置交換檔。

>[!WARNING]
> VM 的資料磁碟區有所需的資料時，不應該使用 EncryptFormatAll。 您可將磁碟取消掛接，將它們排除在加密之外。 您應該先在測試 VM 上試用 EncryptFormatAll，了解功能參數和其含意，然後在生產 VM 上試用。 EncryptFormatAll 選項會將資料磁碟格式化，而其上的所有資料都將遺失。 繼續之前，確認您想要排除的磁碟已正確卸載。 </br></br>
 >如果在更新加密設置時設置此參數,則可能導致在實際加密之前重新啟動。 在這種情況下,您還需要從 fstab 檔中刪除不希望格式化的磁碟。 同樣地，您應該先將想要加密格式化的磁碟分割新增至 fstab 檔案，在起始加密作業。 

### <a name="encryptformatall-criteria"></a> EncryptFormatAll 準則
此參數會通過所有磁碟分割，只要它們符合下列**所有**準則，就會予以加密： 
- 不是根/OS/開機磁碟分割
- 尚未加密
- 不是 BEK 磁碟區
- 不是 RAID 磁碟區
- 不是 LVM 磁碟區
- 已掛接

對組成 RAID 或 LVM 磁碟區的磁碟進行加密，而非對 RAID 或 LVM 磁碟區本身進行加密。

### <a name="use-the-encryptformatall-parameter-with-azure-cli"></a> 使用 EncryptFormatAll 參數搭配 Azure CLI
使用[az vm 加密啟用](/cli/azure/vm/encryption#az-vm-encryption-enable)命令在 Azure 中正在執行的虛擬機器上啟用加密。

-  **使用 EncryptFormatAll 來加密執行中的 VM：**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --encrypt-format-all
     ```

### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a> 使用 EncryptFormatAll 參數搭配 PowerShell cmdlet
使用[「集-AzVM 磁碟加密延伸](/powershell/module/az.compute/set-azvmdiskencryptionextension)cmdlet」與加密格式所有參數。 

**使用加密格式加密正在執行的 VM 全部:** 例如,下面的腳本初始化變數,並使用加密格式All參數運行 Set-AzVMDisk加密擴展 cmdlet。 資源組、VM 和密鑰保管庫是作為先決條件創建的。 將我的虛擬電腦資源組、MySecureVM 和 MySecureVault 替換為您的值。
  
```azurepowershell
$KVRGname = 'MyKeyVaultResourceGroup';
$VMRGName = 'MyVirtualMachineResourceGroup';
$vmName = 'MySecureVM';
$KeyVaultName = 'MySecureVault';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
```


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a> 使用 EncryptFormatAll 參數搭配邏輯磁碟區管理員 (LVM) 
我們建議進行 LVM-on-crypt 設定。 在下列所有範例中，以適合您的使用案例的項目取代裝置路徑和掛接點。 以下步驟可以完成此設定：

- 新增將構成 VM 的資料磁碟。
- 格式化、掛接這些磁碟，並將其新增至 fstab 檔案。

    1. 選擇分區標準,創建跨整個驅動器的分區,然後格式化分區。 我們會 Azure 在此產生的符號連結。 使用符號連結，可避免裝置名稱變更的相關問題。 如需詳細資訊，請參閱[針對裝置名稱問題進行疑難排解](troubleshoot-device-names-problems.md)一文。
    
         ```azurepowershell-interactive
         parted /dev/disk/azure/scsi1/lun0 mklabel gpt
         parted -a opt /dev/disk/azure/scsi1/lun0 mkpart primary ext4 0% 100%
         
         mkfs -t ext4 /dev/disk/azure/scsi1/lun0-part1
         ```
    
    1. 掛接磁碟。
         
         `mount /dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint`
    
    1. 新增至 fstab。
         
        `echo "/dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint ext4 defaults,nofail 0 2" >> /etc/fstab`
    
    1. 使用 -EncryptFormatAll 執行 Set-AzVM 磁碟加密擴展電源 Shell cmdlet 以加密這些磁碟。

       ```azurepowershell-interactive
       $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
           
       Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri  -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data
       ```

    1. 在這些新的磁碟上設定 LVM。 請注意，VM 完成開機之後，加密磁碟機就會解除鎖定。 因此，LVM 掛接後續也必須延遲。


## <a name="new-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>從客戶加密 VHD 和加密金鑰建立的新 VM
在這個案例中，您可以使用 PowerShell Cmdlet 或 CLI 命令啟用加密。 

使用 Azure 磁碟加密中的說明使用相同的腳本來準備可在 Azure 中使用的預加密映射。 映像建立之後，您可以使用下一節的步驟來建立加密的 Azure VM。

* [準備已預先加密的 Linux VHD](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-linux-vhd)

>[!IMPORTANT]
 >您必須在啟用 Azure 磁碟加密之前，先在 Azure 磁碟加密之外對以受控磁碟為基礎的 VM 執行個體建立快照集和/或備份。 可以從門戶獲取託管磁碟的快照,也可以使用[Azure 備份](../../backup/backup-azure-vms-encryption.md)。 擁有備份可確保在加密期間發生任何非預期的失敗時，能有復原選項可供選擇。 進行備份後,Set-AzVMDiskEncrypt 擴展 cmdlet 可用於通過指定 -skipVmBackup 參數來加密託管磁碟。 Set-AzVMDisk加密擴展命令將針對基於託管磁碟的 VM 失敗,直到進行備份並指定此參數。 
>
> 加密或停用加密功能，可能會導致 VM 重新啟動。 



### <a name="use-azure-powershell-to-encrypt-vms-with-pre-encrypted-vhds"></a>使用 Azure PowerShell 使用預加密 VHD 對 VM 進行加密 
您可以使用 PowerShell cmdlet[集-AzVMOS Disk](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples)在加密的 VHD 上啟用磁碟加密。 下列範例會提供一些常見的參數。 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Linux -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>在新增的資料磁碟上啟用加密

您可以使用 [az vm disk attach](add-disk.md) 或[透過 Azure 入口網站](attach-disk-portal.md)，新增磁碟新增。 您必須先掛接新連結的資料磁碟，才可以加密。 您必須要求資料磁碟機加密，因為在進行加密時，將無法使用該磁碟機。 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>透過 Azure CLI 在新增的磁碟上啟用加密

 如果 VM 以前使用"全部"加密,則 --體積類型參數應保持"全部"。 全部包含作業系統與資料磁碟。 如果 VM 以前使用卷類型"OS"進行加密,則應將 --卷類型參數更改為"全部",以便同時包含操作系統和新數據磁碟。 若虛擬機器僅使用「Data」磁碟區類型加密，則可以如下所示，保留參數值為「Data」。 要準備進行加密，僅是新增及附加新資料磁碟至虛擬機器是不夠的。 啟用加密之前，新附加的磁碟必須經過格式化，並正確裝載於虛擬機器中。 在 Linux 上磁碟必須裝載於 /etc/fstab，且必須有[一致的區塊裝置名稱](troubleshoot-device-names-problems.md)。  

在啟用加密時，與 Powershell 語法不同，CLI 不需要使用者提供唯一的序列版本。 CLI 為自動產生並使用其唯一序列版本的值。

-  **加密執行中虛擬機器的資料磁碟：**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **使用 KEK 加密執行中虛擬機器的資料磁碟：**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>透過 Azure PowerShell 在新增的磁碟上啟用加密
 使用 Powershell 來加密 Linux 的新磁碟時，必須指定新的序列版本。 序列版本必須是唯一的。 下列指令碼會產生序列版本的 GUID。 在加密磁碟之前,使用[Azure 備份](../../backup/backup-azure-vms-encryption.md)拍攝[快照](snapshot-copy-managed-disk.md)和/或備份 VM。 -skipVmBackup 參數已在 PowerShell 腳本中指定,用於加密新添加的數據磁碟。
 

-  **加密正在執行的 VM 的資料卷:** 下面的文本初始化變數並運行 Set-AzVM 磁碟加密擴展 cmdlet。 資源群組、VM 和金鑰保存庫應該已經建立為必要條件。 將我的虛擬電腦資源組、MySecureVM 和 MySecureVault 替換為您的值。 VolumeType 參數可使用之值有 All、OS 及 Data。 如果 VM 以前使用"OS" 或「All」的卷類型進行加密,則應將 -VolumeType 參數更改為「全部」,以便將作業系統和新資料磁碟包括在內。

      ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
      ```
- **使用 KEK 加密執行中虛擬機器的資料磁碟：**-VolumeType 參數可使用之值有 All、OS 及 Data。 若虛擬機器先前以「作業系統」或「全部」磁碟區類型加密，則應變更 --VolumeType 參數為「所有」，確保作業系統和新的資料磁碟都包含在其中。

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

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > 磁碟加密金鑰保管參數值的語法是完整的標識字串:/訂閱/[訂閱-id-guid]/資源組/[KV資源組名稱]/提供程式/Microsoft.KeyVault/vault/[密鑰庫名稱]</br> key-encryption-key 參數值的語法為 KEK 的完整 URI：https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 


## <a name="disable-encryption-for-linux-vms"></a>停用 Linux VM 的加密
您可以使用 Azure PowerShell、Azure CLI 或 Resource Manager 範本來停用加密。 

>[!IMPORTANT]
>只有資料磁碟區支援在 Linux VM 上透過 Azure 磁碟加密停用加密。 如果 OS 磁碟區已加密，則不支援在資料或 OS 磁碟區上停用加密。  

- **使用 Azure PowerShell 停用磁碟加密:** 要關閉加密,請使用禁用[AzVM 磁碟加密](/powershell/module/az.compute/disable-azvmdiskencryption)cmdlet。 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [-VolumeType {ALL, DATA, OS}]
     ```

- **透過 Azure CLI 停用加密：** 若要停用加密，請使用 [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) 命令。 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **使用資源管理員樣本關閉加密:** 使用正在執行的 Linux VM 樣本[上的禁用加密](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad)來禁用加密。
     1. 按一下 [ **部署至 Azure**]。
     2. 選取訂用帳戶、資源群組、位置、VM、法律條款及合約。

## <a name="unsupported-scenarios"></a>不支援的情節

Azure 磁碟加密不適用於以下 Linux 方案、功能和技術:

- 加密通過經典 VM 創建方法創建的基本層 VM 或 VM。
- 在作業系統驅動器加密時禁用作業系統驅動器或 Linux VM 的資料驅動器上的加密。
- 加密 Linux 虛擬機器擴展集的作業系統驅動器。
- 加密 Linux VM 上的自定義映射。
- 與本地金鑰管理系統整合。
- Azure 檔案 (共用檔案系統)。
- 網路檔案系統 (NFS)。
- 動態磁碟區。
- 臨時操作系統磁碟。
- 共用/分散式檔案系統的加密,如(但不限於):DFS、GFS、DRDB 和 CephFS。
- 將加密的 VM 放在其他訂閱。
- 內核崩潰轉儲(轉儲)。
- Lsv2 系列 VM(參見[:Lsv2 系列](../lsv2-series.md))

## <a name="next-steps"></a>後續步驟

- [Azure 磁碟加密概觀](disk-encryption-overview.md)
- [Azure 磁碟加密範例指令碼](disk-encryption-sample-scripts.md)
- [Azure 磁碟加密的疑難排解](disk-encryption-troubleshooting.md)
