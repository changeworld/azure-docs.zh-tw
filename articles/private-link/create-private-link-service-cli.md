---
title: 使用 Azure CLI 建立 Azure Private Link 服務
description: 瞭解如何使用 Azure CLI 建立 Azure Private Link 服務
services: private-link
author: asudbring
ms.service: private-link
ms.topic: how-to
ms.date: 01/22/2021
ms.author: allensu
ms.openlocfilehash: 567ed736c52e8b3cbb03edeb19b3c0e2364e4112
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98757334"
---
# <a name="create-a-private-link-service-using-azure-cli"></a>使用 Azure CLI 建立 Private Link 服務

開始建立參考您服務的 Private Link 服務。  針對在 Azure Standard Load Balancer 後方部署的服務或資源，授與 Private Link 存取權限。  您服務的使用者可以從其虛擬網路進行私人存取。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

- 本快速入門需要 2.0.28 版或更新版本的 Azure CLI。 如果您是使用 Azure Cloud Shell，就已安裝最新版本。

## <a name="create-a-resource-group"></a>建立資源群組

Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

使用 [az group create](/cli/azure/group#az_group_create) 來建立資源群組：

* 命名為 **CreatePrivLinkService-rg**。 
* 在 **eastus** 位置中。

```azurecli-interactive
  az group create \
    --name CreatePrivLinkService-rg \
    --location eastus2

```

## <a name="create-an-internal-load-balancer"></a>建立內部負載平衡器

在本節中，您會建立虛擬網路和內部 Azure Load Balancer。

### <a name="virtual-network"></a>虛擬網路

在本節中，您會建立虛擬網路和子網路，以裝載可存取 Private Link 服務的負載平衡器。

使用 [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) 建立虛擬網路：

* 具名 **myVNet**。
* 位址前置詞 **10.1.0.0/16**。
* 名為 **>mysubnet** 的子網。
* **10.1.0.0/24** 的子網路前置詞。
* 在 **CreatePrivLinkService-rg** 資源群組中。
* **Eastus2** 的位置。
* 在子網上停用私人連結服務的網路原則。

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePrivLinkService-rg \
    --location eastus2 \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefixes 10.1.0.0/24

```

若要更新子網以停用 private link 服務網路原則，請使用 [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)：

```azurecli-interactive
az network vnet subnet update \
    --name mySubnet \
    --resource-group CreatePrivLinkService-rg \
    --vnet-name myVNet \
    --disable-private-link-service-network-policies true
```

### <a name="create-standard-load-balancer"></a>建立標準負載平衡器

本節將詳細說明如何建立及設定下列負載平衡器元件：

  * 前端 IP 集區，可接收負載平衡器上的連入網路流量。
  * 後端 IP 集區，前端集區在其中傳送負載平衡網路流量。
  * 健康狀態探查，可判斷後端 VM 執行個體的健康狀態。
  * 負載平衡器規則，可定義如何將流量分散至 VM。

### <a name="create-the-load-balancer-resource"></a>建立負載平衡器資源

使用 [az network lb create](/cli/azure/network/lb#az-network-lb-create) 建立公用負載平衡器：

* 具名 **myLoadBalancer**。
* 名為 **myFrontEnd** 的前端集區。
* 名為 **myBackEndPool** 的後端集區。
* 與虛擬網路 **myVNet** 相關聯。
* 與後端子網 **>mysubnet** 相關聯。

```azurecli-interactive
  az network lb create \
    --resource-group CreatePrivLinkService-rg \
    --name myLoadBalancer \
    --sku Standard \
    --vnet-name myVnet \
    --subnet mySubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool
```

### <a name="create-the-health-probe"></a>建立健康狀態探查

健全狀況探查會檢查所有虛擬機器執行個體，確認可以傳送網路流量。 

已從負載平衡器移除具有失敗探查檢查的虛擬機器。 解決失敗情況之後，系統會將虛擬機器新增回負載平衡器。

使用 [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create) 建立健康狀態探查：

* 監視虛擬機器的健康情況。
* 具名 **myHealthProbe**。
* 通訊協定 **TCP**。
* 監視 **連接埠 80**。

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePrivLinkService-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-the-load-balancer-rule"></a>建立負載平衡器規則

負載平衡器規則定義：

* 傳入流量的前端 IP 設定。
* 接收流量的後端 IP 集區。
* 所需的來源和目的地連接埠。 

使用 [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) 建立負載平衡器規則：

* 已命名為 **myHTTPRule**
* 接聽前端集區 **myFrontEnd** 中的 **連接埠 80**。
* 使用 **連接埠 80** 將負載平衡的網路流量傳送到後端位址集區 **myBackEndPool**。 
* 使用健康情況探查 **MyHealthProbe**。
* 通訊協定 **TCP**。
* 閒置逾時時間為 **15 分鐘**。
* 啟用 TCP 重設。

```azurecli-interactive
  az network lb rule create \
    --resource-group CreatePrivLinkService-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15 \
    --enable-tcp-reset true
```

## <a name="create-a-private-link-service"></a>建立私人連結服務

在本節中，建立使用上一個步驟中所建立之 Azure Load Balancer 的 private link 服務。

使用標準負載平衡器前端 IP 設定搭配 [az network private link-service create](/cli/azure/network/private-link-service#az-network-private-link-service-create)來建立 private link 服務：

* 命名為 **myPrivateLinkService**。
* 在虛擬網路 **MyVNet** 中。
* 關聯于標準負載平衡器 **myLoadBalancer** 和前端設定 **myFrontEnd**。
* 在 **eastus2** 位置。
 
```azurecli-interactive
az network private-link-service create \
    --resource-group CreatePrivLinkService-rg \
    --name myPrivateLinkService \
    --vnet-name myVNet \
    --subnet mySubnet \
    --lb-name myLoadBalancer \
    --lb-frontend-ip-configs myFrontEnd \
    --location eastus2
```

系統會建立您的私人連結服務，並可接收流量。 如果您想要查看流量流程，請在您的標準負載平衡器後方設定您的應用程式。


## <a name="create-private-endpoint"></a>建立私人端點

在本節中，您會將 private link 服務對應至私人端點。 虛擬網路包含私用連結服務的私人端點。 此虛擬網路包含可存取私用連結服務的資源。

### <a name="create-private-endpoint-virtual-network"></a>建立私人端點虛擬網路

使用 [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) 建立虛擬網路：

* 命名為 **myVNetPE**。
* **11.1.0.0/16** 的位址前置詞。
* 名為 **mySubnetPE** 的子網。
* **11.1.0.0/24** 的子網首碼。
* 在 **CreatePrivLinkService-rg** 資源群組中。
* **Eastus2** 的位置。

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePrivLinkService-rg \
    --location eastus2 \
    --name myVNetPE \
    --address-prefixes 11.1.0.0/16 \
    --subnet-name mySubnetPE \
    --subnet-prefixes 11.1.0.0/24
```

若要更新子網以停用私人端點網路原則，請使用 [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)：

```azurecli-interactive
az network vnet subnet update \
    --name mySubnetPE \
    --resource-group CreatePrivLinkService-rg \
    --vnet-name myVNetPE \
    --disable-private-endpoint-network-policies true
```

### <a name="create-endpoint-and-connection"></a>建立端點和連接

* 使用 [az network private-link-service show](/cli/azure/network/private-link-service#az_network_private_link_service_show) 來取得 private link 服務的資源識別碼。 此命令會將資源識別碼放入變數中，以供稍後使用。

* 使用 [az network private-endpoint create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) ，在您先前建立的虛擬網路中建立私人端點。

* 命名為 **MyPrivateEndpoint**。
* 在 **CreatePrivLinkService-rg** 資源群組中。
* 連接名稱 **myPEconnectiontoPLS**。
* **Eastus2** 的位置。
* 在 [虛擬網路] **myVNetPE** 和 [子網] **mySubnetPE** 中。

```azurecli-interactive
  export resourceid=$(az network private-link-service show \
    --name myPrivateLinkService \
    --resource-group CreatePrivLinkService-rg \
    --query id \
    --output tsv)

  az network private-endpoint create \
    --connection-name myPEconnectiontoPLS \
    --name myPrivateEndpoint \
    --private-connection-resource-id $resourceid \
    --resource-group CreatePrivLinkService-rg \
    --subnet mySubnetPE \
    --manual-request false \
    --vnet-name myVNetPE 

```

## <a name="clean-up-resources"></a>清除資源

如果不再需要，請使用 [az group delete](/cli/azure/group#az-group-delete) 命令來移除資源群組、private link 服務、負載平衡器和所有相關資源。

```azurecli-interactive
  az group delete \
    --name CreatePrivLinkService-rg 
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已：

* 建立虛擬網路和內部 Azure Load Balancer。
* 建立私人連結服務

若要深入了解 Azure 私人端點，請繼續：
> [!div class="nextstepaction"]
> [快速入門：使用 Azure CLI 建立私人端點](create-private-endpoint-cli.md)
