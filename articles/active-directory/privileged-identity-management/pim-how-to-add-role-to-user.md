---
title: 在 PIM 中指派 Azure AD 角色-Azure Active Directory |Microsoft Docs
description: 瞭解如何在 Azure AD Privileged Identity Management (PIM) 中指派 Azure AD 角色。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 09/16/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69884b9e07172e9b25f4c14884be8713da23cbdb
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369823"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中指派 Azure AD 角色

使用 Azure Active Directory (Azure AD) 時，全域管理員可以進行 **永久** Azure AD 系統管理員角色指派。 您可以使用 [Azure 入口網站](../roles/permissions-reference.md)或使用 [PowerShell 命令](/powershell/module/azuread#directory_roles)來建立這些角色指派。

Azure AD Privileged Identity Management (PIM) 服務也允許特殊許可權角色管理員進行永久的系統管理員角色指派。 此外，特殊許可權角色系統管理員可以讓使用者符合 Azure AD 管理員角色的 **資格** 。 合格系統管理員可在需要時啟用角色，而在完成工作之後，其權限就隨即失效。

## <a name="determine-your-version-of-pim"></a>判斷您的 PIM 版本

自2019年11月起，Privileged Identity Management 的 Azure AD 角色部分會更新為符合 Azure 資源角色體驗的新版本。 這會建立其他功能以及 [現有 API 的變更](azure-ad-roles-features.md#api-changes)。 當新版本推出時，您在本文中遵循的程式將取決於您目前擁有的 Privileged Identity Management 版本。 遵循本節中的步驟，判斷您擁有的 Privileged Identity Management 版本。 知道您的 Privileged Identity Management 版本之後，您可以在本文中選取符合該版本的程式。

1. 使用[特殊權限角色管理員](../roles/permissions-reference.md#privileged-role-administrator)角色的使用者登入[Azure 入口網站](https://portal.azure.com/)。
1. 開啟 **Azure AD Privileged Identity Management**。 如果您在 [總覽] 頁面頂端有橫幅，請依照本文的 **新版本** 索引標籤中的指示進行。 否則，請遵循 [ **舊版** ] 索引標籤中的指示。

  [![選取 Azure AD > Privileged Identity Management。](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[新版本](#tab/new)

## <a name="assign-a-role"></a>指派角色

遵循這些步驟，讓使用者有資格使用 Azure AD 管理員角色。

1. 使用[特殊權限角色管理員](../roles/permissions-reference.md#privileged-role-administrator)角色成員的使用者登入[Azure 入口網站](https://portal.azure.com/)。

    如需如何授與其他系統管理員存取權以管理 Privileged Identity Management 的詳細資訊，請參閱 [授與其他系統管理員存取權以管理 Privileged Identity Management](pim-how-to-give-access-to-pim.md)。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 選取 **Azure AD 角色**]。

1. 選取 [ **角色** ] 以查看 Azure AD 許可權的角色清單。

    ![已選取 [新增指派] 動作的 [角色] 頁面螢幕擷取畫面。](./media/pim-how-to-add-role-to-user/roles-list.png)

1. 選取 [ **新增指派** ] 以開啟 [ **新增指派** ] 頁面。

1. 選取 [ **選取角色** ] 以開啟 [ **選取角色** ] 頁面。

    ![新增指派窗格](./media/pim-how-to-add-role-to-user/select-role.png)

1. 選取您要指派的角色，選取您要指派給角色的成員，然後選取 **[下一步]**。

1. 在 [**成員資格設定**] 窗格的 [**指派類型**] 清單中，選取 [**合格** **] 或 [作用中]**。

    - **合格** 的指派會要求角色的成員執行動作以使用角色。 動作可能包含執行多重要素驗證 (MFA) 檢查、提供業務理由，或是向指定的核准者要求核准。

    - 使用中**的指派不**需要成員執行任何動作就能使用該角色。 指派為有效的成員隨時具有指派給角色的權限。

1. 若要指定特定的指派持續時間，請新增 [開始] 和 [結束日期] 和 [時間] 方塊。 完成時，選取 [ **指派** ] 以建立新的角色指派。

    ![成員資格設定 - 日期和時間](./media/pim-how-to-add-role-to-user/start-and-end-dates.png)

1. 指派角色之後，就會顯示指派狀態通知。

    ![新增指派 - 通知](./media/pim-how-to-add-role-to-user/assignment-notification.png)

## <a name="assign-a-role-with-restricted-scope"></a>指派受限範圍的角色

針對某些角色，授與許可權的範圍可以限制為單一管理單位、服務主體或應用程式。 如果指派具有管理單位範圍的角色，則此程式是一個範例。 如需透過管理單位支援範圍的角色清單，請參閱 [指派範圍角色給管理單位](../roles/admin-units-assign-roles.md)。 這項功能目前正推出給 Azure AD 組織。

1. 使用具有特殊許可權的角色系統管理員許可權登入 [Azure Active Directory admin center](https://aad.portal.azure.com) 。

1. 選取**Azure Active Directory**  >  **角色和系統管理員**。

1. 選取 **使用者系統管理員**。

    ![當您在入口網站中開啟角色時，可以使用 [新增指派] 命令](./media/pim-how-to-add-role-to-user/add-assignment.png)

1. 選取 [ **新增指派**]。

    ![當角色支援範圍時，您可以選取範圍](./media/pim-how-to-add-role-to-user/add-scope.png)

1. 在 [ **新增指派** ] 頁面上，您可以：

   - 選取要指派給角色的使用者或群組
   - 選取角色範圍 (在此案例中為管理單位) 
   - 選取範圍的管理單位

如需有關建立管理單位的詳細資訊，請參閱 [新增和移除管理單位](../roles/admin-units-manage.md)。

## <a name="update-or-remove-an-existing-role-assignment"></a>更新或移除現有角色指派

請遵循下列步驟來更新或移除現有角色指派。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 選取 **Azure AD 角色**]。

1. 選取 [ **角色** ] 以查看 Azure AD 的角色清單。

1. 選取要更新或移除的角色。

1. 在 [合格角色]**** 或 [有效角色]**** 索引標籤上尋找角色指派。

    ![更新或移除角色指派](./media/pim-how-to-add-role-to-user/remove-update-assignments.png)

1. 選取 [更新]**** 或 [移除]**** 以更新或移除角色指派。

# <a name="previous-version"></a>[先前版本](#tab/previous)

## <a name="make-a-user-eligible-for-a-role"></a>讓使用者有資格獲派角色

遵循這些步驟，讓使用者有資格使用 Azure AD 管理員角色。

1. 選取 [ **角色** ] 或 [ **成員**]。

    ![開啟 Azure AD 角色](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. 選取 [ **新增成員** ] 以開啟 [ **加入 managed 成員**]。

1. 選取 [ **選取角色**]，選取您要管理的角色，然後選取 [ **選取**]。

    ![選取角色](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. 選取 [ **選取成員**]，選取您要指派給角色的使用者，然後選取 [ **選取**]。

    ![選取要指派的使用者或群組](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. 在 [ **新增受控成員**] 中，選取 **[確定]** ，將使用者新增至角色。

1. 在角色清單中，選取您剛剛指派的角色以查看成員清單。

     指派角色之後，您選取的使用者將會出現在 [成員清單] 中，以符合角色的 **資格** 。

    ![角色的合格使用者](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. 現在使用者符合角色的資格，讓他們知道他們可以根據 Privileged Identity Management 中 [ [啟用我的 Azure AD 角色](pim-how-to-activate-role.md)] 中的指示來啟用它。

    合格的系統管理員會在啟用期間要求註冊 Azure Multi-Factor Authentication。 如果使用者無法註冊 MFA，或使用 Microsoft 帳戶 (（例如 @outlook.com) ），您就必須在所有的角色中將它們設為永久。

## <a name="make-a-role-assignment-permanent"></a>設定永久角色指派

根據預設，新使用者僅符合 Azure AD 管理員角色的 *資格* 。 如果您想要設定永久角色指派，請遵循下列步驟。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 選取 **Azure AD 角色**]。

1. 選取 [成員]。

    ![成員的清單](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. 選取您要設為永久的 **合格** 角色。

1. 選取 [ **更多** ]，然後選取 [ **建立為永久**]。

    ![設定永久角色指派](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    角色現在會列為**永久**。

    ![永久變更的成員清單](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>從角色移除使用者

您可以將使用者從角色指派中移除，但請確定永遠至少有一位使用者是永久的全域管理員。 如果您不確定哪些使用者仍然需要其角色指派，您可以[開始進行角色的存取權檢閱](pim-how-to-start-security-review.md)。

請遵循下列步驟，從 Azure AD 管理員角色中移除特定使用者。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 選取 **Azure AD 角色**]。

1. 選取 [成員]。

    ![成員的清單](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. 選取您要移除的角色指派。

1. 選取 [ **更多** ]，然後選取 [ **移除**]。

    ![移除角色](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. 在要求您確認的訊息中，選取 **[是]**。

    ![確認移除](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    已移除角色指派。

## <a name="authorization-error-when-assigning-roles"></a>指派角色時的授權錯誤

如果您最近啟用了訂用帳戶的 Privileged Identity Management，且在嘗試讓使用者符合 Azure AD 系統管理員角色的資格時收到授權錯誤，可能是因為 MS PIM 服務主體尚未具有適當的許可權。 MS PIM 服務主體必須具有「 [使用者存取系統管理員](../../role-based-access-control/built-in-roles.md#user-access-administrator) 」角色，才能將角色指派給其他人。 您不需等到系統為 MS-PIM 指派使用者存取系統管理員角色，可以改為手動指派。

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

   ![[新增角色指派] 窗格-新增 MS PIM 服務主體的許可權](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. 選擇 [儲存]**** 以指派角色。

   稍後片刻，即會在訂用帳戶範圍中，為 MS-PIM 服務主體指派使用者存取系統管理員角色。

   ![顯示 MS PIM 服務主體使用者存取系統管理員角色指派的存取控制頁面](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)

 ---

## <a name="next-steps"></a>後續步驟

- [在 Privileged Identity Management 中設定 Azure AD 管理員角色設定](pim-how-to-change-default-settings.md)
- [在 Privileged Identity Management 中指派 Azure 資源角色](pim-resource-roles-assign-roles.md)