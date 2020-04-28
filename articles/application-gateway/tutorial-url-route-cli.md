---
title: 根據 URL 路由網路流量 - Azure CLI
description: 在本文中，請了解如何使用 Azure CLI 根據 URL 將網路流量路由到伺服器的特定可調整集區。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 08/01/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: b6bc0b00579bdef0a358f756b8cf2b6034aca017
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "68688183"
---
# <a name="route-web-traffic-based-on-the-url-using-the-azure-cli"></a>使用 Azure CLI 根據 URL 路由傳送網路流量

身為管理 Web 流量的 IT 系統管理員，您會想協助客戶或使用者盡快取得他們所需的資訊。 讓使用者獲得最佳體驗的方法之一，就是將不同種類的 Web 流量路由傳送到不同的伺服器資源。 本文說明如何針對來自您應用程式的不同流量類型，使用 Azure CLI 來安裝及設定應用程式閘道路由。 路由接著會根據 URL，將流量導向不同的伺服器集區。

![URL 路由範例](./media/tutorial-url-route-cli/scenario.png)

在本文中，您將學會如何：

> [!div class="checklist"]
> * 為您所需的網路資源建立資源群組
> * 建立網路資源
> * 針對來自您應用程式的流量，建立應用程式閘道
> * 針對不同類型的流量，指定伺服器集區和路由規則
> * 建立每個集區的擴展集，以便自動調整集區
> * 執行測試，以便確認不同類型的流量會前往正確的集區

您可以依偏好使用 [Azure PowerShell](tutorial-url-route-powershell.md) 或 [Azure 入口網站](create-url-route-portal.md)來完成此程序。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本文會要求您執行 Azure CLI 2.0.4 版或更新版本。 若要尋找版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>建立資源群組

資源群組是一種邏輯容器，您會在其中部署與管理 Azure 資源。 使用 `az group create` 建立資源群組。

下列範例會在 eastus** 位置建立名為 myResourceGroupAG** 的資源群組。

```azurecli-interactive
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>建立網路資源

使用 `az network vnet create` 建立名為 myVNet** 的虛擬網路，以及名為 myAGSubnet** 的子網路。 然後使用 `az network vnet subnet create` 新增名為 myBackendSubnet** 的子網路，後端伺服器需要該子網路。 使用 `az network public-ip create` 建立名為 myAGPublicIPAddress** 的公用 IP 位址。

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24

az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24

az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-the-app-gateway-with-a-url-map"></a>建立包含 URL 對應的應用程式閘道

使用 `az network application-gateway create` 建立名為 myAppGateway** 的應用程式閘道。 當您使用 Azure CLI 建立應用程式閘道時，需要指定設定資訊，例如容量、SKU 和 HTTP 設定。 應用程式閘道會指派給 myAGSubnet** 和 myAGPublicIPAddress**。

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

 可能需要幾分鐘的時間來建立應用程式閘道。 建立應用程式閘道後，您可以看到這些新功能：


|功能  |描述  |
|---------|---------|
|appGatewayBackendPool     |應用程式閘道必須至少有一個後端位址集區。|
|appGatewayBackendHttpSettings     |指定以連接埠 80 和 HTTP 通訊協定來進行通訊。|
|appGatewayHttpListener     |與 appGatewayBackendPool 相關聯的預設接聽程式。|
|appGatewayFrontendIP     |將 myAGPublicIPAddress 指派給 appGatewayHttpListener。|
|rule1     |與 appGatewayHttpListener 相關聯的預設路由規則。|

### <a name="add-image-and-video-backend-pools-and-a-port"></a>新增映像和視訊後端集區及連接埠

使用 `az network application-gateway address-pool create`，將名為 imagesBackendPool** 和 videoBackendPool** 的後端集區新增至應用程式閘道。 使用 `az network application-gateway frontend-port create` 新增集區的前端連接埠。

```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name imagesBackendPool

az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name videoBackendPool

az network application-gateway frontend-port create \
  --port 8080 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name port8080
```

### <a name="add-a-backend-listener"></a>新增後端接聽程式

使用 `az network application-gateway http-listener create` 新增名為 backendListener**、路由流量所需的後端接聽程式。


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port port8080 \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-a-url-path-map"></a>新增 URL 路徑對應

URL 路徑對應可確保特定 URL 會路由傳送到特定的後端集區。 使用 `az network application-gateway url-path-map create` 和 `az network application-gateway url-path-map rule create`，建立名為 imagePathRule** 和 videoPathRule** 的 URL 路徑對應。

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name myPathMap \
  --paths /images/* \
  --resource-group myResourceGroupAG \
  --address-pool imagesBackendPool \
  --default-address-pool appGatewayBackendPool \
  --default-http-settings appGatewayBackendHttpSettings \
  --http-settings appGatewayBackendHttpSettings \
  --rule-name imagePathRule

az network application-gateway url-path-map rule create \
  --gateway-name myAppGateway \
  --name videoPathRule \
  --resource-group myResourceGroupAG \
  --path-map-name myPathMap \
  --paths /video/* \
  --address-pool videoBackendPool
```

### <a name="add-a-routing-rule"></a>新增路由規則

路由規則會將 URL 對應與您所建立的接聽程式產生關聯。 使用 `az network application-gateway rule create` 新增名為 rule2** 的規則。

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name rule2 \
  --resource-group myResourceGroupAG \
  --http-listener backendListener \
  --rule-type PathBasedRouting \
  --url-path-map myPathMap \
  --address-pool appGatewayBackendPool
```

## <a name="create-virtual-machine-scale-sets"></a>建立虛擬機器擴展集

在本文中，您會建立三個虛擬機器擴展集，以支援您所建立的三個後端集區。 建立名為 myvmss1**、myvmss2** 和 myvmss3** 的擴展集。 每個擴展集都會包含兩個您安裝 NGINX 的虛擬機器執行個體。

```azurecli-interactive
for i in `seq 1 3`; do

  if [ $i -eq 1 ]
  then
    poolName="appGatewayBackendPool" 
  fi

  if [ $i -eq 2 ]
  then
    poolName="imagesBackendPool"
  fi

  if [ $i -eq 3 ]
  then
    poolName="videoBackendPool"
  fi

  az vmss create \
    --name myvmss$i \
    --resource-group myResourceGroupAG \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password Azure123456! \
    --instance-count 2 \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --vm-sku Standard_DS2 \
    --upgrade-policy-mode Automatic \
    --app-gateway myAppGateway \
    --backend-pool-name $poolName
done
```

### <a name="install-nginx"></a>安裝 NGINX

```azurecli-interactive
for i in `seq 1 3`; do
  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"], "commandToExecute": "./install_nginx.sh" }'
done
```

## <a name="test-the-application-gateway"></a>測試應用程式閘道

若要取得應用程式閘道的公用 IP 位址，請使用 az network public-ip show。 將公用 IP 位址複製並貼到您瀏覽器的網址列。 例如 `http://40.121.222.19`、`http://40.121.222.19:8080/images/test.htm` 或 `http://40.121.222.19:8080/video/test.htm`。

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![在應用程式閘道中測試基底 URL](./media/tutorial-url-route-cli/application-gateway-nginx.png)

將 URL 變更為 http://&lt;ip-address&gt;:8080/images/test.html、使用您的 IP 位址取代 &lt;ip-address&gt;，您應該會看到類似下列的範例：

![在應用程式閘道中測試影像 URL](./media/tutorial-url-route-cli/application-gateway-nginx-images.png)

將 URL 變更為 HTTP://&lt;ip 位址&gt;：8080/video/test.html，取代 ip 位址的&lt;ip 位址&gt;，您應該會看到類似下列範例的內容。

![在應用程式閘道中測試影片 URL](./media/tutorial-url-route-cli/application-gateway-nginx-video.png)

## <a name="clean-up-resources"></a>清除資源

若不再需要，可移除資源群組、應用程式閘道和所有相關資源。

```azurecli-interactive
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>後續步驟

[建立包含 URL 路徑型重新導向的應用程式閘道](./tutorial-url-redirect-cli.md)
