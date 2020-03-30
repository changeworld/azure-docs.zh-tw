---
title: 使用使用 LVM（邏輯卷管理器）的 chroot 恢復 Linux VM - Azure VM
description: 使用 LVM 恢復 Linux VM。
services: virtual-machines-linux
documentationcenter: ''
author: vilibert
manager: spogge
editor: ''
tags: Linux chroot LVM
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/24/2019
ms.author: vilibert
ms.openlocfilehash: 20d710f717a9dff26f46ac7a201a9b694f3fbe84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74684137"
---
# <a name="troubleshooting-a-linux-vm-when-there-is-no-access-to-the-azure-serial-console-and-the-disk-layout-is-using-lvm-logical-volume-manager"></a>當無法訪問 Azure 串列主控台且磁片佈局使用 LVM（邏輯卷管理器）時，對 Linux VM 進行故障排除

此故障排除指南適用于 Linux VM 未啟動、無法啟動和基礎檔案系統佈局配置 LVM（邏輯卷管理器）的情況。

## <a name="take-snapshot-of-the-failing-vm"></a>拍攝失敗的 VM 的快照

拍攝受影響的 VM 的快照。 

然後，快照將附加到**救援**VM。 請按照[此處](https://docs.microsoft.com/azure/virtual-machines/linux/snapshot-copy-managed-disk#use-azure-portal)的說明瞭解如何拍攝**快照**。

## <a name="create-a-rescue-vm"></a>創建救援 VM
通常建議使用相同或類似作業系統版本的救援 VM。 使用受影響的 VM 的相同**區域****和資源組**

## <a name="connect-to-the-rescue-vm"></a>連接到救援 VM
使用 ssh 連接到**救援**VM。 提升特權並成為超級使用者使用

`sudo su -`

## <a name="attach-the-disk"></a>連接磁片
將磁片附加到以前拍攝的快照所做的**救援**VM。

Azure 門戶 ->選擇**救援**VM ->**磁片** 

![建立磁碟](./media/chroot-logical-volume-manager/create-disk-from-snap.png)

填充欄位。 為新磁片分配名稱，選擇與快照、受影響的 VM 和救援 VM 相同的資源組。

**源類型**為**快照**。
**源快照**是以前創建的**快照**的名稱。

![創建磁片 2](./media/chroot-logical-volume-manager/create-disk-from-snap-2.png)

為連接的磁片創建裝載點。

`mkdir /rescue`

運行**fdisk -l**命令以驗證已連接快照磁片並列出所有可用的設備和分區

`fdisk -l`

大多數情況下，附加的快照磁片將被視為 **/dev/sdc**顯示兩個分區 **/dev/sdc1**和 **/dev/sdc2**

![虛擬](./media/chroot-logical-volume-manager/fdisk-output-sdc.png)

指示**\*** 引導分區，兩個分區將被裝載。

運行命令**lsblk**以查看受影響的 VM 的 LVM

`lsblk`

![運行 lsblk](./media/chroot-logical-volume-manager/lsblk-output-mounted.png)


驗證是否顯示來自受影響 VM 的 LVM。
如果沒有，請使用以下命令啟用它們並重新運行**lsblk**。
在繼續操作之前，請確保從連接的磁片中的 LVM 可見。

```
vgscan --mknodes
vgchange -ay
lvscan
mount –a
lsblk
```

找到裝載包含 /（根）分區的邏輯卷的路徑。 它有設定檔，如 /etc/預設/grub

在此示例中，從以前的**lsblk**命令**rootvg-rootlv**獲取輸出是正確的要裝載的**根**LV，可用於下一個命令。

下一個命令的輸出將顯示要裝載根**LV**的路徑

`pvdisplay -m | grep -i rootlv`

![羅特夫夫](./media/chroot-logical-volume-manager/locate-rootlv.png)

繼續將此設備裝載到目錄/救援上

`mount /dev/rootvg/rootlv /rescue`

安裝在 /救援/引導上設置了**引導標誌**的分區

`
mount /dev/sdc1 /rescue/boot
`

驗證連接磁片的檔案系統現在使用**lsblk**命令正確安裝

![運行 lsblk](./media/chroot-logical-volume-manager/lsblk-output-1.png)

或**df -Th**命令

![Df](./media/chroot-logical-volume-manager/df-output.png)

## <a name="gaining-chroot-access"></a>獲得 chroot 訪問

獲得**chroot**訪問，這將使您能夠執行各種修復，每個 Linux 發行版本都存在細微的變化。

```
 cd /rescue
 mount -t proc proc proc
 mount -t sysfs sys sys/
 mount -o bind /dev dev/
 mount -o bind /dev/pts dev/pts/
 chroot /rescue
```

如果遇到錯誤，例如：

**chroot：無法運行命令"/bin/bash"：沒有此類檔或目錄**

嘗試裝載**usr**邏輯卷

`
mount  /dev/mapper/rootvg-usrlv /rescue/usr
`

> [!TIP]
> 在**chroot**環境中執行命令時，請注意這些命令針對附加的 OS 磁片而不是本地**救援**VM 運行。 

命令可用於安裝、刪除和更新軟體。 排除 VM 故障以修復錯誤。


執行 lsblk 命令和 /救援現在是 / 和 /救援/引導![是 /引導 Chroot](./media/chroot-logical-volume-manager/chrooted.png)

## <a name="perform-fixes"></a>執行修復

### <a name="example-1---configure-the-vm-to-boot-from-a-different-kernel"></a>示例 1 - 將 VM 配置為從其他內核啟動

常見方案是強制 VM 從以前的內核啟動，因為當前已安裝的內核可能已損壞或升級未正確完成。


```
cd /boot/grub2

grep -i linux grub.cfg

grub2-editenv list

grub2-set-default "CentOS Linux (3.10.0-1062.1.1.el7.x86_64) 7 (Core)"

grub2-editenv list

grub2-mkconfig -o /boot/grub2/grub.cfg
```

*walkthrough*

**grep**命令列出了**grub.cfg**知道的內核。
![核心](./media/chroot-logical-volume-manager/kernels.png)

**grub2 編輯清單**顯示將在下一個啟動![內核預設值時載入哪個內核](./media/chroot-logical-volume-manager/kernel-default.png)

**grub2 集預設值**用於更改為另一個內核![Grub2 集](./media/chroot-logical-volume-manager/grub2-set-default.png)

**grub2 編輯列**顯示下一個啟動![新內核將載入哪個內核](./media/chroot-logical-volume-manager/kernel-new.png)

**grub2-mkconfig**使用所需的![版本重建 grub.cfg](./media/chroot-logical-volume-manager/grub2-mkconfig.png)



### <a name="example-2---upgrade-packages"></a>示例 2 - 升級包

失敗的內核升級會使 VM 無法啟動。
裝載所有邏輯卷，允許刪除或重新安裝包

運行**lvs**命令以驗證哪些**LV**可用於安裝，每個 VM（已遷移或來自其他雲供應商）的配置將有所不同。

退出安裝所需**LV**的**chroot**環境

![進階](./media/chroot-logical-volume-manager/advanced.png)

現在，通過運行再次訪問**chroot**環境

`chroot /rescue`

所有 LV 都應作為裝載的分區可見

![進階](./media/chroot-logical-volume-manager/chroot-all-mounts.png)

查詢已安裝**的內核**

![進階](./media/chroot-logical-volume-manager/rpm-kernel.png)

如果需要刪除或升級**內核**
![高級](./media/chroot-logical-volume-manager/rpm-remove-kernel.png)


### <a name="example-3---enable-serial-console"></a>示例 3 - 啟用串列主控台
如果無法訪問 Azure 串列主控台，請驗證 Linux VM 的 GRUB 配置參數並更正它們。 詳細資訊可[在此文檔中](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-proactive-configuration)找到

### <a name="example-4---kernel-loading-with-problematic-lvm-swap-volume"></a>示例 4 - 具有有問題的 LVM 交換卷的內核載入

VM 可能無法完全啟動並放入**dracut**提示符。
故障的更多詳細資訊可以從 Azure 串列主控台找到，也可以導航到 Azure 門戶 -> 啟動診斷 ->串列日誌


可能存在與此類似的錯誤：

```
[  188.000765] dracut-initqueue[324]: Warning: /dev/VG/SwapVol does not exist
         Starting Dracut Emergency Shell...
Warning: /dev/VG/SwapVol does not exist
```

在此示例中，grub.cfg 配置為載入名稱為**rd.lvm.lv_VG/SwapVol**的 LV，並且 VM 無法找到此名稱。 此行顯示內核如何載入引用 LV SwapVol

```
[    0.000000] Command line: BOOT_IMAGE=/vmlinuz-3.10.0-1062.4.1.el7.x86_64 root=/dev/mapper/VG-OSVol ro console=tty0 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0 biosdevname=0 crashkernel=256M rd.lvm.lv=VG/OSVol rd.lvm.lv=VG/SwapVol nodmraid rhgb quiet
[    0.000000] e820: BIOS-provided physical RAM map:
```

 從 /etc/default/grub 配置中刪除有問題的 LV，並重建 grub2.cfg


## <a name="exit-chroot-and-swap-the-os-disk"></a>退出 chroot 並交換作業系統磁片

修復問題後，繼續卸載磁片並從救援 VM 上分離磁片，從而允許將其與受影響的 VM OS 磁片交換。

```
exit
cd /
umount /rescue/proc/
umount /rescue/sys/
umount /rescue/dev/pts
umount /rescue/dev/
umount /rescue/boot
umount /rescue
```

從救援 VM 上分離磁片並執行磁片交換。

從門戶**磁片**中選擇 VM 並選擇**分離**
![分離磁片](./media/chroot-logical-volume-manager/detach-disk.png) 

保存更改![保存分離](./media/chroot-logical-volume-manager/save-detach.png) 

磁片現在將可用，允許將其與受影響 VM 的原始 OS 磁片交換。

在 Azure 門戶中導航到失敗的 VM，並選擇**磁片** -> **交換 OS 磁片**
![交換磁片](./media/chroot-logical-volume-manager/swap-disk.png) 

完成"**選擇磁片"** 欄位是快照磁片剛剛在上一步中分離的欄位。 還需要受影響的 VM 的 VM 名稱，然後選擇 **"確定"**

![新 os 磁片](./media/chroot-logical-volume-manager/new-osdisk.png) 

如果 VM 正在運行磁片交換將關閉它，則在磁片交換操作完成後重新開機 VM。


## <a name="next-steps"></a>後續步驟
深入了解

 [Azure 串列主控台]( https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)

[單使用者模式](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode)
