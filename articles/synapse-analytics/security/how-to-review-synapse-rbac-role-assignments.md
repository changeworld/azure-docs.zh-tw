---
title: 如何在 Synapse Studio 中審核 Synapse RBAC 角色指派
description: 本文說明如何使用 Synapse Studio 審核 Synapse RBAC 角色指派
author: billgib
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/1/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: 6d6a0bdb9a6aaa2d9ca75ccd4a6d71e9046bee4a
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523454"
---
# <a name="how-to-review-synapse-rbac-role-assignments"></a>如何審核 Synapse RBAC 角色指派

Synapse RBAC 角色是用來將許可權指派給使用者、群組和其他安全性主體，以啟用 Synapse 資源的存取和使用。  [深入了解](https://go.microsoft.com/fwlink/?linkid=2148306)

本文說明如何檢查工作區目前的角色指派。

您可以使用任何 Synapse RBAC 角色來列出所有範圍的 Synapse RBAC 角色指派，包括您無法存取的物件指派。 只有 Synapse 系統管理員可以授與 Synapse RBAC 存取權。   

## <a name="open-synapse-studio"></a>開啟 Synapse Studio  

若要檢查角色指派，請先 [開啟 Synapse Studio](https://web.azuresynapse.net/) ，然後選取您的工作區。 

![登入工作區](./media/common/login-workspace.png) 
 
 開啟您的工作區之後，請選取左側的 [ **管理** 中樞]，再展開 [ **安全性** ] 區段，然後選取 [ **存取控制**]。 

 ![在左側的 [安全性] 區段中選取存取控制](./media/how-to-manage-synapse-rbac-role-assignments/left-nav-security-access-control.png)

## <a name="review-workspace-role-assignments"></a>檢查工作區角色指派

[存取控制] 畫面會列出工作區的所有目前角色指派（依角色分組）。 每個指派都包含主體名稱、主體類型、角色和其範圍。

![存取控制畫面](./media/how-to-review-synapse-rbac-role-assignments/access-control-assignments.png)

如果主體在不同範圍指派相同的角色，您將會看到該主體有多個指派，每個範圍各指派一個。  

如果將角色指派給安全性群組，您會看到明確指派給群組的角色，而不是繼承自父群組的角色。  

您可以依主體名稱或電子郵件篩選清單，並選擇性地篩選物件類型、角色和範圍。 在名稱篩選中輸入您的名稱或電子郵件別名，以查看指派給您的角色。 只有 Synapse 系統管理員可以變更您的角色。

>[!Important] 
>如果您是直接或間接指派角色的群組成員，您可能會有未顯示的許可權。

>[!tip]
>您可以使用 Azure 入口網站中的 Azure Active Directory 來尋找群組成員資格。  

如果您建立新的工作區，您和工作區 MSI 服務主體會自動給予工作區範圍的 Synapse 系統管理員角色。

## <a name="next-steps"></a>後續步驟

瞭解 [如何管理 SYNAPSE RBAC 角色指派](./how-to-manage-synapse-rbac-role-assignments.md)。

瞭解[您需要哪些角色來進行特定](./synapse-workspace-understand-what-role-you-need.md)工作