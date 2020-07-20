---
title: 將 IPv6 新增至 Azure 虛擬網路中的 IPv4 應用程式-Azure CLI
titlesuffix: Azure Virtual Network
description: 本文說明如何使用 Azure CLI，將 IPv6 位址部署到 Azure 虛擬網路中的現有應用程式。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 0ba8e34c1fb219d86086e73203acf65c2351c340
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708343"
---
# <a name="add-ipv6-to-an-ipv4-application-in-azure-virtual-network---azure-cli"></a>將 IPv6 新增至 Azure 虛擬網路中的 IPv4 應用程式-Azure CLI

本文說明如何在 Azure 虛擬網路中使用 IPv4 公用 IP 位址的應用程式中，為使用 Azure CLI 的 Standard Load Balancer 新增 IPv6 位址。 就地升級包含一個虛擬網路和子網、一個 Standard Load Balancer，其中包含 IPv4 + IPV6 前端設定、具有 IPv4 + IPv6 設定的 Vm、網路安全性群組和公用 Ip。


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您決定改為在本機安裝和使用 CLI，本快速入門會要求您使用 Azure CLI 2.0.28 版或更新版本。 若要尋找您安裝的版本，請執行 `az --version`。 如需安裝或升級的資訊，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="prerequisites"></a>必要條件

本文假設您已部署 Standard Load Balancer，如[快速入門：建立 Standard Load Balancer Azure CLI](../load-balancer/quickstart-load-balancer-standard-public-cli.md)中所述。

## <a name="create-ipv6-addresses"></a>建立 IPv6 位址

建立公用 IPv6 位址，並使用[az network public-ip create 來建立](/cli/azure/network/public-ip)您的 Standard Load Balancer。 下列範例會在*myResourceGroupSLB*資源群組中建立名為*PublicIP_v6*的 IPv6 公用 IP 位址：

```azurecli
  
az network public-ip create \
--name PublicIP_v6 \
--resource-group MyResourceGroupSLB \
--location EastUS \
--sku Standard \
--allocation-method static \
--version IPv6
```

## <a name="configure-ipv6-load-balancer-frontend"></a>設定 IPv6 負載平衡器前端

使用[az network lb 前端-IP create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create) ，以新的 IPv6 IP 位址設定負載平衡器，如下所示：

```azurecli
az network lb frontend-ip create \
--lb-name myLoadBalancer \
--name dsLbFrontEnd_v6 \
--resource-group MyResourceGroupSLB \
--public-ip-address PublicIP_v6
```

## <a name="configure-ipv6-load-balancer-backend-pool"></a>設定 IPv6 負載平衡器後端集區

使用[az network lb 位址集區建立](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create)，為具有 IPv6 位址的 nic 建立後端集區，如下所示：

```azurecli
az network lb address-pool create \
--lb-name myLoadBalancer \
--name dsLbBackEndPool_v6 \
--resource-group MyResourceGroupSLB
```

## <a name="configure-ipv6-load-balancer-rules"></a>設定 IPv6 負載平衡器規則

使用[az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create)建立 IPv6 負載平衡器規則。

```azurecli
az network lb rule create \
--lb-name myLoadBalancer \
--name dsLBrule_v6 \
--resource-group MyResourceGroupSLB \
--frontend-ip-name dsLbFrontEnd_v6 \
--protocol Tcp \
--frontend-port 80 \
--backend-port 80 \
--backend-pool-name dsLbBackEndPool_v6
```

## <a name="add-ipv6-address-ranges"></a>新增 IPv6 位址範圍

將 IPv6 位址範圍新增至裝載負載平衡器的虛擬網路和子網，如下所示：

```azurecli
az network vnet update \
--name myVnet  `
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/16"  "ace:cab:deca::/48"

az network vnet subnet update \
--vnet-name myVnet \
--name mySubnet \
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/24"  "ace:cab:deca:deed::/64"  
```

## <a name="add-ipv6-configuration-to-nics"></a>將 IPv6 設定新增至 Nic

使用[az network nic ip-config create](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create)設定具有 IPv6 位址的 VM nic，如下所示：

```azurecli
az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name myNicVM1 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC2 \
--nic-name myNicVM2 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer

az network nic ip-config create \
--name dsIp6Config_NIC3 \
--nic-name myNicVM3 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer

```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>在 Azure 入口網站中查看 IPv6 雙重堆疊虛擬網路
您可以在 Azure 入口網站中查看 IPv6 雙重堆疊虛擬網路，如下所示：
1. 在入口網站的搜尋列中，輸入*myVnet*。
2. 當**myVnet**出現在搜尋結果中時，請選取它。 這會啟動名為*myVNet*的雙重堆疊虛擬網路的 [**總覽**] 頁面。 雙重堆疊虛擬網路會顯示三個 Nic，其中 IPv4 和 IPv6 設定都位於名為*mySubnet*的雙重堆疊子網中。

  ![Azure 中的 IPv6 雙重堆疊虛擬網路](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)


## <a name="clean-up-resources"></a>清除資源

當不再需要時，您可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令來移除資源群組、VM 及所有相關資源。

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>後續步驟

在本文中，您已將具有 IPv4 前端 IP 設定的現有 Standard Load Balancer 更新為雙重堆疊（IPv4 和 IPv6）設定。 您也會將 IPv6 設定新增至後端集區中 Vm 的 Nic。 若要深入瞭解 Azure 虛擬網路中的 IPv6 支援，請參閱[什麼是適用于 azure 虛擬網路的 ipv6？](ipv6-overview.md)
