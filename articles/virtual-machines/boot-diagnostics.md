---
title: Azure 開機診斷
description: Azure 開機診斷和受控開機診斷的總覽
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 9ffd5a6397fa01efcf9aece93333dcb5e5b418cc
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88042950"
---
# <a name="azure-boot-diagnostics"></a>Azure 開機診斷

開機診斷是 Azure 虛擬機器 (VM) 的偵錯工具功能，可讓您診斷 VM 開機失敗。 開機診斷可讓使用者在開機時，藉由收集序列記錄資訊和螢幕擷取畫面，觀察其 VM 的狀態。

## <a name="boot-diagnostics-storage-account"></a>開機診斷儲存體帳戶
根據預設，會針對使用 Azure 入口網站建立的所有 Vm 啟用開機診斷，並利用受控儲存體帳戶。 藉由使用受控儲存體帳戶，使用者就能在 VM 部署時間中獲得大幅的改進。 基於這個理由，我們建議客戶針對所有 Vm 使用具有受控儲存體帳戶的開機診斷。

> [!NOTE]
> 選擇使用具有受控儲存體帳戶的開機診斷，直到2020年10月1日前，Azure 客戶才需要支付儲存體費用。

替代的開機診斷體驗是使用自訂的儲存體帳戶。 使用者可以建立新的儲存體帳戶，或使用現有的帳戶。 如需有關自訂儲存體帳戶的詳細資訊，請參閱[儲存體帳戶總覽](https://docs.microsoft.com/azure/storage/common/storage-account-overview)。

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-portal.png" alt-text="示範如何啟用開機診斷的螢幕擷取畫面":::

## <a name="boot-diagnostics-view"></a>開機診斷視圖
位於 [虛擬機器] 分頁中，[開機診斷] 選項位於 Azure 入口網站中的 [*支援與疑難排解*] 區段底下。 選取 [開機診斷] 將會顯示幕幕快照和序列記錄檔資訊。 序列記錄檔包含核心訊息，螢幕擷取畫面則是 Vm 目前狀態的快照。 根據 VM 是否正在執行 Windows 或 Linux，會決定預期的螢幕擷取畫面外觀。 對於 Windows，使用者會看到桌面背景，而針對 Linux，使用者會看到登入提示。

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Linux 開機診斷的螢幕擷取畫面":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Windows 開機診斷的螢幕擷取畫面":::


## <a name="limitations"></a>限制
- 開機診斷僅適用于 Azure Resource Manager (ARM) Vm。 
- 開機診斷不支援高階儲存體帳戶，如果將 premium 儲存體帳戶用於開機診斷，使用者將會 `StorageAccountTypeNotSupported` 在啟動 VM 時收到錯誤。 
- Azure 序列主控台目前不支援使用受控儲存體帳戶來進行開機診斷。

## <a name="next-steps"></a>後續步驟

深入瞭解[Azure 序列主控台](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview)，以及如何使用開機診斷對[azure 中的虛擬機器進行疑難排解](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)。
