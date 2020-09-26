---
title: 啟用 B2B 外部共同作業設定 - Azure AD
description: 了解如何啟用 Active Directory B2B 外部共同作業，以及管理誰可以邀請來賓使用者。 使用來賓邀請者角色來委派邀請。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 09/15/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb0147af559d9de4a8589344d61b06368086dd20
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91278782"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>啟用 B2B 外部共同作業和管理誰可以邀請來賓

本文說明如何啟用 Azure Active Directory 的 (Azure AD) B2B 共同作業、指定誰可以邀請來賓，以及判斷來賓使用者在您的 Azure AD 中擁有的許可權。 

根據預設，您目錄中的所有使用者和來賓都可以邀請來賓，即使他們未獲派系統管理員角色亦然。 外部共同作業設定可讓您針對組織中不同類型的使用者，開啟或關閉來賓邀請。 您也可以指派允許個人邀請來賓的角色，將邀請委派給個別使用者。

Azure AD 可讓您限制外部來賓使用者可在 Azure AD 目錄中看到的內容。 根據預設，來賓使用者會設定為有限的許可權等級，以封鎖它們列舉使用者、群組或其他目錄資源，但可讓他們看到非隱藏群組的成員資格。 新的預覽設定可讓您更進一步限制來賓存取，讓來賓只能查看自己的設定檔資訊。 如需詳細資訊，請參閱 [限制來賓存取許可權 (預覽) ](../users-groups-roles/users-restrict-guest-permissions.md)。

## <a name="configure-b2b-external-collaboration-settings"></a>設定 B2B 外部共同作業設定

使用 Azure AD B2B 共同作業，租用戶系統管理員可以設定下列邀請原則：

- 關閉邀請
- 只有系統管理員與「來賓邀請者」角色中使用者可以邀請
- 系統管理員、來賓邀請者角色與成員可以邀請
- 所有使用者 (包括來賓) 都可以邀請

根據預設，所有使用者 (包括來賓) 都可以邀請來賓使用者。

### <a name="to-configure-external-collaboration-settings"></a>若要設定外部共同作業設定：

1. 以租用戶系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 **Azure Active Directory**。
3. 選取 [外部身分識別] > [外部共同作業設定]。

4. 在 [ **來賓使用者存取限制 (預覽]) **中，選擇您希望來賓使用者擁有的存取層級：

   - **來賓使用者具有與成員相同的存取權 (最內含的) **：此選項可讓來賓具有 Azure AD 資源和目錄資料的相同存取權，以做為成員使用者。

   - **來賓使用者對目錄物件的屬性和成員資格具有有限的存取權**： (預設) 這項設定會封鎖來自某些目錄工作的來賓，例如列舉使用者、群組或其他目錄資源。 來賓可以看到所有非隱藏群組的成員資格。

   - **來賓使用者存取權僅限於其本身目錄物件的屬性和成員資格 (限制最多的) **：使用此設定時，來賓只能存取自己的設定檔。 不允許來賓查看其他使用者的設定檔、群組或群組成員資格。
  
    ![來賓使用者存取限制設定](./media/delegate-invitations/guest-user-access.png)

5. 在 [ **來賓邀請設定**] 下，選擇適當的設定：

   - **系統管理員與具備來賓邀請者角色的使用者可邀請**：若要允許系統管理員與具備「來賓邀請者」角色的使用者邀請來賓，請將此原則設定為 [是]。

   - **成員可邀請**：若要允許目錄的非系統管理員成員邀請來賓，請將此原則設定為 [是]。

   - **來賓可邀請**：若要允許來賓邀請其他來賓，請將此原則設定為 [是]。

   - 為**來賓啟用電子郵件單次密碼 (Preview) **：如需單次密碼功能的詳細資訊，請參閱[電子郵件單次密碼驗證 (預覽) ](one-time-passcode.md)。

   - 透過**使用者流程啟用來賓自助式註冊 (預覽) **：如需此設定的詳細資訊，請參閱[將自助註冊使用者流程新增至應用程式 (預覽) ](self-service-sign-up-user-flow.md)。

   > [!NOTE]
   > 如果 [ **成員可以邀請** ] 設定為 [ **否** ]，而且 **[來賓邀請者角色中的使用者可以邀請** ] 設定為 **[是]**，則 **guest 邀請者** 角色中的使用者仍然可以邀請來賓。

    ![來賓邀請設定](./media/delegate-invitations/guest-invite-settings.png)

6. 在 [共同作業 **限制**] 下，選擇是否要允許或拒絕您指定之網域的邀請。 如需詳細資訊，請參閱[允許或封鎖對特定組織的 B2B 使用者的邀請](allow-deny-list.md)。

## <a name="assign-the-guest-inviter-role-to-a-user"></a>將來賓邀請者角色指派給使用者

利用來賓邀請者角色，您可賦予個別使用者邀請來賓的能力，而不需對他們指派全域管理員或其他管理員角色。 將來賓邀請者角色指派給個人。 然後確定將 [系統管理員與具備來賓邀請者角色的使用者可邀請] 設定為 [是]。

以下範例顯示如何使用 PowerShell 將使用者新增到「來賓邀請者」角色：

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>後續步驟

請參閱下列有關 Azure AD B2B 共同作業的文章：

- [何謂 Azure AD B2B 共同作業？](what-is-b2b.md)
- [在沒有邀請的情況下新增 B2B 共同作業來賓使用者](add-user-without-invite.md)
- [將 B2B 共同作業使用者新增至角色](add-guest-to-role.md)

