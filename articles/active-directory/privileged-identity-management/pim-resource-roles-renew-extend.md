---
title: 更新 PIM 中的 Azure 資源角色指派-Azure AD |Microsoft Docs
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
ms.topic: how-to
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f0d22fc540aae448a3da731b709a3b4ea13a69d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84742228"
---
# <a name="extend-or-renew-azure-resource-role-assignments-in-privileged-identity-management"></a>在 Privileged Identity Management 中延長或更新 Azure 資源角色指派

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 提供控制項來管理 Azure 資源的存取權和指派生命週期。 系統管理員可以使用開始和結束日期時間屬性來指派角色。 當指派結束方法時，Privileged Identity Management 會將電子郵件通知傳送給受影響的使用者或群組。 也會傳送電子郵件通知給資源的系統管理員，以確保能維護正確的存取權。 如果存取權未延長，指派可能會進行更新，而且仍會以過期狀態顯示長達 30 天。

## <a name="who-can-extend-and-renew"></a>誰可以延長和更新？

只有資源的管理員可以延長或更新角色指派。 受影響的使用者或群組可以要求擴充即將過期的角色，以及要求更新已經過期的角色。

## <a name="when-are-notifications-sent"></a>何時傳送通知？

Privileged Identity Management 會將電子郵件通知傳送給系統管理員和受影響的使用者或在14天內到期的角色群組，以及到期前的一天。 當指派正式到期時，會傳送額外的電子郵件。

當使用者或群組指派了即將到期或已過期的角色要求來延長或續約時，系統管理員會收到通知。 當特定管理員解決要求時，所有其他管理員會獲知解決決策 (核准或拒絕)。 然後，要求的使用者或群組就會收到決定的通知。

## <a name="extend-role-assignments"></a>延長角色指派

下列步驟會概述要求程序、解決或管理角色指派延長或更新。

### <a name="self-extend-expiring-assignments"></a>自行擴充即將到期的指派

指派給角色的使用者或群組可以在資源的 [我的角色] 頁面上，從 [**我的角色**]**頁面上的**[**合格**] 或 [Privileged Identity Management**作用**中] 索引標籤，直接延伸即將過期的角色指派。使用者或群組可以要求擴充符合和作用中的 (指派) 角色，這些角色會在接下來的14天內到期。

![Azure 資源-[我的角色] 頁面列出符合資格的角色與動作資料行](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-ui.png)

如果指派結束日期/時間在 14 天內，則 [延長]**** 按鈕會變成使用者介面中的有效連結。 在下列範例中，假設目前日期為 3 月 27 日。

![具有啟動或擴充連結的動作資料行](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-within-14.png)

若要要求延長此角色指派，請選取 [延長]**** 來開啟要求表單。

![使用原因方塊擴充角色指派窗格](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-role-assignment-request.png)

若要檢視原始指派相關資訊，請展開 [指派詳細資料]****。 輸入延長要求的原因，然後選取 [延長]****。

>[!NOTE]
>建議包含為何需要延長的詳細資料，以及應該給予多久的延長 (如果您有此資訊的話)。

![擴充指派詳細資料的 [角色指派] 窗格](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-form-complete.png)

在這段時間內，資源管理員會收到電子郵件通知，要求他們審核延伸模組要求。 如果已提交要延長的要求，入口網站中會出現 Azure 通知。

![通知說明已經有現有的暫止角色指派延伸模組](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-failed-existing-request.png)

移至 [ **擱置要求** ] 頁面，以查看您的要求狀態或取消要求。

![Azure 資源-待處理的要求頁面列出任何擱置的要求和取消的連結](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-cancel-request.png)

### <a name="admin-approved-extension"></a>系統管理員核准的延伸模組

當使用者或群組提交延伸角色指派的要求時，資源管理員會收到電子郵件通知，其中包含原始指派的詳細資料和要求的原因。 通知包含可供管理員核准或拒絕要求的直接連結。

除了使用電子郵件的連結之外，系統管理員還可以前往 Privileged Identity Management 系統管理入口網站，然後在左窗格中選取 [ **核准要求** ]，以核准或拒絕要求。

![Azure 資源-核准要求的頁面，並列出核准或拒絕的要求和連結](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

當系統管理員選取 [ **核准** ] 或 [ **拒絕**] 時，會顯示要求的詳細資料，以及可為審核記錄供應商務理由的欄位。

![核准具有要求者原因、指派類型、開始時間、結束時間和原因的角色指派要求](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

資源管理員在核准延長角色指派的要求時，可以選擇新的開始日期、結束日期和指派類型。 如果管理員想要提供有限的存取權來完成特定工作 (例如一天)，則可能需要變更指派類型。 在此範例中，管理員可將指派從 [合格]**** 變更為 [有效]****。 這表示他們可以提供存取權給要求者，而不用要求他們啟用。

### <a name="admin-initiated-extension"></a>系統管理員起始的延伸模組

如果指派給角色的使用者不會要求角色指派的擴充功能，系統管理員可以代表使用者擴充指派。 角色指派的管理延伸模組不需要核准，但在擴充角色之後，會將通知傳送給所有其他系統管理員。

若要擴充角色指派，請流覽至 Privileged Identity Management 中的資源角色或指派查看。 尋找需要擴充的指派。 然後在動作欄中選取 [延長]****。

![Azure 資源-列出符合資格的角色且連結可延伸的 [指派] 頁面](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-extend.png)

## <a name="renew-role-assignments"></a>更新角色指派

雖然在概念上類似要求延長的程序，但更新已過期角色指派的程序其實不同。 使用下列步驟，指派和系統管理員可以在必要時，更新已過期角色的存取權。

### <a name="self-renew"></a>自我更新

無法再存取資源的使用者可以存取最多30天的到期指派歷程記錄。 若要這樣做，請瀏覽至左側窗格中的 [我的角色]****，然後選取 Azure 資源角色區段中的 [到期的角色]****。

![[我的角色] 頁面-過期的角色索引標籤](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-myroles.png)

顯示的角色清單會預設為 [合格角色]****。 使用下拉式功能表來切換 [合格] 與 [有效] 指派的角色。

若要要求更新清單中的任何角色指派，請選取 [更新]**** 動作。 然後提供要求的原因。 除了可協助資源系統管理員決定核准或拒絕的任何其他內容或商業理由之外，還有助於提供持續時間。

![[更新角色指派] 窗格顯示原因方塊](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-request-form.png)

提交要求後，資源管理員會獲知更新角色指派的要求擱置。

### <a name="admin-approves"></a>管理員核准

資源管理員可以從電子郵件通知中的連結存取更新要求，或是從 Azure 入口網站存取 Privileged Identity Management，然後從左窗格選取 [ **核准要求** ]。

![Azure 資源-核准要求的頁面，並列出核准或拒絕的要求和連結](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

當系統管理員選取 [ **核准** ] 或 [ **拒絕**] 時，會顯示要求的詳細資料和一個欄位，以提供審核記錄的業務理由。

![核准具有要求者原因、指派類型、開始時間、結束時間和原因的角色指派要求](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

資源管理員在核准更新角色指派的要求時，必須輸入新的開始日期、結束日期和指派類型。

### <a name="admin-renew"></a>管理員更新

資源管理員可以從資源左側導覽功能表中的 [成員]**** 索引標籤，更新已過期的角色指派。 他們也可從資源角色的 [到期的角色]**** 索引標籤內，更新已過期的角色指派。

若要檢視所有已過期的角色指派清單，請從 [成員]**** 畫面中選取 [到期的角色]****。

![Azure 資源-列出已過期角色並有更新連結的成員頁面](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-member-blade.png)

## <a name="next-steps"></a>接下來的步驟

- [在 Privileged Identity Management 中核准或拒絕 Azure 資源角色的要求](pim-resource-roles-approval-workflow.md)
- [在 Privileged Identity Management 中設定 Azure 資源角色設定](pim-resource-roles-configure-role-settings.md)
