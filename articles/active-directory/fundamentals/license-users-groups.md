---
title: 指派或移除授權 - Azure Active Directory | Microsoft Docs
description: 關於如何從您的使用者或群組指派或移除 Azure Active Directory 授權的指示。
services: active-directory
author: msaburnley
manager: daveba
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20ec1d1909a53a9de29e12be33957acfd1643698
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128833"
---
# <a name="assign-or-remove-licenses-in-the-azure-active-directory-portal"></a>在 Azure 活動目錄門戶中分配或刪除許可證

許多 Azure 活動目錄 （Azure AD） 服務要求您為該服務的每個使用者或組（和關聯成員）授予許可證。 只有具有活動許可證的使用者才能訪問和使用許可的 Azure AD 服務，而這些服務是正確的。 許可證按租戶應用，不會轉移到其他租戶。 

## <a name="available-license-plans"></a>可用的許可證計畫

Azure AD 服務可以使用多個許可證計畫，包括：

- Azure AD Free

- Azure AD Premium P1

- Azure AD Premium P2

有關每個許可證計畫和相關許可詳細資訊的具體資訊，請參閱[我需要哪些許可證？](https://azure.microsoft.com/pricing/details/active-directory/)

某些位置無法使用部分 Microsoft 服務。 必須先為所有成員指定 [使用位置]，才可以將授權指派給群組。**** 您可以在 Azure AD 中的 [Azure Active Directory]&gt;[使用者]&gt;[設定檔]&gt;[設定] 區域中設定此值。**** 未指定使用位置的任何使用者都將繼承 Azure AD 組織的位置。

## <a name="view-license-plans-and-plan-details"></a>查看許可證計畫和計畫詳細資訊

您可以查看可用的服務方案，包括各個許可證、檢查掛起的到期日期以及查看可用分配的數量。

### <a name="to-find-your-service-plan-and-plan-details"></a>查找您的服務方案和計畫詳細資訊

1. 使用 Azure AD 組織中的許可管理員帳戶登錄到[Azure 門戶](https://portal.azure.com/)。

1. 選取 [Azure Active Directory]，然後選取 [授權]。********

    ![許可證頁面，包含已購買服務和分配的許可證數](media/license-users-groups/license-details-blade.png)

1. 選擇 **"已購買**"連結以查看 **"產品"** 頁面，並查看許可證計畫的 **"已分配**、**可用**"和 **"即將到期**"號碼。

    ![服務頁面，包含服務許可證計畫和相關許可證資訊](media/license-users-groups/license-products-blade-with-products.png)

1. 選擇計畫名稱以查看其許可使用者和組。

## <a name="assign-licenses-to-users-or-groups"></a>指派授權給使用者或群組

請確定需要授權 Azure AD 服務的所有人都擁有適當的授權。 您可以將許可許可權添加到使用者或整個組。

### <a name="to-assign-a-license-to-a-user"></a>向使用者分配許可證

1. 在"**產品"** 頁上，選擇要分配給使用者的許可證計畫的名稱。

    ![服務頁面，具有突出顯示的服務許可證計畫](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. 在許可證計畫概述頁上，選擇 **"分配**"。

    ![服務頁面，具有突出顯示的"分配"選項](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. 在 [指派] 頁面上，選取 [使用者和群組]，然後搜尋並選取您想指派授權的使用者。********

    ![指派授權頁面顯示反白的搜尋和 [選取] 選項](media/license-users-groups/assign-license-blade-with-highlight.png)

1. 選取 [指派] 選項，確認已開啟適當的授權選項，然後選取 [確定]。********

    ![許可證選項頁，許可證計畫中提供所有選項](media/license-users-groups/license-option-blade-assignments.png)

    [指派授權] 頁面隨即更新，顯示已選取一名使用者且已設定指派。****

    > [!NOTE]
    > 某些位置無法使用部分 Microsoft 服務。 必須先指定 [使用位置]，才可以將授權指派給使用者。**** 您可以在 Azure AD 中的 [Azure Active Directory]&gt;[使用者]&gt;[設定檔]&gt;[設定] 區域中設定此值。**** 未指定使用位置的任何使用者都將繼承 Azure AD 組織的位置。

1. 選擇 **"分配**"。

    系統會將使用者新增至授權使用者清單中，且該使用者擁有隨附 Azure AD 服務的存取權。
    > [!NOTE]
    > 也可以直接從使用者的 **"許可證"** 頁向使用者分配許可證。 如果使用者通過組成員身份分配了許可證，並且希望直接向使用者分配同一許可證，則只能從步驟 1 中提到的 **"產品**"頁完成。

### <a name="to-assign-a-license-to-a-group"></a>將許可證分配給組

1. 在"**產品"** 頁上，選擇要分配給使用者的許可證計畫的名稱。

    ![產品邊欄選項卡，具有突出顯示的產品許可證計畫](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. 在 [Azure Active Directory Premium Plan 2] 頁面上，選取 [指派]。********

    ![[產品] 頁面顯示反白的 [指派] 選項](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. 在 [指派] 頁面上，選取 [使用者和群組]，然後搜尋並選取您想指派授權的群組。********

    ![指派授權頁面顯示反白的搜尋和 [選取] 選項](media/license-users-groups/assign-group-license-blade-with-highlight.png)

1. 選取 [指派] 選項，確認已開啟適當的授權選項，然後選取 [確定]。********

    ![許可證選項頁，許可證計畫中提供所有選項](media/license-users-groups/license-option-blade-group-assignments.png)

    [指派授權] 頁面隨即更新，顯示已選取一名使用者且已設定指派。****

1. 選擇 **"分配**"。

    系統會將群組新增至授權群組清單中，且所有成員均擁有隨附 Azure AD 服務的存取權。

## <a name="remove-a-license"></a>移除授權

可以從使用者的 Azure AD 使用者頁面、組分配的組概述頁中刪除許可證，或者從 Azure AD**許可證**頁開始查看許可證的使用者和組。

### <a name="to-remove-a-license-from-a-user"></a>從使用者中刪除許可證

1. 在服務方案的 **"許可使用者**"頁上，選擇不應再擁有許可證的使用者。 例如 _Alain Charon_。

1. 選取 [移除授權]****。

    ![[授權使用者] 頁面顯示反白的 [移除] 授權選項](media/license-users-groups/license-products-user-blade-with-remove-option-highlight.png)

> [!IMPORTANT]
> 無法直接刪除使用者從組繼承的許可證。 而是需將使用者從繼承授權的群組中移除。

### <a name="to-remove-a-license-from-a-group"></a>若要移除群組的授權

1. 在許可證計畫的 **"許可組"** 頁上，選擇不應再擁有許可證的組。

1. 選取 [移除授權]****。

    ![[授權群組] 頁面顯示反白的 [移除] 授權選項](media/license-users-groups/license-products-group-blade-with-remove-option-highlight.png)
    
    > [!NOTE]
    > 當同步到 Azure AD 的本地使用者帳戶退出同步範圍或刪除同步時，在 Azure AD 中虛刪除使用者。 發生這種情況時，直接或通過基於組的許可分配給使用者的許可證將被標記為**掛起**，而不是**刪除**。

## <a name="next-steps"></a>後續步驟

指派授權後，您可以執行下列程序：

- [識別並解決授權指派問題](../users-groups-roles/licensing-groups-resolve-problems.md)

- [將經授權的使用者新增至群組以進行授權](../users-groups-roles/licensing-groups-migrate-users.md)

- [使用群組來管理 Azure Active Directory 授權的案例、限制及已知問題](../users-groups-roles/licensing-group-advanced.md)

- [新增或變更設定檔資訊](active-directory-users-profile-azure-portal.md)
