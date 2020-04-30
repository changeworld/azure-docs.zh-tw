---
title: 搭配 Azure 自動化使用動態群組更新管理
description: 本文說明動態群組如何與 Azure 自動化更新管理搭配使用。
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 29a72eb1fe7b8be18cd2160fc63160e408378585
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617446"
---
# <a name="use-dynamic-groups-with-update-management"></a>搭配更新管理使用動態群組

更新管理可讓您將 Azure 或非 Azure Vm 的動態群組設為目標，以進行更新部署。 這些由查詢定義的群組會在部署期間進行評估，因此您不需要編輯部署來新增機器。

## <a name="azure-machines"></a>Azure 機器

動態群組無法與傳統 Vm 搭配使用。 定義查詢時，可以搭配使用下列專案來填入動態群組：

* 訂用帳戶
* 資源群組
* 位置
* Tags

![選取群組](./media/automation-update-management/select-groups.png)

若要預覽動態群組的結果，請按一下 [**預覽**]。 預覽會顯示目前時間的群組成員資格。 在此範例中，我們會搜尋具有群組**BackendServer**標記`Role`的機器。 如果有更多電腦新增此標籤，則會將其新增至該群組的任何未來部署。

![預覽群組](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>非 Azure 機器

針對非 Azure 機器，已儲存的搜尋（也稱為電腦群組）是用來建立動態群組。 若要瞭解如何建立已儲存的搜尋，請參閱[建立電腦群組](../azure-monitor/platform/computer-groups.md#creating-a-computer-group)。 建立群組之後，您可以從已儲存的搜尋清單中加以選取。 按一下 [**預覽**] 以在當時已儲存的搜尋中預覽電腦。

![選取群組](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>後續步驟

建立動態群組之後，您可以[建立更新部署](automation-tutorial-update-management.md)。
