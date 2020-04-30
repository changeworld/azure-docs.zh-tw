---
title: 使用 Azure 原則的相容性
description: 指派 Azure 原則中的內建原則，以審查 Azure container registry 的相容性
ms.topic: article
ms.date: 02/26/2020
ms.openlocfilehash: a2bfdc18f4bbf16fe8fa6bcbcba7bab18aedabf1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82145012"
---
# <a name="audit-compliance-of-azure-container-registries-using-azure-policy"></a>使用 Azure 原則審查 Azure container registry 的相容性

[Azure 原則](../governance/policy/overview.md)是 Azure 中的一項服務，可讓您用來建立、指派和管理原則。 這些原則會對您的資源強制執行不同的規則和效果，讓這些資源能符合公司標準和服務等級協定的規範。

本文介紹適用于 Azure Container Registry 的內建原則（預覽）。 使用這些原則來審核新的和現有的登錄以符合規範。

使用 Azure 原則不會產生任何費用。

> [!IMPORTANT]
> 此功能目前為預覽狀態。 若您同意[補充的使用規定][terms-of-use]即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

## <a name="built-in-policy-definitions"></a>內建原則定義

下列內建原則定義是 Azure Container Registry 特有的：

[!INCLUDE [azure-policy-samples-policies-container-registry](../../includes/policy/samples/bycat/policies-container-registry.md)]

另請參閱內建的網路原則定義： [[預覽] Container Registry 應使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78)。

## <a name="assign-policies"></a>指派原則

* 使用[Azure 入口網站](../governance/policy/assign-policy-portal.md)、 [Azure CLI](../governance/policy/assign-policy-azurecli.md)、 [Resource Manager 範本](../governance/policy/assign-policy-template.md)或 Azure 原則 sdk 來指派原則。
* 將原則指派的範圍限定于資源群組、訂用帳戶或[Azure 管理群組](../governance/management-groups/overview.md)。 容器登錄原則指派會套用至範圍內的現有和新容器登錄。
* 隨時啟用或停用[原則強制執行](../governance/policy/concepts/assignment-structure.md#enforcement-mode)。

> [!NOTE]
> 指派或更新原則之後，需要一些時間，指派才會套用到已定義範圍中的資源。 請參閱[原則評估觸發](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)程式的相關資訊。

## <a name="review-policy-compliance"></a>審查原則合規性

使用 Azure 入口網站、Azure 命令列工具或 Azure 原則 Sdk，存取您的原則指派所產生的合規性資訊。 如需詳細資訊，請參閱[取得 Azure 資源的相容性資料](../governance/policy/how-to/get-compliance-data.md)。

當資源不符合規範時，有許多可能的原因。 若要判斷原因或尋找所需的變更，請參閱[判斷不符合規範](../governance/policy/how-to/determine-non-compliance.md)。

### <a name="policy-compliance-in-the-portal"></a>入口網站中的原則合規性：

1. 選取 [**所有服務**]，然後搜尋 [**原則**]。
1. 選取 [**相容性**]。
1. 使用篩選準則來限制合規性狀態，或在入口![網站](./media/container-registry-azure-policy/azure-policy-compliance.png)中搜尋原則原則合規性。
1. 選取原則來審查匯總合規性詳細資料和事件。 如有需要，請選取特定的登錄以符合資源合規性。

### <a name="policy-compliance-in-the-azure-cli"></a>Azure CLI 中的原則合規性

您也可以使用 Azure CLI 來取得合規性資料。 例如，使用 CLI 中的[az policy 指派 list](/cli/azure/policy/assignment#az-policy-assignment-list)命令，取得所套用 Azure Container Registry 原則的原則識別碼：

```azurecli
az policy assignment list --query "[?contains(displayName,'Container Registries')].{name:displayName, ID:id}" --output table
```

範例輸出：

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
[Preview]: Container Registries should not allow unrestricted network access           /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/b4faf132dc344b84ba68a441
[Preview]: Container Registries should be encrypted with a Customer-Managed Key (CMK)  /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/cce1ed4f38a147ad994ab60a
```

然後執行[az policy state list](/cli/azure/policy/state#az-policy-state-list) ，針對特定原則識別碼下的所有資源傳回 JSON 格式的合規性狀態：

```azurecli
az policy state list \
  --resource <policyID>
```

或執行[az policy state list](/cli/azure/policy/state#az-policy-state-list) ，以傳回特定登錄資源的 JSON 格式合規性狀態，例如*myregistry*：

```azurecli
az policy state list \
 --resource myregistry \
 --namespace Microsoft.ContainerRegistry \
 --resource-type registries \
 --resource-group myresourcegroup
```

## <a name="next-steps"></a>後續步驟

* 深入瞭解 Azure 原則[定義](../governance/policy/concepts/definition-structure.md)和[效果](../governance/policy/concepts/effects.md)

* 建立[自訂原則定義](../governance/policy/tutorials/create-custom-policy-definition.md)

* 深入瞭解 Azure 中的[治理功能](../governance/index.yml)


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/