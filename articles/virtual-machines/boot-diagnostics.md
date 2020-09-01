---
title: Azure 開機診斷
description: Azure 開機診斷和受控開機診斷的總覽
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: d425953b278a98af35a172d8777ab758db52709e
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89257750"
---
# <a name="azure-boot-diagnostics"></a>Azure 開機診斷

開機診斷是 Azure 虛擬機器 (VM) 的偵錯工具，可讓您診斷 VM 開機失敗。 開機診斷可讓使用者藉由收集序列記錄資訊和螢幕擷取畫面，來觀察其 VM 的狀態。

## <a name="boot-diagnostics-storage-account"></a>開機診斷儲存體帳戶
在 Azure 入口網站中建立 VM 時，預設會啟用開機診斷。 建議的開機診斷體驗是使用受控儲存體帳戶，因為它在建立 Azure VM 時，會大幅改善效能。 這是因為將使用 Azure 受控儲存體帳戶，以移除建立新的使用者儲存體帳戶來儲存開機診斷資料所需的時間。

替代的開機診斷體驗是使用使用者管理的儲存體帳戶。 使用者可以建立新的儲存體帳戶或使用現有的儲存體帳戶。

> [!IMPORTANT]
> Azure 客戶將不需支付使用受控儲存體帳戶于2020年10月的開機診斷 associted 的儲存體成本。

## <a name="boot-diagnostics-view"></a>開機診斷視圖
位於虛擬機器分頁中的 [開機診斷] 選項位於 Azure 入口網站的 [ *支援與疑難排解* ] 區段下。 選取 [開機診斷] 將會顯示幕幕快照和序列記錄檔資訊。 序列記錄檔包含核心訊息，而螢幕擷取畫面則是 Vm 目前狀態的快照集。 根據 VM 是否正在執行 Windows 或 Linux，判斷預期的螢幕擷取畫面。 針對 Windows，使用者會看到桌面背景，而針對 Linux，使用者會看到登入提示。

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Linux 開機診斷的螢幕擷取畫面":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Windows 開機診斷的螢幕擷取畫面":::


## <a name="limitations"></a>限制
- 開機診斷僅適用于 Azure Resource Manager Vm。 
- 開機診斷不支援 premium 儲存體帳戶，如果用於開機診斷的高階儲存體帳戶，使用者將會 `StorageAccountTypeNotSupported` 在啟動 VM 時收到錯誤。 
- Azure 序列主控台目前與用於開機診斷的受控儲存體帳戶不相容。 深入瞭解 [Azure 序列主控台](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview)。

## <a name="next-steps"></a>後續步驟

深入瞭解 [Azure 序列主控台](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview) ，以及如何使用開機診斷對 [azure 中的虛擬機器進行疑難排解](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)。
