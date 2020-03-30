---
title: 容器組的靜態 IP 位址
description: 在虛擬網路中創建容器組，並使用 Azure 應用程式閘道將靜態前端 IP 位址公開給容器化 Web 應用
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: 5c3a14f93af3ecc614dc296f0a4d2815d7a64a66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481784"
---
# <a name="expose-a-static-ip-address-for-a-container-group"></a>公開容器組的靜態 IP 位址

本文展示了使用 Azure[應用程式閘道](../application-gateway/overview.md)公開[容器組的](container-instances-container-groups.md)靜態公共 IP 位址的一種方法。 當您需要在 Azure 容器實例中運行的外部容器化應用的靜態進入點時，請執行以下步驟。 

在本文中，您可以使用 Azure CLI 為此方案創建資源：

* Azure 虛擬網路
* 部署在[虛擬網路（預覽）中的](container-instances-vnet.md)容器組，該容器組承載小型 Web 應用
* 具有公共前端 IP 位址的應用程式閘道、在閘道上託管網站的攔截器以及到後端容器組的路由

只要應用程式閘道運行，並且容器組在網路委派的子網中公開穩定的私人 IP 位址，容器組即可在此公共 IP 位址訪問。

> [!NOTE]
> Azure 根據閘道的預配和可用時間以及它處理的資料量對應用程式閘道收費。 請參閱[定價](https://azure.microsoft.com/pricing/details/application-gateway/)。

## <a name="create-virtual-network"></a>建立虛擬網路

在典型情況下，您可能已經擁有 Azure 虛擬網路。 如果沒有，請創建一個，如以下示例命令所示。 虛擬網路需要應用程式閘道和容器組的單獨子網。

如果需要，請創建 Azure 資源組。 例如：

```azureci
az group create --name myResourceGroup --location eastus
```

請使用 [az network vnet create][az-network-vnet-create] 命令來建立虛擬網路。 此命令在網路中創建*myAG 子網*子網。

```azurecli
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroup \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
```

使用[az 網路 vnet 子網創建][az-network-vnet-subnet-create]命令為後端容器組創建子網。 這裡被命名為*myACISubnet。*

```azurecli
az network vnet subnet create \
  --name myACISubnet \
  --resource-group myResourceGroup \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
```

使用[az 網路公共 ip 創建][az-network-public-ip-create]命令創建靜態公共 IP 資源。 在後面的步驟中，此位址配置為應用程式閘道的前端。

```azurecli
az network public-ip create \
  --resource-group myResourceGroup \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-container-group"></a>建立容器群組

運行以下[az 容器創建][az-container-create]，在上一步驟中配置的虛擬網路中創建容器組。 

該組部署在*myACISubnet*子網中，其中包含一個名為*應用容器*的實例，該實例名為`aci-helloworld`應用容器，用於提取映射。 如文檔中的其他文章所示，此映射打包了在 Node.js 中編寫的小型 Web 應用，該應用提供靜態 HTML 頁面。 

```azurecli
az container create \
  --name appcontainer \
  --resource-group myResourceGroup \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet myVNet \
  --subnet myACISubnet
```

成功部署後，將為虛擬網路中的容器組分配私人 IP 位址。 例如，運行以下[az 容器顯示][az-container-show]命令以檢索組的 IP 位址：

```azurecli
az container show \
  --name appcontainer --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv
```

輸出如下：`10.0.2.4`。

要在後面的步驟中使用，將 IP 位址保存在環境變數中：

```azurecli
ACI_IP=$(az container show \
  --name appcontainer \
  --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv)
```

## <a name="create-application-gateway"></a>建立應用程式閘道

按照[應用程式閘道快速入門](../application-gateway/quick-create-cli.md)中的步驟在虛擬網路中創建應用程式閘道。 以下[az 網路應用程式閘道創建][az-network-application-gateway-create]命令創建具有公共前端 IP 位址和到後端容器組的路由的閘道。 有關閘道設置的詳細資訊，請參閱[應用程式閘道文檔](/azure/application-gateway/)。

```azurecli
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroup \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-protocol http \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$ACI_IP" 
```


Azure 最多可能需要 15 分鐘才能創建應用程式閘道。 

## <a name="test-public-ip-address"></a>測試公共 IP 位址
  
現在，您可以測試對在應用程式閘道後面的容器組中運行的 Web 應用的訪問。

運行[az 網路公共 ip 顯示][az-network-public-ip-show]命令以檢索閘道的前端公共 IP 位址：

```azurecli
az network public-ip show \
--resource-group myresourcegroup \
--name myAGPublicIPAddress \
--query [ipAddress] \
--output tsv
```

輸出是公共 IP 位址，類似于： `52.142.18.133`。

要在成功配置時查看正在運行的 Web 應用，請導航到瀏覽器中的閘道公共 IP 位址。 成功訪問類似于：

![顯示在 Azure 容器執行個體中執行之應用程式的瀏覽器螢幕擷取畫面](./media/container-instances-application-gateway/aci-app-app-gateway.png)

## <a name="next-steps"></a>後續步驟

* 請參閱[快速入門範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet)，以創建具有 WordPress 容器實例的容器組，作為應用程式閘道後面的後端伺服器。
* 您還可以使用用於 SSL 終止的證書配置應用程式閘道。 請參閱[概述](../application-gateway/ssl-overview.md)和[教程](../application-gateway/create-ssl-portal.md)。
* 根據您的方案，請考慮將其他 Azure 負載平衡解決方案與 Azure 容器實例一起使用。 例如，使用[Azure 流量管理器](../traffic-manager/traffic-manager-overview.md)跨多個容器實例和多個區域分發流量。 請參閱此 [部落格文章](https://aaronmsft.com/posts/azure-container-instances/)。

[az-network-vnet-create]:  /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-show]: /cli/azure/network/public-ip#az-network-public-ip-show
[az-network-application-gateway-create]: /cli/azure/network/application-gateway#az-network-application-gateway-create
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show