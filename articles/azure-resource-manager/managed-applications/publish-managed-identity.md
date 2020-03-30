---
title: 具有託管標識的託管應用
description: 使用託管標識配置託管應用程式，以便連結到現有資源、管理 Azure 資源以及為活動日誌提供操作標識。
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 05/13/2019
ms.openlocfilehash: dbf75262440474c5cb50a6d733ac7cba212b5f3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651653"
---
# <a name="azure-managed-application-with-managed-identity"></a>具有託管標識的 Azure 託管應用程式

> [!NOTE]
> 託管應用程式的託管標識支援當前處於預覽狀態。 請使用 2018-09-01 預覽 api 版本使用託管標識。

瞭解如何將託管應用程式佈建為包含託管標識。 託管標識可用於允許客戶授予託管應用程式對其他現有資源的存取權限。 身分識別由 Azure 平台負責管理，因此您不需要佈建或輪替任何密碼。 有關 Azure 活動目錄 （AAD） 中的託管標識，請參閱[Azure 資源的託管標識](../../active-directory/managed-identities-azure-resources/overview.md)。

您的應用程式可以授與兩種類型的身分識別：

- **系統指派的身分識別**會繫結至您的應用程式，如果您的應用程式已刪除，則會被刪除。 應用程式只能有一個系統指派的身分識別。
- **使用者分配的標識**是可分配給應用的獨立 Azure 資源。 應用程式可以有多個使用者指派的身分識別。

## <a name="how-to-use-managed-identity"></a>如何使用託管標識

託管標識支援託管應用程式的許多方案。 一些可以解決的常見方案包括：

- 部署連結到現有 Azure 資源的託管應用程式。 例如，在連接到[現有網路介面](../../virtual-network/virtual-network-network-interface-vm.md)的託管應用程式中部署 Azure 虛擬機器 （VM）。
- 授予託管應用程式和發行者對**託管資源組**外部 Azure 資源的訪問。
- 為 Azure 中的活動日誌和其他服務提供託管應用程式的操作標識。

## <a name="adding-managed-identity"></a>添加託管標識

使用託管標識創建託管應用程式需要在 Azure 資源上設置其他屬性。 下面的示例顯示了一個示例**標識**屬性：

```json
{
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity": {}
    }
}
```

創建具有**標識**的託管應用程式有兩種常見方法：[創建 UIDefinition.json](./create-uidefinition-overview.md)和[Azure 資源管理器範本](../templates/template-syntax.md)。 對於簡單的單個創建方案，應使用 CreateUI定義啟用託管標識，因為它提供了更豐富的體驗。 但是，在處理需要自動化或多個託管應用程式部署的高級或複雜系統時，可以使用範本。

### <a name="using-createuidefinition"></a>使用 CreateUI 定義

可以通過[CreateUI定義.json](./create-uidefinition-overview.md)使用託管標識配置託管應用程式。 在[輸出部分](./create-uidefinition-overview.md#outputs)中，該鍵`managedIdentity`可用於重寫託管應用程式範本的識別屬性。 示例波紋管將在託管應用程式上啟用**系統分配的**標識。 通過使用 CreateUI定義元素向消費者請求輸入，可以形成更複雜的標識物件。 這些輸入可用於使用**使用者分配的標識**構造託管應用程式。

```json
"outputs": {
    "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
}
```

#### <a name="when-to-use-createuidefinition-for-managed-identity"></a>何時對託管標識使用 CreateUI 定義

以下是有關何時使用 CreateUI 定義在託管應用程式上啟用託管標識的一些建議。

- 託管應用程式創建通過 Azure 門戶或應用商店。
- 託管標識需要複雜的消費者輸入。
- 創建託管應用程式時需要託管標識。

#### <a name="systemassigned-createuidefinition"></a>系統分配創建 UI 定義

支援託管應用程式的系統分配標識的基本 CreateUI 定義。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
        ],
        "outputs": {
            "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
        }
    }
}
```

#### <a name="userassigned-createuidefinition"></a>使用者分配創建 UI 定義

一個基本的 CreateUI 定義，它以**使用者分配的標識**資源作為輸入，並啟用託管應用程式的使用者分配標識。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "manageIdentity",
                "label": "Identity",
                "subLabel": {
                    "preValidation": "Manage Identities",
                    "postValidation": "Done"
                },
                "bladeTitle": "Identity",
                "elements": [
                    {
                        "name": "userAssignedText",
                        "type": "Microsoft.Common.TextBox",
                        "label": "User assigned managed identity",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('manageIdentity').userAssignedText),':{}}}'))]"
        }
    }
}
```

上面的 CreateUIDefinition.json 生成一個創建使用者體驗，該使用者體驗具有一個文字方塊，供消費者輸入**使用者分配的身份**Azure 資源識別碼。 生成的體驗如下所示：

![示例使用者分配標識 創建 UI 定義](./media/publish-managed-identity/user-assigned-identity.png)

### <a name="using-azure-resource-manager-templates"></a>使用 Azure 資源管理員範本

> [!NOTE]
> 市場託管應用程式範本會自動生成，供通過 Azure 門戶創建體驗的客戶使用。
> 對於這些方案，`managedIdentity`必須使用 CreateUI 定義上的輸出金鑰來啟用標識。

還可以通過 Azure 資源管理器範本啟用託管標識。 示例波紋管將在託管應用程式上啟用**系統分配的**標識。 通過使用 Azure 資源管理器範本參數提供輸入，可以形成更複雜的標識物件。 這些輸入可用於使用**使用者分配的標識**構造託管應用程式。

#### <a name="when-to-use-azure-resource-manager-templates-for-managed-identity"></a>何時使用 Azure 資源管理器範本進行託管標識

以下是有關何時使用 Azure 資源管理器範本在託管應用程式上啟用託管標識的一些建議。

- 可以基於範本以程式設計方式部署託管應用程式。
- 需要託管標識的自訂角色指派來預配託管應用程式。
- 託管應用程式不需要 Azure 門戶和市場創建流。

#### <a name="systemassigned-template"></a>系統分配範本

部署具有**系統分配標識的**託管應用程式的基本 Azure 資源管理器範本。

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

### <a name="userassigned-template"></a>使用者分配範本

使用**使用者分配的標識**部署託管應用程式的基本 Azure 資源管理器範本。

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

## <a name="granting-access-to-azure-resources"></a>授予對 Azure 資源的許可權

授予託管應用程式標識後，就可以授予它對現有 Azure 資源的訪問。 此過程可以通過 Azure 門戶中的存取控制 （IAM） 介面完成。 可以搜索託管應用程式或**使用者分配標識**的名稱以添加角色指派。

![為託管應用程式添加角色指派](./media/publish-managed-identity/identity-role-assignment.png)

## <a name="linking-existing-azure-resources"></a>連結現有的 Azure 資源

> [!NOTE]
> 在部署託管應用程式之前，必須[配置](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)**使用者分配的標識**。 此外，託管應用程式的連結資源部署僅支援**市場**類型。

託管標識還可用於部署託管應用程式，該應用程式需要在部署期間訪問現有資源。 當客戶預配託管應用程式時，可以添加**使用者分配的身份**，以向**主範本**部署提供其他授權。

### <a name="authoring-the-createuidefinition-with-a-linked-resource"></a>使用連結資源創作 CreateUI 定義

將託管應用程式的部署連結到現有資源時，必須提供現有 Azure 資源和**使用者分配的標識**以及該資源上的適用角色指派。

 需要兩個輸入的示例 CreateUI 定義：網路介面資源識別碼 和使用者分配的標識資源識別碼。

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

此 CreateUI 定義.json 生成具有兩個欄位的創建使用者體驗。 第一個欄位允許使用者在 Azure 資源識別碼 中輸入連結到託管應用程式部署的資源。 第二個是消費者輸入**使用者分配的身份**Azure 資源識別碼，該標識有權訪問連結的 Azure 資源。 生成的體驗如下所示：

![示例創建具有兩個輸入的 UI 定義：網路介面資源識別碼 和使用者分配的標識資源識別碼](./media/publish-managed-identity/network-interface-cuid.png)

### <a name="authoring-the-maintemplate-with-a-linked-resource"></a>使用連結資源創作主範本

除了更新 CreateUI 定義之外，還需要更新主範本以接受連結資源識別碼 中傳遞的範本。 可以通過添加新參數來更新主範本以接受新輸出。 由於`managedIdentity`輸出覆蓋生成的託管應用程式範本上的值，因此不會將其傳遞給主範本，並且不應包含在參數部分中。

將網路設定檔設置到 CreateUI 定義提供的現有網路介面的示例主範本。

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

### <a name="consuming-the-managed-application-with-a-linked-resource"></a>使用連結的資源使用託管應用程式

創建託管應用程式包後，可通過 Azure 門戶使用託管應用程式。 在使用它之前，有幾個先決條件步驟。

- 必須創建所需的連結 Azure 資源的實例。
- 必須創建**使用者分配的標識**[，並為其連結的資源配置角色](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。
- 現有的連結資源識別碼**和使用者分配的標識**ID 提供給 CreateUI 定義。

## <a name="accessing-the-managed-identity-token"></a>訪問託管標識權杖

託管應用程式的權杖現在可以通過發行者租戶的`listTokens`api 訪問。 示例請求可能如下所示：

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}/listTokens?api-version=2018-09-01-preview HTTP/1.1

{
    "authorizationAudience": "https://management.azure.com/",
    "userAssignedIdentities": [
        "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userAssignedIdentityName}"
    ]
}
```

請求正文參數：

參數 | 必要 | 描述
---|---|---
授權 | *不* | 目標資源的應用 ID URI。 它也是已頒發的`aud`權杖的（受眾）聲明。 預設值為""https://management.azure.com/
userAssignedIdentities | *不* | 要為其檢索權杖的使用者分配的託管標識的清單。 如果未指定，`listTokens`將返回系統分配的託管標識的權杖。


示例回應可能類似于：

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

回應將在`value`屬性下包含權杖陣列：

參數 | 描述
---|---
access_token | 所要求的存取權杖。
expires_in | 訪問權杖有效的秒數。
expires_on | 存取權杖到期的時間範圍。 這表示為來自紀元的秒數。
not_before | 訪問權杖生效的時間跨度。 這表示為來自紀元的秒數。
授權 | 請求`aud`訪問權杖的（訪問物件）。 這與`listTokens`請求中提供的內容相同。
resourceId | 已頒發權杖的 Azure 資源識別碼。 這是託管應用程式 ID 或使用者分配的標識 ID。
token_type | 語彙基元的類型。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [如何使用自訂提供程式配置託管應用程式](../custom-providers/overview.md)
