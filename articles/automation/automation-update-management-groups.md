---
title: 將動態組與 Azure 更新管理一起使用
description: 本文介紹動態組如何使用 Azure 自動化更新管理。
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 678b3f361e4456a2c482896f7d7dc20d530b917b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75420404"
---
# <a name="use-dynamic-groups-with-update-management"></a>搭配更新管理使用動態群組

更新管理提供了針對更新部署的 Azure 或非 Azure VM 的動態組的能力。 這些組在部署時進行評估，因此您不必編輯部署來添加電腦。

## <a name="azure-machines"></a>Azure 電腦

這些群組會由查詢定義，當更新部署開始時，就會評估該群組的成員。 動態組不適用於經典 VM。 定義查詢時，可以使用以下項一起填充動態組：

* 訂用帳戶
* 資源群組
* 位置
* Tags

![選取群組](./media/automation-update-management/select-groups.png)

若要預覽動態群組的結果，請按一下 [預覽]**** 按鈕。 此預覽會顯示該時間的群組成員資格，在此範例中，我們搜尋的是 **Role** 標記等於 **BackendServer** 的機器。 如果有更多機器新增此標記，系統就會將它們都新增至針對該群組進行的所有未來部署。

![預覽群組](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>非 Azure 電腦

對於非 Azure 電腦，保存的搜索也稱為電腦群組，用於創建動態組。 要瞭解如何創建已保存的搜索，請參閱[創建電腦群組](../azure-monitor/platform/computer-groups.md#creating-a-computer-group)。 創建群組後，您可以從已保存的搜索清單中選擇該組。 按一下 **"預覽**"以預覽當時保存的搜索中的電腦。

![選取群組](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>後續步驟

創建動態組後，可以[創建更新部署](automation-tutorial-update-management.md)
