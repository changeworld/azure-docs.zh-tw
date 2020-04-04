---
title: 在加密裝置上設定 LVM 和 RAID - Azure 磁碟加密
description: 本文提供了在 Linux VM 的加密裝置上配置 LVM 和 RAID 的說明。
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 4e342ff44af38b8e79dc8695c1270b1f5c68e0a8
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657434"
---
# <a name="configure-lvm-and-raid-on-encrypted-devices"></a>在加密裝置上設定 LVM 和 RAID

本文是有關如何在加密設備上執行邏輯卷管理 (LVM) 和 RAID 的分步過程。 該過程適用於以下環境:

- Linux 散發套件
    - RHEL 7.6°
    - 烏本圖 18.04°
    - SUSE 12+
- Azure 磁碟加密單色版延伸
- Azure 磁碟加密雙色版延伸


## <a name="scenarios"></a>案例

本文中的過程支援以下方案:  

- 在加密裝置之上設定 LVM(密碼上的 LVM)
- 在加密裝置(密碼上的 RAID)上設定 RAID

對基礎設備或設備進行加密後,您可以在該加密層之上創建 LVM 或 RAID 結構。 

物理卷 (PV) 在加密層的頂部創建。 物理卷用於創建卷組。 建立卷並在 /etc/fstab 上添加所需的項目。 

![LVM 結構層圖](./media/disk-encryption/lvm-raid-on-crypt/000-lvm-raid-crypt-diagram.png)

類似地,RAID 設備是在磁碟上的加密層之上創建的。 在 RAID 裝置頂部創建檔案系統,並作為常規設備添加到 /etc/fstab 中。

## <a name="considerations"></a>考量

我們建議您在密碼上使用 LVM。 當由於特定的應用程式或環境限制而無法使用 LVM 時,RAID 是一個選項。

您將使用 **「加密格式全部」** 選項。 關於這個選項的詳細資訊,請參考[在 Linux VM 上使用加密格式所有功能進行資料磁碟](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux#use-encryptformatall-feature-for-data-disks-on-linux-vms)。

儘管您也可以在加密操作系統時使用此方法,但我們只是在此處加密數據驅動器。

過程假定您已經審閱了 Linux VM[上的 Azure 磁碟加密方案中](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux)的先決條件,並在[「快速入門」中:使用 Azure CLI 創建和加密 Linux VM。](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-cli-quickstart)

Azure 磁碟加密雙通道版本位於棄用路徑上,不應再在新加密上使用。

## <a name="general-steps"></a>一般步驟

使用"在加密"配置時,請使用以下過程中概述的過程。

>[!NOTE] 
>我們在整個文章中使用變數。 相應地替換值。

### <a name="deploy-a-vm"></a>部署 VM 
以下命令是可選的,但我們建議您在新部署的虛擬機 (VM) 上應用這些命令。

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
### <a name="attach-disks-to-the-vm"></a>將磁碟連線到 VM
對`$N`要附加到 VM 的新磁碟數重複以下命令。

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

### <a name="verify-that-the-disks-are-attached-to-the-vm"></a>驗證磁碟是否連接到 VM
PowerShell：
```powershell
$VM = Get-AzVM -ResourceGroupName ${RGNAME} -Name ${VMNAME}
$VM.StorageProfile.DataDisks | Select-Object Lun,Name,DiskSizeGB
```
![PowerShell 中連接的磁碟清單](./media/disk-encryption/lvm-raid-on-crypt/001-lvm-raid-check-disks-powershell.png)

Azure CLI：

```bash
az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks -o table
```
![Azure CLI 中連線的磁碟清單](./media/disk-encryption/lvm-raid-on-crypt/002-lvm-raid-check-disks-cli.png)

入口網站：

![門戶中附加磁碟清單](./media/disk-encryption/lvm-raid-on-crypt/003-lvm-raid-check-disks-portal.png)

作業系統：

```bash
lsblk 
```
![作業系統中連接的磁碟清單](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="configure-the-disks-to-be-encrypted"></a>將磁碟設定為加密
此配置在操作系統級別完成。 透過 Azure 磁碟加密為傳統加密設定了相應的磁碟:

- 檔案系統在磁碟頂部創建。
- 創建臨時裝載點以裝載文件系統。
- 檔系統配置為在 /etc/fstab 上,可在啟動時裝載。

檢查分配給新磁碟的設備信函。 在此示例中,我們使用四個數據磁碟。

```bash
lsblk 
```
![連線到作業系統的資料磁碟](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="create-a-file-system-on-top-of-each-disk"></a>在每個磁碟之上建立檔案系統
此命令迴圈在"for"週期的"in"部分定義的每個磁碟上創建 ext4 檔案系統。

```bash
for disk in c d e f; do echo mkfs.ext4 -F /dev/sd${disk}; done |bash
```
![建立 ext4 檔案系統](./media/disk-encryption/lvm-raid-on-crypt/005-lvm-raid-create-temp-fs.png)

尋找您最近創建的檔案系統的通用唯一標識碼 (UUID),創建臨時資料夾,在 /etc/fstab 上添加相應的條目,並裝載所有檔案系統。

此命令還會在「for」週期的「in」部分定義的每個磁碟上迴圈:

```bash
for disk in c d e f; do diskuuid="$(blkid -s UUID -o value /dev/sd${disk})"; \
mkdir /tempdata${disk}; \
echo "UUID=${diskuuid} /tempdata${disk} ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
``` 

### <a name="verify-that-the-disks-are-mounted-properly"></a>驗證磁碟是否正確安裝
```bash
lsblk
```
![載入的暫存檔案系統清單](./media/disk-encryption/lvm-raid-on-crypt/006-lvm-raid-verify-temp-fs.png)

要驗證磁碟是否設定:

```bash
cat /etc/fstab
```
![透過 fstab 設定資訊](./media/disk-encryption/lvm-raid-on-crypt/007-lvm-raid-verify-temp-fstab.png)

### <a name="encrypt-the-data-disks"></a>加密資料磁碟
使用金鑰加密金鑰 (KEK) 的 PowerShell:

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

使用 KEK 的 Azure CLI:

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

僅當所有磁碟都加密時,才繼續執行下一步。

PowerShell：

```powershell
Get-AzVmDiskEncryptionStatus -ResourceGroupName ${RGNAME} -VMName ${VMNAME}
```
![PowerShell 的加密狀態](./media/disk-encryption/lvm-raid-on-crypt/008-lvm-raid-verify-encryption-status-ps.png)

Azure CLI：

```bash
az vm encryption show -n ${VMNAME} -g ${RGNAME} -o table
```
![Azure CLI 的加密狀態](./media/disk-encryption/lvm-raid-on-crypt/009-lvm-raid-verify-encryption-status-cli.png)

入口網站：

![門戶的加密狀態](./media/disk-encryption/lvm-raid-on-crypt/010-lvm-raid-verify-encryption-status-portal.png)

作業系統等級:

```bash
lsblk
```
![作業系統中的加密狀態](./media/disk-encryption/lvm-raid-on-crypt/011-lvm-raid-verify-encryption-status-os.png)

擴展名將檔案系統添加到 /var/lib/azure_disk_encryption_config/azure_crypt_mount(舊加密)或 /etc/crypttab(新加密)。

>[!NOTE] 
>不要修改這些檔中的任何一個。

此檔將在引導過程中處理啟動這些磁碟,以便 LVM 或 RAID 以後可以使用它們。 

不要擔心此檔的裝載點。 在我們在這些加密設備上創建物理捲或 RAID 設備後,Azure 磁碟加密將失去將磁碟裝載為正常檔案系統的能力。 (這將刪除我們在準備過程中使用的檔案系統格式。

### <a name="remove-the-temporary-folders-and-temporary-fstab-entries"></a>刪除暫存資料夾與暫存 fstab 項目
在將用作 LVM 一部分的磁碟上卸載文件系統。

```bash
for disk in c d e f; do unmount /tempdata${disk}; done
```
並刪除 /etc/fstab 項目:

```bash
vi /etc/fstab
```
### <a name="verify-that-the-disks-are-not-mounted-and-that-the-entries-on-etcfstab-were-removed"></a>驗證磁碟是否未安裝,以及 /etc/fstab 上的項目是否已被刪除

```bash
lsblk
```
![驗證暫存檔案系統未安裝](./media/disk-encryption/lvm-raid-on-crypt/012-lvm-raid-verify-disks-not-mounted.png)

並驗證磁碟是否設定:
```bash
cat /etc/fstab
```
![驗證已刪除暫存 fstab 項目](./media/disk-encryption/lvm-raid-on-crypt/013-lvm-raid-verify-fstab-temp-removed.png)

## <a name="steps-for-lvm-on-crypt"></a>LVM 在加密點上的步驟
現在基礎磁碟已加密,您可以創建 LVM 結構。

使用 /dev/映射器路徑為每個磁碟創建物理卷(在磁碟頂部的地窖層上,而不是在磁碟本身上),而不是使用設備名稱。

### <a name="configure-lvm-on-top-of-the-encrypted-layers"></a>在加密層頂部設定 LVM
#### <a name="create-the-physical-volumes"></a>建立物理卷
您將看到一條警告,詢問是否可以清除檔案系統簽名。 繼續輸入**y**,或使用**回聲"y",** 如圖所示:

```bash
echo "y" | pvcreate /dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052
echo "y" | pvcreate /dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1
echo "y" | pvcreate /dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a
echo "y" | pvcreate /dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![驗證已建立物理卷](./media/disk-encryption/lvm-raid-on-crypt/014-lvm-raid-pvcreate.png)

>[!NOTE] 
>此處的 /dev/mapper/設備名稱需要基於**lsblk**的輸出進行實際值替換。

#### <a name="verify-the-information-for-physical-volumes"></a>驗證物理卷的資訊
```bash
pvs
```

![實體卷資訊](./media/disk-encryption/lvm-raid-on-crypt/015-lvm-raid-pvs.png)

#### <a name="create-the-volume-group"></a>建立元件
使用已初始化相同裝置建立卷群組:

```bash
vgcreate vgdata /dev/mapper/
```

### <a name="check-the-information-for-the-volume-group"></a>檢查卷群組的資訊

```bash
vgdisplay -v vgdata
```
```bash
pvs
```
![碟片資訊](./media/disk-encryption/lvm-raid-on-crypt/016-lvm-raid-pvs-on-vg.png)

#### <a name="create-logical-volumes"></a>建立邏輯卷

```bash
lvcreate -L 10G -n lvdata1 vgdata
lvcreate -L 7G -n lvdata2 vgdata
``` 

#### <a name="check-the-created-logical-volumes"></a>檢查建立的邏輯卷

```bash
lvdisplay
lvdisplay vgdata/lvdata1
lvdisplay vgdata/lvdata2
```
![邏輯卷的資訊](./media/disk-encryption/lvm-raid-on-crypt/017-lvm-raid-lvs.png)

#### <a name="create-file-systems-on-top-of-the-structures-for-logical-volumes"></a>在邏輯卷的結構之上建立檔案系統

```bash
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata1
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata2
```

#### <a name="create-the-mount-points-for-the-new-file-systems"></a>新增檔案系統建立載入點

```bash
mkdir /data0
mkdir /data1
```

#### <a name="add-the-new-file-systems-to-etcfstab-and-mount-them"></a>新增檔案系統到 /etc/fstab 並載入它們

```bash
echo "/dev/mapper/vgdata-lvdata1 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "/dev/mapper/vgdata-lvdata2 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```

#### <a name="verify-that-the-new-file-systems-are-mounted"></a>驗證是否載入新的檔案系統

```bash
lsblk -fs
df -h
```
![載入檔案系統的資訊](./media/disk-encryption/lvm-raid-on-crypt/018-lvm-raid-lsblk-after-lvm.png)

在**lsblk**的此變體中,我們將按相反順序出顯示依賴項的設備。 此選項有助於標識按邏輯卷而不是原始 /dev/sd_disk) 設備名稱分組的設備。

請務必確保**將 nofail**選項添加到透過 Azure 磁碟加密加密的裝置之上創建的 LVM 卷的裝載點選項。 它可以防止作業系統在啟動過程中(或處於維護模式)期間卡住。

如果不使用 **「無故障**」選項:

- 操作系統永遠不會進入啟動 Azure 磁碟加密和數據磁碟解鎖和裝載階段。 
- 加密磁碟將在啟動過程結束時解鎖。 LVM 卷和檔案系統將自動裝載,直到 Azure 磁碟加密解鎖它們。 

您可以測試重新啟動 VM,並驗證檔案系統在啟動後是否也自動載入。 此過程可能需要幾分鐘時間,具體取決於文件系統的數量和大小。

#### <a name="reboot-the-vm-and-verify-after-reboot"></a>重新啟動 VM 並在重新啟動後進行驗證

```bash
shutdown -r now
```
```bash
lsblk
df -h
```
## <a name="steps-for-raid-on-crypt"></a>在加密點上 RAID 的步驟
現在基礎磁碟已加密,您可以繼續創建 RAID 結構。 該過程與 LVM 的過程相同,但使用 /dev/映射器路徑而不是使用設備名稱。

#### <a name="configure-raid-on-top-of-the-encrypted-layer-of-the-disks"></a>在磁碟的加密層頂部設定 RAID
```bash
mdadm --create /dev/md10 \
--level 0 \
--raid-devices=4 \
/dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052 \
/dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1 \
/dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a \
/dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![透過 mdadm 指令設定 RAID 的資訊](./media/disk-encryption/lvm-raid-on-crypt/019-lvm-raid-md-creation.png)

>[!NOTE] 
>此處的 /dev/mapper/設備名稱需要替換為實際值,具體取決於**lsblk**的輸出。

### <a name="checkmonitor-raid-creation"></a>檢查/監控 RAID 建立
```bash
watch -n1 cat /proc/mdstat
mdadm --examine /dev/mapper/[]
mdadm --detail /dev/md10
```
![RAID 的狀態](./media/disk-encryption/lvm-raid-on-crypt/020-lvm-raid-md-details.png)

### <a name="create-a-file-system-on-top-of-the-new-raid-device"></a>在新 RAID 裝置端建立檔案系統
```bash
mkfs.ext4 /dev/md10
```

為檔案系統建立新的載入點,將新檔案系統加入 /etc/fstab,然後載入它:

```bash
for device in md10; do diskuuid="$(blkid -s UUID -o value /dev/${device})"; \
mkdir /raiddata; \
echo "UUID=${diskuuid} /raiddata ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
```

驗證是否載入新的檔案系統:

```bash
lsblk -fs
df -h
```
![載入檔案系統的資訊](./media/disk-encryption/lvm-raid-on-crypt/021-lvm-raid-lsblk-md-details.png)

請務必確保**將 nofail**選項添加到透過 Azure 磁碟加密加密的裝置之上創建的 RAID 卷的裝載點選項。 它可以防止作業系統在啟動過程中(或處於維護模式)期間卡住。

如果不使用 **「無故障**」選項:

- 操作系統永遠不會進入啟動 Azure 磁碟加密和數據磁碟解鎖和裝載階段。
- 加密磁碟將在啟動過程結束時解鎖。 RAID 卷和檔案系統將自動載入,直到 Azure 磁碟加密解鎖它們。

您可以測試重新啟動 VM,並驗證檔案系統在啟動後是否也自動載入。 此過程可能需要幾分鐘時間,具體取決於文件系統的數量和大小。

```bash
shutdown -r now
```

當您可以登入時:

```bash
lsblk
df -h
```
## <a name="next-steps"></a>後續步驟

- [Azure 磁碟加密的疑難排解](disk-encryption-troubleshooting.md)

