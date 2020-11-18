---
title: 使用 Azure CLI 建立 Azure Private Link 服務
description: 瞭解如何使用 Azure CLI 建立 Azure Private Link 服務
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 87fe02aed19ae7e5858715748a2b4c4da87a07b3
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658565"
---
# <a name="create-a-private-link-service-using-azure-cli"></a>使用 Azure CLI 建立 Private Link 服務
本文說明如何使用 Azure CLI 在 Azure 中建立 Private Link 服務。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 Azure CLI 的最新版本。 如果您是使用 Azure Cloud Shell，就已安裝最新版本。

## <a name="create-a-private-link-service"></a>建立 Private Link 服務
### <a name="create-a-resource-group"></a>建立資源群組

建立虛擬網路之前，您必須先建立資源群組來裝載虛擬網路。 使用 [az group create](/cli/azure/group) 來建立資源群組。 此範例會在 westcentralus 位置建立名為 myResourceGroup 的資源群組：

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```
### <a name="create-a-virtual-network"></a>建立虛擬網路
使用 [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) 建立虛擬網路。 這個範例會建立名為 *myVirtualNetwork* 的預設虛擬網路，其中包含一個名為 *>mysubnet* 的子網：

```azurecli-interactive
az network vnet create --resource-group myResourceGroup --name myVirtualNetwork --address-prefix 10.0.0.0/16  
```
### <a name="create-a-subnet"></a>建立子網路
使用 [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create)來建立虛擬網路的子網。 此範例會在 *myVirtualNetwork* 虛擬網路中建立名為 *>mysubnet* 的子網：

```azurecli-interactive
az network vnet subnet create --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --address-prefixes 10.0.0.0/24    
```
### <a name="create-a-internal-load-balancer"></a>建立內部 Load Balancer 
建立具有 [az network lb create](/cli/azure/network/lb#az-network-lb-create)的內部負載平衡器。 此範例會在名為 *myResourceGroup* 的資源群組中建立名為 *myILB* 的內部負載平衡器。 

```azurecli-interactive
az network lb create --resource-group myResourceGroup --name myILB --sku standard --vnet-name MyVirtualNetwork --subnet mySubnet --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool
```

### <a name="create-a-load-balancer-health-probe"></a>建立負載平衡器健全狀況探查

健全狀況探查會檢查所有虛擬機器執行個體，確認它們可以接收網路流量。 探查檢查失敗的虛擬機器執行個體會從負載平衡器上移除，直到其恢復正常運作且探查判斷其健全狀況良好為止。 使用 [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) 建立健康狀態探查，以檢視虛擬機器的健康狀態。 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-a-load-balancer-rule"></a>建立負載平衡器規則

負載平衡器規則可定義連入流量的前端 IP 組態及接收流量的後端 IP 集區，以及所需的來源和目的地連接埠。 使用 [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) 建立負載平衡器規則 myHTTPRule，用來接聽前端集區 myFrontEnd 中的連接埠 80，以及用來將負載平衡的網路流量傳送到後端位址集區 myBackEndPool (也是使用連接埠 80)。 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```
### <a name="create-backend-servers"></a>建立後端伺服器

在此範例中，我們不會涵蓋虛擬機器的建立。 您可以遵循快速入門中的步驟 [：使用 Azure CLI 建立內部負載平衡器來平衡 vm 的負載](/load-balancer/quickstart-load-balancer-standard-internal-cli#create-backend-servers) ，以建立兩個虛擬機器，作為負載平衡器的後端伺服器使用。 


### <a name="disable-private-link-service-network-policies-on-subnet"></a>在子網上停用 Private Link 服務網路原則 
Private Link 服務需要您在虛擬網路內所選任何子網的 IP。 目前，我們不支援這些 Ip 上的網路原則。  因此，我們必須停用子網上的網路原則。 使用 [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)來更新子網，以停用 Private Link service 網路原則。

```azurecli-interactive
az network vnet subnet update --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --disable-private-link-service-network-policies true 
```
 
## <a name="create-a-private-link-service-using-standard-load-balancer"></a>使用 Standard Load Balancer 建立 Private Link 服務 
 
使用 Standard Load Balancer 前端 IP 設定搭配 [az network Private-Link-service create](/cli/azure/network/private-link-service#az-network-private-link-service-create)來建立 Private Link 服務。 此範例會在名為 *myResourceGroup* 的資源群組中，使用名為 *myLoadBalancer* 的 Standard Load Balancer，建立名為 *myPLS* 的 Private Link 服務。 
 
```azurecli-interactive
az network private-link-service create \
--resource-group myResourceGroup \
--name myPLS \
--vnet-name myVirtualNetwork \
--subnet mySubnet \
--lb-name myILB \
--lb-frontend-ip-configs myFrontEnd \
--location westcentralus 
```
建立之後，請記下 Private Link 服務識別碼。 您稍後將需要此服務來要求連接到此服務。  
 
在這個階段，您的 Private Link 服務已成功建立，並已準備好接收流量。 請注意，上述範例只會示範如何使用 Azure CLI 來建立 Private Link 服務。  我們尚未設定負載平衡器後端集區，或後端集區上的任何應用程式來接聽流量。 如果您想要查看端對端流量流程，強烈建議您在 Standard Load Balancer 後方設定應用程式。  
 
接下來，我們將示範如何使用 Azure CLI 將此服務對應至不同虛擬網路中的私人端點。 同樣地，此範例僅限於建立私人端點，並使用 Azure CLI 連接到上面建立的 Private Link 服務。 此外，您可以在虛擬網路中建立虛擬機器，以傳送/接收私人端點的流量。        
 
## <a name="private-endpoints"></a>私人端點

### <a name="create-the-virtual-network"></a>建立虛擬網路 
建立具有 [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create)的虛擬網路。 此範例會  *myPEVNet*   在名為 *myResourcegroup* 的資源群組中建立名為 myPEVNet 的虛擬網路： 
```azurecli-interactive
az network vnet create \
--resource-group myResourceGroup \
--name myPEVnet \
--address-prefix 10.0.0.0/16  
```
### <a name="create-the-subnet"></a>建立子網 
在具有 [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create)的虛擬網路中建立子網。 此範例會在名為  *mySubnet*   *myResourcegroup* 的資源群組中名為 *myPEVnet* 的虛擬網路中建立名為 >mysubnet 的子網： 

```azurecli-interactive 
az network vnet subnet create \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--address-prefixes 10.0.0.0/24 
```   
## <a name="disable-private-endpoint-network-policies-on-subnet"></a>在子網上停用私人端點網路原則 
您可以在虛擬網路內選擇的任何子網中建立私人端點。 目前，我們不支援私人端點上的網路原則。  因此，我們必須停用子網上的網路原則。 使用 [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)來更新子網，以停用私人端點網路原則。 

```azurecli-interactive
az network vnet subnet update \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--disable-private-endpoint-network-policies true 
```
## <a name="create-private-endpoint-and-connect-to-private-link-service"></a>建立私人端點並連接至 private link 服務 
建立私人端點，以在您的虛擬網路中使用上述 Private Link 服務：
  
```azurecli-interactive
az network private-endpoint create \
--resource-group myResourceGroup \
--name myPE \
--vnet-name myPEVnet \
--subnet myPESubnet \
--private-connection-resource-id {PLS_resourceURI} \
--connection-name myPEConnectingPLS \
--location westcentralus 
```
您可以透過 Private Link 服務取得 *私人連線資源識別碼* `az network private-link-service show` 。 識別碼看起來會像這樣：   
/subscriptions/subID/resourceGroups/*resourcegroupname*/providers/Microsoft.Network/privateLinkServices/**privatelinkservicename** 
 
## <a name="show-private-link-service-connections"></a>顯示 Private Link 服務連接 
 
請參閱您的 Private Link 服務上使用 [az network Private Link-service show](/cli/azure/network/private-link-service#az-network-private-link-service-show)的連接要求。    
```azurecli-interactive 
az network private-link-service show --resource-group myResourceGroup --name myPLS 
```
## <a name="next-steps"></a>後續步驟
- 深入瞭解 [Azure Private Link 服務](private-link-service-overview.md)
 
