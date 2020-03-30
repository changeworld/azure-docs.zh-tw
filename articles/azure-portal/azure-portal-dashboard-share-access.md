---
title: 使用基於角色的存取控制共用 Azure 門戶儀表板
description: 本文說明如何在 Azure 入口網站中使用角色型存取控制來共用儀表板。
services: azure-portal
documentationcenter: ''
author: mgblythe
manager: mtillman
editor: tysonn
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/23/2020
ms.author: mblythe
ms.openlocfilehash: 711e8a7ae31888c9754252d88404d90f24e8030e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131988"
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>使用角色型存取控制來共用 Azure 儀表板

設定儀表板之後，您可以將它發佈，並與組織中的其他使用者共用。 允許其他人使用基於 Azure[角色的存取控制](../role-based-access-control/role-assignments-portal.md)（RBAC） 查看儀表板。 將使用者或使用者組分配給角色。 該角色定義這些使用者是否可以查看或修改已發佈的儀表板。

所有已發佈的儀表板都作為 Azure 資源實現。 它們作為可管理的專案存在於訂閱中，並包含在資源組中。 從存取控制的觀點來看，儀表板與其他資源 (例如虛擬機器或儲存體帳戶) 並無不同。

> [!TIP]
> 儀表板上的個別圖格會根據其顯示的資源，強制執行自己的存取控制需求。 您可以廣泛共用儀表板，同時保護各個磁貼上的資料。
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>了解儀表板的存取控制

若使用角色型存取控制 (RBAC)，您可以將使用者指派給三個不同範圍層級的角色︰

* 訂用帳戶
* 資源群組
* resource

從訂閱向下分配給資源的許可權。 發佈的儀表板是一種資源。 您可能已經將使用者分配到適用于已發佈的儀表板的訂閱的角色。

假設您有 Azure 訂用帳戶，而且已為小組各種成員指派訂用帳戶的*擁有者*、*參與者*或*讀取者*角色。 作為擁有者或參與者的使用者可以列出、查看、創建、修改或刪除訂閱中的儀表板。 讀者使用者可以列出和查看儀表板，但不能修改或刪除儀表板。 具有讀者存取權限的使用者可以對已發佈的儀表板進行本地編輯，例如排除問題故障時，但不能將這些更改發佈回伺服器。 他們可以為自己製作儀表板的私有副本。

您還可以將許可權分配給包含多個儀表板的資源組或單個儀表板的許可權。 例如，您可能會決定，一群使用者應具有有限的訂用帳戶權限，但對特定儀表板則具有更大範圍的存取權。 將這些使用者分配給該儀表板的角色。

## <a name="publish-dashboard"></a>發佈儀表板

假設您配置了要與訂閱中的一組使用者共用的儀表板。 以下步驟演示如何將儀表板共用給名為存儲管理器的組。 您可以隨心所欲地命名群組。 有關詳細資訊，請參閱在[Azure 活動目錄中管理組](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

在指派存取權之前，您必須先發佈儀表板。

1. 在儀表板中選取 [共用] ****。

    ![為儀表板選擇共用](./media/azure-portal-dashboard-share-access/share-dashboard-for-access-control.png)

1. 在 **"共用 + 存取控制"中**，選擇 **"發佈**"。

    ![發佈儀表板](./media/azure-portal-dashboard-share-access/publish-dashboard-for-access-control.png)

     預設情況下，共用會將儀表板發佈到名為**儀表板**的資源組。 要選擇其他資源組，請清除該核取方塊。

儀表板現已發佈。 如果從訂閱繼承的許可權合適，則無需執行任何其他操作。 組織中的其他使用者可以根據其訂閱級別角色訪問和修改儀表板。

## <a name="assign-access-to-a-dashboard"></a>指派儀表板存取權

您可以將一組使用者分配給該儀表板的角色。

1. 發佈儀表板後，選擇"**共用**"或 **"取消共用**"選項以訪問**共用 + 存取控制**。

1. 在 **"共用 + 存取控制"中**，選擇 **"管理使用者**"。

    ![管理儀表板的使用者](./media/azure-portal-dashboard-share-access/manage-users-for-access-control.png)

1. 選擇**角色指派**以查看已為此儀表板分配角色的現有使用者。

1. 要添加新使用者或組，請選擇"**添加**"，然後**添加角色指派**。

    ![添加使用者以訪問儀表板](./media/azure-portal-dashboard-share-access/manage-users-existing-users.png)

1. 選擇表示授予許可權的角色。 在此範例中，請選取 [參與者] ****。

1. 選擇要分配給角色的使用者或組。 如果在清單中看不到要查找的使用者或組，請使用搜索框。 可用組的清單取決於您在活動目錄中創建的組。

1. 完成添加使用者或組後，選擇"**保存**"。

## <a name="next-steps"></a>後續步驟

* 有關角色清單，請參閱[Azure 資源的內置角色](../role-based-access-control/built-in-roles.md)。
* 要瞭解如何管理資源，請參閱[使用 Azure 門戶管理 Azure 資源](resource-group-portal.md)。

