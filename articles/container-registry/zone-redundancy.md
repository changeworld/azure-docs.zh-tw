---
title: 適用于高可用性的區域冗余登錄
description: 瞭解如何在 Azure 可用性區域中建立容器登錄或複寫，以在 Azure Container Registry 中啟用區域冗余。 區域冗余是 Premium 服務層的功能。
ms.topic: article
ms.date: 12/11/2020
ms.openlocfilehash: f94d5a8d61c42e8833e21f035303be173c81764d
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681514"
---
# <a name="enable-zone-redundancy-in-azure-container-registry-for-resiliency-and-high-availability"></a>在 Azure Container Registry 中啟用區域冗余以復原及高可用性

除了 [異地](container-registry-geo-replication.md)複寫之外，它會在一或多個 Azure 區域之間複寫登錄資料，以提供可用性並減少區域作業的延遲，Azure Container Registry 支援選用 *區域冗余*。 [區域冗余](../availability-zones/az-overview.md#availability-zones) 可針對特定區域中) 的登錄或複寫資源 (複本提供復原和高可用性。

本文說明如何使用 Azure 入口網站或 Azure Resource Manager 範本，設定區域冗余容器登錄或區域冗余複本。 

區域冗余是 Premium container registry 服務層級的 **預覽** 功能。 如需登錄服務層級和限制的相關資訊，請參閱 [Azure Container Registry 服務層級](container-registry-skus.md)。

## <a name="preview-limitations"></a>預覽限制

* 目前支援下欄區域：美國東部、美國東部2及美國西部2。
* 目前不支援區域轉換至可用性區域。 若要啟用區域中的可用性區域支援，必須在所需的區域中建立登錄，並啟用可用性區域支援，或必須新增已啟用可用性區域支援的複寫區域。
* 區域中的區域冗余無法停用。
* [ACR 工作](container-registry-tasks-overview.md) 尚不支援可用性區域。
* 目前透過 Azure Resource Manager 範本或 Azure 入口網站支援。 未來版本將會啟用 Azure CLI 支援。
* 目前，當您將區域重複的容器登錄移至另一個資源群組時，區域冗余設定會顯示為 `Disabled` 。

## <a name="about-zone-redundancy"></a>關於區域冗余

使用 Azure [可用性區域](../availability-zones/az-overview.md) ，在 azure 區域內建立彈性且高可用性的 azure container registry。 例如，組織可以設定區域多餘的 Azure container registry 與其他 [支援的 azure 資源](../availability-zones/az-region.md) ，以符合資料落地或其他合規性需求，同時在區域內提供高可用性。

Azure Container Registry 也支援 [異地](container-registry-geo-replication.md)複寫，可跨多個區域複寫服務，讓您可以在其他位置計算資源的冗余和位置。 區域內的冗余和跨多個區域的異地複寫的可用性區域組合，可增強登錄的可靠性和效能。

可用性區域是 Azure 地區內獨特的實體位置。 若要確保復原，所有已啟用的地區中至少要有三個不同的區域。 每個區域都有一或多個資料中心配備了獨立的電源、冷卻和網路功能。 針對區域冗余進行設定時，系統會將登錄 (或不同區域中的登錄複本複寫) 會複寫到區域中的所有可用性區域，並在資料中心發生故障時讓其可供使用。

## <a name="create-a-zone-redundant-registry---portal"></a>建立區域-多餘的登錄-入口網站

1. 登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。
1. 選取 [建立資源] > [容器] > [容器登錄]。
1. 在 [ **基本** ] 索引標籤中，選取或建立資源群組，然後輸入唯一的登錄名稱。 
1. 在 [ **位置**] 中，選取支援 Azure Container Registry （例如 *美國東部*）區域冗余的區域。
1. 在 **SKU** 中，選取 [進階]。
1. 在 [ **可用性區域**] 中，選取 [ **已啟用**]。
1. （選擇性）設定其他登錄設定，然後選取 [ **審核 + 建立**]。
1. 選取 [建立] 以部署登錄執行個體。

    :::image type="content" source="media/zone-redundancy/enable-availability-zones-portal.png" alt-text="在 Azure 入口網站中啟用區域冗余":::

若要建立區域冗余複寫：

1. 流覽至您的進階層容器登錄，然後 **選取 [** 複寫]。
1. 在出現的地圖上，選取區域中的綠色六邊形，以支援 Azure Container Registry 的區域冗余，例如 **美國西部 2**。 然後選取 [建立]。
1. 在 [ **建立** 複寫] 視窗的 [ **可用性區域**] 中，選取 [ **已啟用**]，然後選取 [ **建立**]。

## <a name="create-a-zone-redundant-registry---template"></a>建立區域-多餘的登錄範本

### <a name="create-a-resource-group"></a>建立資源群組

如有需要，請執行 [az group create](/cli/az/group#az_group_create) 命令，在 [支援可用性區域](../availability-zones/az-region.md) 的區域中為登錄建立資源群組，以進行 Azure Container Registry，例如 *eastus*。

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="deploy-the-template"></a>部署範本 

您可以使用下列 Resource Manager 範本來建立區域冗余、異地複寫的登錄。 範本預設會啟用登錄中的區域冗余，以及額外的區域複本。 

將下列內容複寫到新的檔案，並使用檔案名 (例如 `registryZone.json`) 儲存檔案。

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "acrName": {
        "type": "string",
        "defaultValue": "[concat('acr', uniqueString(resourceGroup().id))]",
        "minLength": 5,
        "maxLength": 50,
        "metadata": {
          "description": "Globally unique name of your Azure Container Registry"
        }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Location for registry home replica."
        }
      },
      "acrSku": {
        "type": "string",
        "defaultValue": "Premium",
        "allowedValues": [
          "Premium"
        ],
        "metadata": {
          "description": "Tier of your Azure Container Registry. Geo-replication and zone redundancy require Premium SKU."
        }
      },
      "acrZoneRedundancy": {
        "type": "string",
        "defaultValue": "Enabled",
        "metadata": {
          "description": "Enable zone redundancy of registry's home replica. Requires registry location to support availability zones."
        }
      },
      "acrReplicaLocation": {
        "type": "string",
        "metadata": {
          "description": "Short name for registry replica location."
        }
      },
      "acrReplicaZoneRedundancy": {
        "type": "string",
        "defaultValue": "Enabled",
        "metadata": {
          "description": "Enable zone redundancy of registry replica. Requires replica location to support availability zones."
        }
      }
    },
    "resources": [
      {
        "comments": "Container registry for storing docker images",
        "type": "Microsoft.ContainerRegistry/registries",
        "apiVersion": "2020-11-01-preview",
        "name": "[parameters('acrName')]",
        "location": "[parameters('location')]",
        "sku": {
          "name": "[parameters('acrSku')]",
          "tier": "[parameters('acrSku')]"
        },
        "tags": {
          "displayName": "Container Registry",
          "container.registry": "[parameters('acrName')]"
        },
        "properties": {
          "adminUserEnabled": "[parameters('acrAdminUserEnabled')]",
          "zoneRedundancy": "[parameters('acrZoneRedundancy')]"
        }
      },
      {
        "type": "Microsoft.ContainerRegistry/registries/replications",
        "apiVersion": "2020-11-01-preview",
        "name": "[concat(parameters('acrName'), '/', parameters('acrReplicaLocation'))]",
        "location": "[parameters('acrReplicaLocation')]",
          "dependsOn": [
          "[resourceId('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
        ],
        "properties": {
          "zoneRedundancy": "[parameters('acrReplicaZoneRedundancy')]"
        }
      }
    ],
    "outputs": {
      "acrLoginServer": {
        "value": "[reference(resourceId('Microsoft.ContainerRegistry/registries',parameters('acrName')),'2019-12-01-preview').loginServer]",
        "type": "string"
      }
    }
  }
```

執行下列 [az deployment group create](/cli/az/deployment#az_group_deployment_create) 命令，使用上述範本檔案來建立登錄。 指出時，請提供：

* 唯一的登錄名稱，或部署不含參數的範本，它會為您建立唯一的名稱。
* 支援可用性區域之複本的位置，例如 *westus2*

```azurecli
az deployment group create \
  --resource-group <resource-group-name> \
  --template-file registryZone.json \
  --parameters acrName=<registry-name> acrReplicaLocation=<replica-location>
```

在命令輸出中，記下登錄 `zoneRedundancy` 和複本的屬性。 啟用時，每個資源都是區域冗余：

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

## <a name="next-steps"></a>後續步驟

* 深入瞭解 [支援可用性區域的區域](../availability-zones/az-region.md)。
* 深入瞭解如何在 Azure 中建立 [可靠性](/azure/architecture/framework/resiliency/overview) 。