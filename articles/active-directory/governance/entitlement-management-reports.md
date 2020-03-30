---
title: 在授權管理中查看&日誌的報告 - Azure AD
description: 瞭解如何在 Azure 活動目錄授權管理中查看使用者分配報告和稽核記錄。
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 514f8e86d6bd28cc5212e0f0058f00e270f43e35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128424"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management"></a>在 Azure AD 授權管理中查看報表和日誌

Azure AD 授權管理報告和 Azure AD 稽核記錄提供有關使用者可以訪問哪些資源的其他詳細資訊。 作為管理員，您可以查看使用者的訪問包和資源配置，並查看請求日誌以進行審核或確定使用者請求的狀態。 本文介紹如何使用授權管理報告和 Azure AD 稽核記錄。

觀看以下視頻，瞭解如何查看使用者在授權管理中有權訪問的資源：

>[!VIDEO https://www.youtube.com/embed/omtNJ7ySjS0]

## <a name="view-access-packages-for-a-user"></a>查看使用者的訪問包

此報表使您能夠列出使用者可以請求的所有訪問包以及當前分配給該使用者的訪問包。

**先決條件角色：** 全域管理員或使用者管理員

1. 按一下 [Azure Active Directory]****，然後按一下 [身分識別治理]****。

1. 在左側功能表中，按一下"**報告**"。

1. 按一下 **"訪問使用者包**"。

1. 按一下 **"選擇使用者**"以打開"選擇使用者"窗格。

1. 在清單中查找使用者，然後按一下"**選擇**"。

    **"可以請求"** 選項卡顯示使用者可以請求的訪問包的清單。 此清單由為訪問包定義[的請求策略](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)確定。 

    ![使用者訪問包](./media/entitlement-management-reports/access-packages-report.png)

1. 如果訪問包有多個資源角色或策略，請按一下資源角色或策略條目以查看選擇詳細資訊。

1. 按一下"**已分配"** 選項卡以查看當前分配給使用者的訪問包的清單。 將訪問包分配給使用者時，這意味著使用者可以訪問訪問包中的所有資源角色。

## <a name="view-resource-assignments-for-a-user"></a>查看使用者的資源配置

此報表使您能夠列出當前分配給授權管理中的使用者的資源。 請注意，此報告適用于使用授權管理管理的資源。 使用者可能有權訪問您目錄中的授權管理之外的其他資源。

**先決條件角色：** 全域管理員或使用者管理員

1. 按一下 [Azure Active Directory]****，然後按一下 [身分識別治理]****。

1. 在左側功能表中，按一下"**報告**"。

1. 按一下**使用者的資源配置**。

1. 按一下 **"選擇使用者**"以打開"選擇使用者"窗格。

1. 在清單中查找使用者，然後按一下"**選擇**"。

    將顯示當前分配給使用者的資源的清單。 該清單還顯示了他們從中獲取的資源角色的訪問包和策略，以及訪問的開始日期和結束日期。
    
    如果使用者在兩個或多個包中訪問同一資源，則可以按一下箭頭以查看每個包和策略。

    ![使用者的資源配置](./media/entitlement-management-reports/resource-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>確定使用者請求的狀態

要獲取有關使用者如何請求和接收訪問包存取權限的其他詳細資訊，可以使用 Azure AD 稽核記錄。 特別是，您可以使用`EntitlementManagement`和`UserManagement`類別中的日誌記錄獲取有關每個請求的處理步驟的其他詳細資訊。  

1. 按一下**Azure 活動目錄**，然後按一下**稽核記錄**。

1. 在頂部，根據要查找的審核記錄`EntitlementManagement`，`UserManagement`將**類別**更改為 或 ，具體取決於。  

1. 按一下 [套用]****。

1. 要下載日誌，請按一下"**下載**"。

當 Azure AD 收到新請求時，它會寫入審核記錄，其中**類別**是`EntitlementManagement`，**活動**通常是`User requests access package assignment`。  在 Azure 門戶中創建的直接分配的情況下，審核記錄**的活動**欄位為`Administrator directly assigns user to access package`，執行分配的使用者由**ActorUserTheName**標識。

Azure AD 將在請求進行中寫入其他審核記錄，包括：

| 類別 | 活動 | 要求狀態 |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | 請求不需要批准 |
| `UserManagement` | `Create request approval` | 請求需要批准 |
| `UserManagement` | `Add approver to request approval` | 請求需要批准 |
| `EntitlementManagement` | `Approve access package assignment request` | 請求已批准 |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |請求已批准，或不需要批准 |

分配使用者存取權限時，Azure AD 會為`EntitlementManagement`"**活動"**`Fulfill access package assignment`為類別寫入審核記錄。  接收存取權限的使用者由**ActorUser 主要名稱**欄位標識。

如果未分配存取權限，則 Azure AD 會`EntitlementManagement`為具有 **"活動"** 的類別寫入`Deny access package assignment request`審核記錄，如果請求被審批方拒絕，或者`Access package assignment request timed out (no approver action taken)`，如果請求超時，則審批方可以批准。

當使用者的訪問包分配過期、使用者取消或被管理員刪除時，Azure AD 會為`EntitlementManagement`具有**活動**項`Remove access package assignment`的類別寫入審核記錄。

## <a name="next-steps"></a>後續步驟

- [存檔報告和日誌](entitlement-management-logs-and-reporting.md)
- [排除 Azure AD 授權管理](entitlement-management-troubleshoot.md)
- [常見方案](entitlement-management-scenarios.md)
