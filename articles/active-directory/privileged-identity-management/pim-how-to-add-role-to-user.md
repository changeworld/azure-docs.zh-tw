---
title: 在 PIM 中指派 Azure AD 角色-Azure Active Directory |Microsoft Docs
description: 瞭解如何在 Azure AD Privileged Identity Management （PIM）中指派 Azure AD 角色。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 07/01/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cdea30e7b630b2fa62b004f98cb67fd1994f915
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86024149"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中指派 Azure AD 角色

有了 Azure Active Directory （Azure AD），全域管理員就可以進行**永久**Azure AD 系統管理員角色指派。 您可以使用 [Azure 入口網站](../users-groups-roles/directory-assign-admin-roles.md)或使用 [PowerShell 命令](/powershell/module/azuread#directory_roles)來建立這些角色指派。

Azure AD Privileged Identity Management （PIM）服務也允許特殊許可權角色管理員進行永久的系統管理員角色指派。 此外，特殊許可權角色系統管理員可以讓使用者有**資格**Azure AD 系統管理員角色。 合格系統管理員可在需要時啟用角色，而在完成工作之後，其權限就隨即失效。

## <a name="determine-your-version-of-pim"></a>判斷您的 PIM 版本

從2019年11月開始，Privileged Identity Management 的 Azure AD 角色部分更新為符合 Azure 資源角色體驗的新版本。 這會建立額外的功能，以及[現有 API 的變更](azure-ad-roles-features.md#api-changes)。 推出新版本時，您在本文中遵循的程式取決於您目前擁有的 Privileged Identity Management 版本。 請依照本節中的步驟來判斷您擁有的 Privileged Identity Management 版本。 知道您的 Privileged Identity Management 版本之後，您可以選取本文中符合該版本的程式。

1. 以[特殊權限角色管理員](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)角色的使用者身分登入[Azure 入口網站](https://portal.azure.com/)。
1. 開啟**Azure AD Privileged Identity Management**。 如果您在 [總覽] 頁面頂端有橫幅，請遵循本文的 [**新版本**] 索引標籤中的指示。 否則，請依照 [**先前版本**] 索引標籤中的指示進行。

  [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[新版本](#tab/new)

## <a name="assign-a-role"></a>指派角色

請遵循下列步驟，讓使用者具備 Azure AD 管理員角色的資格。

1. 使用屬於[特殊權限角色管理員](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)角色成員的使用者登入[Azure 入口網站](https://portal.azure.com/)。

    如需如何授與其他系統管理員存取權來管理 Privileged Identity Management 的相關資訊，請參閱將[存取權授與其他管理員以管理 Privileged Identity Management](pim-how-to-give-access-to-pim.md)。

1. 開啟**Azure AD Privileged Identity Management**。

1. 選取 [ **Azure AD 角色**]。

1. 選取 [**角色**] 以查看 Azure AD 許可權的角色清單。

    ![Azure AD 角色](./media/pim-how-to-add-role-to-user/roles-list.png)

1. 選取 [**新增指派**] 以開啟 [**新增指派**] 頁面。

1. 選取 [**選取角色**] 以開啟 [**選取角色**] 頁面。

    ![新增指派窗格](./media/pim-how-to-add-role-to-user/select-role.png)

1. 選取您想要指派的角色，選取您要指派給角色的成員，然後選取 **[下一步]**。

1. 在 [**成員資格設定**] 窗格的 [**指派類型**] 清單中，選取 [**合格** **] 或 [作用中]**。

    - **合格**的指派會要求角色的成員執行動作以使用角色。 動作可能包含執行多重要素驗證 (MFA) 檢查、提供業務理由，或是向指定的核准者要求核准。

    - 作用中**的指派不**需要成員執行任何動作來使用角色。 指派為有效的成員隨時具有指派給角色的權限。

1. 若要指定特定的指派持續時間，請新增 [開始] 和 [結束日期] 和 [時間] 方塊。 完成時，選取 [**指派**] 以建立新的角色指派。

    ![成員資格設定 - 日期和時間](./media/pim-how-to-add-role-to-user/start-and-end-dates.png)

1. 指派角色之後，就會顯示指派狀態通知。

    ![新增指派 - 通知](./media/pim-how-to-add-role-to-user/assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>更新或移除現有角色指派

請遵循下列步驟來更新或移除現有角色指派。

1. 開啟**Azure AD Privileged Identity Management**。

1. 選取 [ **Azure AD 角色**]。

1. 選取 [**角色**] 以查看 Azure AD 的角色清單。

1. 選取要更新或移除的角色。

1. 在 [合格角色]**** 或 [有效角色]**** 索引標籤上尋找角色指派。

    ![更新或移除角色指派](./media/pim-how-to-add-role-to-user/remove-update-assignments.png)

1. 選取 [更新]**** 或 [移除]**** 以更新或移除角色指派。

# <a name="previous-version"></a>[先前的版本](#tab/previous)

## <a name="make-a-user-eligible-for-a-role"></a>讓使用者有資格獲派角色

請遵循下列步驟，讓使用者具備 Azure AD 管理員角色的資格。

1. 選取 [**角色**] 或 [**成員**]。

    ![Azure AD 角色](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. 選取 [**新增成員**] 以開啟 [**新增受控成員**]。

1. 選取 [**選取角色**]，選取您想要管理的角色，然後選取 [**選取**]。

    ![選取角色](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. 選取 [**選取成員**]，選取您要指派給角色的使用者，然後選取 [**選取**]。

    ![選取角色](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. 在 [**新增受控成員**] 中，選取 **[確定]** 將使用者新增至角色。

1. 在 [角色清單] 中，選取您剛指派的角色，以查看成員清單。

     指派角色之後，您選取的使用者將會出現在 [成員] 清單中，以符合該角色的**資格**。

    ![角色的合格使用者](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. 既然使用者已經符合該角色的資格，請讓他們知道他們可以根據在[Privileged Identity Management 中啟用我的 Azure AD 角色](pim-how-to-activate-role.md)中的指示來啟用它。

    合格系統管理員會在啟用期間要求註冊 Azure 多重要素驗證。 如果使用者無法註冊 MFA，或使用 Microsoft 帳戶（例如 @outlook.com ），您必須在其所有角色中將其設為永久。

## <a name="make-a-role-assignment-permanent"></a>設定永久角色指派

根據預設，新*使用者僅適用*于 Azure AD 系統管理員角色。 如果您想要設定永久角色指派，請遵循下列步驟。

1. 開啟**Azure AD Privileged Identity Management**。

1. 選取 [ **Azure AD 角色**]。

1. 選取 [成員]。

    ![成員的清單](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. 選取您要設為永久的**合格**角色。

1. 選取 [**更多**]，然後選取 [**建立永久**]。

    ![設定永久角色指派](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    角色現在會列為**永久**。

    ![永久變更的成員清單](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>從角色移除使用者

您可以從角色指派中移除使用者，但請務必一律至少有一個使用者是永久的全域管理員。 如果您不確定哪些使用者仍然需要其角色指派，您可以[開始進行角色的存取權檢閱](pim-how-to-start-security-review.md)。

請遵循下列步驟，將特定使用者從 Azure AD 系統管理員角色中移除。

1. 開啟**Azure AD Privileged Identity Management**。

1. 選取 [ **Azure AD 角色**]。

1. 選取 [成員]。

    ![成員的清單](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. 選取您想要移除的角色指派。

1. 選取 [**更多**]，然後選取 [**移除**]。

    ![移除角色](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. 在要求您確認的訊息中，選取 **[是]**。

    ![移除角色](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    已移除角色指派。

## <a name="authorization-error-when-assigning-roles"></a>指派角色時的授權錯誤

如果您最近已為訂用帳戶啟用 Privileged Identity Management，而您嘗試讓使用者符合 Azure AD 管理員角色的資格，可能是因為 MS PIM 服務主體尚未擁有適當的許可權。 MS-PIM 服務主體必須具有 [[使用者存取系統管理員](../../role-based-access-control/built-in-roles.md#user-access-administrator)] 角色，才能將角色指派給其他人。 您不需等到系統為 MS-PIM 指派使用者存取系統管理員角色，可以改為手動指派。

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

   ![[新增角色指派] 窗格-[MS-PIM 服務主體的新增許可權]](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. 選擇 [儲存]**** 以指派角色。

   稍後片刻，即會在訂用帳戶範圍中，為 MS-PIM 服務主體指派使用者存取系統管理員角色。

   ![顯示 MS PIM 服務主體之使用者存取管理員角色指派的 [存取控制] 頁面](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)

 ---

## <a name="next-steps"></a>後續步驟

- [在 Privileged Identity Management 中設定 Azure AD 系統管理員角色設定](pim-how-to-change-default-settings.md)
- [在 Privileged Identity Management 中指派 Azure 資源角色](pim-resource-roles-assign-roles.md)