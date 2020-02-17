---
title: Service Fabric Azure Resource Manager 部署護欄
description: 本文概述透過 Azure Resource Manager 部署 Service Fabric 叢集時所發生的常見錯誤，以及如何避免這些問題。
services: service-fabric
documentationcenter: .net
author: peterpogorski
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: pepogors
ms.openlocfilehash: a61b0cf30ca46eb77837eb09d6a9a0b6f30e89a9
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368572"
---
# <a name="service-fabric-guardrails"></a>Service Fabric 護欄 
部署 Service Fabric 叢集時，會將護欄置中，這將會在叢集設定無效時，使 Azure Resource Manager 部署失敗。 下列各節提供常見叢集設定問題的總覽，以及減輕這些問題所需的步驟。 

## <a name="durability-mismatch"></a>持久性不符
### <a name="overview"></a>概觀
Service Fabric 節點類型的持久性值定義于 Azure Resource Manager 範本的兩個不同區段中。 虛擬機器擴展集資源的虛擬機器擴展集擴充功能區段，以及 Service Fabric 叢集資源的節點類型區段。 這些區段中的持久性值必須相符，否則資源部署將會失敗。

下一節包含虛擬機器擴展集擴充功能耐久性設定與 Service Fabric 節點類型耐久性設定之間的持久性不符範例：  

**虛擬機器擴展集耐久性設定**
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

**Service Fabric 節點類型耐久性設定** 
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
* 虛擬機器擴展集耐久性不相符不符合目前的 Service Fabric 節點類型耐久性層級
* 虛擬機器擴展集耐久性與目標 Service Fabric 節點類型耐久性層級不相符
* 虛擬機器擴展集耐久性符合目前的 Service Fabric 耐久性層級或目標 Service Fabric 節點類型耐久性層級 

### <a name="mitigation"></a>降低
若要修正持久性不相符的問題，請使用上述任何錯誤訊息來表示：
1. 更新 [虛擬機器擴展集延伸模組] 或 [Azure Resource Manager 範本 Service Fabric 節點類型] 區段中的持久性層級，以確保這些值相符。
2. 使用更新的值重新部署 Azure Resource Manager 範本。


## <a name="seed-node-deletion"></a>刪除種子節點 
### <a name="overview"></a>概觀
Service Fabric 叢集具有 [[可靠性層級](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster)] 屬性，可用來決定在叢集的主要節點類型上執行的系統服務複本數目。 必要的複本數目將決定必須在叢集的主要節點類型中維護的節點數目下限。 如果主要節點類型中的節點數目低於可靠性層級的最低需求，叢集將會變得不穩定。  

### <a name="error-messages"></a>錯誤訊息 
已偵測到種子節點移除作業，將會拒絕此作業。 
* 這項作業只會導致 {0} 可能的種子節點保留在叢集中，而 {1} 最少需要。
* 從 {1} 移除 {0} 種子節點，會導致叢集因為遺失種子節點仲裁而關閉。 一次可以移除的種子節點數目上限為 {2}。
 
### <a name="mitigation"></a>降低 
請確定您的主要節點類型有足夠的虛擬機器，以滿足您叢集上所指定的可靠性。 如果虛擬機器會將虛擬機器擴展集帶入指定之可靠性層級的最小節點數目，您將無法將其移除。
* 如果已正確指定可靠性層級，請確定您的主要節點類型中有足夠的節點，以確保可靠性層級的需求。 
* 如果可靠性層級不正確，在起始任何虛擬機器擴展集作業之前，先在 Service Fabric 資源上起始變更，以降低可靠性層級，並等候它完成。
* 如果可靠性層級為銅，請遵循下列[步驟](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#manually-remove-vms-from-a-node-typevirtual-machine-scale-set)，正常地相應減少叢集。

## <a name="next-steps"></a>後續步驟
* 在執行 Windows Server 的 VM 或電腦上建立叢集： [建立適用於 Windows Server 的 Service Fabric 叢集](service-fabric-cluster-creation-for-windows-server.md)
* 在 VM 或執行 Linux 的電腦上建立叢集：[建立 Linux 叢集](service-fabric-cluster-creation-via-portal.md)
* 疑難排解 Service Fabric：[疑難排解指南](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
