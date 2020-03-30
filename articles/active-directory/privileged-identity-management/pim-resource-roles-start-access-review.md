---
title: 在 PIM - Azure AD 中創建 Azure 資源角色的訪問審查 |微軟文檔
description: 瞭解如何在 Azure AD 特權標識管理 （PIM） 中創建 Azure 資源角色的訪問審閱。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847079"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>在特權標識管理中創建 Azure 資源角色的訪問審閱

員工對特權 Azure 資源角色的存取權限會隨時間而變化。 要降低與陳舊角色指派相關的風險，應定期查看存取權限。 可以使用 Azure 活動目錄 （Azure AD） 特權標識管理 （PIM） 為特權 Azure 資源角色創建訪問審核。 您還可以配置自動發生的定期訪問審核。

本文介紹如何為特權 Azure 資源角色創建一個或多個訪問審核。

## <a name="prerequisites"></a>Prerequisites

[特殊權限角色管理員](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>打開訪問審核

1. 以屬於特殊權限角色管理員角色一員的使用者身分登入 [Azure 入口網站](https://portal.azure.com/)。

1. 打開**Azure AD 特權標識管理**。

1. 在左側功能表中，選擇**Azure 資源**。

1. 選擇要管理的資源，如訂閱或管理組。

1. 在"管理"下，選擇**訪問審核**。

    ![Azure 資源 - 顯示所有審核狀態的訪問審核清單](./media/pim-resource-roles-start-access-review/access-reviews.png)

[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]

## <a name="start-the-access-review"></a>開始存取權檢閱

在指定存取權檢閱的設定後，請按一下 [啟動]****。 訪問審核將顯示在清單中，並帶有其狀態指示器。

![顯示已開始審核狀態的訪問審核清單](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

依預設，Azure AD 會在檢閱開始不久後傳送電子郵件給檢閱者。 如果您選擇不讓 Azure AD 傳送電子郵件，請務必通知檢閱者有存取權檢閱等待他們完成。 您可以向他們演示如何[查看對 Azure 資源角色的訪問](pim-resource-roles-perform-access-review.md)的說明。

## <a name="manage-the-access-review"></a>管理存取權檢閱

您可以在訪問審核的 **"概述"** 頁上跟蹤檢閱者在完成審核時的進度。 [在完成審核](pim-resource-roles-complete-access-review.md)之前，目錄中不會更改任何存取權限。

![訪問審核概述頁面，顯示審核的詳細資訊](./media/pim-resource-roles-start-access-review/access-review-overview.png)

如果這是一次性審核，則在訪問審核期結束後或管理員停止訪問審閱後，請按照["完成 Azure 資源角色的訪問審閱"](pim-resource-roles-complete-access-review.md)中的步驟查看和應用結果。  

要管理一系列訪問審核，請導航到訪問審核，您將在"計畫審核"中找到即將出現的事件，並編輯結束日期或相應地添加/刪除檢閱者。

根據您在**完成設置**中的選擇，自動應用將在審核結束日期後或手動停止審核時執行。 審核的狀態將從 **"已完成"** 更改為中間狀態，如 **"應用**"，最後更改為"**已應用"。** 您應該希望在幾分鐘內看到被拒絕的使用者（如果有）從角色中刪除。

## <a name="next-steps"></a>後續步驟

- [查看對 Azure 資源角色的訪問](pim-resource-roles-perform-access-review.md)
- [完成 Azure 資源角色的訪問審核](pim-resource-roles-complete-access-review.md)
- [創建 Azure AD 角色的訪問審核](pim-how-to-start-security-review.md)
