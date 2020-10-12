---
title: 使用 Azure 入口網站將 Azure 內部 Load Balancer 移至另一個 Azure 區域
description: 使用 Azure Resource Manager 範本，使用 Azure 入口網站將 Azure 內部 Load Balancer 從一個 Azure 區域移至另一個區域
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/18/2019
ms.author: allensu
ms.openlocfilehash: eb3605249578b15d67bdd9764490d61812b21c18
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84808434"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-the-azure-portal"></a>使用 Azure 入口網站將 Azure 內部 Load Balancer 移至另一個區域

在各種情況下，您會想要將現有的內部負載平衡器從一個區域移到另一個區域。 例如，您可能會想要建立具有相同設定的內部負載平衡器來進行測試。 您也可能想要將內部負載平衡器移至另一個區域，作為嚴重損壞修復計畫的一部分。

Azure 內部負載平衡器無法從一個區域移至另一個區域。 不過，您可以使用 Azure Resource Manager 範本來匯出內部負載平衡器的現有設定和虛擬網路。  然後，您可以將負載平衡器和虛擬網路匯出至範本、修改參數以符合目的地區域，然後將範本部署到新的區域，以暫存另一個區域中的資源。  如需有關 Resource Manager 和範本的詳細資訊，請參閱[快速入門：使用 Azure 入口網站建立及部署 Azure Resource Manager 範本](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)。


## <a name="prerequisites"></a>Prerequisites

- 請確定 Azure 內部負載平衡器位於您要移動的 Azure 區域中。

- 無法在區域之間移動 Azure 內部負載平衡器。  您必須將新的負載平衡器與目的地區域中的資源產生關聯。

- 若要匯出內部負載平衡器設定並部署範本，以在另一個區域中建立內部負載平衡器，您需要網路參與者角色或更高的角色。

- 識別來源網路配置，以及您目前使用的所有資源。 此版面配置包含但不限於負載平衡器、網路安全性群組、虛擬機器和虛擬網路。

- 確認您的 Azure 訂用帳戶可讓您在使用的目的地區域中建立內部負載平衡器。 請連絡支援人員啟用所需的配額。

- 請確定您的訂用帳戶有足夠的資源，可支援為此程式新增負載平衡器。  查看 [Azure 訂用帳戶和服務限制、配額和條件約束](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>準備及移動
下列步驟說明如何使用 Resource Manager 範本準備進行移動的內部負載平衡器，並使用 Azure 入口網站將內部負載平衡器設定移至目的地區域。  在此程式中，必須包含內部負載平衡器的虛擬網路設定，而且必須先完成，才能移動內部負載平衡器。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-the-azure-portal"></a>匯出虛擬網路範本並從 Azure 入口網站部署

1. 登入[Azure 入口網站](https://portal.azure.com)  >  **資源群組**。
2. 找出包含來源虛擬網路的資源群組，然後按一下它。
3. 選取 >**設定**  >  **匯出範本**。
4. 選擇 [**匯出範本**] 分頁中的 [**部署**]。
5. 按一下 [**範本**  >  **編輯參數**]，在線上編輯器中開啟檔案**parameters.js** 。
6. 若要編輯虛擬網路名稱的參數，請變更 [**參數**] 下的 [**值**] 屬性：

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
7. 將編輯器中的 [來源虛擬網路名稱] 值變更為目標 VNET 的您所選擇的名稱。 請確定以引號括住名稱。

8. 在編輯器中按一下 [ **儲存** ]。

9. 按一下 [**範本**  >  **編輯範本**]，在線上編輯器中開啟檔案**template.js** 。

10. 若要編輯將移動 VNET 的目的地區域，請變更 [資源] 底下的 [ **位置** ] 屬性：

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

11. 若要取得區域位置代碼，請參閱 [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。  區域的程式碼是區功能變數名稱稱，不含空格、**美國中部**  =  **centralus**。

12. 您也可以視需要在 [檔案] 的 [ **template.js** ] 中變更其他參數，視您的需求而定：

    * **位址空間**-在儲存之前，您可以修改**資源**  >  **addressSpace**區段，並變更檔案**template.js**中的**addressPrefixes**屬性，以變更 VNET 的位址空間：

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

    * **子**網-修改**template.json**檔案的 [**子**網] 區段，即可變更或新增子網名稱和子網位址空間。 您可以藉由修改 **name** 屬性來變更子網的名稱。 藉由改變**template.json**檔案中的**addressPrefix**屬性，即可變更子網位址空間：

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

         在 **template.js** 檔案中，若要變更位址前置詞，必須在兩個地方進行編輯，上面列出的區段和以下所列的 **類型** 區段。  將 **addressPrefix** 屬性變更為符合上述各項：

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

13. 在線上編輯器中按一下 [ **儲存** ]。

14. 按一下 [**基本**]  >  **訂**用帳戶，選擇將部署目標 VNET 的訂用帳戶。

15. 按一下 [**基本**  >  **資源群組**]，選擇將部署目標 VNET 的資源群組。  您可以按一下 [ **建立新** 的]，為目標 VNET 建立新的資源群組。  請確定名稱與現有 VNET 的來源資源群組不同。

16. 確認**基本**  >  **位置**已設為您想要部署 VNET 的目標位置。

17. 在 [ **設定** ] 底下，確認名稱符合您在上述 [參數編輯器] 中輸入的名稱。

18. 勾選 [ **條款及條件**] 底下的方塊。

19. 按一下 [ **購買** ] 按鈕以部署目標虛擬網路。

### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>匯出內部負載平衡器範本，並從 Azure PowerShell 部署

1. 登入[Azure 入口網站](https://portal.azure.com)  >  **資源群組**。
2. 找出包含來源內部負載平衡器的資源群組，然後按一下它。
3. 選取 >**設定**  >  **匯出範本**。
4. 選擇 [**匯出範本**] 分頁中的 [**部署**]。
5. 按一下 [**範本**  >  **編輯參數**]，在線上編輯器中開啟檔案**parameters.js** 。

6. 若要編輯內部負載平衡器名稱的參數，請將來源內部負載平衡器名稱的屬性 **defaultValue** 變更為目標內部負載平衡器的名稱，並確定名稱是以引號括住：

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

6. 若要編輯上方移動之目標虛擬網路的值，您必須先取得資源識別碼，然後將它複製並貼到 **parameters.js** 檔案中。 若要取得識別碼：

    1. [Azure portal](https://portal.azure.com)  >  在另一個瀏覽器索引標籤或視窗中，登入 Azure 入口網站**資源群組**。
    2. 從上述步驟中找出包含已移動之虛擬網路的目標資源群組，然後按一下它。
    3. 選取 >**設定**  >  **屬性**。
    4. 在右側的分頁中，反白顯示 **資源識別碼** ，並將它複製到剪貼簿。  或者，您可以按一下**資源識別碼**路徑右邊的 [**複製到剪貼**簿] 按鈕。
    5. 將資源識別碼貼到 [ **defaultValue** ] 屬性中，然後在另一個瀏覽器視窗或索引標籤中開啟 [ **編輯參數** 編輯器]：

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
    6. 在線上編輯器中按一下 [ **儲存** ]。

7. 按一下 [**範本**  >  **編輯範本**]，在線上編輯器中開啟檔案**template.js** 。
8. 若要編輯將移動內部負載平衡器設定的目的地區域，請在 [檔案**template.js**的 [**資源**] 下，變更 [**位置**] 屬性：

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

9.  若要取得區域位置代碼，請參閱 [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。  區域的程式碼是區功能變數名稱稱，不含空格、**美國中部**  =  **centralus**。

10. 您也可以根據需求而選擇變更範本中的其他參數：

    * **Sku** -您可以變更**sku**  >  **template.json**檔案中的 [Sku**名稱**] 屬性，將 configuration 中內部負載平衡器的 sku 從 standard 變更為 basic 或 basic 變更為 standard：

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
      如需基本和標準 sku 負載平衡器之間差異的詳細資訊，請參閱 [Azure Standard Load Balancer 總覽](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)

    * **負載平衡規則**-您可以在設定中新增或移除專案至**template.jsOn**檔案的**loadBalancingRules**區段，以新增或移除負載平衡規則：

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
       如需有關負載平衡規則的詳細資訊，請參閱 [什麼是 Azure Load Balancer？](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **探查** -您可以在設定中新增或移除 **探查** **template.js** 區段的專案，以在設定中新增或移除負載平衡器的探查：

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
       如需有關 Azure Load Balancer 健康情況探查的詳細資訊，請參閱 [Load Balancer 健康情況探查](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)

    * **輸入 nat 規則**-您可以新增或移除負載平衡器的輸入 nat 規則，方法是在檔案**template.js**的 [ **loadbalancer.inboundnatrules** ] 區段中新增或移除專案：

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
        若要完成輸入 NAT 規則的新增或移除，規則必須存在或移除為檔案**template.js**結尾的**類型**屬性：

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
        如需輸入 NAT 規則的詳細資訊，請參閱 [什麼是 Azure Load Balancer？](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

12. 在線上編輯器中按一下 [ **儲存** ]。

13. 按一下 [**基本**]  >  **訂**用帳戶，選擇將部署目標內部負載平衡器的訂用帳戶。

15. 按一下 [**基本**  >  **資源群組**]，選擇將部署目標負載平衡器的資源群組。  您可以按一下 [ **建立新** 的]，為目標內部負載平衡器建立新的資源群組，或選擇上述為虛擬網路建立的現有資源群組。  請確定名稱與現有來源內部負載平衡器的來源資源群組不相同。

16. 確認**基本**  >  **位置**已設為您想要部署內部負載平衡器的目標位置。

17. 在 [ **設定** ] 底下，確認名稱符合您在上述 [參數編輯器] 中輸入的名稱。  確認已針對設定中的任何虛擬網路填入資源識別碼。

18. 勾選 [ **條款及條件**] 底下的方塊。

19. 按一下 [ **購買** ] 按鈕以部署目標虛擬網路。

## <a name="discard"></a>捨棄

如果您想要捨棄目標虛擬網路和內部負載平衡器，請刪除包含目標虛擬網路和內部負載平衡器的資源群組。  若要這樣做，請從入口網站中的儀表板選取資源群組，然後選取 [總覽] 頁面頂端的 [ **刪除** ]。

## <a name="clean-up"></a>清除

若要認可變更並完成虛擬網路和內部負載平衡器的移動，請刪除來源虛擬網路和內部負載平衡器或資源群組。 若要這樣做，請從入口網站中的儀表板選取虛擬網路和內部負載平衡器或資源群組，然後選取每個頁面頂端的 [ **刪除** ]。

## <a name="next-steps"></a>接下來的步驟

在本教學課程中，您已將 Azure 內部負載平衡器從一個區域移至另一個區域，並清除來源資源。  若要深入了解如何在 Azure 中的區域之間移動資源和災害復原，請參閱：


- [將資源移至新的資源群組或訂用帳戶](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [將 Azure VM 移至其他區域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
