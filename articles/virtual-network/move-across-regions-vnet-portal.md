---
title: 使用 Azure 入口網站將 Azure 虛擬網路移至另一個 Azure 區域。
description: 使用 Resource Manager 範本和 Azure 入口網站，將 Azure 虛擬網路從一個 Azure 區域移至另一個區域。
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: ed3da649ba65484a79b42ba5bb45431839e123d4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84711437"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-the-azure-portal"></a>使用 Azure 入口網站將 Azure 虛擬網路移至另一個區域

有各種不同的案例可將現有的 Azure 虛擬網路從一個區域移至另一個區域。 例如，您可能想要使用與現有虛擬網路相同的測試和可用性設定來建立虛擬網路。 或者，您可能想要將生產虛擬網路移至另一個區域，作為嚴重損壞修復計畫的一部分。

您可以使用 Azure Resource Manager 範本來完成將虛擬網路移至另一個區域的工作。 做法是將虛擬網路匯出至範本、修改參數以符合目的地區域，然後將範本部署到新的區域。 如需 Resource Manager 範本的詳細資訊，請參閱 [快速入門：使用 Azure 入口網站建立和部署 Azure Resource Manager 範本](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)。


## <a name="prerequisites"></a>Prerequisites

- 請確定您的虛擬網路位於您想要移動的 Azure 區域中。

- 若要匯出虛擬網路並部署範本，以在另一個區域中建立虛擬網路，您必須擁有「網路參與者」角色或更高的角色。

- 虛擬網路對等互連將不會重新建立，如果它們仍然存在於範本中，它們將會失敗。 匯出範本之前，您必須移除任何虛擬網路對等。 然後，您可以在虛擬網路移動之後重新建立它們。

- 識別來源網路配置，以及您目前使用的所有資源。 此版面配置包含但不限於負載平衡器、網路安全性群組 (Nsg) 和公用 Ip。

- 確認您的 Azure 訂用帳戶可讓您在目的地區域中建立虛擬網路。 若要啟用所需的配額，請聯絡支援人員。

- 請確定您的訂用帳戶有足夠的資源，可支援新增此程式的虛擬網路。 如需詳細資訊，請參閱 [Azure 訂用帳戶和服務限制、配額和條件約束](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)。


## <a name="prepare-for-the-move"></a>準備移動
在本節中，您會使用 Resource Manager 範本來準備要進行移動的虛擬網路。 然後，您可以使用 Azure 入口網站將虛擬網路移至目的地區域。

若要匯出虛擬網路，並使用 Azure 入口網站部署目標虛擬網路，請執行下列動作：

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後選取 [ **資源群組**]。
1. 找出包含來源虛擬網路的資源群組，然後選取它。
1. 選取 [**設定**  >  **匯出範本**]。
1. 在 [ **匯出範本** ] 窗格中，選取 [ **部署**]。
1. 若要在線上編輯器中開啟檔案*parameters.js* ，請選取 [**範本**  >  **編輯參數**]。
1. 若要編輯虛擬網路名稱的參數，請變更 [**參數**] 下的 [**值**] 屬性：

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

1. 在編輯器中，將編輯器中的 [來源虛擬網路名稱] 值變更為您想要用於目標虛擬網路的名稱。 請務必以引號括住名稱。

1. 在編輯器中選取 [ **儲存** ]。

1. 若要在線上編輯器中開啟檔案*template.js* ，請選取 [**範本**  >  **編輯範本**]。

1. 在線上編輯器中，若要編輯將移動虛擬網路的目的地區域，請變更 [**資源**] 底下的 [**位置**] 屬性：

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

1. 若要取得區域位置代碼，請參閱 [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。 區域的程式碼是區功能變數名稱稱，沒有空格 (例如，**美國中部**  =  **centralus**) 。

1.  (選擇性) 您也可以變更範本中的其他參數，視您的需求而定：

    * **位址空間**：儲存檔案之前，您可以修改**資源**  >  **addressSpace**區段並變更**addressPrefixes**屬性，以改變虛擬網路的位址空間：

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

    * **子網**：您可以藉由變更範本的 **子網** 區段來變更或新增至子網名稱和子網位址空間。 您可以藉由變更 [ **名稱** ] 屬性來變更子網的名稱。 您可以藉由變更 **addressPrefix** 屬性來變更子網位址空間：

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

        若要變更 *template.js* 檔案中的位址首碼，請在下列兩個位置中進行編輯：在上一節的程式碼中，以及在下列程式碼的 **type** 區段中進行編輯。 變更下列程式碼中的 **addressPrefix** 屬性，以符合上一節程式碼中的 **addressPrefix** 屬性。

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

1. 在 [線上] 編輯器中，選取 [ **儲存**]。

1. 若要選擇將部署目標虛擬網路的訂用帳戶，請選取 [**基本**]  >  **訂**用帳戶。

1. 若要選擇將部署目標虛擬網路的資源群組，請選取 [**基本**  >  **資源群組**]。 

    如果您需要為目標虛擬網路建立新的資源群組，請選取 [ **建立新**的]。 請確定名稱與現有虛擬網路中的來源資源組名不同。

1. 確認**基本**  >  **位置**已設為您想要部署虛擬網路的目標位置。

1. 在 [ **設定**] 底下，確認名稱與您先前在 [參數編輯器] 中輸入的名稱相符。

1. 選取 [ **條款及條件** ] 核取方塊。

1. 若要部署目標虛擬網路，請選取 [ **購買**]。

## <a name="delete-the-target-virtual-network"></a>刪除目標虛擬網路

若要捨棄目標虛擬網路，您可以刪除包含目標虛擬網路的資源群組。 操作方法：
1. 在 Azure 入口網站儀表板上，選取資源群組。
1. 在 [ **總覽** ] 窗格的頂端，選取 [ **刪除**]。

## <a name="clean-up"></a>清除

若要認可變更並完成虛擬網路移動，您可以刪除來源虛擬網路或資源群組。 操作方法：
1. 在 Azure 入口網站儀表板上，選取虛擬網路或資源群組。
1. 在每個窗格的頂端，選取 [ **刪除**]。

## <a name="next-steps"></a>接下來的步驟

在本教學課程中，您已使用 Azure 入口網站將 Azure 虛擬網路從一個區域移至另一個區域，然後清除不需要的來源資源。 若要深入瞭解如何在 Azure 中的區域與災難復原之間移動資源，請參閱：


- [將資源移到新的資源群組或訂用帳戶](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) \(部分機器翻譯\)
- [將 Azure 虛擬機器移至另一個區域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
