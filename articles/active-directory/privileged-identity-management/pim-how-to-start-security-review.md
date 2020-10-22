---
title: 在 PIM 中建立 Azure AD 角色的存取權審核-Azure AD |Microsoft Docs
description: 瞭解如何在 Azure AD Privileged Identity Management (PIM) 中建立 Azure AD 角色的存取權審核。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d9fdc44681c8773d7b6f724174a74e76e57939e
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369755"
---
# <a name="create-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中建立 Azure AD 角色的存取權審核

若要減少與過時角色指派相關聯的風險，您應該定期查看存取權。 您可以使用 Azure AD Privileged Identity Management (PIM) 來建立特殊許可權 Azure AD 角色的存取權審核。 您也可以設定自動發生的週期性存取權檢查。

本文說明如何建立一或多個特殊許可權 Azure AD 角色的存取權審核。

## <a name="prerequisites"></a>先決條件

[特殊權限角色管理員](../roles/permissions-reference.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>開啟存取權評論

1. 使用特殊權限角色管理員角色成員的使用者登入 [Azure 入口網站](https://portal.azure.com/) 。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 選取 **Azure AD 角色**]。

1. 在 [管理] 底下，選取 [ **存取審核**]，然後選取 [ **新增**]。

    ![Azure AD 角色-顯示所有評論狀態的存取權審核清單](./media/pim-how-to-start-security-review/access-reviews.png)

[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]

## <a name="start-the-access-review"></a>開始存取權檢閱

當您指定存取權檢查的設定之後，請選取 [ **開始**]。 存取權審核將會出現在清單中，並顯示其狀態的指標。

![顯示已開始評論狀態的存取權審核清單](./media/pim-how-to-start-security-review/access-reviews-list.png)

依預設，Azure AD 會在檢閱開始不久後傳送電子郵件給檢閱者。 如果您選擇不讓 Azure AD 傳送電子郵件，請務必通知檢閱者有存取權檢閱等待他們完成。 您可以向他們顯示如何 [審核 Azure AD 角色存取權](pim-how-to-perform-security-review.md)的指示。

## <a name="manage-the-access-review"></a>管理存取權檢閱

您可以在審核者在存取權審查的 [ **總覽** ] 頁面上完成審核，以追蹤進度。 在 [完成審核](pim-how-to-complete-review.md)之前，不會變更目錄中的任何存取權限。

![顯示評論詳細資料的 [存取評論] 頁面](./media/pim-how-to-start-security-review/access-review-overview.png)

如果這是一次性評論，則在存取權審核期間結束或系統管理員停止存取權審核之後，請遵循 [完成 Azure AD 角色的存取權檢查](pim-how-to-complete-review.md) 中的步驟，以查看並套用結果。  

若要管理一系列的存取權評論，請流覽至存取權評論，您將會在已排程的評論中找到即將發生的專案，並據以編輯結束日期或新增/移除審核者。

根據您在 **完成設定時**所做的選擇，自動套用將會在審核的結束日期之後或您手動停止審核之後執行。 審核的狀態將會從 [已完成] 變更為 [ **已完成** ]，例如 [套用 **] 和 [** 最後] 套用至 **[狀態]**。 在幾分鐘內，您應該會看到遭到拒絕的使用者（如果有的話）會從角色中移除。

## <a name="next-steps"></a>後續步驟

- [審查 Azure AD 角色的存取權](pim-how-to-perform-security-review.md)
- [完成 Azure AD 角色的存取權審核](pim-how-to-complete-review.md)
- [建立 Azure 資源角色的存取權審核](pim-resource-roles-start-access-review.md)
