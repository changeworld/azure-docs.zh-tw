---
title: 為特殊許可權存取群組指派符合資格的擁有者和成員-Azure Active Directory
description: 瞭解如何在 Azure AD Privileged Identity Management (PIM) 中指派符合資格的擁有者或角色可指派群組的成員。
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
ms.date: 08/18/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48c4473cfafce1215219251c47bce1d5730645fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91534415"
---
# <a name="assign-eligibility-for-a-privileged-access-group-preview-in-privileged-identity-management"></a>在 Privileged Identity Management 中指派特殊許可權存取群組 (預覽) 的資格

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 可協助您管理 Azure AD 中特殊許可權存取群組的指派資格和啟用。 您可以將資格指派給群組的成員或擁有者。

>[!NOTE]
>具有特殊許可權存取群組成員資格或擁有權的每位使用者都必須具備 Azure AD Premium P2 授權。 如需詳細資訊，請參閱 [使用 Privileged Identity Management 的授權需求](subscription-requirements.md)。

## <a name="assign-an-owner-or-member-of-a-group"></a>指派群組的擁有者或成員

遵循這些步驟，讓使用者有資格成為特殊許可權存取群組的成員或擁有者。

1. 使用全域管理員或群組擁有者許可權登[入 Azure AD](https://aad.portal.azure.com) 。
1. 選取 [ **群組** ]，然後選取您想要管理的角色可指派群組。 您可以搜尋或篩選清單。

    ![尋找可在 PIM 中管理的角色可指派群組](./media/groups-assign-member-owner/groups-list-in-azure-ad.png)

1. 開啟群組，然後選取 [特殊許可權 **存取] (預覽) **。

    ![開啟 Privileged Identity Management 體驗](./media/groups-assign-member-owner/groups-discover-groups.png)

1. 選取 [ **新增指派**]。

    ![新增指派窗格](./media/groups-assign-member-owner/groups-add-assignment.png)

1. 選取您要提供給特殊許可權存取群組的成員或擁有者。

    ![顯示 [新增指派] 頁面的螢幕擷取畫面，其中會開啟 [選取成員或群組] 窗格，並反白顯示 [選取] 按鈕。](./media/groups-assign-member-owner/add-assignments.png)

1. 選取 **[下一步]** 以設定成員資格或擁有權的持續時間。

    ![選取成員或群組窗格](./media/groups-assign-member-owner/assignment-duration.png)

1. 在 [指派類型]**** 清單中選取 [合格]**** 或 [有效]****。 特殊許可權存取群組提供兩種不同的指派類型：

    - **合格** 的指派會要求角色的成員執行動作以使用角色。 動作可能包含執行多重要素驗證 (MFA) 檢查、提供業務理由，或是向指定的核准者要求核准。

    - 使用中**的指派不**需要成員執行任何動作就能使用該角色。 指派為有效的成員隨時具有指派給角色的權限。

1. 如果指派應為永久性 (永久符合資格或永久指派) ，請選取 [ **永久** ] 核取方塊。 根據組織的設定，核取方塊可能不會出現或可能無法編輯。

1. 完成時，選取 [ **指派**]。

1. 若要建立新的角色指派， **請選取 [新增]**。 狀態通知會隨即顯示。

    ![新增指派 - 通知](./media/groups-assign-member-owner/groups-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>更新或移除現有角色指派

請遵循下列步驟來更新或移除現有角色指派。

1. 使用全域管理員或群組擁有者許可權登[入 Azure AD](https://aad.portal.azure.com) 。
1. 選取 [ **群組** ]，然後選取您想要管理的角色可指派群組。 您可以搜尋或篩選清單。

    ![尋找可在 PIM 中管理的角色可指派群組](./media/groups-assign-member-owner/groups-list-in-azure-ad.png)

1. 開啟群組，然後選取 [特殊許可權 **存取] (預覽) **。

    ![開啟 Privileged Identity Management 體驗](./media/groups-assign-member-owner/groups-discover-groups.png)

1. 選取要更新或移除的角色。

1. 在 [合格角色]**** 或 [有效角色]**** 索引標籤上尋找角色指派。

    ![更新或移除角色指派](./media/groups-assign-member-owner/groups-bring-under-management.png)

1. 選取 [更新]**** 或 [移除]**** 以更新或移除角色指派。

    如需擴充角色指派的相關資訊，請參閱 [Privileged Identity Management 中的擴充或更新 Azure 資源角色](pim-resource-roles-renew-extend.md)。

## <a name="next-steps"></a>後續步驟

- [在 Privileged Identity Management 中延長或更新 Azure 資源角色](pim-resource-roles-renew-extend.md)
- [在 Privileged Identity Management 中設定 Azure 資源角色設定](pim-resource-roles-configure-role-settings.md)
- [在 Privileged Identity Management 中指派 Azure AD 角色](pim-how-to-add-role-to-user.md)
