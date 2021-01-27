---
title: '將受控識別新增至 Service Fabric 受控叢集節點類型 (preview) '
description: 本文說明如何將受控識別新增至 Service Fabric 受控叢集節點類型
ms.topic: how-to
ms.date: 11/24/2020
ms.custom: references_regions
ms.openlocfilehash: 9edcf75451f43f2a00cd01d5ca7f385704b1ea7f
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878421"
---
# <a name="add-a-managed-identity-to-a-service-fabric-managed-cluster-node-type-preview"></a>將受控識別新增至 Service Fabric 受控叢集節點類型 (preview) 

Service Fabric 受控叢集中的每個節點類型都是由虛擬機器擴展集支援。 若要允許受控識別搭配受控叢集節點類型使用，則會將屬性 `vmManagedIdentity` 新增至節點類型定義，其中包含可能使用的身分識別清單 `userAssignedIdentities` 。 功能會反映受控識別如何在非受控叢集中使用，例如使用受控識別搭配 [Azure Key Vault 的虛擬機器擴展集擴充](../virtual-machines/extensions/key-vault-windows.md)功能。


如需在節點類型上使用受控識別的 Service Fabric 受控叢集部署範例，請參閱 [此範本](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-MI)。 如需支援的區域清單，請參閱 [受控叢集常見問題](./faq-managed-cluster.md#what-regions-are-supported-in-the-preview)。

> [!NOTE]
> 這項功能目前僅支援使用者指派的身分識別。

## <a name="prerequisites"></a>必要條件

開始之前：

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
* 如果您打算使用 PowerShell，請 [安裝](/cli/azure/install-azure-cli) Azure CLI 以執行 CLI 參考命令。

## <a name="create-a-user-assigned-managed-identity"></a>建立使用者指派的受控識別 

使用者指派的受控識別可以在 Azure Resource Manager (ARM) 範本的資源區段中定義，以便在部署時建立：

```JSON
{ 
    "type": "Microsoft.ManagedIdentity/userAssignedIdentities", 
    "name": "[parameters('userAssignedIdentityName')]", 
    "apiVersion": "2018-11-30", 
    "location": "[resourceGroup().location]"  
},
```

或透過 PowerShell 建立：

```powershell
az group create --name <resourceGroupName> --location <location>
az identity create --name <userAssignedIdentityName> --resource-group <resourceGroupName>
```

## <a name="add-a-role-assignment-with-service-fabric-resource-provider"></a>使用 Service Fabric 資源提供者新增角色指派

使用 Service Fabric 資源提供者應用程式，將角色指派新增至受控識別。 此指派可讓 Service Fabric 資源提供者將身分識別指派給受控叢集的虛擬機器擴展集。 

在適用的情況下，您必須使用下列值：

|名稱|對應 Service Fabric 資源提供者值|
|----|-------------------------------------|
|應用程式識別碼|74cb6831-0dbb-4be1-8206-fd4df301cdc2|
|物件識別碼|fbc587f2-66f5-4459-a027-bcd908b9d278|


|角色定義名稱|角色定義識別碼|
|----|-------------------------------------|
|受控身分識別操作員|f1a07417-d97a-45cb-824c-7a7467783830
|



您可以使用物件識別碼和角色定義識別碼，在 resources 區段中定義此角色指派：

```JSON
{
    "type": "Microsoft.Authorization/roleAssignments", 
    "apiVersion": "2020-04-01-preview",
    "name": "[parameters('vmIdentityRoleNameGuid')]",
    "scope": "[concat('Microsoft.ManagedIdentity/userAssignedIdentities', '/', parameters('userAssignedIdentityName'))]",
    "dependsOn": [ 
        "[concat('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
    ], 
    "properties": {
        "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'f1a07417-d97a-45cb-824c-7a7467783830')]",
        "principalId": "fbc587f2-66f5-4459-a027-bcd908b9d278" 
    } 
}, 
```

或使用應用程式識別碼和角色定義識別碼透過 PowerShell 建立：

```powershell
New-AzRoleAssignment -ApplicationId 74cb6831-0dbb-4be1-8206-fd4df301cdc2 -RoleDefinitionName "Managed Identity Operator" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<userAssignedIdentityName>"
```

或物件識別碼和角色定義識別碼：

```powershell
New-AzRoleAssignment -PrincipalId fbc587f2-66f5-4459-a027-bcd908b9d278 -RoleDefinitionName "Managed Identity Operator" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<userAssignedIdentityName>"
```

## <a name="add-managed-identity-properties-to-node-type-definition"></a>將受控識別屬性新增至節點類型定義

最後，將 `vmManagedIdentity` 和 `userAssignedIdentities` 屬性新增至受控叢集的節點類型定義：

```json

"properties": {
    "isPrimary" : true,
    "vmInstanceCount": 5,
    "dataDiskSizeGB": 100,
    "vmSize": "Standard_D2",
    "vmImagePublisher" : "MicrosoftWindowsServer",
    "vmImageOffer" : "WindowsServer",
    "vmImageSku" : "2019-Datacenter",
    "vmImageVersion" : "latest",
    "vmManagedIdentity": {
        "userAssignedIdentities": [
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('userAssignedIdentityName'))]"
        ]
    }
}
```

部署之後，會將建立的受控識別新增至指定的節點類型的虛擬機器擴展集，而且可以如預期般使用，就像在任何非受控叢集一樣。

## <a name="troubleshooting"></a>疑難排解

若無法正確新增角色指派，將會符合下列部署錯誤：

:::image type="content" source="media/how-to-managed-identity-managed-cluster-vmss/role-assignment-error.png" alt-text="Azure 入口網站部署錯誤，顯示具有 SFRP 之物件/應用程式識別碼的用戶端沒有執行身分識別管理活動的許可權":::

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [將應用程式部署到 Service Fabric 受控叢集](./tutorial-managed-cluster-deploy-app.md)