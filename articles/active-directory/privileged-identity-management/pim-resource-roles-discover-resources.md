---
title: 在 PIM 中發現要管理的 Azure 資源 - Azure AD |微軟文檔
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中探索要管理的 Azure 資源。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87da43100c7494937ddc842e0f903ba3a360959e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022881"
---
# <a name="discover-azure-resources-to-manage-in-privileged-identity-management"></a>發現要在特權標識管理中管理的 Azure 資源

使用 Azure 活動目錄 （Azure AD） 特權標識管理 （PIM），可以改進對 Azure 資源的保護。 這對已經使用特權標識管理來保護 Azure AD 角色的組織以及希望保護生產資源的管理組和訂閱擁有者很有説明。

首次為 Azure 資源設置特權標識管理時，需要發現並選擇使用特權標識管理保護的資源。 您可以使用特權標識管理管理的資源數量沒有限制。 不過，我們建議從您最重要的 (生產) 資源開始。

## <a name="discover-resources"></a>探索資源

1. 登錄到 Azure[門戶](https://portal.azure.com/)。

1. 打開**Azure AD 特權標識管理**。

1. 選取 [Azure 資源]****。

    如果這是您第一次對 Azure 資源使用特權標識管理，您將看到 **"發現資源"** 頁。

    ![發現資源窗格，首次未列出任何資源](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    如果組織中的另一位管理員已在特權標識管理中管理 Azure 資源，您將看到當前正在管理的資源的清單。

    ![發現資源窗格列出當前正在管理的資源](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. 選擇 **"發現資源**"以啟動發現體驗。

    ![發現窗格列出可管理的資源，如訂閱和管理組](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. 在 **"發現"** 頁上，使用 **"資源狀態"篩選器**和 **"選擇資源類型**"來篩選您擁有寫入權限的管理組或訂閱。 一開始可能最容易的做法是先使用 [全部]****。

    您只能搜索和選擇管理組或訂閱資源，以便使用特權標識管理進行管理。 在特權標識管理中管理管理組或訂閱時，還可以管理其子資源。

1. 選擇要管理的任何非託管資源旁邊的核取方塊。

1. 選擇 **"管理資源**"以開始管理所選資源。

    > [!NOTE]
    > 管理組或訂閱管理後，無法將其託管。 這可以防止其他資源管理員刪除特權標識管理設置。

    ![選擇資源並突出顯示"管理資源"選項的發現窗格](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. 如果您看到一條消息以確認所選資源的加入以進行管理，請選擇"**是**"。

    ![確認要將所選資源用於管理的消息](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>後續步驟

- [在特權標識管理中配置 Azure 資源角色設置](pim-resource-roles-configure-role-settings.md)
- [在特權標識管理中分配 Azure 資源角色](pim-resource-roles-assign-roles.md)
