---
title: 使用 Azure PowerShell 將 Azure 虛擬網路移動到其他 Azure 區域
description: 通過使用資源管理器範本和 Azure PowerShell 將 Azure 虛擬網路從一個 Azure 區域移動到另一個 Azure 區域。
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: dc316e5bbb88359ff8b1e8a4fc35a56541a577f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646705"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-azure-powershell"></a>使用 Azure PowerShell 將 Azure 虛擬網路移動到其他區域

存在將現有 Azure 虛擬網路從一個區域移動到另一個區域的各種方案。 例如，您可能希望創建配置與現有虛擬網路相同的配置以進行測試和可用性的虛擬網路。 或者，您可能希望將生產虛擬網路移動到其他區域，作為災害復原規劃的一部分。

可以使用 Azure 資源管理器範本完成虛擬網路移動到其他區域。 為此，可以將虛擬網路匯出到範本，修改參數以匹配目的地區域，然後將範本部署到新區域。 有關資源管理器範本的詳細資訊，請參閱[將資源組匯出到範本](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)。


## <a name="prerequisites"></a>Prerequisites

- 確保虛擬網路位於要移動的 Azure 區域中。

- 要匯出虛擬網路並部署範本以在另一個區域中創建虛擬網路，需要具有"網路參與者"角色或更高角色。

- 虛擬網路對等互連不會重新創建，如果範本中仍然存在，它們就會失敗。 在匯出範本之前，必須刪除任何虛擬網路對等體。 然後，您可以在虛擬網路移動後重新建立它們。
    
- 識別來源網路配置，以及您目前使用的所有資源。 此佈局包括但不限於負載等化器、網路安全性群組 （NSG） 和公共 IP。

- 驗證 Azure 訂閱是否允許在目的地區域中創建虛擬網路。 要啟用所需的配額，請聯繫支援人員。

- 請確保您的訂閱有足夠的資源來支援此進程的虛擬網路添加。 如需詳細資訊，請參閱 [Azure 訂用帳戶和服務限制、配額與條件約束](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)。


## <a name="prepare-for-the-move"></a>準備移動
在本節中，您可以使用資源管理器範本為移動準備虛擬網路。 然後，使用 Azure PowerShell 命令將虛擬網路移動到目的地區域。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

要匯出虛擬網路並使用 PowerShell 部署目標虛擬網路，請使用以下操作：

1. 使用[Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)命令登錄到 Azure 訂閱，然後按照螢幕上的說明操作：
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. 獲取要移動到目的地區域的虛擬網路的資源識別碼，然後使用[Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)將其放入變數中：

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id
    ```

1. 將源虛擬網路匯出到執行命令[Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)的目錄中的 .json 檔：
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

1. 下載的檔的名稱與從中匯出的資源組相同。 找到使用 命令匯出*\<的資源組名稱>.json*檔，然後在編輯器中打開該檔：
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

1. 要編輯虛擬網路名稱的參數，請將源虛擬網路名稱的**預設值**屬性更改為目標虛擬網路的名稱。 請務必用引號括上名稱。
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

1. 要編輯將移動虛擬網路的目的地區域，請更改資源下**的位置**屬性：

    ```json
    "resources": [
                {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region>",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                        "addressSpace": {
                            "addressPrefixes": [
                                "10.0.0.0/16"
                            ]
                        },

    ```
  
1. 要獲取區域位置代碼，可以使用 Azure PowerShell Cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0)運行以下命令：

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    ```

1. （可選）您還可以根據您的要求更改*\<資源組名稱>.json*檔中的其他參數：

    * **位址空間**：在保存檔之前，可以通過修改**資源** > **位址空間**部分並更改**位址首碼**屬性來更改虛擬網路的位址空間：

        ```json
                "resources": [
                    {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                    "addressSpace": {
                        "addressPrefixes": [
                        "10.0.0.0/16"
                        ]
                    },
        ```

    * **子網**：您可以通過更改檔的**子網**部分來更改或添加到子網名稱和子網位址空間。 您可以通過更改名稱屬性來更改子網**的名稱**。 還可以通過更改**位址首碼**屬性來更改子網位址空間：

        ```json
                "subnets": [
                    {
                    "name": "subnet-1",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.0.0/24",
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    },
                    {
                    "name": "GatewaySubnet",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.1.0/29",
                    "serviceEndpoints": [],
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    }

                ]
        ```

        要更改位址首碼，請在兩個位置編輯檔：在前面一節中的代碼和以下代碼**的類型**部分中。 更改以下代碼中的**位址首碼**屬性，以匹配上一節中代碼中的**位址首碼**屬性。

        ```json
         "type": "Microsoft.Network/virtualNetworks/subnets",
           "apiVersion": "2019-06-01",
           "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/GatewaySubnet')]",
              "dependsOn": [
                 "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                   ],
              "properties": {
                 "provisioningState": "Succeeded",
                 "addressPrefix": "10.0.1.0/29",
                 "serviceEndpoints": [],
                 "delegations": [],
                 "privateEndpointNetworkPolicies": "Enabled",
                 "privateLinkServiceNetworkPolicies": "Enabled"
                  }
                 },
                  {
                  "type": "Microsoft.Network/virtualNetworks/subnets",
                  "apiVersion": "2019-06-01",
                  "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/subnet-1')]",
                     "dependsOn": [
                        "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                          ],
                     "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "10.0.0.0/24",
                        "delegations": [],
                        "privateEndpointNetworkPolicies": "Enabled",
                        "privateLinkServiceNetworkPolicies": "Enabled"
                         }
                  }
         ]
        ```

1. 保存*\<資源組名稱>.json*檔。

1. 在目的地區域中創建資源組，以便使用[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)進行部署的目標虛擬網路 ：
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
1. 使用[New-AzResourceGroup 部署](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)將編輯*\<的資源組名稱>.json*檔部署到您在上一步中創建的資源組：

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    ```

1. 要驗證資源是否是在目的地區域中創建的，請使用[Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0)和[Get-Az虛擬網路](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)：
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>
    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>
    ```

## <a name="delete-the-virtual-network-or-resource-group"></a>刪除虛擬網路或資源組 

部署虛擬網路後，要重新開始或放棄目的地區域中的虛擬網路，請刪除在目的地區域中創建的資源組，移動的虛擬網路將被刪除。 

要刪除資源組，請使用[刪除 AzResource 組](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)：

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>
```

## <a name="clean-up"></a>清除

要提交更改並完成虛擬網路移動，請執行以下任一操作：

* 使用[刪除-AzResource組](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)刪除資源組 ：

    ```azurepowershell-interactive

    Remove-AzResourceGroup -Name <source-resource-group-name>
    ```

* 使用[刪除-Az虛擬網路刪除源虛擬網路](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0)：  
    ``` azurepowershell-interactive

    Remove-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>
    ```

## <a name="next-steps"></a>後續步驟

在本教程中，您可以使用 PowerShell 將虛擬網路從一個區域移動到另一個區域，然後清理不需要的源資源。 要瞭解有關在 Azure 中在區域和災害復原之間移動資源的更多資訊，請參閱：

- [將資源移至新的資源群組或訂用帳戶](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [將 Azure 虛擬機器移動到其他區域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
