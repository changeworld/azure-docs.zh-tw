---
title: 在特權標識管理中分配 Azure 資源角色 - Azure 活動目錄 |微軟文檔
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中指派 Azure 資源角色。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34051a31c6ccf69356f330d7c5ecb009f760857a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266555"
---
# <a name="assign-azure-resource-roles-in-privileged-identity-management"></a>在特權標識管理中分配 Azure 資源角色

Azure 活動目錄 （Azure AD） 特權標識管理 （PIM） 可以管理內置的 Azure 資源角色以及自訂角色，包括（但不限於）：

- 擁有者
- 使用者存取系統管理員
- 參與者
- 安全性系統管理員
- 安全性管理員

> [!NOTE]
> 分配給擁有者或使用者訪問管理員訂閱角色的組的使用者或組成員，以及在 Azure AD 中啟用訂閱管理的 Azure AD 全域管理員預設具有資源管理員許可權。 這些管理員可以使用 Azure 資源的特權標識管理分配角色、配置角色設置和查看存取權限。 如果沒有資源管理員許可權，使用者無法管理資源的特權標識管理。 請檢視[Azure 資源的內建角色](../../role-based-access-control/built-in-roles.md)清單。

## <a name="assign-a-role"></a>指派角色

請遵循下列步驟來讓使用者有資格獲派 Azure 資源角色。

1. 使用作為[特權角色管理員](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)角色成員的使用者登錄到[Azure 門戶](https://portal.azure.com/)。

    有關如何授予其他管理員存取權限以管理特權身份管理的資訊，請參閱[授予其他管理員管理特權標識管理](pim-how-to-give-access-to-pim.md)的許可權。

1. 打開**Azure AD 特權標識管理**。

1. 選取 [Azure 資源]****。

1. 使用 [資源篩選]**** 來篩選管理的資源清單。

    ![要管理的 Azure 資源清單](./media/pim-resource-roles-assign-roles/resources-list.png)

1. 選擇要管理的資源，如訂閱或管理組。

1. 在"管理"下，選擇**角色**以查看 Azure 資源的角色清單。

    ![Azure 資源角色](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. 選擇 **"添加成員**"以打開"新建分配"窗格。

1. 選擇 **"選擇"角色**以打開"選擇角色窗格"。

    ![新增指派窗格](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. 選取要指派的角色，然後按一下 [選取]****。

    [選取成員或群組] 窗格會隨即開啟。

1. 選擇要分配給角色的成員或組，然後按一下"**選擇**"。

    ![選取成員或群組窗格](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

    [成員資格設定] 窗格會即開啟。

1. 在 [指派類型]**** 清單中選取 [合格]**** 或 [有效]****。

    ![成員資格設定窗格](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    Azure 資源的特權標識管理提供了兩種不同的分配類型：

    - **符合條件的**分配要求角色成員執行操作才能使用該角色。 動作可能包含執行多重要素驗證 (MFA) 檢查、提供業務理由，或是向指定的核准者要求核准。

    - **活動**分配不需要成員執行任何操作才能使用該角色。 指派為有效的成員隨時具有指派給角色的權限。

1. 如果分配應為永久分配（永久合格或永久分配），請選擇 **"永久"** 核取方塊。

    根據不同角色設定，核取方塊可能不會出現或可能設為不可修改。

1. 若要指定特定指派的持續時間，請清除核取方塊，並修改開始和/或結束日期和時間方塊。

    ![成員資格設定 - 日期和時間](./media/pim-resource-roles-assign-roles/resources-membership-settings-date.png)

1. 完成後，選擇 **"完成**"。

    ![新增指派 - 新增](./media/pim-resource-roles-assign-roles/resources-new-assignment-add.png)

1. 要創建新角色指派，請選擇"**添加**"。 狀態通知會隨即顯示。

    ![新增指派 - 通知](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>更新或移除現有角色指派

請遵循下列步驟來更新或移除現有角色指派。

1. 打開**Azure AD 特權標識管理**。

1. 選取 [Azure 資源]****。

1. 選擇要管理的資源，如訂閱或管理組。

1. 在"管理"下，選擇**角色**以查看 Azure 資源的角色清單。

    ![Azure 資源角色 - 選取角色](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. 選取要更新或移除的角色。

1. 在 [合格角色]**** 或 [有效角色]**** 索引標籤上尋找角色指派。

    ![更新或移除角色指派](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. 選取 [更新]**** 或 [移除]**** 以更新或移除角色指派。

    有關擴展角色指派的資訊，請參閱[在特權標識管理中擴展或續訂 Azure 資源角色](pim-resource-roles-renew-extend.md)。

## <a name="next-steps"></a>後續步驟

- [在特權標識管理中擴展或續訂 Azure 資源角色](pim-resource-roles-renew-extend.md)
- [在特權標識管理中配置 Azure 資源角色設置](pim-resource-roles-configure-role-settings.md)
- [在特權標識管理中分配 Azure AD 角色](pim-how-to-add-role-to-user.md)
