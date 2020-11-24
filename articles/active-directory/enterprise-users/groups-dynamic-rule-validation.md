---
title: 驗證動態群組成員資格的規則 (預覽) -Azure AD |Microsoft Docs
description: 如何針對 Azure Active Directory 中動態群組的成員資格規則測試成員。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: yukarppa
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e811cba1596d0bece4966ee5b02ecd9c2a3180cf
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2020
ms.locfileid: "95488890"
---
# <a name="validate-a-dynamic-group-membership-rule-preview-in-azure-active-directory"></a>在 Azure Active Directory 中驗證動態群組成員資格規則 (預覽) 

Azure Active Directory (Azure AD) 現在提供在公開預覽 (中驗證動態群組規則的方法。 在 [ **驗證規則** ] 索引標籤上，您可以針對範例群組成員驗證動態規則，以確認規則如預期般運作。 當建立或更新動態群組規則時，系統管理員想要知道使用者或裝置是否為群組的成員。 這有助於評估使用者或裝置是否符合規則準則，並在不需要成員資格時協助進行疑難排解。

## <a name="step-by-step-walk-through"></a>逐步解說逐步解說

若要開始使用，請移至 **Azure Active Directory**  >  **群組**。 選取現有的動態群組，或建立新的動態群組，然後按一下 [動態成員資格規則]。 然後，您可以看到 [ **驗證規則** ] 索引標籤。

![尋找 [驗證規則] 索引標籤，並從現有的規則開始](./media/groups-dynamic-rule-validation/validate-tab.png)

在 [ **驗證規則** ] 索引標籤上，您可以選取使用者來驗證其成員資格。 您可以一次選取20個使用者或裝置。

![新增使用者以驗證現有的規則](./media/groups-dynamic-rule-validation/validate-tab-add-users.png)

從選擇器選擇使用者或裝置之後， **選取**[驗證] 將會自動啟動，並且會顯示驗證結果。

![查看規則驗證的結果](./media/groups-dynamic-rule-validation/validate-tab-results.png)

結果會指出使用者是否為群組的成員。 如果規則無效或有網路問題，則結果會顯示為「 **未知**」。 如果是 **未知** 的，則詳細的錯誤訊息會描述所需的問題和動作。

![查看規則驗證結果的詳細資料](./media/groups-dynamic-rule-validation/validate-tab-view-details.png)

您可以修改規則，將會觸發成員資格的驗證。 若要查看為什麼使用者不是群組的成員，請按一下 [查看詳細資料]，[驗證詳細資料] 會顯示每個撰寫規則的運算式的結果。 按一下 **[確定]** 以結束。

## <a name="next-steps"></a>後續步驟

- [群組的動態成員資格規則](groups-dynamic-membership.md)
