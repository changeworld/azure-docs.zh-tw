---
title: 使用 Azure 入口網站將 Azure 外部負載平衡器移至另一個 Azure 區域
description: 使用 Azure Resource Manager 範本，使用 Azure 入口網站將外部負載平衡器從一個 Azure 區域移至另一個區域。
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: 0598f21cddbaeef6b3cd10cd77250eeae8bd34bf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84808718"
---
# <a name="move-an-external-load-balancer-to-another-region-by-using-the-azure-portal"></a>使用 Azure 入口網站將外部負載平衡器移至另一個區域

在許多情況下，您會想要將外部負載平衡器從一個區域移至另一個區域。 例如，您可能會想要使用相同的設定來建立另一個具有相同設定的外部負載平衡器來進行測試。 您也可能想要將外部負載平衡器移至另一個區域，做為嚴重損壞修復計畫的一部分。

就常值而言，您無法將 Azure 外部負載平衡器從一個區域移至另一個區域。 但是，您可以使用 Azure Resource Manager 範本來匯出外部負載平衡器的現有設定和公用 IP 位址。 接著，您可以將負載平衡器和公用 IP 匯出至範本、修改參數以符合目的地區域，然後將範本部署到新的區域，藉此將資源放在另一個區域中。 如需 Resource Manager 和範本的詳細資訊，請參閱[將資源群組匯出至範本](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)。


## <a name="prerequisites"></a>必要條件

- 請確定 Azure 外部負載平衡器位於您要移動的 Azure 區域中。

- Azure 外部負載平衡器無法在區域之間移動。 您必須將新的負載平衡器關聯至目的地區域中的資源。

- 若要匯出外部負載平衡器設定並部署範本，以在另一個區域中建立外部負載平衡器，您必須獲指派網路參與者角色或更高的許可權。

- 識別來源網路配置，以及您目前使用的所有資源。 此配置包括但不限於負載平衡器、網路安全性群組、公用 Ip 和虛擬網路。

- 確認您的 Azure 訂用帳戶可讓您在目的地區域中建立外部負載平衡器。 請連絡支援人員啟用所需的配額。

- 請確定您的訂用帳戶有足夠的資源可支援新增負載平衡器。 請參閱 [Azure 訂用帳戶和服務限制、配額與限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)。

## <a name="prepare-and-move"></a>準備及移動
下列程式說明如何使用 Resource Manager 範本來準備移動的外部負載平衡器，並使用 Azure 入口網站將外部負載平衡器設定移至目的地區域。 您必須先匯出外部負載平衡器的公用 IP 設定。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-the-public-ip-from-the-portal"></a>匯出公用 IP 範本，並從入口網站部署公用 IP

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後選取 [資源群組]  。
2. 找出包含來源公用 IP 的資源群組，然後選取它。
3. 選取 [**設定**] [  >  **匯出範本**]。
4. 選取 [**匯出範本**] 底下的 [**部署**]。
5. 選取 [**範本**]  >  [**編輯參數**]，在線上編輯器中開啟檔案的 parameters.js。
8. 若要編輯公用 IP 名稱的參數，請將 [**參數**] 底下的 [**值**] 屬性從來源 [公用 ip 名稱] 變更為您的目標公用 ip 名稱。 將名稱括在引號中。

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

    在編輯器中選取 [**儲存**]。

9.  選取 [**範本**]  >  [**編輯範本**]，在線上編輯器中開啟檔案的 template.js。

10. 若要編輯將會移動公用 IP 的目的地區域，請變更 [**資源**] 底下的 [**位置**] 屬性：

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
  
    若要取得區域位置代碼，請參閱[Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。 區域的程式碼是不含空格的區功能變數名稱稱。 例如，「美國中部」的程式碼為**centralus**。
    
12. 視您的需求而定，您也可以變更範本中的其他參數：

    * **SKU**。 您可以變更檔案中 template.js[ **SKU** ] 底下的 [**名稱**] 屬性，將設定中的公用 IP sku 從 [標準] 變更為 [基本] 或 [從基本] 變更為 [標準]：

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

        如需基本和標準 SKU 公用 Ip 之間差異的詳細資訊，請參閱[建立、變更或刪除公用 IP 位址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)。

    * **公用 IP 配置方法**和**空閒超時**。 您可以藉由將**publicIPAllocationMethod**屬性從**動態**變更為**靜態**，或從**靜態**變更為**動態**，來變更公用 IP 配置方法。 您可以藉由將**idleTimeoutInMinutes**屬性變更為所需的值，來變更閒置的超時時間。 預設值為**4**。

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

        如需配置方法和閒置超時值的相關資訊，請參閱[建立、變更或刪除公用 IP 位址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)。

 
13. 在線上編輯器中選取 [**儲存**]。

14. 選取 [**基本**]  >  [**訂**用帳戶]，以選擇將部署目標公用 IP 的訂用帳戶。

15. 選取 [**基本**]  >  [**資源群組**]，選擇將部署目標公用 IP 的資源群組。 您可以選取 [**建立新**的]，為目標公用 IP 建立新的資源群組。 請確定名稱與現有來源公用 IP 的來源資源群組不同。

16. 確認 [**基本**  >  **位置**] 已設定為您想要部署公用 IP 的目標位置。

17. 在 [**設定**] 底下，確認名稱與您稍早在 [參數編輯器] 中輸入的名稱相符。

18. 選取 [**條款及條件**] 核取方塊。

19. 選取 [**購買**] 以部署目標公用 IP。

20. 如果您有另一個公用 IP 用於已移動的負載平衡器的輸出 NAT，請重複上述步驟，將第二個輸出公用 IP 匯出並部署至目的地區域。

### <a name="export-the-external-load-balancer-template-and-deploy-the-load-balancer-from-the-azure-portal"></a>匯出外部負載平衡器範本，並從 Azure 入口網站部署負載平衡器

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後選取 [資源群組]  。
2. 找出包含來源外部負載平衡器的資源群組，然後選取它。
3. 選取 [**設定**] [  >  **匯出範本**]。
4. 選取 [**匯出範本**] 底下的 [**部署**]。
5. 選取 [**範本**]  >  [**編輯參數**]，在線上編輯器中開啟檔案的 parameters.js。

5. 若要編輯外部負載平衡器名稱的參數，請將來源外部負載平衡器名稱的**value**屬性變更為目標外部負載平衡器的名稱。 將名稱括在引號中。

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

6.  若要編輯您在先前步驟中移動的目標公用 IP 值，您必須先取得資源識別碼，然後將它貼入檔案中的 parameters.js。 若要取得識別碼：

    1. 在另一個瀏覽器索引標籤或視窗中，登入[Azure 入口網站](https://portal.azure.com)，然後選取 [**資源群組**]。
    2. 找出包含您在先前步驟中移動之公用 IP 的目標資源群組。 請加以選取。
    3. 選取 [**設定**] [  >  **屬性**]。
    4. 在右側的分頁中，反白顯示**資源識別碼**，並將它複製到剪貼簿。 或者，您也可以選取**資源識別碼**路徑右邊的 [**複製到剪貼**簿]。
    5. 在其他瀏覽器視窗或索引標籤中開啟的 [**編輯參數**編輯器] 中，將 [資源識別碼] 貼到 [**值**] 屬性中：

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
    6. 在線上編輯器中選取 [**儲存**]。


7.  如果您已設定負載平衡器的輸出 NAT 和輸出規則，您會在此檔案中看到輸出公用 IP 的外部識別碼的第三個專案。 在**目的地區域**中重複上述步驟，以取得輸出公用 IP 的識別碼。 將該識別碼貼入檔案中的 parameters.js：

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

8.  選取 [**範本**]  >  [**編輯範本**]，在線上編輯器中開啟檔案的 template.js。
9.  若要編輯將移動外部負載平衡器設定的目的地區域，請變更檔案中 [template.js**資源**] 底下的 [**位置**] 屬性：

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

10. 若要取得區域位置代碼，請參閱[Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。 區域的程式碼是不含空格的區功能變數名稱稱。 例如，「美國中部」的程式碼為**centralus**。

11. 視您的需求而定，您也可以變更範本中的其他參數：

    * **SKU**。 您可以變更檔案中 template.js[ **SKU** ] 底下的 [**名稱**] 屬性，將設定中的外部負載平衡器 SKU 從 [標準] 變更為 [基本] 或 [從基本] 變更為 [標準]：

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
      如需基本和標準 SKU 負載平衡器之間差異的詳細資訊，請參閱[Azure Standard Load Balancer 總覽](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)。

    * **負載平衡規則**。 您可以藉由新增或移除檔案上 template.js**loadBalancingRules**區段中的專案，在設定中新增或移除負載平衡規則：

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
       如需負載平衡規則的相關資訊，請參閱[什麼是 Azure Load Balancer？](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)。

    * **探查**。 您可以新增或移除檔案上 template.js的**探查**區段中的專案，以在設定中新增或移除負載平衡器的探查：

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
       如需詳細資訊，請參閱[Load Balancer 健康情況探查](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)。

    * **輸入 NAT 規則**。 您可以在 template.json 檔案的**loadbalancer.inboundnatrules**區段中新增或移除專案，以新增或移除負載平衡器的輸入 NAT 規則：

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
        若要完成輸入 NAT 規則的新增或移除，此規則必須存在，或移除為檔案上 template.js結尾的**類型**屬性：

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
        如需輸入 NAT 規則的詳細資訊，請參閱[什麼是 Azure Load Balancer？](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)。

    * **輸出規則**。 您可以藉由編輯 template.json 檔案中的**outboundRules**屬性，來新增或移除設定中的輸出規則：

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

         如需詳細資訊，請參閱[Load Balancer 輸出規則](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)。

12. 在線上編輯器中選取 [**儲存**]。

13. 選取 [**基本**]  >  [**訂**用帳戶]，選擇將部署目標外部負載平衡器的訂用帳戶。

15. 選取 [**基本**]  >  [**資源群組**]，選擇將部署目標負載平衡器的資源群組。 您可以選取 [**建立新**的]，為目標外部負載平衡器建立新的資源群組。 或者，您可以選擇您稍早為公用 IP 建立的現有資源群組。 請確定名稱與現有來源外部負載平衡器的來源資源群組不同。

16. 確認 [**基本**  >  **位置**] 已設定為您要部署外部負載平衡器的目標位置。

17. 在 [**設定**] 底下，確認 [名稱] 符合您稍早在 [參數編輯器] 中輸入的名稱。 確認設定中的資源識別碼已填入任何公用 Ip。

18. 選取 [**條款及條件**] 核取方塊。

19. 選取 [**購買**] 以部署目標公用 IP。

## <a name="discard"></a>捨棄

如果您想要捨棄目標公用 IP 和外部負載平衡器，請刪除包含它們的資源群組。 若要這麼做，請從入口網站的儀表板中選取資源群組，然後選取 [總覽] 頁面頂端的 [**刪除**]。

## <a name="clean-up"></a>清除

若要認可變更並完成移動公用 IP 和外部負載平衡器，請刪除來源公用 IP 和外部負載平衡器或資源群組。 若要這麼做，請在入口網站的儀表板中選取該資源群組，然後選取每個頁面頂端的 [**刪除**]。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已將 Azure 外部負載平衡器從一個區域移至另一個區域，並清除來源資源。 若要深入瞭解如何在 Azure 中的區域和嚴重損壞修復之間移動資源，請參閱：


- [將資源移至新的資源群組或訂用帳戶](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [將 Azure VM 移至其他區域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
