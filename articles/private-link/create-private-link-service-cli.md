---
title: 使用 Azure CLI 創建 Azure 專用連結服務
description: 瞭解如何使用 Azure CLI 創建 Azure 專用連結服務
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 6e6148d305af26f7933567ae58023d2ba73263eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75350248"
---
# <a name="create-a-private-link-service-using-azure-cli"></a>使用 Azure CLI 創建專用連結服務
本文介紹如何使用 Azure CLI 在 Azure 中創建專用連結服務。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您決定在本地安裝和使用 Azure CLI，則此快速入門要求您使用最新的 Azure CLI 版本。 若要尋找您安裝的版本，請執行 `az --version`。 如需安裝或升級的資訊，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。
## <a name="create-a-private-link-service"></a>建立 Private Link 服務
### <a name="create-a-resource-group"></a>建立資源群組

建立虛擬網路之前，您必須先建立資源群組來裝載虛擬網路。 使用 [az group create](/cli/azure/group) 來建立資源群組。 此範例會在 westcentralus** 位置建立名為 myResourceGroup** 的資源群組：

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```
### <a name="create-a-virtual-network"></a>建立虛擬網路
創建具有 az[網路 vnet 創建的](/cli/azure/network/vnet#az-network-vnet-create)虛擬網路。 本示例創建一個名為*myVirtualNetwork*的預設虛擬網路，其中一個子網名為*mySubnet*：

```azurecli-interactive
az network vnet create --resource-group myResourceGroup --name myVirtualNetwork --address-prefix 10.0.0.0/16  
```
### <a name="create-a-subnet"></a>建立子網路
使用[az 網路 vnet 子網創建](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create)虛擬網路的子網。 本示例*在 myVirtualNetwork 虛擬網路中*創建名為*mySubnet*的子網：

```azurecli-interactive
az network vnet subnet create --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --address-prefixes 10.0.0.0/24    
```
### <a name="create-a-internal-load-balancer"></a>創建內部負載等化器 
創建具有 az 網路[lb](/cli/azure/network/lb#az-network-lb-create)的內部負載等化器。 本示例在名為*myResourceGroup*的資源組中創建名為*myILB*的內部負載等化器。 

```azurecli-interactive
az network lb create --resource-group myResourceGroup --name myILB --sku standard --vnet-name MyVirtualNetwork --subnet mySubnet --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool
```

### <a name="create-a-load-balancer-health-probe"></a>建立負載平衡器健全狀況探查

健全狀況探查會檢查所有虛擬機器執行個體，確認它們可以接收網路流量。 探查檢查失敗的虛擬機器執行個體會從負載平衡器上移除，直到其恢復正常運作且探查判斷其健全狀況良好為止。 創建使用 az[網路 lb 探測器創建的](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest)運行狀況探測，以監視虛擬機器的運行狀況。 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-a-load-balancer-rule"></a>建立負載平衡器規則

負載平衡器規則可定義連入流量的前端 IP 組態及接收流量的後端 IP 集區，以及所需的來源和目的地連接埠。 使用 [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) 建立負載平衡器規則 myHTTPRule**，用來接聽前端集區 myFrontEnd** 中的連接埠 80，以及用來將負載平衡的網路流量傳送到後端位址集區 myBackEndPool** (也是使用連接埠 80)。 

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

在此示例中，我們不介紹虛擬機器創建。 您可以按照[創建內部負載等化器的步驟來使用 Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md#create-servers-for-the-backend-address-pool)創建兩個虛擬機器，用作負載等化器的後端伺服器。 


### <a name="disable-private-link-service-network-policies-on-subnet"></a>禁用子網上的專用連結服務網路原則 
專用連結服務需要來自虛擬網路中您選擇的任何子網的 IP。 目前，我們不支援這些 IP 的網路原則。  因此，我們必須禁用子網上的網路原則。 更新子網以禁用具有[az 網路 vnet 子網更新](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)的專用鏈路服務網路原則。

```azurecli-interactive
az network vnet subnet update --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --disable-private-link-service-network-policies true 
```
 
## <a name="create-a-private-link-service"></a>建立 Private Link 服務  
 
使用使用[az 網路專用鏈路服務創建](/cli/azure/network/private-link-service#az-network-private-link-service-create)使用標準負載等化器前端 IP 配置創建專用鏈路服務。 本示例使用名為*myLoadBalancer*的標準負載等化器在名為*myResourceGroup*的資源組中創建名為*myPLS*的專用連結服務。 
 
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
創建後，請注意專用連結服務 ID。 稍後，您需要此連接才能請求連接到此服務。  
 
在此階段，您的專用連結服務已成功創建，並準備好接收流量。 請注意，上述示例僅用於演示使用 Azure CLI 創建專用連結服務。  我們尚未配置負載等化器後端池或後端池上的任何應用程式來偵聽流量。 如果要查看端到端流量，強烈建議您在標準負載等化器後面配置應用程式。  
 
接下來，我們將演示如何使用 Azure CLI 將此服務映射到不同虛擬網路中的專用終結點。 同樣，該示例僅限於創建專用終結點和連接到上面使用 Azure CLI 創建的專用連結服務。 此外，您還可以在虛擬網路中創建虛擬機器，以向專用終結點發送/接收流量。        
 
## <a name="private-endpoints"></a>專用終結點

### <a name="create-the-virtual-network"></a>建立虛擬網路 
創建具有 az [網路 vnet 創建的](/cli/azure/network/vnet#az-network-vnet-create)虛擬網路。 本示例在名為*myResourcegroup*的資源組中創建名為 *myPEVNet* 的虛擬網路： 
```azurecli-interactive
az network vnet create \
--resource-group myResourceGroup \
--name myPEVnet \
--address-prefix 10.0.0.0/16  
```
### <a name="create-the-subnet"></a>創建子網 
在虛擬網路中創建子網，創建 [az 網路 vnet 子網](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create)。 本示例在名為*myResourcegroup*的資源組中創建名為 *mySubnet* 的虛擬網路中名為*mySubnet*的子網， 

```azurecli-interactive 
az network vnet subnet create \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--address-prefixes 10.0.0.0/24 
```   
## <a name="disable-private-endpoint-network-policies-on-subnet"></a>禁用子網上的專用終結點網路原則 
專用終結點可以在虛擬網路內您選擇的任何子網中創建。 目前，我們不支援專用終結點上的網路原則。  因此，我們必須禁用子網上的網路原則。 更新子網以禁用具有 az 網路[vnet 子網更新](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)的專用終結點網路原則。 

```azurecli-interactive
az network vnet subnet update \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--disable-private-endpoint-network-policies true 
```
## <a name="create-private-endpoint-and-connect-to-private-link-service"></a>創建專用終結點並連接到專用連結服務 
創建專用終結點以使用上述在虛擬網路中創建的專用連結服務：
  
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
您可以在專用連結服務`az network private-link-service show`上獲取*專用連線資源識別碼。* 該 ID 將如下所示：   
/訂閱/子 ID/資源組/*資源組名稱*/供應商/微軟.網路/專用連結服務/**專用連結服務名稱** 
 
## <a name="show-private-link-service-connections"></a>顯示專用連結服務連接 
 
使用[az 網路專用連結服務顯示](/cli/azure/network/private-link-service#az-network-private-link-service-show)，請參閱專用鏈路服務上的連接請求。    
```azurecli-interactive 
az network private-link-service show --resource-group myResourceGroup --name myPLS 
```
## <a name="next-steps"></a>後續步驟
- 瞭解有關[Azure 專用連結服務](private-link-service-overview.md)
 
