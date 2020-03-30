---
title: 將 IPv6 添加到 Azure 虛擬網路中的 IPv4 應用程式 - Azure CLI
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
ms.date: 10/23/2019
ms.author: kumud
ms.openlocfilehash: 5dc231febc2e9b605b9e7f603f5d036b8a2c62eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240749"
---
# <a name="add-ipv6-to-an-ipv4-application-in-azure-virtual-network---azure-cli-preview"></a>將 IPv6 添加到 Azure 虛擬網路中的 IPv4 應用程式 - Azure CLI（預覽）

本文介紹如何將 IPv6 位址添加到使用 Azure CLI 的標準負載等化器的 Azure 虛擬網路中使用 IPv4 公共 IP 位址的應用程式。 就地升級包括虛擬網路和子網、具有 IPv4 + IPv6 前端配置的標準負載等化器、具有 IPv4 + IPv6 配置的 NIC 的 VM、網路安全性群組和公共 IP。

> [!Important]
> 對 Azure 虛擬網路的 IPv6 支援當前處於公共預覽版中。 此預覽版是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您決定改為在本機安裝和使用 CLI，本快速入門會要求您使用 Azure CLI 2.0.28 版或更新版本。 若要尋找您安裝的版本，請執行 `az --version`。 如需安裝或升級的資訊，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="prerequisites"></a>Prerequisites

### <a name="register-the-service"></a>註冊服務

在 Azure 中部署雙堆疊應用程式之前，必須使用以下 Azure CLI 為此預覽功能配置訂閱：

```azurecli
az feature register --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
az feature register --name AllowIPv6CAOnStandardLB --namespace Microsoft.Network
```

需要 30 分鐘才能完成功能註冊。 您可以通過運行以下 Azure CLI 命令來檢查註冊狀態：

```azurecli
az feature show --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
az feature show --name AllowIPv6CAOnStandardLB --namespace Microsoft.Network
```

註冊完成之後，請執行下列命令：

```azurecli
az provider register --namespace Microsoft.Network
```

### <a name="create-a-standard-load-balancer"></a>建立標準負載平衡器
本文假定部署了標準負載等化器，如[快速入門：創建標準負載等化器 - Azure CLI](../load-balancer/quickstart-load-balancer-standard-public-cli.md)。

## <a name="create-ipv6-addresses"></a>創建 IPv6 位址

使用[az 網路公共 ip](/cli/azure/network/public-ip)為您的標準負載等化器創建公共 IPv6 位址。 以下示例在*myResourceGroupSLB*資源組中創建名為*PublicIP_v6*的 IPv6 公共 IP 位址：

```azurecli
  
az network public-ip create \
--name PublicIP_v6 \
--resource-group MyResourceGroupSLB \
--location EastUS \
--sku Standard \
--allocation-method static \
--version IPv6
```

## <a name="configure-ipv6-load-balancer-frontend"></a>配置 IPv6 負載等化器前端

使用[az 網路 lb 前端 ip](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create)創建新的 IPv6 IP 位址的負載平衡器，如下所示：

```azurecli
az network lb frontend-ip create \
--lb-name myLoadBalancer \
--name dsLbFrontEnd_v6 \
--resource-group MyResourceGroupSLB \
--public-ip-address PublicIP_v6
```

## <a name="configure-ipv6-load-balancer-backend-pool"></a>配置 IPv6 負載等化器後端池

使用[az 網路 lb 位址集區創建](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create)具有 IPv6 位址的 NIC 後端池，如下所示：

```azurecli
az network lb address-pool create \
--lb-name myLoadBalancer \
--name dsLbBackEndPool_v6 \
--resource-group MyResourceGroupSLB
```

## <a name="configure-ipv6-load-balancer-rules"></a>配置 IPv6 負載等化器規則

使用[az 網路 lb 規則創建](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create)IPv6 負載等化器規則。

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

## <a name="add-ipv6-address-ranges"></a>添加 IPv6 位址範圍

將 IPv6 位址範圍添加到承載負載等化器的虛擬網路和子網，如下所示：

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

## <a name="add-ipv6-configuration-to-nics"></a>將 IPv6 配置添加到 NIC

使用[az 網路 nic ip 配置創建](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create)使用 IPv6 位址配置 VM NIC，如下所示：

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
您可以在 Azure 門戶中查看 IPv6 雙堆疊虛擬網路，如下所示：
1. 在門戶的搜索欄中，輸入*myVnet*。
2. 當**myVnet**顯示在搜尋結果中時，請選擇它。 這將啟動名為*myVNet*的雙堆疊虛擬網路**的概述**頁面。 雙堆疊虛擬網路顯示三個 NIC，其中 IPv4 和 IPv6 配置都位於名為*mySubnet*的雙堆疊子網中。

  ![Azure 中的 IPv6 雙堆疊虛擬網路](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)

> [!NOTE]
> Azure 虛擬網路的 IPv6 在此預覽版本中以唯讀的 Azure 門戶提供。

## <a name="clean-up-resources"></a>清除資源

當不再需要時，您可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令來移除資源群組、VM 及所有相關資源。

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>後續步驟

在本文中，您將具有 IPv4 前端 IP 配置的現有標準負載等化器更新為雙堆疊（IPv4 和 IPv6） 配置。 您還可以將 IPv6 配置添加到後端池中 VM 的 NIC。 要瞭解有關 Azure 虛擬網路中 IPv6 支援的更多資訊，請參閱[什麼是 Azure 虛擬網路的 IPv6？](ipv6-overview.md)
