---
title: 啟用 B2B 外部協作設置 - Azure AD
description: 瞭解如何啟用 Active Directory B2B 外部協作並管理誰可以邀請來賓使用者。 使用來賓邀請者角色委派邀請。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fb10863334392b207c7cfd2172dc9260cf15e2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272905"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>啟用 B2B 外部協作並管理誰可以邀請來賓

本文介紹如何啟用 Azure 活動目錄 （Azure AD） B2B 協作並確定誰可以邀請來賓。 預設情況下，目錄中的所有使用者和來賓都可以邀請來賓，即使他們未分配到管理員角色。 通過外部協作設置，您可以為組織中不同類型的使用者打開或關閉來賓邀請。 您還可以通過分配允許其邀請來賓的角色，將邀請委託給單個使用者。

## <a name="configure-b2b-external-collaboration-settings"></a>配置 B2B 外部協作設置

使用 Azure AD B2B 共同作業，租用戶系統管理員可以設定下列邀請原則：

- 關閉邀請
- 只有系統管理員與「來賓邀請者」角色中使用者可以邀請
- 系統管理員、來賓邀請者角色與成員可以邀請
- 所有使用者 (包括來賓) 都可以邀請

預設情況下，所有使用者（包括來賓）都可以邀請來賓使用者。

### <a name="to-configure-external-collaboration-settings"></a>要配置外部協作設置：

1. 以租戶管理員身份登錄到[Azure 門戶](https://portal.azure.com)。
2. 選擇**Azure 活動目錄** > **使用者** > **使用者使用者設置**。
3. 在 **"外部使用者**"下，選擇 **"管理外部協作設置**"。
   > [!NOTE]
   > [外部共用作業設定]**** 也在 [組織關係]**** 頁面上。 在 Azure Active Directory 中，[管理]**** 下方，移至 [組織關係]**** >  [設定]****。
4. 在 **"外部協作設置"** 頁上，選擇要啟用的策略。

   ![外部共同作業設定](./media/delegate-invitations/control-who-to-invite.png)

  - **來賓使用者許可權有限**：此策略確定目錄中來賓的許可權。 選擇 **"是**"可阻止來賓存取某些目錄任務，例如枚舉使用者、組或其他目錄資源。 選擇 **"否**"可使來賓與目錄中的常規使用者對目錄資料具有相同的存取權限。
   - **來賓邀請者角色的管理員和使用者可以邀請**：要允許管理員和使用者在"來賓邀請者"角色中邀請來賓，請此策略設置為 **"是**"。
   - **會員可以邀請**：要允許目錄中的非管理員成員邀請來賓，請此策略設置為 **"是**"。
   - **客人可以邀請**：為了允許客人邀請其他客人，請將此政策設置為 **"是**"。
   - **為來賓啟用電子郵件一次性密碼（預覽）：** 有關一次性密碼功能的詳細資訊，請參閱[電子郵件一次性密碼身份驗證（預覽）。](one-time-passcode.md)
   - **協作限制**：有關允許或阻止特定域的邀請的詳細資訊，請參閱[允許或阻止來自特定組織的 B2B 使用者的邀請](allow-deny-list.md)。

## <a name="assign-the-guest-inviter-role-to-a-user"></a>將來賓邀請者角色指派給使用者

使用來賓邀請者角色，您可以讓單個使用者能夠邀請來賓，而無需為其分配全域管理員或其他管理員角色。 將來賓邀請者角色指派給個人。 然後，請確保設置**管理員和使用者在來賓邀請者角色可以邀請**到**是**。

以下範例顯示如何使用 PowerShell 將使用者新增到「來賓邀請者」角色：

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>後續步驟

請參閱下列有關 Azure AD B2B 共同作業的文章：

- [何謂 Azure AD B2B 共同作業？](what-is-b2b.md)
- [在沒有邀請的情況下新增 B2B 共同作業來賓使用者](add-user-without-invite.md)
- [將 B2B 共同作業使用者新增至角色](add-guest-to-role.md)


