---
title: 解決 Azure Linux 虛擬機器中的啟動錯誤故障 |微軟文檔
description: 本文可説明您連結到文章，以排除 Azure Linux 虛擬機器中的啟動錯誤。
services: virtual-machines-linux
documentationCenter: ''
author: vilibert
manager: spogge
editor: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2019
ms.author: vilibert
ms.openlocfilehash: 37cb201751f72918838efe5837aa0e357d483f24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74408738"
---
# <a name="troubleshoot-azure-linux-virtual-machines-boot-errors"></a>排除 Azure Linux 虛擬機器啟動錯誤

本文列出了在 Microsoft Azure 中啟動 Linux 虛擬機器 （VM） 時可能收到的常見引導錯誤。 如需這些錯誤的詳細資訊，請參閱**開機錯誤和解決方案**一節中的文章。

## <a name="boot-errors-and-solutions"></a>開機錯誤和解決方案

* [格魯布救援](troubleshoot-vm-boot-error.md)

## <a name="next-steps"></a>後續步驟

- [VM 序列主控台](serial-console-linux.md)

通過使用 Azure 將 OS 磁片附加到恢復 VM，從而對 Linux VM 進行故障排除：

- [Azure VM 修復](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)

 磁片交換 – 這可以使用：
- [電源外殼恢復腳本](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
- [bash 恢復腳本](https://github.com/sribs/azure-support-scripts)

- [Cli](troubleshoot-recovery-disks-linux.md)
- [Azure 門戶](troubleshoot-recovery-disks-portal-linux.md)


## <a name="disk-swap-video"></a>磁片交換視頻：

如果您無法訪問 GRUB 觀看[此](https://youtu.be/m5t0GZ5oGAc)視頻並查看，如何輕鬆自動執行磁片交換過程以恢復 VM

## <a name="unofficial-solution"></a>非官方解決方案

也可以使用不支援的 BETA 腳本[ALAR](https://github.com/malachma/azure-auto-recover)嘗試恢復 VM