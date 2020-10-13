---
title: 在 PIM 中核准或拒絕 Azure AD 角色的要求-Azure AD |Microsoft Docs
description: 瞭解如何在 Azure AD Privileged Identity Management (PIM) 中核准或拒絕 Azure AD 角色的要求。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/07/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e0f74f98adcc8a4e8aabc96f7f35c9c55ae277f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91536353"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>核准或拒絕 Privileged Identity Management 中 Azure AD 角色的要求

您可以使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) ，將角色設定為需要核准才能啟用，並選擇一或多個使用者或群組作為委派的核准者。 委派核准者會有 24 小時的時間來核准要求。 如果未在 24 小時內核准要求，符合資格的使用者就必須重新提交新要求。 24 小時核准時間範圍是不可設定的。

## <a name="determine-your-version-of-pim"></a>判斷您的 PIM 版本

自2019年11月起，Privileged Identity Management 的 Azure AD 角色部分會更新為符合 Azure 角色體驗的新版本。 這會建立其他功能以及 [現有 API 的變更](azure-ad-roles-features.md#api-changes)。 當新版本推出時，您在本文中遵循的程式將取決於您目前擁有的 Privileged Identity Management 版本。 遵循本節中的步驟，判斷您擁有的 Privileged Identity Management 版本。 知道您的 Privileged Identity Management 版本之後，您可以在本文中選取符合該版本的程式。

1. 使用[特殊權限角色管理員](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)角色的使用者登入[Azure 入口網站](https://portal.azure.com/)。
1. 開啟 **Azure AD Privileged Identity Management**。 如果您在 [總覽] 頁面頂端有橫幅，請依照本文的 **新版本** 索引標籤中的指示進行。 否則，請遵循 [ **舊版** ] 索引標籤中的指示。

    [![選取 Azure AD > Privileged Identity Management。](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

遵循本文中的步驟，以核准或拒絕 Azure AD 角色的要求。

# <a name="new-version"></a>[新版本](#tab/new)

## <a name="view-pending-requests"></a>檢視擱置的要求

作為委派核准者，當 Azure AD 的角色要求等待您核准時，您會收到電子郵件通知。 您可以在 Privileged Identity Management 中查看這些擱置的要求。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 選取 [ **核准要求**]。

    ![核准要求-顯示 Azure AD 角色審核要求的頁面](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

    在 [要求啟用角色]**** 區段中，您會看見正等待您核准的要求清單。

## <a name="approve-requests"></a>核准要求

1. 尋找並選取您想要核准的要求。 [核准] 或 [拒絕] 頁面隨即出現。

    ![顯示 [核准要求-Azure AD 角色] 頁面的螢幕擷取畫面。](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

1. 在 [ **理由** ] 方塊中，輸入業務理由。

1. 選取 [核准]  。 您將會收到核准的 Azure 通知。

    ![核准顯示要求已通過核准的通知](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png))

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
- 當核准的使用者在其角色中啟用時，系統會通知全域管理員和特殊許可權角色管理員。

>[!NOTE]
>全域管理員或特殊許可權角色管理員，認為已核准的使用者不應處於作用中狀態，可以移除 Privileged Identity Management 中的作用中角色指派。 雖然系統管理員不會收到擱置中要求的通知，但它們是核准者，但他們可以在 Privileged Identity Management 中查看擱置的要求，藉以查看和取消所有使用者的擱置中要求。

# <a name="previous-version"></a>[先前版本](#tab/previous)

## <a name="view-pending-requests"></a>檢視擱置的要求

作為委派核准者，當 Azure AD 的角色要求等待您核准時，您會收到電子郵件通知。 您可以在 Privileged Identity Management 中查看這些擱置的要求。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 按一下 [Azure AD 角色]****。

1. 按一下 [核准要求]****。

    ![Azure AD 角色-核准要求](./media/azure-ad-pim-approval-workflow/approve-requests.png)

    您將看到一份等待您核准的要求清單。

## <a name="approve-requests"></a>核准要求

1. 選取您要核准的要求，然後按一下 [核准]****，以開啟 [核准選取要求] 窗格。

    ![已反白顯示核准選項的核准要求清單](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. 在 [核准原因]**** 方塊中輸入原因。

    ![核准選取的要求窗格（核准原因）](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. 按一下 [ **核准**]。

    狀態符號會隨著您的核准而更新。

    ![按一下 [核准] 按鈕之後核准選取的要求窗格](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>拒絕要求

1. 選取您要拒絕的要求，然後按一下 [拒絕]****，以開啟 [拒絕選取要求] 窗格。

    ![已反白顯示拒絕選項的核准要求清單](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. 在 [拒絕原因]**** 方塊中輸入原因。

    ![拒絕所選要求窗格的拒絕原因](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. 選取 [ **拒絕**]。

    狀態符號會隨著您的拒絕而更新。

---

## <a name="next-steps"></a>後續步驟

- [Privileged Identity Management 中的電子郵件通知](pim-email-notifications.md)
- [在 Privileged Identity Management 中核准或拒絕 Azure 資源角色的要求](pim-resource-roles-approval-workflow.md)
