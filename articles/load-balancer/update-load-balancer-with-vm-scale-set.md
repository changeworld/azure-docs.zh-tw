---
title: 更新或刪除虛擬機器擴展集所使用的現有 Azure Load Balancer
titleSuffix: Update or delete existing Azure Load Balancer used by Virtual Machine Scale Set
description: 透過此操作說明文章，開始使用 Azure Standard Load Balancer 和虛擬機器擴展集。
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/30/2020
ms.author: irenehua
ms.openlocfilehash: f8f664375e53a1cef28b0c7b95207770434f67fa
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97893258"
---
# <a name="how-to-updatedelete-azure-load-balancer-used-by-virtual-machine-scale-sets"></a>如何更新/刪除虛擬機器擴展集所使用的 Azure Load Balancer

## <a name="how-to-set-up-azure-load-balancer-for-scaling-out-virtual-machine-scale-sets"></a>如何設定擴充虛擬機器擴展集的 Azure Load Balancer
  * 請確定 Load Balancer 已設定 [輸入 NAT](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest) 集區，且虛擬機器擴展集放在 Load Balancer 的後端集區中。 當新的虛擬機器實例新增至虛擬機器擴展集時，Azure Load Balancer 將會自動在輸入 NAT 集區中建立新的輸入 NAT 規則。 
  * 若要檢查是否已正確設定輸入 NAT 集區， 
  1. 在 https://portal.azure.com 登入 Azure 入口網站。
  
  1. 選取左側功能表上的 [所有資源]，然後從資源清單中選取 [MyLoadBalancer]。
  
  1. 在 [ **設定**] 底下，選取 [ **輸入 NAT 規則**]。
如果您在右窗格中看到針對虛擬機器擴展集中的每個個別實例所建立的規則清單，則您的所有恭喜都已設定為在任何時間進行擴充。

## <a name="how-to-add-inbound-nat-rules"></a>如何新增輸入 NAT 規則？ 
  * 無法新增個別的輸入 NAT 規則。 不過，您可以針對虛擬機器擴展集中的所有實例，使用已定義的前端埠範圍和後端埠來新增一組輸入 NAT 規則。
  * 若要為虛擬機器擴展集新增一組完整的輸入 NAT 規則，您必須先在 Load Balancer 中建立輸入 NAT 集區，然後從虛擬機器擴展集的網路設定檔中參考輸入 NAT 集區。 使用 CLI 的完整範例如下所示。
  * 新的輸入 NAT 集區不應與現有的輸入 NAT 集區具有重迭的前端埠範圍。 若要查看已設定的現有輸入 NAT 集區，您可以使用此 [CLI 命令](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest#az_network_lb_inbound_nat_pool_list)
```azurecli-interactive
az network lb inbound-nat-pool create 
        -g MyResourceGroup 
        --lb-name MyLb
        -n MyNatPool 
        --protocol Tcp 
        --frontend-port-range-start 80 
        --frontend-port-range-end 89 
        --backend-port 80 
        --frontend-ip-name MyFrontendIp
az vmss update 
        -g MyResourceGroup 
        -n myVMSS 
        --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool'}"
        
az vmss update-instances
        -–instance-ids *
        --resource-group MyResourceGroup
        --name MyVMSS
```
## <a name="how-to-update-inbound-nat-rules"></a>如何更新輸入 NAT 規則？ 
  * 無法更新個別的輸入 NAT 規則。 不過，您可以針對虛擬機器擴展集中的所有實例，使用已定義的前端埠範圍和後端埠來更新一組輸入 NAT 規則。
  * 若要更新虛擬機器擴展集的一組完整輸入 NAT 規則，您必須更新 Load Balancer 中的輸入 NAT 集區。 
```azurecli-interactive
az network lb inbound-nat-pool update 
        -g MyResourceGroup 
        --lb-name MyLb 
        -n MyNatPool
        --protocol Tcp 
        --backend-port 8080
```

## <a name="how-to-delete-inbound-nat-rules"></a>如何刪除輸入 NAT 規則？ 
* 無法刪除個別的輸入 NAT 規則。 不過，您可以刪除一組完整的輸入 NAT 規則。
* 若要刪除擴展集所使用的一組完整輸入 NAT 規則，您必須先從擴展集移除 NAT 集區。 使用 CLI 的完整範例如下所示：
```azurecli-interactive
  az vmss update
     --resource-group MyResourceGroup
     --name MyVMSS
   az vmss update-instances 
     --instance-ids "*" 
     --resource-group MyResourceGroup
     --name MyVMSS
  az network lb inbound-nat-pool delete
     --resource-group MyResourceGroup
     -–lb-name MyLoadBalancer
     --name MyNatPool
```

## <a name="how-to-add-multiple-ip-configurations"></a>如何新增多個 IP 設定：
1. 選取左側功能表上的 [所有資源]，然後從資源清單中選取 [MyLoadBalancer]。
   
1. 在 [ **設定**] 底下，選取 [ **前端 IP** 設定]，然後選取 [ **新增**]。
   
1. 在 [ **新增前端 IP 位址** ] 頁面上，輸入值並選取 **[確定]**

1. 如果需要新的負載平衡規則，請遵循本教學課程中的[步驟 5](https://docs.microsoft.com/azure/load-balancer/load-balancer-multiple-ip#step-5-configure-the-health-probe)和[步驟 6](https://docs.microsoft.com/azure/load-balancer/load-balancer-multiple-ip#step-5-configure-the-health-probe)

1. 若有需要，請使用新建立的前端 IP 設定，建立一組新的輸入 NAT 規則。 您可以在 [上一節] 中找到範例。

## <a name="how-to-delete-frontend-ip-configuration-used-by-virtual-machine-scale-set"></a>如何刪除虛擬機器擴展集所使用的前端 IP 設定： 
 1. 若要刪除擴展集使用的前端 IP 設定，您必須先刪除輸入 nat 集區 (組) 參考前端 IP 設定的輸入 NAT 規則。 在上一節中可以找到如何刪除輸入規則的指示。
 1. 刪除參考前端 IP 設定的負載平衡規則。 
 1. 刪除前端 IP 設定。
 

## <a name="how-to-delete-azure-load-balancer-used-by-virtual-machine-scale-set"></a>如何刪除虛擬機器擴展集所使用的 Azure Load Balancer： 
 1. 若要刪除擴展集使用的前端 IP 設定，您必須先刪除輸入 nat 集區 (組) 參考前端 IP 設定的輸入 NAT 規則。 在上一節中可以找到如何刪除輸入規則的指示。
 
 1. 刪除參考包含虛擬機器擴展集之後端集區的負載平衡規則。
 
 1. 從虛擬機器擴展集的網路設定檔中移除 loadBalancerBackendAddressPool 參考。 使用 CLI 的完整範例如下所示：
 ```azurecli-interactive
  az vmss update
     --resource-group MyResourceGroup
     --name MyVMSS
     --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools
  az vmss update-instances 
     --instance-ids "*" 
     --resource-group MyResourceGroup
     --name MyVMSS
```
最後，刪除 Load Balancer 資源。
 
## <a name="next-steps"></a>後續步驟

若要深入瞭解 Azure Load Balancer 和虛擬機器擴展集，請參閱概念的詳細資訊。

> [使用 Azure 虛擬機器擴展集 Azure Load Balancer](load-balancer-standard-virtual-machine-scale-sets.md)
