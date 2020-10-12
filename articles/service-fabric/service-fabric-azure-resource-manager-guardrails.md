---
title: Service Fabric Azure Resource Manager 部署護欄
description: 本文概述透過 Azure Resource Manager 部署 Service Fabric 叢集時所發生的常見錯誤，以及如何避免這些問題。
services: service-fabric
documentationcenter: .net
author: peterpogorski
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: pepogors
ms.openlocfilehash: aa1499c57ead28bfcee90a2f224ef9c3bb1d7f58
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86247819"
---
# <a name="service-fabric-guardrails"></a>Service Fabric 護欄 
部署 Service Fabric 叢集時，護欄會就地放置，如果叢集設定無效，將會導致 Azure Resource Manager 部署失敗。 下列各節概述常見的叢集設定問題，以及減輕這些問題所需的步驟。 

## <a name="durability-mismatch"></a>持久性不相符
### <a name="overview"></a>概觀
Service Fabric 節點類型的持久性值是在 Azure Resource Manager 範本的兩個不同區段中定義。 虛擬機器擴展集資源的虛擬機器擴展集擴充功能區段，以及 Service Fabric 叢集資源的節點類型區段。 這些區段中的持久性值必須相符，否則資源部署將會失敗。

下一節包含虛擬機器擴展集延伸耐久性設定和 Service Fabric 節點類型耐久性設定之間的持久性不相符的範例：  

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
* 虛擬機器擴展集耐久性不符，與目前的 Service Fabric 節點類型耐久性等級不符
* 虛擬機器擴展集耐久性與目標 Service Fabric 節點類型耐久性等級不符
* 虛擬機器擴展集耐久性符合目前的 Service Fabric 耐久性等級或目標 Service Fabric 節點類型耐久性等級 

### <a name="mitigation"></a>降低
若要修正耐久性不符的問題，請使用上述任何錯誤訊息來表示：
1. 在 Azure Resource Manager 範本的虛擬機器擴展集擴充功能或 Service Fabric 節點類型] 區段中，更新持久性層級，以確定值相符。
2. 使用更新的值重新部署 Azure Resource Manager 範本。


## <a name="seed-node-deletion"></a>刪除種子節點 
### <a name="overview"></a>概觀
Service Fabric 叢集有一個 [可靠性層級](./service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) 屬性，用來判斷在叢集的主要節點類型上執行的系統服務複本數目。 必要複本的數目會決定叢集的主要節點類型中必須維持的節點數目下限。 如果主要節點類型中的節點數目低於可靠性層級所需的最小值，叢集將會變得不穩定。  

### <a name="error-messages"></a>錯誤訊息 
偵測到種子節點移除作業，而且將會遭到拒絕。 
* 這項作業會導致只有 {0} 可能的種子節點保留在叢集中，而且 {1} 需要最少的種子節點。
* 移除的 {0} 種子節點 {1} 會導致叢集因為遺失種子節點仲裁而停止運作。 可以一次移除的種子節點數目上限為 {2} 。
 
### <a name="mitigation"></a>降低 
確定您的主要節點類型具有足夠的虛擬機器，以滿足您在叢集上指定的可靠性。 如果虛擬機器將虛擬機器擴展集放在指定的可靠性層級的節點數目下限以下，您將無法移除虛擬機器。
* 如果可靠性層級已正確指定，請確定您的主要節點類型中有足夠的節點，以確保可靠性層級的需求。 
* 如果可靠性層級不正確，請先在 Service Fabric 資源上起始變更，以先降低可靠性層級，再起始任何虛擬機器擴展集作業，並等候它完成。
* 如果可靠性層級為銅級，請遵循下列 [步驟](./service-fabric-cluster-scale-in-out.md#manually-remove-vms-from-a-node-typevirtual-machine-scale-set) ，正常地在您的叢集中調整規模。

## <a name="next-steps"></a>接下來的步驟
* 在執行 Windows Server 的 VM 或電腦上建立叢集： [建立適用於 Windows Server 的 Service Fabric 叢集](service-fabric-cluster-creation-for-windows-server.md)
* 在 VM 或執行 Linux 的電腦上建立叢集：[建立 Linux 叢集](service-fabric-cluster-creation-via-portal.md)
* 疑難排解 Service Fabric： [疑難排解指南](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
