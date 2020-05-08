---
title: 使用 REST API 列出 Azure 拒絕指派-Azure RBAC
description: 瞭解如何使用 REST API 和 Azure 角色型存取控制（Azure RBAC），列出使用者、群組和應用程式的 Azure 拒絕指派。
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: dae0352566e6cb4f8ed1384ca12213e2aaa07f9d
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/03/2020
ms.locfileid: "82733865"
---
# <a name="list-azure-deny-assignments-using-the-rest-api"></a>使用 REST API 列出 Azure 拒絕指派

[Azure 拒絕指派](deny-assignments.md)會封鎖使用者執行特定的 Azure 資源動作，即使角色指派授與他們存取權也一樣。 本文說明如何使用 REST API 列出拒絕指派。

> [!NOTE]
> 您無法直接建立自己的拒絕指派。 如需如何建立拒絕指派的相關資訊，請參閱[Azure 拒絕指派](deny-assignments.md)。

## <a name="prerequisites"></a>Prerequisites

若要取得拒絕指派的相關資訊，您必須具有：

- `Microsoft.Authorization/denyAssignments/read`許可權，包含在大多數的[Azure 內建角色](built-in-roles.md)中。

## <a name="list-a-single-deny-assignment"></a>列出單一拒絕指派

1. 從下列要求著手：

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. 在 URI 中，將 *{scope}* 取代為您想要列出拒絕指派的範圍。

    > [!div class="mx-tableFixed"]
    > | 影響範圍 | 類型 |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | 訂用帳戶 |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | 資源群組 |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | 資源 |

1. 將 *{deny-assignment-id}* 取代為您想要擷取的拒絕指派識別碼。

## <a name="list-multiple-deny-assignments"></a>列出多個拒絕指派

1. 從下列其中一個要求開始：

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview
    ```

    使用選擇性參數：

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. 在 URI 中，將 *{scope}* 取代為您想要列出拒絕指派的範圍。

    > [!div class="mx-tableFixed"]
    > | 影響範圍 | 類型 |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | 訂用帳戶 |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | 資源群組 |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | 資源 |

1. 將 *{filter}* 取代為您想要套用來篩選拒絕指派清單的條件。

    > [!div class="mx-tableFixed"]
    > | Filter | 描述 |
    > | --- | --- |
    > | (無篩選條件) | 列出指定範圍的所有拒絕指派。 |
    > | `$filter=atScope()` | 僅列出指定範圍和以上的拒絕指派。 不包含子範圍內的拒絕指派。 |
    > | `$filter=assignedTo('{objectId}')` | 列出所指定使用者或服務主體的拒絕指派。<br/>如果使用者是具有拒絕指派之群組的成員，則也會列出該拒絕指派。 此篩選器可轉移群組，這表示如果使用者是群組的成員，而該群組是另一個具有拒絕指派之群組的成員，則也會列出該拒絕指派。<br/>此篩選器只接受使用者或服務主體的物件識別碼。 您無法傳遞群組的物件識別碼。 |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | 列出指定之使用者或服務主體與指定範圍的拒絕指派。 |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | 列出具有指定名稱的拒絕指派。 |
    > | `$filter=principalId+eq+'{objectId}'` | 列出指定之使用者、群組或服務主體的拒絕指派。 |

## <a name="list-deny-assignments-at-the-root-scope-"></a>列出根範圍 (/) 內的拒絕指派

1. 依照提高存取權[以管理所有 Azure 訂用帳戶和管理群組](elevate-access-global-admin.md)中所述，提升您的存取權。

1. 使用下列要求：

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. 將 *{filter}* 取代為您想要套用來篩選拒絕指派清單的條件。 需要篩選條件。

    > [!div class="mx-tableFixed"]
    > | Filter | 描述 |
    > | --- | --- |
    > | `$filter=atScope()` | 僅列出根範圍的拒絕指派。 不包含子範圍內的拒絕指派。 |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | 列出具有指定名稱的拒絕指派。 |

1. 移除已提高的存取權。

## <a name="next-steps"></a>後續步驟

- [瞭解 Azure 拒絕指派](deny-assignments.md)
- [提高存取權以管理所有 Azure 訂用帳戶和管理群組](elevate-access-global-admin.md)
- [Azure REST API 參考](/rest/api/azure/)
