---
title: AZURE AD 在 PIM 中的角色安全嚮導 - Azure 活動目錄 |微軟文檔
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266568"
---
# <a name="azure-ad-roles-security-wizard-in-privileged-identity-management"></a>Azure AD 在特權標識管理中扮演安全嚮導

如果您是在 Azure 活動目錄 （Azure AD） 組織中使用特權標識管理 （PIM） 的第一個人，則將顯示一個嚮導以開始。 該嚮導可説明您瞭解特權標識的安全風險以及如何使用特權標識管理來降低這些風險。 如果您想要稍後再進行變更，就不需要在精靈中對現有的角色指派進行任何變更。

## <a name="wizard-overview"></a>精靈概觀

在組織開始使用特權標識管理之前，所有角色指派都是永久性的：使用者始終處於這些角色中，即使他們目前不需要其許可權。 精靈的第一個步驟會顯示高特殊權限角色的清單，以及這些角色中目前有多少使用者。 如果不清楚一或多個角色，您可以向內切入某個特定角色來深入了解使用者。

精靈的第二個步驟會讓您有機會變更系統管理員的角色指派。  

> [!WARNING]
> 重要的是，您至少有一個全域管理員和多個具有組織帳戶的特權角色管理員（而不是 Microsoft 帳戶）。 如果只有一個特權角色管理員，則如果刪除該帳戶，組織無法管理特權標識管理。
> 此外，如果使用者擁有 Microsoft 帳戶（換言之，他們用於登錄到 Microsoft 服務（如 Skype 和Outlook.com）的帳戶），則保持角色指派永久化。 如果計畫為該角色啟動需要多重要素驗證，則該使用者將被鎖定。

## <a name="run-the-wizard"></a>執行精靈

1. 登錄到 Azure[門戶](https://portal.azure.com/)。

1. 打開**Azure AD 特權標識管理**。

1. 選擇**Azure AD 角色**，然後選擇**嚮導**。

    ![Azure AD 角色 - 顯示運行嚮導的 3 個步驟的嚮導頁面](./media/pim-security-wizard/wizard-start.png)

1. 選擇**1 發現特權角色**。

1. 檢閱特殊權限角色清單，以查看哪些使用者為永久或合格。

    ![發現特權角色 - 顯示永久和合格成員的角色窗格](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. 選擇 **"下一步**"以選擇要使符合資格的使用者或組。

    ![將成員轉換為符合條件的頁面，其中有選項可選擇要獲得角色資格的成員](./media/pim-security-wizard/convert-members-eligible.png)

1. 選擇使用者或組後，選擇 **"下一步**"。

    ![查看顯示具有將轉換的永久角色指派的成員的更改頁](./media/pim-security-wizard/review-changes.png)

1. 選擇 **"確定"** 可將永久分配轉換為符合資格的。

    轉換完成時，您會看到通知。

    ![顯示轉換狀態的通知](./media/pim-security-wizard/notification-completion.png)

如果您需要將其他特殊權限角色指派轉換為合格，您可以再次執行此精靈。 如果要使用特權標識管理介面而不是嚮導，請參閱[在特權標識管理中分配 Azure AD 角色](pim-how-to-add-role-to-user.md)。

## <a name="next-steps"></a>後續步驟

- [在特權標識管理中分配 Azure AD 角色](pim-how-to-add-role-to-user.md)
- [授予其他管理員管理特權標識管理的許可權](pim-how-to-give-access-to-pim.md)
