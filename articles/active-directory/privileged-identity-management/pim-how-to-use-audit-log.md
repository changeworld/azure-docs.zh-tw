---
title: 在 PIM 中查看 Azure AD 角色的審核報表-Azure AD |Microsoft Docs
description: 瞭解如何在 Azure AD Privileged Identity Management （PIM）中查看 Azure AD 角色的審核歷程記錄。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/13/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d46036efa04b4e0225cad6e8a70cd31ad3c10bd
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024172"
---
# <a name="view-audit-history-for-azure-ad-roles-in-pim"></a>在 PIM 中查看 Azure AD 角色的審核歷程記錄

您可以使用 [Privileged Identity Management （PIM）] 審核歷程記錄，查看過去30天內所有特殊許可權角色的所有角色指派和啟用。 如果您想要在您的 Azure Active Directory （Azure AD）組織中查看活動的完整審核歷程記錄（包括系統管理員、使用者和同步處理活動），您可以使用[Azure Active Directory 安全性和活動報告](../reports-monitoring/overview-reports.md)。

## <a name="determine-your-version-of-pim"></a>判斷您的 PIM 版本

從2019年11月開始，Privileged Identity Management 的 Azure AD 角色部分更新為符合 Azure 資源角色體驗的新版本。 這會建立額外的功能，以及[現有 API 的變更](azure-ad-roles-features.md#api-changes)。 推出新版本時，您在本文中遵循的程式取決於您目前擁有的 Privileged Identity Management 版本。 請依照本節中的步驟來判斷您擁有的 Privileged Identity Management 版本。 知道您的 Privileged Identity Management 版本之後，您可以選取本文中符合該版本的程式。

1. 以[特殊權限角色管理員](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)角色的使用者身分登入[Azure 入口網站](https://portal.azure.com/)。
1. 開啟 **Azure AD Privileged Identity Management**。 如果您在 [總覽] 頁面頂端有橫幅，請遵循本文的 [**新版本**] 索引標籤中的指示。 否則，請依照 [**先前版本**] 索引標籤中的指示進行。

    ![Azure AD 角色新版本](./media/pim-how-to-add-role-to-user/pim-new-version.png)

# <a name="new-versiontabnew"></a>[新版本](#tab/new)

請遵循下列步驟來查看 Azure AD 角色的審核歷程記錄。

## <a name="view-resource-audit-history"></a>檢視資源稽核記錄

資源審核可讓您查看與您的 Azure AD 角色相關聯的所有活動。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 選取 [ **Azure AD 角色**]。

1. 選取 [資源] [ **audit**]。

1. 使用預先定義的日期或自訂範圍篩選記錄。

    ![具有篩選的資源審核清單](media/azure-pim-resource-rbac/rbac-resource-audit.png)

## <a name="view-my-audit"></a>檢視我的稽核

我的稽核可讓您檢視您的個人角色活動。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 選取 [ **Azure AD 角色**]。

1. 選取您想要查看其 audit 歷程記錄的資源。

1. 選取 [**我的審核**]。

1. 使用預先定義的日期或自訂範圍篩選記錄。

    ![目前使用者的審核清單](media/azure-pim-resource-rbac/my-audit-time.png)

# <a name="previous-versiontabprevious"></a>[先前的版本](#tab/previous)

## <a name="view-audit-history"></a>檢視稽核記錄

請遵循下列步驟來查看 Azure AD 角色的審核歷程記錄。

1. 以屬於[特殊權限角色管理員](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)角色一員的使用者身分登入 [Azure 入口網站](https://portal.azure.com/)。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 選取 [ **Azure AD 角色**]。

1. 選取 [**目錄角色**] [audit 歷程記錄]。

    根據您的 audit 歷程記錄，會顯示直條圖，以及啟用總數、每天最大啟用數，以及每天的平均啟用。

    ![目錄角色審查歷程記錄](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    在頁面底部，會顯示一個資料表，其中包含可用的審核歷程記錄中每個動作的相關資訊。 這些資料行具有下列意義：

    | Column | 說明 |
    | --- | --- |
    | 時間 | 當動作發生時。 |
    | 申請者 | 要求啟用或變更角色的使用者。 如果值是**Azure 系統**，請查看 azure audit 歷程記錄以取得詳細資訊。 |
    | 行動 | 要求者所採取的動作。 動作可以包含指派、解除指派、啟用、停用或 AddedOutsidePIM。 |
    | member | 啟用或指派給角色的使用者。 |
    | 角色 | 使用者指派或啟用的角色。 |
    | 推理 | 啟用期間輸入 [原因] 欄位中的文字。 |
    | 到期 | 當已啟用的角色過期時。 僅適用于合格的角色指派。 |

1. 若要排序 audit 歷程記錄，請按一下 [**時間**]、[**動作**] 和 [**角色**] 按鈕。

## <a name="filter-audit-history"></a>篩選稽核記錄

1. 在 [audit 歷程記錄] 頁面頂端，按一下 [**篩選**] 按鈕。

    [**更新圖表參數**] 窗格隨即出現。

1. 在 [**時間範圍**] 中，選取時間範圍。

1. 在 [**角色**] 中，選取核取方塊以指出您想要查看的角色。

    ![更新圖表參數窗格](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. 選取 [**完成**] 以查看篩選的 audit 歷程記錄。

---

## <a name="next-steps"></a>後續步驟

- [在 Privileged Identity Management 中查看 Azure 資源角色的活動和審核歷程記錄](azure-pim-resource-rbac.md)
