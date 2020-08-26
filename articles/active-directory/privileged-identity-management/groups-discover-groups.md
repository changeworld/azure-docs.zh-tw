---
title: 管理可指派角色的群組作為特殊許可權存取群組-Azure AD |Microsoft Docs
description: 同意將角色可指派的群組上架，以在 Privileged Identity Management (PIM) 中管理為特殊許可權的存取群組。
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
ms.date: 08/17/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: a707c66f2562fe442c58ce6292e51c4e67a20ae2
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88869521"
---
# <a name="manage-privileged-access-groups-preview-in-privileged-identity-management"></a>在 Privileged Identity Management 中管理特殊許可權存取群組 (預覽) 

在 Azure Active Directory (Azure AD) ，您可以將 Azure AD 內建角色指派給雲端群組，以簡化管理角色指派的方式。 若要保護 Azure AD 角色以及保護存取的安全，您現在可以使用 Privileged Identity Management (PIM) 來管理這些群組的成員或擁有者的即時存取。 若要在 Privileged Identity Management 中管理 Azure AD 角色可指派群組作為特殊許可權存取群組，您必須將其置於 PIM 的管理下。

## <a name="identify-groups-to-manage"></a>識別要管理的群組

您可以在 Azure AD 中建立可指派角色的群組，如 [Azure Active Directory 中建立角色可指派的群組](../users-groups-roles/roles-groups-create-eligible.md)中所述。 您必須是群組的擁有者，才能將它納入 Privileged Identity Management 的管理之下。

1. 使用特殊許可權角色管理員許可權登[入 Azure AD](https://aad.portal.azure.com) 。
1. 選取 [ **群組** ]，然後選取您想要管理的角色可指派群組。 您可以搜尋或篩選清單。

    ![尋找可在 PIM 中管理的角色可指派群組](./media/groups-discover-groups/groups-list-in-azure-ad.png)

1. 開啟群組，然後選取 [特殊許可權 **存取] (預覽) **。

    ![開啟 Privileged Identity Management 體驗](./media/groups-discover-groups/groups-discover-groups.png)

1. 如果您的群組尚未在 PIM 中進行管理，請選取 [啟用特殊許可權 **存取** 以同意管理]。 只有群組的全域管理員或擁有者可以授與此同意。

    ![同意在 Privileged Identity Management 中管理群組（如有需要）](./media/groups-discover-groups/consent-page.png)

1. 開始在 PIM 中管理指派。

    ![管理 Privileged Identity Management 中的指派](./media/groups-discover-groups/groups-bring-under-management.png)

> [!NOTE]
> 管理特殊許可權存取群組之後，就無法將其從管理中移除。 這可防止其他系統管理員移除 Privileged Identity Management 設定。

## <a name="next-steps"></a>後續步驟

- [在 Privileged Identity Management 中設定特殊許可權存取群組指派](pim-resource-roles-configure-role-settings.md)
- [在 Privileged Identity Management 中指派特殊許可權存取群組](pim-resource-roles-assign-roles.md)
