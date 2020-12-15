---
title: 指派或移除授權 - Azure Active Directory | Microsoft Docs
description: 關於如何從您的使用者或群組指派或移除 Azure Active Directory 授權的指示。
services: active-directory
author: ajburnle
manager: daveba
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6900647acf7182529f34c8cc065dbb039de38be
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97504401"
---
# <a name="assign-or-remove-licenses-in-the-azure-active-directory-portal"></a>在 Azure Active Directory 入口網站中指派或移除授權

許多 Azure Active Directory (Azure AD) 服務都要求您為每個使用者或群組授權 (該服務的相關成員) 和相關聯的成員。 只有具備有效授權的使用者才能存取和使用已授權的 Azure AD 服務，其為 true。 授權會套用至每個租使用者，且不會轉移給其他租使用者。 

## <a name="available-license-plans"></a>可用的授權方案

Azure AD 服務有數個可用的授權方案，包括：

- Azure AD Free

- Azure AD Premium P1

- Azure AD Premium P2

如需有關每個授權方案和相關授權詳細資料的特定資訊，請參閱 [我需要什麼授權？](https://azure.microsoft.com/pricing/details/active-directory/)。 若要註冊 Azure AD premium 授權方案，請參閱 [此處](./active-directory-get-started-premium.md)。

某些位置無法使用部分 Microsoft 服務。 必須先為所有成員指定 [使用位置]，才可以將授權指派給群組。 您可以在 Azure AD 中的 [Azure Active Directory]&gt;[使用者]&gt;[設定檔]&gt;[設定] 區域中設定此值。 未指定其使用位置的任何使用者都會繼承 Azure AD 組織的位置。

## <a name="view-license-plans-and-plan-details"></a>查看授權方案和方案詳細資料

您可以查看可用的服務方案，包括個別授權、檢查暫止的到期日，以及查看可用指派的數目。

### <a name="to-find-your-service-plan-and-plan-details"></a>若要尋找您的服務方案和方案詳細資料

1. 使用 Azure AD 組織中的授權系統管理員帳戶登入 [Azure 入口網站](https://portal.azure.com/) 。

1. 選取 [Azure Active Directory]，然後選取 [授權]。

    :::image type="content" source="media/license-users-groups/license-details-blade.png" alt-text="授權頁面，包含已購買的服務數目和指派的授權":::

1. 選取 [ **所有產品** ] 以查看 [所有產品] 頁面，並查看授權方案的 **總**、 **指派**、 **可用** 和 **即將到期** 的次數。

    :::image type="content" source="media/license-users-groups/license-products-blade-with-products.png" alt-text="服務頁面-包含服務授權方案-相關聯的授權資訊":::

    > [!NOTE]
    > 這些數位的定義如下： 
    > - 總計：購買的授權總數
    > - 已指派：指派給使用者的授權數目
    > - 可用：指派的可用授權數，包括即將過期
    > - 即將到期：即將到期的授權數目

1. 選取方案名稱以查看其授權的使用者和群組。

## <a name="assign-licenses-to-users-or-groups"></a>指派授權給使用者或群組

請確定需要授權 Azure AD 服務的所有人都擁有適當的授權。 您可以將授權許可權新增至使用者或整個群組。

### <a name="to-assign-a-license-to-a-user"></a>將授權指派給使用者

1. 在 [ **產品** ] 頁面上，選取您要指派給使用者的授權方案名稱。

    ![具有反白顯示服務授權方案的 [服務] 頁面](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. 在 [授權方案] 總覽頁面上，選取 [ **指派**]。

    ![具有反白顯示指派選項的 [服務] 頁面](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. 在 [指派] 頁面上，選取 [使用者和群組]，然後搜尋並選取您想指派授權的使用者。

    ![指派授權頁面顯示反白的搜尋和 [選取] 選項](media/license-users-groups/assign-license-blade-with-highlight.png)

1. 選取 [指派] 選項，確認已開啟適當的授權選項，然後選取 [確定]。

    ![授權選項頁面，其中包含授權方案中可用的所有選項](media/license-users-groups/license-option-blade-assignments.png)

    [指派授權] 頁面隨即更新，顯示已選取一名使用者且已設定指派。

    > [!NOTE]
    > 某些位置無法使用部分 Microsoft 服務。 必須先指定 [使用位置]，才可以將授權指派給使用者。 您可以在 Azure AD 中的 [Azure Active Directory]&gt;[使用者]&gt;[設定檔]&gt;[設定] 區域中設定此值。 未指定其使用位置的任何使用者都會繼承 Azure AD 組織的位置。

1. 選取 [指派]。

    系統會將使用者新增至授權使用者清單中，且該使用者擁有隨附 Azure AD 服務的存取權。
    > [!NOTE]
    > 您也可以從使用者的 [ **授權** ] 頁面，將授權直接指派給使用者。 如果使用者具有透過群組成員資格指派的授權，而您想要直接將相同的授權指派給使用者，則只能從步驟1中所述的 [ **產品** ] 頁面完成。

### <a name="to-assign-a-license-to-a-group"></a>將授權指派給群組

1. 在 [ **產品** ] 頁面上，選取您要指派給使用者的授權方案名稱。

    ![產品分頁，具有反白顯示的產品授權方案](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. 在 [Azure Active Directory Premium Plan 2] 頁面上，選取 [指派]。

    ![[產品] 頁面顯示反白的 [指派] 選項](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. 在 [指派] 頁面上，選取 [使用者和群組]，然後搜尋並選取您想指派授權的群組。

    ![指派授權頁面，反白顯示搜尋和選取選項2](media/license-users-groups/assign-group-license-blade-with-highlight.png)

1. 選取 [指派] 選項，確認已開啟適當的授權選項，然後選取 [確定]。

    ![授權選項頁面，其中包含授權方案2中的所有可用選項](media/license-users-groups/license-option-blade-group-assignments.png)

    [指派授權] 頁面隨即更新，顯示已選取一名使用者且已設定指派。

1. 選取 [指派]。

    系統會將群組新增至授權群組清單中，且所有成員均擁有隨附 Azure AD 服務的存取權。

## <a name="remove-a-license"></a>移除授權

您可以從使用者的 [Azure AD 使用者] 頁面、群組指派的 [群組總覽] 頁面，或從 [Azure AD **授權** ] 頁面中移除授權，以查看授權的使用者和群組。

### <a name="to-remove-a-license-from-a-user"></a>若要移除使用者的授權

1. 在服務方案的 [ **授權的使用者** ] 頁面上，選取應不再具有授權的使用者。 例如 _Alain Charon_。

1. 選取 [移除授權]。

    ![[授權使用者] 頁面顯示反白的 [移除] 授權選項](media/license-users-groups/license-products-user-blade-with-remove-option-highlight.png)

> [!IMPORTANT]
> 無法直接移除使用者從群組繼承的授權。 而是需將使用者從繼承授權的群組中移除。

### <a name="to-remove-a-license-from-a-group"></a>若要移除群組的授權

1. 在授權方案的 [ **授權群組** ] 頁面上，選取應不再具有授權的群組。

1. 選取 [移除授權]。

    ![已反白顯示 [移除授權] 選項的 [授權群組] 頁面2](media/license-users-groups/license-products-group-blade-with-remove-option-highlight.png)
    
    > [!NOTE]
    > 當同步處理至 Azure AD 的內部部署使用者帳戶落在同步處理的範圍之外，或移除同步時，就會在 Azure AD 中虛刪除使用者。 發生這種情況時，直接指派給使用者或透過以群組為基礎的授權指派給使用者的授權，將會標示為已 **暫停** 而不是 **刪除**。

## <a name="next-steps"></a>後續步驟

指派授權後，您可以執行下列程序：

- [識別並解決授權指派問題](../enterprise-users/licensing-groups-resolve-problems.md)

- [將經授權的使用者新增至群組以進行授權](../enterprise-users/licensing-groups-migrate-users.md)

- [使用群組來管理 Azure Active Directory 授權的案例、限制及已知問題](../enterprise-users/licensing-group-advanced.md)

- [新增或變更設定檔資訊](active-directory-users-profile-azure-portal.md)
