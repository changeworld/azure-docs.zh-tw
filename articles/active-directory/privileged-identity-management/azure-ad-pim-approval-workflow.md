---
title: 在 PIM 中批准或拒絕對 Azure AD 角色的請求 - Azure AD |微軟文檔
description: 瞭解如何在 Azure AD 特權標識管理 （PIM） 中批准或拒絕 Azure AD 角色的請求。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/07/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73ccfd1ad270072989e9b575fda538b94fd8927c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049007"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>在特權標識管理中批准或拒絕對 Azure AD 角色的請求

使用 Azure 活動目錄 （Azure AD） 特權標識管理 （PIM），可以將角色配置為需要批准進行啟動，並選擇一個或多個使用者或組作為委派的核准者。 委派核准者會有 24 小時的時間來核准要求。 如果未在 24 小時內核准要求，符合資格的使用者就必須重新提交新要求。 24 小時核准時間範圍是不可設定的。

## <a name="determine-your-version-of-pim"></a>確定 PIM 的版本

從 2019 年 11 月開始，特權標識管理的 Azure AD 角色部分正在更新為與 Azure 角色體驗相匹配的新版本。 這將創建其他功能以及對現有[API 的更改](azure-ad-roles-features.md#api-changes)。 在推出新版本時，本文中遵循的哪些過程取決於您當前擁有的特權標識管理版本。 按照本節中的步驟確定您擁有的特權標識管理版本。 瞭解特權標識管理版本後，可以選擇本文中與該版本匹配的過程。

1. 使用處于[特權角色管理員](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)角色的使用者登錄到[Azure 門戶](https://portal.azure.com/)。
1. 打開**Azure AD 特權標識管理**。 如果概述頁面頂部有橫幅，請按照本文**的"新版本**"選項卡中的說明進行操作。 否則，請按照 **"以前版本**"選項卡中的說明操作。

    [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

按照本文中的步驟批准或拒絕對 Azure AD 角色的請求。

# <a name="new-version"></a>[新版本](#tab/new)

## <a name="view-pending-requests"></a>檢視擱置的要求

作為委派的核准者，當 Azure AD 角色請求等待批准時，您將收到電子郵件通知。 您可以在特權標識管理中查看這些掛起的請求。

1. 登錄到 Azure[門戶](https://portal.azure.com/)。

1. 打開**Azure AD 特權標識管理**。

1. 選擇 **"批准請求**"。

    ![批准請求 - 顯示查看 Azure AD 角色的請求的頁面](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

    在 [要求啟用角色]**** 區段中，您會看見正等待您核准的要求清單。

## <a name="approve-requests"></a>核准要求

1. 查找並選擇要批准的請求。 將顯示批准或拒絕頁。

    ![批准請求 - 使用詳細資訊和"對齊"框批准或拒絕窗格](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

1. 在 **"對齊"** 框中，輸入業務理由。

1. 選擇 **"批准**"。 您將收到 Azure 批准通知。

    ![已批准顯示請求已審核的通知](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png))

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
- 當被批准的使用者在其角色中處於活動狀態時，將通知全域管理員和特權角色管理員。

>[!NOTE]
>全域管理員或特權角色管理員認為已批准的使用者不應處於活動狀態，則可以刪除特權標識管理中的活動角色指派。 儘管管理員不會收到掛起請求的通知，除非他們是核准者，否則他們可以通過在特權標識管理中查看掛起的請求來查看和取消所有使用者的任何掛起請求。

# <a name="previous-version"></a>[早期版本](#tab/previous)

## <a name="view-pending-requests"></a>檢視擱置的要求

作為委派的核准者，當 Azure AD 角色請求等待批准時，您將收到電子郵件通知。 您可以在特權標識管理中查看這些掛起的請求。

1. 登錄到 Azure[門戶](https://portal.azure.com/)。

1. 打開**Azure AD 特權標識管理**。

1. 按一下 [Azure AD 角色]****。

1. 按一下 [核准要求]****。

    ![Azure AD 角色 - 批准請求](./media/azure-ad-pim-approval-workflow/approve-requests.png)

    您將看到一份等待您核准的要求清單。

## <a name="approve-requests"></a>核准要求

1. 選取您要核准的要求，然後按一下 [核准]****，以開啟 [核准選取要求] 窗格。

    ![突出顯示"批准"選項的核准請求清單](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. 在 [核准原因]**** 方塊中輸入原因。

    ![出於批准原因批准選定的請求窗格](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. 按一下 **"批准**"。

    狀態符號會隨著您的核准而更新。

    ![按一下"審核"按鈕後批准選定的請求窗格](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>拒絕要求

1. 選取您要拒絕的要求，然後按一下 [拒絕]****，以開啟 [拒絕選取要求] 窗格。

    ![使用突出顯示的拒絕選項批准請求清單](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. 在 [拒絕原因]**** 方塊中輸入原因。

    ![拒絕具有拒絕原因的選定請求窗格](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. 選擇 **"拒絕**"。

    狀態符號會隨著您的拒絕而更新。

---

## <a name="next-steps"></a>後續步驟

- [特權身份管理中的電子郵件通知](pim-email-notifications.md)
- [在特權標識管理中批准或拒絕對 Azure 資源角色的請求](pim-resource-roles-approval-workflow.md)
