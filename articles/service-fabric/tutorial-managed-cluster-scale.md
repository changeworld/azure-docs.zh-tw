---
title: 擴增 Service Fabric 受控叢集 (預覽)
description: 在本教學課程中，您將了解如何擴增 Service Fabric 受控叢集的節點類型。
ms.topic: tutorial
ms.date: 09/28/2020
ms.openlocfilehash: a6777743932f3cc73db973c4d9935d4b5827700b
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/28/2020
ms.locfileid: "91410191"
---
# <a name="tutorial-scale-out-a-service-fabric-managed-cluster-preview"></a>教學課程：擴增 Service Fabric 受控叢集 (預覽)

在本教學課程系列中，我們將討論：

> [!div class="checklist"]
> * [如何部署 Service Fabric 受控叢集。](tutorial-managed-cluster-deploy.md)
> * 如何擴增 Service Fabric 受控叢集
> * [如何新增及移除 Service Fabric 受控叢集中的節點類型](tutorial-managed-cluster-add-remove-node-type.md)
> * [如何將應用程式部署到 Service Fabric 受控叢集](tutorial-managed-cluster-deploy-app.md)

本系列的這個部分涵蓋如何：

> [!div class="checklist"]
> * 調整 Service Fabric 受控叢集節點

## <a name="prerequisites"></a>必要條件

* Service Fabric 受控叢集 (請參閱[部署受控叢集](tutorial-managed-cluster-deploy.md))。
* [Azure PowerShell 4.7.0](https://docs.microsoft.com/powershell/azure/release-notes-azureps?view=azps-4.7.0&preserve-view=true#azservicefabric) 或更新版本 (請參閱[*安裝 Azure PowerShell*](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-4.7.0&preserve-view=true))。

## <a name="scale-a-service-fabric-managed-cluster"></a>調整 Service Fabric 受控叢集
變更執行個體計數以增加或減少您想要調整節點類型上的節點數目。 您可以從叢集部署或在 Service Fabric Explorer 中，尋找 Azure Resource Manager 範本 (ARM 範本) 中的節點類型名稱。  

> [!NOTE]
> 如果節點類型為主要，您的基本 SKU 叢集將無法低於 3 個節點，而標準 SKU 叢集無法低於 5 個節點。

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "mysfcluster"
$nodeTypeName = "FE"
$instanceCount = "7"

Set-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -name $nodeTypeName -InstanceCount $instanceCount -Verbose
```

叢集將會自動開始升級，而幾分鐘後您就會看到其他節點。

## <a name="next-steps"></a>後續步驟

在此步驟中，我們已調整 Service Fabric 受控叢集上的節點類型。 若要深入了解如何新增及移除節點類型，請參閱：

> [!div class="nextstepaction"]
> [新增及移除 Service Fabric 受控叢集中的節點類型](tutorial-managed-cluster-add-remove-node-type.md)
