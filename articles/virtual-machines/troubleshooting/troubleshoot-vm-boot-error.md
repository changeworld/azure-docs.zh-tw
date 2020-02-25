---
title: Linux VM 開機至 Grub 修復
description: 虛擬機器無法開機，因為虛擬機器已進入「修復主控台」
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/28/2019
ms.author: tiag
ms.openlocfilehash: c24a840716841d04537ac5b77bcaf26fca4b78cf
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/22/2020
ms.locfileid: "77561944"
---
# <a name="linux-vm-boots-to-grub-rescue"></a>Linux VM 開機至 Grub 修復

我們發現您的虛擬機器（VM）輸入了「修復主控台」。 當您的 Linux VM 最近套用核心變更（例如核心升級），而且因為開機程式期間的核心錯誤而無法正常啟動時，就會發生此問題。 在開機過程中，當開機載入器嘗試找出 Linux 核心並將開機控制交給它時，VM 會在送出失敗時進入「修復主控台」。

如果您未來發現無法連線到 VM，您可以使用 Azure 入口網站中的 [開機診斷] 分頁來查看 VM 的螢幕擷取畫面。 這樣可以協助您診斷問題，並且判斷問題原因是否為類似的開機錯誤。

## <a name="recommended-steps"></a>建議的步驟

根據您收到的錯誤，遵循下列緩和步驟：

### <a name="error---unknown-filesystem"></a>錯誤-未知的檔案系統

* 如果您收到錯誤不明的**檔案系統**，此錯誤可能是由於開機磁碟分割上的檔案系統損毀或不正確的核心設定所造成。

   * 針對檔案系統問題，請遵循[Linux 復原：由於檔案系統錯誤（fsck、inode）](https://blogs.msdn.microsoft.com/linuxonazure/2016/09/13/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck-inodes/)一文中的步驟，而無法透過 SSH 連線到 linux VM。
   * 針對核心問題，請遵循[Linux 復原：手動修正與核心問題相關的非開機問題](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-manually-fixing-non-boot-issues-related-to-kernel-problems/)一文中的步驟，或[linux 修復：使用 Chroot 修正與核心問題相關的非開機問題](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-fixing-non-boot-issues-related-to-kernel-problems-using-chroot/)。
   
### <a name="error---file-not-found"></a>錯誤-找不到檔案

* 如果您收到錯誤錯誤**15：找不**到檔案，或找不到初始 RAM 磁碟或**initrd/initramfs**檔案，請遵循下列步驟。

    * 針對遺漏的檔案 `/boot/grub2/grub.cfg` 或 `initrd/initramfs` 繼續進行下列程式：

    1. 請確定 `/etc/default/grub` 存在，而且有正確的/所需的設定。 如果您不知道哪些是預設設定，您可以使用運作中的 VM 來檢查。

    2. 接下來，執行下列命令以重新產生其設定： `grub2-mkconfig -o /boot/grub2/grub.cfg`

   * 如果遺失檔案 `/boot/grub/menu.lst`，此錯誤適用于較舊的 OS 版本（**RHEL**6.X、 **Centos** 6.x 和**Ubuntu 14.04**），因此命令可能會有所不同。 您必須加速舊的伺服器並進行測試，以確保提供正確的命令。

### <a name="error---no-such-partition"></a>錯誤-沒有這類資料分割

* 如果您收到錯誤資料**分割**，請參閱[案例：在嘗試擴充 OS 磁片磁碟機之後，嘗試啟動 VM 時，「沒有這種分割區」錯誤](https://blogs.technet.microsoft.com/shwetanayak/2017/03/12/case-scenario-no-such-partition-error-while-trying-to-start-the-vm-after-attempting-to-extend-the-os-drive/)。

### <a name="error---grubcfg-file-not-found"></a>錯誤-找不到 grub 檔案

* 如果您收到「找不到錯誤 **/boot/grub2/grub.cfg**檔案」，請遵循下列步驟。

    * 針對遺漏的檔案 `/boot/grub2/grub.cfg` 或 `initrd/initramfs` 繼續進行下列程式：

    1. 請確定 `/etc/default/grub` 存在，而且有正確的/所需的設定。 如果您不知道哪些是預設設定，您可以使用運作中的 VM 來檢查。

    2. 接下來，執行下列命令以重新產生其設定： `grub2-mkconfig -o /boot/grub2/grub.cfg`。

   * 如果遺失檔案 `/boot/grub/menu.lst`，此錯誤適用于較舊的 OS 版本（**RHEL**6.X、 **Centos** 6.x 和**Ubuntu 14.04**），因此命令可能會延遲。 啟動舊伺服器並加以測試，以確保提供正確的命令。

## <a name="next-steps"></a>後續步驟

* [Azure 虛擬機器代理程式概觀](../extensions/agent-windows.md)
* [適用於 Windows 的虛擬機器擴充功能和功能](../extensions/features-windows.md)

