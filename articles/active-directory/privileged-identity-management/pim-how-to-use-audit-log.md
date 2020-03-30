---
title: 在 Azure AD PIM 中查看 Azure AD 角色的稽核記錄報告 |微軟文檔
description: 瞭解如何在 Azure AD 特權標識管理 （PIM） 中查看 Azure AD 角色的稽核記錄歷史記錄。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 01/07/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b8aef68e0f61e6ca995fc2bb362d59aba73ead2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78329493"
---
# <a name="view-audit-history-for-azure-ad-roles-in-privileged-identity-management"></a>查看特權標識管理中 Azure AD 角色的審核歷史記錄

您可以使用特權標識管理 （PIM） 審核歷史記錄查看過去 30 天內所有特權角色的所有角色指派和啟動。 如果要查看 Azure 活動目錄 （Azure AD） 組織中活動的完整審核歷史記錄（包括管理員、最終使用者和同步活動），可以使用[Azure 活動目錄安全和活動報表](../reports-monitoring/overview-reports.md)。

## <a name="determine-your-version-of-pim"></a>確定 PIM 的版本

從 2019 年 11 月開始，特權標識管理的 Azure AD 角色部分正在更新為與 Azure 資源角色體驗相匹配的新版本。 這將創建其他功能以及對現有[API 的更改](azure-ad-roles-features.md#api-changes)。 在推出新版本時，本文中遵循的哪些過程取決於您當前擁有的特權標識管理版本。 按照本節中的步驟確定您擁有的特權標識管理版本。 瞭解特權標識管理版本後，可以選擇本文中與該版本匹配的過程。

1. 使用處于[特權角色管理員](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)角色的使用者登錄到[Azure 門戶](https://portal.azure.com/)。
1. 打開**Azure AD 特權標識管理**。 如果概述頁面頂部有橫幅，請按照本文**的"新版本**"選項卡中的說明進行操作。 否則，請按照 **"以前版本**"選項卡中的說明操作。

    [![Azure AD 角色新版本](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "選擇版本的選項卡")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

# <a name="new-version"></a>[新版本](#tab/new)

按照以下步驟查看 Azure AD 角色的審核歷史記錄。

## <a name="view-resource-audit-history"></a>檢視資源稽核記錄

資源審核為您提供與 Azure AD 角色關聯的所有活動的視圖。

1. 打開**Azure AD 特權標識管理**。

1. 選擇**Azure AD 角色**。

1. 選擇**資源審核**。

1. 使用預先定義的日期或自訂範圍篩選記錄。

    ![包含篩選器的資源審核清單](media/azure-pim-resource-rbac/rbac-resource-audit.png)

## <a name="view-my-audit"></a>檢視我的稽核

我的稽核可讓您檢視您的個人角色活動。

1. 打開**Azure AD 特權標識管理**。

1. 選擇**Azure AD 角色**。

1. 選擇要為其查看審核歷史記錄的資源。

1. 選擇**我的審核**。

1. 使用預先定義的日期或自訂範圍篩選記錄。

    ![當前使用者的審核清單](media/azure-pim-resource-rbac/my-audit-time.png)

# <a name="previous-version"></a>[早期版本](#tab/previous)

## <a name="view-audit-history"></a>檢視稽核記錄

按照以下步驟查看 Azure AD 角色的審核歷史記錄。

1. 以屬於[特殊權限角色管理員](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)角色一員的使用者身分登入 [Azure 入口網站](https://portal.azure.com/)。

1. 打開**Azure AD 特權標識管理**。

1. 選擇**Azure AD 角色**。

1. 選擇**目錄角色審核歷史記錄**。

    根據您的審核歷史記錄，將顯示直條圖以及總啟動、每天最大啟動和每天的平均啟動。

    [![Azure AD 角色新版本](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "查看目錄角色審核歷史記錄")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    在頁面底部，將顯示一個表，其中包含有關可用審核歷史記錄中每個操作的資訊。 這些列具有以下含義：

    | 資料行 | 描述 |
    | --- | --- |
    | Time | 操作發生的時間。 |
    | 要求者 | 請求角色啟動或更改的使用者。 如果該值是**Azure 系統**，請查看 Azure 審核歷史記錄以瞭解更多資訊。 |
    | 動作 | 要求者執行的操作。 操作可以包括分配、取消分配、啟動、停用或添加外部PIM。 |
    | member | 正在啟動或分配給角色的使用者。 |
    | 角色 | 使用者分配或啟動的角色。 |
    | 推理 | 啟動期間輸入原因欄位中的文本。 |
    | 到期 | 啟動的角色過期時。 僅適用于符合條件的角色指派。 |

1. 要對審核歷史記錄進行排序，請按一下 **"時間**、**操作**"和"**角色**"按鈕。

## <a name="filter-audit-history"></a>篩選稽核記錄

1. 在審核歷史記錄頁的頂部，按一下 **"篩選"** 按鈕。

    將顯示 **"更新圖表"參數**窗格。

1. 在**時間範圍**中，選擇時間範圍。

1. 在 **"角色**"中，選擇核取方塊以指示要查看的角色。

    ![更新圖表參數窗格](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. 選擇 **"完成"** 以查看篩選的審核歷史記錄。

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>獲取審批事件的原因、審批人和票證編號

1. 使用特權角色管理員角色許可權登錄到[Azure 門戶](https://aad.portal.azure.com)，並打開 Azure AD。
1. 選擇**稽核記錄**。
1. 使用 **"服務"** 篩選器僅顯示特權標識管理服務的審核事件。 在**稽核記錄**頁上，您可以：

    - 請參閱"**狀態原因**"列中審核事件的原因。
    - 有關"將成員添加到已批准的角色請求"事件中的"**由（參與者）啟動"** 列中的核准者。

    [![Azure AD 角色新版本](media/pim-how-to-use-audit-log/filter-audit-logs.png "篩選 PIM 服務的稽核記錄")](media/pim-how-to-use-audit-log/filter-audit-logs.png)

1. 選擇稽核記錄事件以查看 **"詳細資訊**"窗格"**活動**"選項卡上的票證編號。
  
    [![Azure AD 角色新版本](media/pim-how-to-use-audit-log/audit-event-ticket-number.png "檢查審核事件的票證編號")](media/pim-how-to-use-audit-log/audit-event-ticket-number.png)

1. 您可以在審核事件的"**詳細資訊**"窗格的 **"目標**"選項卡上查看要求者（啟動角色的人員）。 Azure AD 角色有兩種目標型別：

    - 角色（**類型**= 角色）
    - 要求者（**類型**= 使用者）

通常，審批事件正上方的稽核記錄事件是"將成員添加到已完成的角色"的事件，其中**由（參與者）啟動的"參與者"** 是要求者。 在大多數情況下，您不需要從審核角度在核准請求中找到要求者。

---

## <a name="next-steps"></a>後續步驟

- [在特權標識管理中查看 Azure 資源角色的活動和審核歷史記錄](azure-pim-resource-rbac.md)
