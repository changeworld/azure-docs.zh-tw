---
title: 查看特權標識管理 （PIM） 中的 Azure 資源角色的審核報告 - Azure AD |微軟文檔
description: 在 Azure AD Privileged Identity Management (PIM) 中檢視 Azure 資源角色的活動和稽核記錄。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bf65ad595fb1ab70eb6613b6d54ac2a4f69141e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78329569"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-privileged-identity-management"></a>在特權標識管理中查看 Azure 資源角色的活動和審核歷史記錄

您可以使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM)，檢視組織內 Azure 資源角色的活動、啟用及稽核記錄。 適用範圍包括訂用帳戶、資源群組甚至是虛擬機器。 Azure 門戶中利用基於 Azure 角色的存取控制功能的任何資源都可以利用特權標識管理中的安全和生命週期管理功能。

> [!NOTE]
> 如果您的組織將管理功能外包給使用[Azure 委派資源的](../../lighthouse/concepts/azure-delegated-resource-management.md)服務提供者，則該服務提供者授權的角色指派將不會在此處顯示。

## <a name="view-activity-and-activations"></a>檢視活動和啟用

若要查看各種資源中特定使用者採取了哪些動作，您可以檢視與指定之啟用期間相關聯的 Azure 資源活動。

1. 打開**Azure AD 特權標識管理**。

1. 選取 [Azure 資源]****。

1. 選擇要查看活動的資源和啟動的資源。

1. 選擇**角色**或**成員**。

1. 選取使用者。

    在 Azure 資源中按日期看到使用者操作的摘要。 它也會顯示同一段時間週期內最新的角色啟用。

    ![包含資源活動摘要和角色啟動的使用者詳細資訊](media/azure-pim-resource-rbac/rbac-user-details.png)

1. 選擇特定角色啟動以查看該使用者處於活動狀態時發生的詳細資訊和相應的 Azure 資源活動。

    [![已選擇角色啟動和活動詳細資訊](media/azure-pim-resource-rbac/export-membership.png "已選擇角色啟動和活動詳細資訊")](media/azure-pim-resource-rbac/export-membership.png)

## <a name="export-role-assignments-with-children"></a>匯出具有子系的角色指派

您的合規性需求可能會要求您必須提供完整的角色指派清單給稽核員。 特權標識管理使您能夠查詢特定資源的角色指派，其中包括所有子資源的角色指派。 之前系統管理員很難取得訂用帳戶的角色指派完整清單，而且他們必須針對每個特定資源匯出角色指派。 使用特權標識管理，可以查詢訂閱中的所有活動角色指派，包括所有資源組和資源的角色指派。

1. 打開**Azure AD 特權標識管理**。

1. 選取 [Azure 資源]****。

1. 選擇要為其匯出角色指派的資源，如訂閱。

1. 選取 [成員]****。

1. 選擇 **"匯出**"以打開"匯出成員身份"窗格。

    [![匯出成員身份窗格以匯出所有成員](media/azure-pim-resource-rbac/export-membership.png "匯出成員頁面以匯出所有成員")](media/azure-pim-resource-rbac/export-membership.png)

1. 選擇 **"匯出所有成員**"以匯出 CSV 檔中的所有角色指派。

    ![在 CSV 檔中匯出的角色指派，如在 Excel 中顯示](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>檢視資源稽核記錄

資源稽核可讓您檢視資源的所有角色活動。

1. 打開**Azure AD 特權標識管理**。

1. 選取 [Azure 資源]****。

1. 選擇要為其查看審核歷史記錄的資源。

1. 選擇**資源審核**。

1. 使用預先定義的日期或自訂範圍篩選記錄。

    [![包含篩選器的資源審核清單](media/azure-pim-resource-rbac/rbac-resource-audit.png "包含篩選器的資源審核清單")](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. 在 [稽核類型]**** 中，選取 [啟動 (已指派 + 已啟動)]****。

    [![資源審核清單按啟動審核類型](media/azure-pim-resource-rbac/rbac-audit-activity.png "資源審核清單按"啟動"篩選")](media/azure-pim-resource-rbac/rbac-audit-activity.png)![篩選 資源審核清單，按啟動審核類型篩選](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. 在 [動作]**** 底下，按一下使用者的 [(活動)]**** 以查看該使用者在 Azure 資源中的活動詳細資料。

    ![特定操作的使用者活動詳細資訊](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>檢視我的稽核

我的稽核可讓您檢視您的個人角色活動。

1. 打開**Azure AD 特權標識管理**。

1. 選取 [Azure 資源]****。

1. 選擇要為其查看審核歷史記錄的資源。

1. 選擇**我的審核**。

1. 使用預先定義的日期或自訂範圍篩選記錄。

    [![當前使用者的審核清單](media/azure-pim-resource-rbac/my-audit-time.png "當前使用者的審核清單")](media/azure-pim-resource-rbac/my-audit-time.png)

> [!NOTE]
> 訪問審核歷史記錄需要全域管理員或特權角色管理員角色角色。

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>獲取審批事件的原因、審批人和票證編號

1. 使用特權角色管理員角色許可權登錄到[Azure 門戶](https://aad.portal.azure.com)，並打開 Azure AD。
1. 選擇**稽核記錄**。
1. 使用 **"服務"** 篩選器僅顯示特權標識管理服務的審核事件。 在**稽核記錄**頁上，您可以：

    - 請參閱"**狀態原因**"列中審核事件的原因。
    - 有關"將成員添加到已批准的角色請求"事件中的"**由（參與者）啟動"** 列中的核准者。

    [![篩選 PIM 服務的稽核記錄](media/azure-pim-resource-rbac/filter-audit-logs.png "篩選 PIM 服務的稽核記錄")](media/azure-pim-resource-rbac/filter-audit-logs.png)

1. 選擇稽核記錄事件以查看 **"詳細資訊**"窗格"**活動**"選項卡上的票證編號。
  
    [![檢查審核事件的票證編號](media/azure-pim-resource-rbac/audit-event-ticket-number.png "檢查審核事件的票證編號")](media/azure-pim-resource-rbac/audit-event-ticket-number.png)|

1. 您可以在審核事件的"**詳細資訊**"窗格的 **"目標**"選項卡上查看要求者（啟動角色的人員）。 Azure 資源角色有三種目標型別：

    - 角色（**類型**= 角色）
    - 要求者（**類型**= 其他）
    - 審批人（**類型**= 使用者）

    [![檢查目標型別](media/azure-pim-resource-rbac/audit-event-target-type.png "檢查目標型別")](media/azure-pim-resource-rbac/audit-event-target-type.png)

通常，緊鄰審批事件的日誌事件是"將成員添加到已完成的角色"的事件，其中**由（參與者）啟動的"參與者"** 是要求者。 在大多數情況下，您不需要從審核角度在核准請求中找到要求者。

## <a name="next-steps"></a>後續步驟

- [在特權標識管理中分配 Azure 資源角色](pim-resource-roles-assign-roles.md)
- [在特權標識管理中批准或拒絕對 Azure 資源角色的請求](pim-resource-roles-approval-workflow.md)
- [查看特權標識管理中 Azure AD 角色的審核歷史記錄](pim-how-to-use-audit-log.md)
