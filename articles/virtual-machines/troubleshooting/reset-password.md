---
title: 如何在 Azure VM 上重設本機 Linux 密碼 | Microsoft Docs
description: 介紹在 Azure VM 上重設本機 Linux 密碼的步驟
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 08/20/2019
ms.author: delhan
ms.openlocfilehash: 83751538efe4f3d3af5928caa04b265b6c867442
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "71153564"
---
# <a name="how-to-reset-local-linux-password-on-azure-vms"></a>如何在 Azure VM 上重設本機 Linux 密碼

本文介紹數種方法來重設本機 Linux 虛擬機器 (VM) 的密碼。 如果使用者帳戶已過期，或您只要建立新帳戶，您可以使用下列方法來建立新的本機管理帳戶並重新獲得 VM 的存取權。

## <a name="symptoms"></a>徵狀

您無法登入 VM，並收到一則訊息，指出您所使用的密碼不正確。 此外，您無法使用 VMAgent 來重設您在 Azure 入口網站上的密碼。

## <a name="manual-password-reset-procedure"></a>手動密碼重設程序

> [!NOTE]
> 以下步驟不適用於具有非託管磁片的 VM。

1. 拍攝受影響 VM 的 OS 磁片快照，從快照創建磁片，然後將磁片附加到疑難排解 VM。 有關詳細資訊，請參閱使用[Azure 門戶將 OS 磁片附加到恢復 VM 來排除 Windows VM 的疑難排解](troubleshoot-recovery-disks-portal-linux.md)。

2. 使用遠端桌面連線到故障排除 VM。

3.  在故障排除 VM 上運行以下 SSH 命令，以成為超級使用者。

    ```bash
    sudo su
    ```

4.  執行 **fdisk -l** 或查看系統記錄，以尋找最新連接的磁碟。 找出要掛接的磁碟機名稱。 然後在暫時的 VM 上，尋找相關記錄檔。

    ```bash
    grep SCSI /var/log/kern.log (ubuntu)
    grep SCSI /var/log/messages (centos, suse, oracle)
    ```

    以下是 grep 命令的範例輸出：

    ```bash
    kernel: [ 9707.100572] sd 3:0:0:0: [sdc] Attached SCSI disk
    ```

5.  建立名為 **tempmount** 的掛接點。

    ```bash
    mkdir /tempmount
    ```

6.  在掛接點上掛接 OS 磁碟。 通常需要安裝*sdc1*或*sdc2*。 這將取決於 */etc*目錄中的託管分區，這些分區來自損壞的電腦磁片。

    ```bash
    mount /dev/sdc1 /tempmount
    ```

7.  請先建立核心認證檔案複本，再進行任何變更：

    ```bash
    cp /etc/passwd /etc/passwd_orig    
    cp /etc/shadow /etc/shadow_orig    
    cp /tempmount/etc/passwd /etc/passwd
    cp /tempmount/etc/shadow /etc/shadow 
    cp /tempmount/etc/passwd /tempmount/etc/passwd_orig
    cp /tempmount/etc/shadow /tempmount/etc/shadow_orig
    ```

8.  重設您所需的使用者密碼：

    ```bash
    passwd <<USER>> 
    ```

9.  將修改過的檔案移至已中斷電腦磁碟上的正確位置。

    ```bash
    cp /etc/passwd /tempmount/etc/passwd
    cp /etc/shadow /tempmount/etc/shadow
    cp /etc/passwd_orig /etc/passwd
    cp /etc/shadow_orig /etc/shadow
    ```

10. 返回根目錄，並將磁碟取消掛接。

    ```bash
    cd /
    umount /tempmount
    ```

11. 在 Azure 門戶中，從故障排除 VM 中分離磁片。

12. [更改受影響的 VM 的作業系統磁片](troubleshoot-recovery-disks-portal-linux.md#swap-the-os-disk-for-the-vm)。

## <a name="next-steps"></a>後續步驟

* [將 OS 磁碟連結至另一個 Azure VM，以針對 Azure VM 進行疑難排解](https://social.technet.microsoft.com/wiki/contents/articles/18710.troubleshoot-azure-vm-by-attaching-os-disk-to-another-azure-vm.aspx) \(英文\)

* [Azure CLI：如何刪除並從 VHD 重新部署 VM](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/azure-cli-how-to-delete-and-re-deploy-a-vm-from-vhd/) \(英文\)
