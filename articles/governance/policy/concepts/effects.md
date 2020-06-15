---
title: 了解效果的運作方式
description: 「Azure 原則」定義有各種效果，可決定合規性的管理和回報方式。
ms.date: 05/20/2020
ms.topic: conceptual
ms.openlocfilehash: 6c2dc8303b630eb01de5c3ad9e3504dfec5256bc
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83746907"
---
# <a name="understand-azure-policy-effects"></a>了解 Azure 原則效果

「Azure 原則」中的每個原則定義都有一個效果。 該效果決定了當原則規則評估為相符時會發生的情況。 這些效果在針對新資源、已更新的資源或現有的資源時，各有不同的行為表現。

原則定義中目前支援下列效果：

- [Append](#append)
- [稽核](#audit)
- [AuditIfNotExists](#auditifnotexists)
- [拒絕](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [Disabled](#disabled)
- [EnforceOPAConstraint](#enforceopaconstraint) (預覽)
- [EnforceRegoPolicy](#enforceregopolicy) (預覽)
- [修改](#modify)

## <a name="order-of-evaluation"></a>評估順序

「Azure 原則」會先評估透過 Azure Resource Manager 進行的資源建立或更新要求。 「Azure 原則」會建立適用於資源的所有指派清單，然後對照每個定義來評估資源。 「Azure 原則」會先處理數個效果，然後才處理對適當「資源提供者」的要求。 此做法可避免「資源提供者」在資源不符合所設計的「Azure 原則」治理控制措施時，進行不必要的處理。

- 首先會檢查 **Disabled**，以決定是否應評估原則規則。
- 接著會評估 **Append** 和 **Modify**。 由於兩者均可改變要求，因此所進行的變更可能會導致無法觸發 Audit 或 Deny 效果。
- 接著評估的是 **Deny**。 在 Audit 前先評估 Deny 可防止重複記錄不想要的資源。
- 接著會先評估 **Audit**，然後才將要求傳遞給「資源提供者」。

在「資源提供者」傳回成功碼之後，便會評估 **AuditIfNotExists** 和 **DeployIfNotExists**，以判斷是否需要進行後額外的合規性記錄或動作。

**EnforceOPAConstraint** 或 **EnforceRegoPolicy** 效果目前沒有任何評估順序。

## <a name="append"></a>附加

Append 可用來在建立或更新所要求的資源時，為資源新增額外的欄位。 常見的範例是為儲存體資源指定允許的 IP。

> [!IMPORTANT]
> Append 適用於非標記屬性。 雖然 Append 可以在建立或更新要求期間，將標記新增至資源，但建議您對標記改用[修改](#modify)效果。

### <a name="append-evaluation"></a>Append 評估

在建立或更新資源期間，會先由 Append 進行評估，然後才由「資源提供者」處理要求。 Append 會在原則規則的 **if** 條件相符時，為資源新增欄位。 如果 Append 效果會以不同的值覆寫原始要求中的值，則它會充當 Deny 效果而拒絕該要求。 若要將新值附加至現有陣列中，請使用 **\[\*\]** 版本的別名。

當使用 Append 效果的原則定義在評估週期中執行時，並不會對已經存在的資源進行變更。 取而代之的是，會將符合 **if** 條件的所有資源標示為不符合規範。

### <a name="append-properties"></a>Append 屬性

Append 效果只有一個 **details** 陣列且為必要。 由於 **details** 是陣列，因此它可以接受單一的 **field/value** 配對，也可以接受多個配對。 如需了解可接受的欄位清單，請參考[定義結構](definition-structure.md#fields)。

### <a name="append-examples"></a>Append 範例

範例 1：此單一 **field/value** 配對使用非 **\[\*\]** [別名](definition-structure.md#aliases)搭配陣列 **value**，以設定儲存體帳戶相關 IP 規則。 當非 **\[\*\]** 別名是陣列時，效果會將 **value** 當做整個陣列來附加。 如果陣列已經存在，就會因為衝突而發生拒絕事件。

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
        "value": [{
            "action": "Allow",
            "value": "134.5.0.0/21"
        }]
    }]
}
```

範例 2：此單一 **field/value** 配對使用 **\[\*\]** [別名](definition-structure.md#aliases)搭配陣列 **value**，以設定儲存體帳戶相關 IP 規則。 藉由使用 **\[\*\]** 別名，效果會將 **value** 附加至可能預先存在的陣列。 如果陣列不存在，便會建立陣列。

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]",
        "value": {
            "value": "40.40.40.40",
            "action": "Allow"
        }
    }]
}
```




## <a name="audit"></a>稽核

Audit 效果可用來在評估到不符合規範的資源時，在活動記錄中建立警告事件，但並不會停止該項要求。

### <a name="audit-evaluation"></a>Audit 評估

Audit 是建立或更新資源期間，「Azure 原則」所檢查的最後一個效果。 接著，「Azure 原則」就會將該資源傳送給「資源提供者」。 Audit 對資源要求和評估週期的運作方式相同。 「Azure 原則」會將 `Microsoft.Authorization/policies/audit/action` 作業新增至活動記錄，然後將資源標示為不符合規範。

### <a name="audit-properties"></a>Audit 屬性

Audit 效果沒有任何額外的屬性可供在原則定義的 **then** 條件中使用。

### <a name="audit-example"></a>Audit 範例

範例：使用 Audit 效果。

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists 可讓您稽核符合下列條件的資源：符合 **if** 條件，但沒有 **then** 條件中 **details** 所指定的元件。

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists 評估

AuditIfNotExists 的執行順序是在「資源提供者」已處理建立或更新資源要求，並且已傳回成功狀態碼之後。 如果沒有任何相關資源，或 **ExistenceCondition** 所定義的資源未評估為 true，就會進行稽核。 「Azure 原則」會以和 Audit 效果相同的方式，將 `Microsoft.Authorization/policies/audit/action` 作業新增至活動記錄。 當觸發時，滿足 **if** 條件的資源會是標示為不符合規範的資源。

### <a name="auditifnotexists-properties"></a>AuditIfNotExists 屬性

AuditIfNotExists 效果的 **details** 屬性含有定義所要比對相關資源的所有子屬性。

- **Type** [必要]
  - 指定要比對之相關資源的類型。
  - 如果 **details.type** 是在 **if** 條件資源之下的資源類型，則此原則會在受評估資源範圍內查詢此 **type** 的資源。 否則，原則會在與受評估資源相同的資源群組內進行查詢。
- **Name** (選擇性)
  - 指定要比對的資源確切名稱，然後使原則擷取一個特定資源，而不是所指定類型的所有資源。
  - 當 **if.field.type** 和 **then.details.type** 的條件值符合時，**Name** 會變成「必要」，則必須為 `[field('name')]`。 然而，應改為考慮 [audit](#audit) 效果。
- **ResourceGroupName** (選擇性)
  - 允許比對來自不同資源群組的相關資源。
  - 如果 **type** 是一個會在 **if** 條件資源下的資源，則不適用。
  - 預設值為 **if** 條件資源的資源群組。
- **ExistenceScope** (選擇性)
  - 允許的值為 _Subscription_ 和 _ResourceGroup_。
  - 設定要從中擷取所要比對之相關資源的位置範圍。
  - 如果 **type** 是一個會在 **if** 條件資源下的資源，則不適用。
  - 針對 _ResourceGroup_，將會限制為 **if** 條件資源的資源群組，或 **ResourceGroupName** 中所指定的資源群組。
  - 針對 _Subscription_，則會查詢相關資源的整個訂用帳戶。
  - 預設值為 _ResourceGroup_。
- **ExistenceCondition** (選擇性)
  - 如果未指定，則 **type** 的任何相關資源都可滿足此效果，而不會觸發稽核。
  - 使用與 **if** 條件的原則規則相同的語言，但會個別針對每個相關資源進行評估。
  - 如果有任何相符的相關資源評估為 true，便可滿足此效果，而不會觸發稽核。
  - 可以使用 [field()] 來檢查是否與 **if** 條件中的值相等。
  - 例如，可用來驗證父資源 (在 **if** 條件中) 是否與相符的相關資源位於相同的資源位置。

### <a name="auditifnotexists-example"></a>AuditIfNotExists 範例

範例：評估「虛擬機器」以判斷「反惡意程式碼軟體」延伸模組是否存在，然後在遺漏該軟體時進行稽核。

```json
{
    "if": {
        "field": "type",
        "equals": "Microsoft.Compute/virtualMachines"
    },
    "then": {
        "effect": "auditIfNotExists",
        "details": {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "existenceCondition": {
                "allOf": [{
                        "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                        "equals": "Microsoft.Azure.Security"
                    },
                    {
                        "field": "Microsoft.Compute/virtualMachines/extensions/type",
                        "equals": "IaaSAntimalware"
                    }
                ]
            }
        }
    }
}
```

## <a name="deny"></a>拒絕

Deny 可用來透過原則定義防止不符合所定義標準的資源要求，並讓該要求失敗。

### <a name="deny-evaluation"></a>Deny 評估

建立或更新相符的資源時，Deny 可在要求被傳送給「資源提供者」之前先行阻止。 要求會以 `403 (Forbidden)` 的形式傳回。 在入口網站中，可將「禁止」視為原則指派所阻止的部署狀態。

評估現有資源時，符合 Deny 原則定義的資源會標示為不符合規範。

### <a name="deny-properties"></a>Deny 屬性

Deny 效果沒有任何額外的屬性可供在原則定義的 **then** 條件中使用。

### <a name="deny-example"></a>Deny 範例

範例：使用 Deny 效果。

```json
"then": {
    "effect": "deny"
}
```


## <a name="deployifnotexists"></a>DeployIfNotExists

DeployIfNotExists 原則定義與 AuditIfNotExists 類似，也會在符合條件時執行範本部署。

> [!NOTE]
> 使用 **deployIfNotExists** 時，支援[巢狀範本](../../../azure-resource-manager/templates/linked-templates.md#nested-template)，但目前不支援[連結的範本](../../../azure-resource-manager/templates/linked-templates.md#linked-template)。

### <a name="deployifnotexists-evaluation"></a>DeployIfNotExists 評估

DeployIfNotExists 會在「資源提供者」已處理建立或更新資源要求，並且已傳回成功狀態碼之後，執行約 15 分鐘。 如果沒有任何相關資源，或 **ExistenceCondition** 所定義的資源未評估為 true，就會進行範本部署。
部署的持續時間取決於範本所含資源的複雜度。

在評估週期期間，會將含有 DeployIfNotExists 效果且與資源相符的原則定義標示為不符合規範，但不會對該資源採取任何動作。 現有的不相容資源可透過[補救工作](../how-to/remediate-resources.md)加以補救。

### <a name="deployifnotexists-properties"></a>DeployIfNotExists 屬性

DeployIfNotExists 效果的 **details** 屬性，含有用於定義所要比對的相關資源、所要執行的範本部署的所有子屬性。

- **Type** [必要]
  - 指定要比對之相關資源的類型。
  - 從嘗試在 **if** 條件資源下擷取資源開始著手，然後在與 **if** 條件資源相同的資源群組內進行查詢。
- **Name** (選擇性)
  - 指定要比對的資源確切名稱，然後使原則擷取一個特定資源，而不是所指定類型的所有資源。
  - 當 **if.field.type** 和 **then.details.type** 的條件值符合時，**Name** 會變成「必要」，則必須為 `[field('name')]`。
- **ResourceGroupName** (選擇性)
  - 允許比對來自不同資源群組的相關資源。
  - 如果 **type** 是一個會在 **if** 條件資源下的資源，則不適用。
  - 預設值為 **if** 條件資源的資源群組。
  - 如果執行範本部署，會在此值的資源群組中部署。
- **ExistenceScope** (選擇性)
  - 允許的值為 _Subscription_ 和 _ResourceGroup_。
  - 設定要從中擷取所要比對之相關資源的位置範圍。
  - 如果 **type** 是一個會在 **if** 條件資源下的資源，則不適用。
  - 針對 _ResourceGroup_，將會限制為 **if** 條件資源的資源群組，或 **ResourceGroupName** 中所指定的資源群組。
  - 針對 _Subscription_，則會查詢相關資源的整個訂用帳戶。
  - 預設值為 _ResourceGroup_。
- **ExistenceCondition** (選擇性)
  - 如果未指定，則 **type** 的任何相關資源都可滿足此效果，而不會觸發部署。
  - 使用與 **if** 條件的原則規則相同的語言，但會個別針對每個相關資源進行評估。
  - 如果有任何相符的相關資源評估為 true，便可滿足此效果，而不會觸發部署。
  - 可以使用 [field()] 來檢查是否與 **if** 條件中的值相等。
  - 例如，可用來驗證父資源 (在 **if** 條件中) 是否與相符的相關資源位於相同的資源位置。
- **roleDefinitionIds** [必要]
  - 此屬性必須包含與訂用帳戶可存取之角色型存取控制角色識別碼相符的字串陣列。 如需詳細資訊，請參閱[補救 - 設定原則定義](../how-to/remediate-resources.md#configure-policy-definition)。
- **DeploymentScope** (選擇性)
  - 允許的值為 _Subscription_ 和 _ResourceGroup_。
  - 設定要觸發的部署類型。 _Subscription_ 表示[在訂用帳戶層級進行部署](../../../azure-resource-manager/templates/deploy-to-subscription.md)、_ResourceGroup_ 表示部署至某個資源群組。
  - 使用訂用帳戶層級部署時，必須在 _Deployment_ 中指定 _location_ 屬性。
  - 預設值為 _ResourceGroup_。
- **Deployment** [必要]
  - 此屬性應該包含完整範本部署，因為它將傳遞給 `Microsoft.Resources/deployments` PUT API。 如需詳細資訊，請參閱[部署 REST API](/rest/api/resources/deployments)。

  > [!NOTE]
  > **Deployment** 屬性內的所有函式都會作為範本 (而非原則) 的元件來評估。 **parameters** 屬性例外，此屬性會將來自原則的值傳遞給範本。 此區段中 template 參數名稱底下的 **value** 會用來執行這項值傳遞 (請參閱 DeployIfNotExists 範例中的 _fullDbName_)。

### <a name="deployifnotexists-example"></a>DeployIfNotExists 範例

範例：評估 SQL Server 資料庫，以判斷是否已啟用 transparentDataEncryption。 如果未啟用，則會執行部署來進行啟用。

```json
"if": {
    "field": "type",
    "equals": "Microsoft.Sql/servers/databases"
},
"then": {
    "effect": "DeployIfNotExists",
    "details": {
        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
        "name": "current",
        "roleDefinitionIds": [
            "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
            "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
        ],
        "existenceCondition": {
            "field": "Microsoft.Sql/transparentDataEncryption.status",
            "equals": "Enabled"
        },
        "deployment": {
            "properties": {
                "mode": "incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "fullDbName": {
                            "type": "string"
                        }
                    },
                    "resources": [{
                        "name": "[concat(parameters('fullDbName'), '/current')]",
                        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
                        "apiVersion": "2014-04-01",
                        "properties": {
                            "status": "Enabled"
                        }
                    }]
                },
                "parameters": {
                    "fullDbName": {
                        "value": "[field('fullName')]"
                    }
                }
            }
        }
    }
}
```

## <a name="disabled"></a>已停用

針對測試情況，或當原則定義已將效果參數化時，此效果相當有用。 這個彈性讓您得以停用單一指派，而不是停用該原則的所有指派。

Disabled 效果的替代效果是 **enforcementMode，於指派原則時設定。
當 **enforcementMode** 為 _Disabled_ 時，仍會評估資源。 記錄 (如「活動」記錄) 和原則效果不會發生。 如需詳細資訊，請參閱[原則指派 - 強制模式](./assignment-structure.md#enforcement-mode)。


## <a name="enforceopaconstraint"></a>EnforceOPAConstraint

此效果是搭配 `Microsoft.Kubernetes.Data` 的原則定義「模式」使用， 用於將 [OPA Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint#opa-constraint-framework) 定義的 Gatekeeper v3 許可控制規則傳遞到[開放式原則代理程式](https://www.openpolicyagent.org/) (OPA)，再到 Azure 上的 Kubernetes 叢集。

> [!NOTE]
> [適用於 Kubernetes 的 Azure 原則](./policy-for-kubernetes.md)處理 [預覽] 狀態，且僅支援 Linux 節點集區與內建的原則定義。

### <a name="enforceopaconstraint-evaluation"></a>EnforceOPAConstraint 評估

開放式原則代理程式許可控制器會即時評估叢集上的任何新要求。
每 15 分鐘便會完成一次叢集完整掃描，並將結果回報給「Azure 原則」。

### <a name="enforceopaconstraint-properties"></a>EnforceOPAConstraint 屬性

EnforceOPAConstraint 效果的 **details** 屬性有子屬性可描述 Gatekeeper v3 許可控制規則。

- **constraintTemplate** [必要]
  - 可定義新限制式的限制式範本 CustomResourceDefinition (CRD)。 此範本會定義從「Azure 原則」透過 **values** 傳遞的 Rego 邏輯、限制式結構描述及限制式參數。
- **constraint** [必要]
  - 限制式範本的 CRD 實作， 會使用透過 **values** 作為 `{{ .Values.<valuename> }}` 傳遞的參數。 在下列範例中，這些值是 `{{ .Values.cpuLimit }}` 和 `{{ .Values.memoryLimit }}`。
- **values** [選擇性]
  - 定義任何傳遞給限制式的參數和值。 每個值都必須存在於限制式範本 CRD 中。

### <a name="enforceopaconstraint-example"></a>EnforceOPAConstraint 範例

範例：可在 Kubernetes 中設定容器 CPU 和記憶體資源限制的 Gatekeeper v3 許可控制規則。

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "in": [
                "Microsoft.ContainerService/managedClusters",
                "AKS Engine"
            ]
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "enforceOPAConstraint",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/constraint.yaml",
        "values": {
            "cpuLimit": "[parameters('cpuLimit')]",
            "memoryLimit": "[parameters('memoryLimit')]"
        }
    }
}
```

## <a name="enforceregopolicy"></a>EnforceRegoPolicy

此效果是搭配 `Microsoft.ContainerService.Data` 的原則定義「模式」使用， 用於將 [Rego](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego) 定義的 Gatekeeper v2 許可控制規則傳遞到 [Azure Kubernetes 服務](../../../aks/intro-kubernetes.md)上的[開放式原則代理程式](https://www.openpolicyagent.org/) (OPA)。

> [!NOTE]
> [適用於 Kubernetes 的 Azure 原則](./policy-for-kubernetes.md)處理 [預覽] 狀態，且僅支援 Linux 節點集區與內建的原則定義。 內建的原則定義位在 **Kubernetes** 類別中。 具有 **EnforceRegoPolicy** 效果及相關 **Kubernetes 服務**類別的有限預覽原則定義將會遭_取代_。 請改用更新後的 [EnforceOPAConstraint](#enforceopaconstraint) 效果。

### <a name="enforceregopolicy-evaluation"></a>EnforceRegoPolicy 評估

開放式原則代理程式許可控制器會即時評估叢集上的任何新要求。
每 15 分鐘便會完成一次叢集完整掃描，並將結果回報給「Azure 原則」。

### <a name="enforceregopolicy-properties"></a>EnforceRegoPolicy 屬性

EnforceRegoPolicy 效果的 **details** 屬性有子屬性可描述 Gatekeeper v2 許可控制規則。

- **policyId** [必要]
  - 以參數形式傳遞至 Rego 許可控制規則的唯一名稱。
- **policy** [必要]
  - 指定 Rego 許可控制規則的 URI。
- **policyParameters** [選擇性]
  - 定義任何傳遞給 rego 原則的參數和值。

### <a name="enforceregopolicy-example"></a>EnforceRegoPolicy 範例

範例：僅允許 AKS 中指定容器映像的 Gatekeeper v2 許可控制規則。

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.ContainerService/managedClusters"
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "EnforceRegoPolicy",
    "details": {
        "policyId": "ContainerAllowedImages",
        "policy": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/KubernetesService/container-allowed-images/limited-preview/gatekeeperpolicy.rego",
        "policyParameters": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]"
        }
    }
}
```

## <a name="modify"></a>修改

Modify 用於在建立或更新期間在資源上新增、更新或移除標記。 常見範例如更新 costCenter 等資源上的標記。 除非目標資源是資源群組，否則 Modify 原則應一律將 `mode` 設定為 _Indexed_。 現有的不相容資源可透過[補救工作](../how-to/remediate-resources.md)加以補救。 單項 Modify 規則的作業數目不拘。

> [!IMPORTANT]
> Modify 目前僅供搭配標記使用。 如果您要管理標記，建議使用 Modify 而非 Append，因為 Modify 可提供額外的作業類型，且能補救現有的資源。 不過，如果您無法建立受控識別，則建議使用 Append。

### <a name="modify-evaluation"></a>Modify 評估

在建立或更新資源期間，會先由 Modify 進行評估，然後才由「資源提供者」處理要求。 Modify 會在原則規則的 **if** 條件相符時，在資源上新增或更新標記。

當使用 Modify 效果的原則定義在評估週期中執行時，並不會對已經存在的資源進行變更。 取而代之的是，會將符合 **if** 條件的所有資源標示為不符合規範。

### <a name="modify-properties"></a>Modify 屬性

Modify 效果的 **details** 屬性，含有用於定義修補所需權限的所有子屬性，以及可用於新增、更新或移除目標值的 **operations**。

- **roleDefinitionIds** [必要]
  - 此屬性必須包含與訂用帳戶可存取之角色型存取控制角色識別碼相符的字串陣列。 如需詳細資訊，請參閱[補救 - 設定原則定義](../how-to/remediate-resources.md#configure-policy-definition)。
  - 定義的角色必須包含授與給[參與者](../../../role-based-access-control/built-in-roles.md#contributor)角色的所有作業。
- **operations** [必要]
  - 在比對資源上所有待完成標記作業的陣列。
  - 屬性：
    - **operation** [必要]
      - 定義要對相符資源採取的動作。 選項包括：_addOrReplace_、_Add_、_Remove_。 _Add_ 的行為類似 [Append](#append) 效果。
    - **field** [必要]
      - 要新增、取代或移除的標記。 標記名稱必須遵守其他[欄位](./definition-structure.md#fields)的相同命名慣例。
    - **value** (選擇性)
      - 標記所要設定的值。
      - 如果 **operation** 為 _addOrReplace_ 或 _Add_，則此屬性為必要。

### <a name="modify-operations"></a>Modify 作業

**operations** 屬性陣列有數種不同的方法可更改單一原則定義中的數個標記。 每項作業都是由 **operation**、**field** 及 **value** 屬性組成的。 作業會決定工作要對標記執行何種補救工作，欄位決定要更改的標記，而值則會定義該標記的新設定。 以下範例會進行下列標記變更：

- 將 `environment` 標記設定為 "Test"，即使該標記已存在且具有不同的值。
- 移除標記 `TempResource`。
- 將 `Dept` 標記設定為在原則指派上設定的原則參數 _DeptName_。

```json
"details": {
    ...
    "operations": [
        {
            "operation": "addOrReplace",
            "field": "tags['environment']",
            "value": "Test"
        },
        {
            "operation": "Remove",
            "field": "tags['TempResource']",
        },
        {
            "operation": "addOrReplace",
            "field": "tags['Dept']",
            "value": "[parameters('DeptName')]"
        }
    ]
}
```

**operation** 屬性有下列選項：

|作業 |描述 |
|-|-|
|addOrReplace |將定義的標記和值新增至資源，即使標記已經存在且具有不同的值。 |
|加 |將定義的標記和值新增至資源。 |
|移除 |將定義的標記從資源中移除。 |

### <a name="modify-examples"></a>Modify 範例

範例 1：新增 `environment` 標記，並以 "Test" 取代現有的 `environment` 標記：

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "operations": [
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "Test"
            }
        ]
    }
}
```

範例 2：移除 `env` 標記，並新增 `environment` 標記，或使用參數化的值來取代現有的 `environment` 標記：

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "operations": [
            {
                "operation": "Remove",
                "field": "tags['env']"
            },
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "[parameters('tagValue')]"
            }
        ]
    }
}
```



## <a name="layering-policy-definitions"></a>分層原則定義

一個資源可能會受到數個指派影響。 這些指派可能屬於相同範圍，也可能屬於不同範圍。 這些指派中的每項指派也可能定義了不同的效果。 針對每個原則的條件和效果，都會以獨立方式進行評估。 例如：

- 原則 1
  - 將資源位置限制為 'westus'
  - 指派給訂用帳戶 A
  - Deny 效果
- 原則 2
  - 將資源位置限制為 'eastus'
  - 指派給訂用帳戶 A 中的資源群組 B
  - Audit 效果
  
此設定會產生下列結果：

- 任何已經在資源群組 B 且在 'eastus' 中的資源都符合原則 2 的規範，但不符合原則 1 的規範
- 任何已經在資源群組 B 但不在 'eastus' 中的資源都不符合原則 2 的規範，且如果不在 'westus' 中，則也不符合原則 1 的規範
- 任何在訂用帳戶 A 但不在 'westus' 中的新資源都會被原則 1 拒絕
- 任何在訂用帳戶 A 和資源群組 B 且在 'westus' 中的新資源都會建立，但不符合原則 2 的規範

如果原則 1 和原則 2 都採用 Deny 效果，則情況會變成：

- 任何已經在資源群組 B 但不在 'eastus' 中的資源都不符合原則 2 的規範
- 任何已經在資源群組 B 但不在 'westus' 中的資源都不符合原則 1 的規範
- 任何在訂用帳戶 A 但不在 'westus' 中的新資源都會被原則 1 拒絕
- 任何在訂用帳戶 A 之資源群組 B 中的新資源都會被拒絕

針對每個指派都會以獨立方式進行評估。 因此，不可能讓資源從範圍差異的間隙中逃脫。 分層原則定義的淨結果會被視為**累計限制最嚴格的**結果。 舉例來說，如果原則 1 和原則 2 都具有 Deny 效果，則資源會遭到重疊和衝突的原則定義封鎖。 如果您仍然需要在目標範圍中建立該資源，請檢閱每項指派的相關排除項目，以驗證是由正確的原則指派在影響正確的範圍。

## <a name="next-steps"></a>後續步驟

- 在 [Azure 原則範例](../samples/index.md)檢閱範例。
- 檢閱 [Azure 原則定義結構](definition-structure.md)。
- 了解如何[以程式設計方式建立原則](../how-to/programmatically-create.md)。
- 了解如何[取得合規性資料](../how-to/get-compliance-data.md)。
- 了解如何[補救不符合規範的資源](../how-to/remediate-resources.md)。
- 透過[使用 Azure 管理群組來組織資源](../../management-groups/overview.md)來檢閱何謂管理群組。
