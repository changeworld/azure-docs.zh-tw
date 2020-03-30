---
title: 在 PIM 中續訂 Azure 資源角色指派 - Azure AD |微軟文檔
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中延長或更新 Azure 資源角色指派。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
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
ms.openlocfilehash: 577c028582bc3b23d13e71522bb83db558065ee2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022905"
---
# <a name="extend-or-renew-azure-resource-role-assignments-in-privileged-identity-management"></a>在特權標識管理中擴展或續訂 Azure 資源角色指派

Azure 活動目錄 （Azure AD） 特權標識管理 （PIM） 提供用於管理 Azure 資源的訪問和分配生命週期的控制項。 管理員可以使用開始和結束日期時間屬性分配角色。 當分配結束時，特權標識管理向受影響的使用者或組發送電子郵件通知。 也會傳送電子郵件通知給資源的系統管理員，以確保能維護正確的存取權。 如果存取權未延長，指派可能會進行更新，而且仍會以過期狀態顯示長達 30 天。

## <a name="who-can-extend-and-renew"></a>誰可以延長和更新？

只有資源的管理員可以延長或更新角色指派。 受影響的使用者或組可以請求擴展即將過期的角色，並請求續訂已過期的角色。

## <a name="when-are-notifications-sent"></a>何時傳送通知？

特權身份管理向管理員和受影響的使用者或角色組發送電子郵件通知，這些角色將在過期前 14 天和一天內過期。 當指派正式到期時，會傳送額外的電子郵件。

當使用者或組分配過期或過期的角色請求以續訂或續訂時，管理員會收到通知。 當特定管理員解決要求時，所有其他管理員會獲知解決決策 (核准或拒絕)。 然後，將通知請求的使用者或組。

## <a name="extend-role-assignments"></a>延長角色指派

下列步驟會概述要求程序、解決或管理角色指派延長或更新。

### <a name="self-extend-expiring-assignments"></a>自擴展到期分配

分配給角色的使用者或組可以直接從資源"**我的角色"** 頁上的 **"合格**"或"**活動"** 選項卡和"特權身份監管中心**的頂層我的角色**"頁擴展即將到期的角色指派。使用者或組可以請求延長在未來 14 天內過期的合格和活動（已分配）角色。

![Azure 資源 - 我的角色頁面，其中列出了具有"操作"列的合格角色](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-ui.png)

如果指派結束日期/時間在 14 天內，則 [延長]**** 按鈕會變成使用者介面中的有效連結。 在下列範例中，假設目前日期為 3 月 27 日。

![具有"啟動"或"擴展"連結的操作列](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-within-14.png)

若要要求延長此角色指派，請選取 [延長]**** 來開啟要求表單。

![使用"原因"框擴展角色指派窗格](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-role-assignment-request.png)

若要檢視原始指派相關資訊，請展開 [指派詳細資料]****。 輸入延長要求的原因，然後選取 [延長]****。

>[!NOTE]
>建議包含為何需要延長的詳細資料，以及應該給予多久的延長 (如果您有此資訊的話)。

![擴展角色指派窗格，擴展"分配"詳細資訊](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-form-complete.png)

稍後，資源管理員會收到一封電子郵件通知，請求他們查看擴展請求。 如果已提交擴展請求，則門戶中將顯示 Azure 通知。

![說明已有掛起的角色指派擴展的通知](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-failed-existing-request.png)

轉到 **"掛起的請求"** 頁以查看請求的狀態或取消請求。

![Azure 資源 - 掛起的請求頁面列出任何掛起的請求和到"取消"的連結](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-cancel-request.png)

### <a name="admin-approved-extension"></a>管理員批准的擴展

當使用者或組提交擴展角色指派的請求時，資源管理員會收到一封電子郵件通知，其中包含原始分配的詳細資訊和請求的原因。 通知包含可供管理員核准或拒絕要求的直接連結。

除了使用電子郵件中的連結外，管理員還可以通過存取權限標識監管中心並在左側窗格中選擇 **"批准請求"** 來批准或拒絕請求。

![Azure 資源 - 批准請求頁面清單請求和連結以批准或拒絕](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

當管理員選擇 **"批准**"或 **"拒絕"** 時，將顯示請求的詳細資訊以及為稽核記錄提供業務理由的欄位。

![使用要求者原因、分配類型、開始時間、結束時間和原因批准角色指派請求](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

資源管理員在核准延長角色指派的要求時，可以選擇新的開始日期、結束日期和指派類型。 如果管理員想要提供有限的存取權來完成特定工作 (例如一天)，則可能需要變更指派類型。 在此範例中，管理員可將指派從 [合格]**** 變更為 [有效]****。 這表示他們可以提供存取權給要求者，而不用要求他們啟用。

### <a name="admin-initiated-extension"></a>管理員啟動的擴展

如果分配給角色的使用者不請求角色指派的擴展，則管理員可以代表使用者擴展分配。 角色指派的管理擴展不需要批准，但在角色擴展後將通知發送給所有其他管理員。

要擴展角色指派，請流覽到特權標識管理中的資源角色或分配視圖。 查找需要擴展的分配。 然後在動作欄中選取 [延長]****。

![Azure 資源 - 分配頁面列出符合條件的角色，並帶有要擴展的連結](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-extend.png)

## <a name="renew-role-assignments"></a>更新角色指派

雖然在概念上類似要求延長的程序，但更新已過期角色指派的程序其實不同。 使用以下步驟，分配和管理員可以在必要時續訂對過期角色的存取權限。

### <a name="self-renew"></a>自我更新

無法再訪問資源的使用者可以訪問最多 30 天的過期分配歷史記錄。 若要這樣做，請瀏覽至左側窗格中的 [我的角色]****，然後選取 Azure 資源角色區段中的 [到期的角色]****。

![我的角色頁面 - 過期的角色選項卡](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-myroles.png)

顯示的角色清單會預設為 [合格角色]****。 使用下拉式功能表來切換 [合格] 與 [有效] 指派的角色。

若要要求更新清單中的任何角色指派，請選取 [更新]**** 動作。 然後提供要求的原因。 除了提供任何其他上下文或可説明資源管理員決定批准或拒絕的業務理由外，還提供持續時間會很有説明。

![續訂角色指派窗格，顯示"原因"框](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-request-form.png)

提交要求後，資源管理員會獲知更新角色指派的要求擱置。

### <a name="admin-approves"></a>管理員核准

資源管理員可以從電子郵件通知中的連結或通過從 Azure 門戶存取權限標識管理並從左側窗格選擇 **"批准請求"** 來訪問續訂請求。

![Azure 資源 - 批准請求頁面清單請求和連結以批准或拒絕](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

當管理員選擇 **"批准**"或 **"拒絕"** 時，請求的詳細資訊將連同一個欄位一起顯示，以為稽核記錄提供業務理由。

![使用要求者原因、分配類型、開始時間、結束時間和原因批准角色指派請求](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

資源管理員在核准更新角色指派的要求時，必須輸入新的開始日期、結束日期和指派類型。

### <a name="admin-renew"></a>管理員更新

資源管理員可以從資源左側導覽功能表中的 [成員]**** 索引標籤，更新已過期的角色指派。 他們也可從資源角色的 [到期的角色]**** 索引標籤內，更新已過期的角色指派。

若要檢視所有已過期的角色指派清單，請從 [成員]**** 畫面中選取 [到期的角色]****。

![Azure 資源 - 成員頁面列出過期角色，連結可續訂](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-member-blade.png)

## <a name="next-steps"></a>後續步驟

- [在特權標識管理中批准或拒絕對 Azure 資源角色的請求](pim-resource-roles-approval-workflow.md)
- [在特權標識管理中配置 Azure 資源角色設置](pim-resource-roles-configure-role-settings.md)
