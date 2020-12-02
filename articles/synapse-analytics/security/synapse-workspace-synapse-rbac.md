---
title: Synapse 角色型存取控制
description: 本文說明 Azure Synapse Analytics 中的角色型存取控制
author: billgib
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: 7972f34bf0d2b93828899903e013c2e35bc997c0
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523409"
---
# <a name="what-is-synapse-role-based-access-control-rbac"></a>什麼是 Synapse 以角色為基礎的存取控制 (RBAC) ？

Synapse RBAC 可延伸 [AZURE rbac](https://docs.microsoft.com/azure/role-based-access-control/overview) 的功能，以用於 Synapse 工作區及其內容。 

Azure RBAC 可用來管理誰可以建立、更新或刪除 Synapse 工作區及其 SQL 集區、Apache Spark 集區，以及整合執行時間。

Synapse RBAC 可用來管理誰可以：
- 發佈程式碼成品，並列出或存取已發佈的程式碼成品 
- 在 Apaches Spark 集區和整合執行時間上執行程式碼，
- 存取受認證保護的連結 (資料) 服務 
- 監視或取消作業執行、檢查作業輸出和執行記錄。  

>[!Note]
>雖然 Synapse RBAC 是用來管理已發佈 SQL 腳本的存取權，但它僅提供無伺服器 SQL 集區的有限存取控制，且 _不_ 會用來控制對專用 SQL 集區的存取。  SQL 集區的存取權主要是使用 SQL 安全性來控制。

## <a name="what-can-i-do-with-synapse-rbac"></a>Synapse RBAC 可以做什麼？

以下是您可以使用 Synapse RBAC 進行的一些範例：
  - 允許使用者將對 Apache Spark 筆記本和作業所做的變更發佈至即時服務。
  - 允許使用者在特定的 Apache Spark 集區上執行和取消筆記本和 spark 作業。
  - 允許使用者使用特定的認證，讓他們可以執行工作區系統身分識別所保護的管線，並存取以認證保護之連結服務中的資料。 
  - 允許系統管理員管理、監視和取消特定 Spark 集區上的作業執行。    

## <a name="how-synapse-rbac-works"></a>Synapse RBAC 的運作方式
如同 Azure RBAC，Synapse RBAC 的運作方式是建立角色指派。 角色指派是由以下三個元素組成：安全性主體、角色定義和範圍。  

### <a name="security-principals"></a>安全性主體

_安全性主體_ 是使用者、群組、服務主體或受控識別。

### <a name="roles"></a>角色
 
_角色_ 是許可權或動作的集合，可以在特定資源類型或成品類型上執行。

Synapse 提供內建角色，可定義符合不同角色需求的動作集合：
- 系統管理員可以取得完整的存取權，以建立和設定工作區 
- 開發人員可以建立、更新和偵錯工具 SQL 腳本、筆記本、管線和資料流程，但無法在生產計算資源/資料上發佈或執行此程式碼
- 操作員可以監視和管理系統狀態、應用程式執行和審核記錄，而不需要存取程式碼或執行的輸出。
- 安全性員工可以管理和設定端點，而不需要存取程式碼、計算資源或資料。

[深入瞭解](./synapse-workspace-synapse-rbac-roles.md) 內建的 Synapse 角色。 

### <a name="scopes"></a>範圍

_範圍_ 會定義存取權所套用的資源或構件。  Synapse 支援階層式範圍。  在較高層級範圍授與的許可權會由較低層級的物件繼承。  在 Synapse RBAC 中，最上層範圍是工作區。  指派具有工作區範圍的角色會將許可權授與工作區中所有適用的物件。  

工作區中目前支援的範圍包括： Apache Spark 集區、整合執行時間、連結服務和認證。 

使用工作區範圍授與程式碼成品的存取權。  在稍後的版本中，將會支援授與工作區中成品集合的存取權。

## <a name="resolving-role-assignments-to-determine-permissions"></a>解析角色指派以判斷許可權

角色指派會授與主體在指定範圍內角色所定義的許可權。

Synapse RBAC 是 Azure RBAC 之類的加法模型。 可以將多個角色指派給單一主體和不同的範圍。 在計算安全性主體的許可權時，系統會考慮指派給主體的所有角色，以及直接或間接包含主體的群組。  它也會考慮每個指派的範圍，以決定適用的許可權。  

## <a name="enforcing-assigned-permissions"></a>強制執行指派的許可權

在 Synapse Studio 中，如果您沒有必要的許可權，特定按鈕或選項可能會呈現灰色，或在嘗試執行動作時傳回許可權錯誤。 

如果已停用按鈕或選項，將滑鼠停留在按鈕或選項上方會顯示具有必要許可權的工具提示。  請洽詢 Synapse 系統管理員，以指派授與所需許可權的角色。 您可以在 [這裡](./synapse-workspace-synapse-rbac-roles.md)看到提供特定動作的角色。

## <a name="who-can-assign-synapse-rbac-roles"></a>誰可以指派 Synapse RBAC 角色？

只有 Synapse 系統管理員可以指派 Synapse RBAC 角色。  工作區層級的 Synapse 系統管理員可以在任何範圍授與存取權。  較低層級範圍的 Synapse 系統管理員只能在該範圍授與存取權。 

建立新的工作區時，建立者會自動獲得工作區範圍的 Synapse 系統管理員角色。   

## <a name="where-do-i-manage-synapse-rbac"></a>我要在哪裡管理 Synapse RBAC？

您可以使用 [管理中樞] 中的存取控制工具，從 Synapse Studio 內管理 Synapse RBAC。 

## <a name="next-steps"></a>後續步驟

瞭解內建的 [SYNAPSE RBAC 角色](./synapse-workspace-synapse-rbac-roles.md)。

瞭解 [如何檢查工作區的 SYNAPSE RBAC 角色指派](./how-to-review-synapse-rbac-role-assignments.md) 。

瞭解 [如何指派 SYNAPSE RBAC 角色](./how-to-manage-synapse-rbac-role-assignments.md)