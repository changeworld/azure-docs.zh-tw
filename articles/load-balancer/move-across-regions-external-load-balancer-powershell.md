---
title: 使用 Azure PowerShell 將 Azure 外部負載平衡器移動到其他 Azure 區域
description: 使用 Azure 資源管理器範本使用 Azure PowerShell 將 Azure 外部負載平衡器從一個 Azure 區域移動到另一個 Azure 區域。
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: a24eb4608e7630d5b613751fa2120361eccd7672
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644812"
---
# <a name="move-azure-external-load-balancer-to-another-region-using-azure-powershell"></a>使用 Azure PowerShell 將 Azure 外部負載平衡器移動到其他區域

在各種方案中，您希望將現有外部負載等化器從一個區域移動到另一個區域。 例如，您可能希望創建具有相同配置進行測試的外部負載平衡器。 作為災害復原規劃的一部分，您可能還希望將外部負載平衡器移到另一個區域。

Azure 外部負載等化器不能從一個區域移動到另一個區域。 但是，可以使用 Azure 資源管理器範本匯出外部負載等化器的現有配置和公共 IP。  然後，您可以通過將負載等化器和公共 IP 匯出到範本、修改參數以匹配目的地區域，然後將範本部署到新區域，在另一個區域中暫按資源。  有關資源管理器和範本的詳細資訊，請參閱[將資源組匯出到範本](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Prerequisites

- 確保 Azure 外部負載等化器位於要從其移動的 Azure 區域中。

- Azure 外部負載等化器無法在區域之間移動。  您必須將新的負載等化器與目的地區域中的資源相關聯。

- 要匯出外部負載等化器配置並部署範本以在另一個區域中創建外部負載等化器，您需要"網路參與者"角色或更高版本。
   
- 識別來源網路配置，以及您目前使用的所有資源。 此佈局包括但不限於負載等化器、網路安全性群組、公共 IP 和虛擬網路。

- 驗證 Azure 訂閱是否允許您在使用的目的地區域中創建外部負載等化器。 請連絡支援人員啟用所需的配額。

- 請確保您的訂閱有足夠的資源來支援為此過程添加負載等化器。  請參閱[Azure 訂閱和服務限制、配額和約束](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>準備和移動
以下步驟演示如何使用資源管理器範本準備移動的外部負載等化器，並使用 Azure PowerShell 將外部負載等化器配置移動到目的地區域。  作為此過程的一部分，必須包括外部負載等化器的公共 IP 配置，並且必須先執行，然後才能移動外部負載等化器。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-from-azure-powershell"></a>匯出公共 IP 範本並從 Azure PowerShell 進行部署

1. 使用[Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)命令登錄到 Azure 訂閱，並按照螢幕上的說明操作：
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2. 獲取要移動到目的地區域的公共 IP 的資源識別碼，並使用[Get-AzPublicIP 位址](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)將其放置在變數中：

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. 將源公共 IP 匯出到 .json 檔到執行命令[Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)的目錄中：
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. 下載的檔將以資源匯出的資源組命名。  查找從名為"**\<資源組名稱>.json**的命令匯出的檔，並在您選擇的編輯器中打開該檔：
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. 要編輯公共 IP 名稱的參數，請將源公共 IP 名稱的屬性**預設值**更改為目標公共 IP 的名稱，請確保名稱以引號形式：
    
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

6. 要編輯將移動公共 IP 的目的地區域，請更改資源下**的位置**屬性：

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
  
7. 要獲取區域位置代碼，可以使用 Azure PowerShell Cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0)運行以下命令：

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. 如果願意，還可以更改範本中的其他參數，並且根據您的要求是可選的：

    * **SKU** - 您可以通過更改**\<資源組名稱>.json**檔中的 sku**名稱**屬性，將配置中的公共 IP 的**sKU** > 從標準更改為基本或基本到標準：

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

         有關基本和標準 sku 公共 ip 之間的差異的詳細資訊，請參閱[創建、更改或刪除公共 IP 位址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)。

    * **公共 IP 分配方法和****空閒超時**- 您可以通過將**公共 IP 分配方法**屬性從 **"動態**"更改為 **"靜態****"更改為****"靜態**"來更改範本中的這兩個選項。 可以通過將**idleTimeoutIn分鐘**屬性更改為所需量來更改空閒超時。  預設值為**4**：

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

        有關分配方法和空閒超時值的詳細資訊，請參閱[創建、更改或刪除公共 IP 位址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)。


9. 保存**\<資源組名稱>.json**檔。

10. 在目的地區域中創建一個資源組，以便使用[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)部署的目標公共 IP。
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. 使用[New-AzResourceGroup 部署](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)將編輯**\<的資源組名稱>.json**檔部署到上一步中創建的資源組：

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. 要驗證在目的地區域中創建的資源，請使用[Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0)和[Get-AzPublicIP 位址](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)：
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzPublicIPAddress -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

### <a name="export-the-external-load-balancer-template-and-deploy-from-azure-powershell"></a>匯出外部負載等化器範本並從 Azure PowerShell 進行部署

1. 使用[Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)命令登錄到 Azure 訂閱，並按照螢幕上的說明操作：
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. 獲取要移動到目的地區域的外部負載等化器的資源識別碼，並將其放在使用[Get-AzLoad平衡器的變數中](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0)：

    ```azurepowershell-interactive
    $sourceExtLBID = (Get-AzLoadBalancer -Name <source-external-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. 將源外部負載等化器設定匯出到 .json 檔到執行命令[Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)的目錄中：
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceExtLBID -IncludeParameterDefaultValue
   ```
4. 下載的檔將以資源匯出的資源組命名。  查找從名為"**\<資源組名稱>.json**的命令匯出的檔，並在您選擇的編輯器中打開該檔：
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. 要編輯外部負載等化器名稱的參數，請將源外部負載等化器名稱的屬性**預設值**更改為目標外部負載等化器的名稱，以確保名稱以引號形式使用：

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

6.  要編輯上面移動的目標公共 IP 的值，必須首先獲取資源識別碼，然後將其複製並粘貼到**\<資源組名稱>.json**檔中。  要獲取 ID，請使用[獲取-AzPublicIP 位址](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)：

    ```azurepowershell-interactive
    $targetPubIPID = (Get-AzPublicIPaddress -Name <target-public-ip-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    鍵入變數並點擊輸入以顯示資源識別碼。  突出顯示 ID 路徑並將其複製到剪貼簿：

    ```powershell
    PS C:\> $targetPubIPID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupLB-Move/providers/Microsoft.Network/publicIPAddresses/myPubIP-in-move
    ```

7.  在**\<資源組名稱>.json**檔中，將變數**中的資源識別碼**粘貼在公共 IP 外部 ID 的第二個參數中，以代替**預設值**，確保以引號括起來路徑：

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

8.  如果為負載等化器配置了出站 NAT 和出站規則，則此檔中將存在出站公共 IP 的外部 ID 的第三個條目。  在**目的地區域**中重複上述步驟以獲取出站公共 iP 的 ID，並將該條目粘貼到**\<資源組名稱>.json**檔中：

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

10. 要編輯將移動外部負載等化器配置的目的地區域，請在**\<資源組名稱>.json**檔中**的資源下更改****位置**屬性：

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

11. 要獲取區域位置代碼，可以使用 Azure PowerShell Cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0)運行以下命令：

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
12. 如果願意，還可以更改範本中的其他參數，並且根據您的要求是可選的：
    
    * **SKU** - 您可以通過更改**\<資源組名稱>.json**檔中的 sku**名稱**屬性，將配置中的外部負載等化器的**sKU** > 從標準更改為基本或基本到標準：

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
      有關基本和標準 SKU 負載等化器之間的差異的詳細資訊，請參閱[Azure 標準負載等化器概述](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)

    * **負載平衡規則**- 您可以通過向**\<資源組名稱>.json**檔的**負載平衡規則**部分添加或項目剪除來添加或刪除配置中的負載平衡規則：

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
       有關負載平衡規則的詳細資訊，請參閱什麼是[Azure 負載等化器？](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **探測**- 您可以通過向**\<資源組名稱>.json**檔的**探測**部分添加或項目剪除來添加或刪除配置中的負載等化器的探測器：

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
       有關 Azure 負載等化器運行狀況探測器的詳細資訊，請參閱[負載等化器運行狀況探測器](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)

    * **入站 NAT 規則**- 您可以通過向**\<資源組名稱>.json**檔的**入站 NatRules**部分添加或項目剪除來添加或刪除負載等化器的入站 NAT 規則：

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
        要完成入站 NAT 規則的添加或刪除，必須在**\<資源組名稱>.json**檔末尾作為**類型**屬性存在或刪除該規則：

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
        有關入站 NAT 規則的詳細資訊，請參閱[什麼是 Azure 負載等化器？](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **出站規則**- 您可以通過編輯**\<資源組名稱>.json**檔中**的出站規則**屬性來添加或刪除配置中的出站規則：

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

         有關出站規則的詳細資訊，請參閱[負載等化器出站規則](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)

13. 保存**\<資源組名稱>.json**檔。
    
10. 在目的地區域中創建或資源組，以便使用[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)進行部署的目標外部負載等化器。 上面的現有資源組也可以作為此過程的一部分重用：
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. 使用[New-AzResourceGroup 部署](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)將編輯**\<的資源組名稱>.json**檔部署到上一步中創建的資源組：

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. 要驗證在目的地區域中創建的資源，請使用[Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0)和[Get-AzLoad平衡器](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0)：
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzLoadBalancer -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>捨棄 

部署後，如果要重新開始或丟棄目標中的公共 IP 和負載等化器，請刪除在目標中創建的資源組，並刪除移動的公共 IP 和負載等化器。  要刪除資源組，請使用[刪除 AzResource 組](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)：

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>清除

要提交更改並完成 NSG 的移動，請刪除源 NSG 或資源組，使用[刪除-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)或[刪除-AzPublicIp位址](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress?view=azps-2.6.0)和[刪除-AzLoad平衡器](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzPublicIpAddress -Name <public-ip> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>後續步驟

在本教程中，您將 Azure 網路安全性群組從一個區域移動到另一個區域並清理了源資源。  要瞭解有關在 Azure 中在區域和災害復原之間移動資源的詳細資訊，請參閱：


- [將資源移至新的資源群組或訂用帳戶](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [將 Azure VM 移至其他區域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
