---
title: 受控應用程式與受控識別
description: 使用受控識別來設定受控應用程式，以連結至現有的資源、管理 Azure 資源，以及為活動記錄提供操作身分識別。
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 05/13/2019
ms.openlocfilehash: 277faa2d47df9fddd1762d90d9aa2fb5bf00d4df
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82508119"
---
# <a name="azure-managed-application-with-managed-identity"></a>具有受控識別的 Azure 受控應用程式

> [!NOTE]
> 受控應用程式的受控識別支援目前為預覽狀態。 請使用 2018-09-01-preview api 版本來利用受控識別。

瞭解如何設定受控應用程式以包含受控識別。 受控識別可用來允許客戶將其他現有資源的存取權授與受控應用程式。 身分識別由 Azure 平台負責管理，因此您不需要佈建或輪替任何密碼。 如需 Azure Active Directory （AAD）中受控識別的詳細資訊，請參閱[適用于 Azure 資源的受控](../../active-directory/managed-identities-azure-resources/overview.md)識別。

您的應用程式可以授與兩種類型的身分識別：

- **系統指派的身分識別**會繫結至您的應用程式，如果您的應用程式已刪除，則會被刪除。 應用程式只能有一個系統指派的身分識別。
- **使用者指派的身分識別**是一項獨立 Azure 資源，可指派給您的應用程式。 應用程式可以有多個使用者指派的身分識別。

## <a name="how-to-use-managed-identity"></a>如何使用受控識別

受控識別可為受控應用程式提供許多案例。 可以解決的一些常見案例包括：

- 部署連結至現有 Azure 資源的受控應用程式。 例如，在連接到[現有網路介面](../../virtual-network/virtual-network-network-interface-vm.md)的受控應用程式中部署 Azure 虛擬機器（VM）。
- 授與受控應用程式和發行者對**受控資源群組**外部 Azure 資源的存取權。
- 針對活動記錄和 Azure 中的其他服務，提供受控應用程式的操作身分識別。

## <a name="adding-managed-identity"></a>新增受控識別

若要使用受控識別建立受控應用程式，需要在 Azure 資源上設定額外的屬性。 下列範例顯示範例**標識**屬性：

```json
{
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity": {}
    }
}
```

有兩種常見的方式可使用身分**識別**來建立受控應用程式： [CreateUIDefinition.js](./create-uidefinition-overview.md) ，以及[Azure Resource Manager 範本](../templates/template-syntax.md)。 針對簡單的單一建立案例，應該使用 CreateUIDefinition 來啟用受控識別，因為它提供更豐富的體驗。 不過，在處理需要自動化或多個受控應用程式部署的先進或複雜系統時，可以使用範本。

### <a name="using-createuidefinition"></a>使用 CreateUIDefinition

受控應用程式可以透過[上的CreateUIDefinition.js](./create-uidefinition-overview.md)來設定受控識別。 在 [[輸出] 區段](./create-uidefinition-overview.md#outputs)中，索引鍵 `managedIdentity` 可以用來覆寫受控應用程式範本的 identity 屬性。 範例鈴會在受控應用程式上啟用**系統指派**的身分識別。 您可以使用 CreateUIDefinition 元素來要求取用者輸入，以形成更複雜的身分識別物件。 這些輸入可用於以**使用者指派**的身分識別來建立受控應用程式。

```json
"outputs": {
    "managedIdentity": { "Type": "SystemAssigned" }
}
```

#### <a name="when-to-use-createuidefinition-for-managed-identity"></a>針對受控識別使用 CreateUIDefinition 的時機

以下是使用 CreateUIDefinition 在受控應用程式上啟用受控識別時的一些建議。

- 受控應用程式的建立會經過 Azure 入口網站或 marketplace。
- 受控識別需要複雜的取用者輸入。
- 建立受控應用程式時，需要受控識別。

#### <a name="managed-identity-createuidefinition-control"></a>受控識別 CreateUIDefinition 控制

CreateUIDefinition 支援內建的[受控識別控制項](./microsoft-managedidentity-identityselector.md)。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.0.1-preview",
  "parameters": {
    "basics": [],
    "steps": [
      {
        "name": "applicationSettings",
        "label": "Application Settings",
        "subLabel": {
          "preValidation": "Configure your application settings",
          "postValidation": "Done"
        },
        "bladeTitle": "Application Settings",
        "elements": [
          {
            "name": "appName",
            "type": "Microsoft.Common.TextBox",
            "label": "Managed application Name",
            "toolTip": "Managed application instance name",
            "visible": true
          },
          {
            "name": "appIdentity",
            "type": "Microsoft.ManagedIdentity.IdentitySelector",
            "label": "Managed Identity Configuration",
            "toolTip": {
              "systemAssignedIdentity": "Enable system assigned identity to grant the managed application access to additional existing resources.",
              "userAssignedIdentity": "Add user assigned identities to grant the managed application access to additional existing resources."
            },
            "defaultValue": {
              "systemAssignedIdentity": "Off"
            },
            "options": {
              "hideSystemAssignedIdentity": false,
              "hideUserAssignedIdentity": false,
              "readOnlySystemAssignedIdentity": false
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "applicationResourceName": "[steps('applicationSettings').appName]",
      "location": "[location()]",
      "managedIdentity": "[steps('applicationSettings').appIdentity]"
    }
  }
}
```

![受控識別 CreateUIDefinition](./media/publish-managed-identity/msi-cuid.png)

### <a name="using-azure-resource-manager-templates"></a>使用 Azure 資源管理員範本

> [!NOTE]
> Marketplace 受控應用程式範本會自動產生，供客戶通過 Azure 入口網站建立體驗。
> 在這些情況下， `managedIdentity` CreateUIDefinition 上的輸出索引鍵必須用來啟用身分識別。

受控識別也可以透過 Azure Resource Manager 範本來啟用。 範例鈴會在受控應用程式上啟用**系統指派**的身分識別。 使用 Azure Resource Manager 樣板參數來提供輸入，就可以形成更複雜的識別物件。 這些輸入可用於以**使用者指派**的身分識別來建立受控應用程式。

#### <a name="when-to-use-azure-resource-manager-templates-for-managed-identity"></a>使用受控識別 Azure Resource Manager 範本的時機

以下是有關何時使用 Azure Resource Manager 範本在受控應用程式上啟用受控識別的一些建議。

- 受控應用程式可以根據範本以程式設計方式部署。
- 需要受控識別的自訂角色指派，才能布建受控應用程式。
- 受控應用程式不需要 Azure 入口網站和 marketplace 建立流程。

#### <a name="systemassigned-template"></a>SystemAssigned 範本

基本 Azure Resource Manager 範本，可使用**系統指派**的身分識別來部署受控應用程式。

```json
"resources": [
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

### <a name="userassigned-template"></a>UserAssigned 範本

基本 Azure Resource Manager 範本，可使用**使用者指派**的身分識別來部署受控應用程式。

```json
"resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('managedIdentityName')]",
      "apiVersion": "2018-11-30",
      "location": "[parameters('location')]"
    },
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('managedIdentityName'))]": {}
            }
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

## <a name="granting-access-to-azure-resources"></a>授與 Azure 資源的存取權

一旦受控應用程式被授與身分識別，就可以授與現有 Azure 資源的存取權。 此程式可透過 Azure 入口網站中的存取控制（IAM）介面來完成。 您可以搜尋受控應用程式的名稱或**使用者指派**的身分識別，以新增角色指派。

![新增受控應用程式的角色指派](./media/publish-managed-identity/identity-role-assignment.png)

## <a name="linking-existing-azure-resources"></a>連結現有的 Azure 資源

> [!NOTE]
> 在部署受控應用程式之前，必須先[設定](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)**使用者指派**的身分識別。 此外，只有**marketplace**類型支援受控應用程式的連結資源部署。

受控識別也可以用來部署需要在其部署期間存取現有資源的受控應用程式。 當客戶布建受控應用程式時，可以新增**使用者指派的**身分識別，以提供額外的授權給**mainTemplate**部署。

### <a name="authoring-the-createuidefinition-with-a-linked-resource"></a>使用連結的資源撰寫 CreateUIDefinition

將受控應用程式的部署連結至現有資源時，必須提供現有的 Azure 資源和**使用者指派**的身分識別，以及該資源上適用的角色指派。

 需要兩個輸入的範例 CreateUIDefinition：網路介面資源識別碼和使用者指派的身分識別資源識別碼。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "managedApplicationSetting",
                "label": "Managed Application Settings",
                "subLabel": {
                    "preValidation": "Managed Application Settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Managed Application Settings",
                "elements": [
                    {
                        "name": "networkInterfaceId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "network interface resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Network/networkInterfaces/existingnetworkinterface",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.Network/networkInterfaces/networkinterface1",
                        "visible": true
                    },
                    {
                        "name": "userAssignedId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "user assigned identity resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/identity1",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "existingNetworkInterfaceId": "[steps('managedApplicationSetting').networkInterfaceId]",
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('managedApplicationSetting').userAssignedId),':{}}}'))]"
        }
    }
}
```

此 CreateUIDefinition.js的會產生具有兩個欄位的建立使用者體驗。 第一個欄位可讓使用者輸入要連結至受控應用程式部署之資源的 Azure 資源識別碼。 第二個是讓取用者輸入**使用者指派**的身分識別 AZURE 資源識別碼，其可存取已連結的 azure 資源。 產生的體驗看起來會像這樣：

![具有兩個輸入的範例 CreateUIDefinition：網路介面資源識別碼和使用者指派的身分識別資源識別碼](./media/publish-managed-identity/network-interface-cuid.png)

### <a name="authoring-the-maintemplate-with-a-linked-resource"></a>使用連結的資源撰寫 mainTemplate

除了更新 CreateUIDefinition，主要範本也需要更新，以接受已連結資源識別碼中傳遞的。 您可以藉由新增新的參數，更新主要範本以接受新的輸出。 因為 `managedIdentity` 輸出會覆寫所產生 Managed 應用程式範本上的值，所以它不會傳遞至主要範本，而且不應該包含在 parameters 區段中。

範例主要範本，會將網路設定檔設定為 CreateUIDefinition 所提供的現有網路介面。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "existingNetworkInterfaceId": { "type": "string" }
    },
    "variables": {
    },
    "resources": [
        {
            "apiVersion": "2016-04-30-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "myLinkedResourceVM",
            "location": "[resourceGroup().location]",
            "properties": {
                …,
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[parameters('existingNetworkInterfaceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

### <a name="consuming-the-managed-application-with-a-linked-resource"></a>使用已連結資源的受控應用程式

一旦建立受控應用程式封裝，就可以透過 Azure 入口網站取用受控應用程式。 在可以使用之前，有幾個必要條件步驟。

- 必須建立所需連結 Azure 資源的實例。
- 必須建立**使用者指派**的身分識別[，並指定](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)對連結資源的角色指派。
- 現有的連結資源識別碼和**使用者指派**的身分識別識別碼會提供給 CreateUIDefinition。

## <a name="accessing-the-managed-identity-token"></a>存取受控識別權杖

受控應用程式的權杖現在可以透過 `listTokens` 來自發行者租使用者的 api 來存取。 範例要求可能如下所示：

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}/listTokens?api-version=2018-09-01-preview HTTP/1.1

{
    "authorizationAudience": "https://management.azure.com/",
    "userAssignedIdentities": [
        "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userAssignedIdentityName}"
    ]
}
```

要求主體參數：

參數 | 必要 | 描述
---|---|---
authorizationAudience | *不* | 目標資源的應用程式識別碼 URI。 它也是已 `aud` 發行權杖的（物件）宣告。 預設值為 " https://management.azure.com/ "
userAssignedIdentities | *不* | 要為其取得權杖的使用者指派受控識別清單。 如果未指定， `listTokens` 將會傳回系統指派之受控識別的權杖。


範例回應可能如下所示：

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json

{
    "value": [
        {
            "access_token": "eyJ0eXAi…",
            "expires_in": "2…",
            "expires_on": "1557…",
            "not_before": "1557…",
            "authorizationAudience": "https://management.azure.com/",
            "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}",
            "token_type": "Bearer"
        }
    ]
}
```

回應會包含屬性下的權杖陣列 `value` ：

參數 | 描述
---|---
access_token | 所要求的存取權杖。
expires_in | 存取權杖生效的秒數。
expires_on | 存取權杖到期的時間範圍。 這會表示為 epoch 的秒數。
not_before | 存取權杖生效時的 timespan。 這會表示為 epoch 的秒數。
authorizationAudience | `aud`存取權杖要求的（物件）。 這與要求中所提供的相同 `listTokens` 。
resourceId | 已發行權杖的 Azure 資源識別碼。 這可能是受控應用程式識別碼或使用者指派的身分識別識別碼。
token_type | 語彙基元的類型。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [如何使用自訂提供者來設定受控應用程式](../custom-providers/overview.md)
