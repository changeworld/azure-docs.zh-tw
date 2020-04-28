---
title: Azure 序列主控台電源選項 |Microsoft Docs
description: Azure 序列主控台內可用的 VM 電源選項
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75451205"
---
# <a name="power-options-available-from-the-azure-serial-console"></a>Azure 序列主控台提供的電源選項

Azure 序列主控台為您的 VM 或虛擬機器擴展集提供了數種強大的電源管理工具。 這些電源管理選項可能會令人混淆，所以這是每個工具及其預期使用案例的總覽。

序列主控台功能 | 描述 | 使用案例
:----------------------|:------------|:---------
重新啟動 VM | 您的 VM 或虛擬機器擴展集實例的正常重新開機。 這項作業與呼叫 [總覽] 頁面中提供的 [重新開機] 功能相同。 | 在大部分情況下，此選項應該是您嘗試重新開機 VM 的第一個工具。 您的序列主控台連線會短暫中斷，並會在 VM 重新開機後立即自動繼續。
重設 VM | Azure 平臺提供的 VM 或虛擬機器擴展集的強制電源週期。 | 此選項可用來立即重新開機您的作業系統，而不論其目前的狀態為何。 因為這項作業並不正常，所以會有資料遺失或損毀的風險。 序列主控台連線不會有任何中斷，這可能有助於在開機時儘早傳送命令（例如，在 Linux VM 上取得 GRUB 或 Windows VM 中的安全模式）。
SysRq-重新開機（b） | 強制重新開機來賓的系統要求。 | 這項功能僅適用于 Linux 作業系統，而且需要在作業系統中[啟用 SysRq](./serial-console-nmi-sysrq.md#system-request-sysrq) 。 如果作業系統已針對 SysRq 正確設定，此命令會導致 OS 重新開機。
NMI （非遮罩中斷） | 中斷命令，將會傳遞至作業系統 | 這項作業適用于[Windows](./serial-console-windows.md#use-the-serial-console-for-nmi-calls)和[Linux](./serial-console-nmi-sysrq.md#non-maskable-interrupt-nmi) vm，而且需要啟用 NMI。 傳送 NMI 通常會導致作業系統損毀。 您可以設定作業系統建立傾印檔案，然後在收到 NMI 時重新開機，這在低層級的偵錯工具中可能很有用。

## <a name="next-steps"></a>後續步驟
* 深入瞭解[適用于 Linux vm 的 Azure 序列主控台](./serial-console-linux.md)
* 深入瞭解[適用于 Windows vm 的 Azure 序列主控台](./serial-console-windows.md)