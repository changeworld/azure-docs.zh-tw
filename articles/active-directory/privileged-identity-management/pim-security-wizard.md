---
title: PIM 中的 Azure AD 角色安全性嚮導-Azure Active Directory |Microsoft Docs
description: 說明您可以透過 Azure AD Privileged Identity Management (PIM) 用來將永久特殊權限 Azure AD 角色轉換為合格角色的安全性精靈。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04bd0993873568ba7cce368ddd9277ed356b636c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79266568"
---
# <a name="azure-ad-roles-security-wizard-in-privileged-identity-management"></a>Privileged Identity Management 中的 Azure AD 角色安全性嚮導

如果您是在 Azure Active Directory （Azure AD）組織中使用 Privileged Identity Management （PIM）的第一個人，您會看到一個可供您開始使用的 wizard。 此嚮導可協助您瞭解特殊許可權身分識別的安全性風險，以及如何使用 Privileged Identity Management 來降低這些風險。 如果您想要稍後再進行變更，就不需要在精靈中對現有的角色指派進行任何變更。

## <a name="wizard-overview"></a>精靈概觀

在您的組織開始使用 Privileged Identity Management 之前，所有角色指派都是永久的：使用者一律會在這些角色中，即使他們目前不需要其許可權也一樣。 精靈的第一個步驟會顯示高特殊權限角色的清單，以及這些角色中目前有多少使用者。 如果不清楚一或多個角色，您可以向內切入某個特定角色來深入了解使用者。

精靈的第二個步驟會讓您有機會變更系統管理員的角色指派。  

> [!WARNING]
> 請務必至少有一個全域管理員，以及一個以上具有組織帳戶（而非 Microsoft 帳戶）的特殊權限角色管理員。 如果只有一個特殊權限角色管理員，組織就無法管理該帳戶刪除的 Privileged Identity Management。
> 此外，如果使用者有 Microsoft 帳戶（換句話說，他們用來登入 Skype 和 Outlook.com 等 Microsoft 服務的帳戶），則保留角色指派為永久。 如果您打算要求多重要素驗證以啟用該角色，該使用者將會被鎖定。

## <a name="run-the-wizard"></a>執行精靈

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 選取 [ **Azure AD 角色**]，然後選取 [ **Wizard]** 。

    ![[Azure AD 角色]-[Wizard] 頁面，顯示執行嚮導的3個步驟](./media/pim-security-wizard/wizard-start.png)

1. 選取 [ **1] 探索特殊許可權角色**。

1. 檢閱特殊權限角色清單，以查看哪些使用者為永久或合格。

    ![探索特殊許可權角色-顯示永久和合格成員的角色窗格](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. 選取 [**下一步]** 以選取您要讓其符合資格的使用者或群組。

    ![將成員轉換為合格頁面，並提供選項來選取您想要讓角色符合資格的成員](./media/pim-security-wizard/convert-members-eligible.png)

1. 選取使用者或群組之後，請選取 **[下一步]** 。

    ![[查看變更] 頁面，顯示具有將轉換之永久角色指派的成員](./media/pim-security-wizard/review-changes.png)

1. 選取 **[確定]** ，將永久指派轉換為合格。

    轉換完成時，您會看到通知。

    ![顯示轉換狀態的通知](./media/pim-security-wizard/notification-completion.png)

如果您需要將其他特殊權限角色指派轉換為合格，您可以再次執行此精靈。 如果您想要使用 Privileged Identity Management 介面，而不是 wizard，請參閱[在 Privileged Identity Management 中指派 Azure AD 角色](pim-how-to-add-role-to-user.md)。

## <a name="next-steps"></a>後續步驟

- [在 Privileged Identity Management 中指派 Azure AD 角色](pim-how-to-add-role-to-user.md)
- [授與其他系統管理員存取權以管理 Privileged Identity Management](pim-how-to-give-access-to-pim.md)
