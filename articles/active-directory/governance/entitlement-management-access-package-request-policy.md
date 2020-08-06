---
title: 在 Azure AD 權利管理-Azure Active Directory 中變更存取套件的要求和核准設定
description: 瞭解如何在 Azure Active Directory 權利管理中變更存取套件的要求和核准設定。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: edf38013efb14e412fbcd43e06dcf17e61c3bc4a
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87798761"
---
# <a name="change-request-and-approval-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>在 Azure AD 權利管理中變更存取套件的要求和核准設定

身為存取套件管理員，您可以編輯原則或新增原則，隨時變更可以要求存取套件的使用者。 您也可以變更核准設定。

本文說明如何變更現有存取套件的要求和核准設定。

## <a name="choose-between-one-or-multiple-polices"></a>選擇一個或多個原則

您指定誰可以要求存取套件的方式是使用原則。 當您建立存取套件時，可以指定建立原則的要求和核准設定。 大部分的存取封裝都有單一原則，但單一存取封裝可以有多個原則。 如果您想要允許使用不同的要求和核准設定來授與不同的使用者集合，您可以為存取套件建立多個原則。 例如，單一原則不能用來將內部和外部使用者指派給相同的存取封裝。 不過，您可以在相同的存取套件中建立兩個原則，一個用於內部使用者，另一個用於外部使用者。 如果有多個套用至使用者的原則，則會在其要求選取要指派的原則時提示他們。 下圖顯示具有兩個原則的存取封裝。

![存取套件中的多個原則](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

| 案例 | 原則數目 |
| --- | --- |
| 我希望目錄中的所有使用者都擁有相同的存取套件要求和核准設定 | 一個 |
| 我希望特定連線組織中的所有使用者都能夠要求存取套件 | 一個 |
| 我想要允許目錄中的使用者，以及我目錄以外的使用者要求存取套件 | 多個 |
| 我想要為某些使用者指定不同的核准設定 | 多個 |
| 我想要讓某些使用者存取套件指派，使其在其他使用者可以擴充其存取權時過期 | 多個 |

如需套用多項原則時所使用之優先順序邏輯的詳細資訊，請參閱[多個原則](entitlement-management-troubleshoot.md#multiple-policies
)。

### <a name="open-an-existing-policy-of-request-and-approval-settings"></a>開啟要求和核准設定的現有原則

若要變更存取封裝的要求和核准設定，您需要開啟對應的原則。 請遵循下列步驟來開啟存取套件的要求和核准設定。

**必要角色：** 全域管理員、使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]  ，然後按一下 [身分識別治理]  。

1. 在左側功能表中，按一下 [**存取套件**]，然後開啟存取封裝。

1. 按一下 [**原則**]，然後按一下您想要編輯的原則。

    [原則詳細資料] 窗格會在頁面底部開啟。

    ![存取套件-原則詳細資料窗格](./media/entitlement-management-shared/policy-details.png)

1. 按一下 [**編輯**] 以編輯原則。

    ![存取套件-編輯原則](./media/entitlement-management-shared/policy-edit.png)

1. 按一下 [**要求**] 索引標籤，以開啟要求和核准設定。

1. 執行下列其中一個要求區段中的步驟。

### <a name="add-a-new-policy-of-request-and-approval-settings"></a>加入要求和核准設定的新原則

如果您有一組應該有不同要求和核准設定的使用者，您可能需要建立新的原則。 請遵循下列步驟，開始將新的原則新增至現有的存取封裝。

**必要角色：** 全域管理員、使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]  ，然後按一下 [身分識別治理]  。

1. 在左側功能表中，按一下 [**存取套件**]，然後開啟存取封裝。

1. 依序按一下 [**原則**] 和 [**新增原則**]。

1. 輸入原則的 [名稱] 和 [描述]。

    ![建立具有名稱和描述的原則](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. 按 [下一步]  以開啟 [要求]  索引標籤。

1. 執行下列其中一個要求區段中的步驟。

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

如果您要編輯原則，請按一下 [**更新**]。 如果您要新增新的原則，請按一下 [**建立**]。

## <a name="next-steps"></a>後續步驟

- [變更存取套件的生命週期設定](entitlement-management-access-package-lifecycle-policy.md)
- [查看存取套件的要求](entitlement-management-access-package-requests.md)