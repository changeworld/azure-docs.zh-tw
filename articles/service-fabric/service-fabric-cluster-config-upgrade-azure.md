---
title: 升級 Azure Service Fabric 叢集的設定
description: 了解如何使用 Resource Manager 範本，升級可在 Azure 中執行 Service Fabric 叢集的組態。
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: 692dc2162159ab61a3ac527e12dac43438084a60
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2020
ms.locfileid: "91842711"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>在 Azure 中升級叢集的組態 

此文章說明如何自訂 Service Fabric 叢集的各種網狀架構設定。 針對裝載於 Azure 中的叢集，您可以透過 [Azure 入口網站](https://portal.azure.com)或使用 Azure Resource Manager 範本來自訂設定。

> [!NOTE]
> 並非所有設定都可在入口網站中使用，[最好是使用 Azure Resource Manager 範本進行自訂](./service-fabric-best-practices-infrastructure-as-code.md)；入口網站僅適用於 Service Fabric Dev\Test 案例。
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>使用 Resource Manager 範本自訂叢集設定
透過 JSON Resource Manager 範本可以設定 Azure 叢集。 若要深入了解不同的設定，請參閱[叢集的組態設定](service-fabric-cluster-fabric-settings.md)。 舉例來說，下列步驟示範如何使用 Azure 資源總管將新設定 *MaxDiskQuotaInMB* 新增至 [Diagnostics]** 區段。

1. 移至 https://resources.azure.com 。
2. **展開訂**用帳戶  ->  **\<Your Subscription>**  ->  **resourceGroups**  ->  **\<Your Resource Group>**  ->  **提供者**  ->  **Microsoft. ServiceFabric**  ->  **clusters**叢集，以流覽至您的訂用帳戶 -> **\<Your Cluster Name>**
3. 選取右上角的 [ **讀取/寫入]。**
4. 選取 [ **編輯** ] 並更新 `fabricSettings` JSON 元素，然後加入新的元素：

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

您也可以使用 Azure Resource Manager 透過下列其中一種方式自訂叢集設定：

- 使用 [Azure 入口網站](../azure-resource-manager/templates/export-template-portal.md)來匯出並更新 Resource Manger 範本。
- 使用 [PowerShell](../azure-resource-manager/management/manage-resources-powershell.md) 來匯出並更新 Resource Manger 範本。
- 使用 [Azure CLI](../azure-resource-manager/management/manage-resources-cli.md) 來匯出並更新 Resource Manger 範本。
- 您可以使用 Azure PowerShell [AzServiceFabricSetting](/powershell/module/az.servicefabric/set-azservicefabricsetting) 和 [AzServiceFabricSetting](/powershell/module/az.servicefabric/remove-azservicefabricsetting) 命令來直接修改設定。
- 使用 Azure CLI [az sf cluster setting](/cli/azure/sf/cluster/setting) 命令來直接修改設定。

## <a name="next-steps"></a>後續步驟
* 深入了解 [Service Fabric 叢集設定](service-fabric-cluster-fabric-settings.md)。
* 瞭解如何 [相應縮小和放大您的](service-fabric-cluster-scale-in-out.md)叢集。
