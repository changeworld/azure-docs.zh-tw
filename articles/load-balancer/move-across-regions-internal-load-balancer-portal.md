---
title: 使用 Azure 門戶將 Azure 內部負載平衡器移動到其他 Azure 區域
description: 使用 Azure 資源管理器範本使用 Azure 門戶將 Azure 內部負載平衡器從一個 Azure 區域移動到另一個 Azure 區域
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/18/2019
ms.author: allensu
ms.openlocfilehash: f23923b9d847ef393ebd609eb5fbba530b1a07d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638800"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-the-azure-portal"></a>使用 Azure 門戶將 Azure 內部負載平衡器移動到其他區域

在各種方案中，您希望將現有內部負載等化器從一個區域移動到另一個區域。 例如，您可能希望創建具有相同配置用於測試的內部負載等化器。 作為災害復原規劃的一部分，您可能還希望將內部負載平衡器移到另一個區域。

Azure 內部負載等化器不能從一個區域移動到另一個區域。 但是，可以使用 Azure 資源管理器範本匯出內部負載等化器的現有配置和虛擬網路。  然後，可以通過將負載等化器和虛擬網路匯出到範本、修改參數以匹配目的地區域，然後將範本部署到新區域，將資源暫入另一個區域。  有關資源管理器和範本的詳細資訊，請參閱[快速入門：使用 Azure 門戶創建和部署 Azure 資源管理器範本](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)。


## <a name="prerequisites"></a>Prerequisites

- 確保 Azure 內部負載等化器位於要從其移動的 Azure 區域中。

- Azure 內部負載等化器無法在區域之間移動。  您必須將新的負載等化器與目的地區域中的資源相關聯。

- 要匯出內部負載等化器配置並部署範本以在另一個區域中創建內部負載等化器，您需要"網路參與者"角色或更高版本。

- 識別來源網路配置，以及您目前使用的所有資源。 此佈局包括但不限於負載等化器、網路安全性群組、虛擬機器和虛擬網路。

- 驗證 Azure 訂閱是否允許您在使用的目的地區域中創建內部負載等化器。 請連絡支援人員啟用所需的配額。

- 請確保您的訂閱有足夠的資源來支援為此過程添加負載等化器。  請參閱[Azure 訂閱和服務限制、配額和約束](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>準備和移動
以下步驟演示如何使用資源管理器範本準備內部負載等化器，以及使用 Azure 門戶將內部負載等化器配置移動到目的地區域。  作為此過程的一部分，必須包括內部負載等化器的虛擬網路配置，並且必須首先在移動內部負載等化器之前完成。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-the-azure-portal"></a>匯出虛擬網路範本並從 Azure 門戶進行部署

1. 登錄到[Azure 門戶](https://portal.azure.com) > **資源組**。
2. 找到包含源虛擬網路的資源組並按一下它。
3. 選擇>**設置** > **匯出範本**。
4. 在 **"匯出範本"** 邊欄選項卡中選擇 **"部署**"。
5. 按一下**TEMPLATE** > **編輯參數**以在連線編輯器中打開**參數.json**檔。
6. 要編輯虛擬網路名稱的參數，請更改**參數**下**的值**屬性：

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "virtualNetworks_myVNET1_name": {
                "value": "<target-virtual-network-name>"
            }
        }
    }
    ```
7. 將編輯器中的源虛擬網路名稱值更改為目標 VNET 的首選名稱。 請確保將名稱括在引號中。

8. 按一下編輯器中的 **"保存**"。

9. 按一下**TEMPLATE** > **編輯範本**以在連線編輯器中打開**範本.json**檔。

10. 要編輯將移動 VNET 的目的地區域，請更改資源下**的位置**屬性：

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

11. 要獲取區域位置代碼，請參閱[Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。  區域的代碼是沒有空格的區功能變數名稱稱，**美國** = **中部中心**。

12. 如果您選擇，還可以更改**範本.json**檔中的其他參數，並且根據您的要求是可選的：

    * **位址空間**- 通過修改**資源** > **位址空間**部分並更改**範本.json**檔中**的位址首碼**屬性，可以在保存之前更改 VNET 的位址空間：

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

    * **子網**- 可以通過修改**範本.json**檔的**子網**部分來更改或添加到子網名稱和子網位址空間。 可以通過更改**名稱**屬性來更改子網的名稱。 可以通過更改**範本.json**檔中**的位址首碼**屬性來更改子網位址空間：

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

         在**範本.json**檔中，要更改位址首碼，必須在兩個位置進行編輯，即上面列出的部分和下面列出的**類型**部分。  更改**位址首碼**屬性以匹配上述屬性：

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

13. 按一下連線編輯器中的 **"保存**"。

14. 按一下**BASICS** > **訂閱**以選擇將部署目標 VNET 的訂閱。

15. 按一下**BASICS** > **資源組**以選擇將部署目標 VNET 的資源組。  可以按一下 **"創建新**"以創建目標 VNET 的新資源組。  確保名稱與現有 VNET 的源資源組不同。

16. 驗證**BASICS** > **位置**設置為您希望部署 VNET 的目標位置。

17. 在 **"設置"** 下驗證名稱是否與您在上述參數編輯器中輸入的名稱匹配。

18. 選中 **"條件"和"條件**"下的核取方塊。

19. 按一下 **"購買**"按鈕以部署目標虛擬網路。

### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>匯出內部負載等化器範本並從 Azure PowerShell 進行部署

1. 登錄到[Azure 門戶](https://portal.azure.com) > **資源組**。
2. 找到包含源內部負載等化器的資源組，然後按一下它。
3. 選擇>**設置** > **匯出範本**。
4. 在 **"匯出範本"** 邊欄選項卡中選擇 **"部署**"。
5. 按一下**TEMPLATE** > **編輯參數**以在連線編輯器中打開**參數.json**檔。

6. 要編輯內部負載等化器名稱的參數，請將源內部負載等化器名稱的屬性**預設值**更改為目標內部負載等化器的名稱，以確保名稱以引號形式使用：

    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```

6. 要編輯上面移動的目標虛擬網路的值，必須首先獲取資源識別碼，然後將其複製並粘貼到**參數.json**檔中。 要獲取 ID：

    1. 登錄到另一個瀏覽器選項卡或視窗中的[Azure 門戶](https://portal.azure.com) > **資源組**。
    2. 從上述步驟中查找包含移動虛擬網路的目標資源組，然後按一下它。
    3. 選擇>**設置** > **屬性**。
    4. 在右側的邊欄選項卡中，突出顯示**資源識別碼**並將其複製到剪貼簿。  或者，您可以按一下**資源識別碼**路徑右側的副本**到剪貼簿**按鈕。
    5. 將資源識別碼 粘貼到**預設值**屬性中，以在其他瀏覽器視窗或選項卡中打開**的"編輯參數"** 編輯器中：

        ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
        ```
    6. 按一下連線編輯器中的 **"保存**"。

7. 按一下**TEMPLATE** > **編輯範本**以在連線編輯器中打開**範本.json**檔。
8. 要編輯將移動內部負載等化器配置的目的地區域，請更改**範本.json**檔中**資源**下**的位置**屬性：

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

9.  要獲取區域位置代碼，請參閱[Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。  區域的代碼是沒有空格的區功能變數名稱稱，**美國** = **中部中心**。

10. 如果願意，還可以更改範本中的其他參數，並且根據您的要求是可選的：

    * **SKU** - 您可以通過更改**範本.json**檔中**的 sku** > **名稱**屬性來更改配置中內部負載等化器的 sKU 從標準更改為基本或基本到標準：

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
      有關基本和標準 SKU 負載等化器之間的差異的詳細資訊，請參閱[Azure 標準負載等化器概述](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)

    * **負載平衡規則**- 您可以通過向**範本.json**檔的**負載平衡規則**部分添加或項目剪除來添加或刪除配置中的負載平衡規則：

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

    * **探測**- 您可以通過向**範本.json**檔的**探測**部分添加或項目剪除來添加或刪除配置中的負載等化器的探測器：

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

    * **入站 NAT 規則**- 您可以通過向**範本.json**檔的**入站 NatRules**部分添加或項目剪除來添加或刪除負載等化器的入站 NAT 規則：

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
        要完成入站 NAT 規則的添加或刪除，必須在**範本.json**檔末尾作為**類型**屬性存在或刪除該規則：

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

12. 按一下連線編輯器中的 **"保存**"。

13. 按一下**BASICS** > **訂閱**以選擇將部署目標內部負載等化器的訂閱。

15. 按一下**BASICS** > **資源組**以選擇將部署目標負載等化器的資源組。  可以按一下 **"新建**"為目標內部負載等化器創建新的資源組，或選擇上面為虛擬網路創建的現有資源組。  確保名稱與現有源內部負載等化器的源資源組不同。

16. 驗證**BASICS** > **位置**設置為您希望部署內部負載等化器的目標位置。

17. 在 **"設置"** 下驗證名稱是否與您在上述參數編輯器中輸入的名稱匹配。  驗證配置中任何虛擬網路填充的資源指示。

18. 選中 **"條件"和"條件**"下的核取方塊。

19. 按一下 **"購買**"按鈕以部署目標虛擬網路。

## <a name="discard"></a>捨棄

如果要放棄目標虛擬網路和內部負載等化器，請刪除包含目標虛擬網路和內部負載等化器的資源組。  為此，請從門戶中的儀表板中選擇資源組，然後選擇概覽頁頂部的 **"刪除**"。

## <a name="clean-up"></a>清除

要提交更改並完成虛擬網路和內部負載等化器的移動，請刪除源虛擬網路和內部負載等化器或資源組。 為此，請從門戶中的儀表板中選擇虛擬網路和內部負載等化器或資源組，然後在每個頁面頂部選擇 **"刪除**"。

## <a name="next-steps"></a>後續步驟

在本教程中，您將 Azure 內部負載等化器從一個區域移動到另一個區域並清理了源資源。  要瞭解有關在 Azure 中在區域和災害復原之間移動資源的詳細資訊，請參閱：


- [將資源移至新的資源群組或訂用帳戶](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [將 Azure VM 移至其他區域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
