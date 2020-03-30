---
title: 將節點類型添加到 Azure 服務結構群集
description: 了解如何透過新增虛擬機器擴展集來相應放大 Service Fabric 叢集。
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: 1e7bae89561d43d717eb4d15e95183761b077443
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463967"
---
# <a name="scale-a-service-fabric-cluster-out-by-adding-a-virtual-machine-scale-set"></a>透過新增虛擬機器擴展集來相應放大 Service Fabric 叢集
此文會說明如何將新的節點類型新增至現有的叢集，來調整 Azure Service Fabric 叢集的規模。 Service Fabric 叢集是一組由網路連接的虛擬或實體機器，可用來將您的微服務部署到其中並進行管理。 屬於叢集一部分的機器或 VM 都稱為節點。 虛擬機器擴展集是一個 Azure 計算資源，可以用來將一組虛擬機器當做一個集合加以部署和管理。 在 Azure 叢集中定義的每個節點類型，會[設定為不同的擴展集](service-fabric-cluster-nodetypes.md)。 隨後，您即可個別管理每個節點類型。 建立 Service Fabric 叢集之後，您可以透過將新的節點類型 (虛擬機器擴展集) 新增到現有的叢集，來水平調整叢集的規模。  您可以隨時調整叢集，即使正在叢集上執行工作負載，也是如此。  在叢集進行調整時，您的應用程式也會自動調整。

## <a name="add-an-additional-scale-set-to-an-existing-cluster"></a>將額外的擴展集新增至現有的叢集
向現有群集添加新節點類型（由虛擬機器縮放集支援）類似于[升級主節點類型](service-fabric-scale-up-node-type.md)，但您不會使用相同的 NodeTypeRef;顯然不會禁用任何主動使用的虛擬機器擴展集，如果不更新主節點類型，也不會丟失群集可用性。 

NodeTypeRef 屬性會在虛擬機器擴展集 Service Fabric 擴充功能屬性內宣告：
```json
<snip>
"publisher": "Microsoft.Azure.ServiceFabric",
     "settings": {
     "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
     "nodeTypeRef": "[parameters('vmNodeType2Name')]",
     "dataPath": "D:\\\\SvcFab",
     "durabilityLevel": "Silver",
<snip>
```

此外，您必須將這個新的節點類型新增至您的 Service Fabric 叢集資源：

```json
<snip>
"nodeTypes": [
      {
      "name": "[parameters('vmNodeType2Name')]",
      "applicationPorts": {
                "endPort": "[parameters('nt2applicationEndPort')]",
                "startPort": "[parameters('nt2applicationStartPort')]"
      },
      "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
      "durabilityLevel": "Silver",
       "ephemeralPorts": {
                "endPort": "[parameters('nt2ephemeralEndPort')]",
                "startPort": "[parameters('nt2ephemeralStartPort')]"
      },
      "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
      "isPrimary": false,
      "vmInstanceCount": "[parameters('nt2InstanceCount')]"
},
<snip>
```

## <a name="next-steps"></a>後續步驟
* 了解如何[相應增加主要節點類型](service-fabric-scale-up-node-type.md)
* 深入了解[應用程式延展性](service-fabric-concepts-scalability.md)。
* [將 Azure 叢集相應縮小或相應放大](service-fabric-tutorial-scale-cluster.md)。
* 使用 Fluent Azure 計算 SDK，[以程式設計方式調整 Azure 叢集](service-fabric-cluster-programmatic-scaling.md)。
* [將獨立叢集相應縮小或相應放大](service-fabric-cluster-windows-server-add-remove-nodes.md)。

