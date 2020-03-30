---
title: Linux VM 引導到格魯布救援
description: 虛擬機器無法啟動，因為虛擬機器進入救援主控台
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561944"
---
# <a name="linux-vm-boots-to-grub-rescue"></a>Linux VM 引導到格魯布救援

我們已確定您的虛擬機器 （VM） 已進入救援主控台。 當 Linux VM 最近應用了內核更改（如內核升級），並且由於啟動過程中核心錯誤而不再正確啟動時，就會出現此問題。 在啟動過程中，當引導載入程式嘗試查找 Linux 內核並將其啟動控制交給它時，當移交失敗時，VM 將進入救援主控台。

如果發現將來無法連接到 VM，則可以使用 Azure 門戶中的引導診斷工具查看 VM 的螢幕截圖。 這樣可以協助您診斷問題，並且判斷問題原因是否為類似的開機錯誤。

## <a name="recommended-steps"></a>建議的步驟

根據您收到的錯誤，請遵循以下緩解步驟：

### <a name="error---unknown-filesystem"></a>錯誤 - 未知檔案系統

* 如果收到錯誤**未知檔案系統**，此錯誤可能會導致開機磁碟分割上的檔案系統損壞，或不正確的內核配置。

   * 對於檔案系統問題，請按照文章["Linux 恢復：由於檔案系統錯誤（fsck，iode）"而無法 SSH 到 Linux VM 的步驟](https://blogs.msdn.microsoft.com/linuxonazure/2016/09/13/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck-inodes/)。
   * 對於內核問題，請按照文章[《Linux 恢復：手動修復與內核問題相關的非引導問題](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-manually-fixing-non-boot-issues-related-to-kernel-problems/)》或["Linux 恢復：使用 chroot 修復與內核問題相關的非引導問題](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-fixing-non-boot-issues-related-to-kernel-problems-using-chroot/)"中的步驟。
   
### <a name="error---file-not-found"></a>錯誤 - 找不到檔

* 如果您收到**錯誤錯誤 15：找不到檔或未找到初始 RAM 磁碟**或**未找到 initrd/initramfs 檔**，請按照以下步驟操作。

    * 對於丟失的檔`/boot/grub2/grub.cfg`或`initrd/initramfs`繼續執行以下過程：

    1. 確保`/etc/default/grub`存在且具有正確的/所需設置。 如果您不知道哪些是預設設置，可以使用正常工作的 VM 進行檢查。

    2. 接下來，運行以下命令以重新生成其配置：`grub2-mkconfig -o /boot/grub2/grub.cfg`

   * `/boot/grub/menu.lst`如果缺少的檔是 ，則此錯誤適用于較舊的作業系統版本 **（RHEL 6.x、Centos**6.x 和**Ubuntu 14.04），** 因此命令可能有所不同。 **Centos 6.x** 您必須啟動舊伺服器並進行測試，以確保提供正確的命令。

### <a name="error---no-such-partition"></a>錯誤 - 沒有此類分區

* 如果你得到的錯誤**沒有這樣的分區**，請參閱[案例方案："沒有這樣的分區"錯誤，而嘗試啟動VM後，嘗試擴展操作系統磁碟機](https://blogs.technet.microsoft.com/shwetanayak/2017/03/12/case-scenario-no-such-partition-error-while-trying-to-start-the-vm-after-attempting-to-extend-the-os-drive/)。

### <a name="error---grubcfg-file-not-found"></a>錯誤 - 未找到 grub.cfg 檔

* 如果您收到錯誤 **/引導/grub2/grub.cfg 檔未找到**，請按照以下步驟操作。

    * 對於丟失的檔`/boot/grub2/grub.cfg`或`initrd/initramfs`繼續執行以下過程：

    1. 確保`/etc/default/grub`存在且具有正確的/所需設置。 如果您不知道哪些是預設設置，可以使用正常工作的 VM 進行檢查。

    2. 接下來，運行以下命令以重新生成其配置： `grub2-mkconfig -o /boot/grub2/grub.cfg`。

   * 如果缺少的檔`/boot/grub/menu.lst`是 ，此錯誤適用于較舊的作業系統版本 **（RHEL 6.x、Centos**6.x 和**Ubuntu 14.04），** 因此命令可能會延遲。 **Centos 6.x** 旋轉舊伺服器並對其進行測試，以確保提供正確的命令。

## <a name="next-steps"></a>後續步驟

* [Azure 虛擬機器代理程式概觀](../extensions/agent-windows.md)
* [適用於 Windows 的虛擬機器擴充功能和功能](../extensions/features-windows.md)

