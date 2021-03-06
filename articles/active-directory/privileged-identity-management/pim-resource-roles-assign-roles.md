---
title: 在 Privileged Identity Management Azure Active Directory 中指派 Azure 資源角色 |Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中指派 Azure 資源角色。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/23/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 011b50449f0a02b128bc000535f93e1809fd9777
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92673877"
---
# <a name="assign-azure-resource-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中指派 Azure 資源角色

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 可以管理內建的 Azure 資源角色以及自訂角色，包括 (但不限於) ：

- 擁有者
- 使用者存取系統管理員
- 參與者
- 安全性系統管理員
- 安全性管理員

> [!NOTE]
> 指派給擁有者或使用者存取系統管理員訂用帳戶角色的群組使用者或成員，以及在 Azure AD 中啟用訂用帳戶管理的 Azure AD 全域管理員，預設都具有資源管理員許可權。 這些系統管理員可指派角色、設定角色設定，並使用適用于 Azure 資源的 Privileged Identity Management 來檢查存取權。 如果沒有資源管理員許可權，使用者就無法管理資源的 Privileged Identity Management。 查看 [Azure 內建角色](../../role-based-access-control/built-in-roles.md)的清單。

## <a name="assign-a-role"></a>指派角色

請遵循下列步驟來讓使用者有資格獲派 Azure 資源角色。

1. 使用「擁有者」或「使用者存取系統管理員」角色許可權登入 [Azure 入口網站](https://portal.azure.com/) 。

    如需如何授與其他系統管理員存取權以管理 Privileged Identity Management 的詳細資訊，請參閱 [授與其他系統管理員存取權以管理 Privileged Identity Management](pim-how-to-give-access-to-pim.md)。

1. 開啟 **Azure AD Privileged Identity Management** 。

1. 選取 [ **Azure 資源** ]。

1. 使用資源篩選器來尋找您要尋找的受控資源。

    ![要管理的 Azure 資源清單](./media/pim-resource-roles-assign-roles/resources-list.png)

1. 選取您要管理的資源，以開啟資源總覽頁面。

1. 在 [ **管理** ] 底下，選取 [ **角色** ] 以查看 Azure 資源的角色清單。

    ![Azure 資源角色](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. 選取 [ **新增指派** ] 以開啟 [ **新增指派** ] 窗格。

1. 選取 [ **選取角色** ] 以開啟 [ **選取角色** ] 頁面。

    ![新增指派窗格](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. 選取要指派的角色，然後按一下 [選取]  。

    [ **選取成員或群組** ] 窗格隨即開啟。

1. 選取您要指派給角色的成員或群組，然後按一下 [ **選取** ]。

    ![選取成員或群組窗格](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

1. 在 [ **設定** ] 索引標籤的 [ **指派類型** ] 清單中，選取 [ **合格** **] 或 [作用中]** 。

    ![成員資格設定窗格](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    適用于 Azure 資源的 Privileged Identity Management 提供兩種不同的指派類型：

    - **合格** 的指派會要求角色的成員執行動作以使用角色。 動作可能包含執行多重要素驗證 (MFA) 檢查、提供業務理由，或是向指定的核准者要求核准。

    - 使用中 **的指派不** 需要成員執行任何動作就能使用該角色。 指派為有效的成員隨時具有指派給角色的權限。

1. 若要指定特定的指派持續時間，請變更開始和結束日期和時間。

1. 完成時，選取 [ **指派** ]。

1. 建立新的角色指派之後，即會顯示狀態通知。

    ![新增指派 - 通知](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>更新或移除現有角色指派

請遵循下列步驟來更新或移除現有角色指派。

1. 開啟 **Azure AD Privileged Identity Management** 。

1. 選取 [ **Azure 資源** ]。

1. 選取您要管理的資源，以開啟其 [總覽] 頁面。

1. 在 [ **管理** ] 底下，選取 [ **角色** ] 以查看 Azure 資源的角色清單。

    ![Azure 資源角色 - 選取角色](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. 選取要更新或移除的角色。

1. 在 [合格角色]  或 [有效角色]  索引標籤上尋找角色指派。

    ![更新或移除角色指派](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. 選取 [更新]  或 [移除]  以更新或移除角色指派。

    如需擴充角色指派的相關資訊，請參閱 [Privileged Identity Management 中的擴充或更新 Azure 資源角色](pim-resource-roles-renew-extend.md)。

## <a name="next-steps"></a>後續步驟

- [在 Privileged Identity Management 中延長或更新 Azure 資源角色](pim-resource-roles-renew-extend.md)
- [在 Privileged Identity Management 中設定 Azure 資源角色設定](pim-resource-roles-configure-role-settings.md)
- [在 Privileged Identity Management 中指派 Azure AD 角色](pim-how-to-add-role-to-user.md)
