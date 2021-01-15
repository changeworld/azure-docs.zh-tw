---
title: 如何在 Synapse Studio 中管理 Synapse RBAC 指派
description: 本文說明如何將 Synapse RBAC 角色指派和撤銷至 AAD 安全性主體
author: billgib
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/1/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: c88371eb450d5b0e2e8b821cdec0e87190e291f8
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98218190"
---
# <a name="how-to-manage-synapse-rbac-role-assignments-in-synapse-studio"></a>如何在 Synapse Studio 中管理 Synapse RBAC 角色指派

Synapse RBAC 會使用角色將許可權指派給使用者、群組和其他安全性主體，以啟用 Synapse 資源和程式碼成品的存取和使用。  [深入了解](./synapse-workspace-synapse-rbac.md)

本文說明如何新增及刪除 Synapse RBAC 角色指派。

>[!Note]
>- 若要管理 Synapse RBAC 角色指派，您必須在工作區或較低層級的範圍上擁有 Synapse 系統管理員角色，其中包括您想要管理的物件。 如果您是工作區的 Synapse 系統管理員，您可以將存取權授與工作區中的所有物件。 
>- 來自不同 AD 租 **使用者的來賓使用者**，即使已指派 Synapse 系統管理員角色，也無法查看或管理角色指派。
>- 為協助您在沒有任何 Synapse 系統管理員指派或可供使用的情況下，重新取得工作區的存取權，在工作區上具有管理 **AZURE rbac** 角色指派許可權的使用者也可以管理 **Synapse RBAC** 角色指派，以允許新增 Synapse 系統管理員或其他 Synapse 角色指派。
>- 使用 SQL 版權管理 SQL 集區的存取權。  除了 Synapse 系統管理員和 Synapse SQL 系統管理員角色之外，Synapse RBAC 角色不會授與 SQL 集區的存取權。

>[!important]
>- 對 Synapse RBAC 角色指派所做的變更可能需要2-5 分鐘的時間才會生效。 
>- 如果您要藉由修改安全性群組的成員資格來管理 Synapse RBAC 許可權，則會使用 Azure Active Directory 來管理成員資格的變更。  對群組成員資格所做的變更可能需要10-15 分鐘或更長的時間才會生效。

## <a name="open-synapse-studio"></a>開啟 Synapse Studio  

若要將角色指派給使用者、群組、服務主體或受控識別，請先 [開啟 Synapse Studio](https://web.azuresynapse.net/) ，然後選取您的工作區。 

![登入工作區](./media/common/login-workspace.png) 
 
 開啟您的工作區之後，展開左側的 [ **安全性** ] 區段，然後選取 [ **存取控制**]。 

 ![在左側的 [安全性] 區段中選取存取控制](./media/how-to-manage-synapse-rbac-role-assignments/left-nav-security-access-control.png)

存取控制畫面會列出目前的角色指派。  您可以依主體名稱或電子郵件篩選清單，並選擇性地篩選所包含的物件類型、角色或範圍。 在此畫面中，您可以新增或移除角色指派。  

## <a name="add-a-synapse-role-assignment"></a>新增 Synapse 角色指派

在 [存取控制] 畫面上，選取 [ **+ 新增** ] 以建立新的角色指派

![按一下 [+ 新增] 以建立新的角色指派](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add.png)

在 [新增角色指派] 索引標籤上，您可以在工作區範圍或工作區專案範圍建立角色指派。 

## <a name="add-workspace-scoped-role-assignment"></a>新增工作區範圍的角色指派

首先，選取 [ **工作區** ] 作為範圍，然後選取 [ **Synapse RBAC] 角色**。  選取要指派角色的 **主體 ()** ，然後 (s) 建立角色指派。 

![新增工作區角色指派-選取角色](./media/how-to-manage-synapse-rbac-role-assignments/access-control-workspace-role-assignment.png) 

指派的角色將適用于工作區中所有適用的物件。

## <a name="add-workspace-item-scoped-role-assignment"></a>新增工作區專案範圍的角色指派

若要在更細微的範圍指派角色，請選取 [ **工作區專案** ] 作為範圍，然後選取範圍 **專案類型**。       

![新增工作區專案角色指派-選取專案類型](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add-workspace-item-assignment-select-item-type.png) 

選取要當做範圍使用的特定 **專案** ，然後從下拉式清單中選取要指派的 **角色** 。  下拉式清單只會列出針對所選取專案類型有效的角色。 [深入了解](./synapse-workspace-synapse-rbac.md)。  

![新增工作區專案角色指派-選取角色](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add-workspace-item-assignment-select-role.png) 
 
然後選取要指派角色的 **主體 (s)** 。  您可以反復選取多個主體。  選取 **[** 套用]， (s) 建立角色指派。

## <a name="remove-a-synapse-rbac-role-assignment"></a>移除 Synapse RBAC 角色指派

若要撤銷 Synapse RBAC 存取權，請移除適當的角色指派。  在存取控制畫面上，使用篩選準則來找出要移除)  (的角色指派。  檢查角色指派，然後選取 [ **移除存取權**]。   

![刪除角色指派以移除存取權](./media/how-to-manage-synapse-rbac-role-assignments/access-control-remove-access.png)

請記住，角色指派的變更將需要2-5 分鐘的時間才會生效。   

## <a name="next-steps"></a>後續步驟

[瞭解執行一般工作所需的 Synapse RBAC 角色](./synapse-workspace-understand-what-role-you-need.md)