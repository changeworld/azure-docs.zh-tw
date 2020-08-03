---
title: 指派特殊許可權存取群組的合格擁有者和成員-Azure Active Directory
description: 瞭解如何在 Azure AD Privileged Identity Management （PIM）中指派合格的擁有者或角色可指派群組的成員。
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
ms.date: 07/27/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1dcc98366e37455f462fe1a0740aa161201912f
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87506221"
---
# <a name="assign-eligibility-for-a-privileged-access-group-preview-in-privileged-identity-management"></a>在 Privileged Identity Management 中指派特殊許可權存取群組（預覽）的資格

Azure Active Directory （Azure AD） Privileged Identity Management （PIM）可協助您管理在 Azure AD 中指派給特殊許可權存取群組的資格和啟用。 您可以將資格指派給群組的成員或擁有者。

>[!NOTE]
>符合特殊許可權存取群組之成員資格或擁有權的每位使用者，都必須具有 Azure AD Premium P2 授權。 如需詳細資訊，請參閱[使用 Privileged Identity Management 的授權需求](subscription-requirements.md)。

## <a name="assign-an-owner-or-member-of-a-group"></a>指派群組的擁有者或成員

請遵循下列步驟，讓使用者有資格成為特殊許可權存取群組的成員或擁有者。

1. 使用[特殊權限角色管理員](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)角色許可權登入 Azure 入口網站中的[Privileged Identity Management](https://portal.azure.com/) 。

    如需如何授與其他系統管理員存取權來管理 Privileged Identity Management 的相關資訊，請參閱將[存取權授與其他管理員以管理 Privileged Identity Management](pim-how-to-give-access-to-pim.md)。

1. 開啟**Azure AD Privileged Identity Management**。

1. 選取 **[特殊許可權存取（預覽）**]。

1. 您可以搜尋組名並使用 [**群組類型**] 來篩選清單，以選取您要管理的群組。

    ![要管理的特殊許可權存取群組清單](./media/groups-assign-member-owner/privileged-access-list.png)

1. 在 [**管理**] 下，選取 [**指派**]。

1. 選取 [**新增指派**]。

    ![新增指派窗格](./media/groups-assign-member-owner/groups-add-assignment.png)

1. 選取您想要為特殊許可權存取群組提供資格的成員或擁有者。

    ![選取成員或群組窗格](./media/groups-assign-member-owner/add-assignments.png)

1. 選取 **[下一步]** 以設定成員資格或擁有權的持續時間。

    ![選取成員或群組窗格](./media/groups-assign-member-owner/assignment-duration.png)

1. 在 [指派類型]**** 清單中選取 [合格]**** 或 [有效]****。 特殊許可權存取群組提供兩種不同的指派類型：

    - **合格**的指派會要求角色的成員執行動作以使用角色。 動作可能包含執行多重要素驗證 (MFA) 檢查、提供業務理由，或是向指定的核准者要求核准。

    - 作用中**的指派不**需要成員執行任何動作來使用角色。 指派為有效的成員隨時具有指派給角色的權限。

1. 如果指派應為永久性（永久合格或永久指派），請選取 [**永久**] 核取方塊。 視您組織的設定而定，核取方塊可能不會出現，或可能無法編輯。

1. 完成後，選取 [**指派**]。

1. 若要建立新的角色指派，**請選取 [新增]**。 狀態通知會隨即顯示。

    ![新增指派 - 通知](./media/groups-assign-member-owner/groups-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>更新或移除現有角色指派

請遵循下列步驟來更新或移除現有角色指派。

1. 開啟**Azure AD Privileged Identity Management**。

1. 選取 **[特殊許可權存取（預覽）**]。

1. 您可以搜尋組名並使用 [**群組類型**] 來篩選清單，以選取您要管理的群組。

    ![要管理的特殊許可權存取群組清單](./media/groups-assign-member-owner/privileged-access-list.png)

1. 在 [**管理**] 下，選取 [**指派**]。

1. 選取要更新或移除的角色。

1. 在 [合格角色]**** 或 [有效角色]**** 索引標籤上尋找角色指派。

    ![更新或移除角色指派](./media/groups-assign-member-owner/groups-add-assignment.png)

1. 選取 [更新]**** 或 [移除]**** 以更新或移除角色指派。

    如需有關擴充角色指派的詳細資訊，請參閱[在 Privileged Identity Management 中擴充或更新 Azure 資源角色](pim-resource-roles-renew-extend.md)。

## <a name="next-steps"></a>後續步驟

- [在 Privileged Identity Management 中延長或更新 Azure 資源角色](pim-resource-roles-renew-extend.md)
- [在 Privileged Identity Management 中設定 Azure 資源角色設定](pim-resource-roles-configure-role-settings.md)
- [在 Privileged Identity Management 中指派 Azure AD 角色](pim-how-to-add-role-to-user.md)
