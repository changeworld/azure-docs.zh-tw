---
title: 新增及移除 Service Fabric 受控叢集的節點類型 (預覽)
description: 在本教學課程中，您將了解如何新增及移除 Service Fabric 受控叢集的節點類型。
ms.topic: tutorial
ms.date: 09/28/2020
ms.openlocfilehash: bb33512652677fc4e46d8ba3668dca985bbcfe01
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791235"
---
# <a name="tutorial-add-and-remove-node-types-from-a-service-fabric-managed-cluster-preview"></a>教學課程：新增及移除 Service Fabric 受控叢集的節點類型 (預覽)

在本教學課程系列中，我們將討論：

> [!div class="checklist"]
> * [如何部署 Service Fabric 受控叢集](tutorial-managed-cluster-deploy.md)
> * [如何擴增 Service Fabric 受控叢集](tutorial-managed-cluster-scale.md)
> * 如何新增及移除 Service Fabric 受控叢集中的節點
> * [如何將應用程式部署到 Service Fabric 受控叢集](tutorial-managed-cluster-deploy-app.md)

本系列的這個部分涵蓋如何：

> [!div class="checklist"]
> * 將節點類型新增至 Service Fabric 受控叢集
> * 從 Service Fabric 受控叢集刪除節點類型

## <a name="prerequisites"></a>必要條件

* Service Fabric 受控叢集 (請參閱[部署受控叢集](tutorial-managed-cluster-deploy.md))。
* [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps#azservicefabric) 或更新版本 (請參閱 [*安裝 Azure PowerShell*](/powershell/azure/install-az-ps))。

## <a name="add-a-node-type-to-a-service-fabric-managed-cluster"></a>將節點類型新增至 Service Fabric 受控叢集

您可以透過 Azure Resource Manager 範本、PowerShell 或 CLI，將節點類型新增至 Service Fabric 受控叢集。 在本教學課程中，我們將使用 Azure PowerShell 新增節點類型。

若要建立新的節點類型，您必須定義三個屬性：

* **節點類型名稱**：叢集中任何現有節點類型唯一的名稱。
* **執行個體計數**：新節點類型的初始節點數目。
* **VM 大小**：節點的 VM SKU。 若未指定，就會使用「Standard_D2」的預設值。

> [!NOTE]
> 如果要新增的節點類型是叢集中的第一個或唯一節點類型，則必須使用主要屬性。

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "mysfcluster"
$nodeTypeName = "nt2"
$vmSize = "Standard_D2_v2"

New-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -Name $nodeTypeName -InstanceCount 3 -vmSize $vmSize
```

## <a name="remove-a-node-type-from-a-service-fabric-managed-cluster"></a>從 Service Fabric 受控叢集移除節點類型

若要從 Service Fabric 受控叢集移除節點類型，您必須使用 PowerShell 或 CLI。 在本教學課程中，我們將使用 Azure PowerShell 移除節點類型。

> [!NOTE]
> 如果主要節點類型是叢集中唯一的主要節點類型，則無法將其移除。  

若要移除節點類型：

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "myCluster"
$nodeTypeName = "nt2"

Remove-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName  -Name $nodeTypeName
```

## <a name="next-steps"></a>後續步驟

 在本節中，我們新增及刪除了節點類型。 若要了解如何將應用程式部署到 Service Fabric 受控叢集，請參閱：

> [!div class="nextstepaction"]
> [將應用程式部署到 Service Fabric 受控叢集](tutorial-managed-cluster-deploy-app.md)
