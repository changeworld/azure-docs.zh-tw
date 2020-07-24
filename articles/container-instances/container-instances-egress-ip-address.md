---
title: 設定靜態輸出 IP
description: 針對使用防火牆的公用 IP 位址進行輸入和輸出的 Azure 容器實例工作負載，設定 Azure 防火牆和使用者定義的路由
ms.topic: article
ms.date: 07/16/2020
author: dlepow
ms.author: danlep
ms.openlocfilehash: d748e3e6239ba913afc5b8aadd7e85dcd1027c04
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87023699"
---
# <a name="configure-a-single-public-ip-address-for-outbound-and-inbound-traffic-to-a-container-group"></a>為容器群組的輸出和輸入流量設定單一公用 IP 位址

設定具有外部 IP 位址的[容器群組](container-instances-container-groups.md)，可讓外部用戶端使用 IP 位址來存取群組中的容器。 例如，瀏覽器可以存取在容器中執行的 web 應用程式。 不過，目前容器群組會針對輸出流量使用不同的 IP 位址。 此輸出 IP 位址不會以程式設計方式公開，讓容器群組監視和設定用戶端防火牆規則更為複雜。

本文提供在與[Azure 防火牆](../firewall/overview.md)整合的[虛擬網路](container-instances-virtual-network-concepts.md)中設定容器群組的步驟。 藉由設定容器群組和防火牆規則的使用者定義路由，您可以路由傳送和識別進出容器群組的流量。 容器群組的輸入和輸出會使用防火牆的公用 IP 位址。 在委派給 Azure 容器實例的虛擬網路子網中，多個已部署的容器群組可以使用單一輸出 IP 位址。

在本文中，您會使用 Azure CLI 來建立此案例的資源：

* 部署在[虛擬網路中](container-instances-vnet.md)委派子網上的容器群組 
* 在網路中使用靜態公用 IP 位址部署的 Azure 防火牆
* 容器群組子網上的使用者定義路由
* 適用于防火牆輸入的 NAT 規則和輸出的應用程式規則

接著，您可以透過防火牆來驗證範例容器群組的輸入和輸出。

## <a name="deploy-aci-in-a-virtual-network"></a>在虛擬網路中部署 ACI

在一般情況下，您可能已經有用來部署容器群組的 Azure 虛擬網路。 為了方便示範，下列命令會在建立容器群組時，建立虛擬網路和子網。 子網會委派給 Azure 容器實例。 

容器群組會從映射執行小型的 web 應用程式 `aci-helloworld` 。 如檔中的其他文章所示，此映射會封裝以 Node.js 撰寫並提供靜態 HTML 網頁的小型 web 應用程式。

如果您需要的話，請先使用[az group create][az-group-create]命令來建立 Azure 資源群組。 例如:

```azurecli
az group create --name myResourceGroup --location eastus
```

若要簡化下列命令範例，請針對資源群組的名稱使用環境變數：

```console
export RESOURCE_GROUP_NAME=myResourceGroup
```

使用[az container create][az-container-create]命令來建立容器群組：

```azurecli
az container create \
  --name appcontainer \
  --resource-group $RESOURCE_GROUP_NAME \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet aci-vnet \
  --vnet-address-prefix 10.0.0.0/16 \
  --subnet aci-subnet \
  --subnet-address-prefix 10.0.0.0/24
```

> [!TIP]
> `--subnet address-prefix`針對您在子網中所需的 IP 位址空間，調整的值。 最小的支援子網為/29，這會提供八個 IP 位址。 某些 IP 位址會保留供 Azure 使用。

若要在稍後步驟中使用，請執行 [az container show] [az-container-show] 命令來取得容器群組的私人 IP 位址：

```azurecli
ACI_PRIVATE_IP="$(az container show --name appcontainer \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress.ip --output tsv)"
```

## <a name="deploy-azure-firewall-in-network"></a>在網路中部署 Azure 防火牆

在下列各節中，使用 Azure CLI 在虛擬網路中部署 Azure 防火牆。 如需背景，請參閱[教學課程：使用 Azure 入口網站部署和設定 Azure 防火牆](../firewall/deploy-cli.md)。

首先，使用[az network vnet subnet create][az-network-vnet-subnet-create]來為防火牆新增名為 AzureFirewallSubnet 的子網。 AzureFirewallSubnet 是此子網的*必要*名稱。

```azurecli
az network vnet subnet create \
  --name AzureFirewallSubnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet   \
  --address-prefix 10.0.1.0/26
```

使用下列[Azure CLI 命令](../firewall/deploy-cli.md)在子網中建立防火牆。

如果尚未安裝，請使用[az extension add][az-extension-add]命令，將防火牆擴充功能新增至 Azure CLI：

```azurecli
az extension add --name azure-firewall
```

建立防火牆資源：

```azurecli
az network firewall create \
  --name myFirewall \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus

az network public-ip create \
  --name fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus \
  --allocation-method static \
  --sku standard
    
az network firewall ip-config create \
  --firewall-name myFirewall \
  --name FW-config \
  --public-ip-address fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet
```

使用[az network firewall update][az-network-firewall-update]命令來更新防火牆設定：

```azurecli
az network firewall update \
  --name myFirewall \
  --resource-group $RESOURCE_GROUP_NAME
```

使用[az network firewall IP-config list][az-network-firewall-ip-config-list]命令來取得防火牆的私人 IP 位址。 此私人 IP 位址會在稍後的命令中使用。


```azurecli
FW_PRIVATE_IP="$(az network firewall ip-config list \
  --resource-group $RESOURCE_GROUP_NAME \
  --firewall-name myFirewall \
  --query "[].privateIpAddress" --output tsv)"
```
使用[az network public-IP show][az-network-public-ip-show]命令來取得防火牆的公用 ip 位址。 此公用 IP 位址會在稍後的命令中使用。

```azurecli
FW_PUBLIC_IP="$(az network public-ip show \
  --name fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress --output tsv)"
```

## <a name="define-user-defined-route-on-aci-subnet"></a>定義 ACI 子網上的使用者定義路由

在 ACI 子網上定義使用定義的路由，以將流量轉移到 Azure 防火牆。 如需詳細資訊，請參閱[路由網路流量](../virtual-network/tutorial-create-route-table-cli.md)。 

### <a name="create-route-table"></a>建立路由表

首先，執行下列[az network route-table create][az-network-route-table-create]命令來建立路由表。 在與虛擬網路相同的區域中建立路由表。

```azurecli
az network route-table create \
  --name Firewall-rt-table \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus \
  --disable-bgp-route-propagation true
```

### <a name="create-route"></a>建立路由

執行[az network-route-table create][az-network-route-table-route-create] ，在路由表中建立路由。 若要將流量路由傳送至防火牆，請將 [下一個躍點類型] 設定為 `VirtualAppliance` ，然後將防火牆的私人 IP 位址傳遞為下一個躍點位址。

```azurecli
az network route-table route create \
  --resource-group $RESOURCE_GROUP_NAME \
  --name DG-Route \
  --route-table-name Firewall-rt-table \
  --address-prefix 0.0.0.0/0 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address $FW_PRIVATE_IP
```

### <a name="associate-route-table-to-aci-subnet"></a>將路由表關聯至 ACI 子網

執行[az network vnet subnet update][az-network-vnet-subnet-update]命令，使路由表與委派給 Azure 容器實例的子網產生關聯。

```azurecli
az network vnet subnet update \
  --name aci-subnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet \
  --address-prefixes 10.0.0.0/24 \
  --route-table Firewall-rt-table
```

## <a name="configure-rules-on-firewall"></a>設定防火牆的規則

根據預設，Azure 防火牆會拒絕（封鎖）輸入和輸出流量。 

### <a name="configure-nat-rule-on-firewall-to-aci-subnet"></a>設定防火牆對 ACI 子網的 NAT 規則

在防火牆上建立[NAT 規則](../firewall/rule-processing.md)，以將輸入網際網路流量轉譯並篩選至您先前在網路中啟動的應用程式容器。 如需詳細資訊，請參閱[使用 Azure 防火牆 DNAT 篩選輸入網際網路流量](../firewall/tutorial-firewall-dnat.md)

使用[az network firewall NAT-rule create][az-network-firewall-nat-rule-create]命令來建立 NAT 規則和集合：

```azurecli
az network firewall nat-rule create \
  --firewall-name myFirewall \
  --collection-name myNATCollection \
  --action dnat \
  --name myRule \
  --protocols TCP \
  --source-addresses '*' \
  --destination-addresses $FW_PUBLIC_IP \
  --destination-ports 80 \
  --resource-group $RESOURCE_GROUP_NAME \
  --translated-address $ACI_PRIVATE_IP \
  --translated-port 80 \
  --priority 200
```

視需要新增 NAT 規則，以篩選子網中其他 IP 位址的流量。 例如，子網中的其他容器群組可以公開輸入流量的 IP 位址，或在重新開機之後，將其他內部 IP 位址指派給容器群組。

### <a name="create-outbound-application-rule-on-the-firewall"></a>在防火牆上建立輸出應用程式規則

執行下列[az network firewall application-rule create][az-network-firewall-application-rule-create]命令，以在防火牆上建立輸出規則。 此範例規則允許從委派給 Azure 容器實例的子網存取 FQDN `checkip.dyndns.org` 。 稍後的步驟會使用網站的 HTTP 存取，以確認來自 Azure 容器實例的輸出 IP 位址。

```azurecli
az network firewall application-rule create \
  --collection-name myAppCollection \
  --firewall-name myFirewall \
  --name Allow-CheckIP \
  --protocols Http=80 Https=443 \
  --resource-group $RESOURCE_GROUP_NAME \
  --target-fqdns checkip.dyndns.org \
  --source-addresses 10.0.0.0/24 \
  --priority 200 \
  --action Allow
```

## <a name="test-container-group-access-through-the-firewall"></a>透過防火牆測試容器群組存取

下列各節會驗證委派給 Azure 容器實例的子網是否已正確設定于 Azure 防火牆後方。 先前的步驟會將傳入的流量路由傳送至子網，並將來自子網的連出流量透過防火牆傳遞出去。

### <a name="test-ingress-to-a-container-group"></a>測試輸入至容器群組

藉由流覽至防火牆的公用 IP 位址，測試在虛擬網路中執行的*appcontainer*的輸入存取。 先前，您已將公用 IP 位址儲存在變數 $FW _PUBLIC_IP 中：

```bash
echo $FW_PUBLIC_IP
```

輸出會類似：

```console
52.142.18.133
```

如果防火牆上的 NAT 規則設定正確，當您在瀏覽器中輸入防火牆的公用 IP 位址時，您會看到下列內容：

:::image type="content" source="media/container-instances-egress-ip-address/aci-ingress-ip-address.png" alt-text="流覽至防火牆的公用 IP 位址":::

### <a name="test-egress-from-a-container-group"></a>測試容器群組的輸出


將下列範例容器部署到虛擬網路中。 當它執行時，它會將單一 HTTP 要求傳送至 `http://checkip.dyndns.org` ，以顯示寄件者的 ip 位址（輸出 IP 位址）。 如果防火牆上的應用程式規則已正確設定，則會傳回防火牆的公用 IP 位址。

```azurecli
az container create \
  --resource-group $RESOURCE_GROUP_NAME \
  --name testegress \
  --image mcr.microsoft.com/azuredocs/aci-tutorial-sidecar \
  --command-line "curl -s http://checkip.dyndns.org" \
  --restart-policy OnFailure \
  --vnet aci-vnet \
  --subnet aci-subnet
```

查看容器記錄以確認 IP 位址與防火牆的公用 IP 位址相同。

```azurecli
az container logs \
  --resource-group $RESOURCE_GROUP_NAME \
  --name testegress 
```

輸出會類似：

```console
<html><head><title>Current IP Check</title></head><body>Current IP Address: 52.142.18.133</body></html>
```

## <a name="next-steps"></a>接下來的步驟

在本文中，您會在 Azure 防火牆後方的虛擬網路中設定容器群組。 您已在防火牆上設定使用者定義的路由和 NAT 和應用程式規則。 藉由使用此設定，您可以針對來自 Azure 容器實例的輸入和輸出設定單一靜態 IP 位址。

如需管理流量和保護 Azure 資源的詳細資訊，請參閱[Azure 防火牆](../firewall/index.yml)檔。



[az-group-create]: /cli/azure/group#az-group-create
[az-container-create]: /cli/azure/container#az-container-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-firewall-update]: /cli/azure/ext/azure-firewall/network/firewall#ext-azure-firewall-az-network-firewall-update
[az-network-public-ip-show]: /cli/azure/network/public-ip/#az-network-public-ip-show
[az-network-route-table-create]:/cli/azure/network/route-table/#az-network-route-table-create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az-network-route-table-route-create
[az-network-firewall-ip-config-list]: /cli/azure/ext/azure-firewall/network/firewall/ip-config#ext-azure-firewall-az-network-firewall-ip-config-list
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-update
[az-container-exec]: /cli/azure/container#az-container-exec
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-vm-open-port]: /cli/azure/vm#az-vm-open-port
[az-vm-list-ip-addresses]: /cli/azure/vm#az-vm-list-ip-addresses
[az-network-firewall-application-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/application-rule#ext-azure-firewall-az-network-firewall-application-rule-create
[az-network-firewall-nat-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/nat-rule#ext-azure-firewall-az-network-firewall-nat-rule-create






