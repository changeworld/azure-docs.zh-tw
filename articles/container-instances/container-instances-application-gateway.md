---
title: 容器群組的靜態 IP 位址
description: 在虛擬網路中建立容器群組，並使用 Azure 應用程式閘道將靜態前端 IP 位址公開給容器化 web 應用程式
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: 5c3a14f93af3ecc614dc296f0a4d2815d7a64a66
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79481784"
---
# <a name="expose-a-static-ip-address-for-a-container-group"></a>公開容器群組的靜態 IP 位址

本文說明如何使用 Azure[應用程式閘道](../application-gateway/overview.md)公開[容器群組](container-instances-container-groups.md)的靜態公用 IP 位址的其中一種方式。 當您需要在 Azure 容器實例中執行的外部容器化應用程式的靜態進入點時，請遵循下列步驟。 

在本文中，您會使用 Azure CLI 來建立此案例的資源：

* Azure 虛擬網路
* 在裝載小型 web 應用程式[的虛擬網路（預覽）中](container-instances-vnet.md)部署的容器群組
* 具有公用前端 IP 位址的應用程式閘道、在閘道上裝載網站的接聽程式，以及對後端容器群組的路由

只要應用程式閘道執行，且容器群組在網路的委派子網中公開了穩定的私人 IP 位址，就可以在這個公用 IP 位址存取容器群組。

> [!NOTE]
> Azure 會根據閘道的布建和可用時間長度，以及它所處理的資料量，收取應用程式閘道的費用。 請參閱[定價](https://azure.microsoft.com/pricing/details/application-gateway/)。

## <a name="create-virtual-network"></a>建立虛擬網路

在一般情況下，您可能已有 Azure 虛擬網路。 如果您沒有帳戶，請建立一個，如下列範例命令所示。 虛擬網路需要應用程式閘道和容器群組的個別子網。

如有需要，請建立 Azure 資源群組。 例如：

```azureci
az group create --name myResourceGroup --location eastus
```

請使用 [az network vnet create][az-network-vnet-create] 命令來建立虛擬網路。 此命令會在網路中建立*myAGSubnet*子網。

```azurecli
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroup \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
```

使用[az network vnet subnet create][az-network-vnet-subnet-create]命令來建立後端容器群組的子網。 這裡的名稱是*myACISubnet*。

```azurecli
az network vnet subnet create \
  --name myACISubnet \
  --resource-group myResourceGroup \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
```

使用[az network public ip create][az-network-public-ip-create]命令來建立靜態公用 ip 資源。 在稍後的步驟中，此位址會設定為應用程式閘道的前端。

```azurecli
az network public-ip create \
  --resource-group myResourceGroup \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-container-group"></a>建立容器群組

執行下列[az container create][az-container-create] ，在您于上一個步驟中設定的虛擬網路中建立容器群組。 

此群組會部署在*myACISubnet*子網中，並包含名為*appcontainer*的單一實例`aci-helloworld` ，可提取映射。 如檔中的其他文章所示，此映射會封裝以 node.js 撰寫並提供靜態 HTML 網頁的小型 web 應用程式。 

```azurecli
az container create \
  --name appcontainer \
  --resource-group myResourceGroup \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet myVNet \
  --subnet myACISubnet
```

成功部署時，會將虛擬網路中的私人 IP 位址指派給容器群組。 例如，執行下列[az container show][az-container-show]命令來取出群組的 IP 位址：

```azurecli
az container show \
  --name appcontainer --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv
```

輸出如下：`10.0.2.4`。

若要在稍後步驟中使用，請將 IP 位址儲存在環境變數中：

```azurecli
ACI_IP=$(az container show \
  --name appcontainer \
  --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv)
```

## <a name="create-application-gateway"></a>建立應用程式閘道

遵循[應用程式閘道快速入門](../application-gateway/quick-create-cli.md)中的步驟，在虛擬網路中建立應用程式閘道。 下列[az network application-gateway create][az-network-application-gateway-create]命令會建立具有公用前端 IP 位址的閘道，以及對後端容器群組的路由。 如需閘道設定的詳細資訊，請參閱[應用程式閘道檔](/azure/application-gateway/)。

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


Azure 最多可能需要15分鐘的時間來建立應用程式閘道。 

## <a name="test-public-ip-address"></a>測試公用 IP 位址
  
現在，您可以測試應用程式閘道後方的容器群組中執行之 web 應用程式的存取權。

執行[az network public-ip show][az-network-public-ip-show]命令，以取出閘道的前端公用 ip 位址：

```azurecli
az network public-ip show \
--resource-group myresourcegroup \
--name myAGPublicIPAddress \
--query [ipAddress] \
--output tsv
```

輸出是公用 IP 位址，類似于： `52.142.18.133`。

若要在成功設定時查看正在執行的 web 應用程式，請在瀏覽器中流覽至閘道的公用 IP 位址。 成功的存取如下所示：

![顯示在 Azure 容器執行個體中執行之應用程式的瀏覽器螢幕擷取畫面](./media/container-instances-application-gateway/aci-app-app-gateway.png)

## <a name="next-steps"></a>後續步驟

* 請參閱[快速入門範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet)，以使用 WordPress 容器實例作為應用程式閘道後方的後端伺服器來建立容器群組。
* 您也可以使用憑證來設定應用程式閘道以進行 SSL 終止。 請參閱[總覽](../application-gateway/ssl-overview.md)和[教學](../application-gateway/create-ssl-portal.md)課程。
* 視您的案例而定，請考慮使用其他 Azure 負載平衡解決方案搭配 Azure 容器實例。 例如，使用[Azure 流量管理員](../traffic-manager/traffic-manager-overview.md)將流量分散到多個容器實例，以及跨多個區域。 請參閱此 [部落格文章](https://aaronmsft.com/posts/azure-container-instances/)。

[az-network-vnet-create]:  /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-show]: /cli/azure/network/public-ip#az-network-public-ip-show
[az-network-application-gateway-create]: /cli/azure/network/application-gateway#az-network-application-gateway-create
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show