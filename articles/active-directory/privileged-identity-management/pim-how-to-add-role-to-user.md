---
title: 在 PIM 中分配 Azure AD 角色 - Azure 活動目錄 |微軟文檔
description: 瞭解如何在 Azure AD 特權標識管理 （PIM） 中分配 Azure AD 角色。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/07/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5048cefaae10cd55091dd72f0b73a3cf9d731a35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253269"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>在特權標識管理中分配 Azure AD 角色

使用 Azure 活動目錄 （Azure AD），全域管理員**可以永久分配**Azure AD 管理員角色。 您可以使用 [Azure 入口網站](../users-groups-roles/directory-assign-admin-roles.md)或使用 [PowerShell 命令](/powershell/module/azuread#directory_roles)來建立這些角色指派。

Azure AD 特權標識管理 （PIM） 服務還允許特權角色管理員進行永久管理員角色分配。 此外，特權角色管理員可以使使用者**有資格**擔任 Azure AD 管理員角色。 合格系統管理員可在需要時啟用角色，而在完成工作之後，其權限就隨即失效。

## <a name="determine-your-version-of-pim"></a>確定 PIM 的版本

從 2019 年 11 月開始，特權標識管理的 Azure AD 角色部分正在更新為與 Azure 資源角色體驗相匹配的新版本。 這將創建其他功能以及對現有[API 的更改](azure-ad-roles-features.md#api-changes)。 在推出新版本時，本文中遵循的哪些過程取決於您當前擁有的特權標識管理版本。 按照本節中的步驟確定您擁有的特權標識管理版本。 瞭解特權標識管理版本後，可以選擇本文中與該版本匹配的過程。

1. 使用處于[特權角色管理員](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)角色的使用者登錄到[Azure 門戶](https://portal.azure.com/)。
1. 打開**Azure AD 特權標識管理**。 如果概述頁面頂部有橫幅，請按照本文**的"新版本**"選項卡中的說明進行操作。 否則，請按照 **"以前版本**"選項卡中的說明操作。

  [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[新版本](#tab/new)

## <a name="assign-a-role"></a>指派角色

按照以下步驟使使用者有資格擔任 Azure AD 管理員角色。

1. 使用作為[特權角色管理員](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)角色成員的使用者登錄到[Azure 門戶](https://portal.azure.com/)。

    有關如何授予其他管理員存取權限以管理特權身份管理的資訊，請參閱[授予其他管理員管理特權標識管理](pim-how-to-give-access-to-pim.md)的許可權。

1. 打開**Azure AD 特權標識管理**。

1. 選擇**Azure AD 角色**。

1. 選擇**角色**以查看 Azure AD 許可權的角色清單。

    ![Azure AD 角色](./media/pim-how-to-add-role-to-user/roles-list.png)

1. 選擇 **"添加成員**"以打開 **"新建分配"** 頁。

1. 選擇 **"選擇"角色**以打開"選擇角色"頁。

    ![新增指派窗格](./media/pim-how-to-add-role-to-user/select-role.png)

1. 選取要指派的角色，然後按一下 [選取]****。

1. 選擇要分配給該角色的成員，然後選擇 **"選擇**"。

1. 在 **"成員資格設置"** 窗格中的 **"分配"類型**清單中，選擇 **"合格**"或 **"活動**"。

    - **符合條件的**分配要求角色成員執行操作才能使用該角色。 動作可能包含執行多重要素驗證 (MFA) 檢查、提供業務理由，或是向指定的核准者要求核准。

    - **活動**分配不需要成員執行任何操作才能使用該角色。 指派為有效的成員隨時具有指派給角色的權限。

1. 如果分配應為永久分配（永久合格或永久分配），請選擇 **"永久"** 核取方塊。

    根據不同角色設定，核取方塊可能不會出現或可能設為不可修改。

1. 若要指定特定指派的持續時間，請清除核取方塊，並修改開始和/或結束日期和時間方塊。 完成後，選擇 **"完成**"。

    ![成員資格設定 - 日期和時間](./media/pim-how-to-add-role-to-user/start-and-end-dates.png)

1. 要創建新角色指派，請選擇"**添加**"。 狀態通知會隨即顯示。

    ![新增指派 - 通知](./media/pim-how-to-add-role-to-user/assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>更新或移除現有角色指派

請遵循下列步驟來更新或移除現有角色指派。

1. 打開**Azure AD 特權標識管理**。

1. 選擇**Azure AD 角色**。

1. 選擇**角色**以查看 Azure AD 的角色清單。

1. 選取要更新或移除的角色。

1. 在 [合格角色]**** 或 [有效角色]**** 索引標籤上尋找角色指派。

    ![更新或移除角色指派](./media/pim-how-to-add-role-to-user/remove-update-assignments.png)

1. 選取 [更新]**** 或 [移除]**** 以更新或移除角色指派。

# <a name="previous-version"></a>[早期版本](#tab/previous)

## <a name="make-a-user-eligible-for-a-role"></a>讓使用者有資格獲派角色

按照以下步驟使使用者有資格擔任 Azure AD 管理員角色。

1. 選擇**角色**或**成員**。

    ![Azure AD 角色](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. 選擇 **"添加成員**"以打開 **"添加託管成員**"。

1. 選擇 **"選擇角色**"，選擇要管理的角色，然後**選擇**。

    ![選取角色](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. 選擇 **"選擇成員**"，選擇要分配給角色的使用者，然後選擇 **"**

    ![選取角色](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. 在 **"添加託管成員**"中，選擇 **"確定"** 以將使用者添加到角色。

1. 在角色清單中，選擇您剛剛分配的角色以查看成員清單。

     分配角色後，您選擇的使用者將顯示在成員清單中，稱為 **"符合該角色條件**"。

    ![角色的合格使用者](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. 現在，使用者有資格擔任該角色，請讓他們知道，他們可以根據[在特權標識管理 中啟動我的 Azure AD 角色](pim-how-to-activate-role.md)中的說明啟動該角色。

    啟用期間，系統會要求合格的系統管理員註冊 Azure Multi-factor Authentication (MFA)。 如果使用者無法註冊 MFA，或者正在使用 Microsoft 帳戶（如@outlook.com），則需要使其成為所有角色的永久角色。

## <a name="make-a-role-assignment-permanent"></a>設定永久角色指派

預設情況下，新使用者僅*有資格獲得*Azure AD 管理員角色。 如果您想要設定永久角色指派，請遵循下列步驟。

1. 打開**Azure AD 特權標識管理**。

1. 選擇**Azure AD 角色**。

1. 選取 [成員]****。

    ![成員的清單](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. 選擇要永久的 **"合格**"角色。

1. 選擇**更多**，然後選擇 **"使 perm**"。

    ![設定永久角色指派](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    角色現在會列為**永久**。

    ![永久變更的成員清單](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>從角色移除使用者

可以從角色指派中刪除使用者，但請確保始終至少有一個使用者是永久全域管理員。 如果您不確定哪些使用者仍然需要其角色指派，您可以[開始進行角色的存取權檢閱](pim-how-to-start-security-review.md)。

按照以下步驟從 Azure AD 管理員角色中刪除特定使用者。

1. 打開**Azure AD 特權標識管理**。

1. 選擇**Azure AD 角色**。

1. 選取 [成員]****。

    ![成員的清單](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. 選擇要刪除的角色指派。

1. 選擇 **"更多**"，然後選擇 **"刪除**"。

    ![移除角色](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. 在要求您確認的消息中，選擇 **"是**"。

    ![移除角色](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    已移除角色指派。

## <a name="authorization-error-when-assigning-roles"></a>指派角色時的授權錯誤

如果您最近為訂閱啟用了特權標識管理，並且在嘗試使使用者符合 Azure AD 管理員角色的條件時收到授權錯誤，可能是因為 MS-PIM 服務主體尚未具有適當的許可權。 MS-PIM 服務主體必須具有[使用者訪問管理員](../../role-based-access-control/built-in-roles.md#user-access-administrator)角色才能將角色指派給其他人。 您不需等到系統為 MS-PIM 指派使用者存取系統管理員角色，可以改為手動指派。

遵循下列步驟，將使用者存取系統管理員角色指派給訂用帳戶的 MS-PIM 服務主體。

1. 以全域系統管理員身分登入 Azure 入口網站。

1. 選擇 [所有服務]****，然後選擇 [訂用帳戶]****。

1. 選擇您的訂用帳戶。

1. 選擇 [存取控制 (IAM)]****。

1. 選擇 [角色指派]****，以查看訂用帳戶範圍中目前的角色指派清單。

   ![訂用帳戶的存取控制 (IAM) 刀鋒視窗](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. 檢查是否已為 **MS-PIM** 服務主體指派**使用者存取系統管理員**角色。

1. 若未指派，請選擇 [新增角色指派]**** 以開啟 [新增角色指派]**** 窗格。

1. 在 [角色]**** 下拉式清單中，選取 [使用者存取系統管理員]**** 角色。

1. 在 [選取]**** 清單中，尋找並選取 [MS-PIM]**** 服務主體。

   ![添加角色指派窗格 - 添加 MS-PIM 服務主體的許可權](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. 選擇 [儲存]**** 以指派角色。

   稍後片刻，即會在訂用帳戶範圍中，為 MS-PIM 服務主體指派使用者存取系統管理員角色。

   ![顯示 MS-PIM 服務主體的使用者訪問管理員角色分配的存取控制頁](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)

 ---

## <a name="next-steps"></a>後續步驟

- [在特權標識管理中配置 Azure AD 管理員角色設置](pim-how-to-change-default-settings.md)
- [在特權標識管理中分配 Azure 資源角色](pim-resource-roles-assign-roles.md)