---
title: 在 PIM 中核准 Azure 資源角色的要求-Azure AD |Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中核准或拒絕 Azure 資源角色的要求。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 096da0547806680f62b569f7c7809a7bbb04cb86
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84742074"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中核准或拒絕 Azure 資源角色的要求

您可以使用 Azure Active Directory (Azure AD) 中的 Privileged Identity Management (PIM) ，將角色設定為需要核准才能啟用，並選擇來自 Azure AD 組織的使用者或群組作為委派的核准者。 建議您為每個角色選取兩個或多個核准者，以降低特殊許可權角色系統管理員的工作負載。 委派核准者會有 24 小時的時間來核准要求。 如果未在 24 小時內核准要求，符合資格的使用者就必須重新提交新要求。 24 小時核准時間範圍是不可設定的。

依照本文中的步驟核准或拒絕 Azure 資源角色的要求。

## <a name="view-pending-requests"></a>檢視擱置的要求

身為委派核准者，當有 Azure 資源角色要求正等待您的核准時，您會收到電子郵件通知。 您可以在 Privileged Identity Management 中查看這些擱置的要求。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 選取 [ **核准要求**]。

    ![核准要求-顯示要求審查的 Azure 資源頁面](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    在 [要求啟用角色]**** 區段中，您會看見正等待您核准的要求清單。

## <a name="approve-requests"></a>核准要求

1. 尋找並選取您想要核准的要求。 [核准] 或 [拒絕] 頁面隨即出現。

    ![核准要求-具有詳細資料和理由方塊的 [核准] 或 [拒絕] 窗格](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. 在 [ **理由** ] 方塊中，輸入業務理由。

1. 選取 [核准]  。 您將會收到核准的 Azure 通知。

    ![核准顯示要求已通過核准的通知](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

## <a name="deny-requests"></a>拒絕要求

1. 尋找並選取您要拒絕的要求。 [核准] 或 [拒絕] 頁面隨即出現。

    ![核准要求-具有詳細資料和理由方塊的 [核准] 或 [拒絕] 窗格](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. 在 [ **理由** ] 方塊中，輸入業務理由。

1. 選取 [ **拒絕**]。 表示您拒絕的通知隨即顯示。

## <a name="workflow-notifications"></a>工作流程通知

以下是一些工作流程通知相關資訊：

- 當某個角色的要求正在等待審核時，核准者會收到電子郵件通知。 電子郵件通知包含可供核准者核准或拒絕要求的直接連結。
- 要求是由核准或拒絕的第一位核准者解決。
- 當核准者回應要求時，所有核准者都會收到該動作的通知。
- 當核准的使用者在其角色中變成作用中時，就會通知資源系統管理員。

>[!Note]
>如果資源系統管理員認為核准的使用者不應該處於作用中狀態，則可以移除 Privileged Identity Management 中的作用中角色指派。 雖然資源管理員不會收到擱置中要求的通知，除非它們是核准者，否則可以在 Privileged Identity Management 中查看擱置的要求，藉以查看和取消所有使用者的擱置要求。

## <a name="next-steps"></a>接下來的步驟

- [在 Privileged Identity Management 中延長或更新 Azure 資源角色](pim-resource-roles-renew-extend.md)
- [Privileged Identity Management 中的電子郵件通知](pim-email-notifications.md)
- [核准或拒絕 Privileged Identity Management 中 Azure AD 角色的要求](azure-ad-pim-approval-workflow.md)
