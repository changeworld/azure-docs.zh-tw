---
title: 在 PIM 中建立 Azure 資源角色的存取權審核-Azure AD |Microsoft Docs
description: 瞭解如何在 Azure AD Privileged Identity Management (PIM) 中建立 Azure 資源角色的存取權審核。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd9e3aaf0747e9b28526a43d9f734084f8a75662
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84743689"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中建立 Azure 資源角色的存取權審核

員工在經過一段時間後會變更的特殊許可權 Azure 資源角色。 若要減少與過時角色指派相關聯的風險，您應該定期查看存取權。 您可以使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 來建立特殊許可權 Azure 資源角色的存取權審核。 您也可以設定自動發生的週期性存取權檢查。

本文說明如何針對具特殊許可權的 Azure 資源角色建立一或多個存取權審核。

## <a name="prerequisites"></a>Prerequisites

[特殊權限角色管理員](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>開啟存取權評論

1. 以屬於特殊權限角色管理員角色一員的使用者身分登入 [Azure 入口網站](https://portal.azure.com/)。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 在左側功能表中，選取 [ **Azure 資源**]。

1. 選取您要管理的資源，例如訂用帳戶或管理群組。

1. 在 [管理] 底下，選取 [ **存取審核**]。

    ![Azure 資源-顯示所有評論狀態的存取權評論清單](./media/pim-resource-roles-start-access-review/access-reviews.png)

[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]

## <a name="start-the-access-review"></a>開始存取權檢閱

在指定存取權檢閱的設定後，請按一下 [啟動]****。 存取權審核將會出現在清單中，並顯示其狀態的指標。

![顯示已開始審核狀態的存取權審核清單](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

依預設，Azure AD 會在檢閱開始不久後傳送電子郵件給檢閱者。 如果您選擇不讓 Azure AD 傳送電子郵件，請務必通知檢閱者有存取權檢閱等待他們完成。 您可以向他們顯示如何 [審核 Azure 資源角色存取權](pim-resource-roles-perform-access-review.md)的指示。

## <a name="manage-the-access-review"></a>管理存取權檢閱

您可以在審核者在存取權審查的 [ **總覽** ] 頁面上完成審核，以追蹤進度。 在 [完成審核](pim-resource-roles-complete-access-review.md)之前，不會變更目錄中的任何存取權限。

![顯示評論詳細資料的 [存取評論] 頁面](./media/pim-resource-roles-start-access-review/access-review-overview.png)

如果這是單次審核，則在存取權審核期間結束或系統管理員停止存取權審核之後，請依照 [完成 Azure 資源角色存取權檢查](pim-resource-roles-complete-access-review.md) 中的步驟來查看並套用結果。  

若要管理一系列的存取權評論，請流覽至存取權評論，您將會在已排程的評論中找到即將發生的專案，並據以編輯結束日期或新增/移除審核者。

根據您在 **完成設定時**所做的選擇，自動套用將會在審核的結束日期之後或您手動停止審核之後執行。 審核的狀態將會從 [已完成] 變更為 [ **已完成** ]，例如 [套用 **] 和 [** 最後] 套用至 **[狀態]**。 在幾分鐘內，您應該會看到遭到拒絕的使用者（如果有的話）會從角色中移除。

## <a name="next-steps"></a>接下來的步驟

- [查看 Azure 資源角色的存取權](pim-resource-roles-perform-access-review.md)
- [完成 Azure 資源角色的存取權審核](pim-resource-roles-complete-access-review.md)
- [建立 Azure AD 角色的存取權審核](pim-how-to-start-security-review.md)
