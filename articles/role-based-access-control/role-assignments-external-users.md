---
title: 使用 Azure 入口網站為外部使用者新增或移除 Azure 角色指派-Azure RBAC
description: 瞭解如何使用 Azure 入口網站和 Azure 角色型存取控制（Azure RBAC），為組織外部的使用者授與 Azure 資源的存取權。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: skwan
ms.custom: it-pro
ms.openlocfilehash: 568cb7602d9cb454685ef69e820288bcc68563c4
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735754"
---
# <a name="add-or-remove-azure-role-assignments-for-external-guest-users-using-the-azure-portal"></a>使用 Azure 入口網站為外部來賓使用者新增或移除 Azure 角色指派

[Azure 角色型存取控制（AZURE RBAC）](overview.md)可讓大型組織及小型和中型企業使用需要存取您環境中特定資源，但不一定是整個基礎結構或任何計費相關範圍的中小型企業進行更好的安全性管理。 您可以使用[AZURE ACTIVE DIRECTORY B2B](../active-directory/b2b/what-is-b2b.md)中的功能，與外部來賓使用者共同作業，而且您可以使用 Azure RBAC 僅授與來賓使用者在您的環境中所需的許可權。

## <a name="prerequisites"></a>Prerequisites

若要新增或移除角色指派，您必須具有：

- `Microsoft.Authorization/roleAssignments/write` 和 `Microsoft.Authorization/roleAssignments/delete` 權限，例如[使用者存取系統管理員](built-in-roles.md#user-access-administrator)或[擁有者](built-in-roles.md#owner)

## <a name="when-would-you-invite-guest-users"></a>何時會邀請來賓使用者？

以下是一些範例案例，當您可能會邀請來賓使用者到您的組織並授與許可權時：

- 允許只有電子郵件帳戶的外部自營廠商存取專案的 Azure 資源。
- 允許外部合作夥伴管理特定資源或整個訂用帳戶。
- 允許不在貴組織 (例如 Microsoft 支援服務) 的支援工程師暫時存取您的 Azure 資源，針對問題進行疑難排解。

## <a name="permission-differences-between-member-users-and-guest-users"></a>成員使用者與來賓使用者之間的許可權差異

目錄的原生成員（成員使用者）所擁有的許可權，與另一個目錄的 B2B 共同作業來賓（來賓使用者）不同。 例如，成員使用者可以讀取幾乎所有的目錄資訊，而來賓使用者則擁有限制的目錄許可權。 如需成員使用者和來賓使用者的詳細資訊，請參閱[Azure Active Directory 中的預設使用者權力為何？](../active-directory/fundamentals/users-default-permissions.md)。

## <a name="add-a-guest-user-to-your-directory"></a>將來賓使用者新增至您的目錄

請遵循下列步驟，使用 [Azure Active Directory] 頁面，將來賓使用者新增至您的目錄。

1. 請確定您組織的外部協同作業設定已設定為允許您邀請來賓。 如需詳細資訊，請參閱[啟用 B2B 外部共同作業和管理可以邀請來賓的人員](../active-directory/b2b/delegate-invitations.md)。

1. 在 [Azure 入口網站中，按一下 [ **Azure Active Directory** > **使用者** > ] [**新增來賓使用者**]。

    ![Azure 入口網站中的新來賓使用者功能](./media/role-assignments-external-users/invite-guest-user.png)

1. 遵循步驟來新增來賓使用者。 如需詳細資訊，請參閱[在 Azure 入口網站中新增 AZURE ACTIVE DIRECTORY B2B](../active-directory/b2b/add-users-administrator.md#add-guest-users-to-the-directory)共同作業使用者。

將來賓使用者新增至目錄之後，您就可以將共用應用程式的直接連結傳送給來賓使用者，或者來賓使用者可以按一下邀請電子郵件中的兌換 URL。

![來賓使用者邀請電子郵件](./media/role-assignments-external-users/invite-email.png)

若要讓來賓使用者能夠存取您的目錄，他們必須完成邀請程式。

![來賓使用者邀請審查許可權](./media/role-assignments-external-users/invite-review-permissions.png)

如需有關邀請程式的詳細資訊，請參閱[AZURE ACTIVE DIRECTORY B2B 共同作業邀請兌換](../active-directory/b2b/redemption-experience.md)。

## <a name="add-a-role-assignment-for-a-guest-user"></a>新增來賓使用者的角色指派

在 Azure RBAC 中，若要授與存取權，請指派角色。 若要新增來賓使用者的角色指派，請遵循與成員使用者、群組、服務主體或受控識別[相同的步驟](role-assignments-portal.md#add-a-role-assignment)。 請遵循下列步驟，為不同範圍的來賓使用者新增角色指派。

1. 在 [Azure 入口網站中，按一下 [**所有服務**]。

1.  選取要套用存取權的資源集，也稱為「範圍」。 例如，您可以選取 [管理群組]****、[訂用帳戶]****、[資源群組]**** 或資源。

1. 按一下特定的資源。

1. 按一下 **[存取控制（IAM）**]。

    下列螢幕擷取畫面顯示資源群組的 [存取控制（IAM）] 分頁範例。 如果您在這裡進行任何存取控制變更，它們只會套用至資源群組。

    ![資源群組的存取控制 (IAM) 刀鋒視窗](./media/role-assignments-external-users/access-control-resource-group.png)

1. 按一下 [角色指派]**** 索引標籤以檢視此範圍中的所有角色指派。

1. 按一下 [**新增** > ] [**新增角色指派**] 以開啟 [新增角色指派] 窗格。

    若您沒有指派角色的權限，[新增角色指派] 選項將會被停用。

    ![[新增] 功能表](./media/role-assignments-external-users/add-menu.png)

1. 在 [角色]**** 下拉式清單中選取角色，例如 [虛擬機器參與者]****。

1. 在 [**選取**] 清單中，選取來賓使用者。 如果您在清單中看不到該使用者，您可以在 [**選取**] 方塊中輸入，以在目錄中搜尋顯示名稱、電子郵件地址和物件識別碼。

   ![[新增角色指派] 窗格](./media/role-assignments-external-users/add-role-assignment.png)

1. 按一下 [**儲存**]，在選取的範圍指派角色。

    ![虛擬機器參與者的角色指派](./media/role-assignments-external-users/access-control-role-assignments.png)

## <a name="add-a-role-assignment-for-a-guest-user-not-yet-in-your-directory"></a>為尚未在您目錄中的來賓使用者新增角色指派

若要新增來賓使用者的角色指派，請遵循與成員使用者、群組、服務主體或受控識別[相同的步驟](role-assignments-portal.md#add-a-role-assignment)。

如果來賓使用者尚未在您的目錄中，您可以直接從 [新增角色指派] 窗格邀請使用者。

1. 在 [Azure 入口網站中，按一下 [**所有服務**]。

1.  選取要套用存取權的資源集，也稱為「範圍」。 例如，您可以選取 [管理群組]****、[訂用帳戶]****、[資源群組]**** 或資源。

1. 按一下特定的資源。

1. 按一下 **[存取控制（IAM）**]。

1. 按一下 [角色指派]**** 索引標籤以檢視此範圍中的所有角色指派。

1. 按一下 [**新增** > ] [**新增角色指派**] 以開啟 [新增角色指派] 窗格。

    ![[新增] 功能表](./media/role-assignments-external-users/add-menu.png)

1. 在 [角色]**** 下拉式清單中選取角色，例如 [虛擬機器參與者]****。

1. 在 [**選取**] 清單中，輸入您想要邀請之人員的電子郵件地址，然後選取該人員。

   ![邀請 [新增角色指派] 窗格中的來賓使用者](./media/role-assignments-external-users/add-role-assignment-new-guest.png)

1. 按一下 [**儲存**]，將來賓使用者新增至您的目錄、指派角色，以及傳送邀請。

    幾分鐘後，您會看到角色指派的通知和邀請的相關資訊。

    ![角色指派和受邀的使用者通知](./media/role-assignments-external-users/invited-user-notification.png)

1. 若要手動邀請來賓使用者，請以滑鼠右鍵按一下並複製通知中的邀請連結。 請不要按一下邀請連結，因為它會開始邀請程式。

    邀請連結的格式如下：

    `https://invitations.microsoft.com/redeem/...`

1. 將邀請連結傳送給來賓使用者，以完成邀請程式。

    如需有關邀請程式的詳細資訊，請參閱[AZURE ACTIVE DIRECTORY B2B 共同作業邀請兌換](../active-directory/b2b/redemption-experience.md)。

## <a name="remove-a-guest-user-from-your-directory"></a>從您的目錄中移除來賓使用者

從目錄中移除來賓使用者之前，您應該先移除該來賓使用者的任何角色指派。 請遵循下列步驟，從目錄中移除來賓使用者。

1. 開啟範圍中的**存取控制（IAM）** ，例如管理群組、訂用帳戶、資源群組或資源，其中來賓使用者具有角色指派。

1. 按一下 [**角色指派**] 索引標籤，以查看所有角色指派。

1. 在角色指派清單中，在具有您要移除之角色指派的來賓使用者旁新增核取記號。

   ![移除角色指派](./media/role-assignments-external-users/remove-role-assignment-select.png)

1. 按一下 [移除]****。

   ![移除角色指派訊息](./media/role-assignments-external-users/remove-role-assignment.png)

1. 在顯示的移除角色指派訊息中，按一下 [是]****。

1. 在左側導覽列中，按一下 [ **Azure Active Directory** > **使用者**]。

1. 按一下您要移除的來賓使用者。

1. 按一下 **[刪除]** 。

   ![刪除來賓使用者](./media/role-assignments-external-users/delete-guest-user.png)

1. 在出現的 [刪除] 訊息中，按一下 [**是]**。

## <a name="troubleshoot"></a>疑難排解

### <a name="guest-user-cannot-browse-the-directory"></a>來賓使用者無法流覽目錄

來賓使用者具有受限的目錄權限。 例如，來賓使用者無法流覽目錄，也無法搜尋群組或應用程式。 如需詳細資訊，請參閱[Azure Active Directory 中的預設使用者許可權為何？](../active-directory/fundamentals/users-default-permissions.md)。

![來賓使用者無法流覽目錄中的使用者](./media/role-assignments-external-users/directory-no-users.png)

如果來賓使用者需要目錄中的其他許可權，您可以將目錄角色指派給來賓使用者。 如果您真的想讓來賓使用者擁有目錄的完整讀取權限，您可以在 Azure AD 中將來賓使用者新增至[目錄讀取](../active-directory/users-groups-roles/directory-assign-admin-roles.md)者角色。 如需詳細資訊，請參閱[將許可權授與您 Azure Active Directory 租使用者中合作夥伴組織的使用者](../active-directory/b2b/add-guest-to-role.md)。

![指派目錄讀取者角色](./media/role-assignments-external-users/directory-roles.png)

### <a name="guest-user-cannot-browse-users-groups-or-service-principals-to-assign-roles"></a>來賓使用者無法流覽使用者、群組或服務主體來指派角色

來賓使用者具有受限的目錄權限。 即使來賓使用者是範圍中的[擁有](built-in-roles.md#owner)者，如果他們嘗試新增角色指派以授與其他人存取權，他們就無法流覽使用者、群組或服務主體的清單。

![來賓使用者無法流覽安全性主體來指派角色](./media/role-assignments-external-users/directory-no-browse.png)

如果來賓使用者知道某人在目錄中的確切登入名稱，他們就可以授與存取權。 如果您真的想讓來賓使用者擁有目錄的完整讀取權限，您可以在 Azure AD 中將來賓使用者新增至[目錄讀取](../active-directory/users-groups-roles/directory-assign-admin-roles.md)者角色。 如需詳細資訊，請參閱[將許可權授與您 Azure Active Directory 租使用者中合作夥伴組織的使用者](../active-directory/b2b/add-guest-to-role.md)。

### <a name="guest-user-cannot-register-applications-or-create-service-principals"></a>來賓使用者無法註冊應用程式或建立服務主體

來賓使用者具有受限的目錄權限。 如果來賓使用者需要能夠註冊應用程式或建立服務主體，您可以將來賓使用者新增至 Azure AD 中的[應用程式開發人員](../active-directory/users-groups-roles/directory-assign-admin-roles.md)角色。 如需詳細資訊，請參閱[將許可權授與您 Azure Active Directory 租使用者中合作夥伴組織的使用者](../active-directory/b2b/add-guest-to-role.md)。

![來賓使用者無法註冊應用程式](./media/role-assignments-external-users/directory-access-denied.png)

### <a name="guest-user-does-not-see-the-new-directory"></a>來賓使用者看不到新的目錄

如果來賓使用者已被授與目錄的存取權，但當他們嘗試在其 [**目錄 + 訂**用帳戶] 窗格中切換時，看不到 Azure 入口網站列出的新目錄，請確定來賓使用者已完成邀請程式。 如需有關邀請程式的詳細資訊，請參閱[AZURE ACTIVE DIRECTORY B2B 共同作業邀請兌換](../active-directory/b2b/redemption-experience.md)。

### <a name="guest-user-does-not-see-resources"></a>來賓使用者沒有看到資源

如果來賓使用者已被授與目錄的存取權，但他們在 Azure 入口網站中看不到他們已被授與存取權的資源，請確定來賓使用者已選取正確的目錄。 來賓使用者可能有多個目錄的存取權。 若要切換目錄，請在左上方按一下 [**目錄 + 訂**用帳戶]，然後按一下適當的目錄。

![Azure 入口網站中的 [目錄 + 訂用帳戶] 窗格](./media/role-assignments-external-users/directory-subscription.png)

## <a name="next-steps"></a>後續步驟

- [在 Azure 入口網站中新增 Azure Active Directory B2B 共同作業使用者](../active-directory/b2b/add-users-administrator.md)
- [Azure Active Directory B2B 共同作業使用者的屬性](../active-directory/b2b/user-properties.md)
- [B2B 共同作業邀請電子郵件的元素 - Azure Active Directory](../active-directory/b2b/invitation-email-elements.md)
- [將來賓使用者新增為共同管理員](classic-administrators.md#add-a-guest-user-as-a-co-administrator)