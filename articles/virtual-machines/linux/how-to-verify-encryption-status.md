---
title: 如何驗證 Linux 的加密狀態
description: 本文提供有關從平臺和作業系統級別驗證加密狀態的說明。
author: kailashmsft
ms.service: security
ms.topic: article
ms.author: kaib
ms.date: 03/11/2020
ms.custom: seodec18
ms.openlocfilehash: 0aaa32c46d915eafffcfac9d95cfdd3a24d4086d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123431"
---
# <a name="how-to-verify-encryption-status-for-linux"></a>如何驗證 Linux 的加密狀態 

**此方案適用于 ADE 雙通和單通道擴展。**  
此文檔範圍是使用不同的方法驗證虛擬機器的加密狀態。

### <a name="environment"></a>環境

- Linux 散發套件

### <a name="procedure"></a>程序

虛擬機器已使用雙通或單路加密。

加密狀態可以在加密期間或加密後使用不同的方法進行驗證。

>[!NOTE] 
>我們在整個文檔中使用變數，相應地替換值。

### <a name="verification"></a>驗證

驗證可以從門戶、PowerShell、AZ CLI 和 VM 作業系統端進行。 

此驗證可以通過檢查連接到特定 VM 的磁片來完成。 

或者通過查詢每個磁片上的加密設定，無論磁片是附加的還是未連接的。

在以下不同的驗證方法：

## <a name="using-the-portal"></a>使用入口網站

通過檢查 Azure 門戶上的擴展部分來驗證加密狀態。

在 **"擴展"** 部分中，您將看到列出的 ADE 擴展。 

按一下它並查看**狀態訊息**，它將指示當前加密狀態：

![門戶檢查編號 1](./media/disk-encryption/verify-encryption-linux/portal-check-001.png)

在擴展清單中，您將看到相應的 ADE 擴展版本。 版本 0.x 對應于 ADE 雙通道，版本 1.x 對應于 ADE 單通道。

您可以獲取更多詳細資訊，按一下擴展，然後在*查看詳細狀態*。

您將看到以 json 格式的加密過程的更詳細的狀態：

![門戶檢查編號 2](./media/disk-encryption/verify-encryption-linux/portal-check-002.png)

![門戶檢查編號 3](./media/disk-encryption/verify-encryption-linux/portal-check-003.png)

驗證加密狀態的另一種方法是查看 **"磁片"** 部分。

![門戶檢查編號 4](./media/disk-encryption/verify-encryption-linux/portal-check-004.png)

>[!NOTE] 
> 此狀態表示磁片已加蓋加密設定，但不希望它們實際在作業系統級別加密。 根據設計，磁片首先被蓋章，然後加密。 如果加密過程失敗，磁片可能最終蓋章，但未加密。 要確認磁片是否真正加密，可以在作業系統級別仔細檢查每個磁片的加密。

## <a name="using-powershell"></a>使用 PowerShell

您可以使用以下 PowerShell 命令驗證加密 VM 的**一般**加密狀態：

```azurepowershell
   $VMNAME="VMNAME"
   $RGNAME="RGNAME"
   Get-AzVmDiskEncryptionStatus -ResourceGroupName  ${RGNAME} -VMName ${VMNAME}
```
![檢查電源外殼 1](./media/disk-encryption/verify-encryption-linux/verify-status-ps-01.png)

您可以使用以下 PowerShell 命令從每個磁片捕獲加密設定：

### <a name="single-pass"></a>單通道
如果單次，加密設定在每個磁片（OS 和資料）上蓋章，則可以以單次方式捕獲 OS 磁片加密設定，如下所示：

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
![驗證作業系統單通道 01](./media/disk-encryption/verify-encryption-linux/verify-os-single-ps-001.png)

如果磁片沒有加印加密設定，則輸出將為空，如下所示：

![作業系統加密設定 2](./media/disk-encryption/verify-encryption-linux/os-encryption-settings-2.png)

捕獲資料磁片加密設定：

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
![驗證資料單 ps 001](./media/disk-encryption/verify-encryption-linux/verify-data-single-ps-001.png)

### <a name="dual-pass"></a>雙通道
在雙通道中，加密設定在 VM 模型中標記，而不是在每個單獨的磁片上。

要驗證加密設定在雙通道中蓋章，可以使用以下命令：

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
![驗證雙通電源外殼 1](./media/disk-encryption/verify-encryption-linux/verify-dual-ps-001.png)

### <a name="unattached-disks"></a>未連接磁片

檢查未連接到 VM 的磁片的加密設定。

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
## <a name="using-az-cli"></a>使用 AZ CLI

您可以使用以下 AZ CLI 命令驗證加密 VM 的**一般**加密狀態：

```bash
VMNAME="VMNAME"
RGNAME="RGNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"
```
![使用 CLI 驗證常規 ](./media/disk-encryption/verify-encryption-linux/verify-gen-cli.png)

### <a name="single-pass"></a>單次通行證
您可以使用以下 AZ CLI 命令驗證每個磁片的加密設定：

```bash
az vm encryption show -g ${RGNAME} -n ${VMNAME} --query "disks[*].[name, statuses[*].displayStatus]"  -o table
```

![資料加密設置](./media/disk-encryption/verify-encryption-linux/data-encryption-settings-2.png)

>[!IMPORTANT]
> 如果磁片沒有蓋章的加密設定，則將顯示為"磁片未加密"

詳細的狀態和加密設定：

作業系統磁片：

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

![OSSingleCLI](./media/disk-encryption/verify-encryption-linux/os-single-cli.png)

資料磁片：

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

![資料單 CLI ](./media/disk-encryption/verify-encryption-linux/data-single-cli.png)

### <a name="dual-pass"></a>雙通

``` bash
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} -o table
```

![使用 CLI](./media/disk-encryption/verify-encryption-linux/verify-gen-dual-cli.png)驗證常規雙，您還可以檢查 OS 磁片的 VM 模型存儲設定檔上的加密設定：

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

![使用 CLI 驗證 vm 設定檔雙 ](./media/disk-encryption/verify-encryption-linux/verify-vm-profile-dual-cli.png)

### <a name="unattached-disks"></a>未連接磁片

檢查未連接到 VM 的磁片的加密設定。

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

要獲取特定磁片的詳細資訊，您需要提供：

包含磁片的存儲帳戶的 ID。
該特定存儲帳戶的連接字串。
存儲磁片的容器的名稱。
磁片名稱。

此命令列出所有存儲帳戶的所有 ID：

```bash
az storage account list --query [].[id] -o tsv
```
存儲帳戶 ID 以以下形式列出：

/訂閱/\<訂閱 id>/資源\<組/資源組名稱>/供應商/Microsoft.存儲/存儲帳戶\</存儲帳戶名稱>

選擇適當的 ID 並將其存儲在變數上：
```bash
id="/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"
```
連接字串。

此命令獲取特定存儲帳戶的連接字串並將其存儲在變數上：

```bash
ConnectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
```

容器名稱。

以下命令列出了存儲帳戶下的所有容器：
```bash
az storage container list --connection-string $ConnectionString --query [].[name] -o tsv
```
用於磁片的容器通常稱為"vhds"

將容器名稱存儲在變數上 
```bash
ContainerName="name of the container"
```

磁片名稱。

使用此命令列出特定容器上的所有 Blob
```bash 
az storage blob list -c ${ContainerName} --connection-string $ConnectionString --query [].[name] -o tsv
```
選擇要查詢的磁片並將其名稱存儲在變數上。
```bash
DiskName="diskname.vhd"
```
查詢磁片加密設定
```bash
az storage blob show -c ${ContainerName} --connection-string ${ConnectionString} -n ${DiskName} --query metadata.DiskEncryptionSettings
```

## <a name="from-the-os"></a>從作業系統
驗證資料磁碟分割是否加密（並且作業系統磁片未加密）

當分區/磁片加密時，它顯示為**密碼類型**，當它未加密時，它顯示為**部件/磁片**類型

``` bash
lsblk
```

![Os 密碼層 ](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer.png)

您可以使用以下"lsblk"變體獲取更多詳細資訊。 

您將看到由擴展安裝的**隱場**類型圖層。

下面的示例顯示具有"**加密\_LUKS FSTYPE"** 的邏輯卷和普通磁片。

```bash
lsblk -o NAME,TYPE,FSTYPE,LABEL,SIZE,RO,MOUNTPOINT
```
![Os 密碼層 2](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer-2.png)

作為額外的步驟，您還可以驗證資料磁片是否載入了任何金鑰

``` bash
cryptsetup luksDump /dev/VGNAME/LVNAME
```

``` bash
cryptsetup luksDump /dev/sdd1
```

哪些 dm 設備被列為密碼

```bash
dmsetup ls --target crypt
```

## <a name="next-steps"></a>後續步驟

- [Azure 磁碟加密的疑難排解](disk-encryption-troubleshooting.md)
