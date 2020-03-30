---
title: 使用 Azure 策略的合規性
description: 在 Azure 策略中分配內置策略以審核 Azure 容器註冊表的合規性
ms.topic: article
ms.date: 02/26/2020
ms.openlocfilehash: 012cd013de1c60fddcfb28e4bca96d761ada41ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330731"
---
# <a name="audit-compliance-of-azure-container-registries-using-azure-policy"></a>使用 Azure 策略審核 Azure 容器註冊表的合規性

[Azure 策略](../governance/policy/overview.md)是 Azure 中用於創建、分配和管理原則的服務。 這些原則會對您的資源強制執行不同的規則和效果，讓這些資源能符合公司標準和服務等級協定的規範。

本文介紹了 Azure 容器註冊表的內置策略（預覽）。 使用這些策略審核新的和現有的註冊表是否符合規定。

使用 Azure 策略不收取任何費用。

> [!IMPORTANT]
> 此功能目前為預覽狀態。 若您同意[補充的使用規定][terms-of-use]，即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

## <a name="built-in-policy-definitions"></a>內置策略定義

以下內置策略定義特定于 Azure 容器註冊表：

[!INCLUDE [azure-policy-samples-policies-container-registry](../../includes/azure-policy-samples-policies-container-registry.md)]

另請參閱內置網路原則定義[：[預覽] 容器註冊表應使用虛擬網路服務終結點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78)。

## <a name="assign-policies"></a>指派原則

* 使用 Azure[門戶](../governance/policy/assign-policy-portal.md)[、Azure CLI、](../governance/policy/assign-policy-azurecli.md)[資源管理器範本](../governance/policy/assign-policy-template.md)或 Azure 策略 SDK 分配策略。
* 將策略分配範圍限定為資源組、訂閱或 Azure[管理組](../governance/management-groups/overview.md)。 容器註冊表策略分配適用于範圍內的現有和新的容器註冊表。
* 隨時啟用或禁用[策略實施](../governance/policy/concepts/assignment-structure.md#enforcement-mode)。

> [!NOTE]
> 分配或更新策略後，分配需要一些時間才能應用於定義範圍內的資源。 有關[策略評估觸發器的資訊](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)。

## <a name="review-policy-compliance"></a>查看策略合規性

使用 Azure 門戶、Azure 命令列工具或 Azure 策略 SDK 訪問策略分配生成的合規性資訊。 有關詳細資訊，請參閱[獲取 Azure 資源的合規性資料](../governance/policy/how-to/get-compliance-data.md)。

當資源不合規時，有許多可能的原因。 要確定原因或找到對更改負責，請參閱[確定不合規](../governance/policy/how-to/determine-non-compliance.md)。

### <a name="policy-compliance-in-the-portal"></a>門戶中的策略合規性：

1. 選擇**所有服務**，然後搜索**策略**。
1. 選擇**符合性**。
1. 使用篩選器限制合規性狀態或在門戶![](./media/container-registry-azure-policy/azure-policy-compliance.png)中搜索策略策略合規性。
1. 選擇策略以查看聚合合規性詳細資訊和事件。 如果需要，請選擇資源合規性的特定註冊表。

### <a name="policy-compliance-in-the-azure-cli"></a>Azure CLI 中的策略合規性

您還可以使用 Azure CLI 獲取合規性資料。 例如，使用 CLI 中的[az 策略分配清單](/cli/azure/policy/assignment#az-policy-assignment-list)命令獲取所應用的 Azure 容器註冊表策略的策略 ID：

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

然後運行[az 策略狀態清單](/cli/azure/policy/state#az-policy-state-list)，以返回特定策略 ID 下所有資源的 JSON 格式的符合狀態：

```azurecli
az policy state list \
  --resource <policyID>
```

或運行[az 策略狀態清單](/cli/azure/policy/state#az-policy-state-list)以返回特定註冊表資源的 JSON 格式的合規性狀態，例如*我的註冊表*：

```azurecli
az policy state list \
 --resource myregistry \
 --namespace Microsoft.ContainerRegistry \
 --resource-type registries \
 --resource-group myresourcegroup
```

## <a name="next-steps"></a>後續步驟

* 瞭解有關 Azure 策略[定義](../governance/policy/concepts/definition-structure.md)和[效果](../governance/policy/concepts/effects.md)的更多

* 創建[自訂策略定義](../governance/policy/tutorials/create-custom-policy-definition.md)

* 瞭解有關 Azure[中治理功能](../governance/index.yml)的資訊


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/