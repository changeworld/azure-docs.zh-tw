---
title: 使用 Azure PowerShell 將 Azure 內部 Load Balancer 移至另一個 Azure 區域
description: 使用 Azure Resource Manager 範本，使用 Azure PowerShell 將 Azure 內部 Load Balancer 從一個 Azure 區域移至另一個區域
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: f8e431124155fe23853fe61e985fe4db522c3f77
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75644268"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-powershell"></a>使用 PowerShell 將 Azure 內部 Load Balancer 移至另一個區域

在許多情況下，您會想要將現有的內部負載平衡器從一個區域移至另一個區域。 例如，您可能會想要使用相同的設定來建立內部負載平衡器以進行測試。 您也可以將內部負載平衡器移至另一個區域，做為嚴重損壞修復計畫的一部分。

Azure 內部負載平衡器無法從一個區域移至另一個區域。 不過，您可以使用 Azure Resource Manager 範本來匯出內部負載平衡器的現有設定和虛擬網路。  接著，您可以將負載平衡器和虛擬網路匯出至範本、修改參數以符合目的地區域，然後將範本部署到新的區域，藉此將資源放在另一個區域中。  如需 Resource Manager 和範本的詳細資訊，請參閱[將資源群組匯出至範本](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Prerequisites

- 請確定 Azure 內部負載平衡器位於您要移動的 Azure 區域中。

- Azure 內部負載平衡器無法在區域之間移動。  您必須將新的負載平衡器關聯至目的地區域中的資源。

- 若要匯出內部負載平衡器設定並部署範本，以在另一個區域中建立內部負載平衡器，您需要網路參與者角色或更高版本。
   
- 識別來源網路配置，以及您目前使用的所有資源。 此配置包括但不限於負載平衡器、網路安全性群組、虛擬機器和虛擬網路。

- 確認您的 Azure 訂用帳戶可讓您在所使用的目的地區域中建立內部負載平衡器。 請連絡支援人員啟用所需的配額。

- 請確定您的訂用帳戶有足夠的資源可支援新增此程式的負載平衡器。  請參閱[Azure 訂用帳戶和服務限制、配額和條件約束](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>準備和移動
下列步驟示範如何使用 Resource Manager 範本來準備用於移動的內部負載平衡器，以及使用 Azure PowerShell 將內部負載平衡器設定移至目的地區域。  做為此程式的一部分，必須包含內部負載平衡器的虛擬網路設定，而且必須先完成，才能移動內部負載平衡器。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-azure-powershell"></a>匯出虛擬網路範本，並從 Azure PowerShell 部署

1. 使用[disconnect-azaccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)命令登入您的 Azure 訂用帳戶，並遵循畫面上的指示：
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2.  取得您想要移至目的地區域之虛擬網路的資源識別碼，並使用[new-azvirtualnetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)將它放在變數中：

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. 將來源虛擬網路匯出至 json 檔案，以在您執行命令[匯出-remove-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)的目錄中：
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. 下載的檔案將會以匯出資源的來源資源群組命名。  找出從名為** \<[資源-群組-名稱>** ] 的命令匯出的檔案，並在您選擇的編輯器中開啟它：
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. 若要編輯虛擬網路名稱的參數，請將來源虛擬網路名稱的屬性**defaultValue**變更為目標虛擬網路的名稱，並確定名稱是以引號括住：
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

6.  若要編輯將移動 VNET 的目的地區域，請變更 [資源] 底下的 [**位置**] 屬性：

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
  
7. 若要取得區域位置代碼，您可以藉由執行下列命令來使用 Azure PowerShell Cmdlet [get-azlocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) ：

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8.  如果您選擇，您也可以變更** \<資源群組名稱**中的其他參數> json 檔案，而且是選擇性的，視您的需求而定：

    * **位址空間**-VNET 的位址空間可以藉由修改**resources** > **addressSpace**一節來改變，然後變更** \<資源群組名稱>. json**檔案中的**addressPrefixes**屬性：

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

    * **子**網-您可以修改** \<資源群組名稱>. json**檔案的**子**網區段，來變更或新增子網名稱和子網位址空間。 您可以藉由變更**name**屬性來變更子網的名稱。 藉由改變** \<資源群組名稱>. json**檔案中的**addressPrefix**屬性，即可變更子網位址空間：

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

         在** \<資源群組名稱> json**檔案中，若要變更位址首碼，必須在兩個位置中進行編輯，如上所列的區段和下面所列的**類型**一節。  變更**addressPrefix**屬性，使其符合上述各項：

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

9.  將** \<資源群組名稱儲存> json**檔案。

10. 在目的地區域中建立資源群組，以使用[remove-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)部署目標 VNET
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. 使用[new-azresourcegroupdeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)，將已編輯** \<的資源群組名稱>. json**檔案部署到在上一個步驟中建立的資源群組：

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```
12. 若要確認已在目的地區域中建立資源，請使用[remove-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0)和[new-azvirtualnetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)：
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>

    ```
### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>匯出內部負載平衡器範本，並從 Azure PowerShell 部署

1. 使用[disconnect-azaccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)命令登入您的 Azure 訂用帳戶，並遵循畫面上的指示：
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. 取得您想要移至目的地區域的內部負載平衡器資源識別碼，並使用[remove-azloadbalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0)將它放在變數中：

    ```azurepowershell-interactive
    $sourceIntLBID = (Get-AzLoadBalancer -Name <source-internal-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. 將來源內部負載平衡器設定匯出至 json 檔案，以在您執行命令[匯出-remove-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)的目錄中：
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceIntLBID -IncludeParameterDefaultValue
   ```
4. 下載的檔案將會以匯出資源的來源資源群組命名。  找出從名為** \<[資源-群組-名稱>** ] 的命令匯出的檔案，並在您選擇的編輯器中開啟它：
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. 若要編輯內部負載平衡器名稱的參數，請將來源內部負載平衡器名稱的屬性**defaultValue**變更為目標內部負載平衡器的名稱，並確定名稱是以引號括住：

    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_externalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```
 
6. 若要編輯上方移動之目標虛擬網路的值，您必須先取得資源識別碼，然後將它複製並貼到** \<資源群組名稱>. json**檔案中。  若要取得識別碼，請使用[new-azvirtualnetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)：
   
   ```azurepowershell-interactive
    $targetVNETID = (Get-AzVirtualNetwork -Name <target-vnet-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    輸入變數，然後按 enter 鍵以顯示資源識別碼。  反白顯示識別碼路徑，並將它複製到剪貼簿：

    ```powershell
    PS C:\> $targetVNETID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupVNET-Move/providers/Microsoft.Network/virtualNetworks/myVNET2-Move
    ```

7.  在** \<資源群組名稱> json**檔案中，貼上變數中的**資源識別碼**，以取代目標虛擬網路識別碼的第二個參數中的**defaultValue** ，請務必以引號括住路徑：
   
    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_externalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```

8. 若要編輯將移動內部負載平衡器設定的目的地區域，請變更** \<資源群組名稱> json**檔案中 [**資源**] 下的 [**位置**] 屬性：

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-internal-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

11. 若要取得區域位置代碼，您可以藉由執行下列命令來使用 Azure PowerShell Cmdlet [get-azlocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) ：

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
12. 您也可以根據您的需求，變更範本中的其他參數，而且是選擇性的：
    
    * **Sku** -您可以藉由變更** \<資源群組名稱>. json**檔案中的**sku** > **名稱**屬性，將設定中的內部負載平衡器的 sku 從 [標準] 變更為 [基本] 或 [基本] 設定為 [標準]：

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-internal-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      如需基本和標準 sku 負載平衡器之間差異的詳細資訊，請參閱[Azure Standard Load Balancer 總覽](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)

    * **負載平衡規則**-您可以在設定中新增或移除專案，方法是新增或移除** \<資源群組名稱> json**檔案的**loadBalancingRules**區段中的負載平衡規則：

        ```json
        "loadBalancingRules": [
                    {
                        "name": "myInboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false,
                            "loadDistribution": "Default",
                            "disableOutboundSnat": true,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolInbound')]"
                            },
                            "probe": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/probes/myHTTPProbe')]"
                            }
                        }
                    }
                ]
        ```
       如需負載平衡規則的詳細資訊，請參閱[什麼是 Azure Load Balancer？](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **探查**-您可以藉由新增或移除** \<資源群組-名稱> json**檔案的**探查**區段中的專案，在設定中新增或移除負載平衡器的探查：

        ```json
        "probes": [
                    {
                        "name": "myHTTPProbe",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "Http",
                            "port": 80,
                            "requestPath": "/",
                            "intervalInSeconds": 15,
                            "numberOfProbes": 2
                        }
                    }
                ],
        ```
       如需 Azure Load Balancer 健康狀態探查的詳細資訊，請參閱[Load Balancer 健康情況探查](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)

    * **輸入 nat 規則**-您可以新增或移除負載平衡器的輸入 nat 規則，方法是新增或移除** \<資源群組名稱> json**檔案的**loadbalancer.inboundnatrules**區段中的專案：

        ```json
        "inboundNatRules": [
                    {
                        "name": "myInboundNATRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 4422,
                            "backendPort": 3389,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false
                        }
                    }
                ]
        ```
        若要完成輸入 NAT 規則的新增或移除，必須在** \<資源群組名稱>. json**檔案的結尾，將此規則顯示或移除為**類型**屬性：

        ```json
        {
            "type": "Microsoft.Network/loadBalancers/inboundNatRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('loadBalancers_myLoadBalancer_name'), '/myInboundNATRule')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "frontendIPConfiguration": {
                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                },
                "frontendPort": 4422,
                "backendPort": 3389,
                "enableFloatingIP": false,
                "idleTimeoutInMinutes": 4,
                "protocol": "Tcp",
                "enableTcpReset": false
            }
        }
        ```
        如需輸入 NAT 規則的詳細資訊，請參閱[什麼是 Azure Load Balancer？](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)
    
13. 將** \<資源群組名稱儲存> json**檔案。
    
10. 在目的地區域中，使用[remove-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)建立或部署目標內部負載平衡器的資源群組。 上述的現有資源群組也可以在此程式中重複使用：
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. 使用[new-azresourcegroupdeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)，將已編輯** \<的資源群組名稱>. json**檔案部署到在上一個步驟中建立的資源群組：

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. 若要確認已在目的地區域中建立資源，請使用[remove-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0)和[remove-azloadbalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0)：
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzLoadBalancer -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>捨棄 

部署之後，如果您想要在目標中啟動或捨棄虛擬網路和負載平衡器，請刪除在目標中建立的資源群組，並刪除移動的虛擬網路和負載平衡器。  若要移除資源群組，請使用[remove-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)：

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>清除

若要認可變更並完成 NSG 的移動，請刪除來源[NSG 或資源群組，使用 remove-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)或[remove-new-azvirtualnetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0)並[移除-remove-azloadbalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzVirtualNetwork -Name <virtual-network-name> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已將 Azure 內部負載平衡器從一個區域移至另一個區域，並清除來源資源。  若要深入瞭解如何在 Azure 中的區域和嚴重損壞修復之間移動資源，請參閱：


- [將資源移至新的資源群組或訂用帳戶](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [將 Azure VM 移動到另一個區域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
