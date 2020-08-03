---
title: 在 Privileged Identity Management Azure AD 中核准群組成員和擁有者的啟用要求
description: 瞭解如何在 Azure AD Privileged Identity Management （PIM）中核准或拒絕角色可指派群組的要求。
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
ms.date: 07/27/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: b3fbb92c2e3623f5fd9571cd94ae521a41139dd6
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87505857"
---
# <a name="approve-activation-requests-for-privileged-access-group-members-and-owners-preview"></a>核准特殊許可權存取群組成員和擁有者的啟用要求（預覽）

使用 Azure Active Directory （Azure AD）中的 Privileged Identity Management （PIM），您可以將特殊許可權存取群組成員和擁有者設定為需要核准才能啟用，並從您的 Azure AD 組織選擇使用者或群組作為委派的核准者。 建議您為每個群組選取兩個或多個核准者，以減少特殊許可權角色管理員的工作負載。 委派核准者會有 24 小時的時間來核准要求。 如果未在 24 小時內核准要求，符合資格的使用者就必須重新提交新要求。 24 小時核准時間範圍是不可設定的。

依照本文中的步驟核准或拒絕 Azure 資源角色的要求。

## <a name="view-pending-requests"></a>檢視擱置的要求

身為委派核准者，當有 Azure 資源角色要求正等待您的核准時，您會收到電子郵件通知。 您可以在 Privileged Identity Management 中查看擱置中的要求。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 開啟**Azure AD Privileged Identity Management**。

1. 選取 [**核准要求**]。

    ![核准要求-顯示要審查要求的 Azure 資源頁面](./media/groups-approval-workflow/groups-select-request.png)

    在 [要求啟用角色]**** 區段中，您會看見正等待您核准的要求清單。

## <a name="approve-requests"></a>核准要求

1. 尋找並選取您想要核准的要求，然後選取 [**核准**]。

    ![核准要求-包含詳細資料和理由方塊的核准或拒絕窗格](./media/groups-approval-workflow/groups-confirm-approval.png)

1. 在 [**理由**] 方塊中，輸入業務理由。

1. 選取 [確認]。 您的核准會產生 Azure 通知。

## <a name="deny-requests"></a>拒絕要求

1. 尋找並選取您要拒絕的要求，然後選取 [**拒絕**]。

    ![核准要求-包含詳細資料和理由方塊的核准或拒絕窗格](./media/groups-approval-workflow/groups-confirm-denial.png)

1. 在 [**理由**] 方塊中，輸入業務理由。

1. 選取 [確認]。 「拒絕」會產生 Azure 通知。

## <a name="workflow-notifications"></a>工作流程通知

以下是一些工作流程通知相關資訊：

- 當群組指派的要求擱置其審查時，核准者會收到電子郵件通知。 電子郵件通知包含可供核准者核准或拒絕要求的直接連結。
- 要求是由核准或拒絕的第一位核准者解決。
- 當核准者回應要求時，所有核准者都會收到動作的通知。

>[!Note]
>若系統管理員認為已核准的使用者不應該為使用中，就可以移除 Privileged Identity Management 中的作用中群組指派。 雖然資源管理員不會收到待決要求的通知，除非他們是核准者，他們可以藉由在 Privileged Identity Management 中查看擱置中的要求，來查看和取消所有使用者的擱置要求。

## <a name="next-steps"></a>後續步驟

- [擴充或更新 Privileged Identity Management 中的群組指派](pim-resource-roles-renew-extend.md)
- [Privileged Identity Management 中的電子郵件通知](pim-email-notifications.md)
- [核准或拒絕 Privileged Identity Management 中群組指派的要求](azure-ad-pim-approval-workflow.md)
