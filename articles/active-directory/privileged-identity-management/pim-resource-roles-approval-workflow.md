---
title: 批准 PIM 中的 Azure 資源角色請求 - Azure AD |微軟文檔
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中核准或拒絕 Azure 資源角色的要求。
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
ms.openlocfilehash: e779f633efccf7b594c193e165a584d22b1d653b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74021978"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-privileged-identity-management"></a>在特權標識管理中批准或拒絕對 Azure 資源角色的請求

借助 Azure 活動目錄 （Azure AD） 中的特權標識管理 （PIM），可以將角色配置為需要批准啟動，並從 Azure AD 組織中選擇使用者或組作為委派的核准者。 我們建議為每個角色選擇兩個或多個核准者，以減少特權角色管理員的工作量。 委派核准者會有 24 小時的時間來核准要求。 如果未在 24 小時內核准要求，符合資格的使用者就必須重新提交新要求。 24 小時核准時間範圍是不可設定的。

依照本文中的步驟核准或拒絕 Azure 資源角色的要求。

## <a name="view-pending-requests"></a>檢視擱置的要求

身為委派核准者，當有 Azure 資源角色要求正等待您的核准時，您會收到電子郵件通知。 您可以在特權標識管理中查看這些掛起的請求。

1. 登錄到 Azure[門戶](https://portal.azure.com/)。

1. 打開**Azure AD 特權標識管理**。

1. 選擇 **"批准請求**"。

    ![批准請求 - Azure 資源頁面顯示要查看的請求](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    在 [要求啟用角色]**** 區段中，您會看見正等待您核准的要求清單。

## <a name="approve-requests"></a>核准要求

1. 查找並選擇要批准的請求。 將顯示批准或拒絕頁。

    ![批准請求 - 使用詳細資訊和"對齊"框批准或拒絕窗格](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. 在 **"對齊"** 框中，輸入業務理由。

1. 選擇 **"批准**"。 您將收到 Azure 批准通知。

    ![已批准顯示請求已審核的通知](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

## <a name="deny-requests"></a>拒絕要求

1. 查找並選擇要拒絕的請求。 將顯示批准或拒絕頁。

    ![批准請求 - 使用詳細資訊和"對齊"框批准或拒絕窗格](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. 在 **"對齊"** 框中，輸入業務理由。

1. 選擇 **"拒絕**"。 表示您拒絕的通知隨即顯示。

## <a name="workflow-notifications"></a>工作流程通知

以下是一些工作流程通知相關資訊：

- 當角色請求等待其審核時，核准者將收到電子郵件通知。 電子郵件通知包含可供核准者核准或拒絕要求的直接連結。
- 請求由批准或拒絕的第一個審批人解決。
- 當審批人回應請求時，將通知所有核准者該操作。
- 當已批准的使用者在其角色中處於活動狀態時，將通知資源管理員。

>[!Note]
>認為已批准的使用者不應處於活動狀態的資源管理員可以刪除特權標識管理中的活動角色指派。 儘管資源管理員不會收到掛起的請求的通知，除非他們是核准者，否則他們可以通過在特權標識管理中查看掛起的請求來查看和取消所有使用者的掛起請求。

## <a name="next-steps"></a>後續步驟

- [在特權標識管理中擴展或續訂 Azure 資源角色](pim-resource-roles-renew-extend.md)
- [特權身份管理中的電子郵件通知](pim-email-notifications.md)
- [在特權標識管理中批准或拒絕對 Azure AD 角色的請求](azure-ad-pim-approval-workflow.md)
