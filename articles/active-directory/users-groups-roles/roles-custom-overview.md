---
title: Azure 活動目錄中的自定義管理員角色 |微軟文件
description: 預覽自定義 Azure AD 角色以委派標識管理。 在 Azure 門戶、PowerShell 或圖形 API 中管理 Azure 角色。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/22/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae244d93d679199aaa0bd08891cd34d4ca3a2ddc
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085105"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Azure 活動目錄中的自訂管理員角色(預覽)

本文介紹如何使用基於角色的存取控制和資源作用域瞭解 Azure 活動目錄 (Azure AD) 中的 Azure AD 自定義角色。 自訂 Azure AD 角色將呈現[內建角色](directory-assign-admin-roles.md)的基礎許可權,以便您可以創建和組織自己的自定義角色。 此方法允許您在需要時以比內置角色更精細的方式授予訪問許可權。 Azure AD 自定義角色的第一個版本包括創建角色以分配管理應用註冊的許可權的功能。 隨著時間的推移,將添加組織資源(如企業應用程式、用戶和設備)的其他許可權。  

此外,Azure AD 自定義角色支援基於每個資源的分配,以及更傳統的組織範圍分配。 此方法使您能夠授予管理某些資源(例如,一個應用註冊)的許可權,而無需授予對所有資源(所有應用註冊)的訪問許可權。

基於 Azure AD 角色的存取控制是 Azure AD 的公共預覽功能,可用於任何付費 Azure AD 許可證計畫。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="understand-azure-ad-role-based-access-control"></a>瞭解基於 Azure AD 角色的存取控制

使用自定義 Azure AD 角色授予許可權是一個兩步過程,涉及創建自定義角色定義,然後使用角色分配分配。 自定義角色定義是從預設清單中添加的許可權的集合。 這些許可權與內建角色中使用的許可權相同。  

創建角色定義後,可以通過創建角色分配將其分配給使用者。 角色分配授予使用者在指定作用域中的角色定義中的許可權。 此兩步過程允許您創建單個角色定義,並在不同的作用域中多次分配它。 作用域定義角色成員有權訪問的 Azure AD 資源集。 最常見的範圍是組織範圍(組織範圍)。 可以在組織範圍分配自定義角色,這意味著角色成員對組織中的所有資源具有角色許可權。 也可以在物件作用域中分配自定義角色。 物件作用域的示例是單個應用程式。 相同的角色可以分配給組織中的所有應用程式的一個使用者,然後分配給只有 Contoso 支出報表應用範圍的另一個使用者。  

Azure AD 內建和自定義角色基於類似於 Azure[角色的訪問控制的概](../../role-based-access-control/overview.md)念。 [這兩個基於角色的存取控制系統之間的區別](../../role-based-access-control/rbac-and-directory-admin-roles.md)是,Azure RBAC 使用 Azure 資源管理控制對 Azure 資源(如虛擬機或儲存)的訪問,Azure AD 自定義角色使用圖形 API 控制對 Azure AD 資源的訪問。 這兩個系統都利用了角色定義和角色分配的概念。

### <a name="how-azure-ad-determines-if-a-user-has-access-to-a-resource"></a>Azure AD 如何確定使用者是否有權存取資源

以下是 Azure AD 用於確定您是否有權存取管理資源的進階步驟。 使用此資訊可排除訪問問題。

1. 使用者(或服務主體)獲取 Microsoft 圖形或 Azure AD 圖形終結點的權杖。

1. 使用者使用已頒發的權杖透過 Microsoft 圖形或 Azure AD 圖形對 Azure 活動目錄 (Azure AD) 進行 API 呼叫。

1. 根據具體情況,Azure AD 會執行以下操作之一:

    - 根據使用者訪問權杖中的[wids聲明](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)評估使用者的角色成員身份。
    - 檢索直接或通過組成員身份向執行操作的資源申請使用者的所有角色分配。

1. Azure AD 確定 API 呼叫中的操作是否包含在使用者對此資源的角色中。
1. 如果使用者在要求範圍內不具含有該動作的角色，則不會授與存取權。 否則會授與存取權。

### <a name="role-assignments"></a>角色指派

角色分配是在特定作用域將角色定義附加到使用者以授予 Azure AD 資源訪問許可權的物件。 建立角色指派可授與存取權，而移除角色指派則可撤銷存取權。 角色分配的核心包括三個要素:

- 使用者(在 Azure 活動目錄中具有使用者設定檔的個人)
- 角色定義
- 資源範圍

可以使用 Azure 門戶、Azure AD PowerShell 或圖形 API[建立角色分配](roles-create-custom.md)。 您還可以[檢視自訂角色的分配](roles-view-assignments.md#view-the-assignments-of-a-role)。

下圖顯示角色指派的範例。 在此示例中,Chris Green 已分配了 Contoso 小工具生成器應用註冊範圍內的應用註冊管理員自定義角色。 該分配授予 Chris 僅此特定應用註冊的應用註冊應用註冊應用註冊應用註冊的應用程式註冊管理員角色的許可權。

![角色分配是許可權的強制方式,分為三個部分](./media/roles-custom-overview/rbac-overview.png)

### <a name="security-principal"></a>安全性主體

安全主體表示要分配對 Azure AD 資源訪問許可權的使用者。 *使用者*是在 Azure 活動目錄中具有使用者配置檔的個人。

### <a name="role"></a>角色

角色定義或角色是許可權的集合。 角色定義列出了可在 Azure AD 資源上執行的操作,例如創建、讀取、更新和刪除。 Azure AD 中有兩種類型的角色:

- 由 Microsoft 創建的無法更改的內建角色。
- 由您的組織創建和管理的自定義角色。

### <a name="scope"></a>影響範圍

範圍是允許的操作限制到特定的 Azure AD 資源,作為角色分配的一部分。 分配角色時,可以指定限制管理員對特定資源的訪問的範圍。 例如,如果要向開發人員授予自定義角色,但只是為了管理特定的應用程式註冊,則可以將特定的應用程式註冊作為作用域包含在角色分配中。

  > [!Note]
  > 可以在目錄範圍和資源範圍內分配自定義角色。 它們還不能在行政單位範圍內分配。
  > 內置角色可以在目錄範圍內分配,在某些情況下也可以分配管理單位作用域。 它們還不能在 Azure AD 資源作用域中分配。

## <a name="required-license-plan"></a>所需的授權計劃

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>後續步驟

- 使用[Azure 門戶、Azure AD PowerShell 和圖像 API](roles-create-custom.md)建立自訂角色分配
- [檢視自訂角色的配置](roles-view-assignments.md#view-assignments-of-single-application-scope)
