---
title: 使用 chroot 復原 Linux Vm，其中使用 LVM （邏輯磁片區管理員）-Azure Vm
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
ms.openlocfilehash: c0041a835c02263f23c5cdc6f839756edfb070c1
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86526872"
---
# <a name="troubleshooting-a-linux-vm-when-there-is-no-access-to-the-azure-serial-console-and-the-disk-layout-is-using-lvm-logical-volume-manager"></a>當沒有 Azure 序列主控台的存取權，且磁片配置使用 LVM （邏輯磁片區管理員）時，針對 Linux VM 進行疑難排解

這份疑難排解指南適用于 Linux VM 不開機的案例，不可能使用 ssh，而且基礎檔案系統配置已設定 LVM （邏輯磁片區管理員）。

## <a name="take-snapshot-of-the-failing-vm"></a>製作失敗 VM 的快照集

製作受影響 VM 的快照集。 

快照集會附加至**修復**VM。 依照[這裡](../linux/snapshot-copy-managed-disk.md#use-azure-portal)的指示，瞭解如何製作**快照**集。

## <a name="create-a-rescue-vm"></a>建立修復 VM
通常建議使用相同或類似作業系統版本的修復 VM。 使用受影響 VM 的相同**區域**和**資源群組**

## <a name="connect-to-the-rescue-vm"></a>連接到修復 VM
使用 ssh 連接到**修復**VM。 提升許可權，並使用來成為超級使用者

`sudo su -`

## <a name="attach-the-disk"></a>連接磁片
將磁片連結至從先前取得的快照集所建立的**修復**VM。

Azure 入口網站-> 選取 [**修復**VM->**磁片**] 

![建立磁碟](./media/chroot-logical-volume-manager/create-disk-from-snap.png)

填入欄位。 將名稱指派給新的磁片，選取與快照集、受影響的 VM 和修復 VM 相同的資源群組。

**來源類型**為**Snapshot** 。
**來源快照**集是先前建立的**快照**集名稱。

![建立磁片2](./media/chroot-logical-volume-manager/create-disk-from-snap-2.png)

為連接的磁片建立掛接點。

`mkdir /rescue`

執行**fdisk-l**命令，確認已附加快照磁片，並列出所有可用的裝置和磁碟分割

`fdisk -l`

在大部分情況下，附加的快照磁片將會視為 **/dev/sdc**顯示兩個分割區 **/dev/sdc1**和 **/dev/sdc2**

![Fdisk](./media/chroot-logical-volume-manager/fdisk-output-sdc.png)

**\*** 表示開機磁碟分割，這兩個磁碟分割都將裝載。

執行命令**lsblk**以查看受影響 VM 的 LVMs

`lsblk`

![執行 lsblk](./media/chroot-logical-volume-manager/lsblk-output-mounted.png)


確認是否顯示受影響 VM 的 LVMs。
如果沒有，請使用下列命令來啟用它們，並重新執行**lsblk**。
請確定已從連接的磁片顯示 LVMs，然後再繼續。

```
vgscan --mknodes
vgchange -ay
lvscan
mount –a
lsblk
```

找出裝載包含/（根）磁碟分割之邏輯磁片區的路徑。 它具有設定檔，例如/etc/default/grub

在此範例中，接受先前**lsblk**命令的輸出**rootvg-rootlv**是要掛接的正確**根**LV，而且可以在下一個命令中使用。

下一個命令的輸出會顯示**根目錄**LV 的掛接路徑

`pvdisplay -m | grep -i rootlv`

![Rootlv](./media/chroot-logical-volume-manager/locate-rootlv.png)

繼續在目錄/rescue 上掛接此裝置

`mount /dev/rootvg/rootlv /rescue`

掛接在/rescue/boot 上設定了**開機旗**標的分割區

`
mount /dev/sdc1 /rescue/boot
`

確認已連結磁片的檔案系統現在已使用**lsblk**命令正確掛接

![執行 lsblk](./media/chroot-logical-volume-manager/lsblk-output-1.png)

或**df-Th**命令

![Df](./media/chroot-logical-volume-manager/df-output.png)

## <a name="gaining-chroot-access"></a>取得 chroot 存取權

取得**chroot**存取權，這可讓您執行各種修正，每個 Linux 散發套件各有些許差異。

```
 cd /rescue
 mount -t proc proc proc
 mount -t sysfs sys sys/
 mount -o bind /dev dev/
 mount -o bind /dev/pts dev/pts/
 chroot /rescue
```

如果發生錯誤，例如：

**chroot：無法執行命令 '/bin/bash '：沒有這類檔案或目錄**

嘗試掛接**usr**邏輯磁片區

`
mount  /dev/mapper/rootvg-usrlv /rescue/usr
`

> [!TIP]
> 在**chroot**環境中執行命令時，請注意它們會針對連結的 OS 磁片執行，而不是針對本機的**修復**VM。 

命令可以用來安裝、移除和更新軟體。 針對 Vm 進行疑難排解以修正錯誤。


執行 lsblk 命令，而/rescue 現在是/，而/rescue/boot 是/boot ![ Chrooted](./media/chroot-logical-volume-manager/chrooted.png)

## <a name="perform-fixes"></a>執行修正

### <a name="example-1---configure-the-vm-to-boot-from-a-different-kernel"></a>範例 1-設定 VM 以從不同的核心開機

常見的案例是強制 VM 從先前的核心開機，因為目前安裝的核心可能已損毀或升級未正確完成。


```
cd /boot/grub2

grep -i linux grub.cfg

grub2-editenv list

grub2-set-default "CentOS Linux (3.10.0-1062.1.1.el7.x86_64) 7 (Core)"

grub2-editenv list

grub2-mkconfig -o /boot/grub2/grub.cfg
```

*逐步解說*

**Grep**命令會列出**grub**所感知的核心。
![核心](./media/chroot-logical-volume-manager/kernels.png)

**grub2-editenv 清單**會顯示下次開機核心預設會載入的核心 ![](./media/chroot-logical-volume-manager/kernel-default.png)

**grub2-set-default**是用來變更為另一個核心 ![ grub2 集](./media/chroot-logical-volume-manager/grub2-set-default.png)

**grub2-editenv**清單顯示將在下一次啟動新核心時載入的核心 ![](./media/chroot-logical-volume-manager/kernel-new.png)

**grub2-grub2-mkconfig 進行編譯**會使用所需的版本 ![ grub2 grub2-mkconfig 進行編譯重建 grub](./media/chroot-logical-volume-manager/grub2-mkconfig.png)



### <a name="example-2---upgrade-packages"></a>範例 2-升級套件

失敗的核心升級可能會導致無法啟動 VM。
掛接所有邏輯磁片區，以允許移除或重新安裝套件

執行**如 lvs)** 命令來確認哪些**如 lvs)** 可供掛接，每個已遷移或來自另一個雲端提供者的 VM，設定會有所不同。

結束**chroot**環境掛接必要的**LV**

![進階](./media/chroot-logical-volume-manager/advanced.png)

現在請執行來再次存取**chroot**環境

`chroot /rescue`

所有如 lvs) 都應該顯示為裝載的分割區

![進階](./media/chroot-logical-volume-manager/chroot-all-mounts.png)

查詢已安裝的**核心**

![進階](./media/chroot-logical-volume-manager/rpm-kernel.png)

如有需要，請移除或升級**核心** 
 ![ Advanced](./media/chroot-logical-volume-manager/rpm-remove-kernel.png)


### <a name="example-3---enable-serial-console"></a>範例 3-啟用序列主控台
如果無法存取 Azure 序列主控台，請確認 Linux VM 的 GRUB 設定參數，並加以修正。 您可以[在本檔中](./serial-console-grub-proactive-configuration.md)找到詳細資訊

### <a name="example-4---kernel-loading-with-problematic-lvm-swap-volume"></a>範例 4-具有有問題的 LVM 交換磁片區的核心載入

VM 可能無法完全開機並降至**dracut**提示。
如需失敗的詳細資訊，請從 Azure 序列主控台或流覽至 Azure 入口網站 > 開機診斷-> 序列記錄檔


可能會出現類似如下的錯誤：

```
[  188.000765] dracut-initqueue[324]: Warning: /dev/VG/SwapVol does not exist
         Starting Dracut Emergency Shell...
Warning: /dev/VG/SwapVol does not exist
```

在此範例中設定了 grub，以載入具有 rd 名稱的 LV **。 lv = VG/SwapVol** ，且 VM 找不到此項。 這一行顯示如何載入核心來參考 LV SwapVol

```
[    0.000000] Command line: BOOT_IMAGE=/vmlinuz-3.10.0-1062.4.1.el7.x86_64 root=/dev/mapper/VG-OSVol ro console=tty0 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0 biosdevname=0 crashkernel=256M rd.lvm.lv=VG/OSVol rd.lvm.lv=VG/SwapVol nodmraid rhgb quiet
[    0.000000] e820: BIOS-provided physical RAM map:
```

 從/etc/default/grub 設定中移除違規的 LV，並重建 grub2


## <a name="exit-chroot-and-swap-the-os-disk"></a>結束 chroot 並交換 OS 磁片

修復問題之後，請繼續取消掛接，並從修復 VM 卸離磁片，讓它能夠與受影響的 VM OS 磁片交換。

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

從入口網站**磁片**選取 VM，**然後選取 [卸離卸** 
 ![ 離磁片]](./media/chroot-logical-volume-manager/detach-disk.png) 

儲存變更 ![ 儲存卸離](./media/chroot-logical-volume-manager/save-detach.png) 

磁片現在會變成可用，讓它能夠與受影響 VM 的原始 OS 磁片交換。

在 Azure 入口網站中流覽至失敗的 VM，然後選取 [**磁片**] [  ->  **交換 OS 磁片**] [ 
 ![ 交換磁片]](./media/chroot-logical-volume-manager/swap-disk.png) 

完成 [**選擇磁片**] 是在上一個步驟中剛卸離的快照磁片欄位。 也需要受影響 VM 的 VM 名稱，然後選取 **[確定]**

![新增 os 磁片](./media/chroot-logical-volume-manager/new-osdisk.png) 

如果 VM 正在執行，磁片交換會將它關閉，一旦磁片交換操作完成後，請重新開機 VM。


## <a name="next-steps"></a>後續步驟
深入了解

 [Azure 序列主控台]( https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)

[單一使用者模式](./serial-console-grub-single-user-mode.md)
