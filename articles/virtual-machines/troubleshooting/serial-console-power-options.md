---
title: Azure 串列主控台電源選項 |微軟文檔
description: Azure 串列主控台中提供的 VM 電源選項
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/6/2019
ms.author: alsin
ms.openlocfilehash: 11c2549d7282bae5654ede1ac34d63a43cd0f059
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451205"
---
# <a name="power-options-available-from-the-azure-serial-console"></a>可從 Azure 串列主控台提供的電源選項

Azure 串列主控台提供了多個功能強大的工具，用於在 VM 或虛擬機器規模集中進行電源管理。 這些電源管理選項可能會混淆某些選項，因此這是每個工具及其預期用例的概述。

串列主控台功能 | 描述 | 使用案例
:----------------------|:------------|:---------
重新啟動 VM | 正常重新開機 VM 或虛擬機器縮放集實例。 此操作與調用"概述"頁中可用的重新開機功能相同。 | 在大多數情況下，此選項應該是嘗試重新開機 VM 的第一個工具。 串列主控台連接將經歷短暫的中斷，並在 VM 重新開機後立即自動復原。
重設 VM | 由 Azure 平臺設置的 VM 或虛擬機器擴展的強大電源週期。 | 此選項用於立即重新開機作業系統，而不管其目前狀態如何。 由於此操作不正常，因此存在資料丟失或損壞的風險。 串列主控台連接沒有中斷，這對於在啟動時間早期發送命令（例如，在 Linux VM 上訪問 GRUB 或在 Windows VM 中訪問安全模式）可能很有用。
SysRq - 重新開機 （b） | 強制來賓重新開機的系統請求。 | 此功能僅適用于 Linux 作業系統，並要求在作業系統中[啟用 SysRq。](./serial-console-nmi-sysrq.md#system-request-sysrq) 如果作業系統已正確配置為 SysRq，則此命令將導致作業系統重新開機。
NMI（不可遮罩中斷） | 中斷命令，該命令將傳遞到作業系統 | 此操作適用于[Windows](./serial-console-windows.md#use-the-serial-console-for-nmi-calls)和[Linux](./serial-console-nmi-sysrq.md#non-maskable-interrupt-nmi) VM，並且需要啟用 NMI。 發送 NMI 通常會導致作業系統崩潰。 您可以將作業系統配置為創建轉儲檔，然後在收到 NMI 後重新開機，這在低級調試中可能很有用。

## <a name="next-steps"></a>後續步驟
* 瞭解有關 Linux VM 的[Azure 串列主控台](./serial-console-linux.md)的資訊
* 瞭解有關 Windows VM 的[Azure 串列主控台](./serial-console-windows.md)的資訊