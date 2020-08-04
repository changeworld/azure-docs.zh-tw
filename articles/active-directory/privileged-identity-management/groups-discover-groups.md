---
title: 在 Privileged Identity Management Azure AD 中識別要管理的群組 |Microsoft Docs
description: 瞭解如何在 Privileged Identity Management （PIM）中，將可指派角色的群組上線，以作為特殊許可權存取群組來管理。
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
ms.date: 08/03/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76f7b61b6fb065409a2fe4b2ed8f599f321cd1db
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542264"
---
# <a name="bring-a-privileged-access-groups-preview-into-privileged-identity-management"></a>將特殊許可權存取群組（預覽）帶入 Privileged Identity Management

在 Azure Active Directory （Azure AD）中，您可以將 Azure AD 內建角色指派給雲端群組，以簡化您管理角色指派的方式。 若要保護 Azure AD 角色並保護存取安全，您現在可以使用 Privileged Identity Management （PIM）來管理這些群組成員或擁有者的即時存取權。 若要在 Privileged Identity Management 中管理 Azure AD 角色可指派的群組做為特殊許可權存取群組，您必須在 PIM 中將其納入管理。

## <a name="identify-groups-to-manage"></a>識別要管理的群組

如在[Azure Active Directory 中建立角色可指派的群組](../users-groups-roles/roles-groups-create-eligible.md)中所述，您可以在 Azure AD 中建立可指派角色的群組。 您是群組的擁有者，可使用 Privileged Identity Management 將其納入管理。

1. 使用特殊許可權角色管理員角色許可權登[入 Azure AD](https://aad.portal.azure.com) 。
1. 選取 [**群組**]，然後選取您想要在 PIM 中管理的角色可指派群組。 您可以搜尋和篩選清單。

    ![在 PIM 中尋找要管理的可指派角色群組](./media/groups-discover-groups/groups-list-in-azure-ad.png)

1. 開啟群組，然後選取 [特殊許可權**存取（預覽）**]。

    ![開啟 Privileged Identity Management 體驗](./media/groups-discover-groups/groups-discover-groups.png)

1. 開始管理 PIM 中的指派。

    ![管理 Privileged Identity Management 中的指派](./media/groups-discover-groups/groups-bring-under-management.png)

> [!NOTE]
> 一旦特殊許可權存取群組受到管理，就無法進行管理。 這可防止其他資源管理員移除 Privileged Identity Management 設定。

## <a name="next-steps"></a>後續步驟

- [在 Privileged Identity Management 中設定特殊許可權存取群組指派](pim-resource-roles-configure-role-settings.md)
- [指派 Privileged Identity Management 中的特殊許可權存取群組](pim-resource-roles-assign-roles.md)
