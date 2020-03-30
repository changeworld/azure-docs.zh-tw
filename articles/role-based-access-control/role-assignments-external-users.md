---
title: 使用 RBAC 和 Azure 門戶為外部使用者添加或刪除角色指派
description: 瞭解如何使用基於 Azure 角色的存取控制 （RBAC） 為組織外部的使用者授予對 Azure 資源的訪問。
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
ms.openlocfilehash: 1c440b85f792ac5bb1336f4d20f930aafc38ad7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245638"
---
# <a name="add-or-remove-role-assignments-for-external-guest-users-using-azure-rbac-and-the-azure-portal"></a>使用 Azure RBAC 和 Azure 門戶為外部來賓使用者添加或刪除角色指派

[基於 Azure 角色的存取控制 （RBAC）](overview.md)為大型組織和與外部協作者、供應商或自由職業者合作的中小型企業提供更好的安全管理，這些企業需要訪問環境中的特定資源，但不一定訪問整個基礎結構或任何與計費相關的作用域。 您可以使用[Azure 活動目錄 B2B](../active-directory/b2b/what-is-b2b.md)中的功能與外部來賓使用者協作，也可以使用 RBAC 僅授予來賓使用者在環境中所需的許可權。

## <a name="prerequisites"></a>Prerequisites

要添加或刪除角色指派，您必須具有：

- `Microsoft.Authorization/roleAssignments/write` 和 `Microsoft.Authorization/roleAssignments/delete` 權限，例如[使用者存取系統管理員](built-in-roles.md#user-access-administrator)或[擁有者](built-in-roles.md#owner)

## <a name="when-would-you-invite-guest-users"></a>您何時會邀請訪客使用者？

下面是一些示例方案，當您可以邀請來賓使用者加入您的組織並授予許可權時：

- 允許只有電子郵件帳戶的外部自營廠商存取專案的 Azure 資源。
- 允許外部合作夥伴管理某些資源或整個訂閱。
- 允許不在貴組織 (例如 Microsoft 支援服務) 的支援工程師暫時存取您的 Azure 資源，針對問題進行疑難排解。

## <a name="permission-differences-between-member-users-and-guest-users"></a>成員使用者和來賓使用者之間的許可權差異

目錄的本機成員（成員使用者）具有不同于作為 B2B 協作來賓（來賓使用者）從其他目錄中邀請的使用者具有不同的許可權。 例如，成員使用者可以讀取幾乎所有目錄資訊，而來賓使用者具有受限的目錄許可權。 有關成員使用者和來賓使用者的詳細資訊，請參閱[Azure 活動目錄中的預設使用者許可權是什麼？](../active-directory/fundamentals/users-default-permissions.md)

## <a name="add-a-guest-user-to-your-directory"></a>將來賓使用者新增至您的目錄

按照以下步驟使用 Azure 活動目錄頁面將來賓使用者添加到目錄。

1. 確保組織的外部協作設置已配置為允許您邀請來賓。 有關詳細資訊，請參閱啟用[B2B 外部協作並管理誰可以邀請來賓](../active-directory/b2b/delegate-invitations.md)。

1. 在 Azure 門戶中，按一下**Azure 活動目錄** > **使用者** > **"新來賓使用者**"。

    ![Azure 門戶中的新來賓使用者功能](./media/role-assignments-external-users/invite-guest-user.png)

1. 按照步驟添加新來賓使用者。 有關詳細資訊，請參閱在[Azure 門戶中添加 Azure 活動目錄 B2B 協作使用者](../active-directory/b2b/add-users-administrator.md#add-guest-users-to-the-directory)。

將來賓使用者新增至目錄之後，您就可以將共用應用程式的直接連結傳送給來賓使用者，或者來賓使用者可以按一下邀請電子郵件中的兌換 URL。

![來賓使用者邀請電子郵件](./media/role-assignments-external-users/invite-email.png)

要使來賓使用者能夠訪問您的目錄，他們必須完成邀請過程。

![來賓使用者邀請審閱許可權](./media/role-assignments-external-users/invite-review-permissions.png)

有關邀請過程的詳細資訊，請參閱 Azure[活動目錄 B2B 協作邀請兌換](../active-directory/b2b/redemption-experience.md)。

## <a name="add-a-role-assignment-for-a-guest-user"></a>為來賓使用者添加角色指派

在 RBAC 中，若要授與存取權，您必須指派角色。 要為來賓使用者添加角色指派，請執行與成員使用者、組、服務主體或託管標識[相同的步驟](role-assignments-portal.md#add-a-role-assignment)。 按照以下步驟，為不同作用域的來賓使用者添加角色指派。

1. 在 Azure 門戶中，按一下 **"所有服務**"。

1.  選擇訪問應用於的資源集，也稱為作用域。 例如，您可以選取 [管理群組]****、[訂用帳戶]****、[資源群組]**** 或資源。

1. 按一下特定資源。

1. 按一下**存取控制 （IAM）。**

    以下螢幕截圖顯示了資源組的存取控制 （IAM） 邊欄選項卡的示例。 如果在此處進行任何存取控制更改，這些更改將僅應用於資源組。

    ![資源群組的存取控制 (IAM) 刀鋒視窗](./media/role-assignments-external-users/access-control-resource-group.png)

1. 按一下 [角色指派]**** 索引標籤以檢視此範圍中的所有角色指派。

1. 按一下**Add** > "**添加添加角色指派**"以打開"添加角色指派"窗格。

    若您沒有指派角色的權限，[新增角色指派] 選項將會被停用。

    ![[新增] 功能表](./media/role-assignments-external-users/add-menu.png)

1. 在 [角色]**** 下拉式清單中選取角色，例如 [虛擬機器參與者]****。

1. 在 **"選擇**"清單中，選擇來賓使用者。 如果在清單中看不到使用者，則可以在 **"選擇"** 框中鍵入以搜索目錄以獲取顯示名稱、電子郵件地址和物件識別碼。

   ![[新增角色指派] 窗格](./media/role-assignments-external-users/add-role-assignment.png)

1. 按一下 **"保存**"以在所選作用域中分配角色。

    ![虛擬機器參與者的角色指派](./media/role-assignments-external-users/access-control-role-assignments.png)

## <a name="add-a-role-assignment-for-a-guest-user-not-yet-in-your-directory"></a>為目錄中尚未的來賓使用者添加角色指派

要為來賓使用者添加角色指派，請執行與成員使用者、組、服務主體或託管標識[相同的步驟](role-assignments-portal.md#add-a-role-assignment)。

如果來賓使用者尚未在目錄中，則可以直接從"添加角色指派"窗格邀請該使用者。

1. 在 Azure 門戶中，按一下 **"所有服務**"。

1.  選擇訪問應用於的資源集，也稱為作用域。 例如，您可以選取 [管理群組]****、[訂用帳戶]****、[資源群組]**** 或資源。

1. 按一下特定資源。

1. 按一下**存取控制 （IAM）。**

1. 按一下 [角色指派]**** 索引標籤以檢視此範圍中的所有角色指派。

1. 按一下**Add** > "**添加添加角色指派**"以打開"添加角色指派"窗格。

    ![[新增] 功能表](./media/role-assignments-external-users/add-menu.png)

1. 在 [角色]**** 下拉式清單中選取角色，例如 [虛擬機器參與者]****。

1. 在 **"選擇"** 清單中，鍵入要邀請的人員的電子郵件地址並選擇此人。

   ![在"添加角色指派"窗格中邀請來賓使用者](./media/role-assignments-external-users/add-role-assignment-new-guest.png)

1. 按一下 **"保存**"可將來賓使用者添加到目錄、分配角色併發送邀請。

    幾分鐘後，您將看到有關角色指派的通知和有關邀請的資訊。

    ![角色指派和邀請使用者通知](./media/role-assignments-external-users/invited-user-notification.png)

1. 要手動邀請來賓使用者，請按右鍵並複製通知中的邀請連結。 不要按一下邀請連結，因為它會啟動邀請過程。

    邀請連結將具有以下格式：

    `https://invitations.microsoft.com/redeem/...`

1. 向來賓使用者發送邀請連結以完成邀請過程。

    有關邀請過程的詳細資訊，請參閱 Azure[活動目錄 B2B 協作邀請兌換](../active-directory/b2b/redemption-experience.md)。

## <a name="remove-a-guest-user-from-your-directory"></a>從目錄中刪除來賓使用者

在從目錄中刪除來賓使用者之前，應首先刪除該來賓使用者的任何角色指派。 按照以下步驟從目錄中刪除來賓使用者。

1. 在作用域（如管理組、訂閱、資源組或資源）處打開**存取控制 （IAM），** 其中來賓使用者具有角色指派。

1. 按一下"**角色指派**"選項卡以查看所有角色指派。

1. 在角色指派清單中，在來賓使用者旁邊添加一個核取記號，該來賓使用者具有要刪除的角色指派。

   ![刪除角色指派](./media/role-assignments-external-users/remove-role-assignment-select.png)

1. 按一下 [移除]****。

   ![移除角色指派訊息](./media/role-assignments-external-users/remove-role-assignment.png)

1. 在顯示的移除角色指派訊息中，按一下 [是]****。

1. 在左側巡覽列中，按一下**Azure 活動目錄** > **使用者**。

1. 按一下要刪除的來賓使用者。

1. 按一下 [刪除]****。

   ![刪除來賓使用者](./media/role-assignments-external-users/delete-guest-user.png)

1. 在顯示的刪除郵件中，按一下"**是**"。

## <a name="troubleshoot"></a>疑難排解

### <a name="guest-user-cannot-browse-the-directory"></a>來賓使用者無法流覽目錄

來賓使用者具有受限的目錄權限。 例如，來賓使用者無法流覽目錄，也無法搜索組或應用程式。 有關詳細資訊，請參閱[Azure 活動目錄中的預設使用者許可權是什麼？](../active-directory/fundamentals/users-default-permissions.md)

![來賓使用者無法流覽目錄中的使用者](./media/role-assignments-external-users/directory-no-users.png)

如果來賓使用者需要在目錄中獲得其他許可權，則可以為來賓使用者分配目錄角色。 如果確實希望來賓使用者具有對目錄的完全讀取存取許可權，則可以將來賓使用者添加到 Azure AD 中的[目錄閱讀器](../active-directory/users-groups-roles/directory-assign-admin-roles.md)角色。 有關詳細資訊，請參閱向[Azure 活動目錄租戶中的合作夥伴組織的使用者授予許可權](../active-directory/b2b/add-guest-to-role.md)。

![分配目錄閱讀器角色](./media/role-assignments-external-users/directory-roles.png)

### <a name="guest-user-cannot-browse-users-groups-or-service-principals-to-assign-roles"></a>來賓使用者無法流覽使用者、組或服務主體以分配角色

來賓使用者具有受限的目錄權限。 即使來賓使用者是作用域中的[擁有者](built-in-roles.md#owner)，如果他們嘗試添加角色指派以授予其他人存取權限，他們也無法流覽使用者、組或服務主體的清單。

![來賓使用者無法流覽安全主體以分配角色](./media/role-assignments-external-users/directory-no-browse.png)

如果來賓使用者知道目錄中某人的確切登錄名稱，他們可以授予存取權限。 如果確實希望來賓使用者具有對目錄的完全讀取存取許可權，則可以將來賓使用者添加到 Azure AD 中的[目錄閱讀器](../active-directory/users-groups-roles/directory-assign-admin-roles.md)角色。 有關詳細資訊，請參閱向[Azure 活動目錄租戶中的合作夥伴組織的使用者授予許可權](../active-directory/b2b/add-guest-to-role.md)。

### <a name="guest-user-cannot-register-applications-or-create-service-principals"></a>來賓使用者無法註冊應用程式或創建服務主體

來賓使用者具有受限的目錄權限。 如果來賓使用者需要能夠註冊應用程式或創建服務主體，則可以將來賓使用者添加到 Azure AD 中[的應用程式開發人員](../active-directory/users-groups-roles/directory-assign-admin-roles.md)角色。 有關詳細資訊，請參閱向[Azure 活動目錄租戶中的合作夥伴組織的使用者授予許可權](../active-directory/b2b/add-guest-to-role.md)。

![來賓使用者無法註冊應用程式](./media/role-assignments-external-users/directory-access-denied.png)

### <a name="guest-user-does-not-see-the-new-directory"></a>來賓使用者看不到新目錄

如果來賓使用者被授予對目錄的存取權限，但在嘗試在其**目錄 + 訂閱**窗格中切換時，他們看不到 Azure 門戶中列出的新目錄，請確保來賓使用者已完成邀請過程。 有關邀請過程的詳細資訊，請參閱 Azure[活動目錄 B2B 協作邀請兌換](../active-directory/b2b/redemption-experience.md)。

### <a name="guest-user-does-not-see-resources"></a>來賓使用者看不到資源

如果來賓使用者被授予對目錄的存取權限，但他們沒有看到他們在 Azure 門戶中被授予存取權限的資源，請確保來賓使用者選擇了正確的目錄。 來賓使用者可能有權訪問多個目錄。 要切換目錄，請在左上角按一下 **"目錄 + 訂閱**"，然後按一下相應的目錄。

![Azure 門戶中的目錄和訂閱窗格](./media/role-assignments-external-users/directory-subscription.png)

## <a name="next-steps"></a>後續步驟

- [在 Azure 入口網站中新增 Azure Active Directory B2B 共同作業使用者](../active-directory/b2b/add-users-administrator.md)
- [Azure Active Directory B2B 共同作業使用者的屬性](../active-directory/b2b/user-properties.md)
- [B2B 共同作業邀請電子郵件的元素 - Azure Active Directory](../active-directory/b2b/invitation-email-elements.md)
- [將來賓使用者添加為共同管理員](classic-administrators.md#add-a-guest-user-as-a-co-administrator)