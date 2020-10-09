---
title: 針對 Azure Linux 虛擬機器中的開機錯誤進行疑難排解 |Microsoft Docs
description: 本文可協助您連結至文章，以針對 Azure Linux 虛擬機器中的開機錯誤進行疑難排解。
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "74408738"
---
# <a name="troubleshoot-azure-linux-virtual-machines-boot-errors"></a>針對 Azure Linux 虛擬機器開機錯誤進行疑難排解

本文列出當您在 Microsoft Azure 中啟動 Linux 虛擬機器 (VM) 時，可能會收到的常見開機錯誤。 如需這些錯誤的詳細資訊，請參閱**開機錯誤和解決方案**一節中的文章。

## <a name="boot-errors-and-solutions"></a>開機錯誤和解決方案

* [GRUB 修復](troubleshoot-vm-boot-error.md)

## <a name="next-steps"></a>後續步驟

- [VM 序列主控台](serial-console-linux.md)

使用 Azure 將 OS 磁片連接至復原 VM，以針對 Linux VM 進行疑難排解：

- [Azure VM 修復](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)

 磁片交換–可使用下列其中一種方式自動化：
- [Power Shell 修復腳本](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
- [bash 復原指令碼](https://github.com/sribs/azure-support-scripts)

- [CLI](troubleshoot-recovery-disks-linux.md)
- [Azure 入口網站](troubleshoot-recovery-disks-portal-linux.md)


## <a name="disk-swap-video"></a>磁碟交換影片：

如果您無法存取 GRUB，請觀看[此](https://youtu.be/m5t0GZ5oGAc)影片，以了解如何輕鬆地自動進行用來復原您 VM 的磁碟交換程序

## <a name="unofficial-solution"></a>非官方解決方案

您也可以嘗試使用不支援的 BETA 腳本來復原 VM [ALAR](https://github.com/malachma/azure-auto-recover)