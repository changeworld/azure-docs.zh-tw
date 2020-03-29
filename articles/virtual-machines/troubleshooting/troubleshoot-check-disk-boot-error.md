---
title: 將 Azure 虛擬機器開機時檢查檔案系統| Microsoft Docs
description: 了解如何解決開機時虛擬機器顯示正在檢查檔案系統的問題 | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/31/2018
ms.author: genli
ms.openlocfilehash: 86938c582745cb0759eda9cd0693f407471a0529
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921482"
---
# <a name="windows-shows-checking-file-system-when-booting-an-azure-vm"></a>Azure 虛擬機器開機時 Windows 顯示「正在檢查檔案系統」

本文說明當您在 Microsoft Azure 中將 Windows 虛擬機器 (VM) 開機時碰到「正在檢查檔案系統」錯誤。


## <a name="symptom"></a>徵狀 

Windows 虛擬機器未啟動。 當您檢查[開機診斷](boot-diagnostics.md)中的開機螢幕擷取畫面時，您會看見「檢查磁碟」處理序 (chkdsk.exe) 正在執行，並顯示以下其中一個訊息：

- 掃描和修復磁碟機 (C:)
- 檢查 C: 上的檔案系統

## <a name="cause"></a>原因

如果在檔案系統中發現 NTFS 錯誤，則 Windows 會在下一次重新啟動時檢查與修復磁碟的一致性。 這通常發生在虛擬機器有任何未預期的重新啟動，或如果虛擬機器關機處理序突然中斷時。

## <a name="solution"></a>解決方法 

在「檢查磁碟」處理序完成時，Windows 將正常開機。 如果虛擬機器卡在「檢查磁碟」處理序，請嘗試在虛擬機器離線時執行「磁碟檢查」：
1.  擷取受影響虛擬機器作業系統磁碟的快照集作為備份。 如需詳細資訊，請參閱[擷取磁碟快照集](../windows/snapshot-copy-managed-disk.md)。
2.  [將作業系統磁片附加到恢復 VM。](troubleshoot-recovery-disks-portal-windows.md)  
3.  在復原虛擬機器上，對連結的作業系統磁碟執行「檢查磁碟」。 在下列範例中，所連結作業系統磁碟的磁碟機代號為 E: 
        
        chkdsk E: /f
4.  「檢查磁碟」完成之後，將磁碟與復原虛擬機器中斷連結，然後再將磁碟重新連結至受影響的虛擬機器，作為作業系統磁碟。 如需詳細資訊，請參閱[將 OS 磁碟連結至復原 VM，以針對 Windows VM 進行疑難排解](troubleshoot-recovery-disks-portal-windows.md)。
