---
title: 在 PIM 中建立 Azure 資源角色的存取權審查-Azure AD |Microsoft Docs
description: 瞭解如何在 Azure AD Privileged Identity Management （PIM）中建立 Azure 資源角色的存取權審查。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae70b8386b1dc3ebd570d2651cded3eda75dfc53
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "73847079"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中建立 Azure 資源角色的存取權審查

員工的特殊許可權 Azure 資源角色的存取權會隨時間而改變。 若要降低與過時角色指派相關聯的風險，您應該定期查看存取權。 您可以使用 Azure Active Directory （Azure AD） Privileged Identity Management （PIM）來建立特殊許可權 Azure 資源角色的存取權審查。 您也可以設定自動進行的週期性存取審查。

本文說明如何針對特殊許可權 Azure 資源角色建立一或多個存取權審查。

## <a name="prerequisites"></a>Prerequisites

[特殊權限角色管理員](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>開啟存取權審查

1. 以屬於特殊權限角色管理員角色一員的使用者身分登入 [Azure 入口網站](https://portal.azure.com/)。

1. 開啟**Azure AD Privileged Identity Management**。

1. 在左側功能表中，選取 [ **Azure 資源**]。

1. 選取您想要管理的資源，例如訂用帳戶或管理群組。

1. 在 [管理] 下，選取 [**存取評論**]。

    ![Azure 資源-存取評論清單，顯示所有評論的狀態](./media/pim-resource-roles-start-access-review/access-reviews.png)

[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]

## <a name="start-the-access-review"></a>開始存取權檢閱

在指定存取權檢閱的設定後，請按一下 [啟動]****。 存取權審查會出現在您的清單中，並顯示其狀態的指標。

![存取顯示已開始審查狀態的評論清單](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

依預設，Azure AD 會在檢閱開始不久後傳送電子郵件給檢閱者。 如果您選擇不讓 Azure AD 傳送電子郵件，請務必通知檢閱者有存取權檢閱等待他們完成。 您可以向他們說明如何[檢查 Azure 資源角色的存取權](pim-resource-roles-perform-access-review.md)。

## <a name="manage-the-access-review"></a>管理存取權檢閱

當審核者在存取權審查的 [**總覽**] 頁面上完成審核時，您可以追蹤進度。 在[審核完成](pim-resource-roles-complete-access-review.md)之前，不會變更目錄中的任何存取權限。

![[存取評論] 總覽頁面，其中顯示審核的詳細資料](./media/pim-resource-roles-start-access-review/access-review-overview.png)

如果這是一次性審查，則在存取權審查期間結束後，或系統管理員停止存取權審查之後，請依照[完成 Azure 資源角色的存取權檢查](pim-resource-roles-complete-access-review.md)中的步驟來查看並套用結果。  

若要管理一系列的存取權審查，請流覽至 [存取權審查]，您會發現近期出現在排程審查中，並據此編輯結束日期或新增/移除審核者。

根據您在**完成設定時**的選擇，自動套用會在審核的結束日期之後或當您手動停止審核時執行。 審核的狀態會從 [**已完成**]**變更為 [** 中繼狀態]，例如 [套用] 和 [最後] [套用**的狀態]**。 在幾分鐘內，您應該會看到遭到拒絕的使用者（如果有的話）從角色中移除。

## <a name="next-steps"></a>後續步驟

- [審查 Azure 資源角色的存取權](pim-resource-roles-perform-access-review.md)
- [完成 Azure 資源角色的存取權審查](pim-resource-roles-complete-access-review.md)
- [建立 Azure AD 角色的存取權審查](pim-how-to-start-security-review.md)
