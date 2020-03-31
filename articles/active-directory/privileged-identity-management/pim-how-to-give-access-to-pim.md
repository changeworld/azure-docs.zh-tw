---
title: 授予管理 PIM 的存取權限 - Azure 活動目錄 |微軟文檔
description: 了解如何授與其他系統管理員存取權以管理 Azure AD Privileged Identity Management (PIM)。
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
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7424e92f8520d13137b6ac8787523095058a005f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022121"
---
# <a name="grant-access-to-other-administrators-to-manage-privileged-identity-management"></a>授予其他管理員管理特權標識管理的許可權

為組織啟用特權標識管理 （PIM） 的全域管理員會自動獲取角色指派和特權標識管理存取權限。 但是，Azure 活動目錄 （Azure AD） 組織中的其他人在預設情況下不會獲得寫入存取權限，包括其他全域管理員。 其他全域管理員、安全管理員和安全讀取器具有對特權標識管理的唯讀存取權限。 要授予對特權標識管理的許可權，第一個使用者可以將其他人分配給**特權角色管理員**角色。

> [!NOTE]
> 管理特權標識管理需要 Azure 多重要素驗證。 由於 Microsoft 帳戶無法註冊 Azure 多重要素驗證，因此使用 Microsoft 帳戶登錄的使用者無法存取權限標識管理。

請確保特殊權限角色管理員角色中永遠至少有兩位使用者，以防一位使用者遭到鎖定或他們的帳戶遭刪除。

## <a name="grant-access-to-manage-pim"></a>授予管理 PIM 的存取權

1. 登錄到 Azure[門戶](https://portal.azure.com/)。

1. 在 Azure AD 中，打開**特權標識管理**。

1. 選擇**Azure AD 角色**。

1. 選取 [角色]****。

    ![特權標識管理 Azure AD 角色 - 角色](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. 選擇 **"特權角色管理員"角色以**打開成員頁面。

    ![特殊權限角色管理員 - 成員](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. 選擇 **"添加成員**"以打開"添加託管成員"窗格。

1. 選擇 **"選擇成員**"以打開"選擇成員"窗格。

    ![特殊權限角色管理員 - 選取成員](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. 選取成員，然後按一下 [選取]****。

1. 選擇 **"確定**"使成員符合**特權角色管理員**角色。

    當您將新角色指派給特權身份管理中的某個人時，他們將自動設定為 **"有資格**啟動角色"。

1. 要使成員成為永久成員，請在特權角色管理員成員清單中選擇該使用者。

1. 選擇**更多**，然後**永久化**以使分配永久化。

    ![特殊權限角色管理員 - 永久符合資格](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. 向使用者發送指向["使用特權標識管理開始"](pim-getting-started.md)的連結。

## <a name="remove-access-to-manage-pim"></a>移除管理 PIM 的存取權

將某人自特殊權限角色管理員角色移除之前，請確保該角色至少仍有兩位指派的使用者。

1. 登錄到 Azure[門戶](https://portal.azure.com/)。

1. 打開**Azure AD 特權標識管理**。

1. 選擇**Azure AD 角色**。

1. 選取 [角色]****。

1. 選擇 **"特權角色管理員"角色以**打開成員頁面。

1. 選擇要刪除的使用者旁邊的核取方塊，然後選擇 **"刪除成員**"。

    ![特殊權限角色管理員 - 移除成員](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. 當系統要求您確認要從角色中刪除成員時，請選擇"**是**"。

## <a name="next-steps"></a>後續步驟

- [開始使用 Privileged Identity Management](pim-getting-started.md)
