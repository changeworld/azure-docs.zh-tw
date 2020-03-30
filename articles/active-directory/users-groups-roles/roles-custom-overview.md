---
title: Azure 活動目錄中的自訂管理員角色 |微軟文檔
description: 預覽自訂 Azure AD 角色以委派標識管理。 在 Azure 門戶、PowerShell 或圖形 API 中管理 Azure 角色。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5c7919dcc89e34831cb4cae7921b60b35eb4c69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74024971"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Azure 活動目錄中的自訂管理員角色（預覽）

本文介紹如何使用基於角色的存取控制和資源作用域瞭解 Azure 活動目錄 （Azure AD） 中的 Azure AD 自訂角色。 自訂 Azure AD 角色將呈現[內置角色](directory-assign-admin-roles.md)的基礎許可權，以便您可以創建和組織自己的自訂角色。 此方法允許您在需要時以比內置角色更精細的方式授予存取權限。 Azure AD 自訂角色的第一個版本包括創建角色以分配管理應用註冊的許可權的功能。 隨著時間的推移，將添加組織資源（如企業應用程式、使用者和設備）的其他許可權。  

此外，Azure AD 自訂角色支援基於每個資源的分配，以及更傳統的組織範圍分配。 此方法使您能夠授予管理某些資源（例如，一個應用註冊）的許可權，而無需授予對所有資源（所有應用註冊）的存取權限。

基於 Azure AD 角色的存取控制是 Azure AD 的公共預覽功能，可用於任何付費 Azure AD 許可證計畫。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="understand-azure-ad-role-based-access-control"></a>瞭解基於 Azure AD 角色的存取控制

使用自訂 Azure AD 角色授予許可權是一個兩步過程，涉及創建自訂角色定義，然後使用角色指派分配。 自訂角色定義是從預設清單中添加的許可權的集合。 這些許可權與內置角色中使用的許可權相同。  

創建角色定義後，可以通過創建角色指派將其分配給使用者。 角色指派授予使用者在指定作用域中的角色定義中的許可權。 此兩步過程允許您創建單個角色定義，並在不同的作用域中多次分配它。 作用域定義角色成員有權訪問的 Azure AD 資源集。 最常見的範圍是組織範圍（組織範圍）。 可以在組織範圍分配自訂角色，這意味著角色成員對組織中的所有資源具有角色許可權。 也可以在物件作用域中分配自訂角色。 物件作用域的示例是單個應用程式。 相同的角色可以分配給組織中的所有應用程式的一個使用者，然後分配給只有 Contoso 支出報表應用範圍的另一個使用者。  

Azure AD 內置和自訂角色基於類似于 Azure[角色的存取控制的概](../../role-based-access-control/overview.md)念。 [這兩個基於角色的存取控制系統之間的區別](../../role-based-access-control/rbac-and-directory-admin-roles.md)是，Azure RBAC 使用 Azure 資源管理控制對 Azure 資源（如虛擬機器或存儲）的訪問，Azure AD 自訂角色使用圖形 API 控制對 Azure AD 資源的訪問。 這兩個系統都利用了角色定義和角色指派的概念。

### <a name="role-assignments"></a>角色指派

角色指派是在特定作用域將角色定義附加到使用者以授予 Azure AD 資源存取權限的物件。 建立角色指派可授與存取權，而移除角色指派則可撤銷存取權。 角色指派的核心包括三個要素：

- 使用者（在 Azure 活動目錄中具有使用者設定檔的個人）
- 角色定義
- 資源範圍

可以使用 Azure 門戶、Azure AD PowerShell 或圖形 API[創建角色指派](roles-create-custom.md)。 您還可以[查看自訂角色的分配](roles-view-assignments.md#view-the-assignments-of-a-role)。

下圖顯示角色指派的範例。 在此示例中，Chris Green 已分配了 Contoso 小工具產生器應用註冊範圍內的應用註冊管理員自訂角色。 該分配授予 Chris 僅此特定應用註冊的應用註冊應用註冊應用註冊應用註冊的應用程式註冊管理員角色的許可權。

![角色指派是許可權的強制方式，分為三個部分](./media/roles-custom-overview/rbac-overview.png)

### <a name="security-principal"></a>安全性主體

安全主體表示要分配對 Azure AD 資源存取權限的使用者。 *使用者*是在 Azure 活動目錄中具有使用者設定檔的個人。

### <a name="role"></a>角色

角色定義或角色是許可權的集合。 角色定義列出了可在 Azure AD 資源上執行的操作，例如創建、讀取、更新和刪除。 Azure AD 中有兩種類型的角色：

- 由 Microsoft 創建的無法更改的內置角色。
- 由您的組織創建和管理的自訂角色。

### <a name="scope"></a>影響範圍

範圍是允許的操作限制到特定的 Azure AD 資源，作為角色指派的一部分。 分配角色時，可以指定限制管理員對特定資源的訪問的範圍。 例如，如果要向開發人員授予自訂角色，但只是為了管理特定的應用程式註冊，則可以將特定的應用程式註冊作為作用域包含在角色指派中。

  > [!Note]
  > 可以在目錄範圍和資源範圍內分配自訂角色。 它們還不能在行政單位範圍內分配。
  > 內置角色可以在目錄範圍內分配，在某些情況下也可以分配管理單位作用域。 它們還不能在 Azure AD 資源作用域中分配。

## <a name="required-license-plan"></a>所需的許可證計畫

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>後續步驟

- 使用[Azure 門戶、Azure AD PowerShell 和圖形 API](roles-create-custom.md)創建自訂角色指派
- [查看自訂角色的分配](roles-view-assignments.md#view-assignments-of-single-application-scope)
