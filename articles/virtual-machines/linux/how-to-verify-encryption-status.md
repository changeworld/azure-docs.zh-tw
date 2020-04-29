---
title: 如何驗證 Linux 的加密狀態
description: 本文提供有關從平臺和 OS 層級驗證加密狀態的指示。
author: kailashmsft
ms.service: security
ms.topic: article
ms.author: kaib
ms.date: 03/11/2020
ms.custom: seodec18
ms.openlocfilehash: 0aaa32c46d915eafffcfac9d95cfdd3a24d4086d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80123431"
---
# <a name="how-to-verify-encryption-status-for-linux"></a>如何驗證 Linux 的加密狀態 

**此案例適用于 ADE 雙重傳遞和單一傳遞延伸模組。**  
本檔範圍是使用不同的方法來驗證虛擬機器的加密狀態。

### <a name="environment"></a>環境

- Linux 散發套件

### <a name="procedure"></a>程序

虛擬機器已使用雙重傳遞或單一階段進行加密。

加密狀態可以在使用不同方法進行加密的期間或之後進行驗證。

>[!NOTE] 
>我們會在檔中使用變數，並據以取代值。

### <a name="verification"></a>驗證

您可以從入口網站、PowerShell、AZ CLI 和，或從 VM OS 端進行驗證。 

這項驗證可以藉由檢查連接至特定 VM 的磁片來完成。 

或藉由查詢每個個別磁片上的加密設定，不論該磁片是連接還是未附加。

以下是不同的驗證方法：

## <a name="using-the-portal"></a>使用入口網站

藉由檢查 Azure 入口網站上的 [延伸模組] 區段來驗證加密狀態。

在 [**擴充**功能] 區段中，您會看到列出的 ADE 延伸模組。 

按一下它，查看**狀態訊息**，它會指出目前的加密狀態：

![入口網站檢查編號1](./media/disk-encryption/verify-encryption-linux/portal-check-001.png)

在擴充功能清單中，您會看到對應的 ADE 延伸模組版本。 版本 0. x 對應至 ADE 雙重傳遞，而1.x 版對應到 ADE 單一傳遞。

您可以取得進一步的詳細資料，按一下延伸模組，然後*查看詳細狀態*。

您會看到更詳細的 json 格式的加密程式狀態：

![入口網站檢查編號2](./media/disk-encryption/verify-encryption-linux/portal-check-002.png)

![入口網站檢查編號3](./media/disk-encryption/verify-encryption-linux/portal-check-003.png)

驗證加密狀態的另一種方式是查看 [**磁片**] 區段。

![入口網站檢查編號4](./media/disk-encryption/verify-encryption-linux/portal-check-004.png)

>[!NOTE] 
> 此狀態表示磁片已加蓋加密設定，但實際上是在 OS 層級加密。 根據設計，磁片會先加上戳記，稍後再加密。 如果加密程式失敗，磁片最後可能會加上戳記，但不會加密。 若要確認磁片是否確實加密，您可以在 OS 層級上，再次檢查每個磁片的加密。

## <a name="using-powershell"></a>使用 PowerShell

您可以使用下列 PowerShell 命令來驗證已加密 VM 的**一般**加密狀態：

```azurepowershell
   $VMNAME="VMNAME"
   $RGNAME="RGNAME"
   Get-AzVmDiskEncryptionStatus -ResourceGroupName  ${RGNAME} -VMName ${VMNAME}
```
![檢查 PowerShell 1](./media/disk-encryption/verify-encryption-linux/verify-status-ps-01.png)

您可以使用下列 PowerShell 命令，從每個個別磁片捕捉加密設定：

### <a name="single-pass"></a>單一階段
如果單一傳遞，加密設定是每個磁片（OS 和資料）上的戳記，您可以在單一階段中捕捉 OS 磁片加密設定，如下所示：

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
![驗證 OS 單一行程01](./media/disk-encryption/verify-encryption-linux/verify-os-single-ps-001.png)

如果磁片沒有標記加密設定，輸出將會是空的，如下所示：

![OS 加密設定2](./media/disk-encryption/verify-encryption-linux/os-encryption-settings-2.png)

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
![驗證 data 單一 ps 001](./media/disk-encryption/verify-encryption-linux/verify-data-single-ps-001.png)

### <a name="dual-pass"></a>雙通路
在雙通路中，加密設定會在 VM 模型中加上戳記，而不是在每個個別磁片上。

若要確認加密設定已在雙通路中加蓋戳記，您可以使用下列命令：

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
![驗證雙通路 PowerShell 1](./media/disk-encryption/verify-encryption-linux/verify-dual-ps-001.png)

### <a name="unattached-disks"></a>未連結的磁片

檢查未連接至 VM 之磁片的加密設定。

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

您可以使用下列 AZ CLI 命令，驗證已加密 VM 的**一般**加密狀態：

```bash
VMNAME="VMNAME"
RGNAME="RGNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"
```
![使用 CLI 確認一般 ](./media/disk-encryption/verify-encryption-linux/verify-gen-cli.png)

### <a name="single-pass"></a>單次傳遞
您可以使用下列 AZ CLI 命令，從每個個別磁片驗證加密設定：

```bash
az vm encryption show -g ${RGNAME} -n ${VMNAME} --query "disks[*].[name, statuses[*].displayStatus]"  -o table
```

![資料加密設定](./media/disk-encryption/verify-encryption-linux/data-encryption-settings-2.png)

>[!IMPORTANT]
> 萬一磁片沒有標記加密設定，它會顯示為「磁片未加密」

詳細的狀態和加密設定：

OS 磁片：

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

![資料單一 CLI ](./media/disk-encryption/verify-encryption-linux/data-single-cli.png)

### <a name="dual-pass"></a>雙通路

``` bash
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} -o table
```

![使用 CLI ](./media/disk-encryption/verify-encryption-linux/verify-gen-dual-cli.png)驗證一般雙重：您也可以在 OS 磁片的 VM 模型儲存設定檔上檢查加密設定。。

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

![使用 CLI 驗證 vm 設定檔雙重 ](./media/disk-encryption/verify-encryption-linux/verify-vm-profile-dual-cli.png)

### <a name="unattached-disks"></a>未連結的磁片

檢查未連接至 VM 之磁片的加密設定。

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

若要取得特定磁片的詳細資料，您必須提供：

包含磁片之儲存體帳戶的識別碼。
該特定儲存體帳戶的連接字串。
儲存磁片的容器名稱。
磁片名稱。

此命令會列出所有儲存體帳戶的所有識別碼：

```bash
az storage account list --query [].[id] -o tsv
```
儲存體帳戶識別碼會以下列形式列出：

/subscriptions/\<訂用帳戶識別碼\<>/resourcegroups/資源組名\<，>/providers/microsoft.storage/storageaccounts/儲存體帳戶名稱>

選取適當的識別碼，並將它儲存在變數上：
```bash
id="/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"
```
連接字串。

此命令會取得一個特定儲存體帳戶的連接字串，並將其儲存在變數上：

```bash
ConnectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
```

容器名稱。

下列命令會列出儲存體帳戶底下的所有容器：
```bash
az storage container list --connection-string $ConnectionString --query [].[name] -o tsv
```
用於磁片的容器通常會命名為「vhd」

將容器名稱儲存在變數上 
```bash
ContainerName="name of the container"
```

磁片名稱。

使用此命令來列出特定容器上的所有 blob
```bash 
az storage blob list -c ${ContainerName} --connection-string $ConnectionString --query [].[name] -o tsv
```
選擇您想要查詢的磁片，並將其名稱儲存在變數上。
```bash
DiskName="diskname.vhd"
```
查詢磁片加密設定
```bash
az storage blob show -c ${ContainerName} --connection-string ${ConnectionString} -n ${DiskName} --query metadata.DiskEncryptionSettings
```

## <a name="from-the-os"></a>從作業系統
驗證資料磁碟分割是否已加密（且 OS 磁片不會）

當磁碟分割/磁片加密時，它會顯示為**crypt**類型，當它未加密時，它會顯示為 [**部分/磁片**] 類型

``` bash
lsblk
```

![Os Crypt 層 ](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer.png)

您可以使用下列 "lsblk" 變體取得進一步的詳細資料。 

您會看到延伸模組所裝載的**crypt**類型層。

下列範例會顯示邏輯磁片區，以及具有「**\_加密 LUKS FSTYPE**」的一般磁片。

```bash
lsblk -o NAME,TYPE,FSTYPE,LABEL,SIZE,RO,MOUNTPOINT
```
![Os Crypt 第2層](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer-2.png)

另一個步驟是，您也可以驗證資料磁片是否已載入任何金鑰

``` bash
cryptsetup luksDump /dev/VGNAME/LVNAME
```

``` bash
cryptsetup luksDump /dev/sdd1
```

而哪些 dm 裝置列為 crypt

```bash
dmsetup ls --target crypt
```

## <a name="next-steps"></a>後續步驟

- [Azure 磁碟加密的疑難排解](disk-encryption-troubleshooting.md)
