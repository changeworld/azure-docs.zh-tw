---
title: 了解效果的運作方式
description: Azure 策略定義具有各種效果,可確定如何管理和報告合規性。
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: 0330cb5c732921efda3627dec92e486657097d82
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422446"
---
# <a name="understand-azure-policy-effects"></a>了解 Azure 原則效果

「Azure 原則」中的每個原則定義都有一個效果。 該效果決定了當原則規則評估為相符時會發生的情況。 這些效果在針對新資源、已更新的資源或現有的資源時，各有不同的行為表現。

原則定義目前支援這些影響:

- [附加](#append)
- [稽核](#audit)
- [稽核不存在](#auditifnotexists)
- [拒絕](#deny)
- [部署不存在](#deployifnotexists)
- [關閉](#disabled)
- [強制執行 OPA 規範](#enforceopaconstraint)(預覽)
- [執行重新政策](#enforceregopolicy)(預覽)
- [修改](#modify)

## <a name="order-of-evaluation"></a>評估順序

「Azure 原則」會先評估透過 Azure Resource Manager 進行的資源建立或更新要求。 Azure 策略創建應用於資源的所有分配的清單,然後根據每個定義評估資源。 在將請求交給相應的資源提供程式之前,Azure 策略會處理多個影響。 這樣做可以防止資源提供程式在資源不符合 Azure 策略的設計治理控件時進行不必要的處理。

- 首先會檢查 **Disabled**，以決定是否應評估原則規則。
- 然後評估**新增**與**修改**。 由於任一項可以更改請求,因此所做的更改可能會阻止審核或拒絕觸發效果。
- 接著評估的是 **Deny**。 在 Audit 前先評估 Deny 可防止重複記錄不想要的資源。
- 接著會先評估 **Audit**，然後才將要求傳遞給「資源提供者」。

在「資源提供者」傳回成功碼之後，便會評估 **AuditIfNotExists** 和 **DeployIfNotExists**，以判斷是否需要進行後額外的合規性記錄或動作。

目前,對於**強制 OPA 約束**或**強制重新執行策略**效果,沒有任何評估順序。

## <a name="disabled"></a>已停用

針對測試情況，或當原則定義已將效果參數化時，此效果相當有用。 這個彈性讓您得以停用單一指派，而不是停用該原則的所有指派。

關閉效果的替代是策略配置設定的**強制模式**。
關閉**強制模式**時 _,_ 仍會評估資源。 日誌記錄(如活動日誌)和策略效果不會發生。 有關詳細資訊,請參閱[政策配置 - 執行模式](./assignment-structure.md#enforcement-mode)。

## <a name="append"></a>附加

Append 可用來在建立或更新所要求的資源時，為資源新增額外的欄位。 常見範例是為儲存資源指定允許的 IP。

> [!IMPORTANT]
> 追加功能適用於非標記屬性。 雖然在創建或更新請求期間可以將標記添加到資源,但建議對標記使用["修改](#modify)"效果。

### <a name="append-evaluation"></a>Append 評估

在建立或更新資源期間，會先由 Append 進行評估，然後才由「資源提供者」處理要求。 Append 會在原則規則的 **if** 條件相符時，為資源新增欄位。 如果 Append 效果會以不同的值覆寫原始要求中的值，則它會充當 Deny 效果而拒絕該要求。 要將新值追加到現有陣列,請使用別名的**\**** 版本。

當使用 Append 效果的原則定義在評估週期中執行時，並不會對已經存在的資源進行變更。 取而代之的是，會將符合 **if** 條件的所有資源標示為不符合規範。

### <a name="append-properties"></a>Append 屬性

Append 效果只有一個 **details** 陣列且為必要。 由於 **details** 是陣列，因此它可以接受單一的 **field/value** 配對，也可以接受多個配對。 如需了解可接受的欄位清單，請參考[定義結構](definition-structure.md#fields)。

### <a name="append-examples"></a>Append 範例

範例 1:[使用具有數位](definition-structure.md#aliases)**值**的非**欄位\*****/值**對在儲存帳戶上設置 IP 規則。 當非**\*+ 別名**是陣列時,效果將**值**追加為整個陣列。 如果陣列已經存在，就會因為衝突而發生拒絕事件。

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

範例 2:使用具有數位**值**的**\* +** [別名](definition-structure.md#aliases)的單個**欄位/值**對在儲存帳戶上設置 IP 規則。 通過使用**\** 別名**,效果將**該值**追加到可能預先存在的陣列。 如果陣列尚不存在,將創建該陣列。

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

修改用於在創建或更新期間添加、更新或刪除資源上的標記。 一個常見示例是更新資源(如成本中心)上的標記。 除非目標資源是資源組`mode`,否則修改策略應始終設置為_已編制索引_。 現有的不合規資源可以通過[修正工作進行修正](../how-to/remediate-resources.md)。 單個修改規則可以具有任意數量的操作。

> [!IMPORTANT]
> 修改當前僅用於標記。 如果要管理標記,建議使用"修改"而不是"追加",因為"修改"提供了其他操作類型和修復現有資源的能力。 但是,如果您無法創建託管標識,則建議追加應用。

### <a name="modify-evaluation"></a>修改評估

在創建或更新資源期間資源提供程式處理請求之前進行修改評估。 當滿足策略規則的**if**條件時,修改資源上的添加或更新標記。

當使用"修改"效果的策略定義作為評估週期的一部分運行時,它不會對已經存在的資源進行更改。 取而代之的是，會將符合 **if** 條件的所有資源標示為不符合規範。

### <a name="modify-properties"></a>修改內容

"修改"效果**的詳細資訊**屬性具有定義補救所需的許可權以及用於添加、更新或刪除標記值**的操作**的所有子屬性。

- **roleDefinitionIds** [必要]
  - 此屬性必須包含與訂用帳戶可存取之角色型存取控制角色識別碼相符的字串陣列。 如需詳細資訊，請參閱[補救 - 設定原則定義](../how-to/remediate-resources.md#configure-policy-definition)。
  - 定義的角色必須包括授予[參與者](../../../role-based-access-control/built-in-roles.md#contributor)角色的所有操作。
- **操作**[必需]
  - 要在匹配資源上完成的所有標記操作的陣列。
  - 屬性：
    - **操作**[必需]
      - 定義對匹配的資源執行的操作。 選項包含:_加入或取代_、_新增_、_刪除_。 _添加_與[「追加」](#append)效果類似的效果。
    - **欄位**[必需]
      - 要添加、替換或刪除的標記。 標記名稱必須遵循其他[欄位](./definition-structure.md#fields)的相同命名約定。
    - **值**(選擇性的)
      - 要將標記設置為的值。
      - 如果**操作**是_添加或添加__,則_此屬性是必需的。

### <a name="modify-operations"></a>變更操作

**操作**屬性陣列允許從單個策略定義以不同的方式更改多個標記。 每個操作由**操作**、**欄位**和**值**屬性組成。 操作確定修正任務對標記的作用,欄位確定更改哪個標記,並且值定義該標記的新設置。 下面的範例進行了以下標記變更:

- 將`environment`標記設置為"測試",即使它已使用不同的值存在也是如此。
- 刪除標籤`TempResource`。
- 將`Dept`標記設定到策略分配上配置的策略參數 _「部門名稱_」 。。

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

**操作**屬性有以下選項:

|作業 |描述 |
|-|-|
|新增器取代 |將定義的標記和值添加到資源中,即使該標記已存在具有不同值。 |
|加 |將定義的標記和值添加到資源中。 |
|移除 |從資源中刪除定義的標記。 |

### <a name="modify-examples"></a>變更範例

範例 1:`environment`加入 標籤`environment`並將現有 標記取代為「測試」:

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

範例 2:`env`刪除 標籤並`environment`新增 標籤或將`environment`現有 標籤取代為參數化值:

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

審核是 Azure 策略在創建或更新資源期間檢查的最後一個效果。 然後,Azure 策略將資源發送到資源提供程式。 Audit 對資源要求和評估週期的運作方式相同。 Azure 策略`Microsoft.Authorization/policies/audit/action`將 操作添加到活動日誌,並將資源標記為不合規。

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

AuditIfNotExists 的執行順序是在「資源提供者」已處理建立或更新資源要求，並且已傳回成功狀態碼之後。 如果沒有任何相關資源，或 **ExistenceCondition** 所定義的資源未評估為 true，就會進行稽核。 Azure 策略`Microsoft.Authorization/policies/audit/action`向 活動日誌添加操作的方式與審核效果相同。 當觸發時，滿足 **if** 條件的資源會是標示為不符合規範的資源。

### <a name="auditifnotexists-properties"></a>AuditIfNotExists 屬性

AuditIfNotExists 效果的 **details** 屬性含有定義所要比對相關資源的所有子屬性。

- **Type** [必要]
  - 指定要比對之相關資源的類型。
  - 如果**詳細資訊.type**是**if**條件資源下方的資源類型,則策略查詢評估資源範圍內的此**類型**資源。 否則,策略查詢與計算的資源組在同一資源組中。
- **名稱**(選擇性的)
  - 指定要比對的資源確切名稱，然後使原則擷取一個特定資源，而不是所指定類型的所有資源。
  - 當**if.field.type**與 **.details.type**的條件值符合時,名稱會變為_必填_`[field('name')]`**項目**,並且必須為 。 但是,應考慮[審計](#audit)效果。
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

與 AuditIf NotExists 類似,DeployIfNotExists 策略定義在滿足條件時執行範本部署。

> [!NOTE]
> 使用 **deployIfNotExists** 時，支援[巢狀範本](../../../azure-resource-manager/templates/linked-templates.md#nested-template)，但目前不支援[連結的範本](../../../azure-resource-manager/templates/linked-templates.md#linked-template)。

### <a name="deployifnotexists-evaluation"></a>DeployIfNotExists 評估

部署IfNotExists在資源提供程式處理創建或更新資源請求並返回成功狀態代碼後大約15分鐘運行。 如果沒有任何相關資源，或 **ExistenceCondition** 所定義的資源未評估為 true，就會進行範本部署。
部署的持續時間取決於範本中包含的資源的複雜性。

在評估週期期間，會將含有 DeployIfNotExists 效果且與資源相符的原則定義標示為不符合規範，但不會對該資源採取任何動作。 現有的不合規資源可以通過[修正工作進行修正](../how-to/remediate-resources.md)。

### <a name="deployifnotexists-properties"></a>DeployIfNotExists 屬性

DeployIfNotExists 效果**的詳細資訊**屬性具有定義要匹配的相關資源的所有子屬性和要執行的範本部署。

- **Type** [必要]
  - 指定要比對之相關資源的類型。
  - 從嘗試在 **if** 條件資源下擷取資源開始著手，然後在與 **if** 條件資源相同的資源群組內進行查詢。
- **名稱**(選擇性的)
  - 指定要比對的資源確切名稱，然後使原則擷取一個特定資源，而不是所指定類型的所有資源。
  - 當**if.field.type**與 **.details.type**的條件值符合時,名稱會變為_必填_`[field('name')]`**項目**,並且必須為 。
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

## <a name="enforceopaconstraint"></a>強制執行 OPA 規範

此效果與`Microsoft.Kubernetes.Data`的策略定義*模式*一起使用。 它用於將使用[OPA 約束框架](https://github.com/open-policy-agent/frameworks/tree/master/constraint#opa-constraint-framework)定義的 Gatekeeper v3 准入控制規則傳遞給[Azure](https://www.openpolicyagent.org/)上的自管理的 Kubernetes 群集。

> [!NOTE]
> [AKS 引擎的 Azure 策略](aks-engine.md)處於公共預覽版,僅支援內建策略定義。

### <a name="enforceopaconstraint-evaluation"></a>強制OPA約束評估

開放策略代理准入控制器即時評估群集上的任何新請求。
每隔 5 分鐘完成群集的完整掃描,並將結果報告給 Azure 策略。

### <a name="enforceopaconstraint-properties"></a>強制OPA規範屬性

強制OPA約束效果**的詳細資訊**屬性具有描述門衛 v3 准入控制規則的子屬性。

- **規範樣本**[必需]
  - 定義新約束的約束範本自定義資源定義 (CRD)。 範本定義透過 Azure 策略**的值**傳遞的 Rego 邏輯、約束架構和約束參數。
- **規範**[必需]
  - 約束範本的CRD實現。 使用透過**值**傳遞的參數`{{ .Values.<valuename> }}`。 在下面的範例中,這將是`{{ .Values.cpuLimit }}`和`{{ .Values.memoryLimit }}`。
- **值**[選擇性的]
  - 定義要傳遞給約束的任何參數和值。 約束範本CRD中必須存在每個值。

### <a name="enforceopaconstraint-example"></a>強制 OPA 規範範例

示例:網守 v3 准入控制規則,用於在 AKS 引擎中設置容器 CPU 和記憶體資源限制。

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

## <a name="enforceregopolicy"></a>執行重戈政策

此效果與`Microsoft.ContainerService.Data`的策略定義*模式*一起使用。 它用於傳遞在[Azure Kubernetes 服務](../../../aks/intro-kubernetes.md)上使用[Rego](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego)到[打開策略代理](https://www.openpolicyagent.org/)(OPA) 定義的門衛 v2 准入控制規則。

> [!NOTE]
> [AKS 的 Azure 策略](rego-for-aks.md)處於「有限預覽」狀態,僅支援內建策略定義

### <a name="enforceregopolicy-evaluation"></a>執行重新原則評估

開放策略代理准入控制器即時評估群集上的任何新請求。
每隔 5 分鐘完成群集的完整掃描,並將結果報告給 Azure 策略。

### <a name="enforceregopolicy-properties"></a>強制執行「重新戈策略」屬性

強制重新策略效果**的詳細資訊**屬性具有描述門衛 v2 准入控制規則的子屬性。

- **策略代碼**[必需]
  - 作為參數傳遞給 Rego 准入控制規則的唯一名稱。
- **政策**[必需]
  - 指定"Rego准入控制"規則的URI。
- **原則參數**[選擇性的]
  - 定義要傳遞給 rego 策略的任何參數和值。

### <a name="enforceregopolicy-example"></a>執行重新原則範例

示例:網守 v2 准入控制規則,以僅允許在 AKS 中指定容器映射。

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

針對每個指派都會以獨立方式進行評估。 因此，不可能讓資源從範圍差異的間隙中逃脫。 分層原則或原則重疊的淨結果會被視為**累計限制最嚴格的**結果。 舉例來說，如果原則 1 和原則 2 都具有 Deny 效果，則資源會遭到重疊和衝突原則封鎖。 如果您仍然需要在目標範圍中建立該資源，請檢閱每項指派的相關排除項目，以驗證是由正確的原則影響正確的範圍。

## <a name="next-steps"></a>後續步驟

- 查看[Azure 策略範例](../samples/index.md)中的範例。
- 檢閱 [Azure 原則定義結構](definition-structure.md)。
- 瞭解如何[以程式設計方式建立原則](../how-to/programmatically-create.md)。
- 瞭解如何[取得合規性資料](../how-to/get-compliance-data.md)。
- 瞭解如何[修復不合規資源](../how-to/remediate-resources.md)。
- 使用[Azure 管理組查看](../../management-groups/overview.md)管理組的內容。請組織資源。
