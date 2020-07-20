---
title: 確認 Linux 加密狀態 - Azure 磁碟加密
description: 本文提供從平台和 OS 層級驗證加密狀態的指示。
author: kailashmsft
ms.service: security
ms.topic: article
ms.author: kaib
ms.date: 03/11/2020
ms.custom: seodec18
ms.openlocfilehash: e2916a71f167c415f6bf1dde8ff82a38b0e0557c
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873993"
---
# <a name="verify-encryption-status-for-linux"></a>驗證 Linux 加密狀態 

本文的範圍為使用不同方法來驗證虛擬機器的加密狀態：Azure 入口網站、PowerShell、Azure CLI 或虛擬機器 (VM) 的作業系統。 

您可以透過下列其中一種方式，在加密期間或之後驗證加密狀態：

- 檢查連接至特定 VM 的磁碟。 
- 查詢每個磁碟上的加密設定，不論該磁碟是已連接或未連結。

此案例適用於 Azure 磁碟加密的雙重傳遞和單一傳遞延伸模組。 Linux 散發套件是唯一適合此案例的環境。

>[!NOTE] 
>我們會在本文中使用到變數。 請視需要將值取代。

## <a name="portal"></a>入口網站

在 Azure 入口網站中的 [延伸模組] 區段內，選取清單中的 Azure 磁碟加密延伸模組。 **狀態訊息**的資訊會指出目前的加密狀態：

![反白顯示入口網站檢查的狀態、版本及狀態訊息](./media/disk-encryption/verify-encryption-linux/portal-check-001.png)

在延伸模組清單中，您會看到對應的 Azure 磁碟加密延伸模組版本。 0\.x 版本對應到 Azure 磁碟加密雙重傳遞，而 1.x 版本對應到 Azure 磁碟加密單一傳遞。

選取延伸模組，然後選取 [檢視詳細狀態]，即可取得更多詳細資料。 加密程序的詳細狀態會以 JSON 格式顯示。

![反白顯示「查看詳細狀態」連結的入口網站檢查](./media/disk-encryption/verify-encryption-linux/portal-check-002.png)

![JSON 格式的詳細狀態](./media/disk-encryption/verify-encryption-linux/portal-check-003.png)

另一種驗證加密狀態的方法，就是查看**磁碟設定**區段。

![OS 磁碟和資料磁碟的加密狀態](./media/disk-encryption/verify-encryption-linux/portal-check-004.png)

>[!NOTE] 
> 此狀態表示磁碟已在加密設定加上戳記，而非實際在 OS 層級加密。
>
> 依據設計，系統會先將磁碟加上戳記並於稍後加密。 如果加密程序失敗，磁碟最後可能會加上戳記但不會加密。 
>
> 若要確認磁碟是否確實加密，您可以在 OS 層級上再次檢查每個磁碟是否加密。

## <a name="powershell"></a>PowerShell

您可以使用下列 PowerShell 命令來驗證已加密 VM 的 [一般] 加密狀態：

```azurepowershell
   $VMNAME="VMNAME"
   $RGNAME="RGNAME"
   Get-AzVmDiskEncryptionStatus -ResourceGroupName  ${RGNAME} -VMName ${VMNAME}
```
![PowerShell 中的一般加密狀態](./media/disk-encryption/verify-encryption-linux/verify-status-ps-01.png)

您可以使用下列 PowerShell 命令從每個磁碟擷取加密設定。

### <a name="single-pass"></a>單一傳遞
在單一傳遞中，加密設定會在每個磁碟 (OS 和資料) 上加上戳記。 您可以在單一傳遞中擷取 OS 磁碟的加密設定，如下所示：

``` powershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}  
 $Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
 Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
 Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
 Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
 Write-Host "============================================================================================================================================================="
```
![OS 磁碟的加密設定](./media/disk-encryption/verify-encryption-linux/verify-os-single-ps-001.png)

如果磁碟沒有已加上戳記的加密設定，則輸出將為空白：

![空白輸出](./media/disk-encryption/verify-encryption-linux/os-encryption-settings-2.png)

使用下列命令以擷取資料磁碟的加密設定：

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}
 clear
 foreach ($i in $VM.StorageProfile.DataDisks|ForEach-Object{$_.Name})
 {
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Checking Disk:" $i
 $Disk=(Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $i)
 Write-Host "Encryption Enable: " $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Encryption KeyEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl;
 Write-Host "Encryption DiskEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl;
 Write-Host "============================================================================================================================================================="
 }
```
![資料磁碟的加密設定](./media/disk-encryption/verify-encryption-linux/verify-data-single-ps-001.png)

### <a name="dual-pass"></a>雙重傳遞
在雙重傳遞中，加密設定不會在每個個別磁碟加上戳記，而是在 VM 模型中加上戳記。

若要確認加密設定已在雙重傳遞中加上戳記，請使用下列命令：

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$vm = Get-AzVm -ResourceGroupName ${RGNAME} -Name ${VMNAME};
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
clear
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
![雙重傳遞中的加密設定](./media/disk-encryption/verify-encryption-linux/verify-dual-ps-001.png)

### <a name="unattached-disks"></a>未連接的磁碟

檢查未連接至 VM 磁碟的加密設定。

### <a name="managed-disks"></a>受控磁碟
```powershell
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName ${TARGETDISKNAME}
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
## <a name="azure-cli"></a>Azure CLI

您可以使用下列 Azure CLI 命令來驗證已加密 VM 的 [一般] 加密狀態：

```bash
VMNAME="VMNAME"
RGNAME="RGNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"
```
![Azure CLI 的一般加密狀態 ](./media/disk-encryption/verify-encryption-linux/verify-gen-cli.png)

### <a name="single-pass"></a>單一傳遞
您可以使用下列 Azure CLI 命令來驗證每個磁碟的加密狀態：

```bash
az vm encryption show -g ${RGNAME} -n ${VMNAME} --query "disks[*].[name, statuses[*].displayStatus]"  -o table
```

![資料加密設定](./media/disk-encryption/verify-encryption-linux/data-encryption-settings-2.png)

>[!IMPORTANT]
> 如果磁碟沒有加上戳記的加密設定，您會看到**磁碟未加密**的文字。

使用下列命令以取得詳細的狀態和加密設定。

OS 磁碟：

```bash
RGNAME="RGNAME"
VMNAME="VNAME"

disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![OS 磁碟的詳細狀態和加密設定](./media/disk-encryption/verify-encryption-linux/os-single-cli.png)

資料磁碟：

```bash
RGNAME="RGNAME"
VMNAME="VMNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"

for disk in `az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks[].name -o tsv`; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![資料磁碟的詳細狀態和加密設定](./media/disk-encryption/verify-encryption-linux/data-single-cli.png)

### <a name="dual-pass"></a>雙重傳遞

``` bash
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} -o table
```

![透過 Azure CLI 進行雙重傳遞的一般加密設定](./media/disk-encryption/verify-encryption-linux/verify-gen-dual-cli.png)

您也可以在 OS 磁碟的 VM 模型儲存體設定檔上檢查加密設定：

```bash
disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![透過 Azure CLI 進行雙重傳遞的 VM 設定檔](./media/disk-encryption/verify-encryption-linux/verify-vm-profile-dual-cli.png)

### <a name="unattached-disks"></a>未連接的磁碟

檢查未連接至 VM 磁碟的加密設定。

### <a name="managed-disks"></a>受控磁碟

```bash
RGNAME="RGNAME"
TARGETDISKNAME="DISKNAME"
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
```
### <a name="unmanaged-disks"></a>非受控磁碟

非受控磁碟是 VHD 檔案，會以分頁 Blob 的形式儲存在 Azure 儲存體帳戶中。

若要取得特定磁碟的詳細資料，您必須提供：

- 包含該磁碟的儲存體帳戶識別碼。
- 特定儲存體帳戶的連接字串。
- 儲存該磁碟的容器名稱。
- 磁碟名稱。

此命令會列出所有儲存體帳戶的所有識別碼：

```bash
az storage account list --query [].[id] -o tsv
```
儲存體帳戶識別碼會以下列形式列出：

/subscriptions/\<subscription id>/resourceGroups/\<resource group name>/providers/Microsoft.Storage/storageAccounts/\<storage account name>

選取適當的識別碼，並將其儲存在變數上：
```bash
id="/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"
```

此命令會取得一個特定儲存體帳戶的連接字串，並將其儲存在變數上：

```bash
ConnectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
```

下列命令會列出儲存體帳戶底下的所有容器：
```bash
az storage container list --connection-string $ConnectionString --query [].[name] -o tsv
```
用於磁碟的容器通常會命名為「VHD」。

將容器名稱儲存在變數上： 
```bash
ContainerName="name of the container"
```

使用此命令來列出特定容器上的所有 Blob：
```bash 
az storage blob list -c ${ContainerName} --connection-string $ConnectionString --query [].[name] -o tsv
```
選擇您想要查詢的磁碟，並將其名稱儲存在變數上：
```bash
DiskName="diskname.vhd"
```
查詢磁碟加密設定：
```bash
az storage blob show -c ${ContainerName} --connection-string ${ConnectionString} -n ${DiskName} --query metadata.DiskEncryptionSettings
```

## <a name="operating-system"></a>作業系統
驗證資料磁碟分割區是否已加密 (而且 OS 磁碟未加密)。

當進行分割或磁碟加密時，它會顯示為 **crypt** 類型。 磁碟未加密時，則會顯示為 **part/disk** 類型。

``` bash
lsblk
```

![磁碟分割的 OS crypt 層](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer.png)

您可以使用下列 **lsblk** 變數來取得更多詳細資料。 

您會看到延伸模組所裝載的 **crypt** 類型層。 下列範例顯示邏輯磁碟區和具有 **crypto\_LUKS FSTYPE** 的一般磁碟。

```bash
lsblk -o NAME,TYPE,FSTYPE,LABEL,SIZE,RO,MOUNTPOINT
```
![邏輯磁碟區和一般磁碟的 OS crypt 層](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer-2.png)

另一個步驟是，您可以驗證資料磁碟是否已載入任何金鑰：

``` bash
cryptsetup luksDump /dev/VGNAME/LVNAME
```

``` bash
cryptsetup luksDump /dev/sdd1
```

而且您可以檢查哪些 **dm** 裝置會列為 **crypt**：

```bash
dmsetup ls --target crypt
```

## <a name="next-steps"></a>後續步驟

- [Azure 磁碟加密的疑難排解](disk-encryption-troubleshooting.md)
