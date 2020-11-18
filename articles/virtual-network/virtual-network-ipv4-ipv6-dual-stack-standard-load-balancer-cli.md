---
title: 部署 IPv6 雙重堆疊應用程式-Standard Load Balancer-CLI
titlesuffix: Azure Virtual Network
description: 本文說明如何使用 Azure CLI 在 Azure 虛擬網路中部署 IPv6 雙重堆疊應用程式。
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
ms.openlocfilehash: e4676ec54165c94663ec5ad76d4c43d7fabfc717
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94742244"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure-virtual-network---cli"></a>在 Azure 虛擬網路中部署 IPv6 雙重堆疊應用程式-CLI

本文說明如何使用 Azure 中的 Standard Load Balancer，在 Azure 中部署雙重堆疊 (IPv4 + IPv6) 應用程式，其中包含雙重堆疊子網的雙重堆疊虛擬網路、雙 (IPv4 + IPv6) 前端設定的 Standard Load Balancer、具有雙重 IP 設定的 Nic、雙網路安全性群組規則，以及雙公用 Ip 的虛擬機器。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 Azure CLI 的版本2.0.49 版或更新版本。 如果您是使用 Azure Cloud Shell，就已安裝最新版本。

## <a name="create-a-resource-group"></a>建立資源群組

您必須先建立具有 [az group create](/cli/azure/group)的資源群組，才能建立雙重堆疊虛擬網路。 下列範例會在 *eastus* 位置建立名為 *DsResourceGroup01* 的資源群組：

```azurecli-interactive
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>建立負載平衡器的 IPv4 和 IPv6 公用 IP 位址
若要存取網際網路上的 IPv4 和 IPv6 端點，您需要負載平衡器的 IPv4 和 IPv6 公用 IP 位址。 使用 [az network public-ip create](/cli/azure/network/public-ip) 建立公用 IP 位址。 下列範例會在 *DsResourceGroup01* 資源群組中建立名為 *dsPublicIP_v4* 和 *dsPublicIP_v6* 的 IPv4 和 IPv6 公用 IP 位址：

```azurecli-interactive
# Create an IPV4 IP address
az network public-ip create \
--name dsPublicIP_v4  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku STANDARD  \
--allocation-method static  \
--version IPv4

# Create an IPV6 IP address
az network public-ip create \
--name dsPublicIP_v6  \
--resource-group DsResourceGroup01  \
--location eastus \
--sku STANDARD  \
--allocation-method static  \
--version IPv6

```

## <a name="create-public-ip-addresses-for-vms"></a>建立 Vm 的公用 IP 位址

若要從遠端存取您在網際網路上的 Vm，您需要 Vm 的 IPv4 公用 IP 位址。 使用 [az network public-ip create](/cli/azure/network/public-ip) 建立公用 IP 位址。

```azurecli-interactive
az network public-ip create \
--name dsVM0_remote_access  \
--resource-group DsResourceGroup01 \
--location eastus  \
--sku Standard  \
--allocation-method static  \
--version IPv4

az network public-ip create \
--name dsVM1_remote_access  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku Standard  \
--allocation-method static  \
--version IPv4
```

## <a name="create-standard-load-balancer"></a>建立標準負載平衡器

在本節中，您會為負載平衡器設定雙重前端 IP (IPv4 和 IPv6) 和後端位址集區，然後建立 Standard Load Balancer。

### <a name="create-load-balancer"></a>建立負載平衡器

使用 [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) 命名 **dsLB** 建立 Standard Load Balancer，其中包含名為 **dsLbFrontEnd_v4** 的前端集區，這是一個名為 **dsLbBackEndPool_v4** 的後端集區，與您在上一個步驟中建立的 IPv4 公用 IP 位址 **dsPublicIP_v4** 相關聯。 

```azurecli-interactive
az network lb create \
--name dsLB  \
--resource-group DsResourceGroup01 \
--sku Standard \
--location eastus \
--frontend-ip-name dsLbFrontEnd_v4  \
--public-ip-address dsPublicIP_v4  \
--backend-pool-name dsLbBackEndPool_v4
```

### <a name="create-ipv6-frontend"></a>建立 IPv6 前端

建立具有 [az network lb 前端 ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create)的 IPV6 前端 ip。 下列範例會建立名為 *dsLbFrontEnd_v6* 的前端 IP 設定，並附加 *dsPublicIP_v6* 位址：

```azurecli-interactive
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>設定 IPv6 後端位址集區

使用 [az network lb address pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create)建立 IPv6 後端位址集區。 下列範例會建立名為 *dsLbBackEndPool_v6*  的後端位址集區，以包含具有 IPv6 NIC 配置的 vm：

```azurecli-interactive
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01
```

### <a name="create-a-health-probe"></a>建立健康狀態探查
使用 [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) 建立健康狀態探查，以檢視虛擬機器的健康狀態。 

```azurecli-interactive
az network lb probe create -g DsResourceGroup01  --lb-name dsLB -n dsProbe --protocol tcp --port 3389
```

### <a name="create-a-load-balancer-rule"></a>建立負載平衡器規則

負載平衡器規則用來定義如何將流量分散至 VM。 您可定義連入流量的前端 IP 組態及後端 IP 集區來接收流量，以及所需的來源和目的地連接埠。 

使用 [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create) 建立負載平衡器規則。 下列範例會建立名為 *dsLBrule_v4* 的負載平衡器規則，並 *dsLBrule_v6* ，並將 *TCP* 通訊埠 *80* 上的流量平衡至 IPv4 和 IPv6 前端 IP 設定：

```azurecli-interactive
az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v4  \
--resource-group DsResourceGroup01  \
--frontend-ip-name dsLbFrontEnd_v4  \
--protocol Tcp  \
--frontend-port 80  \
--backend-port 80  \
--probe-name dsProbe \
--backend-pool-name dsLbBackEndPool_v4


az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v6  \
--resource-group DsResourceGroup01 \
--frontend-ip-name dsLbFrontEnd_v6  \
--protocol Tcp  \
--frontend-port 80 \
--backend-port 80  \
--probe-name dsProbe \
--backend-pool-name dsLbBackEndPool_v6

```

## <a name="create-network-resources"></a>建立網路資源
部署一些 Vm 之前，您必須先建立支援的網路資源-可用性設定組、網路安全性群組、虛擬網路和虛擬 Nic。 
### <a name="create-an-availability-set"></a>建立可用性設定組
若要改善應用程式的可用性，請將您的 Vm 放在可用性設定組中。

使用 [az vm availability-set create](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest) 建立可用性設定組。 下列範例會建立名為 *dsAVset* 的可用性設定組：

```azurecli-interactive
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2  
```

### <a name="create-network-security-group"></a>建立網路安全性群組

針對將在您的 VNet 中管理輸入和輸出通訊的規則，建立網路安全性群組。

#### <a name="create-a-network-security-group"></a>建立網路安全性群組

使用[az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)建立網路安全性群組


```azurecli-interactive
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>針對輸入和輸出連接建立網路安全性群組規則

建立網路安全性群組規則，以允許透過埠3389的 RDP 連線、透過埠80的網際網路連線，以及使用 [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create)的輸出連線。

```azurecli-interactive
# Create inbound rule for port 3389
az network nsg rule create \
--name allowRdpIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 100  \
--description "Allow Remote Desktop In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges 3389

# Create inbound rule for port 80
az network nsg rule create \
--name allowHTTPIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 200  \
--description "Allow HTTP In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges 80  \
--destination-address-prefixes "*"  \
--destination-port-ranges 80

# Create outbound rule

az network nsg rule create \
--name allowAllOut  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 300  \
--description "Allow All Out"  \
--access Allow  \
--protocol "*"  \
--direction Outbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges "*"
```


### <a name="create-a-virtual-network"></a>建立虛擬網路

使用 [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create) 建立虛擬網路。 下列範例會建立一個名為 *dsVNET* 的虛擬網路，其中包含 *dsSubNET_v4* 和 *dsSubNET_v6* 的子網：

```azurecli-interactive
# Create the virtual network
az network vnet create \
--name dsVNET \
--resource-group DsResourceGroup01 \
--location eastus  \
--address-prefixes "10.0.0.0/16" "ace:cab:deca::/48"

# Create a single dual stack subnet

az network vnet subnet create \
--name dsSubNET \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--address-prefixes "10.0.0.0/24" "ace:cab:deca:deed::/64" \
--network-security-group dsNSG1
```

### <a name="create-nics"></a>建立 NIC

使用 [az network nic create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create)來為每個 VM 建立虛擬 nic。 下列範例會為每個 VM 建立虛擬 NIC。 每個 NIC 都有兩個 IP 設定 (1 個 IPv4 設定，1個 IPv6 配置) 。 您可以使用 [az network nic ip-config create](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create)建立 IPV6 設定。
 
```azurecli-interactive
# Create NICs
az network nic create \
--name dsNIC0  \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1  \
--vnet-name dsVNET  \
--subnet dsSubNet  \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4  \
--lb-name dsLB  \
--public-ip-address dsVM0_remote_access

az network nic create \
--name dsNIC1 \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4 \
--lb-name dsLB \
--public-ip-address dsVM1_remote_access

# Create IPV6 configurations for each NIC

az network nic ip-config create \
--name dsIp6Config_NIC0  \
--nic-name dsNIC0  \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name dsNIC1 \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB
```

### <a name="create-virtual-machines"></a>建立虛擬機器

使用 [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) 建立 VM。 下列範例會建立兩個 VM 及必要的虛擬網路元件 (如果尚未存在)。 

建立虛擬機器 *dsVM0* ，如下所示：

```azurecli-interactive
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest  
```

建立虛擬機器 *dsVM1* ，如下所示：

```azurecli-interactive
az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest 
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>在 Azure 入口網站中查看 IPv6 雙重堆疊虛擬網路
您可以在 Azure 入口網站中看到 IPv6 雙重堆疊虛擬網路，如下所示：
1. 在入口網站的搜尋列中，輸入 *dsVnet*。
2. 當搜尋結果中出現 **myVirtualNetwork** 時加以選取。 這會啟動名為 *dsVnet* 的雙重堆疊虛擬網路的 [**總覽**] 頁面。 雙重堆疊虛擬網路會顯示兩個 Nic，兩者皆位於名為 *dsSubnet* 的雙重堆疊子網中的 IPv4 和 IPv6 設定。

  ![Azure 中的 IPv6 雙重堆疊虛擬網路](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)

## <a name="clean-up-resources"></a>清除資源

若不再需要，您可以使用 [az group delete](/cli/azure/group#az-group-delete) 命令來移除資源群組、VM 和所有相關資源。

```azurecli-interactive
 az group delete --name DsResourceGroup01
```

## <a name="next-steps"></a>後續步驟

在本文中，您已建立具有雙重前端 IP 設定 (IPv4 和 IPv6) 的 Standard Load Balancer。 您也建立了兩部虛擬機器，其中包含雙 IP 設定 (IPV4 + IPv6) 的 Nic，並已新增至負載平衡器的後端集區。 若要深入瞭解 Azure 虛擬網路中的 IPv6 支援，請參閱 [什麼是 Azure 虛擬網路的 ipv6？](ipv6-overview.md)
