---
title: 了解效果的運作方式
description: Azure 原則定義有各種不同的效果，可決定合規性的管理和報告方式。
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 502c8a87c4e915ebd1fd764915daa9c89a307097
ms.sourcegitcommit: 78f367310e243380b591ff10f2500feca93f5d0a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2020
ms.locfileid: "77544125"
---
# <a name="understand-azure-policy-effects"></a>了解 Azure 原則效果

「Azure 原則」中的每個原則定義都有一個效果。 該效果決定了當原則規則評估為相符時會發生的情況。 這些效果在針對新資源、已更新的資源或現有的資源時，各有不同的行為表現。

原則定義中目前支援這些效果：

- [Append](#append)
- [稽核](#audit)
- [AuditIfNotExists](#auditifnotexists)
- [拒絕](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [Disabled](#disabled)
- [EnforceOPAConstraint](#enforceopaconstraint) （預覽）
- [EnforceRegoPolicy](#enforceregopolicy) （預覽）
- [修改](#modify)

## <a name="order-of-evaluation"></a>評估順序

透過 Azure Resource Manager 來建立或更新資源的要求，會先由 Azure 原則評估。 Azure 原則會建立適用于資源的所有指派清單，然後根據每個定義來評估資源。 Azure 原則會先處理數個效果，再將要求交給適當的資源提供者。 這麼做可避免資源提供者在資源不符合 Azure 原則的設計治理控制項時進行不必要的處理。

- 首先會檢查 **Disabled**，以決定是否應評估原則規則。
- 接著會評估 [**附加**] 和 [**修改**]。 由於可能會改變要求，因此所做的變更可能會導致無法觸發 audit 或 deny 的影響。
- 接著評估的是 **Deny**。 在 Audit 前先評估 Deny 可防止重複記錄不想要的資源。
- 接著會先評估 **Audit**，然後才將要求傳遞給「資源提供者」。

在「資源提供者」傳回成功碼之後，便會評估 **AuditIfNotExists** 和 **DeployIfNotExists**，以判斷是否需要進行後額外的合規性記錄或動作。

目前沒有任何**EnforceOPAConstraint**或**EnforceRegoPolicy**效果的評估順序。

## <a name="disabled"></a>已停用

針對測試情況，或當原則定義已將效果參數化時，此效果相當有用。 這個彈性讓您得以停用單一指派，而不是停用該原則的所有指派。

已停用效果的替代方法是在原則指派上設定的**enforcementMode** 。
_停用_ **enforcementMode**時，仍會評估資源。 記錄（例如活動記錄）和原則效果都不會發生。 如需詳細資訊，請參閱[原則指派-強制模式](./assignment-structure.md#enforcement-mode)。

## <a name="append"></a>附加

Append 可用來在建立或更新所要求的資源時，為資源新增額外的欄位。 常見的範例是針對儲存體資源指定允許的 Ip。

> [!IMPORTANT]
> Append 適用于非標記屬性。 雖然 Append 可以在建立或更新要求期間將標籤新增至資源，但建議您改為使用標籤的[修改](#modify)效果。

### <a name="append-evaluation"></a>Append 評估

在建立或更新資源期間，會先由 Append 進行評估，然後才由「資源提供者」處理要求。 Append 會在原則規則的 **if** 條件相符時，為資源新增欄位。 如果 Append 效果會以不同的值覆寫原始要求中的值，則它會充當 Deny 效果而拒絕該要求。 若要將新值附加至現有陣列中，請使用 **[\*]** 版本的別名。

當使用 Append 效果的原則定義在評估週期中執行時，並不會對已經存在的資源進行變更。 取而代之的是，會將符合 **if** 條件的所有資源標示為不符合規範。

### <a name="append-properties"></a>Append 屬性

Append 效果只有一個 **details** 陣列且為必要。 由於 **details** 是陣列，因此它可以接受單一的 **field/value** 配對，也可以接受多個配對。 如需了解可接受的欄位清單，請參考[定義結構](definition-structure.md#fields)。

### <a name="append-examples"></a>Append 範例

範例1：使用非 **[\*]** [別名](definition-structure.md#aliases)搭配陣列**值**來設定儲存體帳戶 IP 規則的單一**欄位/值**組。 當非 **[\*]** 別名是陣列時，效果會將 **value** 當做整個陣列來附加。 如果陣列已經存在，就會因為衝突而發生拒絕事件。

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

範例2：使用 **[\*]** [別名](definition-structure.md#aliases)搭配陣列**值**來設定儲存體帳戶 IP 規則的單一**欄位/值**組。 藉由使用 **[\*]** 別名，效果會將 **value** 附加至可能預先存在的陣列。 如果陣列尚不存在，則會建立它。

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

## <a name="modify"></a>修改

在建立或更新期間，會使用 Modify 來新增、更新或移除資源上的標記。 常見的範例是在資源（例如 costCenter）上更新標記。 除非目標資源是資源群組，否則修改原則應一律將 `mode` 設定為 [已_編制索引_]。 您可以使用[補救](../how-to/remediate-resources.md)工作來補救現有不符合規範的資源。 單一修改規則可以有任意數目的作業。

> [!IMPORTANT]
> Modify 目前僅供與標記搭配使用。 如果您要管理標記，建議使用 [修改] 而非 [附加] 做為 [修改]，提供額外的作業類型以及補救現有資源的能力。 不過，如果您無法建立受控識別，則建議使用 [附加]。

### <a name="modify-evaluation"></a>修改評估

在建立或更新資源期間，由資源提供者處理要求之前，請先修改評估。 當符合原則規則的**if**條件時，Modify 會在資源上新增或更新標記。

當使用 Modify 效果的原則定義在評估週期中執行時，不會對已經存在的資源進行變更。 取而代之的是，會將符合 **if** 條件的所有資源標示為不符合規範。

### <a name="modify-properties"></a>修改內容

[修改] 效果的 [**詳細資料**] 屬性具有所有子屬性，可定義補救所需的許可權，以及用來新增、更新或移除標記值的**作業**。

- **roleDefinitionIds** [必要]
  - 此屬性必須包含與訂用帳戶可存取之角色型存取控制角色識別碼相符的字串陣列。 如需詳細資訊，請參閱[補救 - 設定原則定義](../how-to/remediate-resources.md#configure-policy-definition)。
  - 定義的角色必須包括授與[參與者](../../../role-based-access-control/built-in-roles.md#contributor)角色的所有作業。
- **作業**[必要]
  - 要在比對資源上完成之所有標記作業的陣列。
  - 屬性:
    - 作業 **[必要**]
      - 定義要對相符資源採取的動作。 選項包括： _addOrReplace_、 _Add_、 _Remove_。 _新增_的行為類似于[附加](#append)效果。
    - **欄位**[必要]
      - 要加入、取代或移除的標記。 標記名稱必須遵守其他[欄位](./definition-structure.md#fields)的相同命名慣例。
    - **值**（選擇性）
      - 要設定標記的值。
      - 如果作業是_addOrReplace_或_Add_ **，就**需要這個屬性。

### <a name="modify-operations"></a>修改作業

**Operations**屬性陣列可讓您從單一原則定義以不同的方式更改數個標記。 每項作業都是由 [作業]、[**欄位**] 和 [**值**] 屬性**所組成。** 作業會決定補救工作對標記執行的動作，欄位會決定要更改的標記，而值則會定義該標記的新設定。 以下範例會變更下列標記：

- 將 `environment` 標記設定為 "Test"，即使它已經存在且具有不同的值。
- 移除標記 `TempResource`。
- 將 `Dept` 標記設定為原則指派上設定的原則參數_DeptName_ 。

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

**Operation**屬性具有下列選項：

|作業 |描述 |
|-|-|
|addOrReplace |將已定義的標籤和值新增至資源，即使標記已經存在且具有不同的值。 |
|Add |將已定義的標記和值加入至資源。 |
|移除 |從資源中移除已定義的標記。 |

### <a name="modify-examples"></a>修改範例

範例1：加入 `environment` 標記，並將現有的 `environment` 標記取代為 "Test"：

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

範例2：移除 `env` 標記，並加入 `environment` 標記，或將現有的 `environment` 標記取代為參數化值：

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

## <a name="audit"></a>稽核

Audit 效果可用來在評估到不符合規範的資源時，在活動記錄中建立警告事件，但並不會停止該項要求。

### <a name="audit-evaluation"></a>Audit 評估

Audit 是在建立或更新資源期間 Azure 原則所檢查的最後一個效果。 Azure 原則接著會將資源傳送至資源提供者。 Audit 對資源要求和評估週期的運作方式相同。 Azure 原則會將 `Microsoft.Authorization/policies/audit/action` 作業新增至活動記錄，並將資源標示為不符合規範。

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

AuditIfNotExists 的執行順序是在「資源提供者」已處理建立或更新資源要求，並且已傳回成功狀態碼之後。 如果沒有任何相關資源，或 **ExistenceCondition** 所定義的資源未評估為 true，就會進行稽核。 Azure 原則會將 `Microsoft.Authorization/policies/audit/action` 作業新增至活動記錄，其方式與審核效果相同。 當觸發時，滿足 **if** 條件的資源會是標示為不符合規範的資源。

### <a name="auditifnotexists-properties"></a>AuditIfNotExists 屬性

AuditIfNotExists 效果的 **details** 屬性含有定義所要比對相關資源的所有子屬性。

- **Type** [必要]
  - 指定要比對之相關資源的類型。
  - 如果**details. type**是在**if**條件資源下的資源類型，原則就會在評估的資源範圍內查詢此**類型**的資源。 否則，原則查詢會在與評估資源相同的資源群組內。
- **Name** (選擇性)
  - 指定要比對的資源確切名稱，然後使原則擷取一個特定資源，而不是所指定類型的所有資源。
  - **如果 if. field. type**和 **. details.** 類型相符，則**Name**會變成_必要_，而且必須 `[field('name')]`。 不過，應該改為考慮[審核](#audit)效果。
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

範例：評估虛擬機器以判斷「反惡意程式碼軟體」延伸模組是否存在，然後在遺漏該軟體時進行稽核。

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

## <a name="deployifnotexists"></a>DeployIfNotExists

類似于 AuditIfNotExists，DeployIfNotExists 原則定義會在符合條件時執行範本部署。

> [!NOTE]
> 使用 [deployIfNotExists](../../../azure-resource-manager/templates/linked-templates.md#nested-template) 時，支援**巢狀範本**，但目前不支援[連結的範本](../../../azure-resource-manager/templates/linked-templates.md#linked-template)。

### <a name="deployifnotexists-evaluation"></a>DeployIfNotExists 評估

在資源提供者已處理建立或更新資源要求，且已傳回成功狀態碼之後，DeployIfNotExists 大約會執行15分鐘。 如果沒有任何相關資源，或 **ExistenceCondition** 所定義的資源未評估為 true，就會進行範本部署。
部署的持續時間取決於範本中包含的資源複雜度。

在評估週期期間，會將含有 DeployIfNotExists 效果且與資源相符的原則定義標示為不符合規範，但不會對該資源採取任何動作。

### <a name="deployifnotexists-properties"></a>DeployIfNotExists 屬性

DeployIfNotExists 效果的**details**屬性具有所有子屬性，可定義要符合的相關資源，以及要執行的範本部署。

- **Type** [必要]
  - 指定要比對之相關資源的類型。
  - 從嘗試在 **if** 條件資源下擷取資源開始著手，然後在與 **if** 條件資源相同的資源群組內進行查詢。
- **Name** (選擇性)
  - 指定要比對的資源確切名稱，然後使原則擷取一個特定資源，而不是所指定類型的所有資源。
  - **如果 if. field. type**和 **. details.** 類型相符，則**Name**會變成_必要_，而且必須 `[field('name')]`。
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

## <a name="enforceopaconstraint"></a>EnforceOPAConstraint

此效果會與 `Microsoft.Kubernetes.Data`的原則定義*模式*搭配使用。 它可用來傳遞以[OPA 條件約束架構](https://github.com/open-policy-agent/frameworks/tree/master/constraint#opa-constraint-framework)定義的閘道管理員 v3 許可控制規則，以將[原則代理程式](https://www.openpolicyagent.org/)（OPA）開啟至 Azure 上的自我管理 Kubernetes 叢集。

> [!NOTE]
> [適用于 AKS 引擎的 Azure 原則](aks-engine.md)處於公開預覽狀態，而且只支援內建原則定義。

### <a name="enforceopaconstraint-evaluation"></a>EnforceOPAConstraint 評估

開放原則代理程式許可控制站會即時評估叢集上的任何新要求。
每隔5分鐘會完成叢集的完整掃描，並回報結果以 Azure 原則。

### <a name="enforceopaconstraint-properties"></a>EnforceOPAConstraint 屬性

EnforceOPAConstraint 效果的**details**屬性具有描述閘道管理員 v3 許可控制規則的子屬性。

- **constraintTemplate** [必要]
  - 定義新條件約束的條件約束範本 CustomResourceDefinition （.CRD）。 此範本會定義 Rego 邏輯、條件約束架構，以及透過 Azure 原則的**值**傳遞的條件約束參數。
- **條件約束**[必要]
  - 條件約束範本的 .CRD 實。 使用透過**值**傳遞的參數做為 `{{ .Values.<valuename> }}`。 在下列範例中，這會是 `{{ .Values.cpuLimit }}` 並 `{{ .Values.memoryLimit }}`。
- **值**[選擇性]
  - 定義要傳遞給條件約束的任何參數和值。 每個值都必須存在於條件約束範本 .CRD 中。

### <a name="enforceregopolicy-example"></a>EnforceRegoPolicy 範例

範例：閘道管理員 v3 的許可控制規則，可在 AKS 引擎中設定容器 CPU 和記憶體資源限制。

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

此效果會與 `Microsoft.ContainerService.Data`的原則定義*模式*搭配使用。 它可用來傳遞以[Rego](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego)定義的閘道管理員 v2 許可控制規則，以在[Azure Kubernetes Service](../../../aks/intro-kubernetes.md)上[開啟原則代理程式](https://www.openpolicyagent.org/)（OPA）。

> [!NOTE]
> [適用于 AKS 的 Azure 原則](rego-for-aks.md)處於有限預覽狀態，且僅支援內建原則定義

### <a name="enforceregopolicy-evaluation"></a>EnforceRegoPolicy 評估

開放原則代理程式許可控制站會即時評估叢集上的任何新要求。
每隔5分鐘會完成叢集的完整掃描，並回報結果以 Azure 原則。

### <a name="enforceregopolicy-properties"></a>EnforceRegoPolicy 屬性

EnforceRegoPolicy 效果的**details**屬性具有描述閘道管理員 v2 許可控制規則的子屬性。

- **policyId** [必要]
  - 當做參數傳遞至 Rego 許可控制規則的唯一名稱。
- **原則**[必要]
  - 指定 Rego 許可控制規則的 URI。
- **policyParameters** [選用]
  - 定義要傳遞至 rego 原則的任何參數和值。

### <a name="enforceregopolicy-example"></a>EnforceRegoPolicy 範例

範例：閘道管理員 v2 許可控制規則，僅允許 AKS 中指定的容器映射。

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

## <a name="layering-policies"></a>分層原則

一個資源可能會受到數個指派影響。 這些指派可能屬於相同範圍，也可能屬於不同範圍。 這些指派中的每項指派也可能定義了不同的效果。 針對每個原則的條件和效果，都會以獨立方式進行評估。 例如，

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

針對每個指派都會以獨立方式進行評估。 因此，不可能讓資源從範圍差異的間隙中逃脫。 分層原則或原則重疊的淨結果會被視為**累計限制最嚴格的**結果。 舉例來說，如果原則 1 和原則 2 都具有 Deny 效果，則資源會遭到重疊和衝突原則封鎖。 如果您仍然需要在目標範圍中建立該資源，請檢閱每項指派的相關排除項目，以驗證是由正確的原則影響正確的範圍。

## <a name="next-steps"></a>後續步驟

- 如[Azure 原則範例](../samples/index.md)，請參閱範例。
- 檢閱 [Azure 原則定義結構](definition-structure.md)。
- 瞭解如何以程式設計[方式建立原則](../how-to/programmatically-create.md)。
- 瞭解如何[取得合規性資料](../how-to/get-compliance-data.md)。
- 瞭解如何[補救不符合規範的資源](../how-to/remediate-resources.md)。
- 透過[使用 Azure 管理群組來組織資源](../../management-groups/overview.md)來檢閱何謂管理群組。
