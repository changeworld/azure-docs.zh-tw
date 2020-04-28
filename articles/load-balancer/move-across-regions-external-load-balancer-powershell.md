---
title: 使用 Azure PowerShell 將 Azure 外部 Load Balancer 移至另一個 Azure 區域
description: 使用 Azure Resource Manager 範本，使用 Azure PowerShell 將 Azure 外部 Load Balancer 從一個 Azure 區域移至另一個區域。
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: a24eb4608e7630d5b613751fa2120361eccd7672
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75644812"
---
# <a name="move-azure-external-load-balancer-to-another-region-using-azure-powershell"></a>使用 Azure PowerShell 將 Azure 外部 Load Balancer 移至另一個區域

在許多情況下，您會想要將現有的外部負載平衡器從一個區域移至另一個區域。 例如，您可能會想要使用相同的設定來建立外部負載平衡器以進行測試。 您也可以將外部負載平衡器移至另一個區域，做為嚴重損壞修復計畫的一部分。

Azure 外部負載平衡器無法從一個區域移至另一個區域。 不過，您可以使用 Azure Resource Manager 範本來匯出外部負載平衡器的現有設定和公用 IP。  接著，您可以將負載平衡器和公用 IP 匯出至範本、修改參數以符合目的地區域，然後將範本部署到新的區域，藉此將資源放在另一個區域中。  如需 Resource Manager 和範本的詳細資訊，請參閱[將資源群組匯出至範本](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Prerequisites

- 請確定 Azure 外部負載平衡器位於您要移動的 Azure 區域中。

- Azure 外部負載平衡器無法在區域之間移動。  您必須將新的負載平衡器關聯至目的地區域中的資源。

- 若要匯出外部負載平衡器設定並部署範本，以在另一個區域中建立外部負載平衡器，您需要網路參與者角色或更高版本。
   
- 識別來源網路配置，以及您目前使用的所有資源。 此配置包括但不限於負載平衡器、網路安全性群組、公用 Ip 和虛擬網路。

- 確認您的 Azure 訂用帳戶可讓您在使用的目的地區域中建立外部負載平衡器。 請連絡支援人員啟用所需的配額。

- 請確定您的訂用帳戶有足夠的資源可支援新增此程式的負載平衡器。  請參閱[Azure 訂用帳戶和服務限制、配額和條件約束](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>準備和移動
下列步驟說明如何準備外部負載平衡器，以使用 Resource Manager 範本進行移動，以及使用 Azure PowerShell 將外部負載平衡器設定移至目的地區域。  在此程式中，必須包含外部負載平衡器的公用 IP 設定，而且必須先完成，才能移動外部負載平衡器。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-from-azure-powershell"></a>匯出公用 IP 範本，並從 Azure PowerShell 部署

1. 使用[disconnect-azaccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)命令登入您的 Azure 訂用帳戶，並遵循畫面上的指示：
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2. 取得您想要移至目的地區域之公用 IP 的資源識別碼，並使用[get-azpublicipaddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)將它放在變數中：

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. 將來源公用 IP 匯出至 json 檔案，以在您執行命令[匯出-remove-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)的目錄中：
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. 下載的檔案將會以匯出資源的來源資源群組命名。  找出從名為** \<[資源-群組-名稱>** ] 的命令匯出的檔案，並在您選擇的編輯器中開啟它：
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. 若要編輯公用 IP 名稱的參數，請將來源公用 IP 名稱的屬性**defaultValue**變更為目標公用 ip 的名稱，確定名稱是以引號括住：
    
    ```json
        {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_myVM1pubIP_name": {
        "defaultValue": "<target-publicip-name>",
        "type": "String"
        }
    }

    ```

6. 若要編輯將移動公用 IP 的目的地區域，請變更 [資源] 底下的 [**位置**] 屬性：

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]             
    ```
  
7. 若要取得區域位置代碼，您可以藉由執行下列命令來使用 Azure PowerShell Cmdlet [get-azlocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) ：

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. 您也可以根據您的需求，變更範本中的其他參數，而且是選擇性的：

    * **Sku** -您可以藉由變更** \<資源群組名稱**中的**sku** > **名稱**屬性> json 檔案，將設定中的公用 IP sku 從 standard 變更為「基本」或「基本」到「標準」：

         ```json
            "resources": [
                   {
                    "type": "Microsoft.Network/publicIPAddresses",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                    "location": "<target-region>",
                    "sku": {
                        "name": "Basic",
                        "tier": "Regional"
                    },
         ```

         如需基本和標準 sku 公用 ip 之間差異的詳細資訊，請參閱[建立、變更或刪除公用 IP 位址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)。

    * **公用 IP 配置方法**和**閒置時間**-您可以在範本中變更這兩個選項，方法是將 [ **PublicIPAllocationMethod** ] 屬性從 [**動態**] 變更為 [**靜態**] 或 [**靜態**] 變更為 [**動態**]。 藉由將**idleTimeoutInMinutes**屬性變更為您想要的數量，可以變更閒置的超時時間。  預設值為**4**：

         ```json
         "resources": [
                {
                "type": "Microsoft.Network/publicIPAddresses",
                "apiVersion": "2019-06-01",
                "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                "location": "<target-region>",
                  "sku": {
                  "name": "Basic",
                  "tier": "Regional"
                 },
                "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
                   }
                }            
         ```

        如需配置方法和閒置超時值的詳細資訊，請參閱[建立、變更或刪除公用 IP 位址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)。


9. 將** \<資源群組名稱儲存> json**檔案。

10. 在目的地區域中建立資源群組，以使用[remove-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)部署目標公用 IP。
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. 使用[new-azresourcegroupdeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)，將已編輯** \<的資源群組名稱>. json**檔案部署到在上一個步驟中建立的資源群組：

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. 若要確認已在目的地區域中建立資源，請使用[remove-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0)和[get-azpublicipaddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)：
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzPublicIPAddress -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

### <a name="export-the-external-load-balancer-template-and-deploy-from-azure-powershell"></a>匯出外部負載平衡器範本並從 Azure PowerShell 部署

1. 使用[disconnect-azaccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)命令登入您的 Azure 訂用帳戶，並遵循畫面上的指示：
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. 取得您想要移至目的地區域之外部負載平衡器的資源識別碼，並使用[remove-azloadbalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0)將它放在變數中：

    ```azurepowershell-interactive
    $sourceExtLBID = (Get-AzLoadBalancer -Name <source-external-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. 將來源外部負載平衡器設定匯出至 json 檔案，以在您執行命令[匯出-remove-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)的目錄中：
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceExtLBID -IncludeParameterDefaultValue
   ```
4. 下載的檔案將會以匯出資源的來源資源群組命名。  找出從名為** \<[資源-群組-名稱>** ] 的命令匯出的檔案，並在您選擇的編輯器中開啟它：
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. 若要編輯外部負載平衡器名稱的參數，請將來源外部負載平衡器名稱的屬性**defaultValue**變更為目標外部負載平衡器的名稱，並確定名稱是以引號括住：

    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        "loadBalancers_myLoadbalancer_ext_name": {
        "defaultValue": "<target-external-lb-name>",
        "type": "String"
            },
        "publicIPAddresses_myPubIP_in_externalid": {
        "defaultValue": "<target-publicIP-resource-ID>",
        "type": "String"
            },

    ```

6.  若要編輯上方已移動的目標公用 IP 值，您必須先取得資源識別碼，然後將它複製並貼到** \<資源群組名稱>. json**檔案中。  若要取得識別碼，請使用[get-azpublicipaddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)：

    ```azurepowershell-interactive
    $targetPubIPID = (Get-AzPublicIPaddress -Name <target-public-ip-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    輸入變數，然後按 enter 鍵以顯示資源識別碼。  反白顯示識別碼路徑，並將它複製到剪貼簿：

    ```powershell
    PS C:\> $targetPubIPID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupLB-Move/providers/Microsoft.Network/publicIPAddresses/myPubIP-in-move
    ```

7.  在** \<資源群組名稱> json**檔案中，貼上變數中的**資源識別碼**，以取代公用 IP 外部識別碼的第二個參數中的**defaultValue** ，請務必以引號括住路徑：

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
            "loadBalancers_myLoadbalancer_ext_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
            },
            "publicIPAddresses_myPubIP_in_externalid": {
            "defaultValue": "<target-publicIP-resource-ID>",
            "type": "String"
            },

    ```

8.  如果您已設定負載平衡器的輸出 NAT 和輸出規則，此檔案中會有第三個專案作為輸出公用 IP 的外部識別碼。  在**目的地區域**中重複上述步驟，以取得輸出公用 iP 的識別碼，並將該專案貼入** \<資源群組名稱> json**檔案：

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "loadBalancers_myLoadbalancer_ext_name": {
                "defaultValue": "<target-external-lb-name>",
                "type": "String"
            },
                "publicIPAddresses_myPubIP_in_externalid": {
                "defaultValue": "<target-publicIP-resource-ID>",
                "type": "String"
            },
                "publicIPAddresses_myPubIP_out_externalid": {
                "defaultValue": "<target-publicIP-outbound-resource-ID>",
                "type": "String"
            }
        },
    ```

10. 若要編輯將移動外部負載平衡器設定的目的地區域，請變更** \<資源群組名稱> json**檔案中 [**資源**] 下的 [**位置**] 屬性：

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-external-lb-region>",
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
    
    * **Sku** -您可以藉由變更** \<資源群組名稱>. json**檔案中的**sku** > **名稱**屬性，將設定中的外部負載平衡器 sku 從 [標準] 變更為 [基本] 或 [基本] 設定為 [標準]：

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-external-lb-region>",
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

    * **輸出規則**-您可以藉由編輯** \<資源群組名稱>. json**檔案中的**outboundRules**屬性，來新增或移除設定中的輸出規則：

        ```json
        "outboundRules": [
                    {
                        "name": "myOutboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "allocatedOutboundPorts": 10000,
                            "protocol": "All",
                            "enableTcpReset": false,
                            "idleTimeoutInMinutes": 15,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolOutbound')]"
                            },
                            "frontendIPConfigurations": [
                                {
                                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPoutbound')]"
                                }
                            ]
                        }
                    }
                ]
        ```

         如需輸出規則的詳細資訊，請參閱[Load Balancer 輸出規則](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)

13. 將** \<資源群組名稱儲存> json**檔案。
    
10. 在目的地區域中，使用[remove-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)建立或部署目標外部負載平衡器的資源群組。 上述的現有資源群組也可以在此程式中重複使用：
    
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

部署之後，如果您想要在目標中開始或捨棄公用 IP 和負載平衡器，請刪除在目標中建立的資源群組，並刪除移動的公用 IP 和負載平衡器。  若要移除資源群組，請使用[remove-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)：

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>清除

若要認可變更並完成 NSG 的移動，請刪除來源[NSG 或資源群組，使用 remove-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)或[remove-get-azpublicipaddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress?view=azps-2.6.0)並[移除-remove-azloadbalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzPublicIpAddress -Name <public-ip> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已將 Azure 網路安全性群組從一個區域移至另一個區域，並清除來源資源。  若要深入瞭解如何在 Azure 中的區域和嚴重損壞修復之間移動資源，請參閱：


- [將資源移至新的資源群組或訂用帳戶](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [將 Azure VM 移動到另一個區域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
