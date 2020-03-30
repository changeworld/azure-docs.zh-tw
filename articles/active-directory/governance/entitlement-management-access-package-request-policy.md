---
title: 更改 Azure AD 授權管理中訪問包的請求和審批設置 - Azure 活動目錄
description: 瞭解如何在 Azure 活動目錄授權管理中更改訪問包的請求和審批設置。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 493ba6396a7ceb11b917fbda5dd6c37c070f2fee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261927"
---
# <a name="change-request-and-approval-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>更改 Azure AD 授權管理中訪問包的請求和審批設置

作為訪問包管理器，您可以通過編輯策略或添加新策略來更改可以隨時請求訪問包的使用者。 您還可以更改審批設置。

本文介紹如何更改現有訪問包的請求和審批設置。

## <a name="choose-between-one-or-multiple-polices"></a>在一個或多個兩個""""""""""""""""""

指定可以請求訪問包的人員的方式與策略一起。 創建訪問包時，指定創建策略的請求和審批設置。 大多數訪問包將具有單個策略，但單個訪問包可以有多個策略。 如果要允許使用不同的請求和審批設置為不同的使用者集分配，則可以為訪問包創建多個策略。 例如，單個策略不能用於將內部和外部使用者分配給同一訪問包。 但是，您可以在同一訪問包中創建兩個策略 ， 一個用於內部使用者，一個用於外部使用者。 如果有多個策略適用于使用者，則在請求時將提示他們選擇要分配給的策略。 下圖顯示了具有兩個策略的訪問包。

![訪問包中的多個策略](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

| 狀況 | 策略數 |
| --- | --- |
| 我希望目錄中的所有使用者都具有相同的訪問包請求和審批設置 | 一個 |
| 我希望某些已連接組織的所有使用者能夠請求訪問包 | 一個 |
| 我想允許目錄中的使用者以及目錄外部的使用者請求訪問包 | 多個 |
| 我想為某些使用者指定不同的審批設置 | 多個 |
| 我希望某些使用者訪問包分配過期，而其他使用者可以擴展其存取權限 | 多個 |

有關應用多個策略時使用的優先順序邏輯的資訊，請參閱[多個策略](entitlement-management-troubleshoot.md#multiple-policies
)。

### <a name="open-an-existing-policy-of-request-and-approval-settings"></a>打開請求和審批設置的現有策略

要更改訪問包的請求和審批設置，需要打開相應的策略。 按照以下步驟打開訪問包的請求和審批設置。

**先決條件角色：** 全域管理員、使用者管理員、目錄擁有者或訪問包管理器

1. 在 Azure 入口網站中按一下 [Azure Active Directory]****，然後按一下 [身分識別治理]****。

1. 在左側功能表中，按一下 **"訪問包**"，然後打開訪問包。

1. 按一下 **"策略"，** 然後按一下要編輯的策略。

    "策略詳細資訊"窗格將在頁面底部打開。

    ![訪問包 - 策略詳細資訊窗格](./media/entitlement-management-shared/policy-details.png)

1. 按一下 **"編輯"** 以編輯策略。

    ![訪問包 - 編輯策略](./media/entitlement-management-shared/policy-edit.png)

1. 按一下"**請求**"選項卡以打開請求和審批設置。

1. 在以下請求部分之一中執行這些步驟。

### <a name="add-a-new-policy-of-request-and-approval-settings"></a>添加新的請求和審批設置策略

如果您有一組使用者應具有不同的請求和審批設置，則可能需要創建新策略。 按照以下步驟開始向現有訪問包添加新策略。

**先決條件角色：** 全域管理員、使用者管理員、目錄擁有者或訪問包管理器

1. 在 Azure 入口網站中按一下 [Azure Active Directory]****，然後按一下 [身分識別治理]****。

1. 在左側功能表中，按一下 **"訪問包**"，然後打開訪問包。

1. 按一下 **"策略"，** 然後**添加策略**。

1. 鍵入策略的名稱和說明。

    ![創建具有名稱和說明的策略](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. 按 [下一步]**** 以開啟 [要求]**** 索引標籤。

1. 在以下請求部分之一中執行這些步驟。

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

如果要編輯策略，請按一下"**更新**"。 如果要添加新策略，請按一下"**創建**"。

## <a name="next-steps"></a>後續步驟

- [更改訪問包的生命週期設置](entitlement-management-access-package-lifecycle-policy.md)
- [查看訪問包的請求](entitlement-management-access-package-requests.md)