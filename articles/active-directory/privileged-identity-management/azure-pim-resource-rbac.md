---
title: 在 Privileged Identity Management (PIM Azure AD) 中查看 Azure 資源角色的審核報告 |Microsoft Docs
description: 在 Azure AD Privileged Identity Management (PIM) 中檢視 Azure 資源角色的活動和稽核記錄。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45144f64789a19390984c3f9f6a660e3c3300215
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002488"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中查看 Azure 資源角色的活動和審核歷程記錄

您可以使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM)，檢視組織內 Azure 資源角色的活動、啟用及稽核記錄。 適用範圍包括訂用帳戶、資源群組甚至是虛擬機器。 Azure 入口網站中任何利用 Azure 角色型存取控制功能的資源，都可以利用 Privileged Identity Management 中的安全性和生命週期管理功能。

> [!NOTE]
> 如果您的組織具有外包管理功能給使用 [Azure 委派資源管理](../../lighthouse/concepts/azure-delegated-resource-management.md)的服務提供者，此服務提供者所授權的角色指派將不會顯示在此處。

## <a name="view-activity-and-activations"></a>檢視活動和啟用

若要查看各種資源中特定使用者採取了哪些動作，您可以檢視與指定之啟用期間相關聯的 Azure 資源活動。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 選取 [ **Azure 資源**]。

1. 選取您要在其中查看活動和啟用的資源。

1. 選取 [ **角色** ] 或 [ **成員**]。

1. 選取使用者。

    您會看到使用者在 Azure 資源中的動作摘要（依日期）。 它也會顯示同一段時間週期內最新的角色啟用。

    ![使用資源活動摘要和角色啟用的使用者詳細資料](media/azure-pim-resource-rbac/rbac-user-details.png)

1. 選取特定的角色啟用，以查看該使用者作用中時所發生的詳細資料和對應的 Azure 資源活動。

    [![已選取角色啟用和活動詳細資料](media/azure-pim-resource-rbac/export-membership.png "已選取角色啟用和活動詳細資料")](media/azure-pim-resource-rbac/export-membership.png)

## <a name="export-role-assignments-with-children"></a>匯出具有子系的角色指派

您的合規性需求可能會要求您必須提供完整的角色指派清單給稽核員。 Privileged Identity Management 可讓您查詢特定資源的角色指派，其中包含所有子資源的角色指派。 之前系統管理員很難取得訂用帳戶的角色指派完整清單，而且他們必須針對每個特定資源匯出角色指派。 您可以使用 Privileged Identity Management 來查詢訂用帳戶中所有作用中和合格的角色指派，包括所有資源群組和資源的角色指派。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 選取 [ **Azure 資源**]。

1. 選取您要匯出角色指派的資源，例如訂用帳戶。

1. 選取 [成員]。

1. 選取 [ **匯出** ] 以開啟 [匯出成員資格] 窗格。

    [![匯出成員資格窗格以匯出所有成員](media/azure-pim-resource-rbac/export-membership.png "匯出成員資格頁面以匯出所有成員")](media/azure-pim-resource-rbac/export-membership.png)

1. 選取 [ **匯出所有成員** ]，匯出 CSV 檔案中的所有角色指派。

    ![CSV 檔案中已匯出的角色指派，在 Excel 中顯示](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>檢視資源稽核記錄

資源稽核可讓您檢視資源的所有角色活動。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 選取 [ **Azure 資源**]。

1. 選取您要查看其審核記錄的資源。

1. 選取 [ **資源審核**]。

1. 使用預先定義的日期或自訂範圍篩選記錄。

    [![具有篩選的資源審核清單](media/azure-pim-resource-rbac/rbac-resource-audit.png "具有篩選的資源審核清單")](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. 在 [稽核類型] 中，選取 [啟動 (已指派 + 已啟動)]。

    [![依啟用審核類型篩選的資源審核清單](media/azure-pim-resource-rbac/rbac-audit-activity.png "依啟用篩選的資源審核清單")](media/azure-pim-resource-rbac/rbac-audit-activity.png) ![依啟用審核類型篩選的資源審核清單](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. 在 [動作] 底下，按一下使用者的 [(活動)] 以查看該使用者在 Azure 資源中的活動詳細資料。

    ![特定動作的使用者活動詳細資料](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>檢視我的稽核

我的稽核可讓您檢視您的個人角色活動。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 選取 [ **Azure 資源**]。

1. 選取您要查看其審核記錄的資源。

1. 選取 [ **我的審核**]。

1. 使用預先定義的日期或自訂範圍篩選記錄。

    [![目前使用者的審核清單](media/azure-pim-resource-rbac/my-audit-time.png "目前使用者的審核清單")](media/azure-pim-resource-rbac/my-audit-time.png)

> [!NOTE]
> 存取 audit 歷程記錄需要全域管理員或特殊許可權角色管理員角色。

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>取得核准事件的原因、核准者和票證號碼

1. 使用特殊許可權角色管理員角色許可權登入 [Azure 入口網站](https://aad.portal.azure.com) ，然後開啟 Azure AD。
1. 選取 [稽核記錄]。
1. 使用 **服務** 篩選器，只顯示許可權身分識別管理服務的 audit 事件。 在 [ **審核記錄** ] 頁面上，您可以：

    - 在 [ **狀態原因** ] 資料行中查看 audit 事件的原因。
    - 在 [將成員新增至角色要求核准] 事件的 [將成員新增至角色要求] 事件中，查看 [ **起始者 (** 動作專案]) 欄

    [![篩選 PIM 服務的審核記錄](media/azure-pim-resource-rbac/filter-audit-logs.png "篩選 PIM 服務的審核記錄")](media/azure-pim-resource-rbac/filter-audit-logs.png)

1. 在 [**詳細資料**] 窗格的 [**活動**] 索引標籤上，選取 [審核記錄] 事件以查看票證號碼。
  
    [![檢查 audit 事件的票證號碼](media/azure-pim-resource-rbac/audit-event-ticket-number.png "檢查 audit 事件的票證號碼")](media/azure-pim-resource-rbac/audit-event-ticket-number.png)]

1. 您可以在 audit 事件的 **詳細資料** 窗格的 [**目標**] 索引標籤上，查看要求者 (啟用角色) 。 有三種適用于 Azure 資源角色的目標型別：

    - Role (**Type** = role) 
    - 要求者 (**類型** = 其他) 
    - 核准者 (**Type** = User) 

    [![檢查目標型別](media/azure-pim-resource-rbac/audit-event-target-type.png "檢查目標型別")](media/azure-pim-resource-rbac/audit-event-target-type.png)

一般來說，在核准事件上方的記錄事件是「將成員新增至角色已完成」的事件，其中 **(** 動作專案) 是要求者。 在大多數情況下，您不需要從審核的觀點來尋找核准要求中的要求者。

## <a name="next-steps"></a>後續步驟

- [在 Privileged Identity Management 中指派 Azure 資源角色](pim-resource-roles-assign-roles.md)
- [在 Privileged Identity Management 中核准或拒絕 Azure 資源角色的要求](pim-resource-roles-approval-workflow.md)
- [在 Privileged Identity Management 中查看 Azure AD 角色的審核歷程記錄](pim-how-to-use-audit-log.md)
