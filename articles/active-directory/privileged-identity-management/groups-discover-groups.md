---
title: 識別要在 Privileged Identity Management Azure AD 中管理的群組 |Microsoft Docs
description: 瞭解如何在 Privileged Identity Management (PIM) 中將角色可指派的群組上架，以管理為特殊許可權的存取群組。
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
ms.openlocfilehash: 68795033d36ad0bcb36b7cd81cea0d848ecd5113
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89049004"
---
# <a name="bring-privileged-access-groups-preview-into-privileged-identity-management"></a>將特殊許可權存取群組 (預覽版) 至 Privileged Identity Management

在 Azure Active Directory (Azure AD) ，您可以將 Azure AD 內建角色指派給雲端群組，以簡化管理角色指派的方式。 若要保護 Azure AD 角色以及保護存取的安全，您現在可以使用 Privileged Identity Management (PIM) 來管理這些群組的成員或擁有者的即時存取。 若要在 Privileged Identity Management 中管理 Azure AD 角色可指派群組作為特殊許可權存取群組，您必須將其置於 PIM 的管理下。

## <a name="identify-groups-to-manage"></a>識別要管理的群組

您可以在 Azure AD 中建立可指派角色的群組，如 [Azure Active Directory 中建立角色可指派的群組](../users-groups-roles/roles-groups-create-eligible.md)中所述。 您必須是群組的擁有者，才能將它納入 Privileged Identity Management 的管理之下。

1. 使用特殊許可權角色管理員角色許可權登[入 Azure AD](https://aad.portal.azure.com) 。
1. 選取 [ **群組** ]，然後選取您想要在 PIM 中管理的角色可指派群組。 您可以搜尋和篩選清單。

    ![尋找可在 PIM 中管理的角色可指派群組](./media/groups-discover-groups/groups-list-in-azure-ad.png)

1. 開啟群組，然後選取 [特殊許可權 **存取] (預覽) **。

    ![開啟 Privileged Identity Management 體驗](./media/groups-discover-groups/groups-discover-groups.png)

1. 開始在 PIM 中管理指派。

    ![管理 Privileged Identity Management 中的指派](./media/groups-discover-groups/groups-bring-under-management.png)

> [!NOTE]
> 管理特殊許可權存取群組之後，就無法將其從管理中移除。 這可防止其他資源管理員移除 Privileged Identity Management 設定。

## <a name="next-steps"></a>後續步驟

- [在 Privileged Identity Management 中設定特殊許可權存取群組指派](pim-resource-roles-configure-role-settings.md)
- [在 Privileged Identity Management 中指派特殊許可權存取群組](pim-resource-roles-assign-roles.md)
