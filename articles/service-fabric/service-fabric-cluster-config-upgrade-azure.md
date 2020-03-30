---
title: 升級 Azure 服務結構群集的配置
description: 了解如何使用 Resource Manager 範本，升級可在 Azure 中執行 Service Fabric 叢集的組態。
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 476a2d910b916ea29132b108478d06f756454813
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463280"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>在 Azure 中升級叢集的組態 

此文章說明如何自訂 Service Fabric 叢集的各種網狀架構設定。 針對裝載於 Azure 中的叢集，您可以透過 [Azure 入口網站](https://portal.azure.com)或使用 Azure Resource Manager 範本來自訂設定。

> [!NOTE]
> 並非所有設定都可在入口網站中使用，[最好是使用 Azure Resource Manager 範本進行自訂](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)；入口網站僅適用於 Service Fabric Dev\Test 案例。
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>使用 Resource Manager 範本自訂叢集設定
透過 JSON Resource Manager 範本可以設定 Azure 叢集。 若要深入了解不同的設定，請參閱[叢集的組態設定](service-fabric-cluster-fabric-settings.md)。 舉例來說，下列步驟示範如何使用 Azure 資源總管將新設定 *MaxDiskQuotaInMB* 新增至 [Diagnostics]** 區段。

1. 移至 https://resources.azure.com。
2. ** **  -> 通過擴展**訂閱** -> **\<>****resourceGroups** -> **資源組\<** -> **providers** -> **\<****Microsoft.ServiceFabric**>供應商 Microsoft 導航到訂閱>。  ->   -> 
3. 在右上角，選擇 **"讀/寫"。**
4. 選擇 **"編輯**並更新`fabricSettings`JSON 元素"並添加新元素：

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

- 使用 [Azure 入口網站](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template)來匯出並更新 Resource Manger 範本。
- 使用 [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell) 來匯出並更新 Resource Manger 範本。
- 使用 [Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli) 來匯出並更新 Resource Manger 範本。
- 使用 Azure PowerShell[集-AzServiceFabric 設置](https://docs.microsoft.com/powershell/module/az.servicefabric/Set-azServiceFabricSetting)和[刪除-AzServiceFabric設置](https://docs.microsoft.com/powershell/module/az.servicefabric/Remove-azServiceFabricSetting)命令直接修改設置。
- 使用 Azure CLI [az sf cluster setting](https://docs.microsoft.com/cli/azure/sf/cluster/setting) 命令來直接修改設定。

## <a name="next-steps"></a>後續步驟
* 深入了解 [Service Fabric 叢集設定](service-fabric-cluster-fabric-settings.md)。
* 瞭解如何[將群集擴展到和外。](service-fabric-cluster-scale-up-down.md)
