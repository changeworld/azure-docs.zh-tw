---
title: 在 PIM 中完成 Azure 資源角色的存取權檢查-Azure AD |Microsoft Docs
description: 瞭解如何在 Azure Active Directory 中完成 Azure 資源角色 Privileged Identity Management 的存取權審查。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e45249245aaab97070b7e774d4b6bab6827bdc9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74021989"
---
# <a name="complete-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中完成 Azure 資源角色的存取權審查

特殊許可權角色管理員可以在[開始存取權審查](pim-resource-roles-start-access-review.md)之後，檢查特殊許可權存取權。 Azure Active Directory （Azure AD）中的 Privileged Identity Management （PIM）會自動傳送電子郵件，提示使用者檢查其存取權。 如果使用者未收到電子郵件，您可以將[如何執行存取權檢閱](pim-resource-roles-perform-access-review.md)中的指示傳送給他們。

存取權檢閱期間結束後，或所有使用者都已完成其自我檢閱後，請遵循本文的步驟來管理檢閱並查看結果。

## <a name="manage-access-reviews"></a>管理存取權檢閱

1. 移至 [Azure 入口網站](https://portal.azure.com/)。 在儀表板上，選取 [ **Azure 資源**] 服務。

2. 選取您的資源。

3. 選取儀表板的 [存取權檢閱] **** 區段。

    ![Azure 資源-存取評論清單，顯示角色、擁有者、開始日期、結束日期和狀態](media/pim-resource-roles-complete-access-review/rbac-access-review-home-list.png)

4. 選取您想要管理的存取權檢閱。

在存取權審查的詳細資料頁面上，有數個選項可用於管理該審查。 選項如下：

![用於管理審查的選項-停止、重設、套用、刪除](media/pim-resource-roles-complete-access-review/rbac-access-review-menu.png)

### <a name="stop"></a>Stop

所有存取審查都有結束日期。 選取 [**停止**] 以提早完成。 尚未完成其審查的任何使用者，在您停止評論之後將無法完成此作業。 在停止檢閱之後，即無法重新開始檢閱。

### <a name="reset"></a>重設

您可以重設存取權檢閱，以移除在其上所做的所有決策。 在您重設存取權審查之後，所有使用者都會標示為不再次檢查。

### <a name="apply"></a>套用

完成存取權檢查之後，**請選取 [** 套用] 以執行審查的結果。 如果使用者的存取權在檢閱中遭拒，則此步驟就會將其角色指派移除。  

### <a name="delete"></a>刪除

如果對檢閱不再有任何興趣，請將其刪除。 選取 [**刪除**]，然後從 Privileged Identity Management 服務移除審核。

## <a name="results"></a>結果

在 [**結果**] 頁面上，查看並下載您的審核結果清單。

![[結果] 頁面會列出使用者、結果、原因、審查者、套用者和套用結果](media/pim-resource-roles-complete-access-review/rbac-access-review-results.png)

## <a name="reviewers"></a>檢閱者

檢視檢閱者並將其新增至現有的存取權檢閱。 提醒檢閱者完成其檢閱。

![審核者頁面清單名稱和使用者主體名稱](media/pim-resource-roles-complete-access-review/rbac-access-review-reviewers.png)

## <a name="next-steps"></a>後續步驟

- [在 Privileged Identity Management 中開始使用 Azure 資源角色的存取權審查](pim-resource-roles-start-access-review.md)
- [在 Privileged Identity Management 中執行 Azure 資源角色的存取權審查](pim-resource-roles-perform-access-review.md)
