---
title: Azure Standard Load Balancer 和虛擬機器擴展集
titleSuffix: Azure Standard Load Balancer and Virtual Machine Scale Sets
description: 透過此學習路徑，開始使用 Azure Standard Load Balancer 和虛擬機器擴展集。
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2020
ms.author: irenehua
ms.openlocfilehash: fdca40d5113f06d185255be2e237cb52b47e9793
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94697436"
---
# <a name="azure-load-balancer-with-azure-virtual-machine-scale-sets"></a>使用 Azure 虛擬機器擴展集 Azure Load Balancer

使用虛擬機器擴展集與負載平衡器時，應考慮下列指導方針：

## <a name="port-forwarding-and-inbound-nat-rules"></a>埠轉送和輸入 NAT 規則：
  * 建立擴展集之後，就無法針對負載平衡器健康情況探查所使用的負載平衡規則，修改後端埠。 若要變更通訊埠，您可以藉由更新 Azure 虛擬機器擴展集來移除健康情況探查、更新埠，然後再次設定健康情況探查。
  * 在負載平衡器的後端集區中使用虛擬機器擴展集時，會自動建立預設的輸入 NAT 規則。
## <a name="inbound-nat-pool"></a>輸入 NAT 集區：
  * 每個虛擬機器擴展集至少必須有一個輸入 NAT 集區。 
  * 輸入 NAT 集區是輸入 NAT 規則的集合。 一個輸入 NAT 集區無法支援多個虛擬機器擴展集。
  * 若要從現有的虛擬機器擴展集刪除 NAT 集區，您必須先從擴展集移除 NAT 集區。 使用 CLI 的完整範例如下所示：
```azurecli-interactive
  az vmss update
     --resource-group MyResourceGroup
     --name MyVMSS
     --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools
  az vmss update-instances
     -–instance-ids *
     --resource-group MyResourceGroup
     --name MyVMSS
  az network lb inbound-nat-pool delete
     --resource-group MyResourceGroup
     -–lb-name MyLoadBalancer
     --name MyNatPool
```
## <a name="load-balancing-rules"></a>負載平衡規則：
  * 在負載平衡器的後端集區中使用虛擬機器擴展集時，會自動建立預設的負載平衡規則。
## <a name="outbound-rules"></a>輸出規則：
  *  若要為已由負載平衡規則參考的後端集區建立輸出規則，您必須先在建立輸入負載平衡規則時，將 [ **建立隱含輸出規則]** 標示為 [ **否** ]。

  :::image type="content" source="./media/vm-scale-sets/load-balancer-and-vm-scale-sets.png" alt-text="建立負載平衡規則" border="true":::

您可以使用下列方法，以現有的 Azure 負載平衡器部署虛擬機器擴展集。

* [使用 Azure 入口網站來設定具有現有 Azure Load Balancer 的虛擬機器擴展集](./configure-vm-scale-set-portal.md)。
* [使用 Azure PowerShell 來設定具有現有 Azure Load Balancer 的虛擬機器擴展集](./configure-vm-scale-set-powershell.md)。
* [使用 Azure CLI 來設定具有現有 Azure Load Balancer 的虛擬機器擴展集](./configure-vm-scale-set-cli.md)。