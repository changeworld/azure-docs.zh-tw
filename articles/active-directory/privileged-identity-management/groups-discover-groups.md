---
title: 在 PIM 中探索要管理的角色可指派群組-Azure AD |Microsoft Docs
description: 瞭解如何在 Privileged Identity Management （PIM）中探索要管理為特殊許可權存取群組的角色可指派群組。
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
ms.date: 07/27/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8b66a41f0f1aa48433dac77fca56d5e2877989a
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87506000"
---
# <a name="discover-privileged-access-groups-preview-to-manage-in-privileged-identity-management"></a>探索要在 Privileged Identity Management 中管理的特殊許可權存取群組（預覽）

在 Azure Active Directory （Azure AD）中，您可以將 Azure AD 內建角色指派給雲端群組，以簡化角色指派的管理。 現在您可以使用 Privileged Identity Management （PIM）來指派這些群組的成員資格或擁有權，以保護 Azure AD 角色及保護存取安全。 在 Privileged Identity Management 中管理 Azure AD 角色可指派的群組做為特殊許可權存取群組之前，您必須先探索它，並將它帶入 PIM 中的管理。

## <a name="discover-resources"></a>探索資源

1. 使用特殊許可權角色管理員角色許可權登[入 Azure AD](https://aad.portal.azure.com) 。
1. 在 Azure AD 中建立可指派角色的群組。 您必須是群組的擁有者，才能使用 Privileged Identity Management 來探索及管理它。
1. 開啟**Privileged Identity Management**。
1. 選取 **[特殊許可權存取（預覽）**]。

    ![探索第一次體驗的群組命令](./media/groups-discover-groups/groups-discover-groups.png)

1. 選取 [**探索群組**]。
1. 依組名搜尋。
1. 選取您的群組，然後選取 [**管理群組**]，將其帶入 PIM 管理。

    ![探索第一次使用時未列出任何資源的群組](./media/groups-discover-groups/groups-bring-under-management.png)

    > [!NOTE]
    > 一旦特殊許可權存取群組受到管理，就無法進行管理。 這可防止其他資源管理員移除 Privileged Identity Management 設定。

1. 如果您看到一則訊息，確認已選取要管理之資源的上架，請選取 **[是]**。

## <a name="next-steps"></a>後續步驟

- [在 Privileged Identity Management 中設定特殊許可權存取群組指派](pim-resource-roles-configure-role-settings.md)
- [指派 Privileged Identity Management 中的特殊許可權存取群組](pim-resource-roles-assign-roles.md)
