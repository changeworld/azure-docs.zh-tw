---
title: 了解效果的運作方式
description: 「Azure 原則」定義有各種效果，可決定合規性的管理和回報方式。
ms.date: 10/05/2020
ms.topic: conceptual
ms.openlocfilehash: e72e94766dce2660409e729bc43eb107fb9ab39a
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97883073"
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
- [修改](#modify)

以下是已被 _取代_ 的效果：

- [EnforceOPAConstraint](#enforceopaconstraint)
- [EnforceRegoPolicy](#enforceregopolicy)

> [!IMPORTANT]
> 若要取代 **EnforceOPAConstraint** 或 **EnforceRegoPolicy** 效果，請使用 _Audit_ 和 _deny_ 搭配資源提供者模式 `Microsoft.Kubernetes.Data` 。 內建原則定義已更新。 當修改這些內建原則定義的現有原則指派時， _效果_ 參數必須變更為更新的 _allowedValues_ 清單中的值。

## <a name="order-of-evaluation"></a>評估順序

先 Azure 原則評估建立或更新資源的要求。 「Azure 原則」會建立適用於資源的所有指派清單，然後對照每個定義來評估資源。 在 [Resource Manager 模式](./definition-structure.md#resource-manager-modes)中，Azure 原則會先處理數個效果，再將要求交給適當的資源提供者。 當資源不符合 Azure 原則的設計治理控制項時，此順序會防止資源提供者進行不必要的處理。 使用 [資源提供者模式](./definition-structure.md#resource-provider-modes)時，資源提供者會管理評估和結果，並將結果回報給 Azure 原則。

- 首先會檢查 **Disabled**，以決定是否應評估原則規則。
- 接著會評估 **Append** 和 **Modify**。 由於兩者均可改變要求，因此所進行的變更可能會導致無法觸發 Audit 或 Deny 效果。 這些效果僅適用于 Resource Manager 模式。
- 接著評估的是 **Deny**。 在 Audit 前先評估 Deny 可防止重複記錄不想要的資源。
- 最後會評估 **Audit** 。

在資源提供者傳回 Resource Manager 模式要求的成功碼之後， **AuditIfNotExists** 和 **DeployIfNotExists** 會評估，以判斷是否需要額外的合規性記錄或動作。

此外， `PATCH` 只會修改相關欄位的要求會 `tags` 將原則評估限制為包含檢查相關欄位之條件的原則 `tags` 。

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

範例 2：此單一 **field/value** 配對使用 **\[\*\]** [別名](definition-structure.md#aliases)搭配陣列 **value**，以設定儲存體帳戶相關 IP 規則。 藉由使用 **\[\*\]** 別名，效果會將 **value** 附加至可能預先存在的陣列。 如果陣列還不存在，就會建立它。

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

Audit 是建立或更新資源期間，「Azure 原則」所檢查的最後一個效果。 針對 Resource Manager 模式，Azure 原則接著將資源傳送給資源提供者。 Audit 對資源要求和評估週期的運作方式相同。 針對新的和更新的資源，Azure 原則會將 `Microsoft.Authorization/policies/audit/action` 作業新增至活動記錄，並將資源標記為不符合規範。

### <a name="audit-properties"></a>Audit 屬性

若為 Resource Manager 模式，則 audit 效果沒有任何額外的屬性可供在原則定義的 **then** 條件中使用。

若為的資源提供者模式 `Microsoft.Kubernetes.Data` ，則審核效果會有下列額外的 **詳細資料** 子屬性。

- **constraintTemplate** (必要) 
  - 可定義新限制式的限制式範本 CustomResourceDefinition (CRD)。 此範本會定義從「Azure 原則」透過 **values** 傳遞的 Rego 邏輯、限制式結構描述及限制式參數。
- **條件約束** (必要) 
  - 限制式範本的 CRD 實作， 會使用透過 **values** 作為 `{{ .Values.<valuename> }}` 傳遞的參數。 在下列範例2中，這些值為 `{{ .Values.excludedNamespaces }}` 和 `{{ .Values.allowedContainerImagesRegex }}` 。
- **值** (選擇性) 
  - 定義任何傳遞給限制式的參數和值。 每個值都必須存在於限制式範本 CRD 中。

### <a name="audit-example"></a>Audit 範例

範例1：使用 Resource Manager 模式的 audit 效果。

```json
"then": {
    "effect": "audit"
}
```

範例2：使用的資源提供者模式的 audit 效果 `Microsoft.Kubernetes.Data` 。 **詳細資料** 中的其他資訊會定義要在 Kubernetes 中使用的條件約束範本和 .crd，以限制允許的容器映射。

```json
"then": {
    "effect": "audit",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/constraint.yaml",
        "values": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]",
            "excludedNamespaces": "[parameters('excludedNamespaces')]"
        }
    }
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists 可讓 _您對符合_ **if** 條件之資源的資源進行審核，但沒有在 **then** 條件的 **詳細資料** 中指定的屬性。

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists 評估

AuditIfNotExists 的執行順序是在「資源提供者」已處理建立或更新資源要求，並且已傳回成功狀態碼之後。 如果沒有任何相關資源，或 **ExistenceCondition** 所定義的資源未評估為 true，就會進行稽核。 針對新的和更新的資源，Azure 原則會將 `Microsoft.Authorization/policies/audit/action` 作業新增至活動記錄，並將資源標記為不符合規範。 當觸發時，滿足 **if** 條件的資源會是標示為不符合規範的資源。

### <a name="auditifnotexists-properties"></a>AuditIfNotExists 屬性

AuditIfNotExists 效果的 **details** 屬性含有定義所要比對相關資源的所有子屬性。

- 需要 **輸入** () 
  - 指定要比對之相關資源的類型。
  - 如果 **details.type** 是在 **if** 條件資源之下的資源類型，則此原則會在受評估資源範圍內查詢此 **type** 的資源。 否則，原則會在與受評估資源相同的資源群組內進行查詢。
- **Name** (選擇性)
  - 指定要比對的資源確切名稱，然後使原則擷取一個特定資源，而不是所指定類型的所有資源。
  - 當 **if. field** 的條件值， **然後輸入** match 時， **Name** 會變成 _required_ ，而必須是 `[field('name')]` 或 `[field('fullName')]` 子資源。
    然而，應改為考慮 [audit](#audit) 效果。
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

在 Resource Manager 模式中建立或更新相符的資源時，拒絕會在傳送給資源提供者之前，先防止要求。 要求會以 `403 (Forbidden)` 的形式傳回。 在入口網站中，可將「禁止」視為原則指派所阻止的部署狀態。 若為資源提供者模式，資源提供者會管理資源的評估。

評估現有資源時，符合 Deny 原則定義的資源會標示為不符合規範。

### <a name="deny-properties"></a>Deny 屬性

在 Resource Manager 模式中，拒絕效果沒有任何額外的屬性可供在原則定義的 **then** 條件中使用。

若為的資源提供者模式 `Microsoft.Kubernetes.Data` ，拒絕效果會有下列額外的 **詳細資料** 子屬性。

- **constraintTemplate** (必要) 
  - 可定義新限制式的限制式範本 CustomResourceDefinition (CRD)。 此範本會定義從「Azure 原則」透過 **values** 傳遞的 Rego 邏輯、限制式結構描述及限制式參數。
- **條件約束** (必要) 
  - 限制式範本的 CRD 實作， 會使用透過 **values** 作為 `{{ .Values.<valuename> }}` 傳遞的參數。 在下列範例2中，這些值為 `{{ .Values.excludedNamespaces }}` 和 `{{ .Values.allowedContainerImagesRegex }}` 。
- **值** (選擇性) 
  - 定義任何傳遞給限制式的參數和值。 每個值都必須存在於限制式範本 CRD 中。

### <a name="deny-example"></a>Deny 範例

範例1：使用 Resource Manager 模式的 deny 效果。

```json
"then": {
    "effect": "deny"
}
```

範例2：使用的資源提供者模式拒絕效果 `Microsoft.Kubernetes.Data` 。 **詳細資料** 中的其他資訊會定義要在 Kubernetes 中使用的條件約束範本和 .crd，以限制允許的容器映射。

```json
"then": {
    "effect": "deny",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/constraint.yaml",
        "values": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]",
            "excludedNamespaces": "[parameters('excludedNamespaces')]"
        }
    }
}
```

## <a name="deployifnotexists"></a>DeployIfNotExists

DeployIfNotExists 原則定義與 AuditIfNotExists 類似，也會在符合條件時執行範本部署。

> [!NOTE]
> 使用 **deployIfNotExists** 時，支援 [巢狀範本](../../../azure-resource-manager/templates/linked-templates.md#nested-template)，但目前不支援 [連結的範本](../../../azure-resource-manager/templates/linked-templates.md#linked-template)。

### <a name="deployifnotexists-evaluation"></a>DeployIfNotExists 評估

DeployIfNotExists 會在「資源提供者」已處理建立或更新資源要求，並且已傳回成功狀態碼之後，執行約 15 分鐘。 如果沒有任何相關資源，或 **ExistenceCondition** 所定義的資源未評估為 true，就會進行範本部署。
部署的持續時間取決於範本所含資源的複雜度。

在評估週期期間，會將含有 DeployIfNotExists 效果且與資源相符的原則定義標示為不符合規範，但不會對該資源採取任何動作。 現有的不相容資源可透過[補救工作](../how-to/remediate-resources.md)加以補救。

### <a name="deployifnotexists-properties"></a>DeployIfNotExists 屬性

DeployIfNotExists 效果的 **details** 屬性，含有用於定義所要比對的相關資源、所要執行的範本部署的所有子屬性。

- 需要 **輸入** () 
  - 指定要比對之相關資源的類型。
  - 從嘗試在 **if** 條件資源下擷取資源開始著手，然後在與 **if** 條件資源相同的資源群組內進行查詢。
- **Name** (選擇性)
  - 指定要比對的資源確切名稱，然後使原則擷取一個特定資源，而不是所指定類型的所有資源。
  - 當 **if. field** 的條件值， **然後輸入** match 時， **Name** 會變成 _required_ ，而必須是 `[field('name')]` 或 `[field('fullName')]` 子資源。
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
- **roleDefinitionIds** (必要) 
  - 此屬性必須包含與訂用帳戶可存取之角色型存取控制角色識別碼相符的字串陣列。 如需詳細資訊，請參閱[補救 - 設定原則定義](../how-to/remediate-resources.md#configure-policy-definition)。
- **DeploymentScope** (選擇性)
  - 允許的值為 _Subscription_ 和 _ResourceGroup_。
  - 設定要觸發的部署類型。 _Subscription_ 表示 [在訂用帳戶層級進行部署](../../../azure-resource-manager/templates/deploy-to-subscription.md)、_ResourceGroup_ 表示部署至某個資源群組。
  - 使用訂用帳戶層級部署時，必須在 _Deployment_ 中指定 _location_ 屬性。
  - 預設值為 _ResourceGroup_。
- 需要 **部署** () 
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

停用效果的替代方案是 **enforcementMode**，這是在原則指派上設定的。
當 **enforcementMode** 為 _Disabled_ 時，仍會評估資源。 記錄 (如「活動」記錄) 和原則效果不會發生。 如需詳細資訊，請參閱[原則指派 - 強制模式](./assignment-structure.md#enforcement-mode)。

## <a name="enforceopaconstraint"></a>EnforceOPAConstraint

此效果是搭配 `Microsoft.Kubernetes.Data` 的原則定義「模式」使用， 用於將 [OPA Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint#opa-constraint-framework) 定義的 Gatekeeper v3 許可控制規則傳遞到[開放式原則代理程式](https://www.openpolicyagent.org/) (OPA)，再到 Azure 上的 Kubernetes 叢集。

> [!IMPORTANT]
> 具有 **EnforceOPAConstraint** 效果的有限預覽原則定義和相關的 **Kubernetes 服務** 類別已被 _取代_。 相反地，請使用對資源提供者模式進行 _audit_ 和 _deny_ 的效果 `Microsoft.Kubernetes.Data` 。

### <a name="enforceopaconstraint-evaluation"></a>EnforceOPAConstraint 評估

開放式原則代理程式許可控制器會即時評估叢集上的任何新要求。
每 15 分鐘便會完成一次叢集完整掃描，並將結果回報給「Azure 原則」。

### <a name="enforceopaconstraint-properties"></a>EnforceOPAConstraint 屬性

EnforceOPAConstraint 效果的 **details** 屬性有子屬性可描述 Gatekeeper v3 許可控制規則。

- **constraintTemplate** (必要) 
  - 可定義新限制式的限制式範本 CustomResourceDefinition (CRD)。 此範本會定義從「Azure 原則」透過 **values** 傳遞的 Rego 邏輯、限制式結構描述及限制式參數。
- **條件約束** (必要) 
  - 限制式範本的 CRD 實作， 會使用透過 **values** 作為 `{{ .Values.<valuename> }}` 傳遞的參數。 在下列範例中，這些值是 `{{ .Values.cpuLimit }}` 和 `{{ .Values.memoryLimit }}`。
- **值** (選擇性) 
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

> [!IMPORTANT]
> 具有 **EnforceRegoPolicy** 效果及相關 **Kubernetes 服務** 類別的有限預覽原則定義將會遭到 _取代_。 相反地，請使用對資源提供者模式進行 _audit_ 和 _deny_ 的效果 `Microsoft.Kubernetes.Data` 。

### <a name="enforceregopolicy-evaluation"></a>EnforceRegoPolicy 評估

開放式原則代理程式許可控制器會即時評估叢集上的任何新要求。
每 15 分鐘便會完成一次叢集完整掃描，並將結果回報給「Azure 原則」。

### <a name="enforceregopolicy-properties"></a>EnforceRegoPolicy 屬性

EnforceRegoPolicy 效果的 **details** 屬性有子屬性可描述 Gatekeeper v2 許可控制規則。

- **policyId** (必要) 
  - 以參數形式傳遞至 Rego 許可控制規則的唯一名稱。
- 需要 (**原則**) 
  - 指定 Rego 許可控制規則的 URI。
- **policyParameters** (選用) 
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

在建立或更新期間，會使用 Modify 來新增、更新或移除資源上的屬性或標記。
常見範例如更新 costCenter 等資源上的標記。 現有的不相容資源可透過[補救工作](../how-to/remediate-resources.md)加以補救。 單項 Modify 規則的作業數目不拘。

以下是修改支援的作業：

- 新增、取代或移除資源標記。 針對標籤， `mode` 除非目標資源是資源群組，否則修改原則應設定為 [ _索引_ ]。
- 新增或取代 `identity.type` 虛擬機器和虛擬機器擴展集 () 受控識別類型的值。
- 新增或取代 (預覽版) 特定別名的值。
  - 使用`Get-AzPolicyAlias | Select-Object -ExpandProperty 'Aliases' | Where-Object { $_.DefaultMetadata.Attributes -eq 'Modifiable' }`
    在 Azure PowerShell **4.6.0** 或更高版本中，取得可搭配 Modify 使用的別名清單。

> [!IMPORTANT]
> 如果您要管理標記，建議使用 Modify 而非 Append as Modify 提供其他作業類型，以及補救現有資源的能力。 但是，如果您無法建立受控識別，或修改還不支援資源屬性的別名，則建議使用 Append。

### <a name="modify-evaluation"></a>Modify 評估

在建立或更新資源期間，會先由 Modify 進行評估，然後才由「資源提供者」處理要求。 當符合原則規則的 **if** 條件時，修改作業會套用至要求內容。 每個修改作業都可以指定條件，以決定其套用時機。 將會略過評估為 _false_ 之條件的作業。

指定別名時，會執行下列額外的檢查，以確保修改作業不會以導致資源提供者拒絕的方式變更要求內容：

- 別名對應的屬性在要求的 API 版本中標示為「可修改」。
- 修改作業中的 token 類型符合要求的 API 版本中，屬性的預期 token 類型。

如果其中一項檢查失敗，則原則評估會回復至指定的 **conflictEffect**。

> [!IMPORTANT]
> 它的 recommeneded 會修改包含別名的定義，使用 _audit_ **衝突效果** 來避免失敗的要求使用 API 版本，其中對應的屬性不是「可修改」。 如果相同的別名在 API 版本之間有不同的行為，則可以使用條件式修改作業來判斷每個 API 版本所使用的修改作業。

當使用 Modify 效果的原則定義在評估週期中執行時，並不會對已經存在的資源進行變更。 取而代之的是，會將符合 **if** 條件的所有資源標示為不符合規範。

### <a name="modify-properties"></a>Modify 屬性

Modify 效果的 **details** 屬性，含有用於定義修補所需權限的所有子屬性，以及可用於新增、更新或移除目標值的 **operations**。

- **roleDefinitionIds** (必要) 
  - 此屬性必須包含與訂用帳戶可存取之角色型存取控制角色識別碼相符的字串陣列。 如需詳細資訊，請參閱[補救 - 設定原則定義](../how-to/remediate-resources.md#configure-policy-definition)。
  - 定義的角色必須包含授與給[參與者](../../../role-based-access-control/built-in-roles.md#contributor)角色的所有作業。
- **conflictEffect** (選用) 
  - 決定當有一個以上的原則定義修改相同的屬性，或修改作業在指定的別名上無法運作時，哪個原則定義「獲勝」。
    - 針對新的或更新的資源，具有 _拒絕_ 的原則定義優先。 具有 _audit_ 的原則定義會略過所有 **作業**。 如果有一個以上的原則定義 _拒絕_，要求就會被視為衝突。 如果所有原則定義都有 _audit_，則不會處理衝突原則定義的任何 **作業** 。
    - 針對現有的資源，如果有一個以上的原則定義有 _拒絕_，合規性狀態會是 [ _衝突_]。 如果有一或多個原則定義 _拒絕_，則每個指派會傳回 _不符合規範_ 的合規性狀態。
  - 可用的值： _audit_、 _deny_、 _disabled_。
  - 預設值為 _deny_。
- **作業** (必要的) 
  - 在比對資源上所有待完成標記作業的陣列。
  - 屬性：
    - 作業 **(必要**) 
      - 定義要對相符資源採取的動作。 選項包括：_addOrReplace_、_Add_、_Remove_。 _Add_ 的行為類似 [Append](#append) 效果。
    - 需要 (**欄位**) 
      - 要新增、取代或移除的標記。 標記名稱必須遵守其他[欄位](./definition-structure.md#fields)的相同命名慣例。
    - **value** (選擇性)
      - 標記所要設定的值。
      - 如果 **operation** 為 _addOrReplace_ 或 _Add_，則此屬性為必要。
    - **條件** (選擇性) 
      - 包含 Azure 原則語言運算式的字串，其 [原則](./definition-structure.md#policy-functions) 函式會評估為 _true_ 或 _false_。
      - 不支援下列原則函數： `field()` 、 `resourceGroup()` 、 `subscription()` 。

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
|addOrReplace |將已定義的屬性或標記和值加入至資源，即使屬性或標記已經存在且具有不同的值。 |
|加 |將已定義的屬性或標記和值新增至資源。 |
|移除 |從資源中移除已定義的屬性或標記。 |

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
        "conflictEffect": "deny",
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

範例3：確定儲存體帳戶不允許 blob 公用存取，只有在評估 API 版本大於或等於 ' 2019-04-01 ' 的要求時，才會套用修改作業：

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/microsoft.authorization/roleDefinitions/17d1049b-9a84-46fb-8f53-869881c3d3ab"
        ],
        "conflictEffect": "audit",
        "operations": [
            {
                "condition": "[greaterOrEquals(requestContext().apiVersion, '2019-04-01')]",
                "operation": "addOrReplace",
                "field": "Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
                "value": false
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

針對每個指派都會以獨立方式進行評估。 因此，不可能讓資源從範圍差異的間隙中逃脫。 分層原則定義的淨結果會被視為 **累計限制最嚴格的** 結果。 舉例來說，如果原則 1 和原則 2 都具有 Deny 效果，則資源會遭到重疊和衝突的原則定義封鎖。 如果您仍然需要在目標範圍中建立該資源，請檢閱每項指派的相關排除項目，以驗證是由正確的原則指派在影響正確的範圍。

## <a name="next-steps"></a>後續步驟

- 在 [Azure 原則範例](../samples/index.md)檢閱範例。
- 檢閱 [Azure 原則定義結構](definition-structure.md)。
- 了解如何[以程式設計方式建立原則](../how-to/programmatically-create.md)。
- 了解如何[取得合規性資料](../how-to/get-compliance-data.md)。
- 了解如何[補救不符合規範的資源](../how-to/remediate-resources.md)。
- 透過[使用 Azure 管理群組來組織資源](../../management-groups/overview.md)來檢閱何謂管理群組。
