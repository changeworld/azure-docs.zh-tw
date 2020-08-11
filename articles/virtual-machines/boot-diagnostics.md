---
title: Azure 開機診斷
description: Azure 開機診斷和受控開機診斷的總覽
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: e2ba5d909a3aa43921f52295d2f7216aac76bc32
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067081"
---
# <a name="azure-boot-diagnostics"></a>Azure 開機診斷

開機診斷是 Azure 虛擬機器 (VM) 的偵錯工具功能，可讓您診斷 VM 開機失敗。 開機診斷可讓使用者在開機時，藉由收集序列記錄資訊和螢幕擷取畫面，觀察其 VM 的狀態。

## <a name="boot-diagnostics-view"></a>開機診斷視圖
位於 [虛擬機器] 分頁中，[開機診斷] 選項位於 Azure 入口網站中的 [*支援與疑難排解*] 區段底下。 選取 [開機診斷] 將會顯示幕幕快照和序列記錄檔資訊。 序列記錄檔包含核心訊息，螢幕擷取畫面則是 Vm 目前狀態的快照。 根據 VM 是否正在執行 Windows 或 Linux，會決定預期的螢幕擷取畫面外觀。 對於 Windows，使用者會看到桌面背景，而針對 Linux，使用者會看到登入提示。

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Linux 開機診斷的螢幕擷取畫面":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Windows 開機診斷的螢幕擷取畫面":::


## <a name="limitations"></a>限制
- 開機診斷僅適用于 Azure Resource Manager Vm。 
- 開機診斷不支援高階儲存體帳戶，如果將 premium 儲存體帳戶用於開機診斷，使用者將會 `StorageAccountTypeNotSupported` 在啟動 VM 時收到錯誤。 

## <a name="next-steps"></a>後續步驟

深入瞭解[Azure 序列主控台](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview)，以及如何使用開機診斷對[azure 中的虛擬機器進行疑難排解](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)。
