---
title: 計畫定義結構的詳細資料
description: 說明如何使用原則計畫定義來群組原則定義，以部署至您組織中的 Azure 資源。
ms.date: 05/29/2020
ms.topic: conceptual
ms.openlocfilehash: 80fa90765caa25d6995220134b9a5b4225133219
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84205869"
---
# <a name="azure-policy-initiative-definition-structure"></a>Azure 原則計畫定義結構

計畫可讓您將數個相關的原則定義組成群組來簡化指派和管理，因為您可以將一個群組當作單一項目來使用。 例如，您可以將相關的標籤原則定義組成單一方案。 您可以套用該計畫，而不個別指派每個原則。

您可以使用 JSON 來建立原則計畫定義。 原則計畫定義包含下列專案的元素：

- 顯示名稱
- description
- 中繼資料
- 參數
- 原則定義
- 原則群組（此屬性屬於[法規合規性（預覽）功能](./regulatory-compliance.md)的一部分）

下列範例說明如何建立一個處理兩個標籤 (`costCenter` 和 `productName`) 的計畫。 它會使用兩個內建的原則來套用預設標籤值。

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "metadata": {
            "version": "1.0.0",
            "category": "Tags"
        },
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                },
                "defaultValue": "DefaultCostCenter"
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                },
                "defaultValue": "DefaultProduct"
            }
        },
        "policyDefinitions": [{
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    }
}
```

Azure 原則內建和模式位於 Azure 原則的[範例](../samples/index.md)。

## <a name="metadata"></a>中繼資料

選擇性的 `metadata` 屬性會儲存原則計畫定義的相關資訊。
客戶可以在中定義適用于其組織的任何屬性和值 `metadata` 。 不過，Azure 原則和內建有一些_常用_的屬性。

### <a name="common-metadata-properties"></a>通用中繼資料屬性

- `version`（字串）：追蹤原則計畫定義內容版本的詳細資料。
- `category`（字串）：決定要在哪個類別目錄中顯示原則定義 Azure 入口網站。

  > [!NOTE]
  > 針對[法規合規性](./regulatory-compliance.md)計畫， `category` 必須**符合法規**。

- `preview`（布林值）：如果原則計畫定義為_預覽_，則為 True 或 false 旗標。
- `deprecated`（布林值）：如果原則計畫定義已標示為已被_取代_，則為 True 或 false 旗標。

> [!NOTE]
> Azure 原則服務會使用 `version`、`preview` 和 `deprecated` 屬性，將變更層級傳達給內建原則定義或計畫和狀態。 `version` 的格式如下：`{Major}.{Minor}.{Patch}`。 特定狀態 (例如 _已淘汰_或_預覽_) 會附加至 `version` 屬性，或在另一個屬性中附加為**布林值**。 如需 Azure 原則版本內建之方式的詳細資訊，請參閱[內建版本](https://github.com/Azure/azure-policy/blob/master/built-in-policies/README.md)設定。

## <a name="parameters"></a>參數

參數可減少原則定義數量來幫助您簡化原則管理。 將參數想像成就像表單上的欄位一樣 – `name``address`、`city``state`。 這些參數一律保持不變，不過它們的值則會根據填寫表單的個人而有所變更。
建立原則計畫時，參數的工作方式相同。 藉由在原則計畫定義中包含參數，您可以在包含的原則中重複使用該參數。

> [!NOTE]
> 指派計畫之後，就無法改變 initative 層級參數。 因此，建議您在定義參數時，設定 **defaultValue**。

### <a name="parameter-properties"></a>參數屬性

參數具有下列在原則計畫定義中使用的屬性：

- `name`：參數的名稱。 由原則規則中的 `parameters` 部署函式使用。 如需詳細資訊，請參閱[使用參數值](#passing-a-parameter-value-to-a-policy-definition)。
- `type`:判斷參數是**字串**、**陣列**、**物件**、**布林值**、**整數**、**浮點**，還是**日期時間**。
- `metadata`:定義主要由 Azure 入口網站使用的子屬性，以顯示使用者易讀的資訊：
  - `description`:參數用途的說明。 能用來提供可接受值的範例。
  - `displayName`:參數在入口網站中顯示的易記名稱。
  - `strongType`:(選擇性) 透過入口網站指派原則定義時會使用。 提供內容感知清單。 如需詳細資訊，請參閱 [strongType](#strongtype)。
- `defaultValue`:(選擇性) 如果沒有提供值，就在指派中設定參數的值。
- `allowedValues`:(選擇性) 提供參數在指派期間所接受的值陣列。

例如，您可以定義原則計畫定義，以限制各種內含原則定義中的資源位置。 可以**allowedLocations**該原則計畫定義的參數。 然後，此參數會提供給每個包含的原則定義，並在指派原則計畫期間定義。

```json
"parameters": {
    "init_allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": [ "westus2" ],
        "allowedValues": [
            "eastus2",
            "westus2",
            "westus"
        ]
    }
}
```

### <a name="passing-a-parameter-value-to-a-policy-definition"></a>將參數值傳遞至原則定義

您可以宣告要傳遞至哪些計畫參數，其中包含計畫定義之[policyDefinitions](#policy-definitions)陣列中的原則定義。 雖然參數名稱可以相同，但是在計畫中使用與原則定義中不同的名稱會簡化程式碼的可讀性。

例如，先前定義的**init_allowedLocations**方案參數可以傳遞至數個包含的原則定義及其參數， **sql_locations**和**vm_locations**，如下所示：

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/0ec8fc28-d5b7-4603-8fec-39044f00a92b",
        "policyDefinitionReferenceId": "allowedLocationsSQL",
        "parameters": {
            "sql_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/aa09bd0f-aa5f-4343-b6ab-a33a6a6304f3",
        "policyDefinitionReferenceId": "allowedLocationsVMs",
        "parameters": {
            "vm_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    }
]
```

這個範例會參考在[參數屬性](#parameter-properties)中所示範的**init_allowedLocations**參數。

### <a name="strongtype"></a>strongType

在 `metadata` 屬性內，您可以使用 **strongType** 在 Azure 入口網站內提供可複選的選項清單。 **strongType** 可以是支援的_資源類型_或允許的值。 若要判斷_資源類型_是否對 **strongType** 有效，請使用 [Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider)。

支援部分不是由 Get-AzResourceProvider 傳回的**資源類型**。 這些資源類型如下：

- `Microsoft.RecoveryServices/vaults/backupPolicies`

**StrongType**的非資源類型允許值為：

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="policy-definitions"></a>原則定義

`policyDefinitions`計畫定義的部分是一種_陣列_，其中現有的原則定義會包含在計畫中。 如將[參數值傳遞至原則定義](#passing-a-parameter-value-to-a-policy-definition)中所述，此屬性為將[方案參數](#parameters)傳遞至原則定義的位置。

### <a name="policy-definition-properties"></a>原則定義屬性

代表原則定義的每個_陣列_元素都具有下列屬性：

- `policyDefinitionId`（字串）：要包含的自訂或內建原則定義的識別碼。
- `policyDefinitionReferenceId`（字串）：包含的原則定義的簡短名稱。
- `parameters`：（選擇性）用來將計畫參數傳遞至所包含原則定義的名稱/值組，做為該原則定義中的屬性。 如需詳細資訊，請參閱[參數](#parameters)。
- `groupNames`（字串陣列）：（選擇性）原則定義為其成員的群組。 如需詳細資訊，請參閱[原則群組](#policy-definition-groups)。

以下是一個範例 `policyDefinitions` ，其中包含兩個已通過相同計畫參數的原則定義：

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/0ec8fc28-d5b7-4603-8fec-39044f00a92b",
        "policyDefinitionReferenceId": "allowedLocationsSQL",
        "parameters": {
            "sql_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/aa09bd0f-aa5f-4343-b6ab-a33a6a6304f3",
        "policyDefinitionReferenceId": "allowedLocationsVMs",
        "parameters": {
            "vm_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    }
]
```

## <a name="policy-definitions-groups-preview"></a><a name="policy-definition-groups"></a>原則定義群組（預覽）

做為 Azure 原則的[法規合規性](./regulatory-compliance.md)（預覽）功能的一部分，計畫定義中的原則定義可以分組。 這項資訊是在 `policyDefinitionGroups` _陣列_屬性中定義。 這些群組具有其他詳細資料，例如原則定義提供涵蓋範圍的**控制**和**合規性網域**。
您可以在 Microsoft 建立的**policyMetadata**物件中找到其他群組詳細資料。 如需詳細資訊，請參閱[中繼資料物件](#metadata-objects)。

### <a name="policy-definition-groups-parameters"></a>原則定義群組參數

中的每個_陣列_元素都 `policyDefinitionGroups` 必須有下列兩個屬性：

- `name`（字串） \[必要 \] ：**控制項**的簡短名稱。 在中，會使用這個屬性的 `groupNames` 值 `policyDefinitions` 。
- `category`（字串）：控制項的**合規性網域**。
- `displayName`（字串）：**控制項**的易記名稱。 由入口網站使用。
- `description`（字串）：**控制項**用途的描述。
- `additionalMetadataId`（字串）： [policyMetadata](#metadata-objects)物件的位置，其中具有與**控制項**和**合規性網域**有關的其他詳細資料。

  > [!NOTE]
  > 客戶可能會指向現有的[policyMetadata](#metadata-objects)物件。 不過，這些物件是_唯讀_的，而且只能由 Microsoft 建立。

`policyDefinitionGroups`NIST 內建計畫定義中的屬性範例如下所示：

```json
"policyDefinitionGroups": [
    {
        "name": "NIST_SP_800-53_R4_AC-1",
        "additionalMetadataId": "/providers/Microsoft.PolicyInsights/policyMetadata/NIST_SP_800-53_R4_AC-1"
    }
]
```

### <a name="metadata-objects"></a>中繼資料物件

由 Microsoft 建立的法規合規性內建具有每個控制項的其他相關資訊。
此資訊為：

- 在 Azure 入口網站中顯示有關法規合規性計畫的**控制項**總覽。
- 可透過 REST API 取得。 請參閱 `Microsoft.PolicyInsights` 資源提供者和[policyMetadata 作業群組](/rest/api/policy-insights/policymetadata/getresource)。
- 可透過 Azure CLI 取得。 請參閱[az policy metadata](/cli/azure/policy/metadata?view=azure-cli-latest)命令。

> [!IMPORTANT]
> 法規合規性的中繼資料物件是_唯讀_的，而且不能由客戶建立。

原則群組的中繼資料在節點中具有下列資訊 `properties` ：

- `metadataId`：群組所關聯的**控制項 ID** 。
- `category`（必要）：**控制項**所屬的**合規性網域**。
- `title`（必要）：**控制項識別碼**的易記名稱。
- `owner`（必要）：識別誰負責 Azure 中的控制項：_客戶_、 _Microsoft_、_共用_。
- `description`：控制項的其他相關資訊。
- `requirements`：關於控制項執行責任的詳細資訊。
- `additionalContentUrl`：控制項的詳細資訊連結。 這個屬性通常是檔區段的連結，涵蓋合規性標準中的此控制項。

以下是**policyMetadata**物件的範例。 此範例中繼資料屬於_NIST SP 800-53 R4 AC-1_控制項。

```json
{
  "properties": {
    "metadataId": "NIST SP 800-53 R4 AC-1",
    "category": "Access Control",
    "title": "Access Control Policy and Procedures",
    "owner": "Shared",
    "description": "**The organization:**    \na. Develops, documents, and disseminates to [Assignment: organization-defined personnel or roles]:  \n1. An access control policy that addresses purpose, scope, roles, responsibilities, management commitment, coordination among organizational entities, and compliance; and  \n2. Procedures to facilitate the implementation of the access control policy and associated access controls; and  \n  
\nb. Reviews and updates the current:  \n1. Access control policy [Assignment: organization-defined frequency]; and  \n2. Access control procedures [Assignment: organization-defined frequency].",
    "requirements": "**a.**  The customer is responsible for developing, documenting, and disseminating access control policies and procedures. The customer access control policies and procedures address access to all customer-deployed resources and customer system access (e.g., access to customer-deployed virtual machines, access to customer-built applications).  \n**b.**  The customer is responsible for reviewing and updating access control policies and procedures in accordance with FedRAMP requirements.",
    "additionalContentUrl": "https://nvd.nist.gov/800-53/Rev4/control/AC-1"
  },
  "id": "/providers/Microsoft.PolicyInsights/policyMetadata/NIST_SP_800-53_R4_AC-1",
  "name": "NIST_SP_800-53_R4_AC-1",
  "type": "Microsoft.PolicyInsights/policyMetadata"
}
```

## <a name="next-steps"></a>後續步驟

- 查看[定義結構](./definition-structure.md)
- 在 [Azure 原則範例](../samples/index.md)檢閱範例。
- 檢閱[了解原則效果](effects.md)。
- 了解如何[以程式設計方式建立原則](../how-to/programmatically-create.md)。
- 了解如何[取得合規性資料](../how-to/get-compliance-data.md)。
- 了解如何[補救不符合規範的資源](../how-to/remediate-resources.md)。
- 透過[使用 Azure 管理群組來組織資源](../../management-groups/overview.md)來檢閱何謂管理群組。
