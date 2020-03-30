---
title: 使用 Azure 門戶將 Azure 虛擬網路移動到其他 Azure 區域。
description: 通過使用資源管理器範本和 Azure 門戶將 Azure 虛擬網路從一個 Azure 區域移動到另一個 Azure 區域。
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: c54725d9a947b0c912a822686d7b2cffe1a7b5c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75640783"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-the-azure-portal"></a>使用 Azure 門戶將 Azure 虛擬網路移動到其他區域

存在將現有 Azure 虛擬網路從一個區域移動到另一個區域的各種方案。 例如，您可能希望創建配置與現有虛擬網路相同的配置以進行測試和可用性的虛擬網路。 或者，您可能希望將生產虛擬網路移動到其他區域，作為災害復原規劃的一部分。

可以使用 Azure 資源管理器範本完成虛擬網路移動到其他區域。 為此，可以將虛擬網路匯出到範本，修改參數以匹配目的地區域，然後將範本部署到新區域。 有關資源管理器範本的詳細資訊，請參閱[快速入門：使用 Azure 門戶創建和部署 Azure 資源管理器範本](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)。


## <a name="prerequisites"></a>Prerequisites

- 確保虛擬網路位於要移動的 Azure 區域中。

- 要匯出虛擬網路並部署範本以在另一個區域中創建虛擬網路，需要具有"網路參與者"角色或更高角色。

- 虛擬網路對等互連不會重新創建，如果範本中仍然存在，它們就會失敗。 在匯出範本之前，必須刪除任何虛擬網路對等體。 然後，您可以在虛擬網路移動後重新建立它們。

- 識別來源網路配置，以及您目前使用的所有資源。 此佈局包括但不限於負載等化器、網路安全性群組 （NSG） 和公共 IP。

- 驗證 Azure 訂閱是否允許在目的地區域中創建虛擬網路。 要啟用所需的配額，請聯繫支援人員。

- 請確保您的訂閱有足夠的資源來支援此進程的虛擬網路添加。 如需詳細資訊，請參閱 [Azure 訂用帳戶和服務限制、配額與條件約束](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)。


## <a name="prepare-for-the-move"></a>準備移動
在本節中，您可以使用資源管理器範本為移動準備虛擬網路。 然後，使用 Azure 門戶將虛擬網路移動到目的地區域。

要匯出虛擬網路並使用 Azure 門戶部署目標虛擬網路，請使用以下操作：

1. 登錄到 Azure[門戶](https://portal.azure.com)，然後選擇**資源組**。
1. 找到包含源虛擬網路的資源組，然後選擇它。
1. 選擇 **"設置** > **匯出"範本**。
1. 在 **"匯出範本"** 窗格中，選擇 **"部署**"。
1. 要在連線編輯器中打開*參數.json*檔，請選擇**範本** > **編輯參數**。
1. 要編輯虛擬網路名稱的參數，請更改**參數**下**的值**屬性：

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

1. 在編輯器中，將編輯器中的源虛擬網路名稱值更改為目標虛擬網路所需的名稱。 請務必用引號括上名稱。

1. 在編輯器中選擇 **"保存在**"

1. 要在連線編輯器中打開*範本.json*檔，請選擇**範本** > **編輯範本**。

1. 在連線編輯器中，要編輯將移動虛擬網路的目的地區域，請更改**資源**下**的位置**屬性：

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

1. 要獲取區域位置代碼，請參閱[Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。 區域的代碼是區功能變數名稱稱，沒有空格（例如，**美國** = **中部中心）。**

1. （可選）您還可以根據您的要求更改範本中的其他參數：

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

    * **子網**：您可以通過更改範本的**子網**部分來更改或添加到子網名稱和子網位址空間。 您可以通過更改名稱屬性來更改子網**的名稱**。 還可以通過更改**位址首碼**屬性來更改子網位址空間：

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

        要更改*範本.json*檔中的位址首碼，請將其編輯在兩個位置：在前面一節中的代碼和以下代碼**的類型**部分中。 更改以下代碼中的**位址首碼**屬性，以匹配上一節中代碼中的**位址首碼**屬性。

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

1. 在連線編輯器中，選擇 **"保存**"。

1. 要選擇將部署目標虛擬網路的訂閱，請選擇 **"基礎** > **訂閱**"。

1. 要選擇將部署目標虛擬網路的資源組，請選擇 **"基本** > **資源組**"。 

    如果需要為目標虛擬網路創建新的資源組，請選擇"**創建新**"。 確保名稱與現有虛擬網路中的源資源組名稱不同。

1. 驗證**基本位置** > **Location**是否設置為要部署虛擬網路的目標位置。

1. 在 **"設置"** 下，驗證名稱是否與以前在參數編輯器中輸入的名稱匹配。

1. 選擇"**條款及條件**"核取方塊。

1. 要部署目標虛擬網路，請選擇 **"購買**"。

## <a name="delete-the-target-virtual-network"></a>刪除目標虛擬網路

要放棄目標虛擬網路，請刪除包含目標虛擬網路的資源組。 若要這樣做：
1. 在 Azure 門戶儀表板上，選擇資源組。
1. 在 **"概述"** 窗格的頂部，選擇 **"刪除**"。

## <a name="clean-up"></a>清除

要提交更改並完成虛擬網路移動，請刪除源虛擬網路或資源組。 若要這樣做：
1. 在 Azure 門戶儀表板上，選擇虛擬網路或資源組。
1. 在每個窗格的頂部，選擇 **"刪除**"。

## <a name="next-steps"></a>後續步驟

在本教程中，您可以使用 Azure 門戶將 Azure 虛擬網路從一個區域移動到另一個區域，然後清理不需要的源資源。 要瞭解有關在 Azure 中在區域和災害復原之間移動資源的更多資訊，請參閱：


- [將資源移至新的資源群組或訂用帳戶](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [將 Azure 虛擬機器移動到其他區域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
