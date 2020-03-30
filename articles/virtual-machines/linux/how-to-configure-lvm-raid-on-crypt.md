---
title: 如何在 Linux VM 上配置 LVM 和 RAID 上密碼
description: 本文提供有關在 Linux VM 上的密碼配置 LVM 和 RAID 的說明。
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 78ba47ba887cf7c90adf70d9d444fbd8a3c721cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284903"
---
# <a name="how-to-configure-lvm-and-raid-on-crypt"></a>如何配置 LVM 和 RAID 密碼

本文檔是關於如何在密碼和密碼配置上執行 LVM 的分步過程。

### <a name="environment"></a>環境

- Linux 散發套件
    - RHEL 7.6°
    - 烏本圖 18.04°
    - SUSE 12+
- ADE 單通道
- ADE 雙通


## <a name="scenarios"></a>案例

**此方案適用于 ADE 雙通和單通道擴展。**  

- 在加密設備之上配置 LVM（加密時 LVM）
- 在加密設備之上配置 RAID（加密時 RAID）

對基礎設備進行加密後，您可以在該加密層之上創建 LVM 或 RAID 結構。 物理卷 （PV） 在加密圖層的頂部創建。
物理卷用於創建卷組。
創建卷並在 /etc/fstab 上添加所需的條目。 

![檢查連接的磁片 PowerShell](./media/disk-encryption/lvm-raid-on-crypt/000-lvm-raid-crypt-diagram.png)

類似地，RAID 設備是在磁片上的加密層之上創建的。 檔案系統在 RAID 設備頂部創建，並作為常規設備添加到 /etc/fstab 中。

### <a name="considerations"></a>考量

建議使用的方法為"密碼上的 LVM"。

當由於特定的應用程式/環境限制而無法使用 LVM 時，將考慮 RAID。

您將使用"加密格式所有"選項，有關此功能的資訊位於此處： https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux#use-encryptformatall-feature-for-data-disks-on-linux-vms。

雖然此方法可以在同時加密作業系統時完成，但我們只是加密資料磁碟機。

此過程假定您已經審閱了此處提及的先決條件：https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux和此處。 https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-cli-quickstart

ADE 雙通道版本位於棄用路徑上，不應再在新的 ADE 加密中使用。

### <a name="procedure"></a>程序

使用"在密碼"配置時，您將遵循以下過程：

>[!NOTE] 
>我們在整個文檔中使用變數，相應地替換值。
## <a name="general-steps"></a>一般步驟
### <a name="deploy-a-vm"></a>部署 VM 
>[!NOTE] 
>雖然這是可選的，但我們建議您在新部署的 VM 上應用此選項。

PowerShell
```powershell
New-AzVm -ResourceGroupName ${RGNAME} `
-Name ${VMNAME} `
-Location ${LOCATION} `
-Size ${VMSIZE} `
-Image ${OSIMAGE} `
-Credential ${creds} `
-Verbose
```
CLI：
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
### <a name="attach-disks-to-the-vm"></a>將磁片附加到 vm：
對要附加到 VM PowerShell 的新磁片$N數重複上述步驟
```powershell
$storageType = 'Standard_LRS'
$dataDiskName = ${VMNAME} + '_datadisk0'
$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $LOCATION -CreateOption Empty -DiskSizeGB 5
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName ${RGNAME}
$vm = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME} 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 0
Update-AzVM -VM ${VM} -ResourceGroupName ${RGNAME}
```
CLI：
```bash
az vm disk attach \
-g ${RGNAME} \
--vm-name ${VMNAME} \
--name ${VMNAME}datadisk1 \
--size-gb 5 \
--new \
-o table
```
### <a name="verify-the-disks-are-attached-to-the-vm"></a>驗證磁片是否連接到 VM：
PowerShell：
```powershell
$VM = Get-AzVM -ResourceGroupName ${RGNAME} -Name ${VMNAME}
$VM.StorageProfile.DataDisks | Select-Object Lun,Name,DiskSizeGB
```
![檢查連接的電源殼](./media/disk-encryption/lvm-raid-on-crypt/001-lvm-raid-check-disks-powershell.png)CLI 磁片：
```bash
az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks -o table
```
![檢查連接的 CLI](./media/disk-encryption/lvm-raid-on-crypt/002-lvm-raid-check-disks-cli.png)門戶![磁片：檢查連接的](./media/disk-encryption/lvm-raid-on-crypt/003-lvm-raid-check-disks-portal.png)CLI 作業系統的磁片：
```bash
lsblk 
```
![檢查磁片連接的門戶](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)
### <a name="configure-the-disks-to-be-encrypted"></a>將磁片配置為加密
此配置完成後，作業系統級別，相應的磁片配置為傳統的 ADE 加密：

檔案系統在磁片頂部創建。

將創建臨時裝載點以裝載檔案系統。

檔案系統配置在 /etc/fstab 上，可在啟動時裝載。

檢查分配給新磁片的設備信函，在此示例中，我們使用四個數據磁片

```bash
lsblk 
```
![檢查連接的磁片](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="create-a-filesystem-on-top-of-each-disk"></a>在每個磁片之上創建一個檔案系統。
此命令在"for"週期的"in"部分定義的每個磁片上迴圈 ext4 檔案系統創建。
```bash
for disk in c d e f; do echo mkfs.ext4 -F /dev/sd${disk}; done |bash
```
![檢查附加的磁片](./media/disk-encryption/lvm-raid-on-crypt/005-lvm-raid-create-temp-fs.png)os 查找最近創建的檔案系統的 UUID，創建一個暫存檔案夾來裝載它，在 /etc/fstab 上添加相應的條目並裝載所有檔案系統。

此命令還會在"for"週期的"in"部分定義的每個磁片上迴圈：
```bash
for disk in c d e f; do diskuuid="$(blkid -s UUID -o value /dev/sd${disk})"; \
mkdir /tempdata${disk}; \
echo "UUID=${diskuuid} /tempdata${disk} ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
``` 
### <a name="verify-the-disks-are-mounted-properly"></a>驗證磁片是否正確安裝：
```bash
lsblk
```
![檢查安裝](./media/disk-encryption/lvm-raid-on-crypt/006-lvm-raid-verify-temp-fs.png)並配置的暫存檔案系統：
```bash
cat /etc/fstab
```
![檢查 fstab](./media/disk-encryption/lvm-raid-on-crypt/007-lvm-raid-verify-temp-fstab.png)
### <a name="encrypt-the-data-disks"></a>加密資料磁片：
使用 KEK 的電源外殼：
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
使用 KEK 的 CLI：
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

僅當所有磁片都加密時，才繼續執行下一步。

PowerShell：
```powershell
Get-AzVmDiskEncryptionStatus -ResourceGroupName ${RGNAME} -VMName ${VMNAME}
```
![檢查加密](./media/disk-encryption/lvm-raid-on-crypt/008-lvm-raid-verify-encryption-status-ps.png)ps CLI：
```bash
az vm encryption show -n ${VMNAME} -g ${RGNAME} -o table
```
![檢查加密 CLI](./media/disk-encryption/lvm-raid-on-crypt/009-lvm-raid-verify-encryption-status-cli.png) ![門戶：檢查](./media/disk-encryption/lvm-raid-on-crypt/010-lvm-raid-verify-encryption-status-portal.png)加密作業系統級別：
```bash
lsblk
```
![檢查加密 CLI](./media/disk-encryption/lvm-raid-on-crypt/011-lvm-raid-verify-encryption-status-os.png)

擴展程式將檔案系統添加到"/var/lib/azure_disk_encryption_config/azure_crypt_mount"（舊加密）或添加到"/等/加密表"（新加密）。

不要修改這些檔中的任何一個。

此檔將在引導過程中處理啟動這些磁片，以便以後 LVM 或 RAID 可以使用這些磁片。 

不要擔心此檔的裝載點，因為 ADE 將失去將磁片裝載為正常檔案系統的能力，因為我們確實在這些加密設備之上創建了物理卷或 raid 設備（這將擺脫我們在準備過程）。
### <a name="remove-the-temp-folders-and-temp-fstab-entries"></a>刪除暫存檔案夾和臨時 fstab 條目
在將用作 LVM 的一部分的磁片上卸載檔案系統
```bash
for disk in c d e f; do umount /tempdata${disk}; done
```
並刪除 /etc/fstab 條目：
```bash
vi /etc/fstab
```
### <a name="verify-that-the-disks-are-not-mounted-and-that-the-entries-on-etcfstab-were-removed"></a>驗證磁片是否未安裝，以及 /etc/fstab 上的條目是否已被刪除
```bash
lsblk
```
![檢查未安裝](./media/disk-encryption/lvm-raid-on-crypt/012-lvm-raid-verify-disks-not-mounted.png)並配置的暫存檔案系統：
```bash
cat /etc/fstab
```
![檢查臨時 fstab 條目被刪除](./media/disk-encryption/lvm-raid-on-crypt/013-lvm-raid-verify-fstab-temp-removed.png)
## <a name="for-lvm-on-crypt"></a>對於 LVM 在密碼
現在基礎磁片已加密，您可以繼續創建 LVM 結構。

使用 /dev/映射器路徑為每個磁片創建物理卷（在磁片頂部的密碼層上而不是在磁片本身上），而不是使用設備名稱。
### <a name="configure-lvm-on-top-of-the-encrypted-layers"></a>在加密層頂部配置 LVM
#### <a name="create-the-physical-volumes"></a>創建物理卷
您會收到一條警告，詢問是否可以清除檔案系統簽名。 

您可以繼續輸入"y"或使用回聲"y"，如下所示：
```bash
echo "y" | pvcreate /dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052
echo "y" | pvcreate /dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1
echo "y" | pvcreate /dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a
echo "y" | pvcreate /dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![pvcreate](./media/disk-encryption/lvm-raid-on-crypt/014-lvm-raid-pvcreate.png)
>[!NOTE] 
>此處的 /dev/映射器/設備名稱需要基於 lsblk 的輸出為實際值進行替換。
#### <a name="verify-the-physical-volumes-information"></a>驗證物理卷資訊
```bash
pvs
```
![檢查物理卷 1](./media/disk-encryption/lvm-raid-on-crypt/015-lvm-raid-pvs.png)
#### <a name="create-the-volume-group"></a>創建卷組
使用已初始化的相同設備創建 VG
```bash
vgcreate vgdata /dev/mapper/
```
### <a name="check-the-volume-group-information"></a>檢查卷組資訊
```bash
vgdisplay -v vgdata
```
```bash
pvs
```
![檢查物理卷 2](./media/disk-encryption/lvm-raid-on-crypt/016-lvm-raid-pvs-on-vg.png)
#### <a name="create-logical-volumes"></a>創建邏輯卷
```bash
lvcreate -L 10G -n lvdata1 vgdata
lvcreate -L 7G -n lvdata2 vgdata
``` 
#### <a name="check-the-logical-volumes-created"></a>檢查創建的邏輯卷
```bash
lvdisplay
lvdisplay vgdata/lvdata1
lvdisplay vgdata/lvdata2
```
![檢查 lvs](./media/disk-encryption/lvm-raid-on-crypt/017-lvm-raid-lvs.png)
#### <a name="create-filesystems-on-top-of-the-logical-volumes-structures"></a>在邏輯卷結構之上創建檔案系統
```bash
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata1
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata2
```
#### <a name="create-the-mount-points-for-the-new-filesystems"></a>為新檔案系統創建裝載點
```bash
mkdir /data0
mkdir /data1
```
#### <a name="add-the-new-file-systems-to-etcfstab-and-mount-them"></a>將新檔案系統添加到 /etc/fstab 並裝載它們
```bash
echo "/dev/mapper/vgdata-lvdata1 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "/dev/mapper/vgdata-lvdata2 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```
#### <a name="verify-that-the-new-filesystems-are-mounted"></a>驗證是否裝載了新檔案系統
```bash
lsblk -fs
df -h
```
![檢查邏輯卷](./media/disk-encryption/lvm-raid-on-crypt/018-lvm-raid-lsblk-after-lvm.png)此 lsblk 變體中，我們將列出按相反順序顯示依賴項的設備，此選項有助於標識按邏輯卷而不是原始 /dev/sd_disk] 設備名稱分組的設備。

重要提示：確保將"無故障"選項添加到在 ADE 加密設備頂部創建的 LVM 卷的裝載點選項中。 避免作業系統在啟動過程中（或處於維護模式）期間卡住非常重要。 

加密磁片在啟動過程結束時解鎖，LVM 卷和檔案系統將自動裝載。

如果未使用 nofail 選項，作業系統將永遠不會進入啟動 ADE 的階段，資料磁片將解鎖並裝載。

您可以測試重新開機 VM 並驗證檔案系統在啟動後也會自動安裝。 

考慮到此過程可能需要幾分鐘時間，具體取決於檔案系統的數量和大小
#### <a name="reboot-the-vm-and-verify-after-reboot"></a>重新開機 VM 並在重新開機後進行驗證
```bash
shutdown -r now
```
```bash
lsblk
df -h
```
## <a name="for-raid-on-crypt"></a>用於在加密的 RAID
現在基礎磁片已加密，您可以繼續創建與 LVM 相同的 RAID 結構，而不是使用設備名稱，為每個磁片使用 /dev/映射器路徑。

#### <a name="configure-raid-on-top-of-the-encrypted-layer-of-the-disks"></a>在磁片的加密層頂部配置 RAID
```bash
mdadm --create /dev/md10 \
--level 0 \
--raid-devices=4 \
/dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052 \
/dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1 \
/dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a \
/dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![mdadm 創建](./media/disk-encryption/lvm-raid-on-crypt/019-lvm-raid-md-creation.png)
>[!NOTE] 
>此處的 /dev/映射器/設備名稱需要基於 lsblk 的輸出為實際值進行替換。
#### <a name="checkmonitor-the-raid-creation"></a>檢查/監視 RAID 創建：
```bash
watch -n1 cat /proc/mdstat
mdadm --examine /dev/mapper/[]
mdadm --detail /dev/md10
```
![檢查 mdadm](./media/disk-encryption/lvm-raid-on-crypt/020-lvm-raid-md-details.png)
#### <a name="create-a-filesystem-on-top-of-the-new-raid-device"></a>在新 Raid 設備頂部創建檔案系統：
```bash
mkfs.ext4 /dev/md10
```
為檔案系統創建新的裝載點，將新檔案系統添加到 /etc/fstab，然後裝載它
```bash
for device in md10; do diskuuid="$(blkid -s UUID -o value /dev/${device})"; \
mkdir /raiddata; \
echo "UUID=${diskuuid} /raiddata ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
```
驗證是否裝載了新檔案系統
```bash
lsblk -fs
df -h
```
![檢查 mdadm](./media/disk-encryption/lvm-raid-on-crypt/021-lvm-raid-lsblk-md-details.png)

重要提示：確保將"無故障"選項添加到在 ADE 加密設備頂部創建的 RAID 卷的裝載點選項中。 

避免作業系統在啟動過程中（或處於維護模式）期間卡住非常重要。 

加密磁片將在啟動過程結束時解鎖，如果不使用 nofail 選項，RAID 卷和檔案系統將自動裝載，直到 ADE 解鎖它們。

作業系統永遠不會進入啟動 ADE 和資料磁片解鎖和裝載的階段。

您可以測試重新開機 VM 並驗證檔案系統在啟動後也會自動安裝。 考慮到此過程可能需要幾分鐘時間，具體取決於檔案系統的數量和大小
```bash
shutdown -r now
```
當您可以登錄時：
```bash
lsblk
df -h
```
## <a name="next-steps"></a>後續步驟

- [Azure 磁碟加密的疑難排解](disk-encryption-troubleshooting.md)

