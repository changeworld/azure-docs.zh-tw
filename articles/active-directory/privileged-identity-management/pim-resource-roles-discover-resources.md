---
title: 在 PIM 中探索要管理的 Azure 資源-Azure AD |Microsoft Docs
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74022881"
---
# <a name="discover-azure-resources-to-manage-in-privileged-identity-management"></a>在 Privileged Identity Management 中探索要管理的 Azure 資源

使用 Azure Active Directory （Azure AD） Privileged Identity Management （PIM），您可以改善 Azure 資源的保護。 這對於已經使用 Privileged Identity Management 保護 Azure AD 角色的組織，以及想要保護生產資源的管理群組和訂用帳戶擁有者很有説明。

當您第一次設定適用于 Azure 資源的 Privileged Identity Management 時，您需要探索並選取要使用 Privileged Identity Management 保護的資源。 您可以使用 Privileged Identity Management 來管理的資源數目並無任何限制。 不過，我們建議從您最重要的 (生產) 資源開始。

## <a name="discover-resources"></a>探索資源

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 開啟**Azure AD Privileged Identity Management**。

1. 選取 [Azure 資源]****。

    如果這是您第一次使用 Azure 資源 Privileged Identity Management，您會看到 [**探索資源**] 頁面。

    ![[探索資源] 窗格沒有列出第一次體驗的資源](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    如果您組織中的另一個系統管理員已經在 Privileged Identity Management 中管理 Azure 資源，您會看到目前受管理的資源清單。

    ![[探索資源] 窗格列出目前受管理的資源](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. 選取 [**探索資源**] 以啟動探索體驗。

    ![探索窗格，列出可以管理的資源，例如訂用帳戶和管理群組](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. 在 [**探索**] 頁面上，使用 [**資源狀態篩選**]，然後**選取 [資源類型**] 來篩選您擁有其寫入權限的管理群組或訂用帳戶。 一開始可能最容易的做法是先使用 [全部]****。

    您只能使用 Privileged Identity Management 搜尋並選取要管理的管理群組或訂用帳戶資源。 當您管理 Privileged Identity Management 中的管理群組或訂用帳戶時，您也可以管理其子資源。

1. 選取您想要管理的任何非受控資源旁的核取方塊。

1. 選取 [**管理資源**] 以開始管理選取的資源。

    > [!NOTE]
    > 管理群組或訂用帳戶一經管理，就無法處於非受控狀態。 這可防止其他資源管理員移除 Privileged Identity Management 設定。

    ![已選取資源並反白顯示 [管理資源] 選項的探索窗格](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. 如果您看到一則訊息，確認已選取要管理之資源的上架，請選取 **[是]**。

    ![確認要讓選取的資源上線以進行管理的訊息](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>後續步驟

- [在 Privileged Identity Management 中設定 Azure 資源角色設定](pim-resource-roles-configure-role-settings.md)
- [在 Privileged Identity Management 中指派 Azure 資源角色](pim-resource-roles-assign-roles.md)
