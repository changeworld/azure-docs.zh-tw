---
title: Linux VM 開機至 Grub 修復
description: 虛擬機器無法開機，因為虛擬機器進入了修復主控台
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
ms.openlocfilehash: 5a2fd7fcfdae8559bfb39bffff7c73c7082a86aa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87543277"
---
# <a name="linux-vm-boots-to-grub-rescue"></a>Linux VM 開機至 Grub 修復

我們發現您的虛擬機器 (VM) 進入「修復主控台」。 當您的 Linux VM 最近套用核心變更（例如核心升級），而且因為開機程式期間發生核心錯誤而無法正確啟動時，就會發生此問題。 在開機過程中，當開機載入器嘗試找出 Linux 核心並將開機控制權交給它時，VM 會在提交失敗時進入修復主控台。

如果您發現未來無法連線至 VM，您可以使用 Azure 入口網站中的開機診斷 blade 來查看 VM 的螢幕擷取畫面。 這樣可以協助您診斷問題，並且判斷問題原因是否為類似的開機錯誤。

## <a name="recommended-steps"></a>建議的步驟

根據您收到的錯誤，遵循下列風險降低步驟：

### <a name="error---unknown-filesystem"></a>錯誤-未知的檔案系統

* 如果您收到 **未知檔**系統的錯誤，此錯誤可能是因為開機磁碟分割上的檔案系統損毀或核心設定不正確所造成。

   * 針對檔案系統問題，請遵循 [Linux 復原：由於檔案系統錯誤 (fsck、inode) 中的步驟，而無法透過 SSH 連線到 LINUX VM ](/archive/blogs/linuxonazure/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck-inodes)。
   * 針對核心問題，請遵循文章中的步驟： [如何從核心相關的開機問題復原 Azure Linux 虛擬機器](https://support.microsoft.com/help/4091524/how-recover-azure-linux-vm-from-kernel-related-boot-related-issues)，或 [Linux 復原：使用 Chroot 修正與核心問題相關的非開機問題](http://linuxonazure.azurewebsites.net/linux-recovery-fixing-non-boot-issues-related-to-kernel-problems-using-chroot/)。
   
### <a name="error---file-not-found"></a>錯誤-找不到檔案

* 如果您收到錯誤15：找不到檔案，或找不到 **初始 RAM 磁碟** 或 **initrd/initramfs**檔案，請依照下列步驟執行。

    * 對於遺失的檔案， `/boot/grub2/grub.cfg` 或 `initrd/initramfs` 繼續進行下列程式：

    1. 請確定 `/etc/default/grub` 存在且具有正確/所需的設定。 如果您不知道哪些是預設設定，則可以使用運作中的 VM 來檢查。

    2. 接下來，執行下列命令以重新產生其設定： `grub2-mkconfig -o /boot/grub2/grub.cfg`

   * 如果遺失的檔案是 `/boot/grub/menu.lst` ，則此錯誤是針對 (**RHEL**6.x、 **Centos** 6.x 和 **Ubuntu) 14.04** 的舊版 OS 版本，因此命令可能會不同。 您必須啟動舊的伺服器並進行測試，以確保提供正確的命令。

### <a name="error---no-such-partition"></a>錯誤-沒有這類分割區

* 如果您收到 **沒有這類磁碟分割**的錯誤，請 [在嘗試擴充 OS 磁片磁碟機之後，嘗試啟動 VM 時，請參閱案例案例：「沒有這類分割區」錯誤](/archive/blogs/shwetanayak/case-scenario-no-such-partition-error-while-trying-to-start-the-vm-after-attempting-to-extend-the-os-drive)。

### <a name="error---grubcfg-file-not-found"></a>錯誤-找不到 grub 檔案

* 如果您收到「找不到錯誤 **/boot/grub2/grub.cfg**檔案」，請遵循下列步驟。

    * 對於遺失的檔案， `/boot/grub2/grub.cfg` 或 `initrd/initramfs` 繼續進行下列程式：

    1. 請確定 `/etc/default/grub` 存在且具有正確/所需的設定。 如果您不知道哪些是預設設定，則可以使用運作中的 VM 來檢查。

    2. 接下來，執行下列命令以重新產生其設定： `grub2-mkconfig -o /boot/grub2/grub.cfg` 。

   * 如果遺失的檔案是 `/boot/grub/menu.lst` ，則此錯誤是針對 (**RHEL**6.x、 **Centos** 6.x 和 **Ubuntu) 14.04** 的舊版 OS 版本，因此命令可能會延遲。 啟動舊伺服器並加以測試，以確保提供正確的命令。

## <a name="next-steps"></a>後續步驟

* [Azure 虛擬機器代理程式概觀](../extensions/agent-windows.md)
* [適用於 Windows 的虛擬機器擴充功能和功能](../extensions/features-windows.md)
