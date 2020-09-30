---
title: 在 PIM 中探索要管理的 Azure 資源-Azure AD |Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中探索要管理的 Azure 資源。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 09/29/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: be48e6e175beae751003895e60322a458cfbc8bd
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91568075"
---
# <a name="discover-azure-resources-to-manage-in-privileged-identity-management"></a>探索要在 Privileged Identity Management 中管理的 Azure 資源

您可以使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) ，來改善 Azure 資源的保護。 這有助於：

- 已使用 Privileged Identity Management 保護 Azure AD 角色的組織
- 嘗試保護生產資源的管理群組和訂用帳戶擁有者

當您第一次設定 Azure 資源的 Privileged Identity Management 時，您需要探索並選取要使用 Privileged Identity Management 保護的資源。 您可以使用 Privileged Identity Management 管理的資源數量沒有任何限制。 不過，我們建議您從最重要的生產資源開始。

## <a name="discover-resources"></a>探索資源

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 選取 [ **Azure 資源**]。

    如果這是您第一次使用 Azure 資源的 Privileged Identity Management，您會看到 [ **探索資源** ] 頁面。

    ![探索沒有任何資源的資源窗格以取得第一次經驗](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    如果您組織中的其他系統管理員已經在 Privileged Identity Management 中管理 Azure 資源，您將會看到目前正在管理的資源清單。

    ![探索資源窗格，列出目前正在管理的資源](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. 選取 [ **探索資源** ] 以啟動探索體驗。

    ![[探索] 窗格會列出可管理的資源，例如訂用帳戶和管理群組](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. 在 [ **探索** ] 頁面上，使用 **資源狀態篩選** 器並 **選取 [資源類型** ]，以篩選您有寫入權限的管理群組或訂用帳戶。 一開始可能最容易的做法是先使用 [全部]****。

   您可以在 Privileged Identity Management 中搜尋並選取要管理的管理群組或訂用帳戶資源。 當您在 Privileged Identity Management 中管理管理群組或訂用帳戶時，也可以管理其子系資源。

   > [!Note]
   > 當您將新的子 Azure 資源新增至 PIM 管理的管理群組時，您可以在 PIM 中搜尋子資源，使其成為受管理的管理群組。

1. 選取任何您想要管理的非受控資源。

1. 選取 [ **管理資源** ] 以開始管理選取的資源。

    > [!NOTE]
    > 管理群組或訂用帳戶一旦受到管理，就無法進行管理。 這可防止其他資源管理員移除 Privileged Identity Management 設定。

    ![已選取資源並反白顯示 [管理資源] 選項的 [探索] 窗格](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. 如果您看到一則訊息，確認所選資源的上架進行管理，請選取 **[是]**。

    ![確認要將選取的資源上線以進行管理的訊息](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>後續步驟

- [在 Privileged Identity Management 中設定 Azure 資源角色設定](pim-resource-roles-configure-role-settings.md)
- [在 Privileged Identity Management 中指派 Azure 資源角色](pim-resource-roles-assign-roles.md)
