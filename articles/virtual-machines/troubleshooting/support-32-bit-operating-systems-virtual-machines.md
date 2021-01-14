---
title: Azure 虛擬機器中32位作業系統的支援 |Microsoft Docs
description: Azure 虛擬機器上支援之作業系統的相關資訊
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/18/2019
ms.author: v-miegge
ms.openlocfilehash: 81b7efdd6bca0471719c11d130be95405f4d54e1
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98210183"
---
# <a name="support-for-32-bit-operating-systems-in-azure-virtual-machines"></a>在 Azure 虛擬機器中的 32 位元作業系統支援

Microsoft Azure 現在可讓使用者將其32位 Windows 作業系統帶入 Azure。 僅支援特製化的 Vhd，且一般化映射無法在 Azure 中運作。 由於其中一些作業系統已達到其生命週期支援協定的結尾，因此 Microsoft 可能不會提供額外的支援。 針對以 Linux 為基礎的或 Berkeley 軟體散發，也不提供支援， (以) 為基礎的作業系統，可在 Microsoft Azure 的虛擬機器 (VM) 上執行。

> [!NOTE]
> Azure 平臺在執行32位作業系統的 Vm 上有一個記憶體位址空間限制，其中只有1GB 的記憶體可供 VM 使用 (*特別是在用戶端 sku 上（例如 Win7 或 Win10*) ），而 vm 的其餘部分會顯示為來賓 VM 中的保留。 這是已知問題，我們目前沒有 ETA 可進行修正。 建議您移至64位作業系統版本。
> 

## <a name="more-information"></a>其他資訊

如需有關 Azure 虛擬機器所支援之作業系統的詳細資訊，請移至下列 Microsoft 知識庫文章：

* [適用於 Microsoft Azure 虛擬機器的 Microsoft 伺服器軟體支援](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)
* [Azure 中對 Linux 及開放原始碼技術的支援](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure)

## <a name="references"></a>參考資料

* [深入瞭解 Azure 中適用于 Windows Server 2008/R2 的免費延伸安全性更新](https://www.microsoft.com/cloud-platform/windows-server-2008)
* [深入瞭解 Azure 中 Windows Server 2008 SP2 32 位特製化映射的支援](/windows-server/get-started/uploading-specialized-ws08-image-to-azure)
* [深入瞭解使用 Azure Site Recovery 將 Windows Server 2008 映射遷移至 Azure 的支援](../../site-recovery/migrate-tutorial-windows-server-2008.md)
* [深入瞭解 Azure 擴充功能支援的作業系統](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems)
* [深入瞭解如何在 Microsoft Azure 上執行 Windows Server 2003](https://support.microsoft.com/help/3206074/running-windows-server-2003-on-microsoft-azure)

## <a name="next-steps"></a>後續步驟

如果您在本文的任何時間點需要更多協助，請洽詢 [MSDN azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 Azure 專家。

或者，提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。
