---
title: 部署 IPv6 雙堆疊應用程式 - 基本負載均衡器 - CLI
titlesuffix: Azure Virtual Network
description: 本文演示如何使用 Azure CLI 在 Azure 虛擬網路中部署 IPv6 雙堆疊應用程式。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 396c37d4c8de6a890102e435c5ec6cc70b598638
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421027"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-basic-load-balancer---cli"></a>使用基本負載均衡器部署 IPv6 雙堆疊應用程式 - CLI

本文介紹如何使用 Azure CLI 使用基本負載均衡器部署雙堆疊 (IPv4 + IPv6) 應用程式,該應用程式包括具有雙堆疊子網的雙堆疊虛擬網路、具有雙 (IPv4 + IPv6) 前端配置的基本負載均衡器、具有雙 IP 配置的 NIC 的 VM、雙網路安全組規則和雙公共 IP。

要使用標準負載均衡器部署雙堆疊 (IPV4+ IPv6) 應用程式,請參閱[使用 Azure CLI 使用標準負載均衡器部署 IPv6 雙堆疊應用程式](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md)。


如果您沒有 Azure 訂用帳戶，請立即建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您決定在本地安裝和使用 Azure CLI,則此快速入門要求您使用 Azure CLI 版本 2.0.49 或更高版本。 若要尋找您安裝的版本，請執行 `az --version`。 如需安裝或升級的資訊，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>建立資源群組

在建立雙堆疊虛擬網路之前,必須建立具有[az 群組的資源群組](/cli/azure/group)。 下面的範例*在東部*位置建立名為*DsResourceGroup01*的資源組:

```azurecli
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>為負載均衡器建立 IPv4 和 IPv6 公共 IP 位址
要存取 Internet 上的 IPv4 和 IPv6 端點,您需要負載均衡器的 IPv4 和 IPv6 公共 IP 位址。 使用 [az network public-ip create](/cli/azure/network/public-ip) 建立公用 IP 位址。 下面的範例在*DsResourceGroup01*資源群組中建立名為*dsPublicIP_v4*和*dsPublicIP_v6*的 IPv4 和 IPv6 公共 IP 位址:

```azurecli
# Create an IPV4 IP address
az network public-ip create \
--name dsPublicIP_v4  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

# Create an IPV6 IP address
az network public-ip create \
--name dsPublicIP_v6  \
--resource-group DsResourceGroup01  \
--location eastus \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv6

```

## <a name="create-public-ip-addresses-for-vms"></a>為 VM 建立公共 IP 位址

要在 Internet 上遠端存取 VM,您需要 VM 的 IPv4 公共 IP 位址。 使用 [az network public-ip create](/cli/azure/network/public-ip) 建立公用 IP 位址。

```azurecli
az network public-ip create \
--name dsVM0_remote_access  \
--resource-group DsResourceGroup01 \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

az network public-ip create \
--name dsVM1_remote_access  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4
```

## <a name="create-basic-load-balancer"></a>建立基本負載平衡器

在本節中,您可以為負載均衡器配置雙前端 IP(IPv4 和 IPv6)和後端位址池,然後創建基本負載均衡器。

### <a name="create-load-balancer"></a>建立負載平衡器

使用 az 網路 lb 建立基本負載均衡器[,創建](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest)名為 dsLB 的命名**dsLB,** 其中包括名為**dsLbFrontEnd_v4**的前端池,名為**dsLbBackEndPool_v4**的後端池,該後端池與您在前面的步驟中創建的 IPv4 公共 IP 位址**dsPublicIP_v4**相關聯。 

```azurecli
az network lb create \
--name dsLB  \
--resource-group DsResourceGroup01 \
--sku Basic \
--location eastus \
--frontend-ip-name dsLbFrontEnd_v4  \
--public-ip-address dsPublicIP_v4  \
--backend-pool-name dsLbBackEndPool_v4
```

### <a name="create-ipv6-frontend"></a>建立 IPv6 前端介面

建立具有 az 網路 lb[前端 ip](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create)的 IPV6 前端 IP 建立 。 以下範例建立名為*dsLbFrontEnd_v6*的前端 IP 設定並附加*dsPublicIP_v6*位址:

```azurecli
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>設定 IPv6 後端位址池

使用[az 網路 lb 位址池建立](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create)IPv6 後端位址池。 以下範例建立名為*dsLbBackEndPool_v6*的後端位址池,以包括具有 IPv6 NIC 設定的 VM:

```azurecli
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01
```

### <a name="create-a-health-probe"></a>建立健康狀態探查
創建使用 az[網路 lb 偵測器創建的](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest)執行狀況探測,以監視虛擬機器的執行狀況。 

```azurecli
az network lb probe create -g DsResourceGroup01  --lb-name dsLB -n dsProbe --protocol tcp --port 3389
```

### <a name="create-a-load-balancer-rule"></a>建立負載平衡器規則

負載平衡器規則用來定義如何將流量分散至 VM。 您可定義連入流量的前端 IP 組態及後端 IP 集區來接收流量，以及所需的來源和目的地連接埠。 

使用 [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create) 建立負載平衡器規則。 以下範例建立名為*dsLBrule_v4*的負載均衡器規則,並*dsLBrule_v6*並平衡*TCP*連接*埠 80*上的流量到 IPv4 和 IPv6 前端 IP 配置:

```azurecli
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
在部署某些 VM 之前,必須建立支援網路資源 -可用性集、網路安全組、虛擬網路和虛擬 NIC。 
### <a name="create-an-availability-set"></a>建立可用性設定組
要提高應用的可用性,請將 VM 放在可用性集中。

使用[az vm 可用性集建立](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest)可用性集。 下面的範例建立名為*dsAVset*可用性集:

```azurecli
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2  
```

### <a name="create-network-security-group"></a>建立網路安全性群組

為在 VNET 中管理入站和出站通信的規則創建網路安全組。

#### <a name="create-a-network-security-group"></a>建立網路安全性群組

建立具有[az 網路 nsg 建立的](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)網路安全群組


```azurecli
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>為入站與出站連接建立網路安全群組規則

創建網路安全組規則,允許 RDP 通過連接埠 3389 連接,透過連接埠 80 進行 Internet 連接,以及使用[az 網路 nsg 規則建立](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create)出站連接。

```azurecli
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

建立具有 az[網路 vnet 創建的](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create)虛擬網路。 下面的範例建立名為*dsVNET*的虛擬網路,該虛擬網路*dsSubNET_v4*子網路 *, 並dsSubNET_v6*:

```azurecli
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

使用 az 網路 nic[為每個](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create)VM 創建虛擬 NIC。 下面的範例為每個 VM 創建虛擬 NIC。 每個 NIC 有兩個 IP 配置(1 個 IPv4 配置,1 個 IPv6 配置)。 使用 az 網路 nic [ip 設定創建](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create)IPV6 設定。

```azurecli
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

建立虛擬機*dsVM0*如下所示:

```azurecli
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest  
```

建立虛擬機*dsVM1*如下所示:

```azurecli
az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest 
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>在 Azure 門戶中查看 IPv6 雙堆疊虛擬網路
您可以在 Azure 門戶中查看 IPv6 雙堆疊虛擬網路,如下所示:
1. 在門戶的搜尋列中,輸入*dsVnet*。
2. 當搜尋結果中出現 **myVirtualNetwork** 時加以選取。 這將啟動名為*dsVnet*的雙堆疊虛擬網路**的概述**頁面。 雙堆疊虛擬網路顯示兩個 NIC,其中 IPv4 和 IPv6 配置都位於名為*dsSubnet*的雙堆疊子網中。

  ![Azure 的 IPv6 雙堆疊虛擬網路](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)



## <a name="clean-up-resources"></a>清除資源

當不再需要時,可以使用[az 組刪除](/cli/azure/group#az-group-delete)命令刪除資源組、VM 和所有相關資源。

```azurecli
 az group delete --name DsResourceGroup01
```

## <a name="next-steps"></a>後續步驟

在本文中,您創建了具有雙前端 IP 配置(IPv4 和 IPv6)的基本負載均衡器。 還創建了兩個虛擬機,其中包括具有雙 IP 配置 (IPV4 + IPv6) 的 NIC,這些配置已添加到負載均衡器的後端池中。 要瞭解有關 Azure 虛擬網路中 IPv6 支援的更多資訊,請參閱[什麼是 Azure 虛擬網路的 IPv6?](ipv6-overview.md)
