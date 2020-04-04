---
title: 變更 Azure AD 授權管理中存取套件的要求與核准設定 - Azure 動作目錄
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
ms.date: 03/30/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0eae4f53283c33f3d7372a606f8c0a3ac27d079
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655934"
---
# <a name="change-request-and-approval-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>變更 Azure AD 授權管理中存取套件的要求與核准設定

作為訪問包管理員,您可以通過編輯策略或添加新策略來更改可以隨時請求訪問包的使用者。 您還可以更改審批設置。

本文介紹如何更改現有訪問包的請求和審批設置。

## <a name="choose-between-one-or-multiple-polices"></a>在一個或多個兩個""""""""""""""""""

指定可以請求訪問包的人員的方式與策略一起。 創建訪問包時,指定創建策略的請求和審批設置。 大多數訪問包將具有單個策略,但單個訪問包可以有多個策略。 如果要允許使用不同的請求和審批設置為不同的使用者集分配,則可以為訪問包創建多個策略。 例如,單個策略不能用於將內部和外部使用者分配給同一訪問包。 但是,您可以在同一訪問包中創建兩個策略,一個用於內部使用者,一個用於外部使用者。 如果有多個策略適用於使用者,則在請求時將提示他們選擇要分配給的策略。 下圖顯示了具有兩個策略的訪問包。

![存取套件中的多個原則](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

| 狀況 | 原則數 |
| --- | --- |
| 我希望目錄中的所有使用者都具有相同的訪問包請求和審批設置 | 一個 |
| 我希望某些已連線組織的所有使用者能夠請求存取套件 | 一個 |
| 我想允許目錄中的使用者以及目錄外部的使用者要求存取套件 | 多個 |
| 我想為某些使用者指定不同的審批設定 | 多個 |
| 我希望某些使用者訪問包分配過期,而其他使用者可以擴展其訪問許可權 | 多個 |

有關應用多個策略時使用的優先順序邏輯的資訊,請參閱[多個策略](entitlement-management-troubleshoot.md#multiple-policies
)。

### <a name="open-an-existing-policy-of-request-and-approval-settings"></a>開啟要求與核准設定的現有原則

要更改訪問包的請求和審批設置,需要打開相應的策略。 按照以下步驟打開訪問包的請求和審批設置。

**先決條件角色:** 全域管理員、使用者管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]****，然後按一下 [身分識別治理]****。

1. 在左側功能表中,按下 **「訪問包**」,然後打開訪問包。

1. 按下 **「策略」 然後**單擊要編輯的策略。

    "策略詳細資訊"窗格將在頁面底部打開。

    ![存取套件 - 策略詳細資訊窗格](./media/entitlement-management-shared/policy-details.png)

1. 按下 **「編輯」** 以編輯策略。

    ![存取套件 - 編輯原則](./media/entitlement-management-shared/policy-edit.png)

1. 按下「**請求**」選項卡以打開請求和審批設定。

1. 在以下請求部分之一中執行這些步驟。

### <a name="add-a-new-policy-of-request-and-approval-settings"></a>新增要求與核准設定原則

如果您有一組使用者應具有不同的請求和審批設置,則可能需要創建新策略。 按照以下步驟開始向現有訪問包添加新策略。

**先決條件角色:** 全域管理員、使用者管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]****，然後按一下 [身分識別治理]****。

1. 在左側功能表中,按下 **「訪問包**」,然後打開訪問包。

1. 點選 **「策略」 來****新增政策**。

1. 鍵入策略的名稱和說明。

    ![建立具有名稱與說明的原則](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. 按 [下一步]**** 以開啟 [要求]**** 索引標籤。

1. 在以下請求部分之一中執行這些步驟。

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

如果要編輯策略,請按兩下「**更新**」。 如果要添加新策略,請按兩下"**創建**"。

## <a name="next-steps"></a>後續步驟

- [變更存取套件的生命週期設定](entitlement-management-access-package-lifecycle-policy.md)
- [檢視存取套件的要求](entitlement-management-access-package-requests.md)