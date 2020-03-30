---
title: 通過使用 Azure 門戶將 Azure 外部負載等化器移動到其他 Azure 區域
description: 使用 Azure 資源管理器範本使用 Azure 門戶將外部負載等化器從一個 Azure 區域移動到另一個 Azure 區域。
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: 5cd5ce2635ce05c4d5962f12ddc3945342897ecd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638488"
---
# <a name="move-an-external-load-balancer-to-another-region-by-using-the-azure-portal"></a>通過使用 Azure 門戶將外部負載等化器移動到其他區域

在各種方案中，您希望將外部負載等化器從一個區域移動到另一個區域。 例如，您可能希望創建另一個配置相同的外部負載等化器以進行測試。 作為災害復原規劃的一部分，您可能還希望將外部負載平衡器移到另一個區域。

從字面上來說，不能將 Azure 外部負載等化器從一個區域移動到另一個區域。 但是，您可以使用 Azure 資源管理器範本匯出外部負載等化器的現有配置和公共 IP 位址。 然後，您可以通過將負載等化器和公共 IP 匯出到範本、修改參數以匹配目的地區域，然後將範本部署到新區域，在另一個區域中暫按資源。 有關資源管理器和範本的詳細資訊，請參閱[將資源組匯出到範本](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)。


## <a name="prerequisites"></a>Prerequisites

- 確保 Azure 外部負載等化器位於要從其移動的 Azure 區域中。

- Azure 外部負載等化器無法在區域之間移動。 您必須將新的負載等化器與目的地區域中的資源相關聯。

- 要匯出外部負載等化器配置並部署範本以在另一個區域中創建外部負載等化器，您需要為網路參與者角色分配或更高版本。

- 識別來源網路配置，以及您目前使用的所有資源。 此佈局包括但不限於負載等化器、網路安全性群組、公共 IP 和虛擬網路。

- 驗證 Azure 訂閱是否允許在目的地區域中創建外部負載等化器。 請連絡支援人員啟用所需的配額。

- 確保訂閱有足夠的資源來支援負載等化器的添加。 請參閱 [Azure 訂用帳戶和服務限制、配額與限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)。

## <a name="prepare-and-move"></a>準備和移動
以下過程演示如何使用資源管理器範本準備外部負載等化器，並使用 Azure 門戶將外部負載等化器配置移動到目的地區域。 必須首先匯出外部負載等化器的公共 IP 配置。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-the-public-ip-from-the-portal"></a>匯出公共 IP 範本並從門戶部署公共 IP

1. 登錄到 Azure[門戶](https://portal.azure.com)並選擇**資源組**。
2. 找到包含源公共 IP 的資源組並選擇它。
3. 選擇 **"設置** > **匯出"範本**。
4. 選擇 **"在****匯出範本**下部署"。
5. 選擇**TEMPLATE** > **編輯參數**以在連線編輯器中打開參數.json 檔。
8. 要編輯公共 IP 名稱的參數，請在**參數**下**的值**屬性從源公共 IP 名稱更改為目標公共 IP 的名稱。 用引號括上名稱。

    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "publicIPAddresses_myVM1pubIP_name": {
            "value": "<target-publicip-name>"
              }
             }
            }

    ```

    在編輯器中選擇 **"保存在**"

9.  選擇**TEMPLATE** > **編輯範本**以在連線編輯器中打開範本.json 檔。

10. 要編輯將公共 IP 移動到的目的地區域，請更改**資源**下**的位置**屬性：

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
  
    要獲取區域位置代碼，請參閱[Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。 區域的代碼是沒有空格的區功能變數名稱稱。 例如，美國中部的代碼是**中央代碼**。
    
12. 如果需要或需要，還可以更改範本中的其他參數，具體取決於您的要求：

    * **SKU**. 您可以通過更改範本.json 檔中**sKU**下**的名稱**屬性，將配置中的公共 IP 的 SKU 從標準更改為基本 IP，也可以從基本更改為標準：

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

        有關基本和標準 SKU 公共 IP 之間的差異的資訊，請參閱[創建、更改或刪除公共 IP 位址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)。

    * **公共 IP 分配方法和****空閒超時**。 您可以通過將**公共 IP 分配方法**的屬性從 **"動態**"更改為 **"靜態**"或"**靜態**"更改為 **"動態**"來更改公共 IP 分配方法。 您可以通過將**idleTimeoutIn分鐘**屬性更改為所需值來更改空閒超時。 預設值為**4**。

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

        ```

        有關分配方法和空閒超時值的資訊，請參閱[創建、更改或刪除公共 IP 位址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)。

 
13. 在連線編輯器中選擇 **"保存**"。

14. 選擇**BASICS** > **訂閱**以選擇將部署目標公共 IP 的訂閱。

15. 選擇**BASICS** > **資源組**以選擇將部署目標公共 IP 的資源組。 您可以選擇 **"新建**"以為目標公共 IP 創建新資源組。 確保名稱與現有源公共 IP 的源資源組不同。

16. 驗證**BASICS** > **位置**是否設置為要部署公共 IP 的目標位置。

17. 在 **"設置"下**，驗證名稱是否與之前在參數編輯器中輸入的名稱匹配。

18. 選中"**條款及條件**"核取方塊。

19. 選擇 **"購買"** 以部署目標公共 IP。

20. 如果移動的負載等化器的另一個公共 IP 正用於出站 NAT，請重複上述步驟，將第二個出站公共 IP 匯出並部署到目的地區域。

### <a name="export-the-external-load-balancer-template-and-deploy-the-load-balancer-from-the-azure-portal"></a>匯出外部負載等化器範本並從 Azure 門戶部署負載等化器

1. 登錄到 Azure[門戶](https://portal.azure.com)並選擇**資源組**。
2. 找到包含源外部負載等化器的資源組並選擇它。
3. 選擇 **"設置** > **匯出"範本**。
4. 選擇 **"在****匯出範本**下部署"。
5. 選擇**TEMPLATE** > **編輯參數**以在連線編輯器中打開參數.json 檔。

5. 要編輯外部負載等化器名稱的參數，請將源外部負載等化器名稱**的值**屬性更改為目標外部負載等化器的名稱。 用引號括上名稱。

    ```json
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
          "loadBalancers_myLoadbalancer_ext_name": {
          "value": "<target-external-lb-name>"
    },
          "publicIPAddresses_myPubIP_in_externalid": {
          "value": "<target-publicIP-resource-ID>"
    },

    ```

6.  要編輯在前面的步驟中移動的目標公共 IP 的值，必須首先獲取資源識別碼，然後將其粘貼到參數.json 檔中。 要獲取 ID：

    1. 在另一個瀏覽器選項卡或視窗中，登錄到[Azure 門戶](https://portal.azure.com)並選擇**資源組**。
    2. 找到包含您在上述步驟中移動的公共 IP 的目標資源組。 請選取它。
    3. 選擇**設置** > **屬性**。
    4. 在右側的邊欄選項卡中，突出顯示**資源識別碼**並將其複製到剪貼簿。 或者，您可以選擇 **"複製"到****資源識別碼**路徑右側的剪貼簿。
    5. 將資源識別碼 粘貼到 **"編輯參數"** 編輯器中在其他瀏覽器視窗或選項卡中打開**的值**屬性中：

        ```json
           ```json
           "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
           "contentVersion": "1.0.0.0",
           "parameters": {
              "loadBalancers_myLoadbalancer_ext_name": {
              "value": "<target-external-lb-name>"
        },
              "publicIPAddresses_myPubIP_in_externalid": {
              "value": "<target-publicIP-resource-ID>"
        },

        ```
    6. 在連線編輯器中選擇 **"保存**"。


7.  如果已為負載等化器配置出站 NAT 和出站規則，則此檔中將看到出站公共 IP 的外部 ID 的第三個條目。 在**目的地區域**中重複上述步驟以獲取出站公共 IP 的 ID。 將該 ID 粘貼到參數.json 檔中：

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "loadBalancers_myLoadbalancer_ext_name": {
                "value": "<target-external-lb-name>",

            },
                "publicIPAddresses_myPubIP_in_externalid": {
                "value": "<target-publicIP-resource-ID>",

            },
                "publicIPAddresses_myPubIP_out_externalid": {
                "defaultValue": "<target-publicIP-outbound-resource-ID>",

            }
        },
    ```

8.  選擇**TEMPLATE** > **編輯範本**以在連線編輯器中打開範本.json 檔。
9.  要編輯將外部負載等化器配置移動到的目的地區域，請更改範本.json 檔中**資源**下**的位置**屬性：

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

10. 要獲取區域位置代碼，請參閱[Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。 區域的代碼是沒有空格的區功能變數名稱稱。 例如，美國中部的代碼是**中央代碼**。

11. 如果需要或需要，還可以更改範本中的其他參數，具體取決於您的要求：

    * **SKU**. 您可以通過更改範本.json 檔中**sKU**下**的名稱**屬性，將配置中外部負載等化器的 SKU 從標準更改為基本或從基本更改為標準：

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
      有關基本和標準 SKU 負載等化器之間的差異的資訊，請參閱[Azure 標準負載等化器概述](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)。

    * **負載平衡規則**。 您可以通過添加或刪除範本.json 檔的**loadBalancingRules**部分中的條目來添加或刪除配置中的負載平衡規則：

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
       有關負載平衡規則的資訊，請參閱[什麼是 Azure 負載平衡器？](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **探頭**. 您可以通過添加或刪除範本.json 檔的**探測**部分中的條目來在配置中添加或刪除負載等化器的探測器：

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
       有關詳細資訊，請參閱[負載等化器運行狀況探測器](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)。

    * **入站 NAT 規則**。 您可以通過在範本.json 檔的**入站NatRules**部分中添加或項目剪除來添加或刪除負載等化器的入站 NAT 規則：

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
        要完成入站 NAT 規則的添加或刪除，必須在範本.json 檔末尾作為**類型**屬性存在或刪除該規則：

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
        有關入站 NAT 規則的資訊，請參閱[什麼是 Azure 負載等化器？](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **出站規則**。 您可以通過編輯範本.json 檔中的**出站規則**屬性來在配置中添加或刪除出站規則：

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

         有關詳細資訊，請參閱[負載等化器出站規則](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)。

12. 在連線編輯器中選擇 **"保存**"。

13. 選擇**BASICS** > **訂閱**以選擇將部署目標外部負載等化器的訂閱。

15. 選擇**BASICS** > **資源組**以選擇將部署目標負載等化器的資源組。 您可以選擇 **"新建**"以為目標外部負載等化器創建新的資源組。 或者，您可以選擇之前為公共 IP 創建的現有資源組。 確保名稱與現有源外部負載等化器的源資源組不同。

16. 驗證**BASICS** > **位置**是否設置為目標位置，您希望部署外部負載等化器。

17. 在 **"設置"下**，驗證名稱是否與之前在參數編輯器中輸入的名稱匹配。 驗證是否為配置中的任何公共 IP 填充了資源指示。

18. 選中"**條款及條件**"核取方塊。

19. 選擇 **"購買"** 以部署目標公共 IP。

## <a name="discard"></a>捨棄

如果要放棄目標公共 IP 和外部負載等化器，請刪除包含它們的資源組。 為此，請從門戶中的儀表板中選擇資源組，然後在概覽頁面頂部選擇 **"刪除**"。

## <a name="clean-up"></a>清除

要提交更改並完成公共 IP 和外部負載等化器的移動，請刪除源公共 IP 和外部負載等化器或資源組。 為此，請從門戶中的儀表板中選擇該資源組，然後在每個頁面頂部選擇 **"刪除**"。

## <a name="next-steps"></a>後續步驟

在本教程中，您將 Azure 外部負載等化器從一個區域移動到另一個區域並清理了源資源。 要瞭解有關在 Azure 中在區域和災害復原之間移動資源的更多資訊，請參閱：


- [將資源移至新的資源群組或訂用帳戶](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [將 Azure VM 移至其他區域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
