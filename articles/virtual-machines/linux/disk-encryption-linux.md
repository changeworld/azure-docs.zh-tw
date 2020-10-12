---
title: Linux VM 上的 Azure 磁碟加密案例
description: 本文會針對各種案例提供可為 Linux VM 啟用 Microsoft Azure 磁碟加密的指示。
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: b7d19d782e9cb29cfc917293f084b78830db89bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88797610"
---
# <a name="azure-disk-encryption-scenarios-on-linux-vms"></a>Linux VM 上的 Azure 磁碟加密案例


適用於 Linux 虛擬機器 (VM) 的 Azure 磁碟加密會使用 Linux 的 DM-Crypt 功能，提供 OS 磁碟和資料磁碟的完整磁碟加密。 此外，使用 EncryptFormatAll 功能時，其也會提供暫存磁碟的加密。

Azure 磁碟加密會與 [Azure Key Vault](disk-encryption-key-vault.md) 整合，協助您控制及管理磁碟加密金鑰與祕密。 如需服務的概觀，請參閱[適用於 Linux VM 的 Azure 磁碟加密](disk-encryption-overview.md)。

您只能將磁碟加密套用到[受支援 VM 大小和作業系統](disk-encryption-overview.md#supported-vms-and-operating-systems)的虛擬機器。 您也必須符合下列必要條件：

- [VM 的其他需求](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [網路需求](disk-encryption-overview.md#networking-requirements)
- [加密金鑰儲存體需求](disk-encryption-overview.md#encryption-key-storage-requirements)

不論是何種情況，您都應該先[建立快照集](snapshot-copy-managed-disk.md)和/或建立備份再將磁碟加密。 擁有備份可確保在加密期間發生任何非預期的失敗時，能有復原選項可供選擇。 具有受控磁碟的 VM 需要有備份，才能進行加密。 在建立備份後，您可以使用 [Set-AzVMDiskEncryptionExtension Cmdlet](/powershell/module/az.compute/set-azvmdiskencryptionextension) 並指定 -skipVmBackup 參數來加密受控磁碟。 如需如何備份和還原已加密 VM 的詳細資訊，請參閱 [Azure 備份](../../backup/backup-azure-vms-encryption.md)一文。 

>[!WARNING]
> - 如果您先前曾使用 Azure 磁碟加密搭配 Azure AD 來加密 VM，則必須繼續使用此選項來加密您的 VM。 如需詳細資料，請參閱[將 Azure 磁碟加密與 Azure AD 搭配使用 (舊版)](disk-encryption-overview-aad.md)。 
>
> - 在加密 Linux OS 磁碟區時，請將 VM 視為無法使用。 強烈建議您在加密進行當下避免進行 SSH 登入，以免發生問題而封鎖加密過程中需要存取的任何已開啟檔案。 若要檢查進度，請使用 [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) PowerShell Cmdlet 或 [vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) CLI 命令。 對於 30GB OS 磁碟區，此過程可能需要幾個小時，再加上額外的時間來進行加密資料磁碟區。 除非使用加密格式所有選項，否則資料磁碟區加密時間將與資料磁碟區的大小和數量成正比。 
> - 只有資料磁碟區支援在 Linux VM 上停用加密。 如果 OS 磁碟區已加密，則不支援在資料或 OS 磁碟區上停用加密。  

## <a name="install-tools-and-connect-to-azure"></a>安裝工具並連線至 Azure

Azure 磁碟加密可以透過 [Azure CLI](/cli/azure) 和 [Azure PowerShell](/powershell/azure/new-azureps-module-az) 來加以啟用及管理。 若要這樣做，您必須在本機安裝這些工具，並連接到您的 Azure 訂用帳戶。

### <a name="azure-cli"></a>Azure CLI

[Azure CLI 2.0](/cli/azure) 是命令列工具，可用於管理 Azure 資源。 CLI 的設計是要讓您能夠彈性地查詢資料、以非封鎖處理序的形式支援長時間執行作業，並輕鬆地撰寫指令碼。 您可以遵循[安裝 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 中的步驟，將其安裝到本機。

 

若要[使用 Azure CLI 登入您的 Azure 帳戶](/cli/azure/authenticate-azure-cli)，請使用 [az login](/cli/azure/reference-index?view=azure-cli-latest#az-login) 命令。

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
[Azure PowerShell az 模組](/powershell/azure/new-azureps-module-az)提供了一組 Cmdlet，其會使用 [Azure Resource Manager](../../azure-resource-manager/management/overview.md) 模型來管理 Azure 資源。 您可以在瀏覽器中搭配 [Azure Cloud Shell](../../cloud-shell/overview.md) 來使用此模組，也可以使用[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)中的指示將其安裝到本機電腦上。 

如果您已將它安裝在本機上，請確定您是使用最新版的 Azure PowerShell SDK 版本來設定 Azure 磁碟加密。 下載最新版的 [Azure PowerShell 版本](https://github.com/Azure/azure-powershell/releases)。

若要[使用 Azure PowerShell 登入您的 Azure 帳戶](/powershell/azure/authenticate-azureps?view=azps-2.5.0)，請使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) Cmdlet。

```powershell
Connect-AzAccount
```

如果您有多個訂用帳戶，而且想要指定其中一個，請使用 [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) Cmdlet 來列出這些訂用帳戶，後面接著 [Set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) Cmdlet：

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

執行 [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) Cmdlet 能確認所選取的訂用帳戶是否正確。

若要確認是否已安裝 Azure 磁碟加密 Cmdlet，請使用 [Get-command](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6) Cmdlet：
     
```powershell
Get-command *diskencryption*
```
如需詳細資訊，請參閱[開始使用 Azure PowerShell](/powershell/azure/get-started-azureps)。 

## <a name="enable-encryption-on-an-existing-or-running-linux-vm"></a>在現有或執行中的 Linux VM 上啟用加密
在這個案例中，您可以使用 Resource Manager 範本、PowerShell Cmdlet 或 CLI 命令啟用加密。 若您需要虛擬機器擴充之結構描述資訊，請參閱 [Linux 擴充功能之 Azure 磁碟加密](../extensions/azure-disk-enc-linux.md)一文。

>[!IMPORTANT]
 >您必須在啟用 Azure 磁碟加密之前，先在 Azure 磁碟加密之外對以受控磁碟為基礎的 VM 執行個體建立快照集和/或備份。 您可以從入口網站建立受控磁碟的快照集，也可以透過 [Azure 備份](../../backup/backup-azure-vms-encryption.md)來建立。 擁有備份可確保在加密期間發生任何非預期的失敗時，能有復原選項可供選擇。 在建立備份後，您就可以使用 Set-AzVMDiskEncryptionExtension Cmdlet 並指定 -skipVmBackup 參數來加密受控磁碟。 在建立好備份並指定了這個參數之前，對受控磁碟型 VM 執行 Set-AzVMDiskEncryptionExtension 命令都將會失敗。 
>
>加密或停用加密功能，可能會導致 VM 重新啟動。 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-azure-cli"></a>使用 Azure CLI 在現有或執行中的 Linux VM 上啟用加密 

您可以安裝並使用 [Azure CLI](/cli/azure/?view=azure-cli-latest) 命令列工具，在加密的 VHD 上啟用磁碟加密。 您可以在瀏覽器中將它與 [Azure Cloud Shell](../../cloud-shell/overview.md) 搭配使用，或可將它安裝在本機電腦上，並在任何 PowerShell 工作階段中使用它。 若要在 Azure 中現有或執行中的 Linux VM 上啟用加密，請使用下列 CLI 命令：

在 Azure 中使用 [az vm encryption enable](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-show) 命令以在執行中的虛擬機器上啟用加密。

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
key-encryption-key 參數值的語法為 KEK 的完整 URI： https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **確認磁碟已加密：** 若要檢查 VM 的加密狀態，請使用 [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) 命令。 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **停用加密：** 若要停用加密，請使用 [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) 命令。 只能在 Linux VM 的資料磁碟區上停用加密。

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "data"
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-powershell"></a>使用 PowerShell 在現有或執行中的 Linux VM 上啟用加密
在 Azure 中使用 [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) Cmdlet 以在執行中的虛擬機器上啟用加密。 請先建立[快照集](snapshot-copy-managed-disk.md)和/或使用 [Azure 備份](../../backup/backup-azure-vms-encryption.md)來備份 VM，再將磁碟加密。 PowerShell 指令碼中已指定 -skipVmBackup 參數以加密執行中的 Linux VM。

-  **加密執行中的 VM：** 下面的指令碼會初始化您的變數並執行 Set-AzVMDiskEncryptionExtension Cmdlet。 資源群組、VM 和金鑰保存庫已建立為必要條件。 將 MyVirtualMachineResourceGroup、MySecureVM 和 MySecureVault 取代為您的值。 修改 -VolumeType 參數，以指定您要加密的磁碟。

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
    > disk-encryption-keyvault 參數值的語法為完整識別碼字串：/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> key-encryption-key 參數值的語法為 KEK 的完整 URI： https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 
    
- **確認磁碟已加密：** 若要檢查 VM 的加密狀態，請使用 [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) Cmdlet。 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **停用磁碟加密**：若要停用加密，請使用 [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) Cmdlet。 只能在 Linux VM 的資料磁碟區上停用加密。
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-with-a-template"></a>透過範本在現有或執行中的 Linux VM 上啟用加密

您可以使用 [Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)，在 Azure 中現有或執行中的 Linux VM 上啟用磁碟加密。

1. 按一下 Azure 快速入門範本上的 [部署至 Azure]。

2. 選取訂用帳戶、資源群組、資源群組位置、參數、法律條款及合約。 按一下 [建立]，以在現有或執行中的 VM 上啟用加密。

下表列出現有或執行中 VM 的 Resource Manager 範本參數︰

| 參數 | 描述 |
| --- | --- |
| vmName | 要執行加密作業的 VM 名稱。 |
| keyVaultName | 應作為加密金鑰上傳目的地的金鑰保存庫名稱。 您可以使用 Cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` 或 Azure CLI 命令 `az keyvault list --resource-group "MyKeyVaultResourceGroupName"` 來取得。|
| keyVaultResourceGroup | 包含金鑰保存庫的資源群組名稱。 |
|  keyEncryptionKeyURL | 用來加密加密金鑰的金鑰加密金鑰 URL。 如果您在 UseExistingKek 下拉式清單中選取 [nokek]，此參數是選擇性的。 如果您在 UseExistingKek 下拉式清單中選取 [kek]，您必須輸入 _keyEncryptionKeyURL_ 值。 |
| volumeType | 執行加密作業所在磁碟區的類型。 有效值為 _OS_、_Data_ 和 _All_。 
| forceUpdateTag | 每次需要強制執行作業時傳入唯一的值，例如 GUID。 |
| location | 所有資源的位置。 |

如需如何設定 Linux VM 磁碟加密範本的詳細資訊，請參閱[適用於 Linux 的 Azure 磁碟加密](../extensions/azure-disk-enc-linux.md)。

## <a name="use-encryptformatall-feature-for-data-disks-on-linux-vms"></a>將 EncryptFormatAll 功能使用於 Linux VM 上的資料磁碟

**EncryptFormatAll** 參數會減少加密 Linux 資料磁碟的時間。 符合特定準則的分割區 (連同其目前的檔案系統) 將會格式化，然後重新掛接回執行命令之前的所在位置。 如果想要排除符合準則的資料磁碟，您可以在執行命令前將它取消掛接。

 執行此命令之後，先前掛接的任何磁碟機都會格式化，而加密層則會在現在的空白磁碟機之上啟動。 選取此選項後，也會加密連結至 VM 的暫存磁碟。 如果重設暫存磁碟，則 Azure 磁碟加密解決方案會為 VM 將其重新格式化並重新加密。 當資源磁碟進行加密後，[Microsoft Azure Linux 代理程式](../extensions/agent-linux.md)就無法管理資源磁碟和啟用分頁檔，但您可以手動設定分頁檔。

>[!WARNING]
> VM 的資料磁碟區有所需的資料時，不應該使用 EncryptFormatAll。 您可將磁碟取消掛接，將它們排除在加密之外。 您應該先在測試 VM 上試用 EncryptFormatAll，了解功能參數和其含意，然後在生產 VM 上試用。 EncryptFormatAll 選項會將資料磁碟格式化，而其上的所有資料都將遺失。 繼續之前，確認您想要排除的磁碟已正確卸載。 </br></br>
 >如果您在更新加密設定時設定此參數，則可能導致在實際加密前重新開機。 在此情況下，您也可以從 fstab 檔案中移除您不想格式化的磁碟。 同樣地，您應該先將想要加密格式化的磁碟分割新增至 fstab 檔案，在起始加密作業。 

### <a name="encryptformatall-criteria"></a>EncryptFormatAll 準則
此參數會通過所有磁碟分割，只要它們符合下列**所有**準則，就會予以加密：
- 不是根/OS/開機磁碟分割
- 尚未加密
- 不是 BEK 磁碟區
- 不是 RAID 磁碟區
- 不是 LVM 磁碟區
- 已掛接

對組成 RAID 或 LVM 磁碟區的磁碟進行加密，而非對 RAID 或 LVM 磁碟區本身進行加密。

### <a name="use-the-encryptformatall-parameter-with-azure-cli"></a>使用 EncryptFormatAll 參數搭配 Azure CLI
在 Azure 中使用 [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) 命令以在執行中的虛擬機器上啟用加密。

-  **使用 EncryptFormatAll 來加密執行中的 VM：**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "data" --encrypt-format-all
     ```

### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a>使用 EncryptFormatAll 參數搭配 PowerShell Cmdlet
使用 [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) Cmdlet 搭配 EncryptFormatAll 參數。 

**使用 EncryptFormatAll 來加密執行中的 VM：** 例如，下面的指令碼會初始化您的變數並使用 EncryptFormatAll 參數執行 Set-AzVMDiskEncryptionExtension Cmdlet。 資源群組、VM 和金鑰保存庫已建立為必要條件。 將 MyVirtualMachineResourceGroup、MySecureVM 和 MySecureVault 取代為您的值。
  
```azurepowershell
$KVRGname = 'MyKeyVaultResourceGroup';
$VMRGName = 'MyVirtualMachineResourceGroup';
$vmName = 'MySecureVM';
$KeyVaultName = 'MySecureVault';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "data" -EncryptFormatAll
```


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a>使用 EncryptFormatAll 參數搭配邏輯磁碟區管理員 (LVM) 
我們建議進行 LVM-on-crypt 設定。 在下列所有範例中，以適合您的使用案例的項目取代裝置路徑和掛接點。 以下步驟可以完成此設定：

1.  新增將構成 VM 的資料磁碟。

1. 格式化、掛接這些磁碟，並將其新增至 fstab 檔案。

1. 選擇分割區標準、建立跨越整個磁碟機的分割區，然後將分割區格式化。 我們會 Azure 在此產生的符號連結。 使用符號連結，可避免裝置名稱變更的相關問題。 如需詳細資訊，請參閱[針對裝置名稱問題進行疑難排解](../troubleshooting/troubleshoot-device-names-problems.md)一文。
    
    ```bash
    parted /dev/disk/azure/scsi1/lun0 mklabel gpt
    parted -a opt /dev/disk/azure/scsi1/lun0 mkpart primary ext4 0% 100%
    
    mkfs -t ext4 /dev/disk/azure/scsi1/lun0-part1
    ```

1. 掛接磁碟：

    ```bash
    mount /dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint
    ````
    
    新增至 fstab 檔案：

    ```bash
    echo "/dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint ext4 defaults,nofail 0 2" >> /etc/fstab
    ```
    
1. 執行 Azure PowerShell [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-3.8.0) Cmdlet 搭配 -EncryptFormatAll 來加密這些磁碟。

    ```azurepowershell-interactive
    $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
    
    Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data
    ```

    如果您想要使用金鑰加密金鑰 (KEK)，請分別將 KEK 的 URI 和金鑰保存庫的 ResourceID 傳遞至 -KeyEncryptionKeyUrl 和 -KeyEncryptionKeyVaultId 參數：

    ```azurepowershell-interactive
    $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
    $KEKKeyVault = Get-AzKeyVault -VaultName "MyKEKVault" -ResourceGroupName "MySecureGroup"
    $KEK = Get-AzKeyVaultKey -VaultName "myKEKVault" -KeyName "myKEKName"
    
    Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data -KeyEncryptionKeyUrl $$KEK.id -KeyEncryptionKeyVaultId $KEKKeyVault.ResourceId
    ```

1. 在這些新的磁碟上設定 LVM。 請注意，VM 完成開機之後，加密磁碟機就會解除鎖定。 因此，LVM 掛接後續也必須延遲。

## <a name="new-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>透過客戶加密的 VHD 和加密金鑰所建立的新 VM
在這個案例中，您可以使用 PowerShell Cmdlet 或 CLI 命令啟用加密。 

使用 Azure 磁碟加密相同指令碼中的指示來準備可用於 Azure 的預先加密映像。 映像建立之後，您可以使用下一節的步驟來建立加密的 Azure VM。

* [準備已預先加密的 Linux VHD](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-linux-vhd)

>[!IMPORTANT]
 >您必須在啟用 Azure 磁碟加密之前，先在 Azure 磁碟加密之外對以受控磁碟為基礎的 VM 執行個體建立快照集和/或備份。 您可以從入口網站建立受控磁碟的快照集，也可以使用 [Azure 備份](../../backup/backup-azure-vms-encryption.md)來建立。 擁有備份可確保在加密期間發生任何非預期的失敗時，能有復原選項可供選擇。 在建立備份後，您就可以使用 Set-AzVMDiskEncryptionExtension Cmdlet 並指定 -skipVmBackup 參數來加密受控磁碟。 在建立好備份並指定了這個參數之前，對受控磁碟型 VM 執行 Set-AzVMDiskEncryptionExtension 命令都將會失敗。 
>
> 加密或停用加密功能，可能會導致 VM 重新啟動。 



### <a name="use-azure-powershell-to-encrypt-vms-with-pre-encrypted-vhds"></a>使用 Azure PowerShell 來加密具有預先加密 VHD 的 VM 
您可以使用 PowerShell Cmdlet [Set-AzVMOSDisk](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples) 在加密的 VHD 上啟用磁碟加密。 下列範例會提供一些常見的參數。 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Linux -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>在新增的資料磁碟上啟用加密

您可以使用 [az vm disk attach](add-disk.md) 或[透過 Azure 入口網站](attach-disk-portal.md)，新增磁碟新增。 您必須先掛接新連結的資料磁碟，才可以加密。 您必須要求資料磁碟機加密，因為在進行加密時，將無法使用該磁碟機。 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>透過 Azure CLI 在新增的磁碟上啟用加密

 若虛擬機器先前以「All」加密，則 --磁碟區類型參數也應該保留為「All」。 全部包含作業系統與資料磁碟。 若虛擬機器先前以「OS」磁碟類別加密，則應變更 --磁碟區類型參數為「All」，確保作業系統和新的資料磁碟都包含在其中。 若虛擬機器僅使用「Data」磁碟區類型加密，則可以如下所示，保留參數值為「Data」。 要準備進行加密，僅是新增及附加新資料磁碟至虛擬機器是不夠的。 啟用加密之前，新附加的磁碟必須經過格式化，並正確裝載於虛擬機器中。 在 Linux 上磁碟必須裝載於 /etc/fstab，且必須有[一致的區塊裝置名稱](../troubleshooting/troubleshoot-device-names-problems.md)。  

在啟用加密時，與 PowerShell 語法不同，CLI 不需要使用者提供唯一的序列版本。 CLI 為自動產生並使用其唯一序列版本的值。

-  **加密執行中虛擬機器的資料磁碟：**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **使用 KEK 加密執行中虛擬機器的資料磁碟：**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>透過 Azure PowerShell 在新增的磁碟上啟用加密
 使用 PowerShell 來加密 Linux 的新磁碟時，必須指定新的序列版本。 序列版本必須是唯一的。 下列指令碼會產生序列版本的 GUID。 請先建立[快照集](snapshot-copy-managed-disk.md)和/或使用 [Azure 備份](../../backup/backup-azure-vms-encryption.md)來備份 VM，再將磁碟加密。 PowerShell 指令碼中已指定 -skipVmBackup 參數以加密新增的資料磁碟。
 

-  **加密執行中虛擬機器的資料磁碟：** 下面的指令碼會初始化您的變數並執行 Set-AzVMDiskEncryptionExtension Cmdlet。 資源群組、VM 和金鑰保存庫應該已經建立為必要條件。 將 MyVirtualMachineResourceGroup、MySecureVM 和 MySecureVault 取代為您的值。 VolumeType 參數可使用之值有 All、OS 及 Data。 若虛擬機器先前以「OS」或「All」磁碟區類型加密，則應變更 --VolumeType 參數為「All」，確保作業系統和新的資料磁碟都包含在其中。

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
- **使用 KEK 加密執行中虛擬機器的資料磁碟：** VolumeType 參數可使用之值有 All、OS 及 Data。 若虛擬機器先前以「作業系統」或「全部」磁碟區類型加密，則應變更 --VolumeType 參數為「所有」，確保作業系統和新的資料磁碟都包含在其中。

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
    > disk-encryption-keyvault 參數值的語法為完整識別碼字串：/subscriptions/[subscription-id-guid]/resourceGroups/[KVresource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> key-encryption-key 參數值的語法為 KEK 的完整 URI： https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 


## <a name="disable-encryption-for-linux-vms"></a>停用 Linux VM 的加密
[!INCLUDE [disk-encryption-disable-encryption-cli](../../../includes/disk-encryption-disable-cli.md)]

## <a name="unsupported-scenarios"></a>不支援的情節

Azure 磁碟加密不適用於下列 Linux 案例、功能和技術：

- 將基本層 VM 或透過傳統 VM 建立方法所建立的 VM 加密。
- 在加密 OS 磁碟機時，於 OS 磁碟機或 Linux VM 的資料磁碟機上停用加密。
- 加密 Linux 虛擬機器擴展集的 OS 磁片磁碟機。
- 加密 Linux VM 上的自訂映像。
- 與內部部署金鑰管理系統整合。
- Azure 檔案 (共用檔案系統)。
- 網路檔案系統 (NFS)。
- 動態磁碟區。
- 暫時性 OS 磁碟。
- 加密共用/分散式檔案系統，例如 (但不限於)：DFS、GFS、DRDB 和 CephFS。
- 將已加密的 VM 移至另一個訂用帳戶或區域。
- 建立已加密 VM 的映射或快照，並使用它來部署額外的 Vm。
- 核心損毀傾印 (kdump)。
- Oracle ACFS (ASM 叢集檔案系統)。
- Gen2 VM (請參閱：[Azure 上第 2 代 VM 的支援](generation-2.md#generation-1-vs-generation-2-capabilities)。
- Lsv2 系列 Vm 的 NVMe 磁片 (請參閱： [Lsv2 系列](../lsv2-series.md)) 。
- 具有「巢狀掛接點」的 VM；也就是在單一路徑中有多個掛接點 (例如 "/1stmountpoint/data/2stmountpoint")。
- 將資料磁片磁碟機掛接在 OS 資料夾上方的 VM。
- 具有寫入加速器磁片的 M 系列 Vm。
- 將 ADE 套用至 VM 時，會使用 [伺服器端加密搭配客戶管理的金鑰](disk-encryption.md) 來加密 (SSE + CMK) 的磁片。 將 SSE + CMK 套用至以 ADE 加密的 VM 上的資料磁片也是不支援的案例。
- 您可以使用客戶管理的金鑰， **將已加密** 的 VM，或使用 ade 加密的 VM 遷移至 [伺服器端加密](disk-encryption.md)。

## <a name="next-steps"></a>後續步驟

- [Azure 磁碟加密概觀](disk-encryption-overview.md)
- [Azure 磁碟加密範例指令碼](disk-encryption-sample-scripts.md)
- [Azure 磁碟加密的疑難排解](disk-encryption-troubleshooting.md)
