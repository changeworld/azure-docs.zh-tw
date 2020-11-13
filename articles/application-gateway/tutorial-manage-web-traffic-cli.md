---
title: 管理網路流量 - Azure CLI
description: 了解如何使用 Azure CLI 建立具有虛擬機器擴展集的應用程式閘道以管理網路流量。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/20/2019
ms.author: victorh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 89ba84be61469ff07eff55bb9cd114fe124b3ec2
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566600"
---
# <a name="manage-web-traffic-with-an-application-gateway-using-the-azure-cli"></a>使用 Azure CLI 以應用程式閘道管理網路流量

針對您維護的伺服器，可以使用應用程式閘道來管理及保護其網路流量。 您可以使用 Azure CLI 來建立[應用程式閘道](overview.md)，它使用[虛擬機器擴展集](../virtual-machine-scale-sets/overview.md)作為後端伺服器。 在此範例中，擴展集會包含兩個虛擬機器執行個體。 會將擴展集新增至應用程式閘道的預設後端集區。

在本文中，您將學會如何：

* 設定網路
* 建立應用程式閘道
* 建立包含預設後端集區的虛擬機器擴展集

如果您想要的話，可以使用 [Azure PowerShell](tutorial-manage-web-traffic-powershell.md) 完成本程序。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - 本教學課程需要 Azure CLI 的版本2.0.4 版或更新版本。 如果您是使用 Azure Cloud Shell，就已安裝最新版本。

## <a name="create-a-resource-group"></a>建立資源群組

資源群組是在其中部署與管理 Azure 資源的邏輯容器。 使用 [az group create](/cli/azure/group#az-group-create) 建立資源群組。

下列範例會在 eastus 位置建立名為 myResourceGroupAG 的資源群組。

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>建立網路資源 

使用 [az network vnet create](/cli/azure/network/vnet) 建立名為 myVNet 的虛擬網路，以及名為 myAGSubnet 的子網路。 然後您可以使用 [az network vnet subnet create](/cli/azure/network/vnet/subnet) 新增名為 myBackendSubnet 的子網路，後端伺服器需要該子網路。 使用 [az network public-ip create](/cli/azure/network/public-ip) 建立名為 myAGPublicIPAddress 的公用 IP 位址。

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

## <a name="create-an-application-gateway"></a>建立應用程式閘道

使用 [az network application-gateway create](/cli/azure/network/application-gateway) 來建立名為 *myAppGateway* 的應用程式閘道。 當您使用 Azure CLI 建立應用程式閘道時，需要指定設定資訊，例如容量、SKU 和 HTTP 設定。 應用程式閘道會指派給您先前建立的 myAGSubnet 和 myPublicIPAddress。 

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

- appGatewayBackendPool - 應用程式閘道必須至少有一個後端位址集區。
- appGatewayBackendHttpSettings - 指定以連接埠 80 和 HTTP 通訊協定來進行通訊。
- appGatewayHttpListener - 與 appGatewayBackendPool 相關聯的預設接聽程式。
- appGatewayFrontendIP - 將 myAGPublicIPAddress 指派給 appGatewayHttpListener。
- rule1 - 與 appGatewayHttpListener 相關聯的預設路由規則。

## <a name="create-a-virtual-machine-scale-set"></a>建立虛擬機器擴展集

在此範例中，您會建立虛擬機器擴展集，以在應用程式閘道中提供後端集區的伺服器。 擴展集中的虛擬機器會與 myBackendSubnet 和 appGatewayBackendPool 相關聯。 若要建立擴展集，請使用 [az vmss create](/cli/azure/vmss#az-vmss-create)。

```azurecli-interactive
az vmss create \
  --name myvmss \
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
  --backend-pool-name appGatewayBackendPool
```

### <a name="install-nginx"></a>安裝 NGINX

現在，您可以將 NGINX 安裝在虛擬機器擴展集上，以便測試對後端集區的 HTTP 連線。

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"], "commandToExecute": "./install_nginx.sh" }'
```

## <a name="test-the-application-gateway"></a>測試應用程式閘道

若要取得應用程式閘道的公用 IP 位址，請使用 [az network public-ip show](/cli/azure/network/public-ip)。 將公用 IP 位址複製並貼到您瀏覽器的網址列。

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![在應用程式閘道中測試基底 URL](./media/tutorial-manage-web-traffic-cli/tutorial-nginxtest.png)

## <a name="clean-up-resources"></a>清除資源

若不再需要，可移除資源群組、應用程式閘道和所有相關資源。

```azurecli-interactive
az group delete --name myResourceGroupAG --location eastus
```

## <a name="next-steps"></a>後續步驟

[使用 Web 應用程式防火牆來限制網路流量](../web-application-firewall/ag/tutorial-restrict-web-traffic-cli.md)