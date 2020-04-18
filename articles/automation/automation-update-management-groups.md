---
title: 將動態群組與 Azure 自動化更新管理一起使用
description: 本文介紹動態組如何使用 Azure 自動化更新管理。
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 29a72eb1fe7b8be18cd2160fc63160e408378585
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617446"
---
# <a name="use-dynamic-groups-with-update-management"></a>搭配更新管理使用動態群組

更新管理允許您針對一組動態的 Azure 或非 Azure VM 進行更新部署。 這些組由查詢定義,在部署時進行評估,因此您不必編輯部署來添加計算機。

## <a name="azure-machines"></a>Azure 電腦

動態組不適用於經典 VM。 定義查詢時,可以使用以下項目一起填充動態群組:

* 訂用帳戶
* 資源群組
* 位置
* Tags

![選取群組](./media/automation-update-management/select-groups.png)

要預覽動態組的結果,請單擊 **「預覽**」。。 預覽顯示當前時間的組成員身份。 在此示例中,我們搜索具有組**BackendServer**的`Role`標記 的電腦。 如果添加了更多計算機,則它們將添加到針對該組的任何將來部署中。

![預覽群組](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>非 Azure 電腦

對於非 Azure 計算機,保存的搜索(也稱為計算機組)用於創建動態組。 要瞭解如何建立已儲存的搜尋,請參考[建立電腦群組](../azure-monitor/platform/computer-groups.md#creating-a-computer-group)。 建立群組後,您可以從已儲存的搜索清單中選擇該組。 按下 **「預覽**」以預覽當時保存的搜索中的電腦。

![選取群組](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>後續步驟

建立動態群組後,可以[建立更新部署](automation-tutorial-update-management.md)。
