---
title: 使用 REST API 列出 Azure 拒絕指派-Azure RBAC
description: 瞭解如何使用 REST API 和 Azure 角色型存取控制，為使用者、群組和應用程式列出 Azure 拒絕指派 (Azure RBAC) 。
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
ms.topic: how-to
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 2f835c270930734bf9963a7c7c3168b873eddaf6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84791906"
---
# <a name="list-azure-deny-assignments-using-the-rest-api"></a>使用 REST API 列出 Azure 拒絕指派

[Azure 拒絕指派](deny-assignments.md) 會封鎖使用者執行特定的 Azure 資源動作，即使角色指派授與他們存取權也一樣。 本文說明如何使用 REST API 列出拒絕指派。

> [!NOTE]
> 您無法直接建立自己的拒絕指派。 如需如何建立拒絕指派的相關資訊，請參閱 [Azure 拒絕指派](deny-assignments.md)。

## <a name="prerequisites"></a>Prerequisites

若要取得拒絕指派的相關資訊，您必須具備：

- `Microsoft.Authorization/denyAssignments/read` 許可權，包含在大部分的 [Azure 內建角色](built-in-roles.md)中。

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
    > | Filter | 說明 |
    > | --- | --- |
    > | (無篩選條件) | 列出指定範圍的所有拒絕指派。 |
    > | `$filter=atScope()` | 列出僅限指定範圍和更新版本的拒絕指派。 不包含子範圍內的拒絕指派。 |
    > | `$filter=assignedTo('{objectId}')` | 列出指定之使用者或服務主體的拒絕指派。<br/>如果使用者是具有拒絕指派之群組的成員，也會列出該拒絕指派。 此篩選準則可轉移給群組，這表示如果使用者是群組的成員，而且該群組是具有拒絕指派之另一個群組的成員，則也會列出該拒絕指派。<br/>此篩選器只接受使用者或服務主體的物件識別碼。 您無法傳遞群組的物件識別碼。 |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | 列出指定的使用者或服務主體以及指定範圍的拒絕指派。 |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | 列出具有指定名稱的拒絕指派。 |
    > | `$filter=principalId+eq+'{objectId}'` | 列出指定之使用者、群組或服務主體的拒絕指派。 |

## <a name="list-deny-assignments-at-the-root-scope-"></a>列出根範圍 (/) 內的拒絕指派

1. 提高存取權，如提高 [存取權以管理所有 Azure 訂用帳戶和管理群組](elevate-access-global-admin.md)中所述。

1. 使用下列要求：

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. 將 *{filter}* 取代為您想要套用來篩選拒絕指派清單的條件。 需要篩選條件。

    > [!div class="mx-tableFixed"]
    > | Filter | 說明 |
    > | --- | --- |
    > | `$filter=atScope()` | 僅列出根範圍的拒絕指派。 不包含子範圍內的拒絕指派。 |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | 列出具有指定名稱的拒絕指派。 |

1. 移除已提高的存取權。

## <a name="next-steps"></a>接下來的步驟

- [瞭解 Azure 拒絕指派](deny-assignments.md)
- [提升存取權以管理所有 Azure 訂用帳戶和管理群組](elevate-access-global-admin.md)
- [Azure REST API 參考](/rest/api/azure/)
