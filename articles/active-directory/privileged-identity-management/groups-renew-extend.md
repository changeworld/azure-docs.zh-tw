---
title: 在 Privileged Identity Management Azure AD 中更新成員指派的已過期群組擁有者 |Microsoft Docs
description: 瞭解如何在 Azure AD Privileged Identity Management (PIM) 中，擴充或更新角色可指派的群組指派。
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87505987"
---
# <a name="extend-or-renew-privileged-access-group-assignments-preview-in-privileged-identity-management"></a>在 Privileged Identity Management 中 (預覽) 擴充或更新特殊許可權存取群組指派

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 提供控制項來管理特殊許可權存取群組的存取權和指派生命週期。 系統管理員可以使用開始和結束日期時間屬性來指派角色。 當指派結束方法時，Privileged Identity Management 會將電子郵件通知傳送給受影響的使用者或群組。 也會傳送電子郵件通知給資源的系統管理員，以確保能維護正確的存取權。 如果存取權未延長，指派可能會進行更新，而且仍會以過期狀態顯示長達 30 天。

## <a name="who-can-extend-and-renew"></a>誰可以延長和續約

只有資源的系統管理員可以擴充或更新特殊許可權存取群組指派。 受影響的使用者或群組可以要求延長即將到期的指派，以及要求更新已經過期的指派。

## <a name="when-notifications-are-sent"></a>傳送通知時

Privileged Identity Management 會將電子郵件通知傳送給系統管理員，以及即將到期的特殊許可權存取群組指派的受影響使用者：

- 在到期前14天內
- 到期前的一天
- 當指派到期時

當使用者或群組要求延長或更新即將到期或已過期的指派時，系統管理員會收到通知。 當系統管理員解決要求時，所有系統管理員和要求的使用者都會收到核准或拒絕的通知。

## <a name="extend-group-assignments"></a>擴充群組指派

下列步驟概述要求、解決或管理群組指派的延伸模組或更新的程式。

### <a name="self-extend-expiring-assignments"></a>自行擴充即將到期的指派

指派給特殊許可權存取群組的使用者，可以直接從群組的 [**指派**] 頁面上的 [**合格**] 或 [作用中] 索引標籤延伸即將**到期的群組**指派。 使用者或群組可以要求延長在接下來14天內到期的合格和有效指派。

![[我的角色] 頁面列出符合資格的 assgnments 與動作資料行](media/groups-renew-extend/self-extend-group-assignment.png)

當指派結束日期時間在14天內時，可以使用 [ **擴充** ] 命令。 若要要求群組指派的擴充功能，請選取 [ **擴充** ] 以開啟要求表單。

![使用原因方塊和詳細資料來擴充群組指派窗格](media/groups-renew-extend/extend-request-details-group-assignment.png)

>[!NOTE]
>建議包含為何需要延長的詳細資料，以及應該給予多久的延長 (如果您有此資訊的話)。

在這段時間內，系統管理員會收到一封電子郵件通知，要求他們審核延伸要求。 如果已提交要延長的要求，入口網站中會出現 Azure 通知。

若要查看或取消要求的狀態，請開啟群組指派的 [ **擱置要求** ] 頁面。

![特殊許可權存取群組指派-待處理的要求頁面，顯示要取消的連結](media/groups-renew-extend/group-assignment-extend-cancel-request.png)

### <a name="admin-approved-extension"></a>系統管理員核准的延伸模組

當使用者或群組提交擴充群組指派的要求時，系統管理員會收到電子郵件通知，其中包含原始指派的詳細資料和要求的原因。 通知包含可供管理員核准或拒絕要求的直接連結。

除了使用電子郵件的連結之外，系統管理員還可以前往 Privileged Identity Management 系統管理入口網站，然後在左窗格中選取 [ **核准要求** ]，以核准或拒絕要求。

![特殊許可權存取群組指派-核准要求的頁面清單要求和連結以核准或拒絕](media/groups-renew-extend/group-assignment-extend-admin-approve.png)

當系統管理員選取 [ **核准** ] 或 [ **拒絕**] 時，會顯示要求的詳細資料，以及可為審核記錄供應商務理由的欄位。

![核准具有要求者原因、指派類型、開始時間、結束時間和原因的群組指派要求](media/groups-renew-extend/group-assignment-extend-admin-approve-reason.png)

核准擴充群組指派的要求時，資源管理員可以選擇新的開始日期、結束日期和指派類型。 如果管理員想要提供有限的存取權來完成特定工作 (例如一天)，則可能需要變更指派類型。 在此範例中，管理員可將指派從 [合格]**** 變更為 [有效]****。 這表示他們可以提供存取權給要求者，而不用要求他們啟用。

### <a name="admin-initiated-extension"></a>系統管理員起始的延伸模組

如果指派給群組的使用者不會要求群組指派的擴充功能，系統管理員可以代表使用者擴充指派。 群組指派的管理延伸不需要核准，但在擴充指派之後，會將通知傳送給所有其他系統管理員。

若要擴充群組指派，請流覽至 Privileged Identity Management 中的 [指派] 視圖。 尋找需要擴充的指派。 然後在動作欄中選取 [延長]****。

![[指派] 頁面，其中列出具有擴充連結的合格群組指派](media/groups-renew-extend/group-assignment-extend-admin-approve.png)

## <a name="renew-group-assignments"></a>更新群組指派

雖然概念上類似于要求延伸模組的程式，但更新過期群組指派的程式並不相同。 使用下列步驟，指派和系統管理員可以在必要時，更新已過期指派的存取權。

### <a name="self-renew"></a>自我更新

無法再存取資源的使用者可以存取最多30天的到期指派歷程記錄。 若要這樣做，請在左窗格中流覽至 [ **我的角色** ]，然後選取 [ **過期的指派** ] 索引標籤。

![[我的角色] 頁面-過期的指派索引標籤](media/groups-renew-extend/groups-renew-from-my-roles.png)

指派的清單預設為合格的 **指派**。 使用下拉式功能表來切換合格和有效的指派。

若要針對清單中的任何群組指派要求續約，請選取 [ **更新** ] 動作。 然後提供要求的原因。 除了可協助資源系統管理員決定核准或拒絕的任何其他內容或商業理由之外，還有助於提供持續時間。

![[更新群組指派] 窗格顯示原因方塊](media/groups-renew-extend/groups-renew-request-form.png)

提交要求之後，資源管理員會收到更新群組指派的暫止要求通知。

### <a name="admin-approves"></a>管理員核准

資源管理員可以從電子郵件通知中的連結存取更新要求，或是從 Azure 入口網站存取 Privileged Identity Management，然後從左窗格選取 [ **核准要求** ]。

當系統管理員選取 [ **核准** ] 或 [ **拒絕**] 時，會顯示要求的詳細資料和一個欄位，以提供審核記錄的業務理由。

核准更新群組指派的要求時，資源管理員必須輸入新的開始日期、結束日期和指派類型。

## <a name="next-steps"></a>接下來的步驟

- [核准或拒絕 Privileged Identity Management 中特殊許可權存取群組指派的要求](groups-approval-workflow.md)
- [在 Privileged Identity Management 中設定特殊許可權存取群組設定](groups-role-settings.md)
