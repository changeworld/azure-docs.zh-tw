---
title: 原則豁免結構的詳細資料
description: 描述 Azure 原則用來免除評估計畫或定義之資源的原則豁免定義。
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: e6ced56c1dc65ca68998c5c58d3e985b63873e0b
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2020
ms.locfileid: "91950171"
---
# <a name="azure-policy-exemption-structure"></a>Azure 原則豁免結構

Azure 原則豁免 (preview) 功能是用來 _豁免_ 資源階層或個別資源，使其無法評估計畫或定義。 _豁免_于整體合規性但無法評估或暫時棄權的資源。 如需詳細資訊，請參閱 [瞭解 Azure 原則中的範圍](./scope.md)。 Azure 原則豁免僅適用于 [Resource Manager 模式](./definition-structure.md#resource-manager-modes) ，且不適用於 [資源提供者模式](./definition-structure.md#resource-provider-modes)。

> [!IMPORTANT]
> 這項功能在 **預覽**期間免費。 如需定價詳細資料，請參閱 [Azure 原則定價](https://azure.microsoft.com/pricing/details/azure-policy/)。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

您可以使用 JSON 來建立原則豁免。 原則豁免包含下列專案的元素：

- 顯示名稱
- description
- 中繼資料
- 原則指派
- 方案內的原則定義
- 豁免分類
- expiration

> [!NOTE]
> 原則豁免會建立為資源階層中的子物件或授與豁免的個別資源，因此不會在豁免定義中包含目標。

例如，下列 JSON 會將資源 **棄權** 類別中的原則豁免顯示為名為的方案指派 `resourceShouldBeCompliantInit` 。 資源只會 _豁免_ 方案中的兩個原則定義， `customOrgPolicy` 自訂原則定義 (參考 `requiredTags`) 和「允許的位置」內建原則定義 (識別碼： `e56962a6-4747-49cd-b67b-bf8b01975c4c` ，參考 `allowedLocations`) ：

```json
{
    "id": "/subscriptions/{subId}/resourceGroups/ExemptRG/providers/Microsoft.Authorization/policyExemptions/resourceIsNotApplicable",
    "name": "resourceIsNotApplicable",
    "type": "Microsoft.Authorization/policyExemptions",
    "properties": {
        "displayName": "This resource is scheduled for deletion",
        "description": "This resources is planned to be deleted by end of quarter and has been granted a waiver to the policy.",
        "metadata": {
            "requestedBy": "Storage team",
            "approvedBy": "IA",
            "approvedOn": "2020-07-26T08:02:32.0000000Z",
            "ticketRef": "4baf214c-8d54-4646-be3f-eb6ec7b9bc4f"
        },
        "policyAssignmentId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyAssignments/resourceShouldBeCompliantInit",
        "policyDefinitionReferenceIds": [
            "requiredTags",
            "allowedLocations"
        ],
        "exemptionCategory": "waiver",
        "expiresOn": "2020-12-31T23:59:00.0000000Z"
    }
}
```

具有原則豁免所用之相符項的相關方案程式碼片段 `policyDefinitionReferenceIds` ：

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/customOrgPolicy",
        "policyDefinitionReferenceId": "requiredTags",
        "parameters": {
            "reqTags": {
                "value": "[parameters('init_reqTags')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
        "policyDefinitionReferenceId": "allowedLocations",
        "parameters": {
            "listOfAllowedLocations": {
                "value": "[parameters('init_listOfAllowedLocations')]"
            }
        }
    }
]
```

## <a name="display-name-and-description"></a>顯示名稱和描述

您可以使用 **displayName** 和 **description** 來識別豁免原則，並提供使用特定資源的內容。 **displayName** 的長度上限為 _128_ 個字元，**description** 的長度上限則為 _512_ 個字元。

## <a name="metadata"></a>中繼資料

**中繼資料**屬性可建立儲存相關資訊所需的任何子屬性。 在上述範例中，屬性 **requestedBy**、 **approvedBy**、 **approvedOn**和 **ticketRef** 包含客戶值，以提供要求豁免的人員、核准該豁免者的時間，以及要求的內部追蹤票證的相關資訊。 這些 **中繼資料** 屬性是範例，但不是必要的，而且 **中繼資料** 不限於這些子屬性。

## <a name="policy-assignment-id"></a>原則指派識別碼

此欄位必須是原則指派或方案指派的完整路徑名稱。
`policyAssignmentId` 是字串，而不是陣列。 這個屬性會定義父資源階層或個別資源 _豁免_ 的指派。

## <a name="policy-definition-ids"></a>原則定義識別碼

如果 `policyAssignmentId` 是方案指派的， `policyDefinitionReferenceIds` 屬性可用來指定在主體資源有豁免的方案中， (s) 的原則定義。 因為可能會從一或多個包含的原則定義中豁免資源，所以這個屬性是 _陣列_。 這些值必須符合欄位中計畫定義中的值 `policyDefinitions.policyDefinitionReferenceId` 。

## <a name="exemption-category"></a>豁免分類

有兩個豁免類別存在，用來分組豁免：

- 緩和 **：已**授與豁免，因為原則意圖是透過另一個方法達成。
- **棄權**：因為暫時接受資源的非合規性狀態，所以會授與豁免。 使用此類別目錄的另一個原因，是針對應從計畫中的一或多個定義排除的資源或資源階層，但不應該從整個計畫中排除。

## <a name="expiration"></a>到期

若要在資源階層或個別資源不再 _豁免_ 指派時設定，請設定 `expiresOn` 屬性。 這個選擇性屬性必須是通用 ISO 8601 日期時間格式 `yyyy-MM-ddTHH:mm:ss.fffffffZ` 。

> [!NOTE]
> 到達日期時，不會刪除原則豁免 `expiresOn` 。 保留物件以進行記錄保留，但不再接受豁免。

## <a name="required-permissions"></a>所需的權限

管理原則豁免物件所需的 Azure RBAC 許可權位於作業 `Microsoft.Authorization/policyExemptions` 群組中。 內建角色的 [資源原則參與者](../../../role-based-access-control/built-in-roles.md#resource-policy-contributor) 和 [安全性系統管理員](../../../role-based-access-control/built-in-roles.md#security-admin) 都具有 `read` 和 `write` 許可權，而 [原則見解資料寫入器外掛程式 (預覽) ](../../../role-based-access-control/built-in-roles.md#policy-insights-data-writer-preview) 具有 `read` 許可權。

豁免有額外的安全性措施，因為授與豁免的影響。 除了要求 `Microsoft.Authorization/policyExemptions/write` 資源階層或個別資源上的作業之外，豁免的建立者必須在 `exempt/Action` 目標指派上擁有動詞。

## <a name="next-steps"></a>後續步驟

- 了解[原則定義結構](./definition-structure.md)。
- 了解如何[以程式設計方式建立原則](../how-to/programmatically-create.md)。
- 了解如何[取得合規性資料](../how-to/get-compliance-data.md)。
- 了解如何[補救不符合規範的資源](../how-to/remediate-resources.md)。
- 透過[使用 Azure 管理群組來組織資源](../../management-groups/overview.md)來檢閱何謂管理群組。