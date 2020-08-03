---
title: 在 Privileged Identity Management Azure AD 中，更新成員指派已過期的群組擁有者 |Microsoft Docs
description: 瞭解如何在 Azure AD Privileged Identity Management （PIM）中，擴充或更新角色可指派的群組指派。
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
ms.date: 06/30/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0cfbb0be22dee4550050d6af10314f3a3bb1f583
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87505987"
---
# <a name="extend-or-renew-privileged-access-group-assignments-preview-in-privileged-identity-management"></a>擴充或更新 Privileged Identity Management 中的特殊許可權存取群組指派（預覽）

Azure Active Directory （Azure AD） Privileged Identity Management （PIM）提供控制項來管理特殊許可權存取群組的存取和指派生命週期。 系統管理員可以使用開始和結束日期時間屬性來指派角色。 指派結束方法時，Privileged Identity Management 會將電子郵件通知傳送給受影響的使用者或群組。 也會傳送電子郵件通知給資源的系統管理員，以確保能維護正確的存取權。 如果存取權未延長，指派可能會進行更新，而且仍會以過期狀態顯示長達 30 天。

## <a name="who-can-extend-and-renew"></a>誰可以擴充和續約

只有資源的系統管理員可以擴充或更新特殊許可權存取群組指派。 受影響的使用者或群組可以要求擴充即將到期的指派，並要求更新已過期的指派。

## <a name="when-notifications-are-sent"></a>傳送通知時

Privileged Identity Management 會將電子郵件通知傳送給系統管理員，以及即將到期之特殊許可權存取群組指派的受影響使用者：

- 在到期前的14天內
- 到期前一天
- 當指派到期時

當使用者或群組要求延長或更新到期或過期的指派時，系統管理員會收到通知。 當系統管理員解決要求時，所有系統管理員和要求的使用者都會收到核准或拒絕的通知。

## <a name="extend-group-assignments"></a>延伸群組指派

下列步驟概述要求、解析或管理群組指派之延伸或更新的程式。

### <a name="self-extend-expiring-assignments"></a>自我延伸過期的指派

指派給特殊許可權存取群組的使用者可以直接從群組的 [**指派** **] 頁面上的**[**合格**] 或 [作用中] 索引標籤，延伸群組指派的到期。 使用者或群組可以要求擴充合格和作用中的指派，在接下來的14天內到期。

![[我的角色] 頁面，其中列出具有動作資料行的合格 assgnments](media/groups-renew-extend/self-extend-group-assignment.png)

當指派結束日期時間在14天內時，就可以使用 [**擴充**] 命令。 若要要求群組指派的延伸模組，請選取 [**擴充**] 以開啟要求表單。

![使用原因方塊和詳細資料擴充群組指派窗格](media/groups-renew-extend/extend-request-details-group-assignment.png)

>[!NOTE]
>建議包含為何需要延長的詳細資料，以及應該給予多久的延長 (如果您有此資訊的話)。

在幾分鐘內，系統管理員會收到電子郵件通知，要求他們檢查延伸模組要求。 如果已提交擴充的要求，入口網站中會出現 Azure 通知。

若要查看或取消要求的狀態，請開啟群組指派的 [**擱置要求**] 頁面。

![特殊許可權存取群組指派-顯示要取消之連結的 [擱置要求] 頁面](media/groups-renew-extend/group-assignment-extend-cancel-request.png)

### <a name="admin-approved-extension"></a>管理員核准的延伸模組

當使用者或群組提交延伸群組指派的要求時，系統管理員會收到電子郵件通知，其中包含原始指派的詳細資料，以及要求的原因。 通知包含可供管理員核准或拒絕要求的直接連結。

除了使用電子郵件中的連結以外，系統管理員還可以前往 Privileged Identity Management 系統管理入口網站，然後在左窗格中選取 [**核准要求**]，來核准或拒絕要求。

![特殊許可權存取群組指派-[核准要求] 頁面，列出要核准或拒絕的要求和連結](media/groups-renew-extend/group-assignment-extend-admin-approve.png)

當系統管理員選取 [**核准**] 或 [**拒絕**] 時，會顯示要求的詳細資料，以及可為 audit 記錄供應商業理由的欄位。

![核准具有要求者原因、指派類型、開始時間、結束時間和原因的群組指派要求](media/groups-renew-extend/group-assignment-extend-admin-approve-reason.png)

在核准擴充群組指派的要求時，資源管理員可以選擇新的開始日期、結束日期和指派類型。 如果管理員想要提供有限的存取權來完成特定工作 (例如一天)，則可能需要變更指派類型。 在此範例中，管理員可將指派從 [合格]**** 變更為 [有效]****。 這表示他們可以提供存取權給要求者，而不用要求他們啟用。

### <a name="admin-initiated-extension"></a>系統管理員起始的延伸模組

如果指派給群組的使用者不要求群組指派的擴充功能，系統管理員可以代表使用者擴充指派。 群組指派的系統管理延伸模組不需要核准，但在擴充指派之後，通知會傳送給其他所有系統管理員。

若要擴充群組指派，請流覽至 Privileged Identity Management 中的 [指派]。 尋找需要擴充的指派。 然後在動作欄中選取 [延長]****。

![[指派] 頁面，其中列出具有要擴充連結的合格群組指派](media/groups-renew-extend/group-assignment-extend-admin-approve.png)

## <a name="renew-group-assignments"></a>更新群組指派

雖然在概念上類似于要求擴充的程式，但更新已過期群組指派的程式並不相同。 使用下列步驟，指派和系統管理員可以視需要更新已過期指派的存取權。

### <a name="self-renew"></a>自我更新

無法再存取資源的使用者最多可以存取30天的過期指派歷程記錄。 若要這樣做，他們會流覽至左窗格中的 [**我的角色**]，然後選取 [**過期的指派**] 索引標籤。

![[我的角色] 頁面-過期的指派索引標籤](media/groups-renew-extend/groups-renew-from-my-roles.png)

顯示的指派清單預設為**合格指派**。 使用下拉式功能表來切換合格和作用中的指派。

若要要求更新清單中的任何群組指派，請選取 [**更新**] 動作。 然後提供要求的原因。 除了可協助資源管理員決定核准或拒絕的任何其他內容或商業理由之外，也有助於提供持續時間。

![[更新群組指派] 窗格顯示原因方塊](media/groups-renew-extend/groups-renew-request-form.png)

提交要求之後，資源管理員會收到更新群組指派的待決要求通知。

### <a name="admin-approves"></a>管理員核准

資源管理員可以從電子郵件通知中的連結存取更新要求，或從 Azure 入口網站存取 Privileged Identity Management，然後從左窗格中選取 [**核准要求**]。

當系統管理員選取 [**核准**] 或 [**拒絕**] 時，要求的詳細資料會連同欄位一起顯示，以提供審核記錄的商業理由。

核准更新群組指派的要求時，資源管理員必須輸入新的開始日期、結束日期和指派類型。

## <a name="next-steps"></a>後續步驟

- [核准或拒絕 Privileged Identity Management 中特殊許可權存取群組指派的要求](groups-approval-workflow.md)
- [在 Privileged Identity Management 中設定特殊許可權存取群組設定](groups-role-settings.md)
