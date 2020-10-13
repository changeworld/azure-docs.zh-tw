---
title: 使用 chroot 復原 Linux Vm，其中使用 LVM (邏輯磁片區管理員) -Azure Vm
description: 使用 LVMs 復原 Linux Vm。
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
ms.openlocfilehash: 98514bad6a04e0c3058faf3133fc44333039ce53
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361461"
---
# <a name="troubleshooting-a-linux-vm-when-there-is-no-access-to-the-azure-serial-console-and-the-disk-layout-is-using-lvm-logical-volume-manager"></a>當無法存取 Azure 序列主控台，而磁片配置使用 LVM (邏輯磁片區管理員時，針對 Linux VM 進行疑難排解) 

這份疑難排解指南適用于 Linux VM 未開機的案例、無法使用 ssh，而且基礎檔案系統配置已設定 LVM (邏輯磁片區管理員) 。

## <a name="take-snapshot-of-the-failing-vm"></a>取得失敗 VM 的快照集

製作受影響 VM 的快照集。 

快照集會附加至 **修復** VM。 請依照 [此處](../linux/snapshot-copy-managed-disk.md#use-azure-portal) 的指示來取得 **快照**集。

## <a name="create-a-rescue-vm"></a>建立修復 VM
建議您通常使用相同或類似作業系統版本的修復 VM。 使用受影響 VM 的相同**區域**和**資源群組**

## <a name="connect-to-the-rescue-vm"></a>連接至修復 VM
使用 ssh 連接到 **修復** VM。 提高許可權並成為超級使用者使用

`sudo su -`

## <a name="attach-the-disk"></a>連接磁片
將磁片連接到從先前建立的快照集建立的 **修復** VM。

Azure 入口網站-> 選取 **修復** VM-> **磁片** 

![建立磁碟](./media/chroot-logical-volume-manager/create-disk-from-snap.png)

填入欄位。 將名稱指派給新的磁片，選取與快照集、受影響 VM 和修復 VM 相同的資源群組。

**來源類型**為**Snapshot** 。
**來源快照**集是先前建立的**快照**集名稱。

![建立磁片2](./media/chroot-logical-volume-manager/create-disk-from-snap-2.png)

為連接的磁片建立掛接點。

`mkdir /rescue`

執行 **fdisk-l** 命令以確認快照集磁片已附加，並列出所有可用的裝置和磁碟分割

`fdisk -l`

大部分的情況下，會將連接的快照磁片視為 **/dev/sdc** ，顯示兩個數據分割 **/dev/sdc1** 和 **/dev/sdc2**

![Fdisk](./media/chroot-logical-volume-manager/fdisk-output-sdc.png)

**\*** 表示開機分割區，這兩個磁碟分割都是裝載的。

執行命令 **lsblk** 以查看受影響 VM 的 LVMs

`lsblk`

![執行 lsblk](./media/chroot-logical-volume-manager/lsblk-output-mounted.png)


確認是否顯示受影響 VM 的 LVMs。
如果沒有，請使用下列命令加以啟用，然後重新執行 **lsblk**。
在繼續之前，請確定已從連接的磁片顯示 LVMs。

```
vgscan --mknodes
vgchange -ay
lvscan
mount –a
lsblk
```

找出要掛接邏輯磁片區的路徑，其中包含/ (根) 磁碟分割。 它有設定檔，例如/etc/default/grub

在此範例中，取得先前 **lsblk** 命令  **rootvg-rootlv** 的輸出，是正確的 **根** LV，可在下一個命令中使用。

下一個命令的輸出會顯示要針對 **根目錄** LV 進行掛接的路徑

`pvdisplay -m | grep -i rootlv`

![Rootlv](./media/chroot-logical-volume-manager/locate-rootlv.png)

繼續在目錄/rescue 上掛接此裝置

`mount /dev/rootvg/rootlv /rescue`

掛接在/rescue/boot 上設定開機 **旗** 標的磁碟分割

`
mount /dev/sdc1 /rescue/boot
`

使用 **lsblk** 命令確認已連接磁片的檔案系統現在已正確裝載

![執行 lsblk](./media/chroot-logical-volume-manager/lsblk-output-1.png)

或 **df-Th** 命令

![Df](./media/chroot-logical-volume-manager/df-output.png)

## <a name="gaining-chroot-access"></a>取得 chroot 存取權

取得 **chroot** 存取權，這可讓您執行各種修正，每個 Linux 發行版本都有些許變化。

```
 cd /rescue
 mount -t proc proc proc
 mount -t sysfs sys sys/
 mount -o bind /dev dev/
 mount -o bind /dev/pts dev/pts/
 chroot /rescue
```

如果有發生錯誤，例如：

**chroot：無法執行命令 '/bin/bash '：沒有這種檔案或目錄**

嘗試掛接 **usr** 邏輯磁片區

`
mount  /dev/mapper/rootvg-usrlv /rescue/usr
`

> [!TIP]
> 在 **chroot** 環境中執行命令時，請注意它們是針對連結的 OS 磁片執行，而不是本機的 **修復** VM。 

您可以使用命令來安裝、移除和更新軟體。 疑難排解 Vm，以便修正錯誤。


執行 lsblk 命令，/rescue 現在為/，/rescue/boot 是/boot ![ 螢幕擷取畫面顯示具有 l s blk 命令及其輸出樹狀的主控台視窗。](./media/chroot-logical-volume-manager/chrooted.png)

## <a name="perform-fixes"></a>執行修正

### <a name="example-1---configure-the-vm-to-boot-from-a-different-kernel"></a>範例 1-將 VM 設定為從不同的核心開機

常見的案例是強制 VM 從先前的核心開機，因為目前已安裝的核心可能已損毀，或升級未正確完成。


```
cd /boot/grub2

grep -i linux grub.cfg

grub2-editenv list

grub2-set-default "CentOS Linux (3.10.0-1062.1.1.el7.x86_64) 7 (Core)"

grub2-editenv list

grub2-mkconfig -o /boot/grub2/grub.cfg
```

*演練*

**Grep**命令會列出**grub**感知的核心。
![螢幕擷取畫面顯示主控台視窗，其中顯示針對核心的 grep 搜尋結果。](./media/chroot-logical-volume-manager/kernels.png)

**grub2-editenv 清單**會顯示下一次開機核心預設會載入的核心 ![](./media/chroot-logical-volume-manager/kernel-default.png)

**grub2-set-default** 可用來變更為另一個核心 ![ grub2 集](./media/chroot-logical-volume-manager/grub2-set-default.png)

**grub2-editenv**清單會顯示下一次啟動新的核心時將載入的核心 ![](./media/chroot-logical-volume-manager/kernel-new.png)

**grub2-grub2-mkconfig** 使用所需的版本 ![ grub2 grub2-mkconfig 重建 grub](./media/chroot-logical-volume-manager/grub2-mkconfig.png)



### <a name="example-2---upgrade-packages"></a>範例 2-升級套件

失敗的核心升級可能會導致 VM 無法啟動。
裝載所有邏輯磁片區，以允許移除或重新安裝套件

執行 **lvs) ** 命令來確認可供掛接的 **lvs) ** ，每個已遷移或來自另一個雲端提供者的 VM，設定會有所不同。

退出 **chroot** 環境，並掛接必要的 **LV**

![螢幕擷取畫面顯示具有 l v s 命令的主控台視窗，然後裝載 L V。](./media/chroot-logical-volume-manager/advanced.png)

現在請執行以再次存取 **chroot** 環境

`chroot /rescue`

所有 Lvs) 都應該顯示為已載入的資料分割

![進階](./media/chroot-logical-volume-manager/chroot-all-mounts.png)

查詢已安裝的 **核心**

![進階](./media/chroot-logical-volume-manager/rpm-kernel.png)

如有需要，請移除或升級**核心** 
 ![ Advanced](./media/chroot-logical-volume-manager/rpm-remove-kernel.png)


### <a name="example-3---enable-serial-console"></a>範例 3-啟用序列主控台
如果 Azure 序列主控台的存取權無法存取，請驗證您 Linux VM 的 GRUB 設定參數，並加以修正。 您可以[在本檔中](./serial-console-grub-proactive-configuration.md)找到詳細資訊

### <a name="example-4---kernel-loading-with-problematic-lvm-swap-volume"></a>範例 4-具有有問題的 LVM 交換磁片區的核心載入

VM 可能無法完整開機並進入 **dracut** 提示字元。
您可以從 Azure 序列主控台找出更多失敗的詳細資料，或流覽至 Azure 入口網站-> 開機診斷-> 序列記錄檔


可能會出現類似以下的錯誤：

```
[  188.000765] dracut-initqueue[324]: Warning: /dev/VG/SwapVol does not exist
         Starting Dracut Emergency Shell...
Warning: /dev/VG/SwapVol does not exist
```

在此範例中，會設定 grub 以載入具有 rd 名稱的 LV. **LV. LV = VG/SwapVol** ，且 VM 找不到此名稱。 這一行顯示核心的載入方式參考 LV SwapVol

```
[    0.000000] Command line: BOOT_IMAGE=/vmlinuz-3.10.0-1062.4.1.el7.x86_64 root=/dev/mapper/VG-OSVol ro console=tty0 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0 biosdevname=0 crashkernel=256M rd.lvm.lv=VG/OSVol rd.lvm.lv=VG/SwapVol nodmraid rhgb quiet
[    0.000000] e820: BIOS-provided physical RAM map:
```

 從/etc/default/grub 設定移除違規的 LV 並重建 grub2


## <a name="exit-chroot-and-swap-the-os-disk"></a>結束 chroot 並交換 OS 磁片

修復問題之後，請繼續卸載並卸離修復 VM 的磁片，讓它能夠與受影響的 VM OS 磁片交換。

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

從修復 VM 卸離磁片，並執行磁片交換。

從入口網站**磁片**選取 VM，然後選取 [卸**離**卸 
 ![ 離磁片]](./media/chroot-logical-volume-manager/detach-disk.png) 

儲存變更 ![ 儲存卸離](./media/chroot-logical-volume-manager/save-detach.png) 

磁片現在會變成可用，讓它能夠與受影響 VM 的原始 OS 磁片交換。

在 Azure 入口網站中流覽至失敗的 VM，然後選取**磁片**  ->  **交換 OS 磁片** 
 ![ 交換磁片](./media/chroot-logical-volume-manager/swap-disk.png) 

完成 [ **選擇磁片** ] 為上一個步驟中剛剛卸離的快照磁片欄位。 也需要受影響 VM 的 VM 名稱，然後選取 **[確定]**

![新的 os 磁片](./media/chroot-logical-volume-manager/new-osdisk.png) 

如果 VM 正在執行，磁片交換會將其關閉，請在磁片交換作業完成後重新開機 VM。


## <a name="next-steps"></a>後續步驟
深入了解

 [Azure 序列主控台]( ./serial-console-linux.md)

[單一使用者模式](./serial-console-grub-single-user-mode.md)