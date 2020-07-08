---
title: 啟用 B2B 外部共同作業設定 - Azure AD
description: 了解如何啟用 Active Directory B2B 外部共同作業，以及管理誰可以邀請來賓使用者。 使用來賓邀請者角色來委派邀請。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6a2c1a9b908503ee5afc2687ebef473ffed626a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85387175"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>啟用 B2B 外部共同作業和管理誰可以邀請來賓

本文說明如何啟用 Azure Active Directory (Azure AD) B2B 共同作業及判定誰可以邀請來賓。 根據預設，您目錄中的所有使用者和來賓都可以邀請來賓，即使他們未獲派系統管理員角色亦然。 外部共同作業設定可讓您針對組織中不同類型的使用者，開啟或關閉來賓邀請。 您也可以指派允許個人邀請來賓的角色，將邀請委派給個別使用者。

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
6. 在 [外部共同作業設定] 頁面上，選擇您想要啟用的原則。

   ![外部共同作業設定](./media/delegate-invitations/control-who-to-invite.png)

  - **來賓使用者權限有限**：此原則會決定您目錄中來賓的權限。 選取 [是] 以封鎖來賓執行特定目錄工作，例如列舉使用者、群組或其他目錄資源。 選取 [否]，讓來賓對於目錄資料具備與目錄中一般使用者相同的存取權。
   - **系統管理員與具備來賓邀請者角色的使用者可邀請**：若要允許系統管理員與具備「來賓邀請者」角色的使用者邀請來賓，請將此原則設定為 [是]。
   - **成員可邀請**：若要允許目錄的非系統管理員成員邀請來賓，請將此原則設定為 [是]。
   - **來賓可邀請**：若要允許來賓邀請其他來賓，請將此原則設定為 [是]。
   - **為來賓啟用電子郵件一次性密碼 (預覽)** ：如需一次性密碼功能的詳細資訊，請參閱[電子郵件一次性密碼驗證 (預覽)](one-time-passcode.md)。
   - **共同作業限制**：如需允許或封鎖特定網域邀請的詳細資訊，請參閱[允許或封鎖特定組織中的 B2B 使用者邀請](allow-deny-list.md)。
   
   > [!NOTE]
   > 如果 [**成員可以邀請**] 設定為 [**否**]，而且 **[來賓邀請者角色中的系統管理員和使用者可以邀請**] 設定為 **[是]**，則**來賓邀請者**角色中的使用者仍然可以邀請來賓。

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

