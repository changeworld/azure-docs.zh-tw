---
title: 將 IPv6 新增到 Azure 虛擬網路中的 IPv4 應用程式 - Azure CLI
titlesuffix: Azure Virtual Network
description: 本文演示如何使用 Azure CLI 將 IPv6 位址部署到 Azure 虛擬網路中的現有應用程式。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: f3f9b32ea55f0ceebf08b22ccc7e2ceec0b6227e
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420796"
---
# <a name="add-ipv6-to-an-ipv4-application-in-azure-virtual-network---azure-cli"></a>將 IPv6 新增到 Azure 虛擬網路中的 IPv4 應用程式 - Azure CLI

本文介紹如何將 IPv6 位址添加到使用 Azure CLI 的標準負載均衡器的 Azure 虛擬網路中使用 IPv4 公共 IP 位址的應用程式。 就地升級包括虛擬網路和子網、具有 IPv4 + IPv6 前端配置的標準負載均衡器、具有 IPv4 + IPv6 配置的 NIC 的 VM、網路安全組和公共 IP。


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您決定改為在本機安裝和使用 CLI，本快速入門會要求您使用 Azure CLI 2.0.28 版或更新版本。 若要尋找您安裝的版本，請執行 `az --version`。 如需安裝或升級的資訊，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="prerequisites"></a>Prerequisites

本文假定部署了標準負載均衡器,如[快速入門:創建標準負載均衡器 - Azure CLI](../load-balancer/quickstart-load-balancer-standard-public-cli.md)。

## <a name="create-ipv6-addresses"></a>建立 IPv6 位址

使用[az 網路公共 ip](/cli/azure/network/public-ip)為您的標準負載均衡器創建公共 IPv6 位址。 以下範例在*myResourceGroupSLB*資源群組中建立名為*PublicIP_v6*的 IPv6 公共 IP 位址:

```azurecli
  
az network public-ip create \
--name PublicIP_v6 \
--resource-group MyResourceGroupSLB \
--location EastUS \
--sku Standard \
--allocation-method static \
--version IPv6
```

## <a name="configure-ipv6-load-balancer-frontend"></a>設定 IPv6 負載均衡器前端

使用[az 網路 lb 前端 ip](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create)建立新的 IPv6 IP 位址的負載平衡器,如下所示:

```azurecli
az network lb frontend-ip create \
--lb-name myLoadBalancer \
--name dsLbFrontEnd_v6 \
--resource-group MyResourceGroupSLB \
--public-ip-address PublicIP_v6
```

## <a name="configure-ipv6-load-balancer-backend-pool"></a>設定 IPv6 負載均衡器後端池

使用[az web lb 位址池建立](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create)具有 IPv6 位址的 NIC 後端池,如下所示:

```azurecli
az network lb address-pool create \
--lb-name myLoadBalancer \
--name dsLbBackEndPool_v6 \
--resource-group MyResourceGroupSLB
```

## <a name="configure-ipv6-load-balancer-rules"></a>設定 IPv6 負載均衡器規則

使用[az 網路 lb 規則創建](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create)IPv6 負載均衡器規則。

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

將 IPv6 位址範圍新增到承載負載均衡器的虛擬網路和子網,如下所示:

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

## <a name="add-ipv6-configuration-to-nics"></a>新增 IPv6 設定加入 NIC

使用[az 網路 nic ip 設定建立](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create)使用 IPv6 位址配置 VM NIC,如下所示:

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

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>在 Azure 門戶中查看 IPv6 雙堆疊虛擬網路
您可以在 Azure 門戶中查看 IPv6 雙堆疊虛擬網路,如下所示:
1. 在門戶的搜尋列中,輸入*myVnet*。
2. 當**myVnet**顯示在搜尋結果中時,請選擇它。 這將啟動名為*myVNet*的雙堆疊虛擬網路**的概述**頁面。 雙堆疊虛擬網路顯示三個 NIC,其中 IPv4 和 IPv6 配置都位於名為*mySubnet*的雙堆疊子網中。

  ![Azure 的 IPv6 雙堆疊虛擬網路](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)


## <a name="clean-up-resources"></a>清除資源

當不再需要時，您可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令來移除資源群組、VM 及所有相關資源。

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>後續步驟

在本文中,您將具有 IPv4 前端 IP 配置的現有標準負載均衡器更新為雙堆疊(IPv4 和 IPv6) 配置。 您還可以將 IPv6 配置添加到後端池中 VM 的 NIC。 要瞭解有關 Azure 虛擬網路中 IPv6 支援的更多資訊,請參閱[什麼是 Azure 虛擬網路的 IPv6?](ipv6-overview.md)
