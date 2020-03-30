---
title: 服務結構 Azure 資源管理器部署護欄
description: 本文概述了通過 Azure 資源管理器部署 Service Fabric 群集時出現的常見錯誤以及如何避免這些錯誤。
services: service-fabric
documentationcenter: .net
author: peterpogorski
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: pepogors
ms.openlocfilehash: a61b0cf30ca46eb77837eb09d6a9a0b6f30e89a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368572"
---
# <a name="service-fabric-guardrails"></a>維修織物護欄 
部署 Service Fabric 群集時，將設置護欄，這樣在群集配置不正確情況下，Azure 資源管理器部署將失敗。 以下各節概述了常見的群集配置問題以及緩解這些問題所需的步驟。 

## <a name="durability-mismatch"></a>耐久性不匹配
### <a name="overview"></a>總覽
服務結構節點類型的持久性值在 Azure 資源管理器範本的兩個不同部分中定義。 虛擬機器縮放集資源的"虛擬機器縮放集"擴展部分以及服務結構群集資源的節點類型部分。 要求這些部分中的持久性值匹配，否則資源部署將失敗。

以下部分包含虛擬機器擴展耐久性設置與服務結構節點類型持久性設置之間的持久性不匹配示例：  

**虛擬機器縮放集持久性設置**
```json 
{
  "extensions": [
    {
      "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
      "properties": {
        "type": "ServiceFabricNode",
        "publisher": "Microsoft.Azure.ServiceFabric",
        "settings": {
          "durabilityLevel": "Silver",
        }
      }
    }
  ]
}
```

**維修結構節點類型耐久性設置** 
```json
{
  "nodeTypes": [
    {
      "name": "[variables('vmNodeType0Name')]",
      "durabilityLevel": "Bronze",
      "isPrimary": true,
      "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    }
  ]
}
```

### <a name="error-messages"></a>錯誤訊息
* 虛擬機器規模集耐久性不匹配與當前服務結構節點類型耐久性級別不匹配
* 虛擬機器規模集耐久性與目標服務結構節點類型耐久性級別不匹配
* 虛擬機器擴展集耐久性與當前服務結構耐久性級別或目標服務結構節點類型耐久性級別匹配 

### <a name="mitigation"></a>降低
要修復持久性不匹配（上述任何錯誤訊息指示）
1. 更新 Azure 資源管理器範本的虛擬機器縮放集擴展或服務結構節點類型部分中的持久性級別，以確保這些值匹配。
2. 使用更新的值重新部署 Azure 資源管理器範本。


## <a name="seed-node-deletion"></a>種子節點刪除 
### <a name="overview"></a>總覽
Service Fabric 群集具有[一個可靠性層](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster)屬性，用於確定在群集的主節點類型上運行的系統服務的副本數。 所需的副本數將確定必須在群集的主節點類型中維護的最小節點數。 如果主節點類型中的節點數低於可靠性層所需的最小值，群集將變得不穩定。  

### <a name="error-messages"></a>錯誤訊息 
已檢測到種子節點刪除操作，並將被拒絕。 
* 此操作將僅{0}導致潛在的種子節點保留在群集中，而{1}至少需要種子節點。
* 刪除{0}種子節點{1}將導致群集因種子節點仲裁丟失而關閉。 一次可以刪除的最大種子節點數為{2}。
 
### <a name="mitigation"></a>降低 
確保主節點類型有足夠的虛擬機器，以保證群集上指定的可靠性。 如果虛擬機器將設置為低於給定可靠性層的最小節點數，則無法刪除虛擬機器。
* 如果正確指定了可靠性層，請確保主節點類型中有足夠的節點，這是可靠性層所需的。 
* 如果可靠性層不正確，請先對 Service Fabric 資源啟動更改以降低可靠性級別，然後再啟動任何虛擬機器縮放集操作，然後等待它完成。
* 如果可靠性層為"青銅"，請按照[以下步驟](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#manually-remove-vms-from-a-node-typevirtual-machine-scale-set)正常縮小群集。

## <a name="next-steps"></a>後續步驟
* 在執行 Windows Server 的 VM 或電腦上建立叢集： [建立適用於 Windows Server 的 Service Fabric 叢集](service-fabric-cluster-creation-for-windows-server.md)
* 在 VM 或執行 Linux 的電腦上建立叢集：[建立 Linux 叢集](service-fabric-cluster-creation-via-portal.md)
* 故障排除服務結構：[故障排除指南](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
