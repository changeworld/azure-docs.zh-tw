---
title: 設定加密裝置上的 LVM 和 RAID-Azure 磁碟加密
description: 本文提供在 Linux Vm 的加密裝置上設定 LVM 和 RAID 的指示。
author: jofrance
ms.service: security
ms.topic: how-to
ms.author: jofrance
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 6ccb74fe58742974798732605b4a017a06777bcc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91328168"
---
# <a name="configure-lvm-and-raid-on-encrypted-devices"></a>設定加密裝置上的 LVM 和 RAID

本文將逐步說明如何在加密裝置上執行 (LVM) 和 RAID 的邏輯磁片區管理。 此程式適用于下列環境：

- Linux 散發套件
    - RHEL 7.6 +
    - Ubuntu 18.04 +
    - SUSE 12 +
- Azure 磁碟加密單一傳遞延伸模組
- Azure 磁碟加密雙重傳遞延伸模組


## <a name="scenarios"></a>案例

本文中的程式支援下列案例：  

- 將 LVM 設定在加密裝置上 (LVM crypt) 
- 在 (crypt) 的加密裝置上設定 RAID

在基礎裝置或裝置加密之後，您就可以在該加密層級上建立 LVM 或 RAID 結構。 

 (PVs) 的實體磁片區是在加密層級上建立的。 實體磁片區是用來建立磁片區群組。 您可以建立磁片區，並在/etc/fstab 上新增必要的專案 

![LVM 結構層的圖表](./media/disk-encryption/lvm-raid-on-crypt/000-lvm-raid-crypt-diagram.png)

以類似的方式，會在磁片上的加密層級上建立 RAID 裝置。 系統會在 RAID 裝置上建立檔案系統，並新增至/etc/fstab 作為一般裝置。

## <a name="considerations"></a>考量

我們建議您在 crypt 上使用 LVM。 當 LVM 因為特定的應用程式或環境限制而無法使用時，可以選擇使用 RAID。

您將會使用 **EncryptFormatAll** 選項。 如需此選項的詳細資訊，請參閱在 [Linux vm 上使用資料磁片的 EncryptFormatAll 功能](./disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms)。

雖然您也可以在加密作業系統時使用這個方法，但是我們只是將資料磁片磁碟機加密。

這些程式假設您已經在 Linux Vm 和快速入門中 [Azure 磁碟加密案例](./disk-encryption-linux.md) 中審查必要條件 [：使用 Azure CLI 建立和加密 linux VM](./disk-encryption-cli-quickstart.md)。

Azure 磁碟加密雙重傳遞版本位於取代路徑上，不應再用於新的加密。

## <a name="general-steps"></a>一般步驟

當您使用「內部 crypt」設定時，請使用下列程式中所述的程式。

>[!NOTE] 
>我們會在本文中使用到變數。 請視需要將值取代。

### <a name="deploy-a-vm"></a>部署 VM 
下列命令是選擇性的，但建議您將它們套用至新部署的虛擬機器 (VM) 。

PowerShell：

```powershell
New-AzVm -ResourceGroupName ${RGNAME} `
-Name ${VMNAME} `
-Location ${LOCATION} `
-Size ${VMSIZE} `
-Image ${OSIMAGE} `
-Credential ${creds} `
-Verbose
```
Azure CLI：

```bash
az vm create \
-n ${VMNAME} \
-g ${RGNAME} \
--image ${OSIMAGE} \
--admin-username ${username} \
--admin-password ${password} \
-l ${LOCATION} \
--size ${VMSIZE} \
-o table
```
### <a name="attach-disks-to-the-vm"></a>將磁片連結至 VM
針對 `$N` 您想要連接至 VM 的新磁片數目重複下列命令。

PowerShell：

```powershell
$storageType = 'Standard_LRS'
$dataDiskName = ${VMNAME} + '_datadisk0'
$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $LOCATION -CreateOption Empty -DiskSizeGB 5
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName ${RGNAME}
$vm = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME} 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 0
Update-AzVM -VM ${VM} -ResourceGroupName ${RGNAME}
```

Azure CLI：

```bash
az vm disk attach \
-g ${RGNAME} \
--vm-name ${VMNAME} \
--name ${VMNAME}datadisk1 \
--size-gb 5 \
--new \
-o table
```

### <a name="verify-that-the-disks-are-attached-to-the-vm"></a>確認磁片已連接至 VM
PowerShell：
```powershell
$VM = Get-AzVM -ResourceGroupName ${RGNAME} -Name ${VMNAME}
$VM.StorageProfile.DataDisks | Select-Object Lun,Name,DiskSizeGB
```
![PowerShell 中已連接磁片的清單](./media/disk-encryption/lvm-raid-on-crypt/001-lvm-raid-check-disks-powershell.png)

Azure CLI：

```bash
az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks -o table
```
![Azure CLI 中的已連接磁片清單](./media/disk-encryption/lvm-raid-on-crypt/002-lvm-raid-check-disks-cli.png)

入口網站：

![入口網站中已連接的磁片清單](./media/disk-encryption/lvm-raid-on-crypt/003-lvm-raid-check-disks-portal.png)

作業系統：

```bash
lsblk 
```
![作業系統中的已連接磁片清單](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="configure-the-disks-to-be-encrypted"></a>設定要加密的磁片
這項設定是在作業系統層級完成。 對應的磁片會透過 Azure 磁碟加密針對傳統加密進行設定：

- 系統會在磁片上建立檔案系統。
- 系統會建立暫存掛接點來掛接檔案系統。
- /Etc/fstab 上的檔案系統設定為在開機時掛接。

檢查指派給新磁片的裝置信件。 在此範例中，我們使用四個數據磁片。

```bash
lsblk 
```
![連接至 OS 的資料磁片](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="create-a-file-system-on-top-of-each-disk"></a>在每個磁片上建立檔案系統
此命令會在 "for" 迴圈的 "in" 部分所定義的每個磁片上，逐一查看 ext4 檔案系統的建立。

```bash
for disk in c d e f; do echo mkfs.ext4 -F /dev/sd${disk}; done |bash
```
![建立 ext4 檔案系統](./media/disk-encryption/lvm-raid-on-crypt/005-lvm-raid-create-temp-fs.png)

找出您最近建立之檔案系統的通用唯一識別碼 (UUID) 、建立暫存資料夾、在/etc/fstab 上新增對應的專案，以及掛接所有檔案系統。

此命令也會逐一查看 "for" 迴圈中 "in" 部分定義的每個磁片：

```bash
for disk in c d e f; do diskuuid="$(blkid -s UUID -o value /dev/sd${disk})"; \
mkdir /tempdata${disk}; \
echo "UUID=${diskuuid} /tempdata${disk} ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
``` 

### <a name="verify-that-the-disks-are-mounted-properly"></a>確認磁片已正確掛接
```bash
lsblk
```
![掛載的暫存檔案系統清單](./media/disk-encryption/lvm-raid-on-crypt/006-lvm-raid-verify-temp-fs.png)

也請確認已設定磁片：

```bash
cat /etc/fstab
```
![透過 fstab 設定資訊](./media/disk-encryption/lvm-raid-on-crypt/007-lvm-raid-verify-temp-fstab.png)

### <a name="encrypt-the-data-disks"></a>加密資料磁片
使用金鑰加密金鑰 (KEK) 的 PowerShell：

```powershell
$sequenceVersion = [Guid]::NewGuid() 
Set-AzVMDiskEncryptionExtension -ResourceGroupName $RGNAME `
-VMName ${VMNAME} `
-DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
-DiskEncryptionKeyVaultId $KeyVaultResourceId `
-KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
-KeyEncryptionKeyVaultId $KeyVaultResourceId `
-VolumeType 'DATA' `
-EncryptFormatAll `
-SequenceVersion $sequenceVersion `
-skipVmBackup;
```

使用 KEK 的 Azure CLI：

```bash
az vm encryption enable \
--resource-group ${RGNAME} \
--name ${VMNAME} \
--disk-encryption-keyvault ${KEYVAULTNAME} \
--key-encryption-key ${KEYNAME} \
--key-encryption-keyvault ${KEYVAULTNAME} \
--volume-type "DATA" \
--encrypt-format-all \
-o table
```
### <a name="verify-the-encryption-status"></a>驗證加密狀態

只有在所有磁片都已加密時，才繼續進行下一個步驟。

PowerShell：

```powershell
Get-AzVmDiskEncryptionStatus -ResourceGroupName ${RGNAME} -VMName ${VMNAME}
```
![PowerShell 中的加密狀態](./media/disk-encryption/lvm-raid-on-crypt/008-lvm-raid-verify-encryption-status-ps.png)

Azure CLI：

```bash
az vm encryption show -n ${VMNAME} -g ${RGNAME} -o table
```
![Azure CLI 中的加密狀態](./media/disk-encryption/lvm-raid-on-crypt/009-lvm-raid-verify-encryption-status-cli.png)

入口網站：

![入口網站中的加密狀態](./media/disk-encryption/lvm-raid-on-crypt/010-lvm-raid-verify-encryption-status-portal.png)

作業系統層級：

```bash
lsblk
```
![作業系統中的加密狀態](./media/disk-encryption/lvm-raid-on-crypt/011-lvm-raid-verify-encryption-status-os.png)

延伸模組會將檔案系統新增至/var/lib/azure_disk_encryption_config/azure_crypt_mount (舊的加密) 或/etc/crypttab (new 加密) 。

>[!NOTE] 
>請勿修改這些檔案中的任何一個。

此檔案會在開機過程中負責啟動這些磁片，讓 LVM 或 RAID 可以稍後使用它們。 

不要擔心這個檔案上的掛接點。 在這些加密裝置上建立實體磁片區或 RAID 裝置之後，Azure 磁碟加密將會失去將磁片掛接為一般檔案系統的能力。  (這會移除我們在準備過程中所使用的檔案系統格式。 ) 

### <a name="remove-the-temporary-folders-and-temporary-fstab-entries"></a>移除暫存資料夾和暫存 fstab 專案
您會將用來作為 LVM 一部分的磁片上的檔案系統卸載。

```bash
for disk in c d e f; do unmount /tempdata${disk}; done
```
並移除/etc/fstab 專案：

```bash
vi /etc/fstab
```
### <a name="verify-that-the-disks-are-not-mounted-and-that-the-entries-on-etcfstab-were-removed"></a>確認磁片未裝載，且/etc/fstab 上的專案已移除

```bash
lsblk
```
![確認暫存檔案系統已卸載](./media/disk-encryption/lvm-raid-on-crypt/012-lvm-raid-verify-disks-not-mounted.png)

並確認已設定磁片：
```bash
cat /etc/fstab
```
![確認已移除暫時 fstab 專案](./media/disk-encryption/lvm-raid-on-crypt/013-lvm-raid-verify-fstab-temp-removed.png)

## <a name="steps-for-lvm-on-crypt"></a>LVM-crypt 的步驟
現在基礎磁片已加密，您可以建立 LVM 結構。

您可以使用每個磁片的/dev/mapper 路徑，而不是使用裝置名稱，而是在磁片上的 crypt 層（而不是磁片本身) ）上建立實體磁片區 (。

### <a name="configure-lvm-on-top-of-the-encrypted-layers"></a>在加密層級上設定 LVM
#### <a name="create-the-physical-volumes"></a>建立實體磁片區
您會收到一則警告，詢問您是否確定要清除檔案系統簽章。 輸入 **y**以繼續，或使用 **echo "y"** ，如下所示：

```bash
echo "y" | pvcreate /dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052
echo "y" | pvcreate /dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1
echo "y" | pvcreate /dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a
echo "y" | pvcreate /dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![確認已建立實體磁片區](./media/disk-encryption/lvm-raid-on-crypt/014-lvm-raid-pvcreate.png)

>[!NOTE] 
>您必須根據 **lsblk**的輸出，將此處的/dev/mapper/device 名稱取代為實際值。

#### <a name="verify-the-information-for-physical-volumes"></a>驗證實體磁片區的資訊
```bash
pvs
```

![實體磁片區的資訊](./media/disk-encryption/lvm-raid-on-crypt/015-lvm-raid-pvs.png)

#### <a name="create-the-volume-group"></a>建立磁片區群組
使用已初始化的相同裝置來建立磁片區群組：

```bash
vgcreate vgdata /dev/mapper/
```

### <a name="check-the-information-for-the-volume-group"></a>檢查磁片區群組的資訊

```bash
vgdisplay -v vgdata
```
```bash
pvs
```
![磁片區群組的資訊](./media/disk-encryption/lvm-raid-on-crypt/016-lvm-raid-pvs-on-vg.png)

#### <a name="create-logical-volumes"></a>建立邏輯磁片區

```bash
lvcreate -L 10G -n lvdata1 vgdata
lvcreate -L 7G -n lvdata2 vgdata
``` 

#### <a name="check-the-created-logical-volumes"></a>檢查建立的邏輯磁片區

```bash
lvdisplay
lvdisplay vgdata/lvdata1
lvdisplay vgdata/lvdata2
```
![邏輯磁片區的資訊](./media/disk-encryption/lvm-raid-on-crypt/017-lvm-raid-lvs.png)

#### <a name="create-file-systems-on-top-of-the-structures-for-logical-volumes"></a>在邏輯磁片區的結構之上建立檔案系統

```bash
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata1
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata2
```

#### <a name="create-the-mount-points-for-the-new-file-systems"></a>建立新檔案系統的掛接點

```bash
mkdir /data0
mkdir /data1
```

#### <a name="add-the-new-file-systems-to-etcfstab-and-mount-them"></a>新增要/etc/fstab 的新檔案系統並加以掛接

```bash
echo "/dev/mapper/vgdata-lvdata1 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "/dev/mapper/vgdata-lvdata2 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```

#### <a name="verify-that-the-new-file-systems-are-mounted"></a>確認已裝載新的檔案系統

```bash
lsblk -fs
df -h
```
![已掛接檔案系統的資訊](./media/disk-encryption/lvm-raid-on-crypt/018-lvm-raid-lsblk-after-lvm.png)

在這種 **lsblk**變化下，我們會列出以相反順序顯示相依性的裝置。 此選項有助於識別依邏輯磁片區分組的裝置，而不是原始的/dev/sd [磁片] 裝置名稱。

請務必確定 **nofail** 選項已新增至在裝置上建立的 LVM 磁片區的掛接點選項，該磁片區是透過 Azure 磁碟加密加密的裝置上所建立。 它可防止作業系統在開機程式期間停滯 (或處於維護模式) 。

如果您未使用 **nofail** 選項：

- 作業系統永遠不會進入 Azure 磁碟加密啟動的階段，而且資料磁片會解除鎖定並掛接。 
- 加密的磁片將在開機程式結束時解除鎖定。 LVM 磁片區和檔案系統將會自動載入，直到 Azure 磁碟加密解除鎖定為止。 

您可以測試重新開機 VM，並驗證檔案系統也會在開機後自動載入。 此程式可能需要幾分鐘的時間，視檔案系統的數目和大小而定。

#### <a name="reboot-the-vm-and-verify-after-reboot"></a>重新開機 VM，並在重新開機後進行驗證

```bash
shutdown -r now
```
```bash
lsblk
df -h
```
## <a name="steps-for-raid-on-crypt"></a>Crypt 的步驟
現在基礎磁片已加密，您可以繼續建立 RAID 結構。 此程式與 LVM 的程式相同，但不使用裝置名稱，而是使用每個磁片的/dev/mapper 路徑。

#### <a name="configure-raid-on-top-of-the-encrypted-layer-of-the-disks"></a>在磁片的加密層級上設定 RAID
```bash
mdadm --create /dev/md10 \
--level 0 \
--raid-devices=4 \
/dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052 \
/dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1 \
/dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a \
/dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![透過 mdadm 命令設定 RAID 的資訊](./media/disk-encryption/lvm-raid-on-crypt/019-lvm-raid-md-creation.png)

>[!NOTE] 
>此處的/dev/mapper/device 名稱必須以您的實際值取代，並以 **lsblk**的輸出為基礎。

### <a name="checkmonitor-raid-creation"></a>檢查/監視 RAID 建立
```bash
watch -n1 cat /proc/mdstat
mdadm --examine /dev/mapper/[]
mdadm --detail /dev/md10
```
![RAID 的狀態](./media/disk-encryption/lvm-raid-on-crypt/020-lvm-raid-md-details.png)

### <a name="create-a-file-system-on-top-of-the-new-raid-device"></a>在新的 RAID 裝置上建立檔案系統
```bash
mkfs.ext4 /dev/md10
```

為檔案系統建立新的掛接點、將新的檔案系統新增至/etc/fstab，然後將它掛接：

```bash
for device in md10; do diskuuid="$(blkid -s UUID -o value /dev/${device})"; \
mkdir /raiddata; \
echo "UUID=${diskuuid} /raiddata ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
```

確認已裝載新的檔案系統：

```bash
lsblk -fs
df -h
```
![已掛接檔案系統的資訊](./media/disk-encryption/lvm-raid-on-crypt/021-lvm-raid-lsblk-md-details.png)

請務必確定 **nofail** 選項已新增至在裝置上所建立之 RAID 磁片區的掛接點選項，該磁片區是透過 Azure 磁碟加密加密的裝置上所建立。 它可防止作業系統在開機程式期間停滯 (或處於維護模式) 。

如果您未使用 **nofail** 選項：

- 作業系統永遠不會進入 Azure 磁碟加密啟動的階段，而且資料磁片會解除鎖定並掛接。
- 加密的磁片將在開機程式結束時解除鎖定。 RAID 磁片區和檔案系統將會自動掛接，直到 Azure 磁碟加密解除鎖定為止。

您可以測試重新開機 VM，並驗證檔案系統也會在開機後自動載入。 此程式可能需要幾分鐘的時間，視檔案系統的數目和大小而定。

```bash
shutdown -r now
```

當您可以登入時：

```bash
lsblk
df -h
```
## <a name="next-steps"></a>後續步驟

- [調整以 Azure 磁碟加密加密的邏輯磁片區管理裝置大小](how-to-resize-encrypted-lvm.md)
- [Azure 磁碟加密的疑難排解](disk-encryption-troubleshooting.md)
